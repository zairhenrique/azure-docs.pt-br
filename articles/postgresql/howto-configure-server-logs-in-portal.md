---
title: Gerenciar logs-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como configurar e acessar os logs do servidor (arquivos. log) no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763677"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurar e acessar o banco de dados do Azure para PostgreSQL-logs de servidor único do portal do Azure

Você pode configurar, listar e baixar os [logs do banco de dados do Azure para PostgreSQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha o [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta e de erros. 

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. 

   ![Captura de tela de opções de logs do servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **clique aqui para habilitar logs e configurar parâmetros de log**.

5. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo.

   Depois de alterar os parâmetros, selecione **salvar**. Ou você pode descartar suas alterações. 

   ![Captura de tela de opções de parâmetros de servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na página **parâmetros do servidor** , você pode retornar à lista de logs fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Após o início do log, você pode exibir uma lista de logs disponíveis e baixar arquivos de log individuais. 

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. A página mostra uma lista de seus arquivos de log.

   ![Captura de tela da página de logs do servidor, com a lista de logs realçada](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **postgresql-yyyy-mm-dd_hh0000.log**. A data e a hora usadas no nome de arquivo são a hora em que o log foi emitido. Os arquivos de log são girados a cada hora ou 100 MB, o que ocorrer primeiro.

4. Se necessário, use a caixa de pesquisa para restringir rapidamente um log específico, com base na data e hora. A pesquisa busca o nome do log.

   ![Captura de tela da página logs do servidor, com a caixa de pesquisa e os resultados realçados](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Para baixar arquivos de log individuais, selecione o ícone de seta para baixo ao lado de cada arquivo de log na linha da tabela.

   ![Captura de tela da página de logs do servidor, com o ícone de seta para baixo realçado](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Próximos passos
- Consulte [logs do servidor de acesso na CLI](howto-configure-server-logs-using-cli.md) para saber como baixar logs programaticamente.
- Saiba mais sobre [os logs de servidor](concepts-server-logs.md) no banco de dados do Azure para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetro e log do PostgreSQL, consulte a documentação do PostgreSQL sobre [relatório de erros e registro em log](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

