---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050386"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, você cria um serviço de back-end que adiciona as propriedades desejadas a um dispositivo e, em seguida, consulta o registro de identidade para localizar todos os dispositivos com propriedades relatadas que foram atualizadas de acordo. Seu serviço precisa da permissão de **conexão de serviço** para modificar as propriedades desejadas de um dispositivo e precisa da permissão de **leitura do registro** para consultar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas duas permissões, portanto, você precisa criar uma.
