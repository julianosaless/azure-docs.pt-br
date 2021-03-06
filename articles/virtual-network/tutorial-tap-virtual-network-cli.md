---
title: Criar, alterar ou excluir um toque de VNet-CLI do Azure
description: Saiba como criar, alterar ou excluir um TAP de rede virtual usando a CLI do Azure.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234961"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Trabalhar com um TAP de rede virtual usando a CLI do Azure

O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). Para obter uma lista de soluções de parceiros validadas para trabalhar com o TAP de rede virtual, consulte [soluções de parceiros](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Criar um recurso de TAP de rede virtual

Leia os [pré-requisitos](virtual-network-tap-overview.md#prerequisites) antes de criar um recurso de TAP de rede virtual. Você pode executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/bash) ou executando a CLI (interface de linha de comando) do Azure no computador. O Azure Cloud Shell é um shell interativo gratuito que não exige a instalação da CLI do Azure no computador. É necessário entrar no Azure com uma conta que tenha as [permissões](virtual-network-tap-overview.md#permissions) apropriadas. Este artigo requer a CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). O toque de rede virtual está disponível atualmente como uma extensão. Para instalar a extensão, você precisa executar `az extension add -n virtual-network-tap`. Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

1. Recupere a ID da assinatura em uma variável que será usada em uma etapa posterior:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Defina a ID da assinatura que você usará para criar um recurso de TAP de rede virtual.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registre novamente a ID da assinatura que você usará para criar um recurso de TAP de rede virtual. Se você receber um erro de registro ao criar um recurso de TAP, execute o comando a seguir:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se o destino para o TAP de rede virtual for o adaptador de rede na solução de virtualização de rede para coletor ou ferramenta analítica -

   - Recupere a configuração IP do adaptador de rede da solução de virtualização de rede em uma variável usada em uma etapa posterior. A ID é o ponto de extremidade que agregará o tráfego de TAP. O exemplo a seguir recupera a ID da configuração IP *ipconfig1* de um adaptador de rede nomeado *myNetworkInterface*, em um grupo de recursos nomeado *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Crie o TAT de rede virtual na região westcentralus azure usando a ID da configuração IP como o destino e uma propriedade de porta opcional. A porta especifica a porta de destino na configuração IP do adaptador de rede onde o tráfego de TAP será recebido:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se o destino do TAP de rede virtual for um balanceador de carga interno do Azure:
  
   - Recupere a configuração IP de front-end do balanceador de carga interno do Azure em uma variável usada em uma etapa posterior. A ID é o ponto de extremidade que agregará o tráfego de TAP. O exemplo a seguir recupera a ID da configuração IP de front-end *frontendipconfig1* para um balanceador de carga nomeado *myInternalLoadBalancer*, em um grupo de recursos nomeado *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Crie o TAP de rede virtual usando a ID da configuração IP de front-end como o destino e uma propriedade de porta opcional. A porta especifica a porta de destino na configuração IP de front-end onde o tráfego de TAP será recebido:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Confirme a criação do TAP de rede virtual:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Adicione uma configuração de TAP a um adaptador de rede

1. Recuperar a ID de um recurso de TAP de rede virtual existente. O exemplo a seguir recupera um TAP de rede virtual TAP nomeado *myTap* em um grupo de recursos nomeado *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Crie uma configuração de TAP no adaptador de rede da máquina virtual monitorada. O exemplo a seguir cria uma configuração de TAP para um adaptador de rede nomeado *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Confirme a criação da configuração de TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Excluir a configuração de TAP em um adaptador de rede

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Listar TAPs de rede virtual em uma assinatura

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Excluir um TAP de rede virtual TAP em um grupo de recursos

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
