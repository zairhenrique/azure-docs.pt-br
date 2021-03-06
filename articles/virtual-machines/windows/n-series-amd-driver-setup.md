---
title: Instalação do driver AMD GPU da série N do Azure para Windows
description: Como configurar drivers de GPU AMD para VMs da série N que executam o Windows Server ou Windows no Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: fdc6834f3fb5ee97f27a6397645b965863e90a6b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190532"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instalar drivers do AMD GPU em VMs da série N que executam o Windows

Para aproveitar os recursos de GPU das novas VMs da série Azure NVv4 que executam o Windows, os drivers de GPU AMD devem ser instalados. A extensão do driver AMD estará disponível nas próximas semanas. Este artigo fornece sistemas operacionais, drivers e etapas de instalação e verificação manuais com suporte.

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operacionais e drivers com suporte

| Sistema operacional | Driver |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10-Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se por Área de Trabalho Remota a cada VM da série NVv4.

1. Baixe e extraia os arquivos de instalação do driver. Navegue até a pasta e execute ' Setup. exe ' para instalar o driver com suporte para o sistema operacional Windows.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra a configuração bem-sucedida da placa MI25 do Radeon instinto em uma VM NVv4 do Azure.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

Você pode usar o DxDiag para verificar as propriedades de exibição de GPU, incluindo a RAM de vídeo. O exemplo a seguir mostra uma partição 1/oitavo da placa MI25 Radeon instinto em uma VM NVv4 do Azure.
<br />
![Propriedades do driver GPU](./media/n-series-amd-driver-setup/dxdiag.png)
