---
title: Exemplo de notebooks Jupyter usando dados da NOAA
titleSuffix: Azure Open Datasets
description: Use notebooks Jupyter como exemplo para abrir Conjuntos de Dados do Abertos do Azure para aprender como carregar conjuntos de dados abertos e usá-los para aprimorar os dados de demonstração. As técnicas incluem o uso do Spark e Pandas para processar dados.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: a25f0ddeb35aaf880682780fd61bcbc3cedbad08
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930311"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Exemplo com notebooks Jupyter mostra como aprimorar os dados com conjuntos de dados abertos 
O exemplo com notebooks Jupyter para Conjunto de Dados Abertos do Azure mostra como carregar conjuntos de dados abertos e usá-los para aprimorar dados de demonstração. As técnicas incluem o uso do Apache Spark e Pandas para processar dados.

>[!IMPORTANT]
>Ao trabalhar em um ambiente que não seja Spark, os conjuntos de dados abertos permitem baixar apenas um mês de dados por vez com determinadas classes para evitar erros de memória com grandes conjuntos de dados.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Carregar dados do ISD (Banco de Dados de Superfície Integrada) do NOAA 
|Notebook        | Descrição                                    |
|----------------|------------------------------------------------|
|[Carregar um mês recente de dados climáticos em um dataframe do Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Saiba como carregar dados climáticos de históricos no seu dataframe favorito do Pandas. |
|[Carregar um mês recente de dados climáticos em um dataframe do Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Saiba como carregar dados climáticos de históricos no seu dataframe favorito do Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Unir dados de demonstração com os dados ISD do NOAA 
|Notebook        | Descrição                                    |
|----------------|------------------------------------------------|
|[Unir dados de demonstração e dados climáticos – Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Una um conjunto de dados de demonstração de 1 mês sobre locais de sensores com leituras climáticas em um dataframe do Pandas.  |
|[Unir dados de demonstração com dados climáticos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Una um conjunto de dados de demonstração mês sobre locais de sensores com leituras climáticas em um dataframe do Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Unir dados de táxis de NYC com os dados de ISD do NOAA 
|Notebook        | Descrição                                    |
|----------------|------------------------------------------------|
|[Dados de corridas de táxi aprimorados com dados climáticos – Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Carregar dados de corridas de táxis verdes de Nova York (mais de 1 mês) e aprimorá-los com dados climáticos em um dataframe do Pandas. Este exemplo substitui o método `get_pandas_limit` e equilibra o desempenho do carregamento de dados com a quantidade de dados.|
|[Dados de corridas de táxi aprimorados com dados climáticos – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Carregar dados de corridas de táxis verdes de Nova York e aprimorá-los com dados climáticos no dataframe do Spark.  |

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: modelagem de regressão com aprendizado de máquina automatizado e um conjunto de dados abertos](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [SDK do Python para conjuntos de dados abertos](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Catálogo de conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/catalog/)
