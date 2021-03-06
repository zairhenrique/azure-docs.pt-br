---
title: Usando a pesquisa no Aplicativo Azure insights | Microsoft Docs
description: Pesquisar e filtrar a telemetria bruta enviada pelo seu aplicativo Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/30/2019
ms.openlocfilehash: 77cd0a8d0c1a93e7dc1db931e987a172d31978ef
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678018"
---
# <a name="using-search-in-application-insights"></a>Usando a pesquisa no Application Insights

A pesquisa é um recurso do [Application insights](../../azure-monitor/app/app-insights-overview.md) que você usa para localizar e explorar itens individuais de telemetria, como exibições de página, exceções ou solicitações da Web. Você também pode exibir rastreamentos de log e eventos que você tenha codificado.

(Para consultas mais complexas sobre seus dados, use a [análise](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Onde você vê a pesquisa?

### <a name="in-the-azure-portal"></a>No portal do Azure

Você pode abrir a pesquisa de diagnóstico na guia Visão geral Application Insights do seu aplicativo (localizada na barra superior) ou em investigar à esquerda.

![Guia Pesquisar](./media/diagnostic-search/view-custom-events.png)

Vá para o menu suspenso tipos de evento para ver uma lista de itens de telemetria-solicitações de servidor, exibições de página, eventos personalizados que você tenha codificado e assim por diante. Na parte superior da lista de resultados, é um gráfico de resumo mostrando contagens de eventos ao longo do tempo.

Clique no menu suspenso ou em atualizar para obter novos eventos.

### <a name="in-visual-studio"></a>No Visual Studio

No Visual Studio, há também uma janela de pesquisa Application Insights. É mais útil para exibir eventos de telemetria gerados pelo aplicativo que você está depurando. Mas ele também pode mostrar os eventos coletados de seu aplicativo publicado no portal do Azure.

Abra a janela Pesquisar no Visual Studio:

![Pesquisa do Visual Studio Open Application Insights](./media/diagnostic-search/32.png)

A janela de pesquisa tem recursos semelhantes ao portal da Web:

![Janela de pesquisa do Visual Studio Application Insights](./media/diagnostic-search/34.png)

A guia rastrear operação está disponível quando você abre uma solicitação ou uma exibição de página. Uma ' operação ' é uma sequência de eventos associada a uma única solicitação ou exibição de página. Por exemplo, chamadas de dependência, exceções, logs de rastreamento e eventos personalizados podem fazer parte de uma única operação. A guia rastrear operação mostra graficamente o tempo e a duração desses eventos em relação à exibição de solicitação ou de página.

## <a name="inspect-individual-items"></a>Inspecionar itens individuais

Selecione qualquer item de telemetria para ver campos de chave e itens relacionados.

![Captura de tela de uma solicitação de dependência individual](./media/diagnostic-search/telemetry-item.png)

Isso iniciará a exibição de detalhes da transação de ponta a ponta.

## <a name="filter-event-types"></a>Filtrar tipos de evento

Abra o menu suspenso tipos de eventos e escolha os tipos de eventos que você deseja ver. (Se, posteriormente, você quiser restaurar os filtros, clique em Redefinir.)

Os tipos de evento são:

* **Rastreamento**  - [logs de diagnóstico](../../azure-monitor/app/asp-net-trace-logs.md) , incluindo chamadas TrackTrace, log4net, NLog e System. Diagnostics. Trace.
* Solicitações HTTP de **solicitação** recebidas pelo aplicativo de servidor, incluindo páginas, scripts, imagens, arquivos de estilo e dados. Esses eventos são usados para criar os gráficos de visão geral de solicitação e resposta.
* **Exibição de página**  - [telemetria enviada pelo cliente Web](../../azure-monitor/app/javascript.md), usada para criar relatórios de exibição de página.
* **Evento personalizado** – se você inseriu chamadas para TrackEvent () para [monitorar o uso](../../azure-monitor/app/api-custom-events-metrics.md), você pode pesquisá-las aqui.
* **Exceção** -exceções não capturadas [no servidor](../../azure-monitor/app/asp-net-exceptions.md)e aquelas que você registra usando trackexception ().
* A **dependência**  - [chamadas de seu aplicativo de servidor](../../azure-monitor/app/asp-net-dependencies.md) para outros serviços, como APIs REST ou bancos de dados, e chamadas AJAX do seu código de [cliente](../../azure-monitor/app/javascript.md).
* **Disponibilidade** -resultados de [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrar nos valores de propriedade

Você pode filtrar eventos nos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou. Clique no ícone de filtro ![Ícone de filtro](./media/diagnostic-search/filter-icon.png) para iniciar.

Escolher nenhum valor de uma propriedade específica tem o mesmo efeito que escolher todos os valores. Ele desativa a filtragem nessa propriedade.

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

## <a name="find-events-with-the-same-property"></a>Localizar eventos com a mesma propriedade

Para localizar todos os itens com o mesmo valor de propriedade, digite-os na barra de pesquisa ou clique na caixa de seleção ao examinar propriedades na guia filtro.

![Clique na caixa de seleção de uma propriedade na guia filtro](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Pesquisar os dados

> [!NOTE]
> Para gravar consultas mais complexas, abra [**logs (análise)** ](../../azure-monitor/log-query/get-started-portal.md) na parte superior da folha de pesquisa.
>

Você pode pesquisar termos em qualquer um dos valores de propriedade. Isso será útil se você tiver escrito [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md) com valores de propriedade.

Você talvez queira definir um tempo de intervalo, já que pesquisas em um intervalo mais curto são mais rápidas.

![Abra a pesquisa de diagnóstico](./media/diagnostic-search/search-property.png)

Pesquisar palavras completas, não subcadeias de caracteres. Use aspas para incluir caracteres especiais.

| Cadeia de caracteres | *Não* encontrado | Encontrado |
| --- | --- | --- |
| HomeController. About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Estados Unidos|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Aqui estão as expressões de pesquisa que você pode usar:

| Consulta de exemplo | Efeito |
| --- | --- |
| `apple` |Localizar todos os eventos no intervalo de tempo cujos campos incluem a palavra "Apple" |
| `apple AND banana` <br/>`apple banana` |Localizar eventos que contenham ambas as palavras. Use "AND” em letras maiúsculas, e não "and". <br/>Forma abreviada. |
| `apple OR banana` |Localizar eventos que contenham uma das palavras. Use "OR", não "or". |
| `apple NOT banana` |Localizar eventos que contenham uma palavra, mas não a outra. |

## <a name="sampling"></a>Amostragem

Se seu aplicativo gerar uma grande quantidade de telemetria (e você estiver usando o ASP.NET SDK versão 2.0.0-Beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume que é enviado ao portal enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação são selecionados ou desmarcados como um grupo, para que você possa navegar entre os eventos relacionados.

[Saiba mais sobre amostragem](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Criar um item de trabalho

Você pode criar um bug no GitHub ou no Azure DevOps com os detalhes de qualquer item de telemetria.

Vá para a exibição de detalhes da transação de ponta a ponta clicando em qualquer item de telemetria e, em seguida, selecione **Criar item de trabalho**.

![Clique em Novo Item de Trabalho, edite os campos e, em seguida, clique em OK.](./media/diagnostic-search/work-item.png)

Na primeira vez que você fizer isso, será solicitado que você configure um link para sua organização e projeto do Azure DevOps.

(Você também pode configurar o link na guia itens de trabalho.)

## <a name="send-more-telemetry-to-application-insights"></a>Enviar mais telemetria para Application Insights

Além de telemetria da caixa enviada pelo SDK do Application Insights, você pode:

* Capture rastreamentos de log de sua estrutura de log favorita em [.net](../../azure-monitor/app/asp-net-trace-logs.md) ou [Java](../../azure-monitor/app/java-trace-logs.md). Isso significa que você pode pesquisar os rastreamentos de log e correlacioná-los com exibições de página, exceções e outros eventos.
* [Escreva o código](../../azure-monitor/app/api-custom-events-metrics.md) para enviar eventos personalizados, exibições de página e exceções.

[Saiba como enviar logs e telemetria personalizada para Application insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>Perguntas e respostas

### <a name="limits"></a>Que quantidade de dados é mantida?

Consulte o [Resumo de limites](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Como consultar dados de POSTAGEM nas minhas solicitações de servidor?

Nós não registramos os dados de POSTAgem automaticamente, mas você pode usar [TrackTrace ou chamadas de log](../../azure-monitor/app/asp-net-trace-logs.md). Coloque os dados de POSTAGEM no parâmetro de mensagem. Não é possível filtrar a mensagem da mesma maneira que é possível filtrar as propriedades, mas o limite de tamanho é maior.

## <a name="add"></a>Próximas etapas

* [Escrever consultas complexas no Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Enviar logs e telemetria personalizada para Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Configurar testes de disponibilidade e capacidade de resposta](../../azure-monitor/app/monitor-web-app-availability.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
