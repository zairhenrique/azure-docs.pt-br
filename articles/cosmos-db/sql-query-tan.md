---
title: TAN na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL TAN no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349169"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Retorna a tangente do ângulo especificado, em radianos, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula a tangente de PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
