---
title: 'Conectar-se a uma VNet usando o P2S VPN & autenticação de certificado: Portal'
titleSuffix: Azure VPN Gateway
description: Conecte clientes Windows, Linux e Mac OS X com segurança a uma rede virtual do Azure usando P2S e certificados autoassinados ou emitidos por autoridade de certificação. Este artigo usa o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: 013ebc2a1343c8eab3d477023e36660c93fa6da5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244479"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configurar uma conexão VPN ponto a site para uma VNet usando a autenticação de certificado nativa do Azure: portal do Azure

Este artigo ajuda você a conectar clientes individuais que executem Windows, Linux ou Mac OS X a uma VNet do Azure com segurança. As conexões VPN Ponto a Site são úteis quando você deseja se conectar à rede virtual de um local remoto, como ao trabalhar de casa ou em uma conferência. Também é possível usar P2S em vez de uma VPN Site a Site, quando você tiver apenas alguns clientes que precisam se conectar a uma VNet. As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público. A P2S cria a conexão VPN no SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Para obter mais informações sobre conexões VPN Ponto a Site, consulte [Sobre VPN Ponto a Site](point-to-site-about.md).

![Conectar um computador a um diagrama de conexão de ponto para Site da rede virtual do Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Arquitetura

Conexões nativas de autenticação de certificado do Azure Ponto a Site usam os itens a seguir, os quais são configurados neste exercício:

* Gateway de VPN RouteBased.
* A chave pública (arquivo .cer) para um certificado raiz, que é carregado no Azure. Depois que o certificado é carregado, ele é considerado um certificado confiável e é usado para autenticação.
* Um certificado do cliente que é gerado a partir do certificado raiz. O certificado do cliente instalado em cada computador cliente que se conectará à VNet. Esse certificado é usado para autenticação do cliente.
* Uma configuração de cliente VPN. Os arquivos de configuração de cliente VPN contém as informações necessárias para o cliente se conectar à VNet. Os arquivos configuram o cliente VPN existente que é nativo do sistema operacional. Cada cliente que se conecta deve ser configurado usando as configurações nos arquivos de configuração.

#### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Você pode usar os seguintes valores para criar um ambiente de teste ou fazer referência a esses valores para entender melhor os exemplos neste artigo:

* **Nome da VNet:** VNet1
* **Espaço de endereço:** 10.1.0.0/16<br>Neste exemplo, usamos apenas um espaço de endereço. Você pode ter mais de um espaço de endereço para sua rede virtual.
* **Nome da sub-rede:** Front-end
* **Intervalo de endereços da sub-rede:** 10.1.0.0/24
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de recursos:** TestRG1
* **Local:** Leste dos EUA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome do gateway de rede virtual:** VNet1GW
* **Tipo de gateway:** VPNS
* **Tipo de VPN:** Baseado em rota
* **Endereço IP público:** VNet1GWpip
* **Tipo de conexão:** ponto a site
* **Pool de endereços do cliente:** 172.16.201.0/24<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. criar uma rede virtual

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. criar um gateway de rede virtual

Nesta etapa, você cria o gateway de rede virtual para sua rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

>[!NOTE]
>O SKU do gateway básico não oferece suporte à autenticação IKEv2 ou RADIUS. Se você planeja ter clientes Mac conectados à sua rede virtual, não use a SKU básica.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. gerar certificados

Certificados são usados pelo Azure para autenticar clientes que se conectam a uma VNet por meio de conexão VPN Ponto a Site. Depois de obter um certificado raiz, você poderá [carregar](#uploadfile) as informações de chave pública para o Azure. O certificado raiz é considerado 'confiável' pelo Azure para conexão sobre P2S com a rede virtual. Você também gera certificados do cliente a partir do certificado raiz confiável e os instala em cada computador cliente. O certificado do cliente é usado para autenticar o cliente quando ele inicia uma conexão de rede virtual. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. Obtenha o arquivo. cer para o certificado raiz

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. gerar um certificado de cliente

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Adicionar o pool de endereços do cliente

O pool de endereços do cliente é um intervalo de endereços IP que você especificar. Os clientes que se conectam por VPN ponto a site recebem dinamicamente um endereço IP desse intervalo. Use um intervalo de endereço IP privado que não coincida com o local de onde você se conecta ou com a rede virtual à qual você deseja se conectar. Se você configurar vários protocolos e o SSTP for um dos protocolos, o pool de endereços configurado será dividido entre os protocolos configurados igualmente.

1. Quando o gateway de rede virtual tiver sido criado, navegue até a seção **Configurações** da página do gateway de rede virtual. Na seção **configurações** , selecione **configuração ponto a site**. Selecione **Configurar agora** para abrir a página de configuração.

   ![Página ponto a site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Configurar ponto a site agora")
2. Na página **configuração ponto a site** , você pode configurar uma variedade de configurações. Se você não vir o tipo de túnel ou tipo de autenticação nesta página, seu gateway estará usando o SKU básico. A SKU Básica não dá suporte à autenticação IKEv2 ou RADIUS. Se você quiser usar essas configurações, será necessário excluir e recriar o gateway usando um SKU de gateway diferente.

   [![Página de configuração ponto a site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "especificar pool de endereços")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Na caixa **pool de endereços** , adicione o intervalo de endereços IP privado que você deseja usar. Os clientes VPN recebem dinamicamente um endereço IP do intervalo que você especificar. A máscara de sub-rede mínima é 29 bits para ativo/passivo e 28 bits para configuração ativa/ativa.
4. Vá para a próxima seção para configurar o tipo de túnel.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. configurar o tipo de túnel

Você pode selecionar o tipo de túnel. As opções de túnel são OpenVPN, SSTP e IKEv2.

* O cliente strongSwan no Android e no Linux e o cliente nativo VPN IKEv2 no iOS e no OSX usarão somente o túnel IKEv2 para se conectar.
* Os clientes Windows tentam IKEv2 primeiro e se não se conectam, eles retornam ao SSTP.
* Você pode usar o cliente OpenVPN para se conectar ao tipo de túnel OpenVPN.

![Tipo de túnel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "especificar tipo de túnel")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. configurar o tipo de autenticação

Para **tipo de autenticação**, selecione **certificado do Azure**.

  ![Tipo de autenticação](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "especificar tipo de autenticação")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Carregue os dados do certificado público do certificado raiz

Você pode carregar outros certificados raiz confiáveis até um total de 20. Uma vez carregados os dados públicos do certificado, o Azure pode usá-los para autenticar clientes com um certificado do cliente instalado gerado a partir de um certificado raiz confiável. Carregue as informações da chave pública do certificado raiz no Azure.

1. Os certificados são adicionados na página **Configuração Ponto a site** na seção **Certificado raiz**.
2. Verifique se você exportou o certificado raiz como um arquivo x.509 (.cer) codificado em Base 64. Você precisa exportar o certificado neste formato para poder abri-lo em um editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Ao copiar os dados do certificado, certifique-se de copiar o texto como uma linha contínua sem retornos de carro ou alimentações de linha. Talvez seja necessário modificar a exibição no editor de texto para 'Mostrar símbolo/Mostrar todos os caracteres' para ver os retornos de carro e alimentações de linha. Copie apenas a seção a seguir como uma linha contínua:

   ![Dados do certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "copiar dados de certificado raiz")
4. Cole os dados do certificado no campo **Dados de Certificado Público**. **Nomeie** o certificado e, em seguida, selecione **salvar**. Pode adicionar até 20 certificados raiz.

   ![Colar dados do certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "colar dados do certificado")
5. Selecione **salvar** na parte superior da página para salvar todas as definições de configuração.

   ![Salvar configuração](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "salvar configuração")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. instalar um certificado de cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

Verifique se o certificado do cliente foi exportado como um .pfx juntamente com a cadeia de certificado inteira (que é o padrão). Caso contrário, as informações do certificado raiz não estão presentes no computador cliente e este não será capaz de fazer a autenticação corretamente.

Para as etapas de instalação, confira [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. gerar e instalar o pacote de configuração de cliente VPN

Os arquivos de configuração de cliente de VPN contêm configurações para definir os dispositivos a fim de se conectar a uma VNet através de uma conexão de P2S. Para obter instruções sobre como gerar e instalar arquivos de configuração de cliente de VPN, confira [Criar e instalar arquivos de configuração de cliente de VPN para configurações de PS2 de autenticação de certificado nativa do Azure ](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. conectar-se ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para conectar-se a partir de um cliente de VPN do Windows

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows do qual você está se conectando.
>
>

1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Selecione **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Selecione **continuar** para usar privilégios elevados.

2. Na página de status **Conexão**, selecione **Conectar** para iniciar a conexão. Se você vir uma tela **Selecionar Certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não estiver, use a seta suspensa para selecionar o certificado correto e, em seguida, selecione **OK**.

   ![Cliente VPN se conecta ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "connect")
3. A conexão é estabelecida.

   ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "conexão estabelecida")

#### <a name="troubleshoot-windows-p2s-connections"></a>Solucionar problemas de conexões P2S do Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Para conectar-se a partir de um cliente de VPN do Mac

Na caixa de diálogo rede, localize o perfil do cliente que você deseja usar, especifique as configurações do [VpnSettings. xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)e, em seguida, selecione **conectar**.

Verifique [ Instalar - Mac (OSX)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) para obter instruções detalhadas. Se você estiver tendo problemas para se conectar, verifique se que o gateway de rede virtual não está usando uma SKU básica. Não há suporte para a SKU básica para clientes Mac.

  ![Conexão Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Conectar")

## <a name="to-verify-your-connection"></a><a name="verify"></a>Para verificar sua conexão

Essas instruções se aplicam a clientes do Windows.

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados são semelhantes a este exemplo:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Para conectar-se a uma máquina virtual

Essas instruções se aplicam a clientes do Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Para adicionar ou remover certificados raiz confiáveis

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado raiz, os clientes que possuem um certificado gerado a partir dessa raiz não serão capazes de fazer a autenticação e, portanto, não serão capazes de se conectar. Se você deseja que um clientes faça autenticação e se conecte, você precisa instalar um novo certificado de cliente gerado a partir de um certificado confiável (carregado) no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. Para obter instruções, consulte a seção [Carregar um certificado raiz confiável](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável

1. Para remover um certificado raiz confiável, navegue até a página **Configuração ponto a site** de seu gateway de rede virtual.
2. Na seção **Certificado raiz** da página, encontre o certificado que você deseja remover.
3. Selecione as reticências ao lado do certificado e, em seguida, selecione ' remover '.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Para revogar um certificado de cliente

É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticação.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Você pode revogar um certificado de cliente adicionando a impressão digital à lista de revogação.

1. Recupere a impressão digital do certificado de cliente. Para obter mais informações, consulte [como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua.
3. Navegue até a página **Configuração ponto a site** do gateway de rede virtual. É a mesma página que você usou para [carregar um certificado raiz confiável](#uploadfile).
4. Na seção **Certificados revogados**, insira um nome amigável para o certificado (não precisa ser o CN do certificado).
5. Copie e cole a cadeia de caracteres de impressão digital no campo **Impressão digital**.
6. A impressão digital é validada e adicionada automaticamente à lista de revogação. Uma mensagem aparece na tela informando que a lista está atualizando. 
7. Após a conclusão da atualização, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado recebem uma mensagem informando que o certificado não é mais válido.

## <a name="point-to-site-faq"></a><a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Para obter informações sobre solução de problemas de P2S, consulte [Solução de problemas de conexões de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
