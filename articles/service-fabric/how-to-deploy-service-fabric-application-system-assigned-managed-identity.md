---
title: Implantar um aplicativo Service Fabric com MI atribuído pelo sistema
description: Este artigo mostra como atribuir uma identidade gerenciada atribuída pelo sistema a um aplicativo de Service Fabric do Azure
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614818"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Implantar Service Fabric aplicativo com identidade gerenciada atribuída pelo sistema (versão prévia)

Para acessar o recurso de identidade gerenciada para aplicativos Service Fabric do Azure, primeiro você deve habilitar o serviço de token de identidade gerenciado no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE] 
> A implantação de aplicativos Service Fabric com identidades gerenciadas tem suporte a partir da versão de API `"2019-06-01-preview"`. Você também pode usar a mesma versão de API para tipo de aplicativo, versão de tipo de aplicativo e recursos de serviço. O tempo de execução mínimo com suporte Service Fabric é 6,5 CU2. No additoin, o ambiente de compilação/pacote também deve ter o SDK do .NET da it em CU2 ou superior

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

### <a name="application-template"></a>Modelo de aplicativo

Para habilitar o aplicativo com uma identidade gerenciada atribuída pelo sistema, adicione a propriedade **Identity** ao recurso de aplicativo, com o tipo **systemAssigned** , conforme mostrado no exemplo abaixo:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Essa propriedade declara (para Azure Resource Manager e a identidade gerenciada e os provedores de recursos de Service Fabric, respectivamente, que esse recurso deve ter uma identidade gerenciada implícita (`system assigned`).

### <a name="application-and-service-package"></a>Pacote de aplicativo e serviço

1. Atualize o manifesto do aplicativo para adicionar um elemento **ManagedIdentity** na seção **entidades** , contendo uma única entrada, como mostrado abaixo:

    **ApplicationManifest. xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Isso mapeia a identidade atribuída ao aplicativo como um recurso para um nome amigável, para atribuição adicional aos serviços que compõem o aplicativo. 

2. Na seção **ServiceManifestImport** correspondente ao serviço que está sendo atribuído à identidade gerenciada, adicione um elemento **IdentityBindingPolicy** , conforme indicado abaixo:

    **ApplicationManifest. xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Esse elemento atribui a identidade do aplicativo ao serviço; sem essa atribuição, o serviço não será capaz de acessar a identidade do aplicativo. No trecho acima, a identidade de `SystemAssigned` (que é uma palavra-chave reservada) é mapeada para a definição do serviço sob o nome amigável `WebAdmin`.

3. Atualize o manifesto do serviço para adicionar um elemento **ManagedIdentity** dentro da seção de **recursos** com o nome correspondente ao valor da configuração de `ServiceIdentityRef` da definição de `IdentityBindingPolicy` no manifesto do aplicativo:

    **Manifesto. xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Esse é o mapeamento equivalente de uma identidade para um serviço, conforme descrito acima, mas da perspectiva da definição de serviço. A identidade é referenciada aqui por seu nome amigável (`WebAdmin`), conforme declarado no manifesto do aplicativo.

## <a name="next-steps"></a>Próximas etapas
* Examinar o [suporte de identidade gerenciada](./concepts-managed-identity.md) no Azure Service Fabric
* [Implantar um novo](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster de Service Fabric do Azure com suporte de identidade gerenciada 
* [Habilitar identidade gerenciada](./configure-existing-cluster-enable-managed-identity-token-service.md) em um cluster existente do Service Fabric do Azure
* Aproveite a identidade gerenciada de um aplicativo Service Fabric [do código-fonte](./how-to-managed-identity-service-fabric-app-code.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)
