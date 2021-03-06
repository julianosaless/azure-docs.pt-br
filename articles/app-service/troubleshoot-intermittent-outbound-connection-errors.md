---
title: Solucionando problemas de erros de conexão de saída intermitente no serviço Azure App
description: Solucionar problemas de erros de conexão intermitente e problemas de desempenho relacionados no serviço Azure App
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80367545"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Solucionando problemas de erros de conexão de saída intermitente no serviço Azure App

Este artigo ajuda você a solucionar problemas de erros de conexão intermitente e problemas de desempenho relacionados no [serviço Azure app](https://docs.microsoft.com/azure/app-service/overview). Este tópico fornecerá mais informações e metodologias de solução de problemas para o esgotamento de portas de SNAT (conversão de rede de endereço de origem). Se você precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure no [msdn do Azure e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="symptoms"></a>Sintomas

Os aplicativos e funções hospedados no serviço Azure App podem apresentar um ou mais dos seguintes sintomas:

* Tempos de resposta lentos em todas ou algumas das instâncias em um plano de serviço.
* Erros intermitentes de gateway ou 5xx **inválidos**
* Mensagens de erro de tempo limite
* Não foi possível conectar-se a pontos de extremidade externos (como SQLDB, Service Fabric, outros serviços de aplicativos, etc.)

## <a name="cause"></a>Causa

Uma grande causa desses sintomas é que a instância do aplicativo não é capaz de abrir uma nova conexão com o ponto de extremidade externo porque ele atingiu um dos seguintes limites:

* Conexões TCP: há um limite no número de conexões de saída que podem ser feitas. Isso é associado ao tamanho do trabalho usado.
* Portas SNAT: conforme discutido em [conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections), o Azure usa o SNAT (conversão de endereço de rede de origem) e um Load Balancer (não exposto aos clientes) para se comunicar com pontos de extremidade fora do Azure no espaço de endereço IP público. Inicialmente, cada instância no serviço de Azure App recebe um número pré-alocado de portas SNAT **128** . Esse limite afeta a abertura de conexões com a mesma combinação de host e porta. Se seu aplicativo cria conexões com uma combinação de combinações de endereços e portas, você não usará as portas SNAT. As portas SNAT são usadas quando você tem chamadas repetidas para a mesma combinação de endereço e porta. Depois que uma porta for liberada, a porta estará disponível para reutilização conforme necessário. O balanceador de carga de rede do Azure recupera a porta SNAT de conexões fechadas somente depois de aguardar por 4 minutos.

Quando aplicativos ou funções abrem rapidamente uma nova conexão, eles podem esgotar rapidamente a cota alocada das portas 128. Eles são bloqueados até que uma nova porta SNAT fique disponível, seja por meio da alocação dinâmica de portas SNAT adicionais ou pela reutilização de uma porta SNAT recuperada. Aplicativos ou funções que são bloqueadas devido a essa incapacidade de criar novas conexões começarão a apresentar um ou mais dos problemas descritos na seção **sintomas** deste artigo.

## <a name="avoiding-the-problem"></a>Evitando o problema

Evitar o problema de porta SNAT significa evitar a criação de novas conexões repetidamente para o mesmo host e porta.

As estratégias gerais para mitigar o esgotamento de porta SNAT são discutidas na [seção solução de problemas](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) das **conexões de saída da** documentação do Azure. Dessas estratégias, as seguintes são aplicáveis a aplicativos e funções hospedados no serviço Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificar o aplicativo para usar o pooling de conexão

* Para conexões HTTP de pool, examine [conexões http do pool com HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Para obter informações sobre o pool de conexões SQL Server, examine [SQL Server pooling de conexão (ADO.net)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Para implementar o pooling com aplicativos do Entity Framework, examine [pooling de DbContext](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Aqui está uma coleção de links para implementar o pool de conexões por uma pilha de solução diferente.

#### <a name="node"></a>Nó

Por padrão, as conexões para NodeJS não são mantidas ativas. Abaixo estão os bancos de dados populares e pacotes para o pool de conexões que contêm exemplos de como implementá-los.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

Keep-Alive de HTTP

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Documentação do node. js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Abaixo estão as bibliotecas populares usadas para o pool de conexões JDBC, que contêm exemplos de como implementá-las: pool de conexões JDBC.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pool de conexões HTTP

* [Gerenciamento de conexão do Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager de classe](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Embora o PHP não ofereça suporte ao pool de conexões, você pode tentar usar conexões de banco de dados persistentes para o servidor back-end.
 
* Servidor MySQL

   * [Conexões mysqli](https://www.php.net/manual/mysqli.quickstart.connections.php) para versões mais recentes
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) para versões mais antigas do php

* Outras fontes de dados

   * [Gerenciamento de conexão PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (Observação: SQLAlchemy pode ser usado com outros bancos de dados além do MicrosoftSQL Server)
* O [http Keep-](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)Alive (Keep-Alive é automático ao usar Session [-Objects](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

Para outros ambientes, examine os documentos específicos do provedor ou do driver para implementar o pool de conexões em seus aplicativos.

### <a name="modify-the-application-to-reuse-connections"></a>Modificar o aplicativo para reutilizar conexões

*  Para obter ponteiros adicionais e exemplos sobre como gerenciar conexões no Azure functions, examine [gerenciar conexões no Azure Functions](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva

* Para obter orientações e exemplos adicionais, reveja o [padrão de repetição](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída

* Para implementar keepalives para aplicativos node. js, examine [meu aplicativo de nó fazendo chamadas de saída excessivas](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Diretrizes adicionais específicas para o serviço de aplicativo:

* Um [teste de carga](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) deve simular dados do mundo real em uma velocidade de alimentação contínua. O teste de aplicativos e funções sob estresse do mundo real pode identificar e resolver os problemas de esgotamento da porta SNAT antes do tempo.
* Verifique se os serviços de back-end podem retornar respostas rapidamente. Para solucionar problemas de desempenho com o banco de dados SQL do Azure, examine [solucionar problemas de desempenho do banco de dados SQL do Azure com Intelligent insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Escalar horizontalmente o plano do serviço de aplicativo para mais instâncias. Para saber mais sobre como dimensionar, consulte [Escalar um aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/manage-scale-up). Cada instância de trabalho em um plano do serviço de aplicativo é alocada a um número de portas SNAT. Se você distribuir seu uso em mais instâncias, poderá obter o uso da porta SNAT por instância abaixo do limite recomendado de 100 conexões de saída, por ponto de extremidade remoto exclusivo.
* Considere a mudança para [ambiente do serviço de aplicativo (ase)](https://docs.microsoft.com/azure/app-service/environment/using-an-ase), em que você está alocado para um único endereço IP de saída, e os limites para conexões e portas SNAT são muito maiores.

Evitar os limites de TCP de saída é mais fácil de resolver, pois os limites são definidos pelo tamanho do seu operador. Você pode ver os limites em [área restrita limite de VM entre VMs-conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome do limite|Descrição|Pequeno (a1)|Média (a2)|Grande (a3)|Camada isolada (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

Para evitar limites de TCP de saída, você pode aumentar o tamanho de seus trabalhadores ou escalar horizontalmente.

## <a name="troubleshooting"></a>Solução de problemas

Conhecer os dois tipos de limites de conexão de saída e o que seu aplicativo faz é facilitar a solução de problemas. Se você souber que seu aplicativo faz muitas chamadas para a mesma conta de armazenamento, você pode suspeitar de um limite de SNAT. Se seu aplicativo cria muitas chamadas para pontos de extremidade pela Internet, você suspeita que está atingindo o limite da VM.

Se você não souber o comportamento do aplicativo o suficiente para determinar a causa rapidamente, há algumas ferramentas e técnicas disponíveis no serviço de aplicativo para ajudar com essa determinação.

### <a name="find-snat-port-allocation-information"></a>Localizar informações de alocação de porta SNAT

Você pode usar o [diagnóstico do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para localizar informações de alocação de porta SNAT e observar a métrica de alocação de portas SNAT de um site do serviço de aplicativo. Para localizar informações de alocação de porta SNAT, siga as seguintes etapas:

1. Para acessar o diagnóstico do serviço de aplicativo, navegue até o aplicativo Web do serviço de aplicativo ou Ambiente do Serviço de Aplicativo na [portal do Azure](https://portal.azure.com/). No painel de navegação esquerdo, selecione **diagnosticar e resolver problemas**.
2. Selecionar categoria de desempenho e disponibilidade
3. Selecione bloco de esgotamento de porta SNAT na lista de blocos disponíveis na categoria. A prática é mantê-lo abaixo de 128.
Se você precisar dele, ainda poderá abrir um tíquete de suporte e o engenheiro de suporte obterá a métrica do back-end para você.

Observe que, como o uso da porta SNAT não está disponível como uma métrica, não é possível fazer a autoescala com base no uso da porta SNAT ou configurar o dimensionamento automático com base na métrica de alocação de portas SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Conexões TCP e portas SNAT

As conexões TCP e SNAT não estão diretamente relacionadas. Um detector de uso de conexões TCP está incluído na folha diagnosticar e resolver problemas de qualquer site do serviço de aplicativo. Procure a frase "conexões TCP" para encontrá-la.

* As portas SNAT são usadas somente para fluxos de rede externos, enquanto o total de conexões TCP inclui conexões de loopback locais.
* Uma porta SNAT pode ser compartilhada por fluxos diferentes, se os fluxos forem diferentes em qualquer protocolo, endereço IP ou porta. A métrica de conexões TCP conta cada conexão TCP.
* O limite de conexões TCP ocorre no nível de instância de trabalho. O balanceamento de carga de saída de rede do Azure não usa a métrica de conexões TCP para limitação de porta SNAT.
* Os limites de conexões TCP são descritos em [área restrita áreas limites numéricos de VM-conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome do limite|Descrição|Pequeno (a1)|Média (a2)|Grande (a3)|Camada isolada (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Trabalhos Web e conexões de banco de dados
 
Se as portas SNAT esgotarem, em que os trabalhos Web não conseguem se conectar ao banco de dados SQL do Azure, não há métrica para mostrar quantas conexões são abertas por cada processo de aplicativo Web individual. Para encontrar o WebJob problemático, mova vários trabalhos Web para outro plano do serviço de aplicativo para ver se a situação melhora ou se um problema permanece em um dos planos. Repita o processo até encontrar o WebJob problemático.

### <a name="using-snat-ports-sooner"></a>Usando portas SNAT mais cedo

Você não pode alterar as configurações do Azure para liberar as portas SNAT usadas mais cedo, pois todas as portas SNAT serão liberadas de acordo com as condições abaixo e o comportamento será por design.
 
* Se o servidor ou o cliente enviar FINACK, a [porta SNAT será liberada](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) após 240 segundos.
* Se um RST for visto, a porta SNAT será liberada após 15 segundos.
* Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.
 
## <a name="additional-information"></a>Informações adicionais

* [SNAT com serviço de aplicativo](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Solucionar problemas de desempenho de aplicativo lento no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
