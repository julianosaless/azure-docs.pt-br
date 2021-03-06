---
title: Acessar dados em notebooks Jupyter-visualização de Azure Notebooks
description: Saiba como acessar arquivos, APIs REST, bancos de dados e diferentes recursos de armazenamento do Azure em um notebook Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646340"
---
# <a name="access-cloud-data-in-a-notebook"></a>Acessar dados de nuvem em um notebook

Fazer o trabalho interessante em um notebook Jupyter requer dados. Os dados, na verdade, são a alma dos notebooks.

Você pode certamente [importar arquivos de dados em um projeto](work-with-project-data-files.md), mesmo usando comandos como `curl` de dentro de um notebook para fazer download de um arquivo diretamente. No entanto, é provável, que você precise trabalhar com dados mais amplo que estão disponíveis de fontes diferentes de arquivos, como APIs REST, bancos de dados relacionais e armazenamento em nuvem, como tabelas do Azure.

Este artigo descreve brevemente essas opções diferentes. Porque o acesso a dados é melhor visto em ação, você pode encontrar o código executável nos [Exemplos do Microsoft Azure Notebooks - Acesse seus dados](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>APIs REST

Em termos gerais, a enorme quantidade de dados disponíveis da Internet é acessada, não por meio de arquivos, mas por meio das APIs de REST. Felizmente, como uma célula de notebook pode conter qualquer código que você quiser, você pode usar o código para enviar solicitações e receber dados JSON. Em seguida, você pode converter esse JSON em qualquer formato que você deseja usar como um dataframe do Pandas.

Para acessar dados usando uma API REST, use o mesmo código em células de código do bloco de anotações que você usar em qualquer outro aplicativo. A estrutura geral usando a biblioteca de solicitações é da seguinte maneira:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Bancos de Dados SQL do Azure

Você pode acessar bancos de dados do SQL Server com a ajuda das bibliotecas pyodbc ou pymssql.

[Usar Python para consultar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) fornece instruções sobre como criar um banco de dados que contém dados do AdventureWorks e mostra como consultar os dados. O mesmo código é mostrado no notebook para esse artigo.

## <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Microsoft Azure fornece vários tipos diferentes de armazenamento não relacional, dependendo do tipo de dados que você tem e como você precisa acessá-lo:

- Armazenamento de Tabelas: fornece armazenamento de baixo custo e de alto volume para dados tabulares, como logs coletados pelo sensor, logs de diagnóstico e assim por diante.
- Armazenamento de Blob: fornece armazenamento de arquivo semelhante para qualquer tipo de dados.

O notebook de exemplo demonstra o trabalho com tabelas e blobs, incluindo como usar uma assinatura de acesso compartilhado para permitir acesso somente leitura aos blobs.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

O Azure Cosmos DB fornece um repositório NoSQL totalmente indexado para documentos JSON). Os artigos a seguir fornecem uma série de maneiras diferentes de trabalhar com o Cosmos DB do Python:

- [Criar um aplicativo da API SQL com o Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Criar um aplicativo Flask com a API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Criar um banco de dados de grafo usando o Python e o Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Compilar um aplicativo Cassandra com o Python e o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Criar um aplicativo API de Tabela com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com o Cosmos DB, você pode usar a biblioteca [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/).

## <a name="other-azure-databases"></a>Outros banco de dados do Azure

O Azure fornece um número de outros tipos de banco de dados que você puder usar. Os artigos a seguir fornecem diretrizes para acessar esses bancos de dados do Python:

- [Banco de dados do Azure para PostgreSQL: usar Python para se conectar e consultar dados](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início Rápido: Como usar o Cache Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Banco de Dados do Azure para MySQL: usar Python para se conectar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Assistente de Cópia do Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Próximas etapas

- [Como trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
