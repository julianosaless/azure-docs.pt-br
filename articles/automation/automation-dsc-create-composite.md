---
title: Converter configurações para recursos de composição para configuração de estado – automação do Azure
description: Saiba como converter configurações para recursos de composição para configuração de estado na automação do Azure.
keywords: DSC,powershell,configuração,instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585522"
---
# <a name="convert-configurations-to-composite-resources"></a>Converter configurações em recursos de composição

> Aplica-se a: Windows PowerShell 5,1

Depois de começar a criar as configurações, você pode criar rapidamente "cenários" que gerenciam grupos de configurações.
Exemplos seriam:

- criar um servidor Web
- criar um servidor DNS
- criar um servidor do SharePoint
- configurar um cluster do SQL
- Gerenciar configurações de firewall
- Gerenciar configurações de senha

Se você estiver interessado em compartilhar esse trabalho com outras pessoas, a melhor opção é empacotar a configuração como um [recurso de composição](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Criar recursos de composição pela primeira vez pode ser impressionante.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade de código-fonte aberto.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-compositeresource"></a>Projeto da Comunidade: CompositeResource

Uma solução mantida pela comunidade chamada [CompositeResource](https://github.com/microsoft/compositeresource) foi criada para resolver esse desafio.

O CompositeResource automatiza o processo de criação de um novo módulo a partir de sua configuração.
Você começa por [dot sourcing](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) do script de configuração em sua estação de trabalho (ou servidor de Build) para que ele seja carregado na memória.
Em seguida, em vez de executar a configuração para gerar um arquivo MOF, use a função fornecida pelo módulo CompositeResource para automatizar uma conversão.
O cmdlet carregará o conteúdo de sua configuração, obterá a lista de parâmetros e gerará um novo módulo com tudo o que você precisa.

Depois de gerar um módulo, você pode incrementar a versão e adicionar notas de versão cada vez que fizer alterações e publicá-las em seu próprio [repositório do PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Depois de criar um módulo de recurso de composição contendo sua configuração (ou várias configurações), você pode usá-los na [experiência de criação combinável](/azure/automation/compose-configurationwithcompositeresources) no Azure ou adicioná-los aos [scripts de configuração DSC](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a automação do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores no [local](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) para efetuar pull das configurações.
A atualização mais recente para o projeto também publicou [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) para a automação do Azure para automatizar o processo de importação de configurações do Galeria do PowerShell.

Para experimentar a automatização da criação de recursos de composição para DSC, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/compositeresource/) e baixe a solução ou clique em "site do projeto" para exibir a [documentação](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Próximas etapas

- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos de DSC](/powershell/scripting/dsc/resources/resources)
- [Configurando o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig)
