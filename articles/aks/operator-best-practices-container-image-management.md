---
title: Práticas recomendadas do operador – Gerenciamento de imagem do contêiner nos Serviços de Kubernetes do Azure (AKS)
description: Conheça as práticas recomendadas de operador do cluster para saber como gerenciar e proteger imagens de contêiner no serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: cd859a4009782ca39732ec004a3d3e05edd377b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442909"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para gerenciamento de imagens de contêiner e a segurança no Serviço de Kubernetes do Azure (AKS)

Ao desenvolver e executar aplicativos no serviço de Kubernetes do Azure (AKS), a segurança de seus contêineres e imagens de contêiner é uma consideração importante. Contêineres que incluam as imagens base desatualizadas ou runtime do aplicativo sem patch apresentam um risco de segurança e um possível vetor de ataque. Para minimizar esses riscos, você deve integrar ferramentas que verificam e corrigem problemas em seus contêineres no momento da criação, bem como runtime. Quanto mais cedo no processo de vulnerabilidade ou da imagem de base desatualizada capturada, mais protegido o cluster. Neste artigo *contêineres* significa as duas as imagens de contêiner armazenadas em um registro de contêiner e os contêineres em execução.

Este artigo se concentra em como proteger seus contêineres do AKS. Você aprenderá como:

> [!div class="checklist"]
> * Verificar e corrigir as vulnerabilidades de imagem
> * Disparar e reimplantar as imagens de contêiner quando uma imagem base é atualizada automaticamente

Você também pode ler as práticas recomendadas para [segurança de cluster][best-practices-cluster-security] e segurança de [Pod][best-practices-pod-security].

Você também pode usar a [segurança de contêiner na central de segurança][security-center-containers] para ajudar a examinar seus contêineres em busca de vulnerabilidades.  Também há [integração do registro de contêiner do Azure][security-center-acr] com a central de segurança para ajudar a proteger suas imagens e o registro de vulnerabilidades.

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e tempo de execução

**Diretrizes de práticas recomendadas** – verifique suas imagens de contêiner de vulnerabilidades e só implante imagens que passaram na validação. Atualize regularmente as imagens base e o runtime do aplicativo, em seguida, implante as cargas de trabalho no cluster AKS.

Uma preocupação com a adoção de cargas de trabalho baseadas em contêiner é verificar a segurança de imagens e o runtime usado para criar seus próprios aplicativos. Como ter certeza de que você não introduziu vulnerabilidades de segurança em suas implantações? O fluxo de trabalho de implantação deve incluir um processo para verificar as imagens de contêiner usando ferramentas como [Twistlock][twistlock] ou [azul-piscina][aqua]e, em seguida, permitir que apenas as imagens verificadas sejam implantadas.

![Verifique e corrija as imagens de contêiner, valide e implante](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Em um exemplo do mundo real, você pode usar um pipeline de implantação contínua (CI/CD) e integração contínua para automatizar as verificações de imagem, verificação e implantações. O Registro de Contêiner do Azure inclui esses recursos de verificação de vulnerabilidades.

## <a name="automatically-build-new-images-on-base-image-update"></a>Crie automaticamente novas imagens na atualização da imagem base

**Diretrizes de práticas recomendadas de** - conforme você usa imagens de base para imagens de aplicativo, use a automação para criar novas imagens quando a imagem base é atualizada. Como essas imagens base geralmente incluem correções de segurança, atualize as imagens de contêiner do aplicativo downtream.

Cada vez que uma imagem base é atualizada, quaisquer imagens de contêiner downstream também devem ser atualizadas. Esse processo de compilação deve ser integrado em pipelines de validação e implantação, como [Azure pipelines][azure-pipelines] ou Jenkins. Esses pipelines certificam que seus aplicativos continuarão sendo executados nas imagens com base atualizada. Depois que suas imagens de contêiner do aplicativo são validadas, as implantações do AKS, em seguida, podem ser atualizadas para executar as imagens mais recentes e seguras.

Tarefas do Registro de Contêiner do Azure também atualizam automaticamente as imagens de contêiner quando a imagem base é atualizada. Esse recurso permite que você crie um pequeno número de imagens de base e regularmente mantê-las atualizadas com as correções de bugs e de segurança.

Para obter mais informações sobre atualizações de imagem de base, consulte [automatizar compilações de imagem na atualização da imagem de base com as tarefas do registro de contêiner do Azure][acr-base-image-update].

## <a name="next-steps"></a>Próximos passos

Este artigo se concentrou em como proteger seus contêineres. Para implementar algumas dessas áreas, confira os seguintes artigos:

* [Automatizar a criação de imagens na atualização da imagem base com as tarefas do registro de contêiner do Azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration