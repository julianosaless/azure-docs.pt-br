---
title: Enriquecimento incremental (visualização)
titleSuffix: Azure Cognitive Search
description: Conteúdo intermediário de cache e alterações incrementais do pipeline de enriquecimento de AI no armazenamento do Azure para preservar investimentos em documentos processados existentes. Esse recurso está atualmente em visualização pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024136"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introdução ao Caching e enriquecimento incremental no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

A sofisticação incremental adiciona cache e monitoração de estado a um pipeline de enriquecimento, preservando seu investimento na saída existente, ao mesmo tempo em que altera apenas os documentos afetados por determinada modificação. Isso não apenas preserva seu investimento monetário em processamento (em particular, processamento de OCR e imagens), mas também torna um sistema mais eficiente. Quando estruturas e conteúdo são armazenados em cache, um indexador pode determinar quais habilidades foram alteradas e executar apenas aquelas que foram modificadas, bem como quaisquer habilidades dependentes de downstream. 

## <a name="indexer-cache"></a>Cache do indexador

O enriquecimento incremental adiciona um cache ao pipeline de enriquecimento. O indexador armazena em cache os resultados de quebra de documento mais as saídas de cada habilidade para cada documento. Quando um conjunto de habilidades é atualizado, somente as habilidades alteradas ou downstream são executadas novamente. Os resultados atualizados são gravados no cache e o documento é atualizado no índice de pesquisa ou na loja de conhecimento.

Fisicamente, o cache é armazenado em um contêiner de BLOB em sua conta de armazenamento do Azure. O cache também usa o armazenamento de tabela para um registro interno de atualizações de processamento. Todos os índices em um serviço de pesquisa podem compartilhar a mesma conta de armazenamento para o cache do indexador. Cada indexador recebe um identificador de cache exclusivo e imutável para o contêiner que está usando.

## <a name="cache-configuration"></a>Configuração do cache

Você precisará definir a propriedade `cache` no indexador para iniciar o beneficiando do enriquecimento incremental. O exemplo a seguir ilustra um indexador com Caching habilitado. Partes específicas dessa configuração são descritas nas seções a seguir. Para obter mais informações, consulte [Configurar o enriquecimento incremental](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Definir essa propriedade em um indexador existente exigirá que você redefina e execute novamente o indexador, o que fará com que todos os documentos na fonte de dados sejam processados novamente. Essa etapa é necessária para eliminar todos os documentos aprimorados pelas versões anteriores do Configurador de habilidades. 

## <a name="cache-management"></a>Gerenciamento de cache

O ciclo de vida do cache é gerenciado pelo indexador. Se a `cache` Propriedade no indexador for definida como nula ou a cadeia de conexão for alterada, o cache existente será excluído na próxima execução do indexador. O ciclo de vida do cache também está vinculado ao ciclo de vida do indexador. Se um indexador for excluído, o cache associado também será.

Embora o aperfeiçoamento incremental seja projetado para detectar e responder a alterações sem intervenção de sua parte, há parâmetros que podem ser usados para substituir os comportamentos padrão:

+ Priorizar novos documentos
+ Ignorar verificações de conferentes
+ Ignorar verificações da fonte de dados
+ Forçar avaliação do conforçador de habilidades

### <a name="prioritize-new-documents"></a>Priorizar novos documentos

Defina a `enableReprocessing` propriedade para controlar o processamento de documentos de entrada já representados no cache. Quando `true` (padrão), os documentos que já estão no cache são reprocessados quando você executa novamente o indexador, supondo que sua atualização de habilidades afete esse documento. 

Quando `false`, os documentos existentes não são reprocessados, priorizando efetivamente o conteúdo novo e recebido sobre o conteúdo existente. Você só deve definir `enableReprocessing` como `false` em uma base temporária. Para garantir a consistência em todo o `enableReprocessing` corpus, `true` deve ser a maior parte do tempo, garantindo que todos os documentos, novos e existentes, sejam válidos de acordo com a definição atual do Configurador de qualificações.

### <a name="bypass-skillset-evaluation"></a>Ignorar avaliação do contorno de habilidades

A modificação de um conjunto de qualificações e o reprocessamento desse conjunto de qualificações normalmente se encontram em mãos. No entanto, algumas alterações em um conjunto de qualificações não devem resultar em reprocessamento (por exemplo, implantação de uma habilidade personalizada em um novo local ou com uma nova chave de acesso). Provavelmente, essas são modificações periféricas que não têm nenhum impacto original sobre a substância do próprio próprio configurador. 

Se você sabe que uma mudança para o conjunto de qualificações é realmente superficial, você deve substituir a avaliação do conconjunto de qualificações definindo o `disableCacheReprocessingChangeDetection` parâmetro como `true`:

1. Chame Update Skillble e modifique a definição do conskillr.
1. Acrescente o `disableCacheReprocessingChangeDetection=true` parâmetro na solicitação.
1. Envie a alteração.

A definição desse parâmetro garante que apenas as atualizações da definição do conjunto de qualificações sejam confirmadas e a alteração não seja avaliada quanto a efeitos no Corpus existente.

O exemplo a seguir mostra uma solicitação Updateset de atualização com o parâmetro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Ignorar verificações de validação da fonte de dados

A maioria das alterações em uma definição de fonte de dados invalidará o cache. No entanto, para cenários em que você sabe que uma alteração não deve invalidar o cache, como alterar uma cadeia de conexão ou girar a chave na conta de armazenamento,`ignoreResetRequirement` acrescente o parâmetro na atualização da fonte de dados. Definir esse parâmetro como `true` permite que a confirmação Continue, sem disparar uma condição de redefinição que resultaria em todos os objetos sendo recriados e populados a partir do zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Forçar avaliação do conforçador de habilidades

A finalidade do cache é evitar o processamento desnecessário, mas suponha que você faça uma alteração em uma habilidade que o indexador não detecta (por exemplo, alterando algo no código externo, como uma habilidade personalizada).

Nesse caso, você pode usar as [habilidades de redefinição](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) para forçar o reprocessamento de uma determinada habilidade, incluindo quaisquer habilidades de downstream que tenham uma dependência na saída dessa habilidade. Essa API aceita uma solicitação POST com uma lista de habilidades que devem ser invalidadas e marcadas para reprocessamento. Após a redefinição das habilidades, execute o indexador para invocar o pipeline.

## <a name="change-detection"></a>Detecção de alteração

Depois de habilitar um cache, o indexador avalia as alterações na composição do pipeline para determinar qual conteúdo pode ser reutilizado e que exige reprocessamento. Esta seção enumera as alterações que invalidam o cache de forma totalmente seguida por alterações que disparam o processamento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Alterações que invalidam o cache

Uma alteração que causa invalidação faz com que o cache inteiro deixe de ser válido. Um exemplo de alteração que causa invalidação é aquela em que a fonte de dados é atualizada. Esta é a lista completa de alterações que invalidarão seu cache:

* Alteração do tipo de fonte de dados
* Alteração do contêiner de fonte de dados
* Credenciais da fonte de dados
* Política de detecção de alteração da fonte de dados
* Política de detecção de exclusão da fonte de dados
* Mapeamentos de campo do indexador
* Parâmetros do indexador
    * Modo de análise
    * Extensões de nome de arquivo excluídas
    * Extensões de nome de arquivo indexadas
    * Indexar metadados de armazenamento somente para documentos excessivamente grandes
    * Cabeçalhos de texto delimitado
    * Delimitador de texto delimitado
    * Raiz do documento
    * Ação de imagem (alterações de como as imagens são extraídas)

### <a name="changes-that-trigger-incremental-processing"></a>Alterações que disparam o processamento incremental

O processamento incremental avalia sua definição de qualificações e determina quais habilidades executar novamente, atualizando seletivamente as partes afetadas da árvore de documentos. Aqui está a lista completa de alterações que resultam em enriquecimento incremental:

* A habilidade no conjunto de habilidades tem um tipo diferente. O tipo odata da habilidade é atualizado
* Atualização de parâmetros específicos da habilidade, por exemplo, a URL, padrões ou outros parâmetros
* Alterações nas saídas da habilidade, a habilidade retorna saídas adicionais ou diferentes
* Atualizações na habilidade que levam a ancestrais diferentes, o encadeamento de habilidades mudou, ou seja, entradas de habilidades
* Qualquer invalidação de habilidade upstream, se uma habilidade que fornece uma entrada para essa habilidade for atualizada
* Atualizações no local de projeção do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nas projeções do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nos mapeamentos do campo de saída de um indexador resultam na reprojeção de documentos para o índice

## <a name="api-reference"></a>Reference API

A versão `2019-05-06-Preview` da API REST fornece aprimoramentos incrementais por meio de propriedades adicionais em indexadores, habilidades e fontes de dados. Além da documentação de referência, consulte [Configurar o Caching para aprimoramento incremental](search-howto-incremental-index.md) para obter detalhes sobre como chamar as APIs.

+ [Criar indexador (versão da API = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Atualizar indexador (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Atualizar o qualificable (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (novo parâmetro Uri na solicitação)

+ [Redefinir habilidades (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indexadores de banco de dados (SQL do Azure, Cosmos DB). Alguns indexadores recuperam dados por meio de consultas. Para consultas que recuperam dados, a [fonte de dados de atualização](https://docs.microsoft.com/rest/api/searchservice/update-data-source) dá suporte a um novo parâmetro em uma solicitação **ignoreResetRequirement**, que deve ser definida como `true` quando a ação de atualização não deve invalidar o cache. 

  Use o **ignoreResetRequirement** com moderação, pois isso pode levar à inconsistência indesejada em seus dados que não serão detectados com facilidade.

## <a name="next-steps"></a>Próximas etapas

O aprimoramento incremental é um recurso poderoso que estende o controle de alterações para o habilidades e o enriquecimento de ia. O enriquecimento de AIncremental permite a reutilização de conteúdo processado existente à medida que você itera sobre o design do skillset.

Como uma próxima etapa, habilite o Caching em um indexador existente ou adicione um cache ao definir um novo indexador.

> [!div class="nextstepaction"]
> [Configurar o Caching para enriquecimento incremental](search-howto-incremental-index.md)
