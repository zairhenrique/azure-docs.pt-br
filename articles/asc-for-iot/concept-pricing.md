---
title: Entender a central de segurança do Azure para custos de IoT | Microsoft Docs
description: Saiba mais sobre os custos associados à central de segurança do Azure para IoT e como controlá-los.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348508"
---
# <a name="pricing-and-associated-costs"></a>Preços e custos associados

Este artigo explica o modelo de preços da central de segurança do Azure para IoT, resume todos os custos associados e explica como gerenciá-los.

## <a name="pricing"></a>Preços

A central de segurança do Azure para o modelo de preços do IoT é composta por duas partes e é cobrada quando um hub IoT é [habilitado](quickstart-onboard-iot-hub.md) na central de segurança do Azure para IOT:

- Custo por recursos de segurança internos do dispositivo com base na análise de logs do Hub IoT.

- Custo por recursos de segurança aprimorados por mensagem com base em mensagens de segurança de dispositivos IoT Edge ou folha.


Para obter mais informações, consulte [preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Custos associados

A central de segurança do Azure para IoT tem custos associados, que não fazem parte do preço direto:


- Custos de armazenamento Log Analytics

Você pode reduzir os custos associados ao optar por determinados recursos da solução. Cancele alterando suas configurações.

Para alterar suas configurações:

1. Abra o Hub IoT.

2. Em **segurança**, clique em **visão geral**.

3. Clique em **Configurações**.

A tabela a seguir fornece um resumo dos custos associados e as implicações de cada opção.

|     | Uso | Comentário |
| --- | --- | --- |
| **Armazenamento Log Analytics** |  |
| Alertas e recomendação de dispositivo| Recomendação e alertas de segurança gerados pelo serviço | Não opcional |
| Dados brutos de segurança| Dados brutos de segurança de dispositivos IoT, coletados por agentes de segurança | Desabilitar _eventos de segurança de dispositivo bruto de armazenamento_ |
|

>[!Important]
> A recusa tem implicações graves na central de segurança do Azure para a disponibilidade do recurso de segurança de IoT. 
  
| Recusar | Envolvidas |
| --- | --- |
| _Coleção de metadados de entrelaçamento_ | Desabilitar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desabilitar IoT Edge recomendações de manifesto |
| | Desabilitar alertas e recomendações com base em identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivo bruto_ | OS detalhes sobre as recomendações de linha de base do dispositivo não estão disponíveis |
| | Detalhes sobre investigações de [recomendação](concept-recommendations.md) e [alerta](concept-security-alerts.md) não estão disponíveis |
|


## <a name="see-also"></a>Consulte também

- Acessar seus [dados brutos de segurança](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entender e explorar as [recomendações de segurança](concept-recommendations.md)
- Entender e explorar [alertas de segurança](concept-security-alerts.md)
