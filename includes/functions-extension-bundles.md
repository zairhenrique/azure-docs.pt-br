---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639162"
---
A maneira mais fácil de instalar as extensões de associação é habilitar [pacotes de extensão](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Quando você habilita os pacotes, um conjunto predefinido de pacotes de extensão é instalado automaticamente.

Para habilitar pacotes de extensão, abra o arquivo host.json e atualize seu conteúdo de acordo com o código a seguir:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```