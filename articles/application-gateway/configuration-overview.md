---
title: Visão geral da configuração do gateway de Aplicativo Azure
description: Este artigo descreve como configurar os componentes do gateway de Aplicativo Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 046946bb9d3ce1ae86d49409d024c862d2edb982
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856064"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração do gateway de aplicativo

Aplicativo Azure gateway consiste em vários componentes que podem ser configurados de várias maneiras para cenários diferentes. Este artigo mostra como configurar cada componente.

![Gráfico de fluxo de componentes do gateway de aplicativo](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra um aplicativo que tem três ouvintes. Os dois primeiros são ouvintes multissite para `http://acme.com/*` e `http://fabrikam.com/*`, respectivamente. Ambos escutam na porta 80. O terceiro é um ouvinte básico que tem terminação de TLS (segurança de camada de transporte) de ponta a ponta, anteriormente conhecido como terminação de protocolo SSL (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual do Azure e sub-rede dedicada

Um gateway de aplicativo é uma implantação dedicada em sua rede virtual. Em sua rede virtual, uma sub-rede dedicada é necessária para o gateway de aplicativo. Você pode ter várias instâncias de uma determinada implantação do gateway de aplicativo em uma sub-rede. Você também pode implantar outros gateways de aplicativo na sub-rede. Mas você não pode implantar nenhum outro recurso na sub-rede do gateway de aplicativo.

> [!NOTE]
> Você não pode misturar Standard_v2 e o gateway de Aplicativo Azure padrão na mesma sub-rede.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O gateway de aplicativo usa um endereço IP privado por instância, mais outro endereço IP privado se um IP de front-end privado estiver configurado.

O Azure também reserva cinco endereços IP em cada sub-rede para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicativo sem um IP de front-end privado. Você precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias do gateway de aplicativo. Portanto, você precisa de um tamanho de sub-rede/27 ou maior.

Considere uma sub-rede que tenha 27 instâncias de gateway de aplicativo e um endereço IP para um IP de front-end privado. Nesse caso, você precisa de 33 endereços IP: 27 para as instâncias do gateway de aplicativo, uma para o front-end privado e cinco para uso interno. Portanto, você precisa de um tamanho de sub-rede/26 ou maior.

Recomendamos que você use um tamanho de sub-rede de pelo menos/28. Esse tamanho fornece a você 11 endereços IP utilizáveis. Se a carga do aplicativo exigir mais de 10 instâncias do gateway de aplicativo, considere um tamanho de sub-rede/27 ou/26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na sub-rede do gateway de aplicativo

Os NSGs (grupos de segurança de rede) têm suporte no gateway de aplicativo. Mas há algumas restrições:

- Você deve permitir o tráfego de entrada na Internet nas portas TCP 65503-65534 para o SKU do gateway de aplicativo v1 e as portas TCP 65200-65535 para a SKU V2 com a sub-rede de destino como qualquer e origem como **uma** marca de serviço do **gatewaymanager** . Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) pelos certificados do Azure. Entidades externas, incluindo os clientes desses gateways, não podem se comunicar nesses pontos de extremidade.

- A conectividade de internet de saída não pode ser bloqueada. As regras de saída padrão no NSG permitem a conectividade com a Internet. É recomendável que você:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que neguem qualquer conectividade de saída.

- O tráfego da marca **AzureLoadBalancer** deve ser permitido.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso do gateway de aplicativo a alguns IPs de origem

Para este cenário, use NSGs na sub-rede do gateway de aplicativo. Coloque as seguintes restrições na sub-rede nesta ordem de prioridade:

1. Permita o tráfego de entrada de um IP de origem ou intervalo de IP com o destino como o intervalo de endereços de sub-rede do gateway de aplicativo inteiro e a porta de destino como sua porta de acesso de entrada, por exemplo, a porta 80 para acesso HTTP.
2. Permitir solicitações de entrada da origem como **uma** marca de serviço do **gatewaymanager** e destino como portas de destino e como 65503-65534 para a SKU do gateway de aplicativo v1 e portas 65200-65535 para SKU v2 para [comunicação de status de integridade de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) pelos certificados do Azure. Sem os certificados apropriados em vigor, as entidades externas não podem iniciar alterações nesses pontos de extremidade.
3. Permitir investigações de Azure Load Balancer de entrada (marca*AzureLoadBalancer* ) e tráfego de rede virtual de entrada (marca*VirtualNetwork* ) no [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquear todos os outros tráfegos de entrada usando uma regra negar-tudo.
5. Permitir tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>As rotas definidas pelo usuário são suportadas na sub-rede do Gateway de Aplicativo

> [!IMPORTANT]
> Usar UDRs na sub-rede do gateway de aplicativo pode fazer com que o status de integridade na [exibição de integridade do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) seja exibido como **desconhecido**. Ele também pode causar falha na geração de logs e métricas do gateway de aplicativo. Recomendamos que você não use UDRs na sub-rede do gateway de aplicativo para poder exibir a integridade, os logs e as métricas de back-end.

- **v1**

   Para a SKU v1, as rotas definidas pelo usuário (UDRs) têm suporte na sub-rede do gateway de aplicativo, desde que elas não alterem a comunicação de solicitação/resposta de ponta a ponta. Por exemplo, você pode configurar um UDR na sub-rede do gateway de aplicativo para apontar para um dispositivo de firewall para inspeção de pacotes. Mas você deve certificar-se de que o pacote pode atingir o destino pretendido após a inspeção. A falha em fazer isso pode resultar em comportamento incorreto de investigação de integridade ou roteamento de tráfego. Isso inclui rotas aprendidas ou rotas 0.0.0.0/0 padrão que são propagadas pelo Azure ExpressRoute ou gateways de VPN na rede virtual.

- **v2**

   Para a SKU v2, há cenários com e sem suporte:

   **v2 cenários com suporte**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rotas pode resultar no roteamento assimétrico no gateway de aplicativo v2. Verifique se todo o tráfego do plano de gerenciamento/controle é enviado diretamente para a Internet e não por meio de um dispositivo virtual. O log e as métricas também podem ser afetados.


  **Cenário 1**: UDR para desabilitar a propagação de rota de Border Gateway Protocol (BGP) para a sub-rede do gateway de aplicativo

   Às vezes, a rota de gateway padrão (0.0.0.0/0) é anunciada por meio dos gateways de ExpressRoute ou VPN associados à rede virtual do gateway de aplicativo. Isso interrompe o tráfego do plano de gerenciamento, que requer um caminho direto para a Internet. Nesses cenários, um UDR pode ser usado para desabilitar a propagação de rota BGP. 

   Para desabilitar a propagação de rota BGP, use as seguintes etapas:

   1. Criar um recurso de tabela de rotas no Azure.
   2. Desabilite o parâmetro de **propagação de rota do gateway de rede virtual** . 
   3. Associe a tabela de rotas à sub-rede apropriada. 

   Habilitar o UDR para esse cenário não deve interromper nenhuma configuração existente.

  **Cenário 2**: UDR para direcionar 0.0.0.0/0 para a Internet

   Você pode criar um UDR para enviar o tráfego 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para o serviço kubernetes do Azure com kubenet

  Se você estiver usando o kubenet com o AKS (serviço kubernetes do Azure) e o AGIC (controlador de entrada do gateway de aplicativo), precisará de uma tabela de rotas para permitir que o tráfego enviado para o pods do gateway de aplicativo seja roteado para o nó correto. Isso não será necessário se você usar o CNI do Azure. 

  Para usar a tabela de rotas para permitir que o kubenet funcione, siga as etapas abaixo:

  1. Vá para o grupo de recursos criado por AKS (o nome do grupo de recursos deve começar com "MC_")
  2. Localize a tabela de rotas criada por AKS nesse grupo de recursos. A tabela de rotas deve ser preenchida com as seguintes informações:
     - O prefixo de endereço deve ser o intervalo de IP do pods que você deseja alcançar no AKS. 
     - O tipo do próximo salto deve ser dispositivo virtual. 
     - O endereço do próximo salto deve ser o endereço IP do nó que hospeda o pods.
  3. Associe essa tabela de rotas à sub-rede do gateway de aplicativo. 
    
  **v2 cenários sem suporte**

  **Cenário 1**: UDR para dispositivos virtuais

  Qualquer cenário em que o 0.0.0.0/0 precisa ser redirecionado por meio de qualquer dispositivo virtual, uma rede virtual hub/spoke ou local (túnel forçado) não tem suporte para a v2.

## <a name="front-end-ip"></a>IP de front-end

Você pode configurar o gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um IP público é necessário quando você hospeda um back-end que os clientes devem acessar pela Internet por meio de um VIP (IP virtual) voltado para a Internet. 

Um IP público não é necessário para um ponto de extremidade interno que não esteja exposto à Internet. Isso é conhecido como ponto de extremidade ILB ( *balanceador de carga interno* ) ou IP de front-end privado. Um ILB de gateway de aplicativo é útil para aplicativos de linha de negócios internos que não são expostos à Internet. Ele também é útil para serviços e camadas em um aplicativo de várias camadas dentro de um limite de segurança que não é exposto à Internet, mas que exigem distribuição de carga Round Robin, adesão de sessão ou terminação de TLS.

Apenas 1 endereço IP público ou um endereço IP privado tem suporte. Você escolhe o IP de front-end ao criar o gateway de aplicativo.

- Para um IP público, você pode criar um novo endereço IP público ou usar um IP público existente no mesmo local que o gateway de aplicativo. Para obter mais informações, consulte [endereço IP público estático vs. dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um IP privado, você pode especificar um endereço IP privado da sub-rede em que o gateway de aplicativo é criado. Se você não especificar um, um endereço IP arbitrário será selecionado automaticamente da sub-rede. O tipo de endereço IP que você selecionar (estático ou dinâmico) não poderá ser alterado posteriormente. Para obter mais informações, consulte [criar um gateway de aplicativo com um balanceador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP de front-end é associado a um *ouvinte*, que verifica as solicitações de entrada no IP de front-end.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão de entrada usando a porta, o protocolo, o host e o endereço IP. Ao configurar o ouvinte, você deve inserir valores para eles que correspondam aos valores correspondentes na solicitação de entrada no gateway.

Ao criar um gateway de aplicativo usando o portal do Azure, você também cria um ouvinte padrão escolhendo o protocolo e a porta para o ouvinte. Você pode escolher se deseja habilitar o suporte do HTTP2 no ouvinte. Depois de criar o gateway de aplicativo, você pode editar as configurações desse ouvinte padrão (*appGatewayHttpListener*) ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Ao criar um novo ouvinte, você escolhe entre [ *básico* e *multissite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se você quiser que todas as suas solicitações (para qualquer domínio) sejam aceitas e encaminhadas para pools de back-end, escolha básico. Saiba [como criar um gateway de aplicativo com um ouvinte básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se você quiser encaminhar solicitações para diferentes pools de back-end com base no cabeçalho ou nome do *host* , escolha ouvinte multissite, em que você também deve especificar um nome de host que corresponda à solicitação de entrada. Isso ocorre porque o gateway de aplicativo depende de cabeçalhos de host HTTP 1,1 para hospedar mais de um site na mesma porta e endereço IP público.

#### <a name="order-of-processing-listeners"></a>Ordem de ouvintes de processamento

Para a SKU v1, as solicitações são correspondidas de acordo com a ordem das regras e o tipo de ouvinte. Se uma regra com um ouvinte básico vier primeiro na ordem, ela será processada primeiro e aceitará qualquer solicitação para essa porta e combinação de IP. Para evitar isso, configure as regras com os ouvintes de vários sites primeiro e envie por push a regra com o ouvinte básico para o último na lista.

Para a SKU v2, os ouvintes multissite são processados antes dos ouvintes básicos.

### <a name="front-end-ip"></a>IP de front-end

Escolha o endereço IP de front-end que você planeja associar a este ouvinte. O ouvinte ouvirá as solicitações de entrada neste IP.

### <a name="front-end-port"></a>Porta de front-end

Escolha a porta de front-end. Selecione uma porta existente ou crie uma nova. Escolha qualquer valor do [intervalo permitido de portas](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Você pode usar não apenas portas bem conhecidas, como 80 e 443, mas qualquer porta personalizada que seja adequada. Uma porta pode ser usada para ouvintes voltados para o público ou para ouvintes de face privada.

### <a name="protocol"></a>Protocolo

Escolha HTTP ou HTTPS:

- Se você escolher HTTP, o tráfego entre o cliente e o gateway de aplicativo será descriptografado.

- Escolha HTTPS se desejar a [rescisão](features.md#secure-sockets-layer-ssltls-termination) de TLS ou [criptografia TLS de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). O tráfego entre o cliente e o gateway de aplicativo é criptografado. E a conexão TLS é encerrada no gateway de aplicativo. Se desejar a criptografia TLS de ponta a ponta, você deverá escolher HTTPS e definir a configuração de **http de back-end** . Isso garante que o tráfego seja criptografado novamente quando ele viajar do gateway de aplicativo para o back-end.


Para configurar a rescisão de TLS e criptografia TLS de ponta a ponta, você deve adicionar um certificado ao ouvinte para permitir que o gateway de aplicativo derive uma chave simétrica. Isso é ditado pela especificação do protocolo TLS. A chave simétrica é usada para criptografar e descriptografar o tráfego que é enviado para o gateway. O certificado de gateway deve estar no formato PFX (troca de informações pessoais). Esse formato permite exportar a chave privada que o gateway usa para criptografar e descriptografar o tráfego.

#### <a name="supported-certificates"></a>Certificados com suporte

Consulte [certificados com suporte para terminação de TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte a protocolo adicional

#### <a name="http2-support"></a>Suporte do HTTP2

O suporte ao protocolo HTTP/2 está disponível para clientes que se conectam somente a ouvintes do gateway de aplicativo. A comunicação com os pools de servidores back-end é por HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. O trecho de código Azure PowerShell a seguir mostra como habilitar isso:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte para WebSocket

O suporte ao WebSocket está habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitá-la ou desabilitá-la. Você pode usar Websockets com ouvintes HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Você pode definir um erro personalizado no nível global ou no nível do ouvinte. No momento, não há suporte para a criação de páginas de erro personalizadas de nível global do portal do Azure. Você pode configurar uma página de erro personalizada para um erro de firewall do aplicativo Web 403 ou uma página de manutenção de 502 no nível do ouvinte. Você também deve especificar uma URL de blob publicamente acessível para o código de status de erro fornecido. Para obter mais informações, confira [Criar páginas de erro personalizadas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [configuração de Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Política TLS

Você pode centralizar o gerenciamento de certificados TLS/SSL e reduzir a sobrecarga de descriptografia de criptografia para um farm de servidores back-end. A manipulação de TLS centralizado também permite que você especifique uma política TLS central adequada aos seus requisitos de segurança. Você pode escolher a política TLS *padrão*, *predefinida*ou *personalizada* .

Você configura a política TLS para controlar as versões do protocolo TLS. Você pode configurar um gateway de aplicativo para usar uma versão mínima de protocolo para Handshakes de TLS do TLS 1.0, TLS 1.1 e TLS 1.2. Por padrão, o SSL 2,0 e o 3,0 estão desabilitados e não são configuráveis. Para obter mais informações, consulte [visão geral da política de TLS do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, você o associa a uma regra de roteamento de solicitação. Essa regra determina como as solicitações recebidas no ouvinte são roteadas para o back-end.

## <a name="request-routing-rules"></a>Regras de roteamento de solicitação

Ao criar um gateway de aplicativo usando o portal do Azure, você cria uma regra padrão (*rule1*). Essa regra associa o ouvinte padrão (*appGatewayHttpListener*) ao pool de back-ends padrão (*appGatewayBackendPool*) e as configurações de http de back-end padrão (*appgatewaybackendhttp*). Depois de criar o gateway, você pode editar as configurações da regra padrão ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Ao criar uma regra, você escolhe entre [ *básica* e *baseada em caminho*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Escolha básico se desejar encaminhar todas as solicitações no ouvinte associado (por exemplo, *blog<i></i>. contoso.com/\*)* a um único pool de back-ends.
- Escolha baseado em caminho se desejar rotear solicitações de caminhos de URL específicos para pools de back-end específicos. O padrão de caminho é aplicado somente ao caminho da URL, não aos seus parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para a SKU v1, a correspondência de padrões de solicitações de entrada é processada na ordem em que os caminhos são listados no mapa de caminho de URL da regra com base no caminho. Se uma solicitação corresponder ao padrão em dois ou mais caminhos no mapa de caminho, o caminho listado primeiro será correspondido. E a solicitação é encaminhada para o back-end associado a esse caminho.

Para a SKU v2, uma correspondência exata é de prioridade mais alta que a ordem de caminho no mapa de caminho de URL. Se uma solicitação corresponder ao padrão em dois ou mais caminhos, a solicitação será encaminhada para o back-end associado ao caminho que corresponde exatamente à solicitação. Se o caminho na solicitação de entrada não corresponder exatamente a nenhum caminho no mapa, a correspondência de padrões da solicitação será processada na lista de ordem do mapa de caminho para a regra com base no caminho.

### <a name="associated-listener"></a>Ouvinte associado

Associe um ouvinte à regra para que a *regra de roteamento de solicitação* associada ao ouvinte seja avaliada para determinar o pool de back-ends para o qual rotear a solicitação.

### <a name="associated-back-end-pool"></a>Pool de back-ends associado

Associe à regra o pool de back-end que contém os destinos de back-end que atendem às solicitações que o ouvinte recebe.

 - Para uma regra básica, apenas um pool de back-end é permitido. Todas as solicitações no ouvinte associado são encaminhadas para esse pool de back-end.

 - Para uma regra com base em caminho, adicione vários pools de back-end que correspondem a cada caminho de URL. As solicitações que correspondem ao caminho de URL inserido são encaminhadas para o pool de back-ends correspondente. Além disso, adicione um pool de back-ends padrão. As solicitações que não correspondem a nenhum caminho de URL na regra são encaminhadas para esse pool.

### <a name="associated-back-end-http-setting"></a>Configuração de HTTP de back-end associada

Adicione uma configuração de HTTP de back-end para cada regra. As solicitações são roteadas do gateway de aplicativo para os destinos de back-end usando o número da porta, o protocolo e outras informações especificadas nessa configuração.

Para uma regra básica, apenas uma configuração de HTTP de back-end é permitida. Todas as solicitações no ouvinte associado são encaminhadas para os destinos de back-end correspondentes usando essa configuração de HTTP.

Para uma regra com base em caminho, adicione várias configurações de HTTP de back-end que correspondam a cada caminho de URL. As solicitações que correspondem ao caminho da URL nessa configuração são encaminhadas para os destinos de back-end correspondentes usando as configurações de HTTP que correspondem a cada caminho de URL. Além disso, adicione uma configuração HTTP padrão. As solicitações que não correspondem a nenhum caminho de URL nesta regra são encaminhadas para o pool de back-ends padrão usando a configuração HTTP padrão.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento estiver configurado para uma regra básica, todas as solicitações no ouvinte associado serão redirecionadas para o destino. Esse é o redirecionamento *global* . Se o redirecionamento estiver configurado para uma regra baseada em caminho, somente as solicitações em uma área específica do site serão redirecionadas. Um exemplo é uma área de carrinho de compras que é denotada por */cart/\**. Esse é o redirecionamento *baseado em caminho* .

Para obter mais informações sobre redirecionamentos, consulte [visão geral do redirecionamento do gateway de aplicativo](redirect-overview.md).

#### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de redirecionamento necessário: *permanente (301)*, *temporário (307)*, *encontrado (302)* ou *Consulte outro (303)*.

#### <a name="redirection-target"></a>Destino de redirecionamento

Escolha outro ouvinte ou um site externo como o destino de redirecionamento.

##### <a name="listener"></a>Ouvinte

Escolha o ouvinte como o destino de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Essa configuração é necessária quando você deseja habilitar o redirecionamento de HTTP para HTTPS. Ele redireciona o tráfego do ouvinte de origem que verifica as solicitações HTTP de entrada para o ouvinte de destino que verifica as solicitações HTTPS de entrada. Você também pode optar por incluir a cadeia de caracteres de consulta e o caminho da solicitação original na solicitação que é encaminhada para o destino de redirecionamento.

![Caixa de diálogo componentes do gateway de aplicativo](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre o redirecionamento de HTTP para HTTPS, consulte:
- [Redirecionamento de HTTP para HTTPS usando o portal do Azure](redirect-http-to-https-portal.md)
- [Redirecionamento de HTTP para HTTPS usando o PowerShell](redirect-http-to-https-powershell.md)
- [Redirecionamento de HTTP para HTTPS usando o CLI do Azure](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Site externo

Escolha site externo quando desejar redirecionar o tráfego no ouvinte associado a essa regra a um site externo. Você pode optar por incluir a cadeia de caracteres de consulta da solicitação original na solicitação que é encaminhada para o destino de redirecionamento. Não é possível encaminhar o caminho para o site externo que estava na solicitação original.

Para obter mais informações sobre o redirecionamento, consulte:
- [Redirecionar o tráfego para um site externo usando o PowerShell](redirect-external-site-powershell.md)
- [Redirecionar o tráfego para um site externo usando a CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Reescrever a configuração do cabeçalho HTTP

Essa configuração adiciona, remove ou atualiza cabeçalhos HTTP de solicitação e resposta, enquanto os pacotes de solicitação e resposta são movidos entre os pools de cliente e de back-end. Para obter mais informações, consulte:

 - [Visão geral de reescrever cabeçalhos HTTP](rewrite-http-headers.md)
 - [Configurar a regravação do cabeçalho HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Configurações de HTTP

O gateway de aplicativo roteia o tráfego para os servidores back-end usando a configuração que você especificar aqui. Depois de criar uma configuração de HTTP, você deve associá-la a uma ou mais regras de roteamento de solicitação.

### <a name="cookie-based-affinity"></a>Afinidade baseada em cookie

Aplicativo Azure gateway usa cookies gerenciados por gateway para manter sessões de usuário. Quando um usuário envia a primeira solicitação ao gateway de aplicativo, ele define um cookie de afinidade na resposta com um valor de hash que contém os detalhes da sessão, para que as solicitações subsequentes que tenham o cookie de afinidade sejam roteadas para o mesmo servidor de back-end para manter a adesão. 

Esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo servidor e quando o estado da sessão é salvo localmente no servidor para uma sessão de usuário. Se o aplicativo não puder lidar com a afinidade baseada em cookie, você não poderá usar esse recurso. Para usá-lo, verifique se os clientes dão suporte a cookies.

A atualização do [Chromium Browser](https://www.chromium.org/Home) [V80](https://chromiumdash.appspot.com/schedule) colocou um mandato em que os cookies http sem o atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) têm que ser tratados como SameSite = LAX. No caso de solicitações CORS (compartilhamento de recursos entre origens), se o cookie tiver que ser enviado em um contexto de terceiros, ele precisará usar *SameSite = None; Proteja* os atributos e eles devem ser enviados somente por HTTPS. Caso contrário, em um cenário somente HTTP, o navegador não enviará os cookies no contexto de terceiros. O objetivo dessa atualização do Chrome é aprimorar a segurança e evitar ataques CSRF (solicitação entre sites forjada). 

Para dar suporte a essa alteração, a partir de fevereiro de 17 2020, o gateway de aplicativo (todos os tipos de SKU) injetará outro cookie chamado *ApplicationGatewayAffinityCORS* além do cookie *ApplicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele (*"SameSite = None; Proteger "*) para que a sessão adesiva seja mantida até mesmo para solicitações entre origens.

Observe que o nome do cookie de afinidade padrão é *ApplicationGatewayAffinity* e você pode alterá-lo. Caso você esteja usando um nome de cookie de afinidade personalizado, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite = None* estiver definido, é obrigatório que o cookie também contenha o sinalizador de *segurança* e deve ser enviado via HTTPS.  Se a afinidade de sessão for necessária em CORS, você deverá migrar sua carga de trabalho para HTTPS. Consulte o descarregamento de TLS e a documentação de TLS de ponta a ponta para o gateway de aplicativo aqui – [visão geral](ssl-overview.md), [configurar um gateway de aplicativo com terminação tls usando o portal do Azure](create-ssl-portal.md), [configurar TLS de ponta a ponta usando o gateway de aplicativo com o portal](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Descarregamento de conexão

O descarregamento de conexão ajuda você a remover normalmente os membros do pool de back-end durante as atualizações de serviço planejadas. Você pode aplicar essa configuração a todos os membros de um pool de back-ends durante a criação da regra. Ele garante que todas as instâncias de cancelamento de registro de um pool de back-end continuem a manter as conexões existentes e atendem a solicitações em andamento para um tempo limite configurável e não recebam novas solicitações ou conexões. A única exceção a isso são solicitações associadas para cancelar o registro de instâncias devido à afinidade de sessão gerenciada pelo gateway e continuarão sendo encaminhadas para as instâncias de cancelamento de registro. O descarregamento de conexão se aplica a instâncias de back-end que são explicitamente removidas do pool de back-end.

### <a name="protocol"></a>Protocolo

O gateway de aplicativo dá suporte a HTTP e HTTPS para roteamento de solicitações para os servidores back-end. Se você escolher HTTP, o tráfego para os servidores back-end será descriptografado. Se a comunicação não criptografada não for aceitável, escolha HTTPS.

Essa configuração combinada com HTTPS no ouvinte dá suporte a [TLS de ponta a ponta](ssl-overview.md). Isso permite que você transmita com segurança dados confidenciais criptografados para o back-end. Cada servidor back-end no pool de back-end que tem o TLS de ponta a ponta habilitado deve ser configurado com um certificado para permitir a comunicação segura.

### <a name="port"></a>Porta

Essa configuração especifica a porta em que os servidores back-end escutam o tráfego do gateway de aplicativo. Você pode configurar portas que variam de 1 a 65535.

### <a name="request-timeout"></a>Tempo limite da solicitação

Essa configuração é o número de segundos que o gateway de aplicativo aguarda para receber uma resposta do servidor back-end.

### <a name="override-back-end-path"></a>Substituir caminho de back-end

Essa configuração permite que você configure um caminho de encaminhamento personalizado opcional para usar quando a solicitação for encaminhada para o back-end. Qualquer parte do caminho de entrada que corresponde ao caminho personalizado no campo **substituir caminho de back-end** é copiada para o caminho encaminhado. A tabela a seguir mostra como esse recurso funciona:

- Quando a configuração HTTP é anexada a uma regra básica de roteamento de solicitação:

  | Solicitação original  | Substituir caminho de back-end | Solicitação encaminhada para o back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /Override.            | /override/home/              |
  | /home/secondhome/ | /Override.            | /override/home/secondhome/   |

- Quando a configuração HTTP é anexada a uma regra de roteamento de solicitação baseada em caminho:

  | Solicitação original           | Regra de caminho       | Substituir caminho de back-end | Solicitação encaminhada para o back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | /Override.            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | /Override.            | /override/home/secondhome/   |
  | /Home/                     | pathrule      | /Override.            | /override/home/              |
  | /home/secondhome/          | pathrule      | /Override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /Override.            | /Override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /Override.            | /override/secondhome/        |
  | pathrule                 | pathrule      | /Override.            | /Override.                   |

### <a name="use-for-app-service"></a>Usar para o serviço de aplicativo

Esse é apenas um atalho de interface do usuário que seleciona as duas configurações necessárias para o back-end do serviço de Azure App. Ele permite **escolher o nome do host do endereço de back-end**e cria uma nova investigação personalizada, se você ainda não tiver uma. (Para obter mais informações, consulte a seção [escolher nome do host do endereço de back-end](#pick) deste artigo.) Uma nova investigação é criada e o cabeçalho de investigação é escolhido do endereço do membro de back-end.

### <a name="use-custom-probe"></a>Usar investigação personalizada

Essa configuração associa uma [investigação personalizada](application-gateway-probe-overview.md#custom-health-probe) a uma configuração de http. Você pode associar apenas uma investigação personalizada a uma configuração de HTTP. Se você não associar explicitamente uma investigação personalizada, a [investigação padrão](application-gateway-probe-overview.md#default-health-probe-settings) será usada para monitorar a integridade do back-end. Recomendamos que você crie uma investigação personalizada para maior controle sobre o monitoramento de integridade de seus back-ends.

> [!NOTE]
> A investigação personalizada não monitora a integridade do pool de back-end, a menos que a configuração de HTTP correspondente esteja explicitamente associada a um ouvinte.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Escolher o nome do host do endereço de back-end

Esse recurso define dinamicamente o cabeçalho de *host* na solicitação para o nome de host do pool de back-ends. Ele usa um endereço IP ou FQDN.

Esse recurso ajuda quando o nome de domínio do back-end é diferente do nome DNS do gateway de aplicativo e o back-end depende de um cabeçalho de host específico para resolver para o ponto de extremidade correto.

Um caso de exemplo são os serviços multilocatários como o back-end. Um serviço de aplicativo é um serviço de vários locatários que usa um espaço compartilhado com um único endereço IP. Portanto, um serviço de aplicativo só pode ser acessado por meio de nomes de host que são configurados nas configurações de domínio personalizado.

Por padrão, o nome de domínio personalizado é *example.azurewebsites.net*. Para acessar o serviço de aplicativo usando um gateway de aplicativo por meio de um nome de host que não está explicitamente registrado no serviço de aplicativo ou por meio do FQDN do gateway de aplicativo, você substitui o nome do host na solicitação original para o nome de host do serviço de aplicativo. Para fazer isso, habilite a configuração **escolher nome do host de endereço de back-end** .

Para um domínio personalizado cujo nome DNS personalizado existente está mapeado para o serviço de aplicativo, você não precisa habilitar essa configuração.

> [!NOTE]
> Essa configuração não é necessária para Ambiente do Serviço de Aplicativo, que é uma implantação dedicada.

### <a name="host-name-override"></a>Substituição do nome do host

Esse recurso substitui o cabeçalho de *host* na solicitação de entrada no gateway de aplicativo pelo nome de host que você especificar.

Por exemplo, se *www.contoso.com* for especificado na configuração do **nome do host** , a solicitação original`https://appgw.eastus.cloudapp.azure.com/path1` * será alterada para`https://www.contoso.com/path1` * quando a solicitação for encaminhada para o servidor back-end.

## <a name="back-end-pool"></a>Pool de back-end

Você pode apontar um pool de back-ends para quatro tipos de membros de back-end: uma máquina virtual específica, um conjunto de dimensionamento de máquinas virtuais, um endereço IP/FQDN ou um serviço de aplicativo. 

Depois de criar um pool de back-end, você deve associá-lo a uma ou mais regras de roteamento de solicitação. Você também deve configurar investigações de integridade para cada pool de back-ends no seu gateway de aplicativo. Quando uma condição de regra de roteamento de solicitação é atendida, o gateway de aplicativo encaminha o tráfego para os servidores íntegros (conforme determinado pelas investigações de integridade) no pool de back-ends correspondente.

## <a name="health-probes"></a>Investigações de integridade

Um gateway de aplicativo monitora a integridade de todos os recursos em seu back-end por padrão. Mas é altamente recomendável que você crie uma investigação personalizada para cada configuração de HTTP de back-end para obter maior controle sobre o monitoramento de integridade. Para saber como configurar uma investigação personalizada, consulte [configurações personalizadas de investigação de integridade](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma investigação de integridade personalizada, você precisa associá-la a uma configuração de HTTP de back-end. Uma investigação personalizada não monitorará a integridade do pool de back-end, a menos que a configuração de HTTP correspondente esteja explicitamente associada a um ouvinte usando uma regra.

## <a name="next-steps"></a>Próximas etapas

Agora que você conhece os componentes do gateway de aplicativo, você pode:

- [Criar um gateway de aplicativo no portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo usando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)
