---
title: Atualizar grupo de contêineres
description: Saiba como atualizar os contêineres em execução nos grupos de contêineres das Instâncias de Contêiner do Azure.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533311"
---
# <a name="update-containers-in-azure-container-instances"></a>Atualizar contêineres nas Instâncias de Contêiner do Azure

Durante a operação normal das instâncias de contêiner, talvez você ache necessário atualizar os contêineres em execução em um [grupo de contêineres](container-instances-container-groups.md). Por exemplo, é recomendável atualizar a versão da imagem, alterar um nome DNS, atualizar as variáveis de ambiente ou atualizar o estado de um contêiner cujo aplicativo falhou.

> [!NOTE]
> Grupos de contêineres encerrados ou excluídos não podem ser atualizados. Depois que um grupo de contêineres termina (está em um estado de êxito ou de falha) ou foi excluído, o grupo deve ser implantado como novo.

## <a name="update-a-container-group"></a>Atualizar um grupo de contêineres

Atualize os contêineres em um grupo de contêineres em execução Reimplantando um grupo existente com pelo menos uma propriedade modificada. Quando você atualiza um grupo de contêineres, todos os contêineres em execução no grupo são reiniciados no local, geralmente no mesmo host de contêiner subjacente.

Reimplante um grupo de contêiner existente, emitindo o comando criar (ou use o portal do Azure) e especifique o nome de um grupo existente. Modifique pelo menos uma propriedade válida do grupo ao emitir o comando Create para disparar a reimplantação e deixe as propriedades restantes inalteradas (ou continue a usar valores padrão). Nem todas as propriedades do grupo de contêineres são válidas para reimplantação. Consulte as [Propriedades que exigem exclusão](#properties-that-require-container-delete) para obter uma lista de propriedades sem suporte.

O exemplo da CLI do Azure a seguir atualiza um grupo de contêineres com um novo rótulo de nome DNS. Como a propriedade rótulo do nome DNS do grupo é aquela que pode ser atualizada, o grupo de contêineres é reimplantado e seus contêineres são reiniciados.

Implantação inicial com o rótulo de nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Atualize o grupo de contêineres com um novo rótulo de nome DNS, *MyApplication*e deixe as propriedades restantes inalteradas:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Benefícios da atualização

O principal benefício da atualização de um grupo de contêineres existente é a implantação mais rápida. Ao reimplantar um grupo de contêineres existente, é efetuado o pull das camadas de imagem de contêiner daquelas armazenados em cache pela implantação anterior. Em vez efetuar pull de todas as camadas de imagem novas do registro como é feito com novas implantações, somente é efetuado o pull das camadas modificadas (se houverem).

Aplicativos com base em imagens de contêiner maiores, como o Windows Server Core podem ter uma melhoria significativa na velocidade de implantação ao atualizar, em vez de excluir e implantar novos.

## <a name="limitations"></a>Limitações

Nem todas as propriedades de um grupo de contêineres dão suporte a atualizações. Para alterar algumas propriedades de um grupo de contêineres, primeiro você deve excluir e, sem seguida, reimplantar o grupo. Para obter detalhes, consulte [Propriedades que exigem exclusão de contêiner](#properties-that-require-container-delete).

Todos os contêineres em um grupo de contêineres são reiniciados quando você atualiza o grupo de contêineres. Não é possível executar uma atualização ou uma reinicialização local de um contêiner específico em um grupo de vários contêineres.

O endereço IP de um contêiner normalmente não será alterado entre as atualizações, mas não há garantia de que permaneça o mesmo. Desde que o grupo de contêineres seja implantado no mesmo host subjacente, o grupo de contêineres retém o endereço IP. Embora seja raro, e enquanto as Instâncias de Contêiner do Azure não poupam esforços para reimplantar no mesmo host, há alguns eventos internos do Azure que podem resultar na reimplantação em um host diferente. Para atenuar esse problema, sempre use um rótulo de nome DNS para as instâncias de contêiner.

Grupos de contêineres encerrados ou excluídos não podem ser atualizados. Após um grupo de contêineres ser interrompido (está no *Terminated*) ou se tiver sido excluído, o grupo será implantado como novo.

## <a name="properties-that-require-container-delete"></a>Propriedades que exigem exclusão de contêiner

Como mencionado anteriormente, nem todas as propriedades do grupo de contêineres podem ser atualizadas. Por exemplo, para alterar as portas ou reiniciar a política de um contêiner, primeiro é necessário excluir o grupo de contêineres e criá-lo novamente.

Essas propriedades exigem a exclusão do grupo de contêineres antes da reimplantação:

* Tipo do SO
* CPU
* Memória
* Política de reinicialização
* Portas

Ao excluir um grupo de contêineres e recriá-lo, ele não será "reimplantado", mas criado como novo. É efetuado o pull de todas as camadas de imagem atualizadas do registro, não aquelas armazenadas em cache por uma implantação anterior. O endereço IP do contêiner também pode ser alterado por estar sendo implantado em um host subjacente diferente.

## <a name="next-steps"></a>Próximos passos

**Grupo de contêineres** foi mencionado várias vezes neste artigo. Cada contêiner em Instâncias de Contêiner do Azure é implantado em um grupo de contêineres, sendo que grupos de contêineres podem conter mais de um contêiner.

[Grupos de contêineres em Instâncias de Contêiner do Azure](container-instances-container-groups.md)

[Implantar um grupo de vários contêineres](container-instances-multi-container-group.md)

[Parar ou iniciar os contêineres manualmente nas instâncias de contêiner do Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
