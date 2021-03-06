---
title: Mapeando a transformação de União de fluxo de dados
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 1874e3536a3dda123c7fff9726c02e5d92d88804
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204331"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Transformação de União de fluxo de dados de mapeamento Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A União combinará vários fluxos de dados em um, com o SQL Union desses fluxos como a nova saída da transformação de União. Todo o esquema de cada fluxo de entrada será combinado dentro do fluxo de dados, sem a necessidade de ter uma chave de junção.

Você pode combinar n-Number de fluxos na tabela de configurações selecionando o ícone "+" ao lado de cada linha configurada, incluindo dados de origem, bem como fluxos de transformações existentes em seu fluxo de dados.

Aqui está uma breve explicação de vídeo sobre a transformação Union no fluxo de dados de mapeamento do ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Transformação União](media/data-flow/union.png "Union")

Nesse caso, você pode combinar metadados distintos de várias fontes (neste exemplo, três arquivos de origem diferentes) e combiná-los em um único fluxo:

![Visão geral da transformação Union](media/data-flow/union111.png "União 1")

Para conseguir isso, adicione linhas adicionais nas configurações de União, incluindo todas as fontes que você deseja adicionar. Não há necessidade de uma pesquisa comum ou chave de junção:

![Configurações de transformação de União](media/data-flow/unionsettings.png "Configurações de União")

Se você definir uma transformação selecionar após sua União, você poderá renomear campos ou campos sobrepostos que não foram nomeados de fontes sem cabeçalho. Clique em "inspecionar" para ver a combinação de metadados com o total de 132 colunas neste exemplo de três fontes diferentes:

![Transformação Union final](media/data-flow/union333.png "União 3")

## <a name="name-and-position"></a>Nome e posição

Quando você escolhe "Union by Name", cada valor de coluna será inserido na coluna correspondente de cada fonte, com um novo esquema de metadados concatenado.

Se você escolher "Union por posição", cada valor de coluna será inserido na posição original de cada fonte correspondente, resultando em um novo fluxo combinado de dados em que os dados de cada fonte são adicionados ao mesmo fluxo:

![Saída de União](media/data-flow/unionoutput.png "Saída de União")

## <a name="next-steps"></a>Próximas etapas

Explore transformações semelhantes, incluindo [Join](data-flow-join.md) e [Exists](data-flow-exists.md).
