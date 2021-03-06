---
title: Cláusula de limite de deslocamento no Azure Cosmos DB
description: Saiba como usar a cláusula de limite de deslocamento para ignorar e pegar alguns valores ao consultar em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771565"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula de limite de deslocamento no Azure Cosmos DB

A cláusula de limite de deslocamento é uma cláusula opcional para ignorar e, em seguida, pegar um número de valores da consulta. A contagem de deslocamento e a contagem de limites são necessárias na cláusula de limite de deslocamento.

Quando o limite de deslocamento é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido ao fazer a ação de ignorar e assumir os valores ordenados. Se nenhuma cláusula ORDER BY for usada, ela resultará em uma ordem determinística de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem ignorar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Comentários
  
  A `OFFSET` contagem e a `LIMIT` contagem são necessárias na `OFFSET LIMIT` cláusula. Se uma cláusula `ORDER BY` opcional for usada, o conjunto de resultados será produzido fazendo o ignorar os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores.

  A cobrança de RU de uma consulta `OFFSET LIMIT` com aumentará conforme o número de termos que estão sendo deslocados aumenta. Para consultas que têm várias páginas de resultados, geralmente recomendamos o uso de tokens de continuação. Os tokens de continuação são um "indicador" para o local onde a consulta pode ser retomada posteriormente. Se você usar `OFFSET LIMIT`, não haverá "indicador". Se você quisesse retornar a próxima página da consulta, precisaria começar desde o início.
  
  Você deve usar `OFFSET LIMIT` para casos em que deseja ignorar completamente os documentos e salvar os recursos do cliente. Por exemplo, você deve usar `OFFSET LIMIT` se quiser pular para o resultado da consulta 1000th e não precisar exibir os resultados de 1 a 999. No back-end `OFFSET LIMIT` , o ainda carrega cada documento, incluindo os ignorados. A vantagem de desempenho é uma economia nos recursos do cliente ao evitar o processamento de documentos que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (na ordem do nome da cidade residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordenação):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula ORDER BY](sql-query-order-by.md)
