---
title: Retenção de dados e armazenamento no Azure Application Insights | Microsoft Docs
description: Declaração de política de privacidade e retenção
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/29/2019
ms.openlocfilehash: ba8a76cd4d3804bcb062ae0554e3fe7002804ed2
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031673"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Coleta de dados, retenção e armazenamento no Application Insights

Quando você instala o SDK do [insights aplicativo Azure][start] em seu aplicativo, ele envia a telemetria sobre seu aplicativo para a nuvem. Naturalmente, os desenvolvedores responsáveis querem saber exatamente quais dados são enviados, o que acontece com os dados e como eles podem manter o controle sobre esses dados. Em particular, poderiam ser enviados dados confidenciais, onde são armazenados e qual é o nível de segurança? 

Primeiro, a resposta curta:

* Os módulos de telemetria padrão que executam "prontos de fábrica" têm pouca probabilidade de enviar dados confidenciais para o serviço. A telemetria está relacionada a carga, a métricas de desempenho e uso, a relatórios de exceção e a outros dados de diagnóstico. Os principais dados de usuário visíveis nos relatórios de diagnóstico são as URLs; mas seu aplicativo, em qualquer caso, não deve colocar dados confidenciais em texto sem formatação em uma URL.
* Você pode escrever código que envie a telemetria personalizada adicional para ajudá-lo com o uso de monitoramento e diagnóstico. (Essa extensibilidade é um grande recurso do Application Insights.) Seria possível, por engano, escrever esse código para que ele inclua dados confidenciais e pessoais. Se seu aplicativo funciona com esses dados, você deve aplicar um processo de revisão completo a todo o código que você escreve.
* Ao desenvolver e testar seu aplicativo, é fácil inspecionar o que está sendo enviado pelo SDK. Os dados aparecem nas janelas de saída de depuração do IDE e do navegador. 
* Os dados são mantidos em servidores do [Microsoft Azure](https://azure.com) nos EUA ou na Europa. (Mas seu aplicativo pode ser executado em qualquer lugar). O Azure tem [processos fortes de segurança e atende a uma ampla gama de padrões de conformidade](https://azure.microsoft.com/support/trust-center/). Somente você e a sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a eles apenas em circunstâncias limitadas específicas e com o seu conhecimento. Ele é criptografado em trânsito e em repouso.
*   Examine os dados coletados, pois isso pode incluir dados permitidos em algumas circunstâncias, mas não em outros.  Um bom exemplo disso é o nome do dispositivo. O nome do dispositivo de um servidor não tem impacto de privacidade e é útil, mas um nome de dispositivo de um telefone ou laptop pode ter um impacto de privacidade e ser menos útil. Um SDK desenvolvido principalmente para servidores de destino, coletaria o nome do dispositivo por padrão, e isso pode precisar ser substituído em eventos normais e exceções.

O restante deste artigo aborda mais detalhadamente essas respostas. Ele foi projetado para ser independente, para que possa mostrá-lo aos colegas que não fazem parte de sua equipe.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
O [aplicativo Azure insights][start] é um serviço fornecido pela Microsoft que ajuda a melhorar o desempenho e a usabilidade do seu aplicativo em tempo real. Ele monitora seus aplicativos em todo o tempo de execução, tanto durante o teste quanto depois de publicado ou implantado. O Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia você tem mais usuários, o nível de capacidade de resposta do aplicativo e quão bem ele é atendido por quaisquer serviços externos dos quais depende. Se houver travamentos, falhas ou problemas de desempenho, você pode pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço lhe enviará emails se houver alterações na disponibilidade e no desempenho do seu aplicativo.

Para obter essa funcionalidade, você instala um SDK do Application Insights em seu aplicativo, que passa a fazer parte do código. Quando o aplicativo é executado, o SDK monitora a operação e envia a telemetria para o serviço do Application Insights. Este é um serviço de nuvem hospedado pelo [Microsoft Azure](https://azure.com). (Mas Application Insights funciona para qualquer aplicativo, não apenas aplicativos hospedados no Azure.)

O serviço Application Insights armazena e analisa a telemetria. Para ver a análise ou a pesquisa por meio de telemetria armazenada, você entra na sua conta do Azure e abre o recurso Application Insights para seu aplicativo. Você também pode compartilhar o acesso aos dados com outros membros da equipe ou com assinantes do Azure especificados.

Você pode ter dados exportados do serviço Application Insights, por exemplo, para um banco de dados ou para ferramentas externas. Você fornece a cada ferramenta uma chave especial obtida do serviço. A chave poderá ser revogada, se necessário. 

Application Insights SDKs estão disponíveis para uma variedade de tipos de aplicativos: serviços Web hospedados em seus próprios servidores Java EE ou ASP.NET ou no Azure; clientes Web – ou seja, o código em execução em uma página da Web; aplicativos da área de trabalho e serviços; aplicativos de dispositivo, como Windows Phone, iOS e Android. Todos enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Quais dados são coletados?
Há três fontes de dados:

* O SDK, que se integra com o seu aplicativo [no desenvolvimento](../../azure-monitor/app/asp-net.md) ou no [tempo de execução](../../azure-monitor/app/monitor-performance-live-website-now.md). Há diferentes SDKs para diferentes tipos de aplicativos. Há também um [SDK para páginas da Web](../../azure-monitor/app/javascript.md), que é carregado no navegador do usuário final junto com a página.
  
  * Cada SDK tem vários [módulos](../../azure-monitor/app/configuration-with-applicationinsights-config.md), que usam diferentes técnicas para coletar diferentes tipos de telemetria.
  * Se você instalar o SDK no desenvolvimento, poderá usar sua API para enviar sua própria telemetria, além dos módulos padrão. Essa telemetria personalizada pode incluir quaisquer dados que você deseje enviar.
* Em alguns servidores web, também existem agentes que são executados juntamente com o aplicativo e que enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, as VMs do Azure, hosts de Docker e [servidores Java EE](../../azure-monitor/app/java-agent.md) podem ter esses agentes.
* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) são processos executados pela Microsoft que enviam solicitações para o aplicativo web a intervalos regulares. Os resultados são enviados para o serviço Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quais tipos de dados são coletados?
As principais categorias são:

* [Telemetria do servidor Web](../../azure-monitor/app/asp-net.md) - solicitações HTTP.  URI, tempo necessário para processar a solicitação, código de resposta, endereço IP do cliente. `Session id`.
* [Páginas da Web](../../azure-monitor/app/javascript.md) - contagens de página, usuário e sessão. Tempos de carregamento de página. Exceções. Chamadas Ajax.
* Contadores de desempenho - memória, CPU, E/S, ocupação de rede.
* Contexto de cliente e servidor - sistema operacional, localidade, tipo de dispositivo, navegador, resolução da tela.
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md) e falhas – **despejos de pilha**, `build id`, tipo de CPU. 
* [Dependências](../../azure-monitor/app/asp-net-dependencies.md) - chamadas a serviços externos, como REST, SQL, AJAX. Cadeia de conexão ou URI, duração, sucesso, comando.
* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) - duração do teste e etapas, respostas.
* [Logs de rastreamento](../../azure-monitor/app/asp-net-trace-logs.md) e [telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md) - **qualquer elemento que você codifique nos seus logs ou telemetria**.

[Mais detalhes](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Como verificar o que está sendo coletado?
Se você estiver desenvolvendo o aplicativo usando o Visual Studio, execute o aplicativo no modo de depuração (F5). A telemetria é exibida na janela Saída. A partir dali, é possível copiá-la e formatá-la como JSON para fácil inspeção. 

![](./media/data-retention-privacy/06-vs.png)

Também há uma exibição mais legível na janela Diagnóstico.

Para páginas da Web, abra a janela de depuração do navegador.

![Pressione F12 e abra a guia Rede.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes de ela ser enviada?
Isso seria possível escrevendo um [plug-in de processador de telemetria](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Por quanto tempo os dados são mantidos?
Pontos de dados brutos (ou seja, itens que você pode consultar na análise e inspecionar na pesquisa) são mantidos por até 730 dias. Você pode [selecionar uma duração de retenção](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. Se você precisar manter os dados com mais de 730 dias, poderá usar a [exportação contínua](../../azure-monitor/app/export-telemetry.md) para copiá-los para uma conta de armazenamento durante a ingestão de dados. 

Os dados mantidos por mais de 90 dias incorrerão em encargos adicionais. Saiba mais sobre preços de Application Insights na [página de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Gerenciador de Métricas) são mantidos em um detalhamento de um minuto por 90 dias.

Os [instantâneos de depuração](../../azure-monitor/app/snapshot-debugger.md) são armazenados por 15 dias. Essa política de retenção é definida por aplicativo. Se for necessário aumentar esse valor, você poderá solicitar o aumento abrindo um caso de suporte no portal do Azure.

## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
Os dados são visíveis para você e, se você tiver uma conta de organização, para os membros de sua equipe. 

Eles podem ser exportados por você e pelos membros da equipe e podem ser copiados para outros locais e passados para outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que a Microsoft faz com as informações que meu aplicativo envia ao Application Insights?
A Microsoft usa os dados apenas para fornecer o serviço a você.

## <a name="where-is-the-data-held"></a>Onde os dados são mantidos?
* Quando você cria um novo recurso do Application Insights, você pode selecionar o local. Saiba mais sobre a disponibilidade de Application Insights por região [aqui](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Isso significa que meu aplicativo deve ser hospedado nos EUA, na Europa ou no sudeste asiático?
* Não. Seu aplicativo pode ser executado em qualquer lugar, em seus próprios hosts locais ou na nuvem.

## <a name="how-secure-is-my-data"></a>Quão seguros meus dados estão?
O Application Insights é um serviço do Azure. Políticas de segurança são descritas no [white paper de segurança, privacidade e conformidade do Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados em servidores do Microsoft Azure. Para contas no portal do Azure, as restrições de conta são descritas no [documento segurança, privacidade e conformidade do Azure](https://go.microsoft.com/fwlink/?linkid=392408).

É restrito o acesso aos seus dados por funcionários da Microsoft. Podemos acessar seus dados com a sua permissão e se isso for necessário para o seu uso do Application Insights. 

Dados agregados em aplicativos de todos os nossos clientes (como taxas de dados e tamanho médio dos rastreamentos) são usados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>A telemetria de outra pessoa poderia interferir meus dados do Application Insights?
Eles poderiam enviar telemetria adicional para sua conta usando a chave de instrumentação, que pode ser encontrada no código de suas páginas da Web. Com dados suficientes adicionais, suas métricas não representariam corretamente o uso e o desempenho do seu aplicativo.

Se você compartilhar código com outros projetos, lembre-se de remover sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são criptografados?
Todos os dados são criptografados em repouso e à medida que se movem entre data centers.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são criptografados em trânsito do meu aplicativo para servidores do Application Insights?
Sim, usamos HTTPS para enviar dados ao portal de praticamente todos os SDKs, incluindo servidores Web, dispositivos e páginas da Web HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>O SDK cria armazenamento local temporário?

Sim, determinados Canais de Telemetria persistirão os dados localmente se um ponto de extremidade não puder ser alcançado. Examine abaixo para ver quais estruturas e canais de telemetria são afetados.

Os canais de telemetria que utilizam o armazenamento local criam arquivos temporários nos diretórios TEMP ou APPDATA, que são restritos à conta específica que executa o aplicativo. Isso poderá acontecer quando um ponto de extremidade estiver temporariamente indisponível ou se o limite da limitação for atingido. Assim que esse problema for resolvido, o canal de telemetria continuará enviando todos os dados novos e persistentes.

Os dados persistentes não são criptografados localmente. Se essa for uma preocupação, revise os dados e restrinja a coleta de dados privados. (Para obter mais informações, consulte [como exportar e excluir dados privados](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Se um cliente precisar configurar esse diretório com requisitos de segurança específicos, ele poderá ser configurado por estrutura. Certifique-se de que o processo executando no aplicativo tenha acesso para gravação ao diretório, mas também certifique-se de que esse diretório está protegido para evitar que a telemetria seja lida por usuários indesejados.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` é usado para persistir dados. Esse local não é configurável no diretório config e as permissões para acessar essa pasta são restritas ao usuário específico com as credenciais necessárias. (Para obter mais informações, consulte [implementação](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Por padrão, `ServerTelemetryChannel` usa a pasta de dados do aplicativo local do usuário atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%`. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.)


Por meio de arquivo de configuração:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Por meio de código:

- Remover ServerTelemetryChannel do arquivo de configuração
- Adicione este snippet à configuração:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Por padrão, `ServerTelemetryChannel` usa a pasta de dados do aplicativo local do usuário atual `%localAppData%\Microsoft\ApplicationInsights` ou a pasta temporária `%TMP%`. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) aqui.) Em um ambiente Linux, o armazenamento local será desabilitado, a menos que uma pasta de armazenamento seja especificada.

O snippet de código a seguir mostra como definir `ServerTelemetryChannel.StorageFolder` no método `ConfigureServices()` da classe `Startup.cs`:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Para obter mais informações, consulte [configuração personalizada do AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Por padrão, `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` é usado para persistir dados. As permissões para acessar essa pasta são restritas ao usuário atual e aos Administradores. (Consulte a [implementação](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) aqui.)

O prefixo de pasta `appInsights-node` pode ser substituído, alterando o valor de runtime da variável estática `Sender.TEMPDIR_PREFIX` localizada em [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="opencensus-python"></a>OpenCensus Python

Por padrão, o SDK do Python OpenCensus usa a pasta do usuário atual `%username%/.opencensus/.azure/`. As permissões para acessar essa pasta são restritas ao usuário atual e aos Administradores. (Consulte a [implementação](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) aqui.) A pasta com os dados persistentes será nomeada após o arquivo Python que gerou a telemetria.

Você pode alterar o local do arquivo de armazenamento passando o parâmetro `storage_path` no construtor do exportador que você está usando.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Como enviar dados ao Application Insights usando o TLS 1.2?

Para garantir a segurança dos dados em trânsito para os pontos de extremidade do Application Insights, incentivamos os clientes para configurar seu aplicativo para usar pelo menos segurança de camada de transporte (TLS) 1.2. Versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto ele ainda estiver atualmente trabalham para permitir a compatibilidade com versões anteriores, eles são **não recomendáveis**, e o setor está se movendo rapidamente para abandonar o suporte para esses protocolos mais antigos. 

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) definiu um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desabilitar versões mais antigas de TLS/SSL e atualizar para protocolos mais seguros. Depois que o Azure tiver descartado o suporte antigo, se seus clientes/aplicativos não conseguirem se comunicar mais com pelo menos TLS 1.2 você não conseguirá enviar dados para o Application Insights. A abordagem de testar e validar o suporte do TLS do seu aplicativo irá variar dependendo da sistema operacional/plataforma, bem como a linguagem/estrutura que seu aplicativo usa.

Não recomendamos definir explicitamente seu aplicativo para usar apenas o TLS 1,2, a menos que seja necessário, pois isso pode interromper os recursos de segurança no nível da plataforma que permitem detectar e aproveitar automaticamente os protocolos mais seguros à medida que eles se tornam disponíveis, como TLS 1,3. É recomendável realizar uma auditoria completa de código do seu aplicativo para verificar se há codificar de versões específicas do TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Diretrizes específicas da plataforma/linguagem

|Plataforma/linguagem | Suporte | Mais informações |
| --- | --- | --- |
| Serviços de Aplicativo do Azure  | Configuração com suporte pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o comunicado para [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Aplicativos de Funções do Azure | Configuração com suporte pode ser necessária. | O suporte foi anunciado em abril de 2018. Leia o comunicado para [detalhes de configuração](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Configuração com suporte varia de acordo com a versão. | Para obter informações de configuração detalhadas para o .NET 4,7 e versões anteriores, consulte [estas instruções](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor de status | Configuração com suporte exigida | Monitor de status depende [configuração do sistema operacional](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [configuração do .NET](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) para dar suporte ao TLS 1.2.
|Node.js |  Configuração com suporte no v10.5.0 pode ser necessária. | Use a [documentação oficial do TLS/SSL do node. js](https://nodejs.org/api/tls.html) para qualquer configuração específica do aplicativo. |
|Java | Com suporte, foi adicionado suporte do JDK para o TLS 1.2 no [atualização do JDK 6 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) e [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 usa [TLS 1.2 por padrão](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Distribuições do Linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte a TLS 1.2.  | Verifique as [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL é suportada.|
| Windows 8.0 - 10 | Suporte e habilitado por padrão. | Para confirmar que você ainda está usando o [as configurações padrão](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suporte e habilitado por padrão. | Para confirmar que você ainda está usando o [as configurações padrão](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows Server 7 SP1 e Windows Server 2008 R2 SP1 | Com suporte, mas não habilitado por padrão. | Consulte a página [configurações do registro de segurança de camada de transporte (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) para obter detalhes sobre como habilitar.  |
| Windows Server 2008 SP2 | Suporte para TLS 1.2 requer uma atualização. | Ver [atualização para adicionar suporte para TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) no Windows Server 2008 SP2. |
|Windows Vista | Sem suporte. | {1&gt;N/A&lt;1}

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Verifique qual versão do OpenSSL sua distribuição do Linux está em execução

Para verificar qual versão do OpenSSL que você tiver instalado, abra o terminal e execute:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Executar um teste de transação do TLS 1.2 no Linux

Para executar um teste preliminar para ver se o seu sistema Linux pode se comunicar por meio do TLS 1,2., abra o terminal e execute:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dados pessoais armazenados no Application Insights

Nossos [artigo de dados pessoais do Application Insights](../../azure-monitor/platform/personal-data-mgmt.md) aborda esse problema detalhado.

#### <a name="can-my-users-turn-off-application-insights"></a>Meus usuários podem desativar o Application Insights?
Não diretamente. Não fornecemos um comutador que os usuários possam operar para desativar o Application Insights.

No entanto, você pode implementar tal recurso em seu aplicativo. Todos os SDKs incluem uma configuração de API que desativa a coleta da telemetria. 

## <a name="data-sent-by-application-insights"></a>Dados enviados pelo Application Insights
Os SDKs variam entre diferentes plataformas, e há vários componentes que você pode instalar. (Consulte [Application insights-visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviados em cenários diferentes

| Sua ação | Classes de dados coletados (consulte a tabela a seguir) |
| --- | --- |
| [Adicionar Application Insights SDK a um projeto Web .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Solicitações<br/>**Exceções**<br/>Session<br/>usuários |
| [Instalar o Status Monitor no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar Application Insights SDK a um aplicativo Web Java][java] |ServerContext<br/>Inferido<br/>Solicitação<br/>Session<br/>usuários |
| [Adicionar SDK do JavaScript à página da Web][client] |ClientContext <br/>Inferido<br/>{1&gt;Página&lt;1}<br/>ClientPerf<br/>Ajax |
| [Definir propriedades padrão][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamar TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Controle de chamada *][api] |Nome do evento<br/>**Propriedades** |
| [Chamar Trackexception][api] |**Exceções**<br/>Despejo da pilha<br/>**Propriedades** |
| O SDK não é capaz de coletar dados. Por exemplo: <br/> - não é possível acessar os contadores de desempenho<br/> - exceção no inicializador de telemetria |Diagnóstico do SDK |

Para [SDKs para outras plataformas][platforms], consulte seus documentos.

#### <a name="the-classes-of-collected-data"></a>As classes dos dados coletados

| Classe de dados coletados | Inclui (não é uma lista completa) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| DeviceContext |`Id`, IP, localidade, modelo de dispositivo, rede, tipo de rede, nome do OEM, resolução da tela, instância de função, nome da função, tipo de dispositivo |
| ClientContext |Sistema operacional, localidade, linguagem, rede, resolução da janela |
| Session |`session id` |
| ServerContext |Nome do computador, localidade, sistema operacional, dispositivo, sessão de usuário, contexto de usuário, operação |
| Inferido |localização geográfica do endereço IP, carimbo de data/hora, sistema operacional, navegador |
| Métricas |Valor e nome da métrica |
| Eventos |Valor e nome do evento |
| PageViews |URL e nome da página ou o nome de tela |
| Desempenho do cliente |URL/nome de página, tempo de carregamento do navegador |
| Ajax |Chamadas HTTP da página da Web para o servidor |
| Solicitações |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP,...), Cadeia de conexão, URI, sincronização/Async, duração, êxito, instrução SQL (com Status Monitor) |
| **Exceções** |Tipo, **mensagem**, pilhas de chamadas, arquivo de origem, número de linha `thread id` |
| Falhas |`Process id`, `parent process id`, `crash thread id`; patch do aplicativo, `id`, Build;  tipo de exceção, endereço, motivo; símbolos e registros ofuscados, endereços de início e término binários, nome e caminho binários, tipo de CPU |
| Rastreamento |**Mensagem** e nível de severidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de solicitação, taxa de exceções, bytes particulares do processo, taxa de E/S, duração da solicitação, comprimento da fila de solicitações |
| Availability |Código de resposta de teste da Web, duração de cada etapa de teste, nome do teste, carimbo de data/hora, sucesso, tempo de resposta, local de teste |
| Diagnóstico do SDK |Mensagem de rastreamento ou exceção |

Você pode [desativar alguns dos dados editando ApplicationInsights. config][config]

> [!NOTE]
> O IP do cliente é usado para inferir a localização geográfica mas, por padrão, os dados do IP não são mais armazenados e todos os zeros são gravados no campo associado. Para compreender mais sobre tratamento de dados pessoais, recomendamos este [artigo](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Se você precisar armazenar dados de endereço IP, nosso [artigo de coleção de endereços IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) o guiará pelas suas opções.

## <a name="credits"></a>Credits
Este produto inclui dados GeoLite2 criados pelo MaxMind, disponíveis em [https://www.maxmind.com](https://www.maxmind.com).



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
