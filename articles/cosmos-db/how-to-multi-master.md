---
title: Como configurar vários mestres no Azure Cosmos DB
description: Saiba como configurar vários mestres para seus aplicativos usando SDKs diferentes no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873634"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurar vários mestres nos aplicativos que usam o Azure Cosmos DB

Quando uma conta tiver sido criada com várias regiões de gravação habilitadas, faça duas alterações em seu aplicativo em ConnectionPolicy para DocumentClient a fim de habilitar os recursos de vários mestres e a hospedagem múltipla no Azure Cosmos DB. Em ConnectionPolicy, defina UseMultipleWriteLocations como true e passe o nome da região em que o aplicativo é implantado em SetCurrentLocation. Isso preencherá a propriedade PreferredLocations com base na proximidade geográfica do local passado. Se uma nova região posteriormente é adicionada à conta, o aplicativo não precisa ser atualizado ou reimplantado, ele detectará automaticamente a região mais próxima e será automaticamente iniciado em caso de evento regional.

> [!Note]
> Contas do Cosmos configuradas inicialmente com uma região de gravação podem ser configuradas para várias regiões de gravação (ou seja, vários mestres) sem nenhum tempo de inatividade. Para saber mais, consulte [Configurar a gravação de várias regiões](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>SDK do .NET v2

Para habilitar vários mestres em seu aplicativo, defina `UseMultipleWriteLocations` como `true`. Além disso, defina `SetCurrentLocation` como a região na qual o aplicativo está sendo implantado e em que o Azure Cosmos DB está replicado:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>SDK do .NET v3

Para habilitar vários mestres no seu aplicativo, defina `ApplicationRegion` como a região na qual o aplicativo está sendo implantado e o Cosmos DB está replicado:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionalmente, você pode usar o `CosmosClientBuilder` e o `WithApplicationRegion` para obter o mesmo resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>SDK de Java Assíncrono

Para habilitar vários mestres no seu aplicativo, defina `policy.setUsingMultipleWriteLocations(true)` e defina `policy.setPreferredLocations` como a região na qual o aplicativo está sendo implantado e o Cosmos DB está replicado:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>SDK do Node.js, do JavaScript e do TypeScript

Para habilitar vários mestres em seu aplicativo, defina `connectionPolicy.UseMultipleWriteLocations` como `true`. Além disso, defina `connectionPolicy.PreferredLocations` como a região na qual o aplicativo está sendo implantado e em que o Cosmos DB está replicado:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>SDK do Python

Para habilitar vários mestres em seu aplicativo, defina `connection_policy.UseMultipleWriteLocations` como `true`. Além disso, defina `connection_policy.PreferredLocations` como a região na qual o aplicativo está sendo implantado e em que o Cosmos DB está replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Próximas etapas

Leia os seguintes artigos:

* [Usar tokens de sessão para gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflitos e políticas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolher o nível de consistência correto no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global: nos bastidores](global-dist-under-the-hood.md)
