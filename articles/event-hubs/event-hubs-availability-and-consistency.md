---
title: Disponibilidade e consistência – Hubs de Eventos do Azure | Microsoft Docs
description: Como fornecer o máximo de disponibilidade e consistência com os Hubs de Eventos do Azure usando partições.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904490"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência nos Hubs de Eventos

## <a name="overview"></a>Visão Geral
Os Hubs de Eventos do Azure usam um [modelo de particionamento](event-hubs-scalability.md#partitions) para melhorar a disponibilidade e a paralelização dentro de um único hub de eventos. Por exemplo, se um hub de eventos tiver quatro partições e uma delas estiver sendo movida de um servidor para outro em uma operação de balanceamento de carga, você ainda poderá enviar e receber das outras três partições. Além disso, ter mais partições permite que você tenha mais leitores simultâneos processando seus dados, melhorando sua taxa de transferência agregada. Noções básicas sobre as implicações de particionamento e ordenação em um sistema distribuído é um aspecto fundamental do design de soluções.

Para ajudar a explicar a compensação entre ordenação e disponibilidade, confira o [Teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), também conhecido como teorema de Brewer. Este teorema discute a escolha entre consistência, disponibilidade e tolerância a partição. Ele informa que para os sistemas particionados por rede sempre há uma compensação entre a consistência e a disponibilidade.

O teorema de Brewer define a consistência e a disponibilidade como a seguir:
* Tolerância a partição: a capacidade de um sistema de processamento de dados continuar processando dados mesmo que ocorra uma falha de partição.
* Disponibilidade: um nó sem falha retorna uma resposta razoável dentro de um período razoável (sem erros nem tempos limites).
* Consistência: há garantia de que uma leitura retorne a gravação mais recente para um determinado cliente.

## <a name="partition-tolerance"></a>Tolerância a partição
Os Hubs de Eventos são criados sobre um modelo de dados particionado. Você pode configurar o número de partições no seu hub de eventos durante a instalação, mas você não pode alterar este valor posteriormente. Já que você deve usar partições com Hubs de Eventos, você precisa tomar uma decisão quanto à disponibilidade e à consistência do seu aplicativo.

## <a name="availability"></a>Disponibilidade
A maneira mais simples de começar com os Hubs de Eventos é usar o comportamento padrão. Se você criar um novo objeto **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** e usar o método **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** , os eventos serão distribuídos automaticamente entre as partições do seu hub de eventos. Esse comportamento permite a maior quantidade possível de tempo de atividade.

Para casos de uso que exigem o máximo tempo de atividade, esse modelo é preferencial.

## <a name="consistency"></a>Consistência
Em alguns cenários, a ordenação de eventos pode ser importante. Por exemplo, você pode desejar que seu sistema de back-end processe um comando update antes de processar um comando delete. Neste exemplo, você pode definir a chave de partição em um evento ou usar um objeto `PartitionSender` para enviar eventos apenas para uma determinada partição. Isso assegura que, quando esses eventos forem lidos da partição, eles serão lidos em ordem.

Com essa configuração, tenha em mente que, se a partição específica para a qual você está enviando não estiver disponível, você receberá uma resposta de erro. Como um ponto de comparação, se você não tiver uma afinidade com uma única partição, o serviço de Hubs de Eventos enviará seu evento para a próxima partição disponível.

Uma solução possível para assegurar a ordenação e também maximizar a atividade seria agregar eventos como parte do seu aplicativo de processamento de eventos. A maneira mais fácil de fazer isso é carimbar seu evento com uma propriedade de número de sequência personalizada. O código a seguir mostra um exemplo:

#### <a name="azuremessagingeventhubs-500-or-latertablatest"></a>[Azure. Messaging. EventHubs (5.0.0 ou posterior)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earliertabold"></a>[Microsoft. Azure. EventHubs (4.1.0 ou anterior)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

O exemplo envia seu evento para uma das partições disponíveis em seu hub de eventos e define o número de sequência correspondente do seu aplicativo. Essa solução requer que o estado seja mantido pelo seu aplicativo de processamento, mas fornece a seus remetentes um ponto de extremidade com maior probabilidade de estar disponível.

## <a name="next-steps"></a>Próximos passos
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral do serviço dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
