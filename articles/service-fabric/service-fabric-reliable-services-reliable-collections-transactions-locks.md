---
title: Transações e modos de bloqueio em coleções confiáveis
description: Gerenciador de estado confiável do Azure Service Fabric e Bloqueio e Transações de Coleções Confiáveis.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938921"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transações e modos de bloqueio em Coleções Confiáveis do Azure Service Fabric

## <a name="transaction"></a>Transação

Uma transação é uma sequência de operações executadas como uma única unidade lógica de trabalho. Ele exibe as propriedades [Acid](https://en.wikipedia.org/wiki/ACID) comuns (*atomicidade*, *consistência*, *isolamento*, *durabilidade*) das transações de banco de dados:

* **Atomicidade**: uma transação deve ser uma unidade atômica de trabalho. Em outras palavras, todas as suas modificações de dados são realizadas ou nenhuma delas é realizada.
* **Consistência**: quando concluída, uma transação deve deixar todos os dados em um estado consistente. Todas as estruturas de dados internos devem estar corretas ao final da transação.
* **Isolamento**: as modificações feitas por transações simultâneas devem ser isoladas das modificações feitas por quaisquer outras transações simultâneas. O nível de isolamento usado para uma operação em um [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) é determinado pelo [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) que executa a operação.
* **Durabilidade**: após uma transação ser concluída, seus efeitos ficam permanentemente vigentes no sistema. As modificações persistem até mesmo no caso de uma queda do sistema.

### <a name="isolation-levels"></a>Níveis de isolamento

Nível de isolamento define o grau no qual a transação deve ser isolada de modificações feitas por outras transações.
Há dois níveis de isolamento com suporte nas Coleções Confiáveis:

* **Leitura repetida**: especifica que as instruções não podem ler dados que foram modificados, mas ainda não foram confirmados por outras transações e que nenhuma outra transação pode modificar dados que foram lidos pela transação atual até que a transação atual seja concluída.
* **Instantâneo**: especifica que os dados lidos por qualquer instrução em uma transação são a versão transacionalmente consistente dos dados que existiam no início da transação.
  A transação pode reconhecer apenas modificações de dados que foram confirmadas antes do início da transação.
  Modificações de dados efetuadas por outras transações após o início da transação atual não são visíveis para as instruções em execução na transação atual.
  O efeito é como se as instruções em uma transação obtivessem um instantâneo dos dados confirmados conforme existiam no início da transação.
  Os instantâneos são consistentes entre as Coleções Confiáveis.

As Coleções Confiáveis escolhem automaticamente o nível de isolamento a ser usado para uma determinada operação de leitura dependendo da operação e da função da réplica no momento da criação da transação.
A seguir está a tabela que descreve os padrões de nível de isolamento para operações de Dicionário Confiável e Fila.

| Operação\função | Primária | Secundário |
| --- |:--- |:--- |
| Leitura de entidade única |Leitura repetida |Instantâneo |
| Enumeração, Contagem |Instantâneo |Instantâneo |

> [!NOTE]
> Exemplos comuns de Operações de Entidade Única são `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

O dicionário confiável e a fila confiável dão suporte à *leitura de suas gravações*.
Em outras palavras, qualquer gravação em uma transação será visível para uma leitura seguinte que pertence à mesma transação.

## <a name="locks"></a>Locks

Nas Coleções Confiáveis, todas as transações implementam um bloqueio de duas fases rigoroso: uma transação não libera os bloqueios que adquiriu até que a transação seja encerrada com uma confirmação ou anulação.

O dicionário confiável usa o bloqueio em nível de linha para todas as operações de entidade única.
Fila Confiável compensa simultaneidade para propriedade PEPS transacional estrita.
A fila confiável usa bloqueios em nível de operação, `TryPeekAsync` permitindo que uma `TryDequeueAsync` transação com e/ `EnqueueAsync` ou e uma transação seja por vez.
Observe que, para preservar PEPS, se um `TryPeekAsync` ou `TryDequeueAsync` nunca observarem que a Fila Confiável está vazia, também bloquearão `EnqueueAsync`.

Operações de gravação sempre utilizam bloqueios exclusivos.
Para operações de leitura, o bloqueio depende de alguns fatores:

- Qualquer operação de leitura feita usando o isolamento de instantâneo é sem bloqueio.
- Qualquer operação de Leitura Repetida por padrão recebe bloqueios compartilhados.
- No entanto, para qualquer operação de leitura que dá suporte à Leitura Repetida, o usuário pode solicitar um bloqueio de atualização, em vez do bloqueio compartilhado.
Um Bloqueio de atualização é um bloqueio assimétrico usado para evitar uma forma comum de deadlock que ocorre quando várias transações bloqueiam recursos para atualização potencial em um momento posterior.

A matriz de compatibilidade de bloqueio pode ser encontrada na tabela a seguir:

| Solicitação\concedida | Nenhum | Compartilhado | Atualizar | Exclusivo |
| --- |:--- |:--- |:--- |:--- |
| Compartilhado |Sem conflito |Sem conflito |Conflito |Conflito |
| Atualizar |Sem conflito |Sem conflito |Conflito |Conflito |
| Exclusivo |Sem conflito |Conflito |Conflito |Conflito |

O argumento Timeout nas APIs de coleções confiáveis é usado para detecção de deadlock.
Por exemplo, duas transações (T1 e T2) estão tentando ler e atualizar K1.
É possível que ocorra um deadlock, porque ambas acabam tendo o bloqueio compartilhado.
Nesse caso, uma ou ambas as operações atingirão o tempo limite. Nesse cenário, um bloqueio de atualização pode impedir esse deadlock.

## <a name="next-steps"></a>Próximas etapas

* [Trabalhando com Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
* [Backup e restauração do Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
* [Configuração do Gerenciador de Estado Confiável](service-fabric-reliable-services-configuration.md)
* [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
