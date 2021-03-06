---
title: Solução de problemas do Hub IoT do Azure erro 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Entenda como corrigir o erro 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960835"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Este artigo descreve as causas e soluções para erros de **403006 DeviceMaximumActiveFileUploadLimitExceeded** .

## <a name="symptoms"></a>Sintomas

A solicitação de upload de arquivo falha com o código de erro **403006** e uma mensagem "o número de solicitações de carregamento de arquivo ativo não pode exceder 10".

## <a name="cause"></a>Causa

Cada cliente de dispositivo é limitado a [10 carregamentos de arquivo simultâneos](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Você poderá exceder facilmente o limite se o dispositivo não notificar o Hub IoT quando os carregamentos de arquivos forem concluídos. Esse problema é normalmente causado por uma rede do lado do dispositivo não confiável.

## <a name="solution"></a>Solução

Verifique se o dispositivo pode [notificar imediatamente a conclusão do upload do arquivo do Hub IOT](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Em seguida, tente [reduzir o TTL do token SAS para a configuração de carregamento de arquivo](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre carregamentos de arquivos, consulte [carregar arquivos com o Hub IOT](./iot-hub-devguide-file-upload.md) e [Configurar carregamentos de arquivo do Hub iot usando o portal do Azure](./iot-hub-configure-file-upload.md).