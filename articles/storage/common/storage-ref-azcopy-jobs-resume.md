---
title: retomada de trabalhos do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando retomar trabalhos do azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034151"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Retoma o trabalho existente com a ID de trabalho fornecida.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--destino-Cadeia de caracteres SAS|SAS de destino do destino para determinada JobId.|
|--excluir cadeia de caracteres|Filtro: exclua essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por '; '.|
|-h, --help|Mostra o conteúdo da ajuda para o comando retomar.|
|--incluir Cadeia de caracteres|Filtro: inclua apenas essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por '; '.|
|--origem – cadeia de caracteres SAS |SAS de origem da origem para determinada JobId.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Confira também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
