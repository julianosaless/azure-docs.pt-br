---
title: Análise de SQL do Azure solução no Azure Monitor | Microsoft Docs
description: Solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados SQL do Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275458"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorar o Banco de Dados SQL do Azure usando a Análise de SQL do Azure (Versão Prévia)

![Símbolo da Análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

Análise de SQL do Azure é uma solução de monitoramento de nuvem avançada para monitorar o desempenho de todos os seus bancos de dados SQL do Azure em escala e em várias assinaturas em uma única exibição. Análise de SQL do Azure coleta e visualiza as principais métricas de desempenho com inteligência interna para solução de problemas de desempenho.

Usando essas métricas coletadas, você pode criar alertas e regras de monitoramento personalizados. Análise de SQL do Azure ajuda a identificar problemas em cada camada de sua pilha de aplicativos. Ele usa métricas de diagnóstico do Azure junto com Azure Monitor exibições para apresentar dados sobre todos os bancos de dado SQL do Azure em um único espaço de trabalho Log Analytics. Azure Monitor ajuda a coletar, correlacionar e Visualizar dados estruturados e não estruturados.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Fontes conectadas

Análise de SQL do Azure é uma solução de monitoramento somente de nuvem com suporte para streaming de telemetria de diagnóstico para todos os seus bancos de dados SQL do Azure. Como Análise de SQL do Azure não usa agentes para se conectar ao Azure Monitor, ele não oferece suporte ao monitoramento de SQL Server hospedados localmente ou em máquinas virtuais.

| Fonte Conectada | Com suporte | Descrição |
| --- | --- | --- |
| [Configurações de diagnóstico](../platform/diagnostic-settings.md) | **Sim** | Os dados de log e métrica do Azure são enviados para Azure Monitor logs diretamente pelo Azure. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | Azure Monitor não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Os agentes diretos do Windows não são usados pelo Análise de SQL do Azure. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não | Os agentes diretos do Linux não são usados pelo Análise de SQL do Azure. |
| [Grupo de gerenciamento do System Center Operations Manager](../platform/om-agents.md) | Não | Uma conexão direta do agente de Operations Manager para Azure Monitor não é usada pelo Análise de SQL do Azure. |

## <a name="azure-sql-analytics-options"></a>Opções de Análise de SQL do Azure

A tabela abaixo descreve as opções com suporte para duas versões do painel Análise de SQL do Azure, uma para bancos de dados individuais e pools elásticos e o outro para instâncias gerenciadas e bancos de dados de instância.

| Opção Análise de SQL do Azure | Descrição | Suporte a banco de dados único e em pool e pools elásticos | Instâncias gerenciadas e suporte a banco de dados de instâncias |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. | Sim | Sim |
| Insights | Fornece uma busca detalhada hierárquica no Intelligent Insights sobre o desempenho. | Sim | Sim |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. | Sim | Sim |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. | Sim | Não |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. | Sim | Não |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |Sim | Não |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. | Sim | Sim |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração

Use o processo descrito em [Adicionar soluções de Azure monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md) para adicionar análise de SQL do Azure (versão prévia) ao seu espaço de trabalho do log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Configurar bancos de dados SQL do Azure para transmitir a telemetria de diagnóstico

Depois de criar Análise de SQL do Azure solução no espaço de trabalho, você precisa **configurar cada** recurso que deseja monitorar para transmitir sua telemetria de diagnóstico para análise de SQL do Azure. Siga as instruções detalhadas nesta página:

- Habilite o Diagnóstico do Azure para o Banco de Dados SQL do Azure para [telemetria de diagnóstico de fluxo para a Análise de SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima também fornece instruções sobre como habilitar o suporte para monitorar várias assinaturas do Azure em um único workspace da Análise de SQL do Azure como um único painel de controle.

## <a name="using-azure-sql-analytics"></a>Usando Análise de SQL do Azure

Quando você adiciona Análise de SQL do Azure ao seu espaço de trabalho, o bloco Análise de SQL do Azure é adicionado ao seu espaço de trabalho e aparece em visão geral. Selecione Exibir link de resumo para carregar o conteúdo do bloco.

![Bloco de resumo Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

Depois de carregado, o bloco mostra o número de bancos de dados únicos e em pool, pools elásticos, instâncias gerenciadas e bancos de dados de instância gerenciada dos quais Análise de SQL do Azure está recebendo telemetria de diagnóstico.

![Bloco de Análise do SQL Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

Análise de SQL do Azure fornece duas exibições separadas, uma para monitorar bancos de dados individuais e bancos de dados em pool e pools elásticos e a outra exibição para monitorar instâncias gerenciadas e bancos de dados de instância.

Para exibir Análise de SQL do Azure painel de monitoramento para bancos de dados individuais e em pool e pools elásticos, clique na parte superior do bloco. Para exibir Análise de SQL do Azure painel de monitoramento para instâncias gerenciadas e bancos de dados de instância, clique na parte inferior do bloco.

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que diferentes perspectivas funcionem, você deve habilitar as métricas ou os logs apropriados em seus recursos do SQL para serem transmitidos para Log Analytics espaço de trabalho.

Se algumas métricas ou logs não forem transmitidos para Azure Monitor, os blocos em Análise de SQL do Azure não serão preenchidos com informações de monitoramento.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Exibição de bancos de dados individuais e pools elásticos

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva nessa exibição fornece resumos na assinatura, no servidor, no pool elástico e nos níveis de banco de dados. Além disso, cada perspectiva mostrada é específica ao relatório à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista dá continuidade ao drill down.

### <a name="managed-instance-and-instances-databases-view"></a>Exibição de bancos de dados de instância e instâncias gerenciadas

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

Selecionar a exibição de instância gerenciada, mostra detalhes sobre a utilização da instância gerenciada, os bancos de dados que ele contém e a telemetria nas consultas executadas na instância.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

O [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) do Banco de dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do Banco de Dados SQL do Microsoft Azure. Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights.

![Insights de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pools elásticos e relatórios de banco de dados

Os pools elásticos e os bancos de dados têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no tempo especificado.

![Banco de dados de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elástico SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio das perspectivas de duração da consulta e esperas de consulta, você pode correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para usar a Análise de SQL do Azure, os usuários precisam receber uma permissão mínima da função de leitor no Azure. No entanto, essa função não permite que os usuários vejam o texto de consulta ou realizem quaisquer ações de ajuste automático. Mais funções permissivas no Azure que permitem o uso de Análise de SQL do Azure para a extensão mais completa são proprietário, colaborador, colaborador de BD SQL ou colaborador de SQL Server. Também convém considerar a criação de uma função personalizada no portal, com permissões específicas necessárias apenas para usar a Análise de SQL do Azure e sem acesso ao gerenciamento de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar uma função personalizada no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Reconhecendo que algumas organizações impõem controles de permissão estrito no Azure, localize o seguinte script PowerShell, possibilitando a criação de uma função personalizada "Operador de monitoramento da Análise de SQL" no portal do Azure, com, pelo menos, permissões de leitura e gravação necessárias para usar a Análise de SQL do Azure até a extensão máxima.

Substitua "{SubscriptionId}" no script abaixo por sua ID da assinatura do Azure e execute o script conectado como uma função de Proprietário ou Colaborador no Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Depois de criar a nova função, atribua essa função para cada usuário ao qual você precisa conceder permissões personalizadas para usar a Análise de SQL do Azure.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados na Análise de SQL do Azure baseia-se na [linguagem de programação do Log Analytics](../log-query/get-started-queries.md) para suas consultas personalizadas e relatórios. Leia a descrição dos dados disponíveis coletados do recurso de banco de dados para a consulta personalizada [métricas e logs disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Os alertas automatizados no Análise de SQL do Azure baseiam-se na gravação de uma consulta Log Analytics que dispara um alerta sobre uma condição atendida. Encontre abaixo vários exemplos de Log Analytics consultas sobre as quais os alertas podem ser configurados no Análise de SQL do Azure.

### <a name="creating-alerts-for-azure-sql-database"></a>Criando alertas para o Banco de Dados SQL do Azure

Você pode [criar alertas](../platform/alerts-metric.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Aqui estão algumas consultas de [consultas de logs](../log-query/log-query-overview.md) que podem ser usadas com um alerta do log:

#### <a name="high-cpu-on-azure-sql-database"></a>CPU alta no Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para Análise de SQL do Azure.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>CPU alta em pools elásticos do Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para Análise de SQL do Azure.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Banco de Dados SQL do Azure em média acima de 95% na última 1 hora

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem métricas básicas para Análise de SQL do Azure.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe em alguns bancos de dados. A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta no Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que os bancos de dados monitorados transmitem o log de diagnóstico do sqlsights para Análise de SQL do Azure.
> - Essa consulta exige que uma regra de alerta seja configurada para ser executada com a mesma frequência que alert_run_interval a fim de evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando houver resultados (resultados > 0) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu nos bancos de dados configurados para transmitir o log sqlsights para Análise de SQL do Azure.
> - Personalize o insights_string para capturar a saída do texto da análise de causa de raiz do Insights. Esse é o mesmo texto exibido na interface do usuário do Análise de SQL do Azure que você pode usar a partir dos insights existentes. Como alternativa, você pode usar a consulta a seguir para ver o texto de todos os Insights gerados em sua assinatura. Use a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas no Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Criando alertas para instâncias gerenciadas

#### <a name="managed-instance-storage-is-above-90"></a>O armazenamento de instância gerenciada está acima de 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que o monitorado da instância gerenciada tem o streaming do log ResourceUsageStats habilitado para Análise de SQL do Azure.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, indicando que a condição existe na instância gerenciada. A saída é o consumo percentual de armazenamento na instância gerenciada.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>O consumo médio da CPU da instância gerenciada está acima de 95% na última hora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - O pré-requisito da configuração desse alerta é que a instância gerenciada monitorada tem o streaming do log ResourceUsageStats habilitado para Análise de SQL do Azure.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, indicando que a condição existe na instância gerenciada. A saída é o consumo percentual médio de utilização da CPU no período definido na instância gerenciada.

### <a name="pricing"></a>Preços

Embora Análise de SQL do Azure esteja livre para uso, o consumo de telemetria de diagnóstico acima das unidades livres de ingestão de dados alocada a cada mês se aplica, consulte [preços de log Analytics](https://azure.microsoft.com/pricing/details/monitor). As unidades gratuitas de ingestão de dados fornecidas habilitam o monitoramento gratuito de vários bancos de dados por mês. Mais bancos de dados ativos com cargas de trabalho mais pesadas ingerirão mais informações em comparação com bancos de dados ociosos. Você pode monitorar facilmente o consumo de ingestão de dados em Análise de SQL do Azure selecionando espaço de trabalho do OMS no menu de navegação do Análise de SQL do Azure e, em seguida, selecionando uso e custos estimados.

## <a name="next-steps"></a>Próximas etapas

- Use [consultas de log](../log-query/log-query-overview.md) em Azure monitor para exibir dados do Azure SQL detalhados.
- [Criar seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
