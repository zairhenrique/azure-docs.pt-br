---
title: Usar a análise de alterações do aplicativo no Azure Monitor para encontrar problemas do aplicativo Web | Microsoft Docs
description: Use a análise de alterações do aplicativo no Azure Monitor para solucionar problemas de aplicativos em sites ativos no serviço Azure App.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: f2602dbee12f82c32ab3a3c2ec0566d8dfbeaa83
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211827"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usar a análise de alterações do aplicativo (versão prévia) no Azure Monitor

Quando ocorre um problema ou uma interrupção do site ativo, determinar rapidamente a causa raiz é fundamental. As soluções de monitoramento padrão podem alertá-lo sobre um problema. Eles podem até mesmo indicar qual componente está falhando. Mas esse alerta nem sempre explicará imediatamente a causa da falha. Você sabe que seu site funcionou cinco minutos atrás e agora ele está interrompido. O que mudou nos últimos cinco minutos? Essa é a pergunta que a análise de alterações do aplicativo foi projetada para responder em Azure Monitor.

Aproveitando o poder do [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), a análise de alterações fornece informações sobre as alterações do aplicativo do Azure para aumentar a capacidade de observação e reduzir o MTTR (tempo médio de reparo).

> [!IMPORTANT]
> A análise de alterações está atualmente em visualização. Esta versão de visualização é fornecida sem um contrato de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter recursos restritos. Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Visão geral

A análise de alterações detecta vários tipos de alterações, da camada de infraestrutura até a implantação do aplicativo. É um provedor de recursos do Azure de nível de assinatura que verifica as alterações de recursos na assinatura. A análise de alterações fornece dados para várias ferramentas de diagnóstico para ajudar os usuários a entender quais alterações podem ter causado problemas.

O diagrama a seguir ilustra a arquitetura da análise de alterações:

![Diagrama de arquitetura de como a análise de alterações obtém dados de alteração e fornece a ele ferramentas de cliente](./media/change-analysis/overview.png)

Atualmente, a análise de alterações é integrada à experiência **diagnosticar e resolver problemas** no aplicativo Web do serviço de aplicativo, bem como disponível como uma guia autônoma no portal do Azure.
Consulte a seção *exibindo alterações de todos os recursos no Azure* para acessar a folha de análise de alterações e a seção *análise de alterações para o recurso aplicativos Web* para usá-la no portal de aplicativos Web mais adiante neste artigo.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager alterações de propriedades rastreadas

Usando o [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), a análise de alterações fornece um registro histórico de como os recursos do Azure que hospedam seu aplicativo foram alterados ao longo do tempo. Configurações rastreadas, como identidades gerenciadas, atualização do sistema operacional da plataforma e nomes de host podem ser detectadas.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Alterações de configuração Azure Resource Manager com proxy
As configurações como regra de configuração de IP, configurações de SSL e versões de extensão ainda não estão disponíveis em ARG, portanto, as consultas de análise de alterações e calculam essas alterações com segurança para fornecer mais detalhes sobre o que mudou no aplicativo. Essas informações ainda não estão disponíveis no grafo de recursos do Azure, mas estarão disponíveis em breve.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implantação e na configuração do aplicativo Web (alterações no convidado)

A análise de alterações captura o estado de implantação e configuração de um aplicativo a cada 4 horas. Ele pode detectar, por exemplo, alterações nas variáveis de ambiente do aplicativo. A ferramenta computa as diferenças e apresenta o que mudou. Ao contrário das alterações do Resource Manager, as informações de alteração da implantação de código podem não estar disponíveis imediatamente na ferramenta. Para exibir as alterações mais recentes na análise de alterações, selecione **verificar alterações agora**.

![Captura de tela do botão "examinar alterações agora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas em um aplicativo Web. Por exemplo, se um aplicativo Web chamar um cache Redis, a SKU do cache Redis poderá afetar o desempenho do aplicativo Web. Para detectar alterações nas dependências, a análise de alterações verifica o registro DNS do aplicativo Web. Dessa forma, ele identifica as alterações em todos os componentes de aplicativo que podem causar problemas.
Atualmente, há suporte para as seguintes dependências:
- Aplicativos Web
- Armazenamento do Azure
- SQL do Azure

### <a name="enablement"></a>Habilitação
O provedor de recursos "Microsoft. ChangeAnalysis" precisa ser registrado com uma assinatura para o Azure Resource Manager propriedades rastreadas e as configurações de proxy alteram os dados para que estejam disponíveis. À medida que você insere a ferramenta diagnosticar e solucionar problemas do aplicativo Web ou ativa a guia autônomo da análise de alterações, esse provedor de recursos é registrado automaticamente. Ele não tem nenhuma implementação de desempenho e custo para sua assinatura. Quando você habilita a análise de alterações para aplicativos Web (ou habilitando a ferramenta diagnosticar e resolver problemas), ele terá um impacto insignificante no desempenho do aplicativo Web e nenhum custo de cobrança.
Para alterações no convidado do aplicativo Web, a habilitação separada é necessária para a verificação de arquivos de código em um aplicativo Web. Para obter mais informações, consulte [habilitar a análise de alterações na seção ferramenta diagnosticar e resolver problemas](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) mais adiante neste artigo para obter mais detalhes.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Exibindo alterações para todos os recursos no Azure
Em Azure Monitor, há uma folha autônoma para a análise de alterações exibir todas as alterações com informações e recursos de dependências de aplicativos.

Procure análise de alterações na barra de pesquisa em portal do Azure para iniciar a folha.

![Captura de tela da pesquisa de análise de alterações no portal do Azure](./media/change-analysis/search-change-analysis.png)

Selecione o grupo de recursos e os recursos para começar a exibir as alterações.

![Captura de tela da folha de análise de alterações no portal do Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Você pode ver informações e recursos de dependências relacionados que hospedam seu aplicativo. Essa exibição foi projetada para ser centrada no aplicativo para que os desenvolvedores solucionem problemas.

Os recursos com suporte no momento incluem:
- Máquinas Virtuais
- Conjunto de dimensionamento de máquinas virtuais
- Recursos de rede do Azure
- Aplicativo Web com alterações de variáveis de ambiente e acompanhamento de arquivo no convidado

Para qualquer comentário, use o botão enviar comentários na folha ou changeanalysisteam@microsoft.comde email.

![Captura de tela do botão de comentários na folha de análise de alterações](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Análise de alteração para o recurso de aplicativos Web

Em Azure Monitor, a análise de alterações também é incorporada à experiência de **diagnóstico e solução de problemas** de autoatendimento. Acesse essa experiência na página **visão geral** do aplicativo do serviço de aplicativo.

![Captura de tela do botão "visão geral" e o botão "diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Habilitar a análise de alterações na ferramenta diagnosticar e resolver problemas

1. Selecione **disponibilidade e desempenho**.

    ![Captura de tela das opções de solução de problemas de "disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

1. Selecione **alterações do aplicativo**. Não que o recurso também esteja disponível em **falhas do aplicativo**.

   ![Captura de tela do botão "falhas do aplicativo"](./media/change-analysis/application-changes.png)

1. Para habilitar a análise de alterações, selecione **habilitar agora**.

   ![Captura de tela de opções de "falhas do aplicativo"](./media/change-analysis/enable-changeanalysis.png)

1. Ative a **análise de alterações** e selecione **salvar**. A ferramenta exibe todos os aplicativos Web em um plano do serviço de aplicativo. Você pode usar a opção de nível de plano para ativar a análise de alterações para todos os aplicativos Web em um plano.

    ![Captura de tela da interface do usuário "Habilitar análise de alteração"](./media/change-analysis/change-analysis-on.png)


1. Para acessar a análise de alterações, selecione **diagnosticar e resolver problemas** > **disponibilidade e desempenho** > **falhas do aplicativo**. Você verá um grafo que resume o tipo de alterações ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são exibidas para ajudar com problemas imediatos.

     ![Captura de tela da exibição alterar comparação](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Habilitar análise de alterações em escala

Se sua assinatura inclui vários aplicativos Web, a habilitação do serviço no nível do aplicativo Web seria ineficiente. Execute o script a seguir para habilitar todos os aplicativos Web em sua assinatura.

Pré-requisitos:
* Módulo AZ do PowerShell. Siga as instruções em [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Execute o seguinte script:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Próximas etapas

- Habilitar Application Insights para [aplicativos de serviços Azure apps](azure-web-apps.md).
- Habilite Application Insights para a [VM do Azure e o conjunto de dimensionamento de máquinas virtuais do Azure aplicativos hospedados pelo IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre o [grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/overview), que ajuda a análise de alterações de energia.
