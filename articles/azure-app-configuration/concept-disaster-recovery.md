---
title: Resiliência de configuração Azure App e recuperação de desastre
description: Saiba como implementar resiliência e recuperação de desastre com a configuração de Azure App.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523757"
---
# <a name="resiliency-and-disaster-recovery"></a>Resiliência e recuperação de desastre

Atualmente, a Configuração de Aplicativos do Azure é um serviço regional. Cada repositório de configuração é criado em uma determinada região do Azure. Uma interrupção em toda a região afetará todos os repositórios dessa região. A Configuração de Aplicativos não oferece failover automático para outra região. Este artigo fornece orientações gerais sobre como você pode usar vários repositórios de configuração nas regiões do Azure para aumentar a resiliência geográfica de seu aplicativo.

## <a name="high-availability-architecture"></a>Arquitetura de alta disponibilidade

Para obter redundância entre regiões, crie vários repositórios da Configuração de Aplicativos em regiões diferentes. Com essa configuração, seu aplicativo terá, pelo menos, um repositório de configurações adicional para fallback caso o repositório primário fique inacessível. O seguinte diagrama ilustra a topologia entre o aplicativo e seus repositórios de configurações primário e secundário:

![Repositórios com redundância geográfica](./media/geo-redundant-app-configuration-stores.png)

O aplicativo carrega sua configuração dos repositórios primário e secundário em paralelo. Essa ação aumenta a chance de obter dados de configuração com êxito. Você é responsável por manter os dados em ambos os repositórios sincronizados. As seções a seguir explicam como você pode criar resiliência geográfica em seu aplicativo.

## <a name="failover-between-configuration-stores"></a>Failover entre os repositórios de configuração

Tecnicamente, o aplicativo não está executando um failover. Ele está tentando recuperar o mesmo conjunto de dados de configuração de dois repositórios da Configuração de Aplicativos simultaneamente. Organize seu código de modo que ele seja carregado primeiro do repositório secundário e depois do repositório primário. Essa abordagem garante que os dados de configuração do repositório primário tenham precedência sempre que estiverem disponíveis. O trecho de código a seguir mostra como você pode implementar essa disposição no .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Observe o parâmetro `optional` passado para a função `AddAzureAppConfiguration`. Quando definido como `true`, esse parâmetro impede que a falha do aplicativo continue se a função não pode carregar os dados de configuração.

## <a name="synchronization-between-configuration-stores"></a>Sincronização entre os repositórios de configuração

É importante que os repositórios de configurações com redundância geográfica tenham todos o mesmo conjunto de dados. Use a função **Exportar** da Configuração de Aplicativos para copiar os dados do repositório primário para o secundário sob demanda. Essa função está disponível no portal e na CLI do Azure.

No portal do Azure, efetue push de uma alteração para outro repositório de configurações seguindo estas etapas.

1. Acesse a guia **Importação/Exportação** e, em seguida, selecione **Exportar** > **Configuração de Aplicativos** > **Destino** > **Selecionar um recurso**.

1. Na nova folha que é aberta, especifique a assinatura, o grupo de recursos e o nome do recurso do armazenamento secundário e, em seguida, selecione **aplicar**.

1. A interface do usuário será atualizada para que você possa escolher quais dados de configuração deseja exportar para o repositório secundário. Você pode manter o valor temporal padrão e definir **Do rótulo** e **Para o rótulo** com o mesmo valor. Escolha **Aplicar**.

1. Repita as etapas anteriores para todas as alterações de configuração.

Para automatizar esse processo de exportação, use a CLI do Azure. O seguinte comando mostra como exportar uma única alteração de configuração do repositório primário para o secundário:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como aumentar seu aplicativo para obter a resiliência geográfica durante o runtime da Configuração de Aplicativos. Você também pode inserir dados de configuração da Configuração de Aplicativos no momento do build ou da implantação. Para saber mais, confira [Integrar com o pipeline de CI/CD](./integrate-ci-cd-pipeline.md).
