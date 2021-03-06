---
title: Armazene backups por até 10 anos
description: Saiba como o Banco de Dados SQL do Azure dá suporte para o armazenamento de backups completos do banco de dados por até 10 anos.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 15a2d58d2fc14c370c41d5454d62c74a5b66ad42
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499971"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Armazenar backups do Banco de Dados SQL do Azure por um período de até 10 anos

Muitos aplicativos têm fins regulamentares, de conformidade ou outros fins comerciais que exigem a retenção dos backups de banco de dados além dos 7 a 35 dias fornecidos pelos [backups automáticos](sql-database-automated-backups.md) do Banco de Dados SQL do Azure. Usando o recurso de retenção de longo prazo (EPD), você pode armazenar backups completos do banco de dados SQL especificado no armazenamento de BLOBs do Azure com armazenamento com redundância geográfica com acesso de leitura por até 10 anos. É possível restaurar qualquer backup como um novo banco de dados. Para obter mais informações sobre a redundância de armazenamento do Azure, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md).

> [!NOTE]
> O LTR pode ser habilitado para bancos de dados individuais e em pool. Ainda não está disponível para bancos de dados de instâncias em hospedados em Instâncias Gerenciadas. Você pode usar trabalhos do SQL Agent para agendar [backups somente cópia de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR alem de 35 dias.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longo prazo do Banco de Dados SQL

A retenção de backup de longo prazo (LTR) aproveita os backups completos do banco de dados [criados automaticamente](sql-database-automated-backups.md) para habilitar a restauração pontual (PITR). Se uma política EPD estiver configurada, esses backups serão copiados para diferentes BLOBs para armazenamento de longo prazo. A cópia é um trabalho em segundo plano que não tem impacto sobre o desempenho na carga de trabalho do banco de dados. A política EPD para cada banco de dados SQL também pode especificar com que frequência os backups EPD são criados.

Para habilitar EPD, você pode definir uma política usando uma combinação de quatro parâmetros: retenção de backup semanal (W), retenção de backup mensal (M), retenção de backup anual (Y) e semana do ano (WeekOfYear). Se você especificar W, um backup por semana será copiado para o armazenamento de longo prazo. Se você especificar M, o primeiro backup de cada mês será copiado para o armazenamento de longo prazo. Se você especificar Y, um backup durante a semana especificada por WeekOfYear será copiado para o armazenamento de longo prazo. Se o WeekOfYear especificado estiver no passado quando a política estiver configurada, o primeiro backup EPD será criado no ano seguinte. Cada backup será mantido no armazenamento de longo prazo de acordo com os parâmetros de política que são configurados quando o backup EPD é criado.

> [!NOTE]
> Qualquer alteração na política EPD se aplica somente a backups futuros. Por exemplo, se a retenção de backup semanal (W), retenção de backup mensal (M) ou retenção de backup anual (Y) for modificada, a nova configuração de retenção será aplicada somente a novos backups. A retenção de backups existentes não será modificada. Se sua intenção for excluir backups LTR antigos antes de seu período de retenção expirar, você precisará [excluir manualmente os backups](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exemplos da política EPD:

-  W=0, M=0, Y=5, WeekOfYear=3

   O terceiro backup completo de cada ano será mantido por cinco anos.
   
- W=0, M=3, Y=0

   O primeiro backup completo de cada mês será mantido por três meses.

- W=12, M=0, Y=0

   Cada backup completo semanal será mantido por 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada backup completo semanal será mantido por seis semanas. Exceto o 1º backup completo de cada mês, que será mantido por 12 meses. Exceto o backup completo realizado na 16ª semana do ano, que será mantido por 10 anos. 

A tabela a seguir ilustra a cadência e a expiração dos backups de longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![Exemplo de LTR](./media/sql-database-long-term-retention/ltr-example.png)



Se você modificar a política acima e definir W = 0 (sem backups semanais), a cadência das cópias de backup será alterada conforme mostrado na tabela acima pelas datas realçadas. A as quantidade de armazenamento necessária para manter esses backups reduziria adequadamente. 

> [!IMPORTANT]
> O tempo dos backups de LTR individuais é controlado pelo banco de dados SQL do Azure. Não é possível criar manualmente um backup EPD ou controlar o tempo de criação do backup. Depois de configurar uma política EPD, pode levar até 7 dias antes que o primeiro backup EPD seja exibido na lista de backups disponíveis.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Replicação geográfica e retenção de backup de longo prazo

Se você estiver usando grupos de failover ou replicação geográfica ativa como sua solução de continuidade de negócios, deverá se preparar para failovers eventuales e configurar a mesma política EPD no banco de dados geograficamente secundário. O custo de armazenamento EPD não aumentará conforme os backups não forem gerados dos secundários. Somente quando o secundário tornar-se primário, os backups serão criados. Ele garante a geração não-interrompida dos backups EPD quando o failover é disparado e o primário é movido para a região secundária. 

> [!NOTE]
> Quando o banco de dados primário original for recuperado de uma interrupção que causou o failover, ele se tornará um novo secundário. Portanto, a criação de backup não será retomada e a política de LTR existente não terá efeito até que torne-se primário novamente. 

## <a name="configure-long-term-backup-retention"></a>Configurar retenção de backup de longo prazo

Para saber como configurar a retenção de longo prazo usando o portal do Azure ou o PowerShell, consulte [gerenciar retenção de backup de longo prazo do banco de dados SQL do Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Restaurar banco de dados do backup EPD

Para restaurar um banco de dados do armazenamento LTR, você pode selecionar um backup específico com base no carimbo de data/hora. O banco de dados pode ser restaurado para qualquer servidor existente sob a mesma assinatura do banco de dados original. Para saber como restaurar seu banco de dados de um backup EPD, usando o portal do Azure ou o PowerShell, consulte [gerenciar retenção de backup de longo prazo do banco de dados SQL do Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Próximas etapas

Como os backups de banco de dados protegem os dados de danos ou exclusão acidental, eles são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
