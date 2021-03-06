---
title: MABS & matriz de suporte do System Center DPM
description: Este artigo resume o suporte ao backup do Azure quando você usa o Backup do Microsoft Azure Server (MABS) ou o System Center DPM para fazer backup de recursos locais e da VM do Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 9441f7ce9069cd85475877f37abe669f3c4fd516
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444019"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matriz de suporte para backup com o Backup do Microsoft Azure Server ou o System Center DPM

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de máquinas locais e cargas de trabalho e VMs (máquinas virtuais) do Azure. Este artigo resume as configurações de suporte e as limitações para fazer backup de computadores usando o Backup do Microsoft Azure Server (MABS) ou o System Center Data Protection Manager (DPM) e o backup do Azure.

## <a name="about-dpmmabs"></a>Sobre o DPM/MABS

[O System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) é uma solução empresarial que configura, facilita e gerencia o backup e a recuperação de computadores e dados corporativos. Ele faz parte do conjunto [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) de produtos.

MABS é um produto de servidor que pode ser usado para fazer backup de servidores físicos locais, VMs e aplicativos em execução neles.

O MABS é baseado no System Center DPM e fornece funcionalidade semelhante com algumas diferenças:

- Não é necessária a licença do System Center para executar o MABS.
- Para o MABS e o DPM, o Azure fornece armazenamento de backup de longo prazo. Além disso, o DPM permite fazer backup de dados para armazenamento de longo prazo em fita. O MABS não possui essa funcionalidade.
- Você pode fazer backup de um servidor DPM primário com um servidor DPM secundário. O servidor secundário protegerá o banco de dados do servidor primário e as réplicas de fonte de dados armazenadas no servidor primário. Se o servidor primário falhar, o servidor secundário poderá continuar a proteger as cargas de trabalho protegidas pelo servidor primário, até que o servidor primário esteja disponível novamente.  O MABS não possui essa funcionalidade.

Você baixa o MABS do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Ele pode ser executado localmente ou em uma VM do Azure.

O DPM e o MABS são compatíveis com backup de uma ampla variedade de aplicativos e sistemas operacionais de clientes e servidores. Eles fornecem vários cenários de backup:

- É possível fazer backup no nível do computador com o backup de estado do sistema ou bare-metal.
- É possível fazer backup de arquivos, compartilhamentos, pastas e volumes específicos.
- Você pode fazer backup de aplicativos específicos usando configurações otimizadas de reconhecimento de aplicativo.

## <a name="dpmmabs-backup"></a>Backup do DPM/MABS

O backup usando o DPM/MABS e o backup do Azure funciona da seguinte maneira:

1. O agente de proteção do DPM/MABS é instalado em cada computador cujo backup será feito.
1. O backup de computadores e aplicativos é feito no armazenamento local no DPM/MABS.
1. O Agente do MARS (Serviços de Recuperação do Microsoft Azure) é instalado no servidor MARS/DPM.
1. O agente MARS faz backup dos discos do DPM/MABS em um cofre de serviços de recuperação de backup no Azure usando o backup do Azure.

Para mais informações:

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Examine o que tem suporte](backup-support-matrix-mars-agent.md) para o agente Mars.

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Agente** | **Localidade**
--- | --- | ---
**Fazer backup das cargas de trabalho/computadores locais** | O agente de proteção do DPM/MABS é executado nos computadores que você deseja fazer backup.<br/><br/> O agente MARS no servidor DPM/MABS.<br/> A versão mínima do agente dos Serviços de Recuperação do Microsoft Azure ou do agente de Backup do Azure necessária para habilitar esse recurso é 2.0.8719.0.  | O DPM/MABS deve estar em execução no local.

## <a name="supported-deployments"></a>Implantações com suporte

O DPM/MABS pode ser implantado como resumido na tabela a seguir.

**Implantação** | **Suporte** | **Detalhes**
--- | --- | ---
**Implantado localmente** | Servidor físico<br/><br/>VM do Hyper-V<br/><br/> VM do VMware | Se o DPM/MABS for instalado como uma VM VMware, ele fará o backup apenas das VMs e das cargas de trabalho do VMware em execução nessas VMs.
**Implantado como uma VM do Azure Stack** | Somente no MABS | O DPM não pode ser usado para fazer backup das VMs do Azure Stack.
**Implantado como uma VM do Azure** | Protege as VMs e cargas de trabalho do Azure em execução nessas VMs | O DPM/MABS em execução no Azure não pode fazer backup de computadores locais.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemas operacionais compatíveis com MABS e DPM

O backup do Azure pode fazer backup de instâncias do DPM/MABS que estão executando qualquer um dos sistemas operacionais a seguir. Os sistemas operacionais devem estar executando os pacotes e as atualizações de serviços mais recentes.

**Cenário** | **DPM/MABS**
--- | ---
**MABS na VM do Azure** |  Datacenter do Windows 2016.<br/><br/> Datacenter do Windows 2019.<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e 8 GB de RAM.
**DPM na VM do Azure** | System Center 2012 R2 com atualização 3 ou posterior.<br/><br/> Sistema de operacional Windows conforme [exigido pelo System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Recomendamos que você inicie com uma imagem do Marketplace.<br/><br/> Standard_A4_v2 mínimo com quatro núcleos e 8 GB de RAM.
**O MABS no local** |  MABS v3 e posterior: Windows Server 2016 ou Windows Server 2019
**O DPM no local** | VM do Hyper-V/servidor físico: System Center 2012 SP1 ou posterior.<br/><br/> VM VMware: System Center 2012 R2 com atualização 5 ou posterior.

>[!NOTE]
>Não há suporte para a instalação do Servidor de Backup do Azure no Windows Server Core ou Microsoft Hyper-V Server.

## <a name="management-support"></a>Suporte de gerenciamento

**Problema** | **Detalhes**
--- | ---
**Instalação** | Instale o DPM/MABS em um computador de finalidade única.<br/><br/> Não instale o DPM/MABS em um controlador de domínio, em um computador com a instalação da função de servidor de aplicativos, em um computador que esteja executando o Microsoft Exchange Server ou System Center Operations Manager, ou em um nó de cluster.<br/><br/> [Examine todos os requisitos de sistema do DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domínio** | O DPM/MABS deve ser Unido a um domínio. Instale primeiro e, em seguida, associe o MABS/DPM em um domínio. Não é possível mover o MABS/DPM para um novo domínio após a implantação.
**Storage** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para o MABS v1.
**Atualização do MABS** | Você pode instalar diretamente o MABS v3 ou atualizar o MABS v2 para o MABS v3. [Saiba mais](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Mover o MABS** | Se você estiver usando o MBS, é possível mover o MABS para um novo servidor mantendo o armazenamento.<br/><br/> O servidor deve ter o mesmo nome que o original. Você não pode alterar o nome se quiser manter o mesmo pool de armazenamento e usar o mesmo banco de dados do MABS para armazenar os pontos de recuperação de dados.<br/><br/> Você precisará de um backup do banco de dados MABS pois precisará restaurá-lo.

## <a name="mabs-support-on-azure-stack"></a>Compatibilidade do MABS no Azure Stack

Você pode implantar o MABS em uma VM do Azure Stack para poder gerenciar o backup de VMs do Azure Stack e as cargas de trabalho em um único local.

**Componente** | **Detalhes**
--- | ---
**MABS na VM do Azure Stack** | Pelo menos o tamanho a2. Recomendamos que você comece com uma imagem do Windows Server 2012 R2 ou do Windows Server 2016 do Azure Marketplace.<br/><br/> Não instale mais nada na VM MABS.
**Armazenamento do MABS** | Use uma conta de armazenamento separada para a VM MABS. O agente MARS em execução no MABS precisa de armazenamento temporário para um local de cache e para manter os dados restaurados da nuvem.
**Pool de armazenamento do MABS** | O tamanho do pool de armazenamento MABS é determinado pelo número e pelo tamanho dos discos que são anexados à VM MABS. Cada tamanho de VM do Azure Stack tem um número máximo de discos. Por exemplo, A2 é para quatro discos.
**Retenção do MABS** | Não retenha os dados de backup nos discos MABS locais por mais de cinco dias.
**Escalar verticalmente o MABS** | Para escalar verticalmente sua implantação, você pode aumentar o tamanho da VM do MABS. Por exemplo, você pode alterar de uma série para D.<br/><br/> Você também pode garantir que esteja descarregando dados com backup no Azure regularmente. Se necessário, você pode implantar servidores MABS adicionais.
**.NET Framework em MABS** | A VM MABS precisa .NET Framework 3,3 SP1 ou posterior instalado.
**Domínio do MABS** | A VM do MABS deve ser associada a um domínio. Um usuário de domínio com privilégios de administrador deve instalar o MABS na VM.
**Backup de dados da VM do Azure Stack** | Você pode fazer backup de arquivos, pastas e aplicativos.
**Compatibilidade de backup** | Esses sistemas operacionais têm suporte para VMs das quais você deseja fazer backup:<br/><br/> Canal semestral do Windows Server (datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Suporte SQL Server para VMs Azure Stack** | Faça backup SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Fazer backup e recuperar um banco de dados.
**Compatibilidade do SharePoint para VMs do Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Faça backup e recupere um farm, banco de dados, front-end e servidor Web.
**Requisitos de rede para backups de VMs** | Todas as VMs na carga de trabalho Azure Stack devem pertencer à mesma rede virtual e pertencer à mesma assinatura.

## <a name="dpmmabs-networking-support"></a>Suporte às redes MABS/DPM

### <a name="url-access"></a>acesso à URL

O servidor MABS/DPM precisa de acesso a estas URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Conectividade do MABS/DPM no Backup do Azure

É necessária conectividade com o serviço de Backup do Azure para os backups funcionarem corretamente e a assinatura do Azure deve estar ativa. A tabela a seguir mostra o comportamento se essas duas coisas não ocorrerem.

**MABS no Azure** | **Assinatura** | **Backup/restauração**
--- | --- | ---
Conectado | Ativo | Faça backup no disco do DPM/MABS.<br/><br/> Faça backup no Azure.<br/><br/> Restaurar do disco.<br/><br/> Restaurar do Azure.
Conectado | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.
Sem conectividade por mais de 15 dias | Ativo | Nenhum backup em disco ou no Azure.<br/><br/> Você pode restaurar do disco ou do Azure.
Sem conectividade por mais de 15 dias | Expirado/desprovisionado | Nenhum backup em disco ou no Azure.<br/><br/> Se a assinatura tiver expirado, você poderá restaurar do disco ou do Azure.<br/><br/> Se a assinatura for encerrada, você não poderá restaurar do disco ou do Azure. Os pontos de recuperação do Azure são excluídos.

## <a name="dpmmabs-storage-support"></a>Compatível com armazenamento do MABS/DPM

Os dados submetidos a backup no DPM/MABS são armazenados no armazenamento em disco local.

**Storage** | **Detalhes**
--- | ---
**MBS** | O MBS (armazenamento de backup moderno) tem suporte do DPM 2016/MABS V2 e posterior. Ele não está disponível para o MABS v1.
**Armazenamento MABS na VM do Azure** | Os dados são armazenados em discos do Azure que são anexados à VM do DPM/MABS e que são gerenciados no DPM/MABS. O número de discos que podem ser usados para o pool de armazenamento do DPM/MABS é limitado pelo tamanho da VM.<br/><br/> VM a2:4 discos; VM a3:8 discos; VM A4:16 discos, com um tamanho máximo de 1 TB para cada disco. Isso determina o pool de armazenamento de backup total que está disponível.<br/><br/> A quantidade de dados que você pode fazer backup depende do número e do tamanho dos discos anexados.
**Retenção de dados do MABS em VM do Azure** | Recomendamos que você retenha dados de um dia no disco do Azure/MABS do DPM e faça backup do DPM/MABS no cofre para maior retenção. Assim, você pode proteger uma quantidade maior de dados descarregando-os no Backup do Azure.

### <a name="modern-backup-storage-mbs"></a>Armazenamento de backup moderno (MBS)

Do DPM 2016/MABS v2 (em execução no Windows Server 2016) e posterior, você pode aproveitar o armazenamento de backup moderno (MBS).

- Os backups do MBS são armazenados no disco ReFS (Sistema de Arquivos Resiliente).
- O MBS usa a clonagem de bloco ReFS para um backup mais rápido e um uso mais eficiente do espaço de armazenamento.
- Quando você adiciona volumes ao pool de armazenamento local do DPM/MABS, você os configura com letras de unidade. Em seguida, é possível configurar o armazenamento de carga de trabalho em diferentes volumes.
- Ao criar grupos de proteção para fazer backup dos dados do MABS/DPM, é possível selecionar a unidade que você deseja usar. Por exemplo, você pode armazenar backups para SQL ou outras cargas de trabalho de IOPS alta em uma unidade de alto desempenho e armazenar cargas de trabalho que são submetidas a backup com menos frequência em uma unidade de desempenho inferior.

## <a name="supported-backups-to-mabs"></a>Backups compatíveis com o MABS

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com Servidor de Backup do Azure, consulte a [matriz de proteção de servidor de backup do Azure](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Backups compatíveis com o DPM

Para obter informações sobre os vários servidores e cargas de trabalho que você pode proteger com Data Protection Manager, consulte o artigo [o que o DPM pode fazer backup?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- As cargas de trabalho clusterizadas com backup pelo DPM/MABS devem estar no mesmo domínio que o DPM/MABS ou em um domínio filho/confiável.
- Você pode usar a autenticação NTLM/certificado para fazer backup de dados em grupos de trabalho ou domínios não confiáveis.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](backup-architecture.md#architecture-back-up-to-dpmmabs) sobre a arquitetura do MABS.
- [Revise](backup-support-matrix-mars-agent.md) o que é compatível com o agente do MARS.
- [Configure](backup-azure-microsoft-azure-backup.md) um servidor MABS.
- [Configurar o DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
