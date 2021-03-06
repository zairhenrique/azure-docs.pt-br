---
title: Como habilitar o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como habilitar e configurar Azure Monitor para contêineres para que você possa entender como o contêiner está sendo executado e quais problemas relacionados ao desempenho foram identificados.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198064"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como habilitar o Azure Monitor para contêineres

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para contêineres para monitorar o desempenho de cargas de trabalho implantadas em ambientes kubernetes e hospedadas em:

- AKs ( [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/) )

- Clusters kubernetes autogerenciados hospedados no Azure usando o [mecanismo AKs](https://github.com/Azure/aks-engine).

- Clusters kubernetes autogerenciados hospedados em [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou localmente usando o mecanismo AKs.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor para contêineres podem ser habilitados para novas ou uma ou mais implantações existentes do kubernetes usando os seguintes métodos com suporte:

- Na portal do Azure, Azure PowerShell ou com CLI do Azure

- Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de começar, verifique se você tem o seguinte:

- **Um espaço de trabalho Log Analytics.**

    Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Você pode criar um espaço de trabalho ao habilitar o monitoramento do novo cluster AKS ou permitir que a experiência de integração crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../platform/template-workspace-configuration.md), do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento com suporte usados para o espaço de trabalho padrão, consulte [mapeamento de região para Azure monitor para contêineres](container-insights-region-mapping.md).

- Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../platform/manage-access.md).

- Você é membro da função **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKs.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* As métricas de Prometheus não são coletadas por padrão. Antes [de configurar o agente](container-insights-prometheus-integration.md) para coletá-los, é importante que você examine a [documentação](https://prometheus.io/) do Prometheus para entender o que pode ser recolhido e os métodos com suporte.

## <a name="supported-configurations"></a>Configurações com suporte

O seguinte é oficialmente suportado com Azure Monitor para contêineres.

- Ambientes: Azure Red Hat OpenShift, kubernetes local e AKS Engine no Azure e Azure Stack. Para obter mais informações, consulte [AKs Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões do kubernetes e da política de suporte são as mesmas que as versões do [AKs com suporte](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações na tabela a seguir listam as informações de configuração de proxy e firewall necessárias para que o agente em contêiner se comunique com Azure Monitor para contêineres. Todo o tráfego de rede do agente é de saída para Azure Monitor.

|Recurso de agente|Portas |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

As informações na tabela a seguir listam as informações de configuração de proxy e firewall para o Azure China.

|Recurso de agente|Portas |DESCRIÇÃO | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Ingestão de dados |
| *. oms.opinsights.azure.cn | 443 | Integração do OMS |
| *.blob.core.windows.net | 443 | Usado para monitorar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isso só será necessário se a versão da imagem do agente for ciprod09262019 ou anterior. |
| dc.services.visualstudio.com | 443 | Para a telemetria do agente usando Application Insights de nuvem pública do Azure. |

As informações na tabela a seguir listam as informações de configuração de proxy e firewall para o governo dos EUA do Azure.

|Recurso de agente|Portas |DESCRIÇÃO | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Ingestão de dados |
| *.oms.opinsights.azure.us | 443 | Integração do OMS |
| *.blob.core.windows.net | 443 | Usado para monitorar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isso só será necessário se a versão da imagem do agente for ciprod09262019 ou anterior. |
| dc.services.visualstudio.com | 443 | Para telemetria de agente usando Application Insights de nuvem pública do Azure. |

## <a name="components"></a>Componentes

Sua capacidade de monitorar o desempenho depende de um agente de Log Analytics em contêiner para Linux desenvolvido especificamente para Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior e é representada por uma data no seguinte formato: *mmddaaaa*.

>[!NOTE]
>Com a versão de visualização do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para coletar dados e encaminhá-los para Azure Monitor. Em vez disso, um nó do Linux implantado automaticamente no cluster como parte da implantação padrão coleta e encaminha os dados para Azure Monitor em nome de todos os nós do Windows no cluster.  
>

Quando uma nova versão do agente é lançada, ele é atualizado automaticamente nos clusters gerenciados do Kubernetes hospedados no AKS (Serviço de Kubernetes do Azure). Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente.
>O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

Habilite Azure Monitor para contêineres usando um dos seguintes métodos descritos na tabela a seguir.

| Estado da implantação | Método | DESCRIÇÃO |
|------------------|--------|-------------|
| Novo cluster AKS kubernetes | [Criar cluster AKS usando o CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento de um novo cluster AKS criado com CLI do Azure. |
| | [Criar cluster AKS usando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria usando a ferramenta de código-fonte aberto Terraform. |
| | [Criar um cluster OpenShift usando um modelo de Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento de um novo cluster OpenShift que você cria com um modelo de Azure Resource Manager pré-configurado. |
| | [Criar cluster OpenShift usando o CLI do Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Você pode habilitar o monitoramento ao implantar um novo cluster OpenShift usando CLI do Azure. |
| Cluster AKS kubernetes existente | [Habilitar para o cluster AKS usando CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando CLI do Azure. |
| |[Habilitar para o cluster AKS usando Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando a ferramenta de código-fonte aberto Terraform. |
| | [Habilitar para o cluster AKS de Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento de um ou mais clusters AKS já implantados na página de vários clusters no Azure Monitor. |
| | [Habilitar do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster AKS no portal do Azure. |
| | [Habilitar para o cluster AKS usando um modelo de Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento de um cluster AKS com um modelo de Azure Resource Manager pré-configurado. |
| | [Habilitar para o cluster kubernetes híbrido](container-insights-hybrid-setup.md) | Você pode habilitar o monitoramento de um mecanismo AKS hospedado no Azure Stack ou para o kubernetes hospedado no local. |
| | [Habilitar para o cluster OpenShift usando um modelo de Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento de um cluster OpenShift existente com um modelo de Azure Resource Manager pré-configurado. |
| | [Habilitar para o cluster OpenShift de Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Você pode habilitar o monitoramento de um ou mais clusters OpenShift já implantados na página de vários clusters no Azure Monitor. |

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento habilitado, você pode começar a analisar o desempenho dos clusters kubernetes hospedados no AKS (serviço kubernetes do Azure), Azure Stack ou em outro ambiente. Para saber como usar Azure Monitor para contêineres, consulte [Exibir o desempenho do cluster kubernetes](container-insights-analyze.md).
