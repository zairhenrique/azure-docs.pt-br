---
title: Usar a Análise de Armazenamento do Azure para coletar dados de logs e métricas | Microsoft Docs
description: A Análise de Armazenamento permite que você para acompanhe dados de métricas de todos os serviços de armazenamento e para coletar logs para o armazenamento de Tabelas, Blobs e Filas.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: efca3dad6d8bfadbc334067b0189d2bea5aef445
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750512"
---
# <a name="storage-analytics"></a>Análise de Armazenamento

A análise de armazenamento do Azure realiza registro e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear pedidos, analisar tendências de uso e diagnosticar problemas em sua conta de armazenamento.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use as operações definir propriedades do [serviço blob](/rest/api/storageservices/set-blob-service-properties), [definir propriedades](/rest/api/storageservices/set-queue-service-properties)do serviço de fila, [definir propriedades do serviço tabela](/rest/api/storageservices/set-table-service-properties)e [definir propriedades do serviço de arquivo](/rest/api/storageservices/Get-File-Service-Properties) para habilitar a análise de armazenamento para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando os serviços de Blob e APIs do serviço de tabela

A Análise de Armazenamento tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para sua conta de armazenamento. Para obter mais informações sobre limites de conta de armazenamento, consulte [escalabilidade e metas de desempenho para contas de armazenamento Standard](scalability-targets-standard-account.md).

Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>Cobrança da análise de armazenamento

Todos os dados de métricas são gravados pelos serviços de uma conta de armazenamento. Como resultado, cada operação de gravação executada pela análise de armazenamento é faturável. Além disso, a quantidade de armazenamento usado por dados de métrica também é faturável.

As seguintes ações executadas pela análise de armazenamento são faturáveis:

* Solicitações para criar blobs para registro em log.
* Solicitações para criar entidades de tabela para métricas.

Se você configurou uma política de retenção de dados, você não é cobrado para excluir transações quando a análise de armazenamento exclui dados antigos de log e métricas. No entanto, excluir as transações de um cliente são faturáveis. Para saber mais sobre as políticas de retenção, consulte [Definindo uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Noções básicas sobre solicitações faturáveis

Todas as solicitações feitas ao serviço de armazenamento da conta são faturáveis ou então não faturáveis. A análise de armazenamento registra cada solicitação individual feita a um serviço, incluindo uma mensagem de status que indica como a solicitação foi processada. Da mesma forma, a análise de armazenamento armazena métricas para um serviço e as operações de API desse serviço, incluindo os percentuais e a contagem de determinadas mensagens de status. Juntos, esses recursos podem ajudá-lo a analisar suas solicitações faturáveis, fazer melhorias em seu aplicativo e diagnosticar problemas com solicitações de serviços. Para saber mais sobre a cobrança, confira [Noções básicas sobre cobrança no armazenamento do Azure – largura de banda, transações e capacidade](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao analisar os dados de análise de armazenamento, você pode usar as tabelas no tópico [Mensagens de operações e status registradas da análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) para determinar quais solicitações são faturáveis. Em seguida, você pode comparar seus logs e dados de métricas para as mensagens de status para ver se houve cobrança por uma determinada solicitação. Você também pode usar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou operação de API individual.

## <a name="next-steps"></a>Próximos passos
* [Monitorar uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)
* [Métricas da Análise de Armazenamento](storage-analytics-metrics.md)
* [Registro em log da Análise de Armazenamento](storage-analytics-logging.md)
