---
title: Implantar no host dedicado
description: Use um host dedicado para obter um isolamento verdadeiro no nível de host para suas cargas de trabalho de instâncias de contêiner do Azure
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934140"
---
# <a name="deploy-on-dedicated-hosts"></a>Implantar em hosts dedicados

"Dedicado" é um SKU de ACI (instâncias de contêiner do Azure) que fornece um ambiente de computação isolado e dedicado para executar contêineres com segurança. O uso do SKU dedicado resulta em cada grupo de contêineres com um servidor físico dedicado em um datacenter do Azure, garantindo o isolamento completo da carga de trabalho para ajudar a atender aos requisitos de segurança e conformidade da sua organização. 

O SKU dedicado é apropriado para cargas de trabalho de contêiner que exigem isolamento de carga de trabalho de uma perspectiva de servidor físico.

## <a name="prerequisites"></a>Pré-requisitos

* O limite padrão para qualquer assinatura usar o SKU dedicado é 0. Se você quiser usar essa SKU para suas implantações de contêiner de produção, crie uma [solicitação de suporte do Azure][azure-support] para aumentar o limite.

## <a name="use-the-dedicated-sku"></a>Usar o SKU dedicado

> [!IMPORTANT]
> O uso do SKU dedicado só está disponível na versão mais recente da API (2019-12-01) que está sendo distribuída no momento. Especifique essa versão de API em seu modelo de implantação.
>

A partir da versão de API 2019-12-01, há uma propriedade `sku` na seção Propriedades do grupo de contêineres de um modelo de implantação, que é necessária para uma implantação do ACI. No momento, você pode usar essa propriedade como parte de um modelo de implantação Azure Resource Manager para ACI. Saiba mais sobre a implantação de recursos do ACI com um modelo no [tutorial: implantar um grupo de vários contêineres usando um modelo do Resource Manager](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

A propriedade `sku` pode ter um dos seguintes valores:
* `Standard`-a opção de implantação ACI padrão, que ainda garante a segurança em nível de hipervisor 
* `Dedicated`-usado para isolamento de nível de carga de trabalho com hosts físicos dedicados para o grupo de contêineres

## <a name="modify-your-json-deployment-template"></a>Modificar seu modelo de implantação JSON

Em seu modelo de implantação, modifique ou adicione as seguintes propriedades:
* Em `resources`, defina `apiVersion` como `2012-12-01`.
* Nas propriedades do grupo de contêineres, adicione uma propriedade `sku` com valor `Dedicated`.

Aqui está um trecho de código de exemplo para a seção de recursos de um modelo de implantação de grupo de contêineres que usa o SKU dedicado:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Veja a seguir um modelo completo que implanta um grupo de contêineres de exemplo executando uma única instância de contêiner:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Implantar seu grupo de contêineres

Se você criou e editou o arquivo de modelo de implantação em sua área de trabalho, você pode carregá-lo em seu diretório Cloud Shell arrastando o arquivo para ele. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Em alguns segundos, você deverá receber uma resposta inicial do Azure. Uma implantação bem-sucedida ocorre em um host dedicado.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
