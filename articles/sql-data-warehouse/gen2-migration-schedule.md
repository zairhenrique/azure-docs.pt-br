---
title: Migrar seu data warehouse para o Gen2
description: Instruções para migrar um data warehouse existente para Gen2 e o agendamento de migração por região.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019
ms.openlocfilehash: 3f793fd68c83f90b87182647eef47a07eb452f45
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314769"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Atualizar o seu data warehouse para Gen2

A Microsoft está ajudando a reduzir o custo de nível de entrada da execução de um data warehouse.  As camadas de computação mais baixas capazes de lidar com consultas exigentes agora estão disponíveis para o Azure SQL Data Warehouse. Leia o suporte de [camada de computação inferior](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)do anúncio completo para Gen2. A nova oferta está disponível nas regiões indicadas na tabela a seguir. Para regiões compatíveis, data warehouses Gen1 existentes podem ser atualizados para Gen2 por um destes processos:

- **O processo de atualização automática:** As atualizações automáticas não são iniciadas assim que o serviço está disponível em uma região.  Quando as atualizações automáticas são iniciadas em uma região específica, as atualizações individuais do data warehouse ocorrem durante o agendamento de manutenção selecionado.
- [**Atualização automática para Gen2:** ](#self-upgrade-to-gen2) Você pode controlar quando atualizar fazendo uma autoatualização para Gen2. Se sua região ainda não tiver suporte, você poderá restaurar de um ponto de restauração diretamente para uma instância do Gen2 em uma região com suporte.

## <a name="automated-schedule-and-region-availability-table"></a>Agendamento automatizado e tabela de disponibilidade na região

A tabela a seguir resume por região quando a camada de computação inferior Gen2 estará disponível e quando as atualizações automáticas serão iniciadas. As datas estão sujeitas à alteração. Consulte a tabela com frequência para saber quando a sua região estará disponível.

\* indica que um agendamento específico para a região está indisponível no momento.

| **Região** | **Gen2 inferior disponível** | **Início das atualizações automáticas** |
|:--- |:--- |:--- |
| Leste do Canadá |1º de junho de 2020 |1º de julho de 2020 |
| Leste da China |\* |\* |
| Norte da China |\* |\* |
| Alemanha Central |\* |\* |
| Centro-oeste da Alemanha |Disponível |1 de maio de 2020 |
| Oeste da Índia |Disponível |1 de maio de 2020  |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, vamos agendar atualizações automatizadas para suas instâncias do Gen1. Para evitar interrupções inesperadas de disponibilidade do data warehouse, as atualizações automatizadas serão agendadas durante o seu agendamento de manutenção. A capacidade de criar uma nova instância de Gen1 será desabilitada em regiões que passam por atualização automática para Gen2. O Gen1 será preterido depois que as atualizações automáticas forem concluídas. Para saber mais sobre agendamentos, consulte [Exibir um agendamento de manutenção](viewing-maintenance-schedule.md)

O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos o data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. No entanto, você pode enfrentar uma degradação no desempenho enquanto o processo de atualização continua a atualizar os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Você também pode agilizar o processo de atualização do arquivo de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias usando uma classe maior de SLO e recursos após a reinicialização.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Autoatualização para Gen2

Você pode optar por fazer a atualização automática seguindo estas etapas em um data warehouse Gen1 existente. Se você optar por atualizar automaticamente, deverá concluí-lo antes que o processo de atualização automática comece na sua região. Isso garante que você evite qualquer risco de atualizações automáticas causando um conflito.

Há duas opções ao realizar uma autoatualização.  Você pode atualizar o seu data warehouse atual in-loco ou restaurar um data warehouse Gen1 em uma instância Gen2.

- [Atualização in-loco](upgrade-to-latest-generation.md): essa opção atualizará o seu data warehouse Gen1 existente para Gen2. O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) à medida que reiniciamos o data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. Se você tiver problemas durante a atualização, abra uma [solicitação de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e faça referência a "atualização Gen2" como a possível causa.
- [Atualização do ponto de restauração](sql-data-warehouse-restore.md): crie um ponto de restauração definido pelo usuário no seu data warehouse Gen1 atual e, em seguida, restaure diretamente em uma instância Gen2. O data warehouse Gen1 existente permanecerá em vigor. Depois que a restauração for concluída, o seu data warehouse Gen2 estará completamente disponível para uso.  Depois de executar todos os processos de teste e validação na instância Gen2 restaurada, a instância Gen1 original pode ser excluída.

   - Etapa 1: no portal do Azure, [crie um ponto de restauração definido pelo usuário](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Etapa 2: ao restaurar de um ponto de restauração definido pelo usuário, defina o "nível de desempenho" para sua camada de Gen2 preferida.

Talvez você perceba um período de degradação no desempenho enquanto o processo continua atualizando os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Para acelerar o processo de migração de dados em segundo plano, você pode forçar imediatamente a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas de columnstore primárias que você estaria consultando em uma classe de recursos e SLO maior.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

Se houver algum problema com o data warehouse, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência à "atualização de Gen2" como a possível causa.

Para saber mais, confira [Atualizar para Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas frequentes sobre migração

**P: o Gen2 custa o mesmo que Gen1?**

- R: Sim.

**P: como as atualizações afetarão meus scripts de automação?**

- R: qualquer script de automação que faz referência a um objetivo de nível de serviço deve ser alterado para corresponder ao equivalente de Gen2.  Veja os detalhes [aqui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: quanto tempo uma autoatualização normalmente leva?**

- R: você pode atualizar no local ou atualizar de um ponto de restauração.  
   - A atualização in-loco fará com que o data warehouse seja pausado momentaneamente e retomado.  Um processo em segundo plano continuará enquanto o data warehouse estiver online.  
   - Será mais demorado se você atualizar usando um ponto de restauração, pois a atualização passará pelo processo completo de restauração.

**P: quanto tempo ocorrerá a atualização automática?**

- R: o tempo de inatividade real para a atualização é a única vez que leva para pausar e retomar o serviço, que está entre 5 e 10 minutos. Após esse breve tempo de inatividade, um processo em segundo plano executará uma migração de armazenamento. A duração do processo em segundo plano depende do tamanho do seu data warehouse.

**P: quando essa atualização automática ocorrerá?**

- R: durante o agendamento de manutenção. Aproveitar o agendamento de manutenção escolhido minimizará a interrupção nos seus negócios.

**P: o que devo fazer se meu processo de atualização em segundo plano parecer estar preso?**

 - R: disparar uma reindexação de suas tabelas Columnstore. Observe que a reindexação da tabela será offline durante essa operação.

**P: e se Gen2 não tiver o objetivo de nível de serviço que tenho em Gen1?**
- R: se você estiver executando um DW600 ou DW1200 no Gen1, é aconselhável usar DW500c ou DW1000c respectivamente, já que Gen2 fornece mais memória, recursos e melhor desempenho do que o Gen1.

**P: posso desabilitar o backup geográfico?**
- R: Não. O backup geográfico é um recurso corporativo para preservar a disponibilidade do seu data warehouse no caso de uma região se tornar indisponível. Abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) em caso de outras dúvidas.

**P: há uma diferença na sintaxe de T-SQL entre Gen1 e Gen2?**

- R: não há nenhuma alteração na sintaxe da linguagem T-SQL de Gen1 para Gen2.

**P: o Gen2 dá suporte a janelas de manutenção?**

- R: Sim.

**P: poderei criar uma nova instância do Gen1 depois que minha região for atualizada?**

- R: Não. Depois que uma região for atualizada, a criação de novas instâncias de Gen1 será desabilitada.

## <a name="next-steps"></a>Próximos passos

- [Etapas de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitoramento da integridade de recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Revise o artigo Antes de começar a migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualizar in-loco e atualizar de um ponto de restauração](upgrade-to-latest-generation.md)
- [Criar um ponto de restauração definido pelo usuário](sql-data-warehouse-restore-points.md)
- [Saiba como restaurar para Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Abrir uma solicitação de suporte para SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
