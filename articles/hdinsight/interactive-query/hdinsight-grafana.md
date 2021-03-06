---
title: Usar Grafana no Microsoft Azure HDInsight
description: Saiba como acessar o painel do Grafana com clusters de Apache Hadoop no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 6027978b50ca72de5d18ff474b36814e22a94e85
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552209"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Acessar Grafana no Microsoft Azure HDInsight

[Grafana](https://grafana.com/) é um popular construtor de gráficos e painéis de código aberto. O Grafana é rico; não apenas permite que os usuários criem painéis personalizáveis e compartilha´veis, mas também oferece painéis de amostra/com script, integração LDAP, fontes de dados múltiplas e muito mais.

Atualmente, no Azure HDInsight, o Grafana tem suporte com os tipos de cluster HBase, Kafka e consulta interativa. Não há suporte para clusters com o Enterprise Security Pack habilitado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster do Apache Hadoop

Consulte [criar Apache Hadoop clusters usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Para **tipo de cluster**, selecione **Kafka**, **HBase**ou **consulta interativa**.

## <a name="access-the-grafana-dashboard"></a>Acessar o painel do Grafana

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/grafana/` em que CLUSTERname é o nome do cluster.

1. Insira as credenciais de usuário do cluster Hadoop.

1. A aparência do painel do Grafana é semelhante a este exemplo:

    ![Painel da Web do HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Painel do HDInsight Grafana")

## <a name="clean-up-resources"></a>Limpar os recursos

Se não for continuar a usar este aplicativo, exclua o cluster que criou seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.

1. Selecione **Clusters do HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparece, selecione o **...** ao lado do cluster que você criou.

1. Selecione **Excluir**. Selecione **Sim** na barra superior.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como analisar dados com o HDInsight, consulte os seguintes artigos:

* [Use Apache Hive com o HDInsight](../hadoop/hdinsight-use-hive.md).

* [Use o MapReduce com o HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Introdução ao uso das ferramentas do Hadoop do Visual Studio para HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
