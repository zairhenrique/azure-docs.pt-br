---
title: 'Tutorial: Monitorar recursos do Azure Spring Cloud usando alertas e grupos de ações | Microsoft Docs'
description: Saiba como usar os alertas do Spring Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: 49fea7d568e356169f8bbf0dfd1f4ce5c80a7223
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690357"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Tutorial: Monitorar recursos do Spring Cloud usando alertas e grupos de ações

Os alertas do Azure Spring Cloud dão suporte ao monitoramento de recursos com base em condições como o armazenamento disponível, a taxa de solicitações ou o uso de dados. Um alerta envia uma notificação quando as taxas ou condições atendem às especificações definidas.

Há duas etapas para configurar um pipeline de alerta: 
1. Configurar um grupo de ações com as ações a serem tomadas quando um alerta for disparado, como email, SMS, runbook ou webhook. Os grupos de ações podem ser reutilizados entre diferentes alertas.
2. Configurar regras de alerta. As regras associam padrões de métrica com os grupos de ações com base no recurso de destino, métrica, condição, agregação de tempo, etc.

## <a name="prerequisites"></a>Prerequisites
Além dos requisitos do Azure Spring, este tutorial depende dos recursos a seguir.

* Uma instância do Azure Spring Cloud implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar a usar.

* Um recurso do Azure a monitorar. Este exemplo monitora uma instância do Spring Cloud.
 
Os procedimentos a seguir inicializam **Grupo de Ações** e **Alerta**, começando pela opção **Alertas** no painel de navegação esquerdo de uma instância do Spring Cloud. (O procedimento também pode ser iniciado na página **Visão Geral do Monitor** do portal do Azure.) 

Navegue de um grupo de recursos para sua instância do Spring Cloud. Selecione **Alertas** no painel esquerdo e, em seguida, selecione **Gerenciar ações**:

![Captura de tela da página do grupo de recursos do portal](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Configurar grupo de ações

Para começar o procedimento para inicializar um novo **grupo de ações**, selecione **+ Adicionar grupo de ações**.

![Captura de tela do portal Adicionar grupo de ações](media/alerts-action-groups/action-1.png)

Na página **Adicionar grupo de ações**:

 1. Especifique um **Nome do grupo de ações** e um **Nome curto**.

 1. Especifique uma **Assinatura** e um **Grupo de recursos**.

 1. Especifique **Nome da Ação**.

 1. Selecione o **Tipo de Ação**.  Isso abrirá outro painel à direita para definir a ação que será tomada na ativação.

 1. Defina a ação usando as opções no painel direito.  Este caso usa a notificação por email.

 1. Clique em **OK** no painel de ação à direita.

 1. Clique em **OK** na caixa de diálogo **Adicionar grupo de ações**. 

  ![Captura de tela de definição de ação no portal](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurar Alerta 

As etapas anteriores criaram um **grupo de ações** que usa email. Você também pode usar a notificação por telefone, WebHooks, funções do Azure, etc.  

Para configurar um **Alerta**, navegue de volta para a página **Alertas** e clique em **Gerenciar Regras de Alerta**.

  ![Captura de tela de definição de alerta no portal](media/alerts-action-groups/alerts-2.png)

1. Selecione o **Recurso** para o alerta.

1. Clique em **+ Nova regra de alerta**.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-3.png)

1. Na página **Criar regra**, especifique o **RECURSO**.

1. A configuração da **CONDIÇÃO** fornece muitas opções para monitorar seus recursos do **Spring Cloud**.  Clique em **Adicionar** para abrir o painel **Configurar lógica do sinal**.

1. Selecione uma condição. Esse exemplo usa **Percentual de uso da CPU do sistema**.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-3-1.png)

1. Role para baixo o painel **Configurar lógica do sinal** para definir o **valor limite** a monitorar.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-3-2.png)

1. Clique em **Concluído**.

Para obter detalhes sobre as condições disponíveis para monitorar, consulte as [Opções de métricas do portal do usuário](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-portal-metrics-options).

 Em **AÇÕES**, clique em **Selecionar grupo de ações**. No painel **AÇÕES**, selecione o **Grupo de Ações** definido anteriormente.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-3-3.png) 

1. Role para baixo e, em **DETALHES DO ALERTA**, dê um nome à regra de alerta.

1. Defina a **Gravidade**.

1. Clique em **Criar regra de alerta**.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-3-4.png)

Verifique se a nova regra de alerta está habilitada.

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-4.png)

Uma regra também pode ser criada usando a página **Métricas**:

   ![Captura de tela de nova regra de alerta no portal](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Próximas etapas
* [Opções de métricas do portal do usuário](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-portal-metrics-options)
* [Criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Comportamento dos alertas de SMS em grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Tutorial: Usar o Rastreamento Distribuído com o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
