---
title: 'Tutorial: configurar o ZPA (Zscaler Private Access) para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o ZPA (Zscaler Private Access).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064131"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: configurar o ZPA (Zscaler Private Access) para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no ZPA (Zscaler Private Access) e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para ZPA (Zscaler Private Access).

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário ZPA (Zscaler Private Access)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Uma conta de usuário no ZPA (Zscaler Private Access) com permissões de administrador.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Atribuindo usuários ao ZPA (Zscaler Private Access)

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao ZPA (Zscaler Private Access). Depois de decidir, você pode atribuir esses usuários e/ou grupos ao ZPA (Zscaler Private Access) seguindo as instruções aqui:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Dicas importantes para atribuir usuários ao ZPA (Zscaler Private Access)

* É recomendável que um único usuário do Azure AD seja atribuído ao ZPA (Zscaler Private Access) para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao ZPA (Zscaler Private Access), você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Configurar o ZPA (Zscaler Private Access) para provisionamento

1. Entre no seu [console de administração do ZPA (Zscaler Private Access)](https://admin.private.zscaler.com/). Navegue até **administração > configuração do IDP**.

    ![Console de administração do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Verifique se um IdP para **logon único** está configurado. Se nenhum IdP for configurado, adicione um clicando no ícone de adição no canto superior direito da tela.

    ![ZPA (Zscaler Private Access)-adicionar SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Siga o assistente para **Adicionar configuração de IDP** para adicionar um IDP. Deixe o campo **logon único** definido como **usuário**. Forneça um **nome** e selecione os **domínios** na lista suspensa. Clique em **Avançar** para navegar até a próxima janela.

    ![ZPA (Zscaler Private Access) adicionar IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Baixe o **certificado do provedor de serviços**. Clique em **Avançar** para navegar até a próxima janela.

    ![Certificado SP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Na próxima janela, carregue o **certificado do provedor de serviços** baixado anteriormente.

    ![Certificado de upload do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Role para baixo para fornecer a **URL de logon único** e a **ID da entidade IDP**.

    ![ID IdP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Role para baixo para **habilitar a sincronização do scim**. Clique no botão **gerar novo token** . Copie o **token de portador**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo ZPA (Zscaler Private Access) no portal do Azure.

    ![Token de criação do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Para localizar a **URL do locatário** , navegue até **Administração > configuração do IDP**. Clique no nome da configuração IdP recém-adicionada listada na página.

    ![Nome de IDP do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Role para baixo para exibir o **ponto de extremidade do provedor de serviços scim** no final da página. Copie o **ponto de extremidade do provedor de serviços scim**. Esse valor será inserido no campo URL do locatário na guia provisionamento do seu aplicativo ZPA (Zscaler Private Access) no portal do Azure.

    ![URL SCIM do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicionar ZPA (Zscaler Private Access) da Galeria

Antes de configurar o ZPA (Zscaler Private Access) para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o ZPA (Zscaler Private Access) da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZPA (Zscaler Private Access) da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **ZPA (Zscaler Private Access)**, selecione **ZPA (Zscaler Private Access)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ZPA (Zscaler Private Access) na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Configurando o provisionamento automático de usuário para o ZPA (Zscaler Private Access) 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no ZPA (Zscaler Private Access) com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o ZPA (Zscaler Private Access) seguindo as instruções fornecidas no tutorial de [logon único do ZPA (Zscaler Private Access)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade SCIM do Zscaler Private Access, confira [isso](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o ZPA (Zscaler Private Access) no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)**.

    ![O link do ZPA (Zscaler Private Access) na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira o valor de **ponto de extremidade do provedor de serviço scim** recuperado anteriormente na **URL do locatário**. Insira o valor do **token de portador** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao ZPA (Zscaler Private Access). Se a conexão falhar, verifique se sua conta do ZPA (Zscaler Private Access) tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o ZPA (Zscaler Private Access)**.

    ![Mapeamentos de usuário do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o ZPA (Zscaler Private Access) na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no ZPA (Zscaler Private Access) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o ZPA (Zscaler Private Access)**.

    ![Mapeamentos de grupos do ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o ZPA (Zscaler Private Access) na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no ZPA (Zscaler Private Access) para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de grupo ZPA (Zscaler Private Access)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o ZPA (Zscaler Private Access), altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o ZPA (Zscaler Private Access) escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no ZPA (Zscaler Private Access).

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

