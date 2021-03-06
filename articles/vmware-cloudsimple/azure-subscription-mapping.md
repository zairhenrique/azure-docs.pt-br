---
title: Criar pools de recursos com o mapeamento de assinatura do Azure
description: Descreve como criar pools de recursos para sua nuvem privada da AVS por meio do mapeamento de assinatura do Azure
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014956"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Criar pools de recursos para sua nuvem privada de AVS com o mapeamento de assinatura do Azure
O mapeamento de assinatura do Azure permite que você crie pools de recursos para sua nuvem privada da AVS a partir dos pools de recursos do vSphere disponíveis. No portal da AVS, você pode exibir e gerenciar a assinatura do Azure para suas nuvens privadas da AVS.

> [!NOTE]
> O mapeamento de um pool de recursos também mapeia os pools de recursos filho. Um pool de recursos pai não poderá ser mapeado se algum pool de recursos filho já estiver mapeado.

1. [Acesse o portal da AVS](access-cloudsimple-portal.md).
2. Abra a página **recursos** e selecione **mapeamento de assinaturas do Azure**.  
3. Clique em **Editar mapeamento de assinatura do Azure**.  
4. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta para a esquerda. 

    ![Assinaturas do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
