---
title: 'Pré-processar texto: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de texto de pré-processamento em Azure Machine Learning para limpar e simplificar o texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477486"
---
# <a name="preprocess-text"></a>Pré-processar Texto

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use o módulo de **texto pré-processar** para limpar e simplificar o texto. Ele dá suporte a essas operações comuns de processamento de texto:

* Remoção de palavras irrelevantes
* Usando expressões regulares para pesquisar e substituir cadeias de caracteres de destino específicas
* Lematização, que converte várias palavras relacionadas em um único Formulário canônico
* Normalização de caso
* Remoção de determinadas classes de caracteres, como números, caracteres especiais e sequências de caracteres repetidos, como "aaaa"
* Identificação e remoção de emails e URLs

O módulo de **texto de pré-processamento** atualmente só dá suporte ao inglês.

## <a name="configure-text-preprocessing"></a>Configurar o pré-processamento de texto  

1.  Adicione o módulo de **texto de pré-processamento** ao seu pipeline no Azure Machine Learning. Você pode encontrar esse módulo em **análise de texto**.

1. Conecte um conjunto de um DataSet que tenha pelo menos uma coluna contendo texto.

1. Selecione o idioma na lista suspensa **idioma** .

1. **Coluna de texto a ser limpa**: selecione a coluna que você deseja pré-processar.

1. **Remover palavras de parada**: Selecione esta opção se desejar aplicar uma lista de palavra irrelevante predefinida à coluna de texto. 

    As listas de palavra irrelevante são dependentes de idioma e personalizáveis.

1. **Lematização**: Selecione esta opção se quiser que as palavras sejam representadas em sua forma canônica. Essa opção é útil para reduzir o número de ocorrências exclusivas de outros tokens de texto semelhantes.

    O processo lematização é dependente de linguagem altamente.

1. **Detectar sentenças**: Selecione esta opção se desejar que o módulo Insira uma marca de limite de frase ao executar a análise.

    Esse módulo usa uma série de três caracteres `|||` de pipe para representar o terminador de frase.

1. Execute operações de localização e substituição opcionais usando expressões regulares.

    * **Expressão regular personalizada**: defina o texto que você está pesquisando.
    * **Cadeia de substituição personalizada**: defina um único valor de substituição.

1. **Normalizar maiúsculas e minúsculas**: Selecione esta opção se desejar converter caracteres maiúsculos ASCII em formatos minúsculos.

    Se os caracteres não forem normalizados, a mesma palavra em letras maiúsculas e minúsculas será considerada duas palavras diferentes.

1. Você também pode remover os seguintes tipos de caracteres ou sequências de caracteres do texto de saída processado:

    * **Remover números**: Selecione esta opção para remover todos os caracteres numéricos do idioma especificado. Os números de identificação são dependentes de domínio e de idioma. Se os caracteres numéricos forem parte integrante de uma palavra conhecida, o número poderá não ser removido.
    
    * **Remover caracteres especiais**: Use esta opção para remover quaisquer caracteres especiais não alfanuméricos.
    
    * **Remover caracteres duplicados**: Selecione esta opção para remover caracteres extras em qualquer sequência que se repita para mais de duas vezes. Por exemplo, uma sequência como "AAAAA" seria reduzida para "AA".
    
    * **Remover endereços de email**: Selecione esta opção para remover qualquer sequência do formato `<string>@<string>`.  
    * **Remover URLs**: Selecione esta opção para remover qualquer sequência que inclua os seguintes prefixos de `http`URL `https`: `ftp`,,,`www`
    
1. **Expandir contratações de verbo**: essa opção se aplica somente a idiomas que usam contratações de verbo; Atualmente, somente em inglês. 

    Por exemplo, ao selecionar essa opção, você pode substituir a frase *"não estaria lá"* com *"não permaneceria lá"*.

1. **Normalizar barras invertidas para barras**: Selecione esta opção para mapear todas `\\` as `/`instâncias de para.

1. **Dividir tokens em caracteres especiais**: Selecione esta opção se desejar quebrar palavras em caracteres como `&`, `-`e assim por diante. Essa opção também pode reduzir os caracteres especiais quando ele se repetir mais de duas vezes. 

    Por exemplo, a cadeia `MS---WORD` de caracteres seria separada em três `MS`tokens `-`,, `WORD`e.

1. Envie o pipeline.

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 