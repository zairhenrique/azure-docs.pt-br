---
title: Inserir Video Indexer widgets em seus aplicativos
titleSuffix: Azure Media Services
description: Este artigo demonstra como inserir os widgets Video Indexer de serviços de mídia do Azure em seu aplicativo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: ed3e2cf9830e3776886e662fd27f43f76728d6b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988712"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Inserir Video Indexer widgets em seus aplicativos

Este artigo mostra como você pode inserir Video Indexer widgets em seus aplicativos. O Video Indexer dá suporte à inserção de três tipos de widgets em seu aplicativo: *ideias cognitivas*, *Player*e *Editor*. 

A partir da versão 2, a URL base do widget inclui a região da conta especificada. Por exemplo, uma conta da região Oeste dos EUA gera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Um widget de análise cognitiva inclui todos os insights visuais que foram extraídos do seu processo de indexação de vídeo. O widget cognitiva insights dá suporte aos seguintes parâmetros de URL opcionais.

|Nome|Definição|Description|
|---|---|---|
|`widgets`|Cadeias de caracteres separadas por vírgula|Permite que você controle as informações que deseja renderizar. <br/> Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` renderiza apenas pessoas e marcas da interface do usuário do.<br/>Opções disponíveis: people, keywords, annotations, brands, sentiments, transcript e search.<br/>Observe que o parâmetro de URL `widgets` não tem suporte na versão 2.<br/>|
|`locale`|Um código de idioma curto|Controla o idioma do insights. O valor padrão é `en`. <br/> Exemplo: `locale=de`.|
|`tab`|A guia selecionada padrão|Controla a guia **insights** que é renderizada por padrão. <br/> Exemplo: `tab=timeline` renderiza as informações com a guia **linha do tempo** selecionada.|

### <a name="player-widget"></a>Widget do player

Você pode usar o widget Player para transmitir vídeo usando a taxa de bits adaptável. O widget Player dá suporte aos seguintes parâmetros de URL opcionais.

|Nome|Definição|Description|
|---|---|---|
|`t`|Segundos desde o início|Faz com que o Player comece a ser reproduzido do ponto de tempo especificado.<br/> Exemplo: `t=60`.|
|`captions`|Um código de idioma|Busca a legenda no idioma especificado durante o carregamento do widget para estar disponível no menu **legendas** .<br/> Exemplo: `captions=en-US`.|
|`showCaptions`|Um valor booliano|Faz o player ser carregado com as legendas já habilitadas.<br/> Exemplo: `showCaptions=true`.|
|`type`||Ativa uma capa de player de áudio (a parte de vídeo é removida).<br/> Exemplo: `type=audio`.|
|`autoplay`|Um valor booliano|Indica se o Player deve começar a reproduzir o vídeo quando carregado. O valor padrão é `true`.<br/> Exemplo: `autoplay=false`.|
|`language`|Um código de idioma|Controla o idioma do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`.|

### <a name="editor-widget"></a>Widget do editor

Você pode usar o widget editor para criar novos projetos e gerenciar informações de um vídeo. O widget editor dá suporte aos seguintes parâmetros de URL opcionais.

|Nome|Definição|Description|
|---|---|---|
|`accessToken`<sup>*</sup>|String|Fornece acesso a vídeos que estão apenas na conta que é usada para inserir o widget.<br> O widget do editor requer o parâmetro `accessToken`.|
|`language`|Um código de idioma|Controla o idioma do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`.|
|`locale`|Um código de idioma curto|Controla o idioma do insights. O valor padrão é `en`.<br/>Exemplo: `language=de`.|

<sup>*</sup> O proprietário deve fornecer `accessToken` com cautela.

## <a name="embedding-public-content"></a>Inserindo conteúdo público

1. Entre no site do [Video indexer](https://www.videoindexer.ai/) .
2. Selecione o vídeo com o qual você deseja trabalhar.
3. Selecione o botão **Inserir** que aparece sob o vídeo.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Depois de selecionar o botão **Inserir** , você pode selecionar o widget que deseja inserir em seu aplicativo. 
4. Selecione o tipo de widget que você deseja (**ideias cognitivas**, **Player**ou **Editor**).
 
5. Copie o código de inserção e, em seguida, adicione-o ao seu aplicativo. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Se você tiver problemas para compartilhar suas URLs de vídeo, adicione o parâmetro `location` ao link. O parâmetro deve ser definido para as [regiões do Azure nas quais Video indexer existe](regions.md). Por exemplo: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Inserindo conteúdo privado

Para inserir um vídeo privado, você deve passar um token de acesso no atributo **src** do iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Para obter o conteúdo do widget do insights cognitiva, use um dos seguintes:<br/>
- A API do [widget obter insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- O [token de acesso obter vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adicione-o como um parâmetro de consulta à URL. Especifique essa URL como o valor **src** para o iframe, conforme mostrado anteriormente.

Para fornecer recursos de edição de informações em seu widget inserido, você deve passar um token de acesso que inclui permissões de edição. Use o [widget obter informações](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou [obtenha o token de acesso do vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) com `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interação de widgets

O widget cognitiva insights pode interagir com um vídeo em seu aplicativo. Esta seção mostra como conseguir essa interação.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações entre origens

Para obter Video Indexer widgets se comuniquem com outros componentes, o serviço Video Indexer:

- Usa a **mensagem**do método HTML5 de comunicação entre origens. 
- Valida a mensagem entre a origem do VideoIndexer.ai. 

Se você implementar seu próprio código do Player e integrá-lo a widgets cognitivas, é sua responsabilidade validar a origem da mensagem que vem de VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Inserir widgets em seu aplicativo ou blog (recomendado) 

Esta seção mostra como obter a interação entre dois widgets Video Indexer para que, quando um usuário selecionar o controle Insight em seu aplicativo, o jogador salte para o momento relevante.

1. Copie o código de inserção do widget do Player.
2. Copie o código de inserção de insights cognitivas.
3. Adicione o [arquivo mediador](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para lidar com a comunicação entre os dois widgets:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um usuário seleciona o controle Insight em seu aplicativo, o jogador salta para o momento relevante.

Para obter mais informações, consulte a [demonstração Video indexer-incorporar widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Inserir o widget de Insights cognitivos e usar o Player de Mídia do Azure para reproduzir o conteúdo

Esta seção mostra como obter a interação entre um widget do insights cognitiva e uma instância de Player de Mídia do Azure usando o [plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Adicionar um plug-in de Video Indexer para o AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instancie Player de Mídia do Azure com o plug-in de Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Copie o código de inserção de insights cognitivas.

Agora você deve ser capaz de se comunicar com Player de Mídia do Azure.

Para obter mais informações, consulte a [demonstração do player de mídia do Azure + vi insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Inserir o widget Video Indexer cognitiva insights e usar um player de vídeo diferente

Se você usar um player de vídeo diferente de Player de Mídia do Azure, será necessário manipular manualmente o player de vídeo para obter a comunicação. 

1. Insira seu player de vídeo.

    Por exemplo, um player HTML5 padrão:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Insira o widget de Insights cognitivos.
3. Implemente a comunicação para seu player escutando o evento de "mensagem". Por exemplo:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Para obter mais informações, consulte a [demonstração do player de mídia do Azure + vi insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionando legendas

Se você inserir informações de Video Indexer com seu próprio [player de mídia do Azure](https://aka.ms/azuremediaplayer), poderá usar o método **GetVttUrl** para obter legendas ocultas (legendas). Você também pode chamar um método JavaScript do Video Indexer AMP plug-in **getSubtitlesUrl** (conforme mostrado anteriormente). 

## <a name="customizing-embeddable-widgets"></a>Personalizando widgets que permitem inserção

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Você pode escolher os tipos de informações desejadas. Para fazer isso, especifique-os como um valor para o seguinte parâmetro de URL que é adicionado ao código de inserção que você obtém (da API ou do aplicativo Web): `&widgets=<list of wanted widgets>`.

Os valores possíveis são: **pessoas**, **palavras-chave**, **sentimentos**, **transcrição**e **pesquisa**.

Por exemplo, se você quiser inserir um widget que contenha apenas pessoas e Pesquisar percepções, a URL de inserção do iframe terá a seguinte aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

O título da janela do iframe também pode ser personalizado fornecendo `&title=<YourTitle>` à URL do iframe. (Ele personaliza o HTML \<título > valor).
    
Por exemplo, se você quiser dar à sua janela do iframe o título "MeusInsights", a URL terá esta aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observe que essa opção será relevante apenas em casos em que for necessário abrir os insights em uma nova janela.

### <a name="player-widget"></a>Widget do player

Se inserir o player do Video Indexer, você poderá escolher seu tamanho especificando o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por padrão, Video Indexer Player tem legendas codificadas geradas automaticamente com base na transcrição do vídeo. A transcrição é extraída do vídeo com o idioma de origem que foi selecionado quando o vídeo foi carregado.

Se você quiser inserir um idioma diferente, poderá adicionar `&captions=< Language | "all" | "false" >` à URL do player de inserção. Se você quiser legendas em todas as legendas de idiomas disponíveis, use o valor `all`. Se quiser que as legendas sejam exibidas por padrão, passe `&showCaptions=true`.

A URL de inserção terá esta aparência: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se você quiser desabilitar legendas, poderá passar o valor do parâmetro `captions` como `false`.

#### <a name="autoplay"></a>AutoPlay
Por padrão, o Player começará a reproduzir o vídeo. Você pode optar por não passar `&autoplay=false` para a URL de inserção anterior.

## <a name="code-samples"></a>Exemplos de código

Consulte o repositório de [exemplos de código](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) que contém exemplos para video indexer API e widgets:

| Arquivo/pasta                       | Description                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Carregar vídeo do indexador de vídeo em um Player de Mídia do Azure personalizado                        |
| `azure-media-player-vi-insights`  | Inserir ideias do VI com um Player de Mídia do Azure personalizado                             |
| `control-vi-embedded-player`      | Inserir o VI Player e controlá-lo de fora                                    |
| `custom-index-location`           | Inserir ideias do VI de um local externo personalizado (pode ser cliente um blob)     |
| `embed-both-insights`             | Uso básico do VI insights e insights                            |
| `embed-insights-with-AMP`         | Inserir o widget do VI insights com um Player de Mídia do Azure personalizado                      |
| `customize-the-widgets`           | Inserir widgets VI com opções personalizadas                                     |
| `embed-both-widgets`              | Inserir o VI Player e as informações e se comunicar entre eles                      |
| `url-generator`                   | Gera a URL de inserção personalizada de widgets com base nas opções especificadas pelo usuário             |
| `html5-player`                    | Inserir ideias do VI com um player de vídeo HTML5 padrão                            |

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como exibir e editar insights Video Indexer, consulte [Exibir e editar informações de video indexer](video-indexer-view-edit.md).

Além disso, confira [CodePen do indexador de vídeo](https://codepen.io/videoindexer/pen/eGxebZ).
