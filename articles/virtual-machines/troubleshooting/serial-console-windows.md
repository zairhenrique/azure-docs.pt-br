---
title: Console serial do Azure para Windows | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451249"
---
# <a name="azure-serial-console-for-windows"></a>Console serial do Azure para Windows

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) do Windows e instâncias do conjunto de dimensionamento de máquinas virtuais. Essa conexão serial conecta-se à porta serial COM1 da VM ou à instância do conjunto de dimensionamento de máquinas virtuais, fornecendo acesso a ela independentemente da rede ou do estado do sistema operacional. O console serial só pode ser acessado usando o portal do Azure e é permitido somente para os usuários que têm uma função de acesso de colaborador ou superior ao conjunto de dimensionamento de máquinas virtuais ou VM.

O console serial funciona da mesma maneira para VMs e instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menção a VMs irão incluir implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

Para obter a documentação do console serial para Linux, consulte [console serial do Azure para Linux](serial-console-linux.md).

> [!NOTE]
> O console serial está geralmente disponível em regiões globais do Azure e em visualização pública no Azure governamental. Ele ainda não está disponível na nuvem do Azure na China.


## <a name="prerequisites"></a>Pré-requisitos

* A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve usar o modelo de implantação do gerenciamento de recursos. Implantações clássicas não são suportadas.

- Sua conta que usa o console serial deve ter a [função de colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de armazenamento de [diagnóstico de inicialização](boot-diagnostics.md)

- Sua VM ou instância do conjunto de dimensionamento de máquinas virtuais deve ter um usuário baseado em senha. Você pode criar um com a função [reset password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso da VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.

* A VM para a instância do conjunto de dimensionamento de máquinas virtuais deve ter o [diagnóstico de inicialização](boot-diagnostics.md) habilitado.

    ![Configurações de diagnóstico de inicialização](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Habilitar a funcionalidade do console serial para o Windows Server

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado em sua VM ou conjunto de dimensionamento de máquinas virtuais.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Ativar o console serial em imagens personalizadas ou antigas
As imagens mais recentes do Windows Server no Azure têm o [console de administração especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitado por padrão. O SAC é suportado em versões de servidor do Windows, mas não está disponível em versões de cliente (por exemplo, Windows 10, Windows 8 ou Windows 7).

Para imagens mais antigas do Windows Server (criadas antes de fevereiro de 2018), você pode habilitar automaticamente o console serial por meio do recurso de comando de execução do portal do Azure. No portal do Azure, selecione **executar comando**e, em seguida, selecione o comando chamado **EnableEMS** na lista.

![Executar lista de comandos](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Como alternativa, para habilitar manualmente o console serial para VMs do Windows/conjunto de dimensionamento de máquinas virtuais criado antes de fevereiro de 2018, siga estas etapas:

1. Conecte-se à sua máquina virtual do Windows usando a Área de Trabalho Remota
1. Em um prompt de comando administrativo, execute os seguintes comandos:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicialize o sistema para que o console do SAC seja ativado.

    ![Console do SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Se necessário, o SAC pode ser habilitado offline também:

1. Anexe o disco do Windows para o qual você deseja que o SAC seja configurado como um disco de dados para a VM existente.

1. Em um prompt de comando administrativo, execute os seguintes comandos:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Como sei se o SAC está habilitado?

Se [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) não estiver ativado, o console serial não exibirá o prompt do SAC. Em alguns casos, as informações de integridade da VM são mostradas e, em outros casos, ficam em branco. Se você estiver usando uma imagem do Windows Server criada antes de fevereiro de 2018, o SAC provavelmente não estará habilitado.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Ativar o menu de inicialização do Windows no console serial

Se você precisar ativar os prompts do carregador de inicialização do Windows para serem exibidos no console serial, poderá adicionar as seguintes opções adicionais aos dados de configuração da inicialização. Para obter mais informações, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Conecte-se à sua VM do Windows ou à instância do conjunto de dimensionamento de máquinas virtuais usando Área de Trabalho Remota.

1. Em um prompt de comando administrativo, execute os seguintes comandos:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicie o sistema para que o menu de inicialização seja habilitado

> [!NOTE]
> O tempo limite que você definiu para exibir o menu do gerenciador de inicialização afetará o tempo de inicialização do sistema operacional. Se você acha que o valor de tempo limite de 10 segundos é muito curto ou muito longo, defina-o como um valor diferente.

## <a name="use-serial-console"></a>Use o Console Serial

### <a name="use-cmd-or-powershell-in-serial-console"></a>Usar o CMD ou o PowerShell no console serial

1. Conecte-se ao console serial. Se você se conectar com sucesso, o prompt será **SAC>** :

    ![Conectar ao SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Digite `cmd` para criar um canal que tenha uma instância do CMD.

1.  Insira `ch -si 1` ou pressione `<esc>+<tab>` teclas de atalho para alternar para o canal que está executando a instância CMD.

1.  Pressione **Enter** e insira as credenciais de login com permissões administrativas.

1.  Depois de inserir credenciais válidas, a instância do CMD é aberta.

1.  Para iniciar uma instância do PowerShell, digite `PowerShell` na instância CMD e pressione **Enter**.

    ![Abra o PowerShell e execute](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Use o console serial para chamadas NMI
Uma interrupção não-mascarável (NMI) é projetada para criar um sinal que o software em uma máquina virtual não irá ignorar. Historicamente, as NMIs são usadas para monitorar os problemas de hardware em sistemas que necessitam de tempos de resposta específicos. Atualmente, os programadores e administradores de sistema geralmente usam NMI como um mecanismo para depurar ou solucionar problemas de sistemas que não estão respondendo.

O console serial pode ser usado para enviar uma NMI para uma máquina virtual do Azure usando o ícone do teclado na barra de comando. Depois que o NMI é entregue, controlará a configuração da máquina virtual como o sistema responde. O Windows pode ser configurado para travar e criar um arquivo de despejo de memória ao receber um NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obter informações sobre como configurar o Windows para criar um arquivo de despejo de memória quando ele recebe uma NMI, consulte [Como gerar um arquivo de despejo de memória usando uma NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Usar teclas de função no console serial
As teclas de função estão habilitadas para uso no console serial nas VMs do Windows. A tecla F8 na lista suspensa do console serial fornece a conveniência de acessar facilmente o menu Configurações Avançadas de Inicialização, mas o console serial é compatível com todas as outras teclas de função. Talvez seja necessário pressionar **Fn** + **F1** (ou F2, F3, etc.) no teclado, dependendo do computador do qual você está usando o console serial.

### <a name="use-wsl-in-serial-console"></a>Use o WSL no console serial
O Subsistema Windows para Linux (WSL) foi habilitado para o Windows Server 2019 ou posterior, portanto, também é possível habilitar WSL para uso no console serial, se você estiver executando o Windows Server 2019 ou posterior. Isso pode ser benéfico para usuários que estão também familiarizados com os comandos do Linux. Para obter instruções habilitar WSL para o Windows Server, confira o [Guia de instalação](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reiniciar sua instância de conjunto de dimensionamento de máquinas virtuais/VM do Windows dentro do console serial
Você pode iniciar uma reinicialização dentro do console serial navegando até o botão de energia e clicando em "reiniciar VM". Isso iniciará uma reinicialização da VM e você verá uma notificação no portal do Azure sobre a reinicialização.

Isso é útil em situações em que você talvez queira acessar o menu de inicialização sem sair da experiência do console serial.

![Reinicialização do console serial do Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Desabilitar o console serial
Por padrão, todas as assinaturas têm o acesso ao console serial habilitado. Você pode desabilitar o console serial no nível de assinatura ou de conjunto de dimensionamento de máquinas virtuais/VM. Para obter instruções detalhadas, visite [habilitar e desabilitar o console serial do Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança de console serial

### <a name="access-security"></a>Segurança de acesso
O acesso ao console serial é limitado aos usuários que têm uma função de acesso de [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o locatário do Azure Active Directory exigir autenticação de vários fatores (MFA), o acesso ao console serial também precisará do MFA, pois o acesso ao console serial é feito através do [Portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados enviados são criptografados na rede.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.

> [!CAUTION]
> Nenhuma senha de acesso para o console seja registrada. No entanto, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de informações de identificação pessoal (PII), eles serão gravados nos logs de diagnóstico de inicialização VM. Eles serão gravados juntamente com todos os outro texto visível, como parte da implementação de scrollback do console serial função. Esses logs são circulares e somente indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso a eles. No entanto, recomendamos seguir a melhor prática de usar a Área de Trabalho Remota para qualquer coisa que possa envolver segredos e / ou PII.

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito acesso à mesma máquina virtual, o primeiro usuário será desconectado e o segundo usuário será conectado à mesma sessão.

> [!CAUTION]
> Isso significa que um usuário que está desconectado não será desconectado. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo semelhante) ainda está no roteiro. No Windows, há um tempo limite automático ativado no SAC; para Linux, você pode configurar o tempo limite do terminal.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é o foco principal do console serial do Azure. Para esse fim, garantimos que o console serial seja acessível aos deficientes visuais e auditivos, bem como às pessoas que talvez não consigam usar o mouse.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla **Tab** no seu teclado para navegar na interface do console serial do portal do Azure. Sua localização será destacada na tela. Para deixar o foco da janela do console serial, pressione **Ctrl**+**F6** no seu teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Use o console serial com um leitor de tela
O console serial possui suporte ao leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o console serial

Cenário          | Ações no console serial
:------------------|:-----------------------------------------
Regras de firewall incorretas | Acesse regras de firewall de Windows seriais de console e correção.
Corrupção de sistema de arquivos/verificação | Acesse o console serial e recupere o sistema de arquivos.
Problemas de configuração de RDP | Acesse o console serial e altere as configurações. Para mais informações, consulte a [documentação do RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Bloqueio de sistema de rede | Acesse o console serial do portal do Azure para gerenciar o sistema. Alguns comandos de rede são listados em [comandos do Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
Interagir com o carregador de inicialização | Acesso BCD através do console serial. Para obter informações, consulte [Ative o menu de inicialização do Windows no console serial](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com o console serial e o sistema operacional da VM. Aqui está uma lista desses problemas e etapas para mitigação de VMs do Windows. Esses problemas e atenuações se aplicam tanto a VMs quanto a instâncias do conjunto de dimensionamento de máquinas virtuais. Se eles não corresponderem ao erro que você está vendo, consulte os erros comuns do serviço de console serial em [erros comuns do console serial](./serial-console-errors.md).

Problema                             |   Atenuação
:---------------------------------|:--------------------------------------------|
Pressionando **Enter** depois que o banner de conexão não faz com que um prompt de login seja exibido. | Para mais informações, consulte [Hitting enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse erro pode ocorrer se você estiver executando uma VM personalizada, um appliance reforçado ou uma configuração de inicialização que faça com que o Windows não consiga se conectar corretamente à porta serial. Esse erro também ocorrerá se você estiver executando uma VM do Windows 10, porque somente as VMs do Windows Server estão configuradas para ter o EMS habilitado.
Apenas as informações de integridade são mostradas ao se conectar a uma VM do Windows| Esse erro ocorrerá se o console de administração especial não tiver sido habilitado para sua imagem do Windows. Consulte [Ative o console serial em imagens personalizadas ou antigas](#enable-the-serial-console-in-custom-or-older-images) para obter instruções sobre como ativar manualmente o SAC em sua VM do Windows. Para obter mais informações, consulte [Sinais de integridade do Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
O SAC não ocupa toda a área do console serial no navegador | Esse é um problema conhecido com o Windows e o emulador de terminal. Estamos acompanhando esse problema com as duas equipes, mas, por enquanto, não há nenhuma mitigação.
Não é possível digitar no prompt do SAC se a depuração de kernel está habilitado. | RDP para VM e execute `bcdedit /debug {current} off` a partir de um prompt de comandos com privilégios elevados. Se você não puder RDP, poderá anexar o disco do sistema operacional a outra VM do Azure e modificá-lo enquanto estiver conectado como um disco de dados, executando `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` e, em seguida, trocando o disco de volta.
Colar no PowerShell no SAC resulta em um terceiro caractere se o conteúdo original tiver um caractere repetitivo. | Para uma solução alternativa, execute `Remove-Module PSReadLine` para descarregar o módulo PSReadLine da sessão atual. Esta ação não irá excluir ou desinstalar o módulo.
Algumas entradas de teclado produzem saída de SAC estranha (por exemplo, **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) sequências de escape não são suportadas pelo prompt do SAC.
Colar longas cadeias de caracteres não funciona. | O console serial limita o comprimento de cadeias de caracteres colados em terminal a 2048 caracteres para impedir a sobrecarga da largura de banda de porta serial.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P. como posso enviar comentários?**

a. Forneça feedback criando um problema no GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), você pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria da máquina virtual https://feedback.azure.com.

**P. o console serial dá suporte a copiar/colar?**

a. Sim. Use **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift**+**V** para copiar e colar no terminal.

**P. quem pode habilitar ou desabilitar o console serial para minha assinatura?**

a. Para ativar ou desativar o console serial em um nível de assinatura, você deve ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

**P. quem pode acessar o console serial da minha VM?**

a. Você deve ter a função de Colaborador da Máquina Virtual ou superior para que uma VM acesse o console serial da VM.

**P. meu console serial não está exibindo nada, o que eu faço?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para ativar o console serial, consulte [Ative o console serial em imagens personalizadas ou antigas](#enable-the-serial-console-in-custom-or-older-images).

**P. o console serial está disponível para conjuntos de dimensionamento de máquinas virtuais?**

a. Sim, é! Consulte o [console serial para conjuntos de dimensionamento de máquinas virtuais](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Próximos passos
* Para obter um guia detalhado dos comandos do CMD e do PowerShell que você pode usar no Windows SAC, consulte [Comandos do Windows: CMD e PowerShell](serial-console-cmd-ps-commands.md).
* O console serial também está disponível para VMs do [Linux](serial-console-linux.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).
