---
title: Executar contêineres privilegiados em um cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Execute contêineres privilegiados para monitorar a segurança e a conformidade.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: toa, openshift, aquasec, Twistlock, Red Hat
ms.openlocfilehash: 4241296a991283f14fbb294fdc059ecde58d6d75
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069656"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Executar contêineres privilegiados em um cluster do Azure Red Hat OpenShift

Não é possível executar contêineres com privilégios arbitrários em clusters do Azure Red Hat OpenShift.
Duas soluções de monitoramento e conformidade de segurança podem ser executadas em clusters de toa.
Este documento descreve as diferenças da documentação de implantação genérica do OpenShift dos fornecedores de produtos de segurança.


Leia estas instruções antes de seguir as instruções do fornecedor.
Os títulos de seção nas etapas específicas do produto abaixo referem-se diretamente aos títulos das seções na documentação dos fornecedores.

## <a name="before-you-begin"></a>Antes de começar

A documentação da maioria dos produtos de segurança pressupõe que você tenha privilégios de administrador de cluster.
Os administradores do cliente não têm todos os privilégios no Azure Red Hat OpenShift. As permissões necessárias para modificar os recursos de todo o cluster são limitadas.

Primeiro, verifique se o usuário está conectado ao cluster como administrador do cliente, executando `oc get scc`. Todos os usuários que são membros do grupo administrador do cliente têm permissões para exibir as restrições de contexto de segurança (SCCs) no cluster.

Em seguida, verifique se a versão binária `oc` é `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Etapas específicas do produto para segurança de água
As instruções básicas que serão modificadas podem ser encontradas na [documentação de implantação de segurança em água](https://docs.aquasec.com/docs/openshift-red-hat). As etapas aqui serão executadas em conjunto com a documentação de implantação em água.

A primeira etapa é anotar o SCCs necessário que será atualizado. Essas anotações impedem que o pod de sincronização do cluster reverte todas as alterações feitas nesses SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Etapa 1: preparar os pré-requisitos
Lembre-se de fazer logon no cluster como um administrador de cliente de toa em vez da função de administrador de cluster.

Crie o projeto e a conta de serviço.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Em vez de atribuir a função de leitor de cluster, atribua a função Customer-admin-cluster à conta azul com o comando a seguir.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continue seguindo as instruções restantes na etapa 1.  Essas instruções descrevem a configuração do segredo para o registro em azul-piscina.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Etapa 2: implantar o servidor, o banco de dados e o gateway de água
Siga as etapas fornecidas na documentação da água para instalar a água-console. YAML.

Modifique o `aqua-console.yaml`fornecido.  Remova os dois primeiros objetos rotulados, `kind: ClusterRole` e `kind: ClusterRoleBinding`.  Esses recursos não serão criados, pois o administrador do cliente não tem permissão no momento para modificar `ClusterRole` e `ClusterRoleBinding` objetos.

A segunda modificação será a parte `kind: Route` da `aqua-console.yaml`. Substitua o seguinte YAML para o objeto `kind: Route` no arquivo `aqua-console.yaml`.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga as instruções restantes.

### <a name="step-3-login-to-the-aqua-server"></a>Etapa 3: fazer logon no servidor de azul-piscina
Esta seção não é modificada de nenhuma forma.  Siga a documentação da água.

Use o comando a seguir para obter o endereço do console em água.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Etapa 4: implantar os executores de água
Defina os seguintes campos ao implantar os imforçadores:

| Campo          | Valor         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | água-conta  |
| Project        | água-segurança |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Etapas específicas do produto para prisma Cloud/Twistlock

As instruções básicas que vamos modificar podem ser encontradas na [documentação de implantação do prisma Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Comece criando um novo projeto OpenShift
```
oc new-project twistlock
```

Você pode seguir a documentação até a seção "instalar console", usar o registro de contêiner do prisma Cloud em vez de criar um interno.

### <a name="install-console"></a>Instalar console

Durante `oc create -f twistlock_console.yaml` na etapa 2, você receberá um erro ao criar o namespace.
Você pode ignorá-lo com segurança, o namespace foi criado anteriormente com o comando `oc new-project`.

### <a name="create-an-external-route-to-console"></a>Criar uma rota externa para o console

Você pode seguir a documentação ou as instruções abaixo se preferir o comando OC.
Copie a seguinte definição de rota para um arquivo chamado twistlock_route. YAML em seu computador
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
em seguida, execute:
```
oc create -f twistlock_route.yaml
```

Você pode obter a URL atribuída ao console do Twistlock com este comando: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configurar console

Siga a documentação do Twistlock.

### <a name="install-defender"></a>Instalar o defender

Durante `oc create -f defender.yaml` na etapa 2, você receberá erros ao criar a função de cluster e a associação de função de cluster.
Você pode ignorá-los.

Os defensores serão implantados somente em nós de computação. Você não precisa limitá-los com um seletor de nó.
