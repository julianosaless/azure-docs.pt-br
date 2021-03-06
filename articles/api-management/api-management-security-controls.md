---
title: Controles de segurança para o gerenciamento de API do Azure
description: Uma lista de verificação de controles de segurança para avaliar o gerenciamento de API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751129"
---
# <a name="security-controls-for-api-management"></a>Controles de segurança para o gerenciamento de API

Este artigo documenta os controles de segurança incorporados ao gerenciamento de API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Suporte ao ponto de extremidade de serviço| Não | |  |
| Suporte à injeção de VNet| Sim | |  |
| Isolamento de rede e suporte de firewall| Sim | Usar NSG (grupos de segurança de rede) e Aplicativo Azure gateway (ou outro dispositivo de software), respectivamente. |  |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | | |
| Registro e auditoria do plano de gerenciamento e controle| Sim | [Logs de atividades Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Log e auditoria do plano de dados| Sim | [Azure monitor logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) e (opcionalmente) [aplicativo Azure insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Autenticação| Sim | |  |
| Autorização| Sim | |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | Documentação |
|---|---|--|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Dados confidenciais, como certificados, chaves e valores nomeados por segredo, são criptografados com chaves gerenciadas por serviço, por chave de instância de serviço. |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Todas as chaves de criptografia são por instância de serviço e são gerenciadas pelo serviço. |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | A criptografia de [rota expressa](../expressroute/index.yml) e VNet é fornecida pela [rede do Azure](../virtual-network/index.yml). |  |
| Chamadas criptografadas à API| Sim | As chamadas do plano de gerenciamento são feitas por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) sobre TLS. Um JWT (token Web JSON) válido é necessário.  As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação com suporte (por exemplo, certificado de cliente ou JWT). |   |
 |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Usando o [DevOps Resource Kit de gerenciamento de API do Azure](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade verifica falsos positivos

Esta seção documenta as vulnerabilidades comuns que não afetam o gerenciamento de API do Azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed é uma vulnerabilidade na implementação da extensão SessionTicket do TLS encontrada em alguns produtos F5. Ele permite o vazamento ("sangramento") de até 31 bytes de dados de memória não inicializada. Isso é causado pelo preenchimento da pilha TLS de uma ID de sessão, passada do cliente, com dados para torná-la de 32 bits de comprimento. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).