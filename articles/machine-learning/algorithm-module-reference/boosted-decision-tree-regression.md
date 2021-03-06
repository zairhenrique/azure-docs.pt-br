---
title: 'Regressão de árvore de decisão aumentada: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão da árvore de decisão aumentada no Azure Machine Learning para criar um Ensemble de árvores de regressão usando o aumento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 5298655437e04736e56193c443b8a770ea929606
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152407"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão da árvore de decisão aumentada

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para criar um Ensemble de árvores de regressão usando o aumento. O *aumento* significa que cada árvore depende de árvores anteriores. O algoritmo aprende ajustando o resíduo das árvores que o precedem. Assim, impulsionada em um ensemble de árvore de decisão tende a aumentar a precisão com um pequeno risco de menos cobertura.  
  
Esse método de regressão é um método de aprendizado supervisionado e, portanto, requer um conjunto de informações *rotulado*. A coluna de rótulo deve conter valores numéricos.  

> [!NOTE]
> Use esse módulo somente com conjuntos de dados que usam variáveis numéricas.  

Depois de definir o modelo, treine-o usando o [modelo de treinamento](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Mais sobre árvores de regressão aumentadas  

O impulsionamento é um dos vários métodos clássicos para criar modelos ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  Em Azure Machine Learning, as árvores de decisão aumentadas usam uma implementação eficiente do algoritmo de aumento de gradiente do MART. Impulsionamento de gradiente é uma técnica de aprendizado de máquina para problemas de regressão. Ele cria cada árvore de regressão por etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo na próxima. Portanto, o modelo de previsão é realmente um ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, o aumento cria uma série de árvores de uma maneira passo e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciável arbitrária.  
  
Para obter mais informações, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipédia sobre o aumento de gradiente fornece alguma experiência em árvores aumentadas. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: de RankNet para LambdaRank para LambdaMART: uma visão geral. Por J.C. Burges.

O método de impulsionamento de gradiente também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores aumentadas para tarefas de classificação, consulte [árvore de decisão aumentada de duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão da árvore de decisão aumentada

1.  Adicione o módulo **árvore de decisão aumentada** ao seu pipeline. Você pode encontrar esse módulo em **Machine Learning**, **inicializar**, na categoria **regressão** . 
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de folhas por árvore**: indica o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.  

    Aumentando este valor, você aumenta potencialmente o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.  

4. **Número mínimo de amostras por nó folha**: indique o número mínimo de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.

    Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos 5 casos que atendem as mesmas condições.

5. **Taxa de aprendizagem**: digite um número entre 0 e 1 que defina o tamanho da etapa durante o aprendizado. A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi na solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: indica o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumenta.

    Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, poderá definir o valor como 1; no entanto, apenas uma árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

7. **Semente de número aleatório**: digite um inteiro não negativo opcional para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.
  
8. **Permitir níveis categóricos desconhecidos**: Selecione esta opção para criar um grupo de valores desconhecidos nos conjuntos de treinamento e validação. Se você desmarcar essa opção, o modelo poderá aceitar somente os valores contidos nos dados de treinamento. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer previsões melhores para valores novos (desconhecidos).

9. Adicione um conjunto de uma de treinamento e um dos módulos de treinamento:

    - Se você definir a opção **criar modo de instrutor** como **parâmetro único**, use o módulo [treinar modelo](train-model.md) .  
  
    

10. Execute o pipeline.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para usar o modelo de pontuação, conecte-o ao [modelo de Pontuação](./score-model.md)para prever valores para novos exemplos de entrada.

+ Para salvar um instantâneo do modelo treinado, selecione a guia **saídas** no painel direito do **modelo treinado** e clique no ícone **registrar conjunto de registros** . A cópia do modelo treinado será salva como um módulo na árvore de módulo e não será atualizada em execuções sucessivas do pipeline.

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
