---
title: O que é monitorado pelo Azure Monitor
description: Referência de todos os serviços e outros recursos monitorados pelo Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 27503defa23b2a35e767156139228aa2e177652f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405174"
---
# <a name="what-is-monitored-by-azure-monitor"></a>O que é monitorado pelo Azure Monitor?
Este artigo descreve os diferentes aplicativos e serviços monitorados pelo Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Informações e soluções principais
As principais informações e soluções são consideradas parte do Azure Monitor e seguem os contratos de nível de serviço e suporte para o Azure. Eles têm suporte em todas as regiões do Azure onde Azure Monitor está disponível.

### <a name="insights"></a>Insights

As informações fornecem uma experiência de monitoramento Personalizada para aplicativos e serviços específicos. Eles coletam e analisam os logs e as métricas.

| Insight | Descrição |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Serviço de gerenciamento de desempenho de aplicativos (APM) extensível para monitorar seu aplicativo Web ativo em qualquer plataforma. |
| [Azure Monitor para contêineres](insights/container-insights-overview.md) | Monitora o desempenho de cargas de trabalho de contêiner implantadas em instâncias de contêiner do Azure ou em clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure). |
| [Azure Monitor para Cosmos DB (versão prévia)](insights/cosmosdb-insights-overview.md) | Fornece uma visão do desempenho geral, falhas, capacidade e integridade operacional de todos os seus Azure Cosmos DB recursos em uma experiência interativa unificada. |
| [Azure Monitor para redes (visualização)](insights/network-insights-overview.md) | Fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede. A capacidade de pesquisa avançada ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site, simplesmente pesquisando o nome do site. |
[Azure Monitor para grupos de recursos (visualização)](insights/resource-group-insights.md) |  Faça a triagem e diagnostique quaisquer problemas que seus recursos individuais encontrem, enquanto oferece contexto para a integridade e o desempenho do grupo de recursos como um todo. |
| [Azure Monitor para armazenamento (visualização)](insights/storage-insights-overview.md) | Fornece monitoramento abrangente de suas contas de armazenamento do Azure fornecendo uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços de armazenamento do Azure. |
| [Azure Monitor para VMs](insights/container-insights-overview.md) | Monitora as máquinas virtuais (VM) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. |

### <a name="core-solutions"></a>Soluções principais

As soluções são baseadas em consultas de log e exibições personalizadas para um determinado aplicativo ou serviço. Eles coletam e analisam somente logs e estão sendo preteridos ao longo do tempo em favor das ideias.

| Solução | Descrição |
|:---|:---|
| [Integridade do agente](insights/solution-agenthealth.md) | Analise a integridade e a configuração dos agentes de Log Analytics. |
| [Gerenciamento de alertas](platform/alert-management-solution.md) | Analise os alertas coletados de System Center Operations Manager, Nagios ou Zabbix. |
| [Mapa do Serviço](insights/service-map.md) | Descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. |



## <a name="azure-services"></a>Serviços do Azure
A tabela a seguir lista os serviços do Azure e os dados coletados no Azure Monitor. 

- Métricas – o serviço coleta automaticamente as métricas em métricas de Azure Monitor. 
- Logs – o serviço dá suporte a configurações de diagnóstico que podem coletar logs e métricas de plataforma para Azure Monitor logs.
- Insight – há uma Insight disponível para o serviço que fornece uma experiência de monitoramento Personalizada para o serviço.

| Serviço | Métricas | Logs | Insight | Anotações |
|:---|:---|:---|:---|:---|
|Active Directory | Não | Sim | [Sim](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Não | Não | Não |  |
|Active Directory Domain Services | Não | Sim | Não |  |
|Log de atividades | Não | Sim | Não | |
|Proteção Avançada contra Ameaças | Não | Não | Não |  |
|Supervisor | Não | Não | Não |  |
|AI Builder | Não | Não | Não |  |
|Serviços de análise | Sim | Sim | Não |  |
|API para FHIR | Não | Não | Não |  |
|Gerenciamento de API | Sim | Sim | Não |  |
|Serviço de Aplicativo | Sim | Sim | Não |  |
|AppConfig | Não | Não | Não |  |
|Gateway de Aplicativo | Sim | Sim | Não |  |
|Serviço de atestado | Não | Não | Não |  |
|Automação | Sim | Sim | Não |  |
|Service Manager do Azure (RDFE) | Não | Não | Não |  |
|Backup | Não | Sim | Não |  |
|Bastion | Não | Não | Não |  |
|Lote | Sim | Sim | Não |  |
|Lote AI | Não | Não | Não |  |
|Blockchain Service | Não | Sim | Não |  |
|Blueprints | Não | Não | Não |  |
|Serviço de Bot | Não | Não | Não |  |
|Serviços de Nuvem | Sim | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho.  |
|Cloud Shell | Não | Não | Não |  |
|Serviços Cognitivos | Sim | Sim | Não |  |
|Instâncias de Contêiner | Sim | Não | Não |  |
|Registro de Contêiner | Sim | Sim | Não |  |
|CDN (Rede de Distribuição de Conteúdo) | Não | Sim | Não |  |
|Cosmos DB | Sim | Sim | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Gerenciamento de Custos | Não | Não | Não |  |
|Data Box | Não | Não | Não |  |
|Gen2 do catálogo de dados | Não | Não | Não |  |
|Data Explorer | Sim | Sim | Não |  |
|Data Factory | Sim | Sim | Não |  |
|Data Factory v2 | Não | Sim | Não |  |
|Data Share | Não | Não | Não |  |
|Banco de Dados para MariaDB | Sim | Sim | Não |  |
|Banco de Dados para MySQL | Sim | Sim | Não |  |
|Banco de Dados para PostgreSQL | Sim | Sim | Não |  |
|Serviço de Migração de Banco de Dados | Não | Não | Não |  |
|Databricks | Não | Sim | Não |  |
|Proteção contra DDoS | Sim | Sim | Não |  |
|DevOps | Não | Não | Não |  |
|DNS | Sim | Não | Não |  |
|Nomes de domínio | Não | Não | Não |  |
|DPS | Não | Não | Não |  |
|Compromisso com o cliente do Dynamics 365 | Não | Não | Não |  |
|Operações e finanças do Dynamics 365 | Não | Não | Não |  |
|Grade de Eventos | Sim | Não | Não |  |
|Hubs de Eventos | Sim | Sim | Não |  |
|ExpressRoute | Sim | Sim | Não |  |
|Firewall | Sim | Sim | Não |  |
|Front Door | Sim | Sim | Não |  |
|Funções | Sim | Sim | Não |  |
|HDInsight | Não | Sim | Não |  |
|HPC Cache | Não | Não | Não |  |
|Proteção de Informações | Não | Sim | Não |  |
|Intune | Não | Sim | Não |  |
|Central da IoT | Não | Não | Não |  |
|Hub IoT | Sim | Sim | Não |  |
|Key Vault | Sim | Sim | Não |  |
|AKS (Serviço do Kubernetes) | Não | Não | [Sim](insights/container-insights-overview.md)  |  |
|Load Balancer | Sim | Sim | Não |  |
|Aplicativos Lógicos | Sim | Sim | Não |  |
|Serviço do Machine Learning | Não | Não | Não |  |
|Aplicativos gerenciados  | Não | Não | Não |  |
|Mapas  | Não | Não | Não |  |
|Serviços de mídia | Sim | Sim | Não |  |
|Microsoft Flow | Não | Não | Não |  |
|Área de Trabalho Gerenciada da Microsoft | Não | Não | Não |  |
|Microsoft PowerApps | Não | Não | Não |  |
|Microsoft Social Engagement | Não | Não | Não |  |
|Microsoft Stream | Sim | Sim | Não |  |
|Migrar | Não | Não | Não |  |
|Autenticação Multifator | Não | Sim | Não |  |
|Observador de Rede | Sim | Sim | Não |  |
|Hubs de Notificação | Sim | Não | Não |  |
|Conjuntos de dados em aberto | Não | Não | Não |  |
|Política | Não | Não | Não |  |
|Power BI | Sim | Sim | Não |  |
|Power BI Embedded | Não | Não | Não |  |
|Link Privado | Não | Não | Não |  |
|Plataforma de comunicação do spool do projeto | Não | Não | Não |  |
|Red Hat OpenShift | Não | Não | Não |  |
|Cache Redis | Sim | Sim | Não |  |
|Resource Graph | Não | Não | Não |  |
|Gerenciador de Recursos | Não | Não | Não |  |
|Pesquisa de varejo – por Bing | Não | Não | Não |  |
|Pesquisar | Sim | Sim | Não |  |
|Service Bus | Sim | Sim | Não |  |
|Service Fabric | Não | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho.  |
|Portal de inscrição | Não | Não | Não |  |
|Site Recovery | Não | Sim | Não |  |
|Serviço Spring Cloud | Não | Não | Não |  |
|SQL Data Warehouse | Sim | Sim | Não |  |
|Banco de Dados SQL | Sim | Sim | Não |  |
|SQL Server Stretch Database | Sim | Sim | Não |  |
|Pilha | Não | Não | Não |  |
|Armazenamento | Sim | Não | [Sim](insights/storage-insights-overview.md) |  |
|Cache de armazenamento | Não | Não | Não |  |
|Serviços de sincronização de armazenamento | Não | Não | Não |  |
|Stream Analytics | Sim | Sim | Não |  |
|Time Series Insights | Sim | Sim | Não |  |
|TINA | Não | Não | Não |  |
|Gerenciador de Tráfego | Sim | Sim | Não |  |
|Impressão universal | Não | Não | Não |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | Não | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho. |
|Máquinas Virtuais | Sim | Sim | [Sim](insights/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho. |
|Rede Virtual | Sim | Sim | [Sim](insights/network-insights-overview.md) |  |
|Rede virtual-logs de fluxo NSG | Não | Sim | Não |  |
|Gateway de VPN | Sim | Sim | Não |  |
|Área de Trabalho Virtual do Windows | Não | Não | Não |  |


## <a name="product-integrations"></a>Integrações de produtos
Os serviços e as soluções na tabela a seguir armazenam seus dados em um Log Analytics espaço de trabalho para que possam ser analisados com outros dados de log coletados pelo Azure Monitor.

| Produto/Serviço | Descrição |
|:---|:---|
| [Automação do Azure](/azure/automation/) | Gerenciar atualizações do sistema operacional e controlar alterações em computadores Windows e Linux. Consulte [controle de alterações](../automation/change-tracking.md) e [Gerenciamento de atualizações](../automation/automation-update-management.md). |
| [Proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/) | Classifique e, opcionalmente, proteja documentos e emails. Consulte [relatórios centrais para a proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Central de Segurança do Azure](/azure/security-center/) | Colete e analise eventos de segurança e execute a análise de ameaças. Consulte [coleta de dados na central de segurança do Azure](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Conecta-se a fontes diferentes, incluindo o Office 365 e a trilha de nuvem do Amazon Web Services. Consulte [conectar fontes de dados](/azure/sentinel/connect-data-sources). |
| [Análise do Cofre de Chaves](insights/azure-key-vault.md) | Analise Azure Key Vault logs do AuditEvent. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Crie uma configuração de diagnóstico para enviar logs para Azure Monitor. Consulte [enviar dados de log para armazenamento, hubs de eventos ou log Analytics no Intune (versão prévia)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor de desempenho de rede](insights/network-performance-monitor.md) -monitore a conectividade de rede e o desempenho para pontos de extremidade de serviço e de aplicativo.<br>[Aplicativo Azure gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) -analise os logs e as métricas do gateway de aplicativo Azure.<br>[Análise de tráfego](/azure/network-watcher/traffic-analytics) -analisa os logs de fluxo do NSG (grupo de segurança de rede) do observador de rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. |
| [Office 365](insights/solution-office-365.md) | Monitore seu ambiente do Office 365. Versão atualizada com integração aprimorada disponível por meio do Azure Sentinel. |
| [Análise de SQL](insights/azure-sql.md) | Monitore o desempenho de bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas em escala e em várias assinaturas. |
| [Surface Hub](insights/surface-hubs.md) | Acompanhe a integridade e o uso de dispositivos Surface Hub. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Colete dados de agentes de Operations Manager conectando seu grupo de gerenciamento ao Azure Monitor. Consulte [conectar Operations Manager ao Azure monitor](platform/om-agents.md)<br> Avalie o risco e a integridade de seu grupo de gerenciamento de System Center Operations Manager com Operations Manager solução de [avaliação](insights/scom-assessment.md) . |
| [Salas do Microsoft Teams](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Gerenciamento integrado de ponta a ponta de dispositivos de salas do Microsoft Teams. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Crie, teste e distribua aplicativos e, em seguida, monitore seu status e uso. Consulte [começar a analisar seu aplicativo móvel com app Center e Application insights](learn/mobile-center-quickstart.md). |
| Windows | [Conformidade de Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) – avalie as atualizações da área de trabalho do Windows.<br>[Análise de desktop](https://docs.microsoft.com/configmgr/desktop-analytics/overview) -integra-se com Configuration Manager para fornecer informações e inteligência para tomar decisões mais informadas sobre a prontidão de atualização de seus clientes Windows. |



## <a name="other-solutions"></a>Outras soluções
Outras soluções estão disponíveis para monitorar diferentes aplicativos e serviços, mas o desenvolvimento ativo foi interrompido e eles podem não estar disponíveis em todas as regiões. Eles são cobertos pelo contrato de nível de serviço de ingestão de dados do Azure Log Analytics.

| Solução | Descrição |
|:---|:---|
| [Active Directory verificação de integridade](insights/ad-assessment.md) | Avalie o risco e a integridade de seus ambientes de Active Directory. |
| [Status de replicação do Active Directory](insights/ad-replication-status.md) | Monitora regularmente seu ambiente de Active Directory em busca de falhas de replicação. |
| [Log Analytics de atividades](platform/activity-log-view.md#azure-portal) | Exibir entradas do log de atividades. |
| [Análise de DNS (versão prévia)](insights/dns-analytics.md) | Coleta, analisa e correlaciona os logs analíticos e de auditoria do DNS do Windows e outros dados relacionados dos servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Colete, exiba e analise suas Cloud Foundry métricas de desempenho e integridade do sistema em várias implantações. |
| [Contêineres](insights/containers.md) | Exiba e gerencie hosts de contêiner do Docker e do Windows. |
| [Avaliações sob demanda](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, a segurança e o desempenho de seus ambientes de tecnologia da Microsoft locais, híbridos e na nuvem. |
| [Verificação de integridade do SQL](insights/sql-assessment.md) | Avalie o risco e a integridade de seus ambientes de SQL Server.  |
| [Dados durante a transmissão](insights/wire-data.md) | Dados consolidados de rede e de desempenho coletados de computadores conectados ao Windows e conectados ao Linux com o agente de Log Analytics. |

## <a name="third-party-integration"></a>Integração de terceiros

| Solução | Descrição |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | O ITSMC (Conector de Gerenciamento de Serviços de TI) permite que você conecte o Azure e um produto/serviço de ITSM (Gerenciamento de Serviços de TI) compatível.  |


## <a name="resources-outside-of-azure"></a>Recursos fora do Azure
Azure Monitor pode coletar dados de recursos fora do Azure usando os métodos listados na tabela a seguir.

| Recurso | Método |
|:---|:---|
| Aplicativos | Monitore aplicativos Web fora do Azure usando Application Insights. Veja [o que é Application insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Máquinas virtuais | Use o agente de Log Analytics para coletar dados do sistema operacional convidado de máquinas virtuais em outros ambientes de nuvem ou locais. Consulte [coletar dados de log com o agente de log Analytics](platform/log-analytics-agent.md). |
| Cliente da API REST | APIs separadas estão disponíveis para gravar dados em Azure Monitor logs e métricas de qualquer cliente de API REST. Consulte [enviar dados de log para Azure monitor com a API do coletor de dados http](platform/data-collector-api.md) para logs e [Enviar métricas personalizadas para um recurso do Azure para o repositório de métrica Azure monitor usando uma API REST](platform/metrics-store-custom-rest-api.md) para métricas. |



## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a [plataforma de dados Azure monitor que armazena os logs e as métricas coletados por informações e soluções](platform/data-platform.md).
- Conclua um [tutorial sobre como monitorar um recurso do Azure](learn/tutorial-resource-logs.md).
- Conclua um [tutorial sobre como gravar uma consulta de log para analisar dados em logs de Azure monitor](learn/tutorial-resource-logs.md).
- Conclua um [tutorial sobre como criar um gráfico de métricas para analisar dados em métricas de Azure monitor](learn/tutorial-metrics-explorer.md).

 
