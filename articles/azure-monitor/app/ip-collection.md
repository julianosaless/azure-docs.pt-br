---
title: Coleção de endereços IP do Aplicativo Azure insights | Microsoft Docs
description: Compreendendo como os endereços IP e a localização geográfica são tratados com o Aplicativo Azure insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656510"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocalização e manipulação de endereço IP

Este artigo explica como a pesquisa de localização geográfica e o tratamento de endereços IP ocorrem em Application Insights juntamente com como modificar o comportamento padrão.

## <a name="default-behavior"></a>Comportamento padrão

Por padrão, os endereços IP são coletados temporariamente, mas não são armazenados em Application Insights. O processo básico é como descrito a seguir:

Os endereços IP são enviados para Application Insights como parte dos dados de telemetria. Ao atingir o ponto de extremidade de ingestão no Azure, o endereço IP é usado para executar uma pesquisa de localização geográfica usando [GeoLite2 de MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Os resultados dessa pesquisa são usados para popular os campos `client_City`a seguir, `client_StateOrProvince`, `client_CountryOrRegion`. Neste ponto, o endereço IP é Descartado e `0.0.0.0` gravado no `client_IP` campo.

* Telemetria do navegador: coletamos temporariamente o endereço IP do remetente. O endereço IP é calculado pelo ponto de extremidade de ingestão.
* Telemetria do servidor: o módulo Application Insights coleta temporariamente o endereço IP do cliente. Ele não será coletado se `X-Forwarded-For` estiver configurado.

Esse comportamento é por design para ajudar a evitar a coleta desnecessária de dados pessoais. Sempre que possível, é recomendável evitar a coleta de dados pessoais. 

## <a name="overriding-default-behavior"></a>Substituindo o comportamento padrão

Embora o comportamento padrão seja minimizar a coleta de dados pessoais, ainda oferecemos a flexibilidade para coletar e armazenar dados de endereço IP. Antes de optar por armazenar quaisquer dados pessoais, como endereços IP, é altamente recomendável verificar se isso não interrompe os requisitos de conformidade ou as normas locais às quais você pode estar sujeito. Para saber mais sobre a manipulação de dados pessoais no Application Insights, consulte as [diretrizes para dados pessoais](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Armazenando dados de endereço IP

Para habilitar a coleta e o armazenamento de IP, `DisableIpMasking` a propriedade do componente Application insights deve ser definida como `true`. Essa propriedade pode ser definida por meio de modelos de Azure Resource Manager ou chamando a API REST. 

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Se você só precisa modificar o comportamento de um único recurso Application Insights, a maneira mais fácil de fazer isso é por meio do portal do Azure.  

1. Acesse o recurso de Application insights > **configurações** > **Exportar modelo** 

    ![Exportar modelo](media/ip-collection/export-template.png)

2. Selecionar **implantar**

    ![Botão implantar realçado em vermelho](media/ip-collection/deploy.png)

3. Selecione **Editar modelo**. (Se o modelo tiver Propriedades ou recursos adicionais que não aparecem neste modelo de exemplo, continue com cuidado para garantir que todos os recursos aceitem a implantação do modelo como uma alteração/atualização incremental.)

    ![Editar Modelo](media/ip-collection/edit-template.png)

4. Faça as seguintes alterações no JSON para o recurso e, em seguida, clique em **salvar**:

    ![A captura de tela adiciona uma vírgula após "IbizaAIExtension" e adiciona uma nova linha abaixo com "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Se você tiver um erro que diz: ** _o grupo de recursos está em um local que não tem suporte de um ou mais recursos no modelo. Escolha um grupo de recursos diferente._** Selecione temporariamente um grupo de recursos diferente na lista suspensa e, em seguida, selecione novamente o grupo de recursos original para resolver o erro.

5. Selecione **concordo** > **compra**. 

    ![Editar Modelo](media/ip-collection/purchase.png)

    Nesse caso, nada de novo está sendo comprado, estamos apenas atualizando a configuração do recurso de Application Insights existente.

6. Depois que a implantação for concluída, novos dados de telemetria serão registrados.

    Se você fosse selecionar e editar o modelo novamente, só veria o modelo padrão e não veria sua propriedade recém-adicionada e seu valor associado. Se você não estiver vendo os dados de endereço IP e quiser `"DisableIpMasking": true` confirmar que está definido. Execute o seguinte PowerShell: (substitua `Fabrikam-dev` pelo recurso apropriado e o nome do grupo de recursos.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Uma lista de propriedades será retornada como resultado. Uma das propriedades deve ser lida `DisableIpMasking: true`. Se você executar o PowerShell antes de implantar a nova propriedade com Azure Resource Manager, a propriedade não existirá.

### <a name="rest-api"></a>API Rest

A carga da [API REST](https://docs.microsoft.com/rest/api/azure/) para fazer as mesmas modificações é a seguinte:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializador de telemetria

Se precisar de uma alternativa mais flexível do `DisableIpMasking` que registrar todo ou parte dos endereços IP, você poderá usar um [inicializador de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) para copiar todo ou parte do IP para um campo personalizado. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Se não for possível acessar `ISupportProperties`o, verifique e verifique se você está executando a versão estável mais recente do SDK do Application insights. `ISupportProperties`são destinadas a valores de cardinalidade alta `GlobalProperties` , enquanto são mais apropriadas para valores de cardinalidade baixa, como nome da região, nome do ambiente, etc. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Habilitar inicializador de telemetria para ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Habilitar inicializador de telemetria para ASP.NET Core

Você pode criar seu inicializador de telemetria da mesma maneira para ASP.NET Core como ASP.NET, mas para habilitar o inicializador, use o exemplo a seguir para referência:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript do lado do cliente

Ao contrário dos SDKs do lado do servidor, o SDK do JavaScript do lado do cliente não calcula o endereço IP. Por padrão, o cálculo de endereço IP para telemetria do lado do cliente é executado no ponto de extremidade de ingestão no Azure na chegada da telemetria. Isso significa que, se você estivesse enviando dados do lado do cliente para um proxy e, em seguida, encaminhando para o ponto de extremidade de ingestão, o cálculo do endereço IP pode mostrar o endereço IP do proxy e não o cliente. Se nenhum proxy for usado, isso não deve ser um problema.

Se você quiser calcular o endereço IP diretamente no lado do cliente, precisará adicionar sua própria lógica personalizada para executar esse cálculo e usar o resultado para definir a `ai.location.ip` marca. Quando `ai.location.ip` é definido, o cálculo de endereço IP não é executado pelo ponto de extremidade de ingestão e o endereço IP fornecido é respeitado e usado para executar a pesquisa geográfica. Nesse cenário, o endereço IP ainda será zerado por padrão. 

Para manter todo o endereço IP calculado de sua lógica personalizada, você pode usar um inicializador de telemetria que copiaria os dados de endereço `ai.location.ip` IP que você forneceu em um campo personalizado separado. Mas, novamente, diferentemente dos SDKs do lado do servidor, sem depender de bibliotecas de terceiros ou sua própria lógica de coleção de IPS personalizada do lado do cliente, o SDK do lado do cliente não calculará o IP para você.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Exibir os resultados do inicializador de telemetria

Se você disparar o novo tráfego em seu site e aguardar aproximadamente 2-5 minutos para garantir que ele tenha tempo de ser ingerido, você poderá executar uma consulta Kusto para ver se a coleção de endereços IP está funcionando:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Endereços IP recentemente coletados devem aparecer na `customDimensions_client-ip` coluna. A coluna `client-ip` padrão ainda terá quatro octetos zerados ou apenas exibindo os três primeiros octetos, dependendo de como você configurou a coleta de endereços IP no nível do componente. Se você estiver testando localmente após implementar o inicializador de telemetria e o `customDimensions_client-ip` valor `::1` que você vê para o for, esse é o comportamento esperado. `::1`representa o endereço de loopback no IPv6. É equivalente a `127.0.01` no IPv4 e é o resultado que você verá ao testar a partir do localhost.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [coleta de dados pessoais](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) no Application insights.

* Saiba mais sobre como a [coleta de endereços IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) no Application insights funciona. (Esta é uma postagem de blog externa mais antiga escrita por um de nossos engenheiros. Ele atinge o comportamento padrão atual em que o endereço IP é registrado como `0.0.0.0`, mas ele entra em mais detalhes sobre a mecânica do interno `ClientIpHeaderTelemetryInitializer`.)
