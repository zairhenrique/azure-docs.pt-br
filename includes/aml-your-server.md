---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/26/2019
ms.openlocfilehash: 1260ee32c267a2652ad02e74c3978654bf2f9669
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529772"
---
1. Use as instruções em [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar o SDK do Azure Machine Learning para Python

1. Criar um [Workspace do Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Escrever um [arquivo de configuração](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clone [o repositório do GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de notebook do seu diretório clonado.

    ```shell
    jupyter notebook
    ```
