---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSO do Aplicativo SAML Profit.co | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Aplicativo SAML Profit.co.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSO do Aplicativo SAML Profit.co

Neste tutorial, você aprenderá a integrar o Aplicativo SAML Profit.co ao Azure AD (Azure Active Directory). Ao integrar o aplicativo SAML Profit.co ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Aplicativo SAML Profit.co.
* Habilitar seus usuários para que entrem automaticamente no Profit.co SAML App com suas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Aplicativo SAML Profit.co habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Profit.co SAML App é compatível com SSO iniciado por IDP.

* Depois de configurar o Profit.co SAML App, você pode impor o controle de sessão. Isso protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Adicionar o Profit.co SAML App da galeria

Para configurar a integração do Aplicativo SAML Profit.co ao Azure AD, você precisará adicionar o Aplicativo SAML Profit.co por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Aplicativo SAML Profit.co** na caixa de pesquisa.
1. Selecione **Profit.co SAML App** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Configurar e testar logon único do Azure AD para o Aplicativo SAML Profit.co

Configure e teste o SSO do Azure AD com o SSO do Profit.co SAML App usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Profit.co SAML App.

Aqui estão as etapas gerais para configurar e testar o SSO do Azure AD com o Profit.co SAML App:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Aplicativo SAML Profit.co](#configure-profitco-saml-app-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Profit.co SAML App](#create-a-profitco-saml-app-test-user)** para ter um equivalente de B.Fernandes no Profit.co SAML App. Esse equivalente é vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Aplicativo SAML Profit.co**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente no Azure. Os usuários precisam salvar a configuração selecionando o botão **Salvar**.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o botão **Copiar**. Isso copia a **URL de metadados de federação de aplicativos** e salva em seu computador.

    ![Captura de tela do Certificado de Autenticação SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor mostrado no campo **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Aplicativo SAML Profit.co.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Aplicativo SAML Profit.co**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-profitco-saml-app-sso"></a>Configurar SSO do Aplicativo SAML Profit.co

Para configurar o logon único no lado do Profit.co SAML App, você precisa enviar a URL de Metadados de Federação do Aplicativo para a [equipe de suporte do Profit.co SAML App](mailto:support@profit.co). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-a-profitco-saml-app-test-user"></a>Criar um usuário de teste do Aplicativo SAML Profit.co

Nesta seção, você criará um usuário chamado B.Fernandes no Profit.co Aplicativo SAML Profit.co. Trabalhe com a  [equipe de suporte do Aplicativo SAML Profit.co](mailto:support@profit.co) para adicionar os usuários na plataforma do Aplicativo SAML Profit.co. Você não pode usar o logon único antes de criar e ativar usuários.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Aplicativo SAML Profit.co no Painel de Acesso, você deverá ser conectado automaticamente ao Aplicativo SAML Profit.co, para o qual você configurou o SSO. Para obter mais informações, confira [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Avaliar o Aplicativo SAML Profit.co com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Aplicativo SAML Profit.co com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
