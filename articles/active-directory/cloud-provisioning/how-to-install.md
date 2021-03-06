---
title: Instalar o agente de provisionamento em nuvem do Azure AD Connect
description: Este artigo descreve como instalar o Azure AD Connect agente de provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263342"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalar o agente de provisionamento em nuvem do Azure AD Connect
Este documento orienta o processo de instalação para o agente de provisionamento do Azure Active Directory (Azure AD) Connect e como configurá-lo inicialmente no portal do Azure.

>[!IMPORTANT]
>As instruções de instalação a seguir pressupõem que todos os [pré-requisitos](how-to-prerequisites.md) foram atendidos.

Instalar e configurar Azure AD Connect provisionamento é feito nas seguintes etapas:
    
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estas etapas.

1. Entre no servidor que você usará com as permissões de administrador corporativo.
1. Vá para o portal do Azure. À esquerda, selecione **Azure Active Directory**.
1. Selecione **gerenciar provisionamento (versão prévia)** > **examinar todos os agentes**.
1. Baixe o agente de provisionamento do Azure AD Connect do portal do Azure.

   ![Baixar agente local](media/how-to-install/install9.png)</br>
1. Execute o instalador de provisionamento do Azure AD Connect (AADConnectProvisioningAgent. Installer).
1. Na tela **pacote de agente de provisionamento do Microsoft Azure ad Connect** , aceite os termos de licenciamento e selecione **instalar**.

   ![Tela do pacote do agente de provisionamento do Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. Após a conclusão dessa operação, o assistente de configuração é iniciado. Entre com sua conta de administrador global do Azure AD.
1. Na tela **conectar Active Directory** , selecione **Adicionar diretório**. Em seguida, entre com sua conta de administrador do Active Directory. Esta operação adiciona seu diretório local. Selecione **Avançar**.

   ![Tela conectar Active Directory](media/how-to-install/install3.png)</br>

1. Na tela **configuração concluída** , selecione **confirmar**. Esta operação registra e reinicia o agente.

   ![Tela de configuração concluída](media/how-to-install/install4.png)</br>

1. Após a conclusão dessa operação, você deverá ver o aviso de que **a configuração do agente foi verificada com êxito.** Selecione **sair**.

   ![Botão Sair](media/how-to-install/install5.png)</br>
1. Se você ainda vir a tela inicial do **pacote de agente de provisionamento do Microsoft Azure ad Connect** , selecione **fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre na portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas.

1. Entre no Portal do Azure.
1. À esquerda, selecione **Azure Active Directory** > **Azure ad Connect**. No centro, selecione **gerenciar provisionamento (versão prévia)**.

   ![Portal do Azure](media/how-to-install/install6.png)</br>

1.  Na tela **provisionamento do Azure AD (versão prévia)** , selecione **examinar todos os agentes**.

    ![Opção examinar todos os agentes](media/how-to-install/install7.png)</br>
 
1. Na tela **agentes de provisionamento locais** , você vê os agentes que instalou. Verifique se o agente em questão está lá e se está marcado como *ativo*.

   ![Tela de agentes de provisionamento local](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verificar a porta
Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, siga estas etapas.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verifica se os agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor em que o agente está instalado.

![Verificação de acessibilidade de porta](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas.

1.  Entre no servidor com uma conta de administrador.
1.  Abra **Serviços** navegando até ele ou acessando **Iniciar** > **executar** > **Services. msc**.
1.  Em **Serviços**, verifique se **Microsoft Azure ad atualizador do agente** e **Microsoft Azure ad agente de provisionamento do Connect** estão lá e se seu status está *em execução*.

    ![Tela de serviços](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e habilitado antes de iniciar a sincronização de usuários. Para configurar um novo agente, consulte [criar uma nova configuração para Azure ad Connect provisionamento baseado em nuvem](how-to-configure.md).



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
 
