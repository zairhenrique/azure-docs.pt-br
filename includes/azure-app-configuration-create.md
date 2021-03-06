---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 017833403beca6fb8689bd6f90cc8f460d56c65e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961592"
---
1. Para criar um novo repositório de Configurações de Aplicativo, entre no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da Página Inicial, selecione **Criar um recurso**. Na caixa **Pesquisar no Marketplace**, insira **Configuração de Aplicativos** e selecione Enter.

    ![Pesquisar a Configuração de Aplicativo](media/azure-app-configuration-create/azure-portal-search.png)

1. Selecione **Configuração de Aplicativo** nos resultados da pesquisa e, em seguida, selecione **Criar**.

    ![Selecione Criar](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. No painel **Configuração de Aplicativos** > **Criar**, insira as configurações a seguir:

    | Configuração | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome exclusivo do recurso a ser usado para o recurso do repositório de Configurações de Aplicativo. O nome deve ser uma cadeia de 5 a 50 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode começar nem terminar com o caractere `-`.  |
    | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar para testar a Configuração de Aplicativo. Se a conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista **Assinatura** não será exibida. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso do repositório de Configurações de Aplicativo. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo, excluindo o grupo de recursos. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localidade** | *Centro dos EUA* | Use **Localização** para especificar a localização geográfica na qual o repositório de configurações de aplicativo está hospedado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes do aplicativo. |

    ![Criar um recurso do repositório de Configurações de Aplicativo](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selecione **Criar**. A implantação pode levar alguns minutos.

1. Após a conclusão da implantação, selecione **Configurações** > **Chaves de acesso**. Anote a cadeia de conexão de chave somente leitura primária. Você usará essa cadeia de conexão posteriormente para configurar o aplicativo a comunicar-se com o repositório de Configurações de Aplicativo que você criou.
