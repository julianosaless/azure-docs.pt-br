---
title: Definir o tempo limite de inatividade no nível do diretório para os usuários do portal do Azure | Microsoft Docs
description: Os administradores podem impor o tempo ocioso máximo antes que uma sessão seja desconectada. A política de tempo limite de inatividade é definida no nível do diretório.
services: azure-portal
keywords: configurações, tempo limite
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79096616"
---
# <a name="set-directory-level-inactivity-timeout"></a>Definir tempo limite de inatividade no nível do diretório

A configuração de tempo limite de inatividade ajuda a proteger seus recursos contra acesso não autorizado se os usuários esquecerem de proteger sua estação de trabalho. Quando um usuário esteve ocioso por um tempo, sua sessão de portal do Azure é desconectada automaticamente. Os administradores na [função de administrador global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) podem impor o tempo ocioso máximo antes que uma sessão seja desconectada. A configuração de tempo limite de inatividade se aplica ao nível do diretório. Para obter mais informações sobre diretórios, consulte [Active Directory Domain Services visão geral](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Definir a configuração de tempo limite inativo

Se você for um administrador global e quiser impor uma configuração de tempo limite de ociosidade para todos os usuários da portal do Azure, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **configurações** no cabeçalho da página global.
3. Selecione o texto do link **Configurar o tempo limite do nível do diretório**.

    ![Captura de tela mostrando as configurações do portal com texto do link realçado](./media/admin-timeout/settings.png)

4. Uma nova página é aberta. Na página **Configurar tempo limite de inatividade de nível de diretório** , selecione **habilitar tempo limite de ociosidade de nível de diretório para a Portal do Azure** ativar a configuração.
5. Em seguida, insira as **horas** e os **minutos** para o tempo máximo que um usuário pode ficar ocioso antes de sua sessão ser desconectada automaticamente.
6. Escolha **Aplicar**.

    ![Captura de tela mostrando a página para definir o tempo limite de inatividade no nível do diretório](./media/admin-timeout/configure.png)

Para confirmar se a política de tempo limite de inatividade está definida corretamente, selecione **notificações** no cabeçalho da página global. Verifique se uma notificação de êxito está listada.

  ![Captura de tela mostrando mensagem de notificação com êxito para tempo limite de inatividade no nível do diretório](./media/admin-timeout/confirmation.png)

A configuração entra em vigor para novas sessões. Ele não será aplicado imediatamente a nenhum usuário que já esteja conectado.

> [!NOTE]
> Se um administrador global tiver configurado uma configuração de tempo limite no nível de diretório, os usuários poderão substituir a política e definir sua própria duração de saída inativa. No entanto, o usuário deve escolher um intervalo de tempo menor que o que está definido no nível do diretório pelo administrador global.
>

## <a name="next-steps"></a>Próximas etapas

* [Definir suas preferências do portal do Azure](set-preferences.md)
* [Exportar ou excluir configurações do usuário](azure-portal-export-delete-settings.md)
* [Ativar alto contraste ou alterar tema](azure-portal-change-theme-high-contrast.md)
