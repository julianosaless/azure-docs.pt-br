---
title: Série Eav4 e Easv4-máquinas virtuais do Azure
description: Especificações para as VMs da série Eav4 e Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: ab45298dbcb8988da7d0211fcbde3087d0099038
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437074"
---
# <a name="eav4-and-easv4-series"></a>Séries Eav4 e Easv4

A série Eav4 e a Easv4 utilizam o processador de 2.35 GHz EPYC<sup>TM</sup> 7452 em uma configuração multi-threaded com até 256 MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho com otimização de memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

## <a name="eav4-series"></a>Série Eav4

ACU: 230-260

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Migração ao Vivo: com suporte

Atualizações de preservação de memória: com suporte

Os tamanhos da série Eav4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz e usar SSD Premium. Os tamanhos da série Eav4 são ideais para aplicativos empresariais com uso intensivo de memória. O armazenamento do disco de dados é faturado separadamente das máquinas virtuais. Para usar o SSD Premium, use os tamanhos da série Easv4. Os medidores de cobrança e preço para os tamanhos de Easv4 são os mesmos que os da série Eav3.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Máximo de NICs/largura de banda de rede esperada (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| E2a\_\_v4 padrão|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| E4a\_\_v4 padrão|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| E8a\_\_v4 padrão|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| E16a\_\_v4 padrão|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| E20a\_\_v4 padrão|20|160|500|32|30000/468/234|8 / 10000 |
| E32a\_\_v4 padrão|32|256|800|32|48000 / 750 / 375|8 / 16000 |
| E48a\_\_v4 padrão|48|384|1200|32|96000/1000 (500)|8 / 24000 |
| E64a\_\_v4 padrão|64|512|1600|32|96000/1000 (500)|8 / 30000 |
| E96a\_\_v4 padrão|96|672|2400|32|96000/1000 (500)|8 / 30000 |

## <a name="easv4-series"></a>Série Easv4

ACU: 230-260

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Migração ao Vivo: com suporte

Atualizações de preservação de memória: com suporte

Os tamanhos da série Easv4 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar uma frequência máxima aumentada de 3.35 GHz e usar SSD Premium. Os tamanhos da série Easv4 são ideais para aplicativos empresariais com uso intensivo de memória.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 / 24000 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 / 30000 |
| Standard_E96as_v4|96|672|1344|32|192000/1020 (2400)|80000/1200|8 / 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
