---
title: Gerenciar os Serviços de Nuvem do Azure usando a Automação do Azure | Microsoft Docs
description: Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar serviços de nuvem do Azure em grande escala.
services: cloud-services, automation
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 482fcf7d100a90d9527f510382c5dafb4f67adfa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "72439080"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerenciamento de Serviços de Nuvem do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus serviços de nuvem do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como a Automação do Azure ajuda a gerenciar os serviços de nuvem do Azure?
Os serviços de nuvem do Azure podem ser gerenciados na Automação do Azure usando os cmdlets do PowerShell disponíveis em [Ferramentas PowerShell do Azure](/powershell/). A Automação do Azure tem esses cmdlets do PowerShell do serviço de nuvem disponíveis imediatamente para que você possa executar todas as tarefas de gerenciamento do serviço de nuvem no serviço. Você também pode combinar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

Entre alguns usos de exemplo da Automação do Azure para gerenciar os Serviços de Nuvem do Azure estão:

* [Implantação contínua de um Serviço de Nuvem sempre que cscfg ou cspkg é atualizado no Armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicialização das instâncias do Serviço de Nuvem em paralelo, com um domínio de atualização por vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para serviços de nuvem do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* [Visão geral da automação do Azure](../automation/automation-intro.md)
* [Meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
