---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482432"
---
1. Inicie o Visual Studio 2019.

1. Verifique se a carga de trabalho **Desenvolvimento multiplataforma do .NET** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, feche a caixa de diálogo.

   ![Captura de tela do instalador do Visual Studio, com a guia Cargas de Trabalho realçada](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Caso contrário, marque a caixa ao lado de **Desenvolvimento multiplataforma do .NET Core** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação do novo recurso levará alguns instantes.

1. Crie um novo Aplicativo de Console do .NET Core do Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** > **Visual C#**  >  **.NET Core**. Em seguida, selecione **Aplicativo do Console (.NET Core)** . Para o nome do projeto, insira *helloworld*.

   ![Captura de tela da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Criar Aplicativo de Console do Visual C# (.NET Core)")

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a Solução**.

   ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gerenciar Pacotes NuGet para a Solução")

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para a Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

   ![Captura de tela da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Aceitar a licença")

Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.
