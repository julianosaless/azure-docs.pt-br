---
title: azcopy doc | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d071517377053e8f4f22ad00966e2be688b0d486
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74029869"
---
# <a name="azcopy-doc"></a>azcopy doc

Gera documentação para a ferramenta no formato de redução.

## <a name="synopsis"></a>Sinopse

Gera a documentação para a ferramenta no formato de redução e as armazena no local designado.

Por padrão, os arquivos são armazenados em uma pasta chamada ' Doc ' dentro do diretório atual.

```azcopy
azcopy doc [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostra o conteúdo da ajuda para o comando doc.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
