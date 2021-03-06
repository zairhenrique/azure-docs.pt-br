---
title: Enviar e receber eventos dos hubs de eventos do Azure usando o .NET (antigo)
description: Este artigo fornece instruções para a criação de um aplicativo .NET Core que envia/recebe eventos de/para os hubs de eventos do Azure usando o pacote Microsoft. Azure. EventHubs antigo.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 1d399ffec2cd702dcfa39310803bc780b85bfde1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187455"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Enviar eventos para ou receber eventos de hubs de eventos do Azure usando o .NET Core (Microsoft. Azure. EventHubs)
Este guia de início rápido mostra como enviar eventos para e receber eventos de um hub de eventos usando a biblioteca do .NET Core **Microsoft. Azure. EventHubs** .

> [!WARNING]
> Este guia de início rápido usa o pacote antigo **Microsoft. Azure. EventHubs** . Para obter um início rápido que usa a biblioteca **Azure. Messaging. EventHubs** mais recente, consulte [Enviar e receber eventos usando a biblioteca Azure. Messaging. EventHubs](get-started-dotnet-standard-send-v2.md). Para mover seu aplicativo do usando a biblioteca antiga para uma nova, consulte o [guia para migrar de Microsoft. Azure. EventHubs para Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).

## <a name="prerequisites"></a>Prerequisites
Se você for novo nos hubs de eventos do Azure, consulte [visão geral dos hubs de eventos](event-hubs-about.md) antes de fazer este guia de início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Microsoft Azure assinatura**. Para usar os serviços do Azure, incluindo os hubs de eventos do Azure, você precisa de uma assinatura.  Se você não tiver uma conta do Azure existente, poderá se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/free/) ou usar seus benefícios de assinante do MSDN ao [criar uma conta](https://azure.microsoft.com).
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](https://www.microsoft.com/net/core). 
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace do hub de eventos** seguindo as instruções do artigo: obter a cadeia de [conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usará a cadeia de conexão posteriormente neste guia de início rápido.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo de console do .NET Core para enviar eventos para um hub de eventos. 

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, você pode seguir as etapas neste guia de início rápido para criar suas próprias.


### <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio. No menu **Arquivo**, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

![Novo Projeto](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

Adicione o pacote NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) de biblioteca do .NET Core ao projeto seguindo estas etapas: 

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por "Microsoft.Azure.EventHubs" e selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

1. Adicione as instruções `using` a seguir na parte superior do arquivo Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` para a cadeia de conexão de Hubs de Eventos e o caminho da entidade (nome do hub de eventos individual). Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação do hub de eventos. Verifique se `{Event Hubs connection string}` é a cadeia de conexão de nível de namespace e não a cadeia de hub de eventos. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Adicione um novo método chamado `MainAsync` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adicione um novo método chamado `SendMessagesToEventHub` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o código a seguir ao método `Main` na classe `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs deve ficar assim.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Execute o programa e certifique-se de que não existem erros.

## <a name="receive-events"></a>Receber eventos
Esta seção mostra como escrever um aplicativo de console .NET Core que recebe mensagens de um hub de eventos usando o [host do processador de eventos](event-hubs-event-processor-host.md). O [Host do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Ao usar o Host do Processador de Eventos, você pode dividir eventos através de vários receptores, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o Host do Processador de Eventos para um único destinatário.
> [!NOTE]
> Você pode baixar este início rápido como uma amostra com o [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName`, `StorageAccountName`, `StorageAccountKey` e `StorageContainerName` com seus valores de hub de eventos, e executá-los. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio. No menu **Arquivo**, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

![Novo Projeto](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

Adicione os pacotes NuGet da biblioteca .NET Standard [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) a seu projeto seguindo estas etapas: 

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por **Microsoft.Azure.EventHubs** e selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
3. Repita as etapas 1 e 2 e instalar o pacote **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e em **Classe**. Nomeie a nova classe **SimpleEventProcessor**.

2. Abra o arquivo SimpleEventProcessor.cs e adicione as seguintes instruções `using` à parte superior do arquivo.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implemente a interface `IEventProcessor`. Substitua todo o conteúdo da classe `SimpleEventProcessor` pelo código a seguir:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualizar o método Main para usar o SimpleEventProcessor

1. Adicione as instruções `using` a seguir à parte superior do arquivo Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` da cadeia de conexão do hub de eventos, do nome do hub de eventos, do nome do contêiner da conta de armazenamento, do nome da conta de armazenamento e da chave da conta de armazenamento. Adicione o código a seguir, substituindo os espaços reservados pelos valores correspondentes:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Adicione um novo método chamado `MainAsync` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Adicione a linha de código a seguir ao método `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    O arquivo Program.cs deve ficar assim:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Execute o programa e certifique-se de que não existem erros.


## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [Exemplos de RBAC (controle de acesso baseado em função)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Esses exemplos usam a antiga biblioteca **Microsoft. Azure. EventHubs** , mas você pode atualizá-lo facilmente para usar a biblioteca **Azure. Messaging. EventHubs** mais recente. Para mover o exemplo do usando a biblioteca antiga para uma nova, consulte o [guia para migrar de Microsoft. Azure. EventHubs para Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).)
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)


