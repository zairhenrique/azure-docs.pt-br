---
title: Perguntas frequentes - Solução de Monitor de Desempenho de Rede no Azure | Microsoft Docs
description: Este artigo captura as perguntas frequentes sobre Monitor de Desempenho de Rede no Azure. O Monitor de Desempenho de Rede (NPM) ajuda a monitorar o desempenho de suas redes quase em tempo real e detectar e localizar afunilamentos de desempenho de rede.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 7ee593a8db020134e13ea853f17f097d716f7814
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538195"
---
# <a name="network-performance-monitor-solution-faq"></a>Perguntas da Solução do Monitor de Desempenho de Rede

![Símbolo do Monitor de Desempenho de Rede](media/network-performance-monitor-faq/npm-symbol.png)

Este artigo captura as perguntas frequentes (FAQs) sobre o Monitor de Desempenho de Rede (NPM) no Azure

[O Monitor de Desempenho de Rede](/azure/networking/network-monitoring-overview) é uma solução [de monitoramento de rede híbirada](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) baseado em nuvem que ajuda a monitorar o desempenho de rede entre vários pontos em sua infraestrutura de rede. Também ajuda a monitorar a conectividade de rede para [serviços e pontos de extremidade do aplicativo e a](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) e [monitorar o desempenho do Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

O Monitor de Desempenho de Rede detecta problemas de tráfego de rede, como blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não são capazes de detectar. A solução gera alertas e notifica como e quando um limite é ultrapassado para um link de rede. Ela também garante a detecção oportuna de problemas de desempenho de rede e localiza a origem do problema para determinado segmento de rede ou dispositivo. 

Mais informações sobre os diversos recursos de suporte do [Monitor de Desempenho de Rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.

## <a name="set-up-and-configure-agents"></a>Definir e configurar agentes

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de plataforma para os nós a serem usados para monitorar pelo NPM?
Abaixo estão os requisitos de plataforma das várias funcionalidades do NPM:

- Os recursos do monitor de conectividade do serviço e do monitor de desempenho do NPM dão suporte a sistemas operacionais Windows Server e Windows desktops/cliente. As versões do sistema operacional do Windows Server com suporte são 2008 SP1 ou posterior. As versões de desktop/cliente do Windows com suporte são Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- Funcionalidade do Monitor do ExpressRoute do NPM dá suporte a apenas ao sistema operacional do servidor do Windows (2008 SP1) ou posterior.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Posso usar máquinas Linux como o monitoramento de nós no NPM?
No momento, a capacidade de monitorar redes usando nós baseados em Linux está em versão prévia. Entre em contato para seu gerente de conta para saber mais. Agentes do Linux fornecem a capacidade de monitoramento para o recurso de Monitor de desempenho do NPM e não estão disponíveis para os recursos do Monitor de conectividade do serviço e Monitor do ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quais são os requisitos de plataforma para os nós a serem usados para monitorar pelo NPM?
Para executar a solução NPM nas VMs para monitorar redes do nó, os nós devem ter pelo menos de 500 MB de memória e um núcleo. Você não precisa usar nós separados para executar o NPM. A solução pode ser executada em nós que têm outras cargas de trabalho em execução nele. A solução tem a capacidade de interromper o processo de monitoramento se usar mais de 5% da CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Para usar o NPM, devo conectar Meus nós como o agente direto ou por meio do Operations Manager do Centro do Sistema?
O monitor de desempenho e os recursos do monitor de conectividade de serviço dão suporte a nós [conectados como agentes diretos](../../azure-monitor/platform/agent-windows.md) e [conectados por meio de Operations Manager](../../azure-monitor/platform/om-agents.md).

Para o recurso de Monitor do ExpressRoute, os nós do Azure devem estar conectados como agentes diretos somente. Não há suporte para os nós do Azure, que são conectados por meio do Operations Manager. Para nós locais, os nós conectados como agentes diretos e por meio de Operations Manager têm suporte para monitorar um circuito de ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Qual protocolo entre TCP e ICMP deve ser escolhido para o monitoramento?
Se você estiver monitorando sua rede usando nós baseados no Windows Server, recomendamos o uso de TCP como o protocolo de monitoramento, pois ele fornece maior precisão. 

O ICMP é recomendado para nós baseados no sistema operacional Windows / desktops cliente. Essa plataforma não permite que dados TCP sejam enviados por soquetes brutos, que o NPM usa para descobrir a topologia de rede.

Você pode obter mais detalhes sobre as vantagens relativas de cada protocolo [aqui](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Como configurar um nó para dar suporte ao monitoramento usando o protocolo TCP?
Para o nó para dar suporte ao monitoramento usando o protocolo TCP: 
* Certifique-se de que a plataforma de nó é o Windows Server (2008 SP1 ou posterior).
* Execute o [EnableRules.ps1](https://aka.ms/npmpowershellscript) script do Powershell no nó. Consulte as [instruções](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) para obter mais detalhes.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Como alterar a porta TCP usada pelo NPM para monitoramento?
Você pode alterar a porta TCP usada pelo NPM para monitoramento, executando o script [EnableRules.ps1](https://aka.ms/npmpowershellscript). Você precisa inserir o número de porta que pretende usar como um parâmetro. Por exemplo, para habilitar o TCP na porta 8060, execute `EnableRules.ps1 8060`. Certifique-se de que você use a mesma porta TCP em todos os nós que está sendo usados para monitoramento.

O script configura apenas o firewall do windows localmente. Se você tiver o firewall de rede ou regras de grupo de segurança de rede (NSG), certifique-se de que permitem o tráfego destinado à porta TCP usada pelo NPM.

### <a name="how-many-agents-should-i-use"></a>Quantas agentes devo usar?
Você deve usar pelo menos um agente para cada sub-rede que deseja monitorar.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Qual é o número máximo de agentes que posso usar ou que eu vejo o erro ".... Você atingiu seu limite de configuração "?
O NPM limita o número de IPs a 5000 IPs por workspace. Se um nó tiver ambos os endereços IPv4 e IPv6, isso contará como 2 IPs para esse nó. Portanto, esse limite de 5000 IPs determinaria o limite superior do número de agentes. Você pode excluir os agentes inativos da guia Nós no NPM >> Configurar. O NPM também mantém o histórico de todos os IPs que já foram atribuídos à VM que hospeda o agente e cada um é contado como um IP separado, contribuindo para esse limite superior de 5000 IPs. Para liberar IPs para seu espaço de trabalho, você pode usar a página nós para excluir os IPs que não estão em uso.

## <a name="monitoring"></a>Monitoramento

### <a name="how-are-loss-and-latency-calculated"></a>Como o perda e latência são calculados
Agentes de origem enviam qualquer SYN TCP (se o TCP é escolhido como o protocolo para monitoramento) de solicitações ou solicitações de eco ICMP (se ICMP é escolhido como o protocolo para monitoramento) para o IP de destino em intervalos regulares para garantir que todos os caminhos entre o IP de origem-destino sejam abordados. A porcentagem de pacotes recebidos e o tempo de ida e volta do pacote é medida para calcular a perda e latência de cada caminho. Esses dados são agregados durante o intervalo de sondagem e em todos os caminhos para obter os valores agregados de perda e latência para a combinação de IP para o intervalo de sondagem específico.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Com que frequência o agente de origem envia pacotes para o destino de monitoramento?
Para recursos do Monitor de Desempenho e Monitor do ExpressRoute, a origem envia pacotes a cada 5 segundos e registra as medidas de rede. Esses dados são agregados durante um intervalo de sondagem de 3 minutos para calcular os valores médios e de pico de perda e latência. Para o recurso de Monitor de Conectividade do Serviço, a frequência de envio de pacotes para medição de rede é determinada pela frequência inserida pelo usuário para o teste específico ao configurar o teste.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quantos pacotes são enviados para o monitoramento?
O número de pacotes enviados pelo agente de origem para destino em uma sondagem é adaptável e é decidido por nosso algoritmo proprietário, o que pode ser diferente para topologias de rede diferentes. Mais o número de caminhos de rede entre a combinação de IP de origem / destino, mais será o número de pacotes que são enviados. O sistema garante que todos os caminhos entre a combinação de IP de origem-destino estejam cobertos.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Como o NPM descobre a topologia de rede entre a origem e destino?
O NPM usa um algoritmo proprietário com base no rastreamento de rotas para descobrir todos os caminhos e saltos entre origem e destino.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>O NPM oferece informações sobre o nível comutação e roteamento? 
Embora o NPM possa detectar todas as possíveis rotas entre o agente de origem e destino, não fornece visibilidade na qual rota foi colocada pelos pacotes enviados por suas cargas de trabalho específicas. A solução pode ajudar você a identificar os caminhos e saltos de rede subjacente, que estão adicionando uma latência mais do que você esperava.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Por que alguns dos caminhos não são íntegros?
Caminhos de rede diferentes podem existir entre as IPs de origem e destino e cada caminho pode ter um valor diferente de perda e latência. O NPM marca esses caminhos como não íntegros (indicados pela cor vermelha) para o qual os valores de perda de e/ou latência é maior que o respectivo limite definido na configuração de monitoramento.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>O que um salto na cor vermelha significa no mapa de topologia de rede?
Se for um salto vermelho, significa que ele faz parte de pelo menos um caminho não íntegro. NPM marca apenas os caminhos como não íntegros, não segregar o status de integridade de cada caminho. Para identificar os saltos problemáticos, você pode exibir a latência de salto a salto e separar os adicionando mais do que a latência prevista.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Como funciona a localização de falhas no Monitor de Desempenho?
NPM usa um mecanismo probabilístico para atribuir as probabilidades de falha para cada caminho de rede, o segmento de rede, e os saltos de rede constituintes com base no número de caminhos não íntegros fazem parte. Conforme os segmentos de rede e saltos se tornam parte do maior número de caminhos não íntegros, aumenta a probabilidade de falhas associada a eles. Esse algoritmo funciona melhor quando você tiver vários nós com o agente NPM conectado um ao outro, como isso aumenta os pontos de dados para calcular as probabilidades de falha.

### <a name="how-can-i-create-alerts-in-npm"></a>Como criar alertas no NPM?
Consulte a [seção de alertas na documentação do](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) para obter instruções passo a passo.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Quais são as consultas de Log Analytics padrão para alertas
Consulta do monitor de desempenho

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Consulta do monitor de conectividade de serviço

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
Consultas do monitor do ExpressRoute: consulta de circuitos

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Emparelhamento privado

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Emparelhamento da Microsoft

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Consulta comum   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>O NPM pode monitorar roteadores e servidores como dispositivos individuais?
NPM só identifica o IP e nome do host subjacente de saltos de rede (comutadores, roteadores, servidores, etc.) entre as IPs de origem e destino. Ele também identifica a latência entre esses saltos identificados. Ele não monitora individualmente esses saltos subjacentes.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM pode ser usado para monitorar a conectividade de rede entre o Azure e AWS?
Sim. Consulte o artigo [Monitorar o Azure, AWS e redes locais usando o NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) para obter detalhes.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>É o uso de largura de banda do ExpressRoute de entrada ou saída?
Uso de largura de banda é o total de largura de banda de entrada e saída. Ele é expresso em Bits/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Podemos obter informações de largura de banda de entrada e saída para o ExpressRoute?
Valores de entrada e saídas para a largura de banda primária e secundária podem ser capturados.

Para informações de nível de emparelhamento MS, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Para obter informações de nível de emparelhamento privado, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Para informações de nível de circuito, use a consulta abaixo mencionada na pesquisa de logs

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Quais regiões têm suporte para o Monitor de desempenho do NPM?
O NPM pode monitorar a conectividade entre redes em qualquer parte do mundo, de um workspace que está hospedado em uma das [regiões com suporte](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Quais regiões têm suporte para o Monitor de Conectividade do Serviço do NPM?
NPM pode monitorar a conectividade para serviços em qualquer parte do mundo, de workspace que está hospedado em uma das [regiões com suporte](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Quais regiões têm suporte para o Monitor do ExpressRoute do NPM?
O NPM pode monitorar seus circuitos do ExpressRoute localizados em qualquer região do Azure. Para carregar no NPM, você precisará de um espaço de trabalho do Log Analytics que deve ser hospedado em uma das [regiões com suporte](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Por que alguns dos saltos são marcos como não identificados na exibição da topologia de rede?
O NPM usa uma versão modificada do rastreamento de rotas para descobrir a topologia do agente de origem para o destino. Um salto não identificado representa que o salto de rede não respondeu à solicitação de rastreamento de rotas do agente de origem. Se três saltos de rede consecutivos não responderem aos traceroute do agente, a solução marcará os saltos sem resposta como não sendo identificados e não tentará descobrir mais saltos.

Um salto não pode responder a um rastreamento de rotas em um ou mais dos cenários abaixo:

* Os roteadores foram configurados para não revelar sua identidade.
* Os dispositivos de rede não estão permitindo o tráfego ICMP_TTL_EXCEEDED.
* Um firewall está bloqueando a resposta ICMP_TTL_EXCEEDED do dispositivo de rede.

Quando qualquer um dos pontos de extremidade está no Azure, os traceroute exibem saltos não identificados, pois o Azure ndrastructure não revela identidades para os traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Obtenho alertas para testes não íntegros, mas não vejo os valores altos no grafo de perda e latência do NPM. Como fazer verificar o que não está íntegro?
O NPM emitirá um alerta se a latência de ponta a ponta entre a origem e o destino cruzar o limite de qualquer caminho entre eles. Algumas redes têm vários caminhos conectando a mesma origem e destino. NPM gera um alerta é que qualquer caminho não está íntegro. A perda e a latência vistas nos grafos são o valor médio de todos os caminhos, portanto, ele pode não mostrar o valor exato de um único caminho. Para entender onde o limite foi violado, procure a coluna "subtipo" no alerta. Se o problema for causado por um caminho, o valor do subtipo será NetworkPath (para testes do monitor de desempenho), EndpointPath (para testes do monitor de conectividade de serviço) e ExpressRoutePath (para testes do monitor ExpressRotue). 

A consulta de exemplo para localizar o caminho não está íntegra:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Por que meu teste mostra não íntegro, mas a topologia não 
O NPM monitora a perda, a latência e a topologia de ponta a ponta em intervalos diferentes. A perda e a latência são medidas uma vez a cada 5 segundos e agregadas a cada três minutos (para o monitor de desempenho e monitor de rota expressa) enquanto a topologia é calculada usando traceroute uma vez a cada 10 minutos. Por exemplo, entre 3:44 e 4:04, a topologia pode ser atualizada três vezes (3:44, 3:54, 4:04), mas a perda e a latência são atualizadas cerca de sete vezes (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A topologia gerada em 3:54 será renderizada para a perda e a latência calculadas às 3:56, 3:59 e 4:02. Suponha que você receba um alerta de que o circuito ER não estava íntegro em 3:59. Faça logon no NPM e tente definir o tempo de topologia como 3:59. NPM renderizará a topologia gerada em 3:54. Para entender a última topologia conhecida de sua rede, compare os campos timeprocessod (tempo em que a perda e a latência foram calculadas) e TracerouteCompletedTime (hora em que a topologia foi calculada). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Qual é a diferença entre os campos E2EMedianLatency e AvgHopLatencyList na tabela NetworkMonitoring
E2EMedianLatency é a latência atualizada a cada três minutos após a agregação dos resultados de testes de ping TCP, enquanto o AvgHopLatencyList é atualizado a cada 10 minutos com base em traceroute. Para entender a hora exata em que o E2EMedianLatency foi calculado, use o campo tempo processado. Para entender a hora exata em que os traceroutes foram concluídos e atualizados AvgHopLatencyList, use o campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Por que os números de latência de salto por salto são diferentes de HopLatencyValues 
HopLatencyValues são de origem para ponto de extremidade.
Por exemplo: Hops-A, B, C. AvgHopLatency-10, 15, 20. Isso significa origem para uma latência = 10, latência de origem para B = 15 e a latência de origem para C é 20. A interface do usuário calculará A latência de salto-B como 5 na topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A solução mostra 100% de perda, mas há conectividade entre a origem e o destino
Isso pode acontecer se o firewall do host ou o firewall intermediário (firewall de rede ou NSG do Azure) está bloqueando a comunicação entre o agente de origem e destino pela porta que está sendo usada para monitoramento pelo NPM (por padrão a porta é 8084, a menos que o cliente tenha mudado isso).

* Para verificar se o firewall do host não está bloqueando a comunicação na porta necessária, exiba o status de integridade de nós de origem e de destino do seguinte modo de exibição: Monitor de Desempenho de Rede -> Configuração -> Nós. 
  Se eles não estiverem íntegros, exiba as instruções e tome uma ação corretiva. Se os nós estiverem íntegros, vá para a etapa b. abaixo.
* Para verificar se um firewall de rede intermediário ou o NSG do Azure não está bloqueando a comunicação na porta necessária, use o utilitário de PsPing de terceiros usando as instruções abaixo:
  * o utilitário psping está disponível para download [aqui](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Execute o seguinte comando do nó de origem.
    * psping -n 15 \<endereço IP do nó de destino\>: portNumber por padrão o NPM usa a porta 8084. Caso você tenha mudado explicitamente usando o script EnableRules.ps1, insira o número de porta personalizada, que você está usando). Esse é um ping de máquina do Azure para local
* Verifique se os pings forem bem-sucedidos. Caso contrário, em seguida, ele indica se um firewall de rede intermediários ou o NSG do Azure está bloqueando o tráfego nesta porta.
* Agora, execute o comando a partir do nó de destino para o IP do nó de origem.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Não há perda do nó A para B, mas não do nó B para A. Por quê?
Como os caminhos de rede entre A para B podem ser diferentes do que os caminhos de rede entre B para A, valores diferentes para a perda e latência podem ser observados.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Por que todos os meus circuitos do ExpressRoute e conexões de emparelhamento não estão sendo descobertos?
O Monitor de Desempenho de Rede agora detecta circuitos do ExpressRoute e conexões de emparelhamento em todas as assinaturas às quais o usuário tem acesso. Escolha todas as assinaturas em que seus recursos do ExpressRoute estão vinculados e habilite o monitoramento para cada recurso descoberto. O Monitor de Desempenho de Rede procura objetos de conexão ao descobrir um emparelhamento privado; portanto, verifique se uma rede virtual está associada ao emparelhamento.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>O recurso de Monitor de ER apresentará uma mensagem de diagnóstico "O tráfego não está passando por meio de qualquer circuito". O que isso significa?

Pode haver um cenário onde há uma conexão saudável entre os locais e nós do Azure, mas o tráfego não vão no circuito de ExpressRoute configurado para ser monitorado pelo NPM. 

Isso pode ocorrer se:

* O circuito estiver inoperante.
* Os filtros de rota são configurados de modo que eles dão prioridade a outras rotas (como uma conexão VPN ou outro circuito do ExpressRoute) no circuito do ExpressRoute pretendido. 
* Os nós no local e Azure escolhidos para monitoramento do circuito ExpressRoute na configuração de monitoramento, não têm conectividade um com o outro sobre o circuito do ExpressRoute pretendido. Certifique-se de que você escolheu os nós que tenham conectividade entre si no circuito do ExpressRoute que você pretende monitorar.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Ao configurar o monitoramento do meu circuito do ExpressRoute, os nós do Azure não estão sendo detectados.
Isso pode acontecer se os nós do Azure são conectados por meio do Operations Manager. O recurso de Monitor do ExpressRoute dá suporte a apenas os nós do Azure que estão conectados como Agentes Diretos.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Não é possível descobrir por circuitos do ExpressRoute no portal do OMS
Embora o NPM possa ser usado tanto do portal do Azure, bem como o portal do OMS, a descoberta de circuito da funcionalidade do Monitor do ExpressRoute funciona apenas no portal do Azure. Depois que os circuitos forem descobertos por meio do portal do Azure, você pode usar o recurso em qualquer um dos dois portais. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Na funcionalidade do Monitor de Conectividade do Serviço, o tempo de resposta do serviço, perda de rede, bem como latência é mostrada como NA
Isso pode ocorrer se uma um mais opções forem verdadeiras:

* O serviço está inativo.
* O nó usado para verificar a conectividade de rede para o serviço está inoperante.
* O destino inserido na configuração de teste está incorreto.
* O nó não tem nenhuma conectividade de rede.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>O recurso de Monitor de Conectividade do Serviço, um tempo de resposta do serviço válido é exibido mas a perda da rede, bem como a latência, é mostrada como NA
 Isso pode ocorrer se uma um mais opções forem verdadeiras:

* Se o nó usado para verificar a conectividade de rede para o serviço for máquina cliente Windows, o serviço de destino está bloqueando as solicitações ICMP ou um firewall de rede está bloqueando as solicitações ICMP provenientes do nó.
* A caixa de seleção Executar medidas de red está em branco na configuração de teste.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Na funcionalidade do Monitor de Conectividade do Serviço, o tempo de resposta do serviço for NA, mas a perda da rede, bem como a latência é válida
Isso pode acontecer se o serviço de destino não for um aplicativo web, mas o teste está configurado como um teste da Web. Edite a configuração de teste e escolha o tipo de teste como Rede em vez de Web.

## <a name="miscellaneous"></a>Disposições gerais

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Há um impacto de desempenho no nó que está sendo usado para monitoramento?
O processo do NPM está configurado para ser interrompido se ele utiliza mais de 5% dos recursos de CPU do host. Isso é para garantir que você pode continuar usando os nós para suas cargas de trabalho normais sem afetar o desempenho.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>O NPM edita regras de firewall para o monitoramento?
O NPM só cria uma regra de Firewall do Windows local em nós em que o script do Powershell EnableRules.ps1 é executado para permitir que os agentes criem conexões TCP entre si na porta especificada. A solução não modifica qualquer firewall de rede ou regras de Grupo de Segurança de Rede (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Como verificar a integridade de nós que está sendo usada para monitoramento?
Você pode exibir o status de integridade de nós que estão sendo usados para monitoramento do seguinte modo de exibição: Monitor de Desempenho de Rede -> Configuração -> Nós. Se um nó não estiver íntegro, você pode exibir os detalhes do erro e executar a ação sugerida.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>O NPM pode relatar os números de latência em microssegundos?
O NPM arredonda os números de latência na interface do usuário e, em milissegundos. Os mesmos dados são armazenados em uma granularidade maior (às vezes, até quatro decimais).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o Monitor de desempenho de rede consultando [Solução de Monitor de Desempenho de Rede no Azure](../../azure-monitor/insights/network-performance-monitor.md).
