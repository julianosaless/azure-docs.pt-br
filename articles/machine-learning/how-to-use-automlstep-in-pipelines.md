---
title: Usar ML automatizado em pipelines ML
titleSuffix: Azure Machine Learning
description: O AutoMLStep permite que você use o aprendizado de máquina automatizado em seus pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 04/28/2020
ms.openlocfilehash: 9bf17512d0b14c7106101d98598e2914020afc7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857943"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Usar o ML automatizado em um pipeline de Azure Machine Learning no Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O recurso ML automatizado do Azure Machine Learning ajuda a descobrir modelos de alto desempenho sem a necessidade de reimplementar todas as abordagens possíveis. Combinado com pipelines de Azure Machine Learning, você pode criar fluxos de trabalho implantáveis que podem descobrir rapidamente o algoritmo que funciona melhor para seus dados. Este artigo mostrará como unir com eficiência uma etapa de preparação de dados a uma etapa de ML automatizada. O ML automatizado pode descobrir rapidamente o algoritmo que funciona melhor para seus dados, enquanto coloca você no caminho de MLOps e operacionalização do ciclo de vida do modelo com pipelines.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um Workspace do Azure Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).  

* Familiaridade básica com o [aprendizado de máquina automatizado](concept-automated-ml.md) do Azure e recursos de [pipeline de aprendizado de máquina](concept-ml-pipelines.md) e SDK.

## <a name="review-automated-mls-central-classes"></a>Examinar as classes centrais do ML automatizado

O ML automatizado em um pipeline é representado `AutoMLStep` por um objeto. A classe `AutoMLStep` é uma subclasse de `PipelineStep`. Um grafo de `PipelineStep` objetos define um `Pipeline`.

Há várias subclasses de `PipelineStep`. Além do `AutoMLStep`, este artigo mostrará um para a `PythonScriptStep` preparação de dados e outro para registrar o modelo.

A maneira preferida de _mover dados inicialmente para um_ pipeline ml é com `Dataset` objetos. Para mover dados _entre_ etapas, a maneira preferida é com `PipelineData` objetos. Para ser usado com `AutoMLStep`o, `PipelineData` o objeto deve ser transformado em um `PipelineOutputTabularDataset` objeto. Para obter mais informações, consulte [dados de entrada e saída de pipelines de ml](how-to-move-data-in-out-of-pipelines.md).

O `AutoMLStep` é configurado por meio `AutoMLConfig` de um objeto. `AutoMLConfig`é uma classe flexível, conforme discutido em [Configurar experimentos de ml automatizados no Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings). 

Um `Pipeline` é executado em `Experiment`um. O pipeline `Run` tem, para cada etapa, um filho `StepRun`. As saídas do ML `StepRun` automatizado são as métricas de treinamento e o modelo de maior desempenho.

Para tornar as coisas concretas, este artigo cria um pipeline simples para uma tarefa de classificação. A tarefa está prevendo a sobrevivência Titanic, mas não discutiremos os dados ou a tarefa, exceto na passagem.

## <a name="get-started"></a>Introdução

### <a name="retrieve-initial-dataset"></a>Recuperar conjunto de conjuntos inicial

Geralmente, um fluxo de trabalho de am começa com dados de linha de base pré-existentes. Esse é um bom cenário para um conjunto de um DataSet registrado. Os conjuntos de valores são visíveis no espaço de trabalho, no controle de versão de suporte e podem ser explorados interativamente. Há muitas maneiras de criar e popular um conjunto de um DataSet, conforme discutido em [criar conjuntos de Azure Machine Learning](how-to-create-register-datasets.md)de conjunto de valores. Como usaremos o SDK do Python para criar nosso pipeline, uso o SDK para baixar dados de linha de base e registrá-los com o nome ' titanic_ds '.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

O código primeiro faz logon no espaço de trabalho Azure Machine Learning definido em **config. JSON** (para obter uma explicação, consulte [tutorial: introdução à criação de seu primeiro experimento do ml com o SDK do Python](tutorial-1st-experiment-sdk-setup.md)). Se já não houver um conjunto de `'titanic_ds'` um DataSet chamado registrado, ele criará um. O código baixa dados CSV da Web, usa-os para instanciar `TabularDataset` um e, em seguida, registra o DataSet com o espaço de trabalho. Por fim, a `Dataset.get_by_name()` função atribui o `Dataset` para `titanic_ds`. 

### <a name="configure-your-storage-and-compute-target"></a>Configurar seu armazenamento e o destino de computação

Recursos adicionais que serão necessários para o pipeline são o armazenamento e, geralmente, Azure Machine Learning recursos de computação. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

Os dados intermediários entre a preparação de dados e a etapa de ML automatizada podem ser armazenados no repositório de armazenamento padrão do espaço de trabalho, portanto, não `get_default_datastore()` precisamos fazer `Workspace` mais do que chamar no objeto. 

Depois disso, o código verifica se o destino `'cpu-cluster'` de computação AML já existe. Caso contrário, especificamos que queremos um pequeno destino de computação baseado em CPU. Se você planeja usar os recursos de aprendizado profundo do ML automatizado (por exemplo, personalização de texto com suporte a DNN), deverá escolher uma computação com suporte de GPU forte, conforme descrito em [tamanhos de máquina virtual com otimização de GPU](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu). 

O código é bloqueado até que o destino seja provisionado e, em seguida, imprima alguns detalhes do destino de computação recém-criado. Por fim, o destino de computação nomeado é recuperado do espaço de trabalho `compute_target`e atribuído a. 

### <a name="configure-the-training-run"></a>Configurar a execução de treinamento

A próxima etapa é garantir que a execução de treinamento remoto tenha todas as dependências exigidas pelas etapas de treinamento. As dependências e o contexto de tempo de execução são definidos `RunConfiguration` criando e configurando um objeto. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
aml_run_config.environment.python.user_managed_dependencies = False

# Add some packages relied on by data prep step
aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
    conda_packages=['pandas','scikit-learn'], 
    pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)
```

## <a name="prepare-data-for-automated-machine-learning"></a>Preparar dados para o aprendizado de máquina automatizado

### <a name="write-the-data-preparation-code"></a>Gravar o código de preparação de dados

O conjunto de dados Titanic de linha de base consiste em um texto numérico misto e com alguns valores ausentes. Para prepará-lo para o Machine Learning automatizado, a etapa de pipeline de preparação de dados irá:

- Preencher dados ausentes com dados aleatórios ou uma categoria correspondente a "desconhecido"
- Transformar dados categóricos em inteiros
- Remover colunas que não pretendem usar
- Dividir os dados em conjuntos de treinamento e teste
- Gravar os dados transformados `PipelineData` nos caminhos de saída

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

O trecho de código acima é um exemplo completo, mas mínimo, de preparação de dados para os dados de Titanic. O trecho começa com um Jupyter "comando mágico" para gerar o código em um arquivo. Se você não estiver usando um notebook Jupyter, remova essa linha e crie o arquivo manualmente.

As várias `prepare_` funções no trecho acima modificam a coluna relevante no conjunto de dados de entrada. Essas funções funcionam nos dados depois de serem alterados em um `DataFrame` objeto pandas. Em cada caso, os dados ausentes são preenchidos com dados aleatórios representativos ou dados categóricos indicando "desconhecido". Dados categóricos baseados em texto são mapeados para números inteiros. Colunas não mais necessárias são substituídas ou descartadas. 

Depois que o código define as funções de preparação de dados, o código analisa o argumento de entrada, que é o caminho para o qual desejamos escrever nossos dados. (Esses valores serão determinados por `PipelineData` objetos que serão discutidos na próxima etapa.) O código recupera o registrado `'titanic_cs'` `Dataset`, converte-o em um pandas `DataFrame`e chama as várias funções de preparação de dados. 

Como o `output_path` é totalmente qualificado, a função `os.makedirs()` é usada para preparar a estrutura do diretório. Neste ponto, você pode usar `DataFrame.to_csv()` para gravar os dados de saída, mas os arquivos parquet são mais eficientes. Essa eficiência provavelmente seria irrelevante com um conjunto de informações pequeno, mas usar **PyArrow** o pacote PyArrow `from_pandas()` e `write_table()` as funções são apenas mais alguns pressionamentos de teclas `to_csv()`do que.

Os arquivos parquet têm suporte nativo na etapa de ML automatizada discutida abaixo, portanto, nenhum processamento especial é necessário para consumi-los. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Gravar a etapa do pipeline de preparação`PythonScriptStep`de dados ()

O código de preparação de dados descrito acima deve ser associado `PythonScripStep` a um objeto a ser usado com um pipeline. O caminho para o qual a saída de preparação de dados parquet é gravada é `PipelineData` gerado por um objeto. Os recursos preparados anteriormente, como `ComputeTarget`, `RunConfig`e `'titanic_ds' Dataset` são usados para concluir a especificação.

```python
from azureml.pipeline.core import PipelineData
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```

O `prepped_data_path` objeto é do tipo `PipelineOutputFileDataset`. Observe que ele é especificado nos argumentos `arguments` e. `outputs` Se você examinar a etapa anterior, verá que, dentro do código de preparação de dados, o valor do argumento `'--output_path'` será o caminho do arquivo para o qual o arquivo parquet foi gravado. 

## <a name="train-with-automlstep"></a>Treinar com o AutoMLStep

A configuração de uma etapa de pipeline de ML automatizada é feita com a `AutoMLConfig` classe. Essa classe flexível é descrita em [Configurar experimentos de ml automatizados no Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Entrada e saída de dados são os únicos aspectos da configuração que exigem atenção especial em um pipeline de ML. A entrada e a `AutoMLConfig` saída para o em pipelines são discutidas em detalhes abaixo. Além dos dados, uma vantagem dos pipelines de ML é a capacidade de usar diferentes destinos de computação para diferentes etapas. Você pode optar por usar um mais poderoso `ComputeTarget` somente para o processo de ml automatizado. Fazer isso é tão simples quanto atribuir um mais poderoso `RunConfiguration` ao parâmetro do `AutoMLConfig` `run_configuration` objeto.

### <a name="send-data-to-automlstep"></a>Enviar dados para`AutoMLStep`

Em um pipeline de ML, os dados de entrada devem `Dataset` ser um objeto. A maneira de desempenho mais alto é fornecer os dados de entrada na forma de `PipelineOutputTabularDataset` objetos. Você cria um objeto desse tipo com `parse_parquet_files()` o ou `parse_delimited_files()` em um `PipelineOutputFileDataset`, como o `prepped_data_path` objeto.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

O trecho acima cria uma alta execução `PipelineOutputTabularDataset` a `PipelineOutputFileDataset` partir da saída da etapa de preparação de dados.

Outra opção é usar `Dataset` objetos registrados no espaço de trabalho:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Comparando as duas técnicas:

| Técnica |  | 
|-|-|
|`PipelineOutputTabularDataset`| Maior desempenho | 
|| Rota natural de`PipelineData` | 
|| Os dados não são persistidos após a execução do pipeline |
|| [Laptop mostrando `PipelineOutputTabularDataset` técnica](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registrados`Dataset` | Desempenho inferior |
| | Pode ser gerado de várias maneiras | 
| | Os dados persistem e ficam visíveis em todo o espaço de trabalho |
| | [Bloco de anotações `Dataset` mostrando a técnica registrada](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)

### <a name="specify-automated-ml-outputs"></a>Especificar saídas de ML automatizadas

As saídas das `AutoMLStep` são as pontuações de métricas finais do modelo de maior desempenho e o próprio modelo. Para usar essas saídas em etapas de pipeline adicionais, `PipelineData` Prepare objetos para recebê-las.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                           datastore=datastore,
                           pipeline_output_name='metrics_output',
                           training_output=TrainingOutput(type='Metrics'))
model_data = PipelineData(name='best_model_data',
                           datastore=datastore,
                           pipeline_output_name='model_output',
                           training_output=TrainingOutput(type='Model'))
```

O trecho acima cria os dois `PipelineData` objetos para as métricas e a saída do modelo. Cada um é nomeado, atribuído ao repositório de armazenamento padrão recuperado anteriormente e associado com o específico `type` de `TrainingOutput` do `AutoMLStep`. Como atribuímos `pipeline_output_name` esses `PipelineData` objetos, seus valores estarão disponíveis não apenas da etapa de pipeline individual, mas do pipeline como um todo, como serão discutidos abaixo na seção "examinar os resultados do pipeline". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Configurar e criar a etapa de pipeline do ML automatizado

Depois que as entradas e saídas são definidas, é hora de criar `AutoMLConfig` e. `AutoMLStep` Os detalhes da configuração dependerão de sua tarefa, conforme descrito em [Configurar experimentos de ml automatizados em Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Para a tarefa de classificação Titanic sobrevivência, o trecho a seguir demonstra uma configuração simples.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    allow_reuse=True)
```
O trecho mostra um idioma comumente usado com `AutoMLConfig`. Os argumentos que são mais fluidos (hiperparâmetro-ish) são especificados em um dicionário separado, enquanto os valores menos prováveis de serem alterados são `AutoMLConfig` especificados diretamente no construtor. Nesse caso, `automl_settings` especifique uma breve execução: a execução será interrompida após apenas 2 iterações ou 15 minutos, o que ocorrer primeiro.

O `automl_settings` dicionário é passado para o `AutoMLConfig` Construtor como kwargs. Os outros parâmetros não são complexos:

- `task`é definido como `classification` para este exemplo. Outros valores válidos são `regression` e`forecasting`
- `path`e `debug_log` descrevem o caminho para o projeto e um arquivo local no qual as informações de depuração serão gravadas 
- `compute_target`é o anteriormente definido `compute_target` , neste exemplo, é um computador baseado em CPU de baixo custo. Se você estiver usando os recursos de aprendizado profundo do AutoML, você desejaria alterar o destino de computação para ser baseado em GPU
- `featurization` está definido como `auto`. Mais detalhes podem ser encontrados na seção [Data personalização](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) do documento de configuração do ml automatizado 
- `training_data`é definido para os `PipelineOutputTabularDataset` objetos feitos das saídas da etapa de preparação de dados 
- `label_column_name`indica em qual coluna estamos interessados em prever 

A `AutoMLStep` si mesma usa `AutoMLConfig` e tem, como saídas, os `PipelineData` objetos criados para manter as métricas e os dados do modelo. 

>[!Important]
> Você deve definir `passthru_automl_config` como `False` se o `AutoMLStep` estiver usando `PipelineOutputTabularDataset` objetos para entrada.

Neste exemplo, o processo de ML automatizado executará validações cruzadas no `training_data`. Você pode controlar o número de validações cruzadas com o `n_cross_validations` argumento. Se você já tiver dividido os dados de treinamento como parte de suas etapas de preparação de dados, `validation_data` poderá definir como `Dataset`seu próprio.

Ocasionalmente, você pode ver o `X` uso de recursos de `y` dados e rótulos de dados. Essa técnica foi preterida e você deve `training_data` usar para entrada. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registrar o modelo gerado por ML automatizado 

A última etapa em um pipeline de ML básico é registrar o modelo criado. Ao adicionar o modelo ao registro de modelo do espaço de trabalho, ele estará disponível no portal e poderá ter controle de versão. Para registrar o modelo, escreva outro `PythonScriptStep` que receba a `model_data` saída do `AutoMLStep`.

### <a name="write-the-code-to-register-the-model"></a>Escreva o código para registrar o modelo

Um modelo é registrado em um `Workspace`. Você provavelmente está familiarizado com o `Workspace.from_config()` uso do para fazer logon no seu espaço de trabalho no computador local, mas há outra maneira de obter o espaço de trabalho em um pipeline ml em execução. O `Run.get_context()` recupera o ativo `Run`. Este `run` objeto fornece acesso a muitos objetos importantes, incluindo o `Workspace` usado aqui.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Escreva o código PythonScriptStep

O registro de modelo `PythonScriptStep` usa um `PipelineParameter` para um de seus argumentos. Os parâmetros de pipeline são argumentos para pipelines que podem ser facilmente definidos em tempo de envio de execução. Depois de declarado, eles são passados como argumentos normais. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Criar e executar seu pipeline de ML automatizado

Criar e executar um pipeline que contém um `AutoMLStep` não é diferente de um pipeline normal. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

O código acima combina as etapas de preparação de dados, ML automatizado e registro de modelo em `Pipeline` um objeto. Em seguida, ele `Experiment` cria um objeto. O `Experiment` Construtor irá recuperar o experimento nomeado se ele existir ou criá-lo, se necessário. Ele envia o `Pipeline` para o `Experiment`, criando um `Run` objeto que irá executar o pipeline de forma assíncrona. A `wait_for_completion()` função é bloqueada até que a execução seja concluída.

### <a name="examine-pipeline-results"></a>Examinar os resultados do pipeline 

Quando o `run` for concluído, você poderá recuperar `PipelineData` objetos que foram atribuídos a um `pipeline_output_name`. Você pode baixar os resultados e carregá-los para processamento adicional.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Os arquivos baixados são gravados no subdiretório `azureml/{run.id}/`. O arquivo de métricas é formatado em JSON e pode ser convertido em um dataframe do pandas para exame.

Para o processamento local, talvez seja necessário instalar pacotes relevantes, como pandas, Pickle, o SDK do AzureML e assim por diante. Neste exemplo, é provável que o melhor modelo encontrado por ML automatizado dependerá do XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

O trecho de código acima mostra o arquivo de métricas que está sendo carregado de seu local no repositório de armazenamento do Azure. Você também pode carregá-lo do arquivo baixado, conforme mostrado no comentário. Depois de desserializá-lo e convertê-lo em um dataframe do pandas, você poderá ver métricas detalhadas para cada uma das iterações da etapa de ML automatizada.

O arquivo de modelo pode ser desserializado em `Model` um objeto que você pode usar para inferência, análise de métricas adicionais e assim por diante. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Para obter mais informações sobre como carregar e trabalhar com modelos existentes, consulte [usar um modelo existente com Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Baixar os resultados de uma execução de ML automatizada

Se estiver acompanhando o artigo, você terá um `run` objeto instanciado. Mas você também pode recuperar objetos `Run` concluídos `Workspace` por meio de um `Experiment` objeto.

O espaço de trabalho contém um registro completo de todos os experimentos e execuções. Você pode usar o portal para localizar e baixar as saídas de experimentos ou usar o código. Para acessar os registros de uma execução histórica, use Azure Machine Learning para localizar a ID da execução em que você está interessado. Com essa ID, você pode escolher o específico `run` por meio do `Workspace` e `Experiment`do.

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Você precisaria alterar as cadeias de caracteres no código acima para as especificidades de sua execução histórica. O trecho acima pressupõe que você tenha atribuído `ws` ao relevante `Workspace` com o normal `from_config()`. O experimento de interesse é recuperado diretamente e, em seguida, o `Run` código encontra o interesse correspondendo ao `run.id` valor.

Depois de ter um `Run` objeto, você pode baixar as métricas e o modelo. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Cada `Run` objeto contém `StepRun` objetos que contêm informações sobre a execução da etapa de pipeline individual. O `run` é pesquisado para `StepRun` o objeto para `AutoMLStep`o. As métricas e o modelo são recuperados usando seus nomes padrão, que estão disponíveis mesmo se você `PipelineData` não passar objetos `outputs` para o parâmetro `AutoMLStep`do. 

Por fim, as métricas e o modelo reais são baixados em seu computador local, como foi discutido na seção "examinar resultados do pipeline" acima.

## <a name="next-steps"></a>Próximas etapas

- Execute este Notebook Jupyter mostrando um [exemplo completo de ml automatizado em um pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) que usa a regressão para prever as tarifas de táxi
- [Criar experimentos de ML automatizados sem escrever código](how-to-use-automated-ml-for-ml-models.md)
- Explore uma variedade de [notebooks Jupyter demonstrando ml automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Leia sobre como integrar seu pipeline no [MLOps de ponta a ponta](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) ou investigar o [repositório GitHub do MLOps](https://github.com/Microsoft/MLOpspython) 
