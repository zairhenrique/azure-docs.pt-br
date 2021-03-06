---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: d3e2fa082184ce8de7685d03803360045b89c911
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119783"
---
Para criar um projeto do Visual Studio para desenvolvimento da UWP (Plataforma Universal do Windows), você precisa configurar as opções de desenvolvimento do Visual Studio, criar o projeto, selecionar a arquitetura de destino, configurar a captura de áudio e instalar o SDK de Fala.

### <a name="set-up-visual-studio-development-options"></a>Configurar as opções de desenvolvimento do Visual Studio

Para começar, verifique se o Visual Studio está configurado corretamente para o desenvolvimento da UWP:

1. Abra o Visual Studio 2019 para exibir a janela **Iniciar**.

   ![Janela inicial – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Selecione **Continuar sem código** para acessar o IDE do Visual Studio.

1. Na barra de menus do Visual Studio, selecione **Ferramentas** > **Obter ferramentas e recursos** para abrir o Instalador do Visual Studio e exibir a caixa de diálogo **Modificação**.

   ![Guia Cargas de trabalho, caixa de diálogo Modificação, Instalador do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na guia **Cargas de trabalho**, em **Windows**, encontre a carga de trabalho de desenvolvimento da **Plataforma Universal do Windows**. Se a caixa de seleção ao lado dessa carga de trabalho já estiver selecionada, feche a caixa de diálogo **Modificação** e vá para a etapa 6.

1. Marque a caixa de seleção de **desenvolvimento da Plataforma Universal do Windows**, selecione **Modificar** e, em seguida, na caixa de diálogo **Antes de começar**, selecione **Continuar** para instalar a carga de trabalho de desenvolvimento da UWP. A instalação do novo recurso pode levar algum tempo.

1. Feche o Instalador do Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Criar o projeto e selecionar a arquitetura de destino

Em seguida, crie seu projeto:

1. Na barra de menus do Visual Studio, escolha **Arquivo** > **Novo** > **Projeto** para exibir a janela **Criar projeto**.

   ![Criar projeto – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Localize e selecione **Aplicativo em branco (Universal do Windows)** . Certifique-se de selecionar a versão C# desse tipo de projeto (em vez de Visual Basic).

1. Selecione **Avançar** para exibir a tela **Configurar seu novo projeto**.

   ![Configurar seu novo projeto – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Em **Nome do projeto**, insira `helloworld`.

1. Em **Localização**, navegue até a pasta em que deseja salvar o projeto e selecione-a ou, caso ainda não exista, crie-a.

1. Selecione **Criar** para acessar a janela **Novo Projeto da Plataforma Universal do Windows**.

   ![Nova caixa de diálogo do Projeto da Plataforma Universal do Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Em **Versão mínima** (a segunda caixa suspensa), escolha **Windows 10 Fall Creators Update (10.0; Build 16299)** , que é o requisito mínimo para o SDK de Fala.

1. Em **Versão de destino** (a primeira caixa suspensa), escolha um valor idêntico (ou posterior) ao valor em **Versão mínima**.

1. Selecione **OK**. Você será direcionado de volta ao IDE do Visual Studio, com o novo projeto criado e visível no painel do **Gerenciador de Soluções**.

   ![Projeto Olá Mundo – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Agora, selecione a arquitetura da plataforma de destino. Na barra de ferramentas do Visual Studio, localize a caixa suspensa **Plataformas de Solução**. (Caso não a veja, escolha **Exibir** > **Barras de Ferramentas** > **Padrão** para exibir a barra de ferramentas que contém a caixa **Plataformas de Solução**.) Se você estiver executando o Windows de 64 bits, escolha **x64** na caixa suspensa. O Windows de 64 bits também pode executar aplicativos de 32 bits. Portanto, você poderá escolher **x86**, se preferir.

> [!NOTE]
> O SDK de Fala dá suporte somente a processadores compatíveis com Intel. Processadores ARM não são compatíveis no momento.

### <a name="set-up-audio-capture"></a>Configurar a captura de áudio

Em seguida, permitir que o projeto capture a entrada de áudio:

1. No **Gerenciador de Soluções**, clique duas vezes em **Package.appxmanifest** para abrir o manifesto do aplicativo de pacotes.

1. Selecione a guia **Funcionalidades**.

   ![Guia Funcionalidades, manifesto do aplicativo de pacotes – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Selecione a caixa da funcionalidade **Microfone**.

1. Na barra de menus, escolha **Arquivo** > **Salvar Package.appxmanifest** para salvar suas alterações.

### <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Por fim, instale o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e referencie o SDK de Fala no seu projeto:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet para Solução** a fim de acessar a janela **NuGet – Solução**.

1. Selecione **Procurar**.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Em **Origem do pacote**, escolha **nuget.org**.

1. Na caixa **Pesquisar**, insira `Microsoft.CognitiveServices.Speech` e escolha o pacote depois que ele aparecer nos resultados da pesquisa.

   ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. No painel de status do pacote, ao lado dos resultados da pesquisa, selecione seu projeto **Olá Mundo**.

1. Selecione **Instalar**.

1. Na caixa de diálogo **Visualizar Alterações**, selecione **OK**.

1. Na caixa de diálogo **Aceitação da Licença**, exiba a licença e, em seguida, selecione **Aceito**. A instalação do pacote começa e, ao ser concluída, o painel **Saída** exibe uma mensagem semelhante ao seguinte texto: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.9.0' to helloworld`.
