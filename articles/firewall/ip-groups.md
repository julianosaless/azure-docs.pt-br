---
title: Grupos de IPs no firewall do Azure
description: Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: e0638cbccd5e3bc282dbdd7d3b5918e29081a12b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80757170"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>Grupos de IPS (versão prévia) no firewall do Azure

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Os grupos de IPS permitem agrupar e gerenciar endereços IP para regras de firewall do Azure das seguintes maneiras:

- Como um endereço de origem em regras DNAT
- Como um endereço de origem ou de destino nas regras de rede
- Como um endereço de origem nas regras de aplicativo


Um grupo de IPS pode ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereços IP.

Os grupos de IPS podem ser reutilizados no firewall do Azure DNAT, rede e regras de aplicativo para vários firewalls em regiões e assinaturas no Azure. Os nomes de grupo devem ser exclusivos. Você pode configurar um grupo de IP no portal do Azure, CLI do Azure ou API REST. Um modelo de exemplo é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Formato de exemplo

Os exemplos de formato de endereço IPv4 a seguir são válidos para uso em grupos de IPS:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Intervalo de endereços: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um grupo de IPS

Um grupo de IPS pode ser criado usando o portal do Azure, CLI do Azure ou a API REST. Para obter mais informações, consulte [criar um grupo de IPS (versão prévia)](create-ip-group.md).

## <a name="browse-ip-groups"></a>Procurar grupos de IP
1. Na barra de pesquisa portal do Azure, digite **grupos de IPS** e selecione-o. Você pode ver a lista de grupos de IPS ou pode selecionar **Adicionar** para criar um novo grupo de IPS.
2. Selecione um grupo de IPS para abrir a página Visão geral. Você pode editar, adicionar ou excluir endereços IP ou grupos de IPS.

   ![Visão geral de grupos de IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gerenciar um grupo de IPS

Você pode ver todos os endereços IP no grupo de IPS e as regras ou os recursos associados a ele. Para excluir um grupo de IPS, primeiro você deve dissociar o grupo de IPS do recurso que o está usando.

1. Para exibir ou editar os endereços IP, selecione **endereços IP** em **configurações** no painel esquerdo.
2. Para adicionar um ou vários endereços IP, selecione **adicionar endereços IP**. Isso abre a página de **arrastar ou procurar** para um upload ou você pode inserir o endereço manualmente.
3.    Selecionando as reticências (**...**) à direita para editar ou excluir endereços IP. Para editar ou excluir vários endereços IP, marque as caixas e selecione **Editar** ou **excluir** na parte superior.
4. Por fim, o pode exportar o arquivo no formato de arquivo CSV.

> [!NOTE]
> Se você excluir todos os endereços IP em um grupo de IPS enquanto ele ainda estiver em uso em uma regra, essa regra será ignorada.


## <a name="use-an-ip-group"></a>Usar um grupo de IPS

Agora você pode selecionar o **grupo IP** como um tipo de **origem** ou de **destino** para os endereços IP ao criar regras de DNAT, aplicativo ou rede do firewall do Azure.

> [!NOTE]
> Não há suporte para grupos de IP na política de firewall. Atualmente, ele só tem suporte com regras de firewall tradicionais.

![Grupos de IPs no firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Disponibilidade de região

Os grupos de IP estão disponíveis em todas as regiões de nuvem pública.

## <a name="ip-address-limits"></a>Limites de endereço IP

Para grupos IP 50 ou menos, você pode ter um máximo de 5000 endereços IP individuais cada por instância de firewall. Para 51 a 100 grupos de IP, você pode ter 500 endereço IP individual cada instância de firewall.

### <a name="examples"></a>Exemplos

#### <a name="example-1-supported"></a>Exemplo 1: com suporte

|Grupos de IP  |N º de endereços IP  |Notation  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |3|196.0.0.0 - 196.0.0.2|Rule1|
|IPGroup3     |1|1.2.3.4|Rule1|
|     |**Total de 4100**|         |         |
|     |         |         |         |

#### <a name="example-2-supported"></a>Exemplo 2: com suporte

|Grupos de IP  |N º de endereços IP  |Notation  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Rule1|
|     |**Total de 8192**|         |         |

#### <a name="example-3-not-supported"></a>Exemplo 3: sem suporte

|Grupos de IP  |N º de endereços IP  |Notation  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |8192     |10.0.0.0/20, 11.0.0.0/20  |Rule1|
|     |**Total de 8192**|||

#### <a name="example-4-supported"></a>Exemplo 4: com suporte

|Grupos de IP  |N º de endereços IP  |Notation  |Regra  |
|---------|---------|---------|---------|
|IPGroup1 |4096     |10.0.0.0/20  |Rule1|
|IPGroup2     |4096|11.0.0.0/20|Rule2|
|     |**Total de 8192**|         |         |


## <a name="related-azure-powershell-cmdlets"></a>Cmdlets de Azure PowerShell relacionados

Os cmdlets Azure PowerShell a seguir podem ser usados para criar e gerenciar grupos de IPS:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).