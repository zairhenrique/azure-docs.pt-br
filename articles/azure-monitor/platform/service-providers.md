---
title: Logs de Azure Monitor para provedores de serviço | Microsoft Docs
description: Os logs de Azure Monitor podem ajudar provedores de serviços gerenciados (MSPs), grandes empresas, ISVs (fornecedores independentes de software) e provedores de serviços de hospedagem a gerenciar e monitorar servidores na infraestrutura local ou na nuvem do cliente.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 1b5c181569ea569ecc4808284683501cb20f7bf5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117065"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Logs de Azure Monitor para provedores de serviço

Os espaços de trabalho Log Analytics no Azure Monitor podem ajudar provedores de serviços gerenciados (MSPs), grandes empresas, ISVs (fornecedores independentes de software) e provedores de serviços de hospedagem a gerenciar e monitorar servidores na infraestrutura local ou de nuvem do cliente.

Grandes empresas compartilham várias semelhanças com provedores de serviços, especialmente quando há uma equipe de TI centralizada que é responsável por gerenciar a TI para muitas unidades de negócios diferentes. Para simplificar, este documento usa o termo *provedor*, mas a mesma funcionalidade também está disponível para empresas e outros clientes.

Para parceiros e provedores de serviços que fazem parte do programa [CSP (provedor de soluções na nuvem)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analytics em Azure monitor é um dos serviços do Azure disponíveis nas assinaturas do Azure CSP.

Log Analytics no Azure Monitor também pode ser usado por um provedor de serviços que gerencia recursos do cliente por meio do recurso de gerenciamento de recursos delegado do Azure no [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Arquiteturas para provedores de serviços

Os espaços de trabalho do Log Analytics fornecem um método para o administrador controlar o fluxo e o isolamento dos dados de [log](data-platform-logs.md) e criar uma arquitetura que atenda às suas necessidades de negócios específicas. [Este artigo](design-logs-deployment.md) explica as considerações de design, implantação e migração para um espaço de trabalho e o artigo [gerenciar acesso](manage-access.md) discute como aplicar e gerenciar permissões para dados de log. Os provedores de serviços têm considerações adicionais.

Há três arquiteturas possíveis para os provedores de serviços em relação aos workspaces do Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. os logs distribuídos são armazenados em espaços de trabalho localizados no locatário do cliente

Nesta arquitetura, um workspace é implantado no locatário do cliente que é usado para todos os logs desse cliente.

Há duas maneiras pelas quais os administradores do provedor de serviços podem obter acesso a um espaço de trabalho Log Analytics em um locatário do cliente:

- Um cliente pode adicionar usuários individuais do provedor de serviços como [Azure Active Directory usuários convidados (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Os administradores do provedor de serviços precisarão entrar no diretório de cada cliente na portal do Azure para poder acessar esses espaços de trabalho. Isso também exige que os clientes gerenciem o acesso individual para cada administrador do provedor de serviços.
- Para obter maior escalabilidade e flexibilidade, os provedores de serviços podem usar o recurso de [Gerenciamento de recursos delegado do Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) do [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) para acessar o locatário do cliente. Com esse método, os administradores do provedor de serviços são incluídos em um grupo de usuários do Azure AD no locatário do provedor de serviços, e esse grupo recebe acesso durante o processo de integração para cada cliente. Esses administradores podem então acessar os espaços de trabalho de cada cliente de dentro de seu próprio locatário do provedor de serviços, em vez de ter que fazer logon no locatário de cada cliente individualmente. Acessar os recursos de espaços de trabalho de Log Analytics de seus clientes dessa maneira reduz o trabalho necessário no lado do cliente e pode facilitar a coleta e análise de dados em vários clientes gerenciados pelo mesmo provedor de serviços por meio de ferramentas como [Azure monitor pastas de trabalho](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Para obter mais informações, consulte [monitorar recursos do cliente em escala](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

As vantagens da arquitetura distribuída são:

* O cliente pode confirmar níveis específicos de permissões por meio [do gerenciamento de recursos delegado do Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)ou pode gerenciar o acesso aos logs usando seu próprio [acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Os logs podem ser coletados de todos os tipos de recursos, não apenas de dados de VM baseados em agente. Por exemplo, os Logs de Auditoria do Azure.
* Cada cliente pode ter configurações diferentes para seu workspace, como retenção e limitação de dados.
* Isolamento entre os clientes para fins de regulamentação e conformidade.
* O encargo de cada workspace será distribuído para a assinatura do cliente.

As desvantagens da arquitetura distribuída são:

* Visualizar centralmente e analisar dados em locatários do cliente com ferramentas como Azure Monitor pastas de trabalho podem resultar em experiências mais lentas, especialmente ao analisar dados em mais de 50 espaços de trabalho.
* Se os clientes não estiverem integrados ao gerenciamento de recursos delegado do Azure, os administradores do provedor de serviços deverão ser provisionados no diretório do cliente e será mais difícil para o provedor de serviços gerenciar um grande número de locatários do cliente ao mesmo tempo.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. central-os logs são armazenados em um espaço de trabalho localizado no locatário do provedor de serviço

Nessa arquitetura, os logs não são armazenados nos locatários do cliente, mas apenas em um local central em uma das assinaturas do provedor de serviços. Os agentes que estão instalados nas VMs do cliente são configurados para enviar seus logs para esse workspace usando a ID de workspace e a chave secreta.

As vantagens da arquitetura centralizada são:

* É fácil gerenciar um grande número de clientes e integrá-los aos vários sistemas de back-end.
* O provedor de serviços tem propriedade total sobre os logs e os vários artefatos, como funções e consultas salvas.
* O provedor de serviços pode executar a análise em todos os clientes.

As desvantagens da arquitetura centralizada são:

* Essa arquitetura é aplicável somente a dados de VM baseados em agente; ela não aborda fontes de dados de PaaS, SaaS e de malha do Azure.
* Talvez seja difícil separar os dados entre os clientes quando eles são mesclados em um único workspace. O único método satisfatório para fazer isso é usar o FQDN (nome de domínio totalmente qualificado) do computador ou por meio da ID da assinatura do Azure. 
* Todos os dados de todos os clientes serão armazenados na mesma região com uma única fatura e as mesmas definições de retenção e configuração.
* Os serviços de PaaS e de malha do Azure, como o Diagnóstico do Azure e os Logs de Auditoria do Azure, exigem que o workspace esteja no mesmo locatário do recurso; portanto, eles não podem enviar os logs para o workspace central.
* Todos os agentes de VM de todos os clientes serão autenticados no workspace central usando a mesma ID e chave do workspace. Não há nenhum método para bloquear os logs de um cliente específico sem interromper outros clientes.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. os logs híbridos são armazenados no espaço de trabalho localizado no locatário do cliente e alguns deles são retirados para um local central.

A terceira combinação de arquitetura entre as duas opções. Ela se baseia na primeira arquitetura distribuída, na qual os logs são locais para cada cliente, mas usando um mecanismo para criar um repositório central de logs. Uma parte dos logs é inserida em um local central para relatórios e análise. Essa parte pode ser uma pequena quantidade de tipos de dados ou um resumo da atividade, como estatísticas diárias.

Há duas opções para implementar logs em um local central:

1. Workspace central: o provedor de serviços pode criar um workspace em seu locatário e usar um script que utiliza a [API de Consulta](https://dev.loganalytics.io/) com a [API de Coleta de Dados](../../azure-monitor/platform/data-collector-api.md) para levar os dados de vários workspaces para esse local central. Outra opção, além de um script, é usar os [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI como um local central: Power BI pode atuar como o local central quando os vários espaços de trabalho exportam dados para ele usando a integração entre o espaço de trabalho Log Analytics e [Power bi](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Próximas etapas

* Automatizar a criação e configuração de workspaces usando [modelos do Gerenciador de recursos](template-workspace-configuration.md)

* Automatizar a criação de workspaces usando o [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Use [Alertas](../../azure-monitor/platform/alerts-overview.md) para integrar com sistemas existentes

* Gerar relatórios de resumo usando o [Power BI](../../azure-monitor/platform/powerbi.md)

* Integrar clientes ao [Gerenciamento de recursos delegado do Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
