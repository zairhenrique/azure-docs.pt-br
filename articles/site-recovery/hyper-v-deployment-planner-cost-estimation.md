---
title: Examinar o relatório de estimativa de custo do Planejador de Implantações do Azure Site Recovery para recuperação de desastre de VMs do Hyper-V no Azure | Microsoft Docs
description: Este artigo descreve como revisar o relatório de estimativa de custo do Planejador de Implantações do Azure Site Recovery para recuperação de desastres do Hyper-V no Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: bced6a9e6c59dc32657dbabef986e29e0447b28b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60947177"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Relatório de estimativa de custo do Planejador de Implantações do Azure Site Recovery 

O relatório do Planejador de Implantações do Azure Site Recovery fornece o resumo de estimativa de custo nas planilhas de [Recomendações](hyper-v-deployment-planner-analyze-report.md#recommendations) e a análise de custo detalhada na planilha de Estimativa de Custo. Ele tem a análise de custo detalhada por VM. 

### <a name="cost-estimation-summary"></a>Resumo de estimativa de custo 
O gráfico mostra a exibição de resumo do custo total de DR (recuperação de desastre) estimado para o Azure da sua região de destino escolhida e da moeda que você especificou para a geração de relatórios.

![Resumo de estimativa de custo](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

O resumo ajuda a entender o custo que precisa ser pago para armazenamento, computação, rede e licença ao proteger todas as VMs compatíveis usando o Azure Site Recovery. O custo é calculado para VMs compatíveis e não todas as VMs com criação de perfil. 
 
Você pode exibir o custo mensal ou anual. Saiba mais sobre [regiões de destino com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas com suporte](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes**: O custo total da recuperação de desastre é dividido em quatro componentes: custos de licença de computação, armazenamento, rede e do Site Recovery. O custo é calculado com base no consumo incorrido durante a replicação e no momento da análise de recuperação de desastre. A computação, o armazenamento (premium e standard), a ExpressRoute/VPN que está configurada entre o site local e o Azure e a licença do Site Recovery são usados para os cálculos.

**Custo por estados**: A categoria de custo total da recuperação de desastre (DR) se baseia em dois estados diferentes: replicação e análise de recuperação de desastre. 

**Custo de replicação**: O custo que será incorrido durante a replicação. Abrange o custo de armazenamento, de rede e da licença do Site Recovery. 

**Custo de Análise de DR**: O custo que será incorrido durante os failovers de teste. O Site Recovery gira máquinas virtuais durante o failover de teste. O custo da análise de recuperação de desastre abrange os custos de computação e armazenamento das VMs em execução. 

**Custo do armazenamento do Microsoft Azure por mês/ano**: O custo total de armazenamento incorrido no armazenamento standard e premium para replicação e análise de DR.

## <a name="detailed-cost-analysis"></a>Análise de custo detalhada
Os preços do Azure para computação, armazenamento e rede variam entre as regiões do Azure. É possível gerar um relatório de estimativa de custo com os preços do Azure mais recentes com base em sua assinatura, a oferta associada à sua assinatura e a região do Azure de destino especificada em uma moeda especificada. Por padrão, a ferramenta usa a região Oeste dos EUA 2 do Azure e a moeda dólar norte-americano (USD). Caso use qualquer outra região e moeda, na próxima vez em que gerar um relatório sem ID da assinatura, ID da oferta, região de destino e moeda, a ferramenta usará os preços da região de destino e moeda usadas pela última vez para fazer a estimativa de custo.

Esta seção mostra a ID da assinatura e a ID da oferta que você usou para a geração de relatórios. Se não forem usadas, ficará em branco.

Em todo o relatório, as células marcadas em cinza são somente leitura. As células em branco podem ser modificadas de acordo com seus requisitos.

![Detalhes da estimativa de custo](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Custo geral de recuperação de desastre por componentes
A primeira seção mostra o custo geral da recuperação de desastre pelos componentes e o custo de recuperação de desastre por estados. 

**Computação**: O custo de VMs IaaS executadas no Azure para as necessidades de recuperação de Desastre. Inclui as VMs que são criadas pelo Site Recovery durante as análises de recuperação de desastre (failovers de teste). Também inclui as VMs em execução no Azure, como o SQL Server com Grupos de Disponibilidade AlwaysOn e controladores de domínio ou servidores de nome de domínio.

**Armazenamento**: Custo do consumo de armazenamento do Azure para as necessidades de recuperação de desastre. Ele inclui o consumo de armazenamento para a replicação e durante os exercícios de recuperação de desastre.

**Rede**: Custo do ExpressRoute e VPN site a site para necessidades de recuperação de desastre. 

**Licença do Azure Site Recovery**: Custo da licença do Azure Site Recovery para todas as VMs compatíveis. Caso tenha inserido manualmente uma VM na tabela detalhada de análise de custo, o custo da licença do Site Recovery também estará incluído para essa VM.

### <a name="overall-dr-costs-by-states"></a>Custo geral de recuperação de desastre por estados
O custo total da recuperação de desastre é categorizado com base em dois estados diferentes: a replicação e a análise de recuperação de desastre.

**Replicação**: O custo incorrido no momento da replicação. Abrange o custo de armazenamento, de rede e da licença do Site Recovery. 

**Análise de recuperação de desastre**: O custo incorrido no momento da análise de recuperação de desastre. O Site Recovery gira VMs durante a análise de recuperação de desastre. Os custos de análise de recuperação de desastre e os custos de computação e armazenamento das VMs em execução.

* Duração total da análise de recuperação de desastre em um ano = Número de análises de recuperação de desastre x Duração de cada de análise de recuperação de desastre (dias)
* Custo médio de análise de recuperação de desastre (por mês) = Custo total de análise de recuperação de desastre/12

### <a name="storage-cost-table"></a>Tabela de custo de armazenamento
Esta tabela mostra o custo de armazenamento premium e standard incorrido para replicação e análises de recuperação de desastre com e sem descontos.

### <a name="site-to-azure-network"></a>Site para rede do Azure
Selecione a configuração apropriada de acordo com seus requisitos. 

**ExpressRoute**: Por padrão, a ferramenta seleciona o plano de ExpressRoute mais próximo que corresponda à largura de banda de rede necessária para a replicação delta. É possível alterar o plano de acordo com suas necessidades.

**Tipo de Gateway de VPN**: Selecione o Azure Gateway de VPN, se houver algum no seu ambiente. Por padrão, é NA.

**Região de destino**: Região do Azure especificada para DR. O preço usado no relatório de computação, armazenamento, rede e licença baseia-se nos preços do Azure para essa região. 

### <a name="vm-running-on-azure"></a>VM em execução no Azure
Talvez você tenha um controlador de domínio ou VM de DNS ou VM do SQL Server com grupos de disponibilidade Always On em execução no Azure para recuperação de desastre. É possível fornecer o número e o tamanho de VMs para considerar os custos de computação no custo total de recuperação de desastre. 

### <a name="apply-overall-discount-if-applicable"></a>Aplicar desconto geral, se aplicável
Se você for um cliente ou um parceiro do Azure e estiver qualificado para receber qualquer desconto no preço geral do Azure, poderá usar esse campo. A ferramenta aplica o desconto (em porcentagem) a todos os componentes.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Número de tipos de máquinas virtuais e custo de computação (por ano)
Essa tabela mostra o número de VMs do Windows e não Windows e o custo de computação da análise de recuperação de desastre para elas.

### <a name="settings"></a>Configurações 
**Uso do disco gerenciado**: Essa configuração especifica se um disco gerenciado está sendo usado no momento da análise de recuperação de desastre. O padrão é **Sim**. Se você tiver definido **-UseManagedDisks** como **Não**, o preço de disco não gerenciado será usado para o cálculo de custos.

**Moeda**: A moeda na qual o relatório é gerado.

**Duração do custo**: É possível exibir todos os custos do mês ou do ano inteiro. 

## <a name="detailed-cost-analysis-table"></a>Tabela de análise detalhada de custo
![Análise de custo detalhada](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

A tabela lista o detalhamento de custo para cada VM compatível. Também é possível usar essa tabela para obter o custo estimado de recuperação de desastre do Azure de VMs sem criação de perfil ao adicioná-las manualmente. Essa informação será útil em casos em que é necessário estimar os custos do Azure para uma nova implantação de recuperação de desastre sem a execução de uma criação de perfil detalhada.

Para adicionar manualmente as máquinas virtuais:

1. Selecione **Inserir linha** para inserir uma nova linha entre as linhas **Início** e **Término**.

1. Preencha as colunas a seguir com base no tamanho aproximado da VM e no número de VMs que correspondem a essa configuração: 

    a. **Número de VMs**

    b. **Tamanho da IaaS (Sua seleção)**

    c. **Tipo de armazenamento Standard/Premium**

    d. **Tamanho total de armazenamento da VM (GB)**

    e. **Número de análises de recuperação de desastre em um ano**

    f. **Duração de cada análise de recuperação de desastre (Dias)**

    g. **Tipo de sistema operacional**

    h. **Redundância de dados**

    i. **Benefício de Uso do Azure Híbrido**

1. É possível aplicar o mesmo valor a todas as VMs na tabela selecionando **Aplicar a todos** para **Número de análises de recuperação de desastre em um ano**, **Duração de cada análise recuperação de desastre (Dias)** , **Redundância de dados** e **Benefício de Uso Híbrido do Azure**.

1. Selecione **Recalcular custo** para atualizar o custo.

**Nome da VM**: O nome da VM.

**Número de VMs**: O número de VMs que correspondem à configuração. É possível atualizar o número de VMs existentes se uma configuração semelhante de VMs não tiver tido o perfil criado, mas estão protegidas.

**Tamanho de IaaS (recomendação)** : Tamanho da função VM da VM compatível recomendado pela ferramenta. 

**Tamanho da IaaS (Sua seleção)** : Por padrão, é igual ao tamanho da função de VM recomendado. Você pode alterar a função com base no requisito. O custo de computação se baseia no tamanho da função de VM selecionada.

**Tipo de armazenamento**: O tipo de armazenamento usado pela VM. É o armazenamento standard ou premium.

**Tamanho total de armazenamento da VM (GB)** : Armazenamento total da VM.

**Número de análises de recuperação de desastre em um ano**: O número de vezes que você executa análises de DR em um ano. Por padrão, são quatro vezes ao ano. É possível modificar o período de VMs específicas ou aplicar o novo valor para todas as VMs. Digite o novo valor na linha superior e selecione **Aplicar a todos**. O custo total das análises de recuperação de desastre é calculado com base no número de análises e no período de duração de cada uma delas. 

**Duração de cada análise de recuperação de desastre (Dias)** : A duração de cada análise de recuperação de desastres. Por padrão, são 7 dias a cada 90 dias, de acordo com o [Benefício do Software Assurance para Recuperação de Desastre](https://azure.microsoft.com/pricing/details/site-recovery). É possível modificar o período de VMs específicas ou aplicar um novo valor para todas as VMs. Insira um novo valor na linha superior e selecione **Aplicar a todos**. O custo total da análise de recuperação de desastre é calculado com base no número de análises em um ano e no período de duração de cada uma delas.
 
**Tipo de SO**: O tipo de sistema operacional (SO) da VM. É Windows ou Linux. Se o tipo de sistema operacional for Windows, o Benefício de Uso do Azure Híbrido poderá ser aplicado a essa VM. 

**Redundância de dados**: Pode ser um armazenamento com redundância local, um armazenamento com redundância geográfica ou um armazenamento com redundância geográfica com acesso de leitura. O padrão é o armazenamento com redundância local. É possível alterar o tipo com base em sua conta de armazenamento para VMs específicas ou aplicar o novo tipo a todas as VMs. Altere o tipo da linha superior e selecione **Aplicar a todos**. O custo de armazenamento para a replicação é calculado com base no preço de redundância de dados que você selecionou. 

**Benefício de Uso do Azure Híbrido**: É possível aplicar o Benefício de Uso Híbrido do Azure para as VMs, se for aplicável. O padrão é **Sim**. É possível alterar a configuração de VMs específicas ou atualizar todas as VMs. Selecione **Aplicar a todas**.

**Consumo total do Azure**: O custo de computação, armazenamento e licença do Site Recovery para a recuperação de desastre. Com base em sua seleção, mostra o custo mensal ou anual.

**Custo de replicação de estado contínuo**: Custo de armazenamento para replicação.

**Custo total de Análise de Recuperação de Desastre (média)** : Custo de computação e de armazenamento para análise de recuperação de desastre.

**Custo de licença do Azure Site Recovery**: Custo de licença do Site Recovery.

## <a name="supported-target-regions"></a>Regiões de destino com suporte
O Planejador de Implantações do Site Recovery fornece uma estimativa de custo para as seguintes regiões do Azure. Se sua região não estiver listada aqui, será possível usar qualquer uma das regiões a seguir com o preço mais próximo de sua região:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Moedas com suporte
O Planejador de Implantações do Site Recovery pode gerar o relatório de custo com qualquer uma das moedas a seguir.

|Moeda|NOME||Moeda|NOME||Moeda|NOME|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentino ($)||AUD|Dólar australiano ($)||BRL|Real brasileiro (R$)|
|CAD|Dólar canadense ($)||CHF|Franco suíço (chf)||DKK|Coroa dinamarquesa (kr)|
|EUR|Euro (€)||GBP|Libra esterlina (£)||HKD|Dólar de Hong Kong (HK$)|
|IDR|Rúpia da Indonésia (Rp)||INR|Rúpia (Índia) (₹)||JPY|Iene (Japão) (¥)|
|KRW|Won (Coreia) (₩)||MXN|Peso mexicano (MXN$)||MYR|Ringgit malaio (RM$)|
|NOK|Coroa norueguesa (kr)||NZD|Dólar neozelandês ($)||RUB|Rublo russo (руб)|
|SAR|Rial saudita (SR)||SEK|Coroa sueca (kr)||TWD|Dólar taiwanês (NT$)|
|TRY|Lira turca (TL)||USD| Dólar americano (US$)||ZAR|Rand da África do Sul (R)|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como proteger [VMs do Hyper-V para o Azure usando o Site Recovery](hyper-v-azure-tutorial.md).
