---
title: Métricas com suporte na grade de eventos do Azure
description: Este artigo fornece Azure Monitor métricas com suporte no serviço de grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630129"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas com suporte na grade de eventos do Azure
Este artigo fornece listas de métricas de grade de eventos que são categorizadas por namespaces. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domínios

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Nenhum|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Error, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhum|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo: [logs de diagnóstico](diagnostic-logs.md)
