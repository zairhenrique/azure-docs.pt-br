---
title: Copiar dados do Google BigQuery utilizando o Azure Data Factory
description: Saiba como copiar dados do Google BigQuery para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: c0eb043ce040f154050ef4c3675f165dad326e32
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929418"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiar dados do Google BigQuery utilizando o Azure Data Factory

Este artigo descreve como usar Atividade de Cópia no Azure Data Factory para copiar dados do Google BigQuery. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Google BigQuery tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um Google BigQuery para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

>[!NOTE]
>Este conector do Google BigQuery baseia-se sobre as APIs do BigQuery. Lembre-se de que os limites de BigQuery a taxa máxima de recebimento de solicitações e impõe cotas apropriadas em uma base por projeto, consulte [cotas e limites - solicitações da API](https://cloud.google.com/bigquery/quotas#api_requests). Verifique se que você não disparam muitas solicitações simultâneas para a conta.

## <a name="get-started"></a>Comece agora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Google BigQuery.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **Google BigQuery**. | SIM |
| project | A ID de projeto do projeto padrão do BigQuery para consulta.  | SIM |
| additionalProjects | Uma lista de IDs de projeto separadas por vírgulas dos projetos públicos do BigQuery para acesso.  | Não |
| requestGoogleDriveScope | Se deve solicitar acesso ao Google Drive. Permitir o acesso ao Google Drive habilita o suporte para tabelas federadas que combinam dados do BigQuery com dados do Google Drive. O valor padrão é **false**.  | Não |
| authenticationType | O mecanismo de autenticação OAuth 2.0 usado para autenticação. ServiceAuthentication só pode ser usado em Integration Runtime auto-hospedado. <br/>Os valores permitidos são **UserAuthentication** e **ServiceAuthentication**. Consulte as seções abaixo desta tabela para mais propriedades e amostras JSON para esses tipos de autenticação, respectivamente. | SIM |

### <a name="using-user-authentication"></a>Usar a autenticação do usuário

Defina a propriedade "authenticationType" para **UserAuthentication** e especifique as propriedades a seguir juntamente com as propriedades genéricas descritas na seção anterior:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| clientId | ID do aplicativo usado para gerar o token de atualização. | Não |
| clientSecret | Segredo do aplicativo usado para gerar o token de atualização. Marque esse campo como um SecureString para armazená-lo de forma segura no Data Factory, ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| refreshToken | O token de atualização obtido no Google usado para autorizar o acesso ao BigQuery. Saiba como obter um em [Obter tokens de acesso do OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) e [no blog desta comunidade](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marque esse campo como um SecureString para armazená-lo de forma segura no Data Factory, ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |

**Exemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Usar autenticação de serviço

Defina a propriedade "authenticationType" para **ServiceAuthentication** e especifique as propriedades a seguir juntamente com as propriedades genéricas descritas na seção anterior. Esse tipo de autenticação pode ser usado somente no Integration Runtime auto-hospedado.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| email | A ID de e-mail da conta de serviço que é usada para ServiceAuthentication. Só pode ser usado em Integration Runtime auto-hospedado.  | Não |
| keyFilePath | O caminho completo para o arquivo chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço. | Não |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis usados para verificar o servidor ao se conectar via SSL. Essa propriedade pode ser definida somente quando você usa o SSL em Integration Runtime auto-hospedada. O valor padrão é o arquivo de cacerts.pem instalado com o runtime de integração.  | Não |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo .pem especificado. O valor padrão é **false**.  | Não |

**Exemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Google BigQuery.

Para copiar dados do Google BigQuery, defina a propriedade type do conjunto de dados para **GoogleBigQueryObject**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **GoogleBigQueryObject** | SIM |
| dataset | Nome do conjunto de BigQuery do Google. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da tabela. Essa propriedade tem suporte para compatibilidade com versões anteriores. Para uma nova carga de trabalho, use `dataset` e `table`. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo tipo de fonte do Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de fonte

Para copiar dados do Google BigQuery, defina o tipo de origem na atividade de cópia como **GoogleBigQuerySource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte da atividade de cópia deve ser definida como: **GoogleBigQuerySource**. | SIM |
| query | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
