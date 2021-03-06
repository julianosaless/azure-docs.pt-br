---
title: Restringir o tráfego de saída no serviço kubernetes do Azure (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no serviço de kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: d723f7b1e7331e65d17dca5873b891ec46d76c0e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207166"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlar o tráfego de saída para nós de cluster no serviço de kubernetes do Azure (AKS)

Por padrão, os clusters AKS têm acesso irrestrito à Internet (saída). Esse nível de acesso à rede permite que os nós e serviços que você executa acessem recursos externos, conforme necessário. Se você quiser restringir o tráfego de saída, um número limitado de portas e endereços deve ser acessível para manter tarefas de manutenção de cluster íntegras. O cluster é configurado por padrão para usar apenas as imagens de contêiner do sistema base do MCR (registro de contêiner da Microsoft) ou do ACR (registro de contêiner do Azure). Configure o firewall e as regras de segurança preferenciais para permitir essas portas e endereços necessários.

Este artigo detalha quais portas de rede e FQDNs (nomes de domínio totalmente qualificados) são necessários e opcionais se você restringir o tráfego de saída em um cluster AKS.

> [!IMPORTANT]
> Este documento aborda apenas como bloquear o tráfego deixando a sub-rede AKS. AKS não tem nenhum requisito de ingresso.  Não há suporte para o bloqueio de tráfego de sub-rede interna usando NSGs (grupos de segurança de rede) e firewalls. Para controlar e bloquear o tráfego no cluster, use [as políticas de rede][network-policy].

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.66 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

Para fins operacionais e de gerenciamento, os nós em um cluster AKS precisam acessar determinadas portas e FQDNs (nomes de domínio totalmente qualificados). Essas ações podem ser comunicadas com o servidor de API ou para baixar e instalar os componentes principais do cluster kubernetes e as atualizações de segurança do nó. Por padrão, o tráfego de saída da Internet (saída) não é restrito para nós em um cluster AKS. O cluster pode extrair imagens de contêiner do sistema base de repositórios externos.

Para aumentar a segurança do cluster AKS, talvez você queira restringir o tráfego de saída. O cluster está configurado para extrair imagens de contêiner do sistema base de MCR ou ACR. Se você bloquear o tráfego de saída dessa maneira, defina portas específicas e FQDNs para permitir que os nós AKS se comuniquem corretamente com os serviços externos necessários. Sem essas portas autorizadas e FQDNs, seus nós AKS não podem se comunicar com o servidor de API nem instalar componentes principais.

Você pode usar o [Firewall do Azure][azure-firewall] ou um dispositivo de firewall de terceiros para proteger o tráfego de saída e definir as portas e os endereços necessários. O AKS não cria essas regras automaticamente para você. As portas e os endereços a seguir são para referência à medida que você cria as regras apropriadas no firewall de rede.

> [!IMPORTANT]
> Quando você usa o Firewall do Azure para restringir o tráfego de saída e criar uma UDR (rota definida pelo usuário) para forçar todo o tráfego de saída, certifique-se de criar uma regra DNAT apropriada no firewall para permitir corretamente o tráfego de entrada. Usar o Firewall do Azure com um UDR interrompe a configuração de entrada devido ao roteamento assimétrico. (O problema ocorre se a sub-rede AKs tem uma rota padrão que vai para o endereço IP privado do firewall, mas você está usando um serviço de entrada de balanceador de carga público ou kubernetes do tipo: Balancer). Nesse caso, o tráfego de entrada do balanceador de carga é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoração de estado, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Firewall do Azure com o balanceador de carga de entrada ou serviço, consulte [integrar o Firewall do Azure com o Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Você pode bloquear o tráfego para a porta TCP 9000, a porta TCP 22 e a porta UDP 1194 usando uma regra de rede entre os IP do nó de trabalho de saída e o IP para o servidor de API.

No AKS, há dois conjuntos de portas e endereços:

* As [portas e o endereço necessários para clusters AKs](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego de saída autorizado.
* Os [endereços e portas recomendados opcionais para clusters AKs](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços, como o Azure monitor, não funcionará corretamente. Examine esta lista de portas opcionais e FQDNs e autorize qualquer um dos serviços e componentes usados em seu cluster AKS.

> [!NOTE]
> Limitar o tráfego de saída funciona apenas em novos clusters AKS. Para clusters existentes, [Execute uma operação de atualização][aks-upgrade] de cluster `az aks upgrade` usando o comando antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters do AKS

As seguintes regras de rede/portas de saída são necessárias para um cluster AKS:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]: 443 será necessário se você tiver um aplicativo que precisa se comunicar com o servidor de API.  Essa alteração pode ser definida depois que o cluster é criado.
* A porta TCP *9000*, a porta TCP *22* e a porta UDP *1194* para que o Pod frontal do túnel se comunique com a extremidade do túnel no servidor de API.
    * Para obter mais especificações, consulte o **. HCP.\< Location\>. azmk8s.Io* e **. Execute.\< endereços\>location. azmk8s.Io* na tabela a seguir.
* Porta UDP *123* para sincronização de tempo de protocolo NTP (NTP) (nós do Linux).
* A porta UDP *53* para DNS também será necessária se você tiver o pods acessando diretamente o servidor de API.

As seguintes regras de FQDN/aplicativo são necessárias:

> [!IMPORTANT]
> ***. blob.Core.Windows.net e aksrepos.azurecr.Io** não são mais necessárias regras de FQDN para bloqueio de saída.  Para clusters existentes, [Execute uma operação de atualização][aks-upgrade] de cluster `az aks upgrade` usando o comando para remover essas regras.

> [!IMPORTANT]
> *. cdn.mscr.io foi substituído por *. data.mcr.microsoft.com para as regiões de nuvem pública do Azure. Atualize suas regras de firewall existentes para que as alterações entrem em vigor.

- Azure Global

| FQDN                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *. HCP. \<local\>. azmk8s.Io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| *. Execute. \<local\>. azmk8s.Io | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| *. cdn.mscr.io       | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| mcr.microsoft.com          | HTTPS: 443 | Esse endereço é necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, Moby, etc.) necessários para o funcionamento do cluster durante a atualização e a escala do cluster |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| management.azure.com       | HTTPS: 443 | Esse endereço é necessário para operações GET/PUT do kubernetes. |
| login.microsoftonline.com  | HTTPS: 443 | Esse endereço é necessário para Azure Active Directory autenticação. |
| ntp.ubuntu.com             | UDP: 123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS: 443 | Esse endereço é o repositório de pacotes da Microsoft usado para operações *apt-get* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Esse endereço é para o repositório necessário para instalar os binários necessários, como kubenet e CNI do Azure. |

- Azure China 21Vianet

| FQDN                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *. HCP. \<local\>. CX.Prod.Service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| *. Execute. \<local\>. CX.Prod.Service.azk8s.cn | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| *. azk8s.cn        | HTTPS: 443 | Esse endereço é necessário para baixar os binários e as imagens necessárias|
| mcr.microsoft.com          | HTTPS: 443 | Esse endereço é necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, Moby, etc.) necessários para o funcionamento do cluster durante a atualização e a escala do cluster |
| *. cdn.mscr.io       | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| management.chinacloudapi.cn       | HTTPS: 443 | Esse endereço é necessário para operações GET/PUT do kubernetes. |
| login.chinacloudapi.cn  | HTTPS: 443 | Esse endereço é necessário para Azure Active Directory autenticação. |
| ntp.ubuntu.com             | UDP: 123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS: 443 | Esse endereço é o repositório de pacotes da Microsoft usado para operações *apt-get* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |

- Azure Government

| FQDN                       | Porta      | Uso      |
|----------------------------|-----------|----------|
| *. HCP. \<local\>. CX.AKs.containerservice.Azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| *. Execute. \<local\>. CX.AKs.containerservice.Azure.us | HTTPS: 443, TCP: 22, TCP: 9000, UDP: 1194 | Esse endereço é necessário para a comunicação de servidor de API de > de < de nó. Substitua * \<o\> local* pela região onde o cluster AKs está implantado. |
| mcr.microsoft.com          | HTTPS: 443 | Esse endereço é necessário para acessar imagens no registro de contêiner da Microsoft (MCR). Esse registro contém gráficos/imagens de terceiros (por exemplo, Moby, etc.) necessários para o funcionamento do cluster durante a atualização e a escala do cluster |
|*. cdn.mscr.io              | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| *. data.mcr.microsoft.com             | HTTPS: 443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| management.usgovcloudapi.net       | HTTPS: 443 | Esse endereço é necessário para operações GET/PUT do kubernetes. |
| login.microsoftonline.us  | HTTPS: 443 | Esse endereço é necessário para Azure Active Directory autenticação. |
| ntp.ubuntu.com             | UDP: 123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| packages.microsoft.com     | HTTPS: 443 | Esse endereço é o repositório de pacotes da Microsoft usado para operações *apt-get* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e CLI do Azure. |
| acs-mirror.azureedge.net      | HTTPS: 443 | Esse endereço é para o repositório necessário para instalar os binários necessários, como kubenet e CNI do Azure. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Portas e endereços recomendados opcionais para clusters AKS

As seguintes regras de rede/portas de saída são opcionais para um cluster AKS:

As seguintes regras de FQDN/aplicativo são recomendadas para que os clusters AKS funcionem corretamente:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP: 80   | Esse endereço permite que os nós de cluster do Linux baixem as atualizações e os patches de segurança necessários. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Endereços e portas necessários para clusters AKS habilitados para GPU

As seguintes regras de FQDN/aplicativo são necessárias para clusters AKS que têm a GPU habilitada:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS: 443 | Esse endereço é usado para a instalação e operação corretas do driver em nós baseados em GPU. |
| us.download.nvidia.com | HTTPS: 443 | Esse endereço é usado para a instalação e operação corretas do driver em nós baseados em GPU. |
| apt.dockerproject.org | HTTPS: 443 | Esse endereço é usado para a instalação e operação corretas do driver em nós baseados em GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Endereços e portas necessários com Azure Monitor para contêineres habilitados

As seguintes regras de FQDN/aplicativo são necessárias para clusters AKS que têm o Azure Monitor para contêineres habilitados:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS: 443    | Isso é para métricas corretas e monitoramento de telemetria usando Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS: 443    | Isso é usado pelo Azure Monitor para ingerir dados do log Analytics. |
| *.oms.opinsights.azure.com | HTTPS: 443 | Esse endereço é usado pelo omsagent, que é usado para autenticar o serviço do log Analytics. |
|*.microsoftonline.com | HTTPS: 443 | Isso é usado para autenticar e enviar métricas para Azure Monitor. |
|*. monitoring.azure.com | HTTPS: 443 | Isso é usado para enviar dados de métricas para Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Endereços e portas necessários com Azure Dev Spaces habilitado

As seguintes regras de FQDN/aplicativo são necessárias para clusters AKS que têm o Azure Dev Spaces habilitado:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Este endereço é usado para extrair as imagens do Linux Alpine e outras Azure Dev Spaces |
| gcr.io | HTTP: 443 | Esse endereço é usado para efetuar pull de imagens Helm/gaveta |
| storage.googleapis.com | HTTP: 443 | Esse endereço é usado para efetuar pull de imagens Helm/gaveta |
| azds-\<GUID\>. \<local\>. azds.Io | HTTPS: 443 | Para se comunicar com os serviços de back-end Azure Dev Spaces para seu controlador. O FQDN exato pode ser encontrado em "dataplaneFqdn" em% USERPROFILE%\.azds\settings.JSON |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Endereços e portas necessários para clusters AKS com Azure Policy (em visualização pública) habilitados

> [!CAUTION]
> Alguns dos recursos a seguir estão em versão prévia.  As sugestões neste artigo estão sujeitas a alterações à medida que o recurso é movido para a visualização pública e para os próximos estágios da versão.

As seguintes regras de FQDN/aplicativo são necessárias para clusters AKS que têm o Azure Policy habilitado.

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | Esse endereço é usado para a operação correta de Azure Policy. (atualmente em visualização em AKS) |
| raw.githubusercontent.com | HTTPS: 443 | Esse endereço é usado para efetuar pull das políticas internas do GitHub para garantir a operação correta de Azure Policy. (atualmente em visualização em AKS) |
| *. GK. \<local\>. azmk8s.Io | HTTPS: 443    | Azure Policy complemento que se comunica com o ponto de extremidade de auditoria do gatekeeper em execução no servidor mestre para obter os resultados da auditoria. |
| dc.services.visualstudio.com | HTTPS: 443 | Azure Policy complemento que envia dados de telemetria para o ponto de extremidade do Application insights. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Exigido por nós baseados no Windows Server habilitado

As seguintes regras de FQDN/aplicativo são necessárias para usar pools de nós baseados no Windows Server:

| FQDN                                    | Porta      | Uso      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS: 443 | Para instalar binários relacionados ao Windows |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP: 80 | Para instalar binários relacionados ao Windows |
| kms.core.windows.net | TCP: 1688 | Para instalar binários relacionados ao Windows |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu quais portas e endereços permitir se você restringir o tráfego de saída para o cluster. Você também pode definir como o próprio pods pode se comunicar e quais restrições eles têm dentro do cluster. Para obter mais informações, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
