---
title: 'Tutorial: configurar a nuvem do Atlassian para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77059325"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: configurar o Atlassian Cloud para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Atlassian Cloud e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Atlassian Cloud.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de nuvem do Atlassian](https://www.atlassian.com/licensing/cloud)
* Uma conta de usuário no Atlassian Cloud com permissões de administrador.

> [!NOTE]
> A integração de provisionamento do Azure AD depende da API do **Atlassian Cloud scim**, que está disponível para as equipes de nuvem do Atlassian.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud por meio da galeria

Antes de configurar o Atlassian Cloud para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)**, no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atribuindo usuários ao Atlassian Cloud

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Atlassian Cloud. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Atlassian Cloud seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Dicas importantes para atribuir usuários ao Atlassian Cloud

* É recomendável que um único usuário do Azure AD seja atribuído ao Atlassian Cloud para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Atlassian Cloud, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configurando o provisionamento automático de usuário para o Atlassian Cloud 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Atlassian Cloud com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Atlassian Cloud, seguindo as instruções fornecidas no [tutorial de logon único do Atlassian Cloud](atlassian-cloud-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Atlassian Cloud no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Atlassian Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navegue até [Atlassian Organization Manager](https://admin.atlassian.com) **> selecione o diretório > org**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Clique em **provisionamento de usuário** e clique em **criar um diretório**. Copie a **URL base do diretório** e o **token de portador** para os campos **URL do locatário** e **token secreto** , respectivamente.

    ![](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Provisionamento em nuvem do Atlassian Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![provisionamento de nuvem Atlassian provisionamento de nuvem](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Na seção **credenciais de administrador** , insira a **URL do locatário** e o **token secreto** da conta da nuvem do Atlassian. Exemplos desses valores são:

   * No campo **URL do locatário** , preencha o ponto de extremidade do locatário específico que você recebe do Atlassian, conforme descrito na etapa 6. Por exemplo: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * No campo **token secreto** , preencha o token secreto conforme descrito na etapa 6.

8. Ao preencher os campos mostrados na etapa 7, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Atlassian Cloud. Se a conexão falhar, verifique se sua conta do Atlassian Cloud tem permissões de administrador e tente novamente.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Clique em **Salvar**.

11. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Examine os atributos de usuário que são sincronizados do Azure AD para o Atlassian Cloud na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o Atlassian Cloud**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Examine os atributos de grupo que são sincronizados do Azure AD para o Atlassian Cloud na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no Atlassian Cloud para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

16. Para habilitar o serviço de provisionamento do Azure AD para o Atlassian Cloud, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Defina os usuários e/ou grupos que você deseja provisionar para o Atlassian Cloud escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Provisionamento de nuvem do Atlassian](./media/atlassian-cloud-provisioning-tutorial/save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Atlassian Cloud.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Atlassian Cloud permite o provisionamento de usuários somente de [domínios verificados](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* A nuvem Atlassian não dá suporte a renomeações de grupo hoje. Isso significa que qualquer alteração no displayName de um grupo no Azure AD não será atualizada e refletida no Atlassian Cloud.
* O valor do atributo de usuário de **email** no Azure ad só será populado se o usuário tiver uma caixa de correio do Microsoft Exchange. Se o usuário não tiver um, é recomendável mapear um atributo desejado diferente para o atributo **emails** no Atlassian Cloud.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png