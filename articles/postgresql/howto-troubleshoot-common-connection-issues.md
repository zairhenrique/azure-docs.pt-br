---
title: Solucionar problemas de conexões-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como solucionar problemas de conexão com o banco de dados do Azure para PostgreSQL-servidor único.
keywords: conexão do postgresql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c74b819893133116b8ac6905988f3fe11220ed95
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770060"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Solucionar problemas de conexão com o banco de dados do Azure para PostgreSQL-servidor único

Os problemas de conexão podem ser causados por uma variedade de coisas, incluindo:

* Configurações de Firewall
* Tempo limite da conexão
* Informações de logon incorretas
* Atingido o limite máximo em alguns recursos do Banco de Dados do Azure para PostgreSQL
* Problemas com a infraestrutura do serviço
* Manutenção executada no serviço
* A alocação de computação do servidor é alterada pelo dimensionamento do número de vCores ou pela movimentação para outra camada de serviço

Normalmente, problemas de conexão com o Banco de Dados do Azure para PostgreSQL podem ser classificados da seguinte forma:

* Erros transitórios (de curta duração ou intermitentes)
* Erros persistentes ou não transitórios (erros regularmente recorrentes)

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de erros transitórios

Quando a manutenção é executada, o sistema encontra um erro com o hardware ou software ou se você altera a camada de serviço ou vCores do seu servidor, ocorrem erros transitórios. O serviço Banco de Dados do Azure para PostgreSQL tem alta disponibilidade interna e foi projetado para atenuar esses tipos de problemas automaticamente. No entanto, seu aplicativo perde sua conexão ao servidor por um curto período de tempo de geralmente menos de 60 segundos, no máximo. Alguns eventos ocasionalmente podem levar mais tempo para serem corrigidos, como quando uma transação grande causa uma recuperação de execução longa.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporários

1. Confira o [Painel de Serviços do Microsoft Azure](https://azure.microsoft.com/status) quanto a quaisquer interrupções conhecidas que tenham ocorrido durante o tempo em que o erro foi relatado pelo aplicativo.
2. Os aplicativos que se conectam a um serviço de nuvem, como Banco de Dados do Azure para PostgreSQL, devem esperar eventos transitórios de reconfiguração periódicos e implementar lógica de repetição para lidar com esses erros, em vez de exibir esses erros como erros de aplicativo aos usuários. Examine [Tratamento de erros de conectividade transitória para o Banco de Dados do Azure para PostgreSQL](concepts-connectivity.md) para práticas recomendadas e diretrizes de design para tratar erros transitórios.
3. Conforme um servidor se aproxima dos limites de recursos, os erros podem parecer um problema de conectividade transitório. Consulte [Limitações no Banco de Dados do Azure para PostgreSQL](concepts-limits.md).
4. Se problemas de conectividade continuarem, se a duração pela qual o aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, envie uma solicitação de suporte do Azure selecionando **Obter Suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes

Se o aplicativo falhar persistentemente em se conectar ao Banco de Dados do Azure para PostgreSQL, ele normalmente indicará um problema com um dos seguintes:

* Configuração de firewall do servidor: Verifique se o Firewall do servidor do banco de dados do Azure para PostgreSQL está configurado para permitir conexões do cliente, incluindo servidores proxy e gateways.
* Configuração de firewall do cliente: o firewall em seu cliente deve permitir conexões com o servidor de banco de dados. Endereços IP e portas do servidor devem ser permitidos, bem como nomes de aplicativo, como MySQL, em alguns firewalls.
* Erro do usuário: você pode ter parâmetros de conexão digitados, como o nome do servidor na cadeia de conexão ou um sufixo ausente do *\@ServerName* no nome de usuário.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver os problemas de conectividade temporários

1. Configure as [regras de firewall](howto-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Para fins de testes temporários, configure uma regra de firewall usando 0.0.0.0 como o endereço IP inicial e usando 255.255.255.255 como o endereço IP final. Isso abrirá o servidor para todos os endereços IP. Se isso resolver seu problema de conectividade, remova essa regra e crie uma regra de firewall para um intervalo de endereçamento ou um endereço IP adequadamente limitado.
2. Em todos os firewalls entre o cliente e a Internet, verifique se a porta 5432 está aberta para conexões de saída.
3. Verifique a cadeia de conexão e outras configurações de conexão.
4. Verifique a integridade do serviço no painel. Se você achar que há uma interrupção regional, consulte [Visão geral da continuidade dos negócios com o Banco de Dados do Azure para PostgreSQL](concepts-business-continuity.md) para obter as etapas para recuperar para uma nova região.

## <a name="next-steps"></a>Próximos passos

* [Manipulação de erros de conectividade transitória para Banco de Dados do Azure para PostgreSQL](concepts-connectivity.md)
