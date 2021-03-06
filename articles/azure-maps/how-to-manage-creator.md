---
title: Gerenciar o Criador do Azure Mapas
description: Neste artigo, você aprenderá a gerenciar o Criador do Azure Mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677967"
---
# <a name="manage-azure-maps-creator"></a>Gerenciar o Criador do Azure Mapas

O Criador do Azure Mapas permite criar dados de mapas internos privados. Usando a API do Azure Mapas e o módulo de Mapas Internos, você pode desenvolver aplicativos Web de mapas internos interativos e dinâmicos. Atualmente, o Criador só está disponível no Estados Unidos usando o tipo de preço S1.

Este artigo orienta você pelas etapas para criar e excluir um recurso do Criador em uma conta do Azure Mapas.

## <a name="create-creator-resource"></a>Criar um recurso do Criador

1. Entre no [Portal do Azure](https://portal.azure.com)

2. Selecione sua conta do Azure Mapas. Se você não conseguir ver sua conta do Azure Mapas em **Recursos recentes**, navegue até o menu do portal do Azure. Selecione **Todos os recursos**. Localize e selecione sua conta do Azure Mapas.

    ![Página inicial do portal do Azure Mapas](./media/how-to-manage-creator/select-maps-account.png)

3. Quando você estiver na página da conta do Azure Mapas, navegue até a opção **Visão geral** em **Criador**. Clique em **Criar** para criar um recurso do Criador do Azure Mapas.

    ![Página do Criador do Azure Mapas](./media/how-to-manage-creator/creator-blade-settings.png)

4. Insira o nome e o local para o recurso do Criador. Atualmente, o Criador só tem suporte no Estados Unidos. Clique em **Revisar + Criar**.

   ![Entre na página de informações sobre a conta do Criador](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Revise as configurações e clique em **Criar**.

    ![Confirme as configurações de conta do Creator](./media/how-to-manage-creator/creator-create-dialog.png)

6. Quando a implantação for concluída, você verá uma página com uma mensagem de êxito ou de falha.

   ![Página de status da implantação do recurso](./media/how-to-manage-creator/creator-resource-created.png)

7. Clique em **Ir para o recurso**. A página de exibição de recursos do Criador mostra o status do recurso do Criador e a região demográfica escolhida.

    ![Página de status do Criador](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Na página de recursos do Criador, você pode navegar de volta para a conta do Azure Mapas à qual ele pertence, clicando em conta do Azure Mapas.

## <a name="delete-creator-resource"></a>Excluir um recurso do Criador

Para excluir o recurso do Criador, navegue até sua conta do Azure Mapas. Selecione **Visão geral**, em **Criador**. Clique no botão **Excluir** .

>[!WARNING]
>Ao excluir o recurso do Criador de sua conta do Azure Mapas, você também excluirá os conjuntos de dados, conjuntos de peças e conjuntos de estado do recurso criados usando os serviços do Criador.

![Página do Criador com botão de exclusão](./media/how-to-manage-creator/creator-delete.png)

Clique no botão **Excluir** e digite o nome do Criador para confirmar a exclusão. Depois que o recurso for excluído, você verá uma página de confirmação, como na imagem abaixo:

![Página do Criador com botão de confirmação](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Autenticação

O Criador herda as configurações de Controle de Acesso (IAM) do Azure Mapas. Todas as chamadas à API para acesso a dados devem ser enviadas com regras de autenticação e autorização.

Os dados de uso do Criador são incorporados em seus gráficos de uso do Azure Mapas e no log de atividades.  Para obter mais informações, consulte [Gerenciar a autenticação no Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Acesso aos serviços do Criador

Os serviços do Criador são acessíveis somente de dentro do local selecionado durante a criação. Se forem feitas chamadas para os serviços do Criador de fora do local selecionado, uma mensagem de erro do usuário será retornada. Para fazer chamadas de fora do local selecionado, a URL do serviço deve incluir o prefixo geográfico dos locais selecionados. Por exemplo, se o Criador for criado na Estados Unidos, todas as chamadas para o serviço de conversão deverão ser enviadas para `us.atlas.microsoft.com/conversion/convert`.

Além disso, todos os dados importados para o Criador deverão ser carregados na mesma localização geográfica que o recurso do Criador. Por exemplo, se o Criador for provisionado nos Estados Unidos, todos os dados brutos deverão ser carregados pelo `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Próximas etapas

Introdução ao Criador para mapeamento interno:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de Dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Conjunto de peças](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de estado do recurso](creator-indoor-maps.md#feature-statesets)

Saiba como usar o Criador para renderizar mapas em modo interno em seu aplicativo:

> [!div class="nextstepaction"]
> [Tutorial do Criador do Azure Mapas](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilos dinâmicos para mapas internos](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Usar o módulo do Azure Mapas Interno](how-to-use-indoor-module.md)