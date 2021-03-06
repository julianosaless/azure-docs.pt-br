---
title: Endereços de IP do Azure Integration Runtime
description: Saiba quais endereços IP você deve permitir o tráfego de entrada, para configurar corretamente os firewalls para proteger o acesso à rede para armazenamentos de dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: e3fae34d0bb1aaaa2471f1cc9fd55ef9b4b73dfd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594280"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços de IP do Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os endereços IP que Azure Integration Runtime usa depende da região em que o tempo de execução de integração do Azure está localizado. *Todos* Os tempos de execução de integração do Azure que estão na mesma região usam os mesmos intervalos de endereços IP.

> [!IMPORTANT]  
> Os fluxos de dados não dão suporte ao uso de intervalos de IP fixos.
>
> Você pode usar esses intervalos de IP para a movimentação de dados, pipeline e execuções de atividades externas. Esses intervalos de IP podem ser usados para a lista de permissões em armazenamentos de dados/grupo de segurança de rede (NSG)/firewalls para acesso de entrada do tempo de execução de integração do Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime endereços IP: regiões específicas

Permita o tráfego dos endereços IP listados para o tempo de execução de integração do Azure na região específica do Azure onde os recursos estão localizados:

|                | Região              | Endereços IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Ásia           | Leste da Ásia           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Sudeste Asiático      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Austrália      | Leste da Austrália      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Sudeste da Austrália | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brasil         | Sul do Brasil        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Canadá Central      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| China          | Leste da China 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Ocidental         | Norte da Europa        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa Ocidental         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| França         | França Central      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Índia          | Índia Central       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japão          | Leste do Japão          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Coreia do Sul          | Coreia Central       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| África do Sul   | Norte da África do Sul  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| United Kingdom | Sul do Reino Unido            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Estados Unidos  | Centro dos EUA          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | Leste dos EUA             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | Leste dos EUA 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Leste dos EUA 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | Centro-Norte dos EUA    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | Centro-Sul dos Estados Unidos    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | Centro-Oeste dos EUA     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | Oeste dos EUA             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | Oeste dos EUA 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | Gov. dos EUA – Virgínia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Problema conhecido com o armazenamento do Azure

* Ao se conectar à conta de armazenamento do Azure, as regras de rede IP não têm nenhum efeito nas solicitações originadas do tempo de execução de integração do Azure na mesma região que a conta de armazenamento. Para obter mais detalhes, [Veja este artigo](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Em vez disso, sugerimos o uso [de serviços confiáveis durante a conexão com o armazenamento do Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Próximas etapas

* [Considerações sobre segurança para movimentação de dados no Azure Data Factory](data-movement-security-considerations.md)
