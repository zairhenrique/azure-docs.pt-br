---
title: Personalizar propriedades RDP com o PowerShell – Azure
description: Como personalizar as propriedades de RDP para a área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: b811b9afe0abcd81fe81d47ef0e1566d3042c8ae
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367379"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizar propriedades de protocolo RDP para um pool de hosts

Personalizar as propriedades de protocolo RDP (RDP) de um pool de hosts, como experiência de vários monitores e redirecionamento de áudio, permite que você forneça uma experiência ideal para seus usuários com base em suas necessidades. Você pode personalizar as propriedades de RDP na área de trabalho virtual do Windows usando o parâmetro **-CustomRdpProperty** no cmdlet **set-RdsHostPool** .

Consulte [configurações de arquivo RDP com suporte](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) para obter uma lista completa das propriedades com suporte e seus valores padrão.

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Propriedades padrão de RDP

Por padrão, os arquivos RDP publicados contêm as seguintes propriedades:

|Propriedades de RDP | Áreas de trabalho | RemoteApps |
|---|---| --- |
| Modo de vários monitores | habilitado | N/D |
| Redirecionamentos de unidade habilitados | Unidades, área de transferência, impressoras, portas COM, dispositivos USB e cartões inteligentes| Unidades, área de transferência e impressoras |
| Modo de áudio remoto | Reproduzir localmente | Reproduzir localmente |

Todas as propriedades personalizadas que você definir para o pool de hosts substituirão esses padrões.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adicionar ou editar uma única propriedade RDP personalizada

Para adicionar ou editar uma única propriedade RDP personalizada, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Adicionar ou editar várias propriedades personalizadas de RDP

Para adicionar ou editar várias propriedades de RDP personalizadas, execute os seguintes cmdlets do PowerShell fornecendo as propriedades de RDP personalizadas como uma cadeia de caracteres separada por ponto-e-vírgula:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Redefinir todas as propriedades de RDP personalizadas

Você pode redefinir propriedades individuais de RDP personalizadas para seus valores padrão seguindo as instruções em [Adicionar ou editar uma única propriedade RDP personalizada](#add-or-edit-a-single-custom-rdp-property), ou você pode redefinir todas as propriedades RDP personalizadas para um pool de hosts executando o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Uma captura de tela do cmdlet Get-RDSRemoteApp do PowerShell com Name e FriendlyName realçado.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você personalizou as propriedades de RDP para um determinado pool de hosts, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Esses próximos dois como tos informarão como se conectar a uma sessão usando o cliente de sua escolha:

- [Conectar-se ao cliente de desktop do Windows](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web](connect-web.md)
