---
title: 'Exportar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo exportar dados no Azure Machine Learning para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 11f5bd7f01e142273509ae59ddc19a2557464bde
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152305"
---
# <a name="export-data-module"></a>Exportar módulo de dados

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning. 

Este módulo dá suporte à exportação de dados para os seguintes serviços de dados de nuvem:

- Contêiner de blob do Azure
- Compartilhamento de arquivos do Azure
- Azure Data Lake
- Azure Data Lake Gen2

Antes de exportar seus dados, primeiro você precisa registrar um armazenamento de um datastore em seu espaço de trabalho Azure Machine Learning. Para obter mais informações, consulte [acessar dados nos serviços de armazenamento do Azure](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **exportar dados** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria **entrada e saída** .

1. Conecte **dados de exportação** ao módulo que contém os dados que você deseja exportar.

1. Selecione **exportar dados** para abrir o painel **Propriedades** .

1. Para o **repositório de armazenamento**, selecione um repositório de armazenamento existente na lista suspensa. Você também pode criar um novo repositório de armazenamento. Verifique como [acessar dados do Access nos serviços de armazenamento do Azure](../how-to-access-data.md).

1. Defina o caminho no repositório de dados no qual gravar o dado. 


1. Para **formato de arquivo**, selecione o formato no qual os dados devem ser armazenados.
 
1. Execute o pipeline.

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
