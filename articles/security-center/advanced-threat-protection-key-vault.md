---
title: Configure a proteção avançada contra ameaças do Azure Key Vault
description: Este artigo explica como configurar a proteção avançada contra ameaças para Azure Key Vault na central de segurança do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430543"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Configurar a proteção avançada contra ameaças para Azure Key Vault (versão prévia)

A proteção avançada contra ameaças para Azure Key Vault fornece uma camada adicional de inteligência de segurança. Essa ferramenta detecta tentativas potencialmente perigosas de acessar ou explorar contas de Key Vault. Usando a proteção de ameaças avançadas nativa na central de segurança do Azure, você pode lidar com ameaças sem ser um especialista em segurança e sem aprender sistemas de monitoramento de segurança adicionais.

Quando a central de segurança detecta a atividade anômala, ela exibe alertas. Ele também envia emails ao administrador da assinatura com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças identificadas.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Configurar a proteção avançada contra ameaças na central de segurança do Azure

Por padrão, a proteção avançada contra ameaças está habilitada para todas as suas contas de Key Vault quando você assina a camada Standard da central de segurança. Para saber mais, consulte [Preços](security-center-pricing.md).

Para habilitar ou desabilitar a proteção para uma assinatura específica, siga estas etapas.

1. No painel esquerdo na central de segurança, selecione **preços & configurações**.
1. Selecione a assinatura com as contas de armazenamento para as quais você deseja habilitar ou desabilitar a proteção contra ameaças.
1. Selecione **Tipo de preço**.
1. No grupo **selecionar camada de preços por tipo de recurso** , localize a linha **cofres de chaves** e selecione **habilitado** ou **desabilitado**.

    [![habilitar ou desabilitar a proteção avançada contra ameaças para Key Vault na central de segurança do Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Clique em **Salvar**.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a habilitar e desabilitar a proteção avançada contra ameaças para Azure Key Vault. 

Para obter outros materiais relacionados, consulte o seguinte artigo:

- [Detecção de ameaças para as camadas dos serviços do Azure na central de segurança](security-center-alerts-service-layer.md): Este artigo descreve os alertas relacionados à proteção avançada contra ameaças para o Azure Key Vault.
