---
title: 'Início Rápido: QnA Maker com APIs REST para Node.js'
titleSuffix: Azure Cognitive Services
description: Este início rápido oferece uma introdução às APIs REST do QnA Maker para Node.js. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.  O QnA Maker permite ter um serviço avançado de perguntas e respostas a partir de um conteúdo semiestruturado, como documentos de perguntas frequentes ou URLs e manuais de produtos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3df0a486baa2184a58ac33c24a5d13973885890f
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109513"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Início Rápido: APIs REST do QnA Maker para Node.js

Introdução às APIs REST do QnA Maker para Node.js. Siga estas etapas para experimentar o código de exemplo para tarefas básicas.  O QnA Maker permite ter um serviço avançado de perguntas e respostas a partir de um conteúdo semiestruturado, como documentos de perguntas frequentes ou URLs e manuais de produtos.

Use as APIs REST do QnA Maker para Node.js para:

* Como criar uma base de dados de conhecimento
* Substituir uma base de dados de conhecimento
* Como publicar uma base de dados de conhecimento
* Excluir uma base de dados de conhecimento
* Baixar uma base de dados de conhecimento
* Obter o status de uma operação

[Documentação de referência](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Exemplos do Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar a chave e o ponto de extremidade (que inclui o nome do recurso), selecione **Início Rápido** para o recurso no portal do Azure.

## <a name="setting-up"></a>Configurando

### <a name="create-a-qna-maker-azure-resource"></a>Criar um recurso do Azure do QnA Maker

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o QnA Maker usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local.

Depois de obter uma chave de seu recurso, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o recurso chamadas `QNAMAKER_RESOURCE_KEY` e `QNAMAKER_AUTHORING_ENDPOINT`. Use os valores de chave e de ponto de extremidade encontrados na página **Início Rápido** do recurso no portal do Azure.

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init -y` para criar um arquivo `package.json` de nó.

```console
npm init -y
```

Adicione os pacotes NPM `reqeuestretry` e `request`:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com as APIs REST do QnA Maker para Node.js:

* [Criar uma base de dados de conhecimento](#create-a-knowledge-base)
* [Substituir uma base de dados de conhecimento](#replace-a-knowledge-base)
* [Publicar uma base de dados de conhecimento](#publish-a-knowledge-base)
* [Excluir uma base de dados de conhecimento](#delete-a-knowledge-base)
* [Baixar uma base de dados de conhecimento](#download-the-knowledge-base)
* [Obter o status de uma operação](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adicionar as dependências

Crie um arquivo chamado `rest-apis.js` e adicione a instrução _requires_ a seguir para fazer solicitações HTTP.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Adicionar informações de recursos do Azure

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

Defina os seguintes valores de ambiente:

* `QNAMAKER_RESOURCE_KEY` – A **chave** é uma cadeia de 32 caracteres e está disponível no portal do Azure, no recurso QnA Maker, na página Início Rápido. Isso não é o mesmo que a chave de ponto de extremidade de previsão.
* `QNAMAKER_AUTHORING_ENDPOINT` – Seu ponto de extremidade de criação, no formato `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, inclui o seu **nome do recurso**. Essa não é a mesma URL usada para consultar o ponto de extremidade de previsão.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

Uma base de dados de conhecimento armazena pares de perguntas e respostas criados com base em um objeto JSON de:

* **Conteúdo editorial**.
* **Arquivos** – arquivos locais que não exigem nenhuma permissão.
* **URLs** – URLs disponíveis publicamente.

Use a [API REST para criar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Substituir uma base de dados de conhecimento

Use a [API REST para substituir uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Como publicar uma base de dados de conhecimento

Publicar a base de dados de conhecimento. Esse processo torna a base de dados de conhecimento disponível de um ponto de extremidade de previsão de consulta HTTP.

Use a [API REST para publicar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Baixar a base de dados de conhecimento

Use a [API REST para baixar uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

Quando você terminar de usar a base de dados de conhecimento, exclua-a.

Use a [API REST para excluir uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obter o status de uma operação

Processos de execução longa, como o processo de criação, retornam uma ID de operação, que precisa ser verificada com uma chamada à API REST separada. Essa função usa o corpo da resposta de criação. A chave importante é o `operationState`, que determina se você precisa continuar a sondagem.

Use a [API REST para monitorar operações em uma base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node rest-apis.js` do seu próprio diretório de aplicativo.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Tutorial: Criar e responder a uma KB](../tutorials/create-publish-query-in-portal.md)

* [O que é a API de QnA Maker?](../Overview/overview.md)
* [Editar uma base de dados de conhecimento](../how-to/edit-knowledge-base.md)
* [Obter a análise de uso](../how-to/get-analytics-knowledge-base.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).