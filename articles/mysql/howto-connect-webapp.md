---
title: Conectar-se ao serviço de Azure App-banco de dados do Azure para MySQL
description: Instruções sobre como conectar corretamente um Serviço de Aplicativo do Azure ao Banco de Dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 63ef9ac55fcfaebfd58ae1ccdb34107d41900be5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770536"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Conectar um Serviço de Aplicativo do Azure existente ao Banco de Dados do Azure para MySQL server
Este tópico explica como conectar um Serviço de Aplicativo do Azure existente ao Banco de Dados do Azure para o servidor MySQL.

## <a name="before-you-begin"></a>Antes de começar
Entre no [portal do Azure](https://portal.azure.com). Criar um servidor de Banco de Dados do Azure para MySQL. Para obter detalhes, confira [Como criar o Banco de Dados do Azure para servidor MySQL por meio do Portal](quickstart-create-mysql-server-database-using-azure-portal.md) ou [Como criar o Banco de Dados do Azure para servidor MySQL usando a CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Atualmente, há duas soluções para habilitar o acesso de um Serviço de Aplicativo do Azure a um Banco de Dados do Azure para MySQL. Ambas as soluções envolvem a configuração de regras de firewall de nível de servidor.

## <a name="solution-1---allow-azure-services"></a>Solução 1 – permitir os serviços do Azure
O Banco de Dados do Azure para MySQL fornece segurança de acesso usando um firewall para proteger os dados. Ao se conectar de um Serviço de Aplicativo do Azure ao banco de dados para MySQL server, lembre-se de que os IPs de saída do Serviço de Aplicativo são dinâmicos por natureza. Escolher a opção "Permitir o acesso aos serviços do Azure" permitirá que o Serviço de Aplicativo se conecte ao servidor MySQL.

1. Na folha do servidor MySQL, no título Configurações, clique em **Segurança de conexão** para abrir a folha Segurança de conexão para o Banco de Dados do Azure para MySQL.

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-connect-webapp/1-connection-security.png)

2. Selecione **ATIVAR** em **Permitir o acesso aos serviços do Azure**, depois **Salvar**.
   ![Portal do Azure – Permitir acesso ao Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solução 2: Criar uma regra de firewall para permitir explicitamente IPs de saída
Você pode adicionar explicitamente todos os IPs de saída do Serviço de Aplicativo do Azure.

1. Na folha Propriedades do Serviço de Aplicativo, exiba o **ENDEREÇO IP DE SAÍDA**.

   ![Portal do Azure – exibir IPs de saída](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Na folha Segurança de Conexão do MySQL, adicione os IPs de saída individualmente.

   ![Portal do Azure – adicionar IPs explícitos](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Lembre-se de **Salvar** as regras de firewall.

Embora o serviço de aplicativo do Azure tente manter os endereços IP constantes ao longo do tempo, há casos em que os endereços IP podem mudar. Por exemplo, isso pode ocorrer quando o aplicativo é reciclado ou ocorre uma operação em escala, ou quando são adicionados novos computadores aos data centers regionais do Azure para aumentar a capacidade. Quando os endereços IP são alterados, o aplicativo pode apresentar tempo de inatividade, caso ele não possa mais se conectar ao servidor MySQL. Lembre-se disso ao escolher uma das soluções anteriores.

## <a name="ssl-configuration"></a>Configuração de SSL
O Banco de Dados do Azure para MySQL tem SSL habilitado por padrão. Se o aplicativo não estiver usando SSL para se conectar ao banco de dados, você precisará desabilitar o SSL no servidor MySQL. Para obter detalhes sobre como configurar o SSL, confira [Usar SSL com o Banco de Dados do Azure para MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre cadeias de conexão, confira [Cadeias de conexão](howto-connection-string.md).
