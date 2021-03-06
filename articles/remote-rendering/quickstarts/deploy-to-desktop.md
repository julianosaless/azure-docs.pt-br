---
title: Implantar a amostra de Unity na Área de Trabalho
description: Início Rápido que mostra como obter o exemplo do Unity em um PC desktop
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415717"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Início Rápido: Implantar a amostra de Unity na Área de Trabalho

Este início rápido aborda como implantar e executar o aplicativo de exemplo de início rápido para Unity em um PC desktop.

Neste início rápido, você aprenderá a:

> [!div class="checklist"]
>
>* Criar o aplicativo de exemplo de início rápido para desktop
>* Implantar o exemplo em um PC
>* Executar o exemplo em um PC

## <a name="prerequisites"></a>Pré-requisitos

Neste início rápido, implantaremos o projeto de exemplo com base em [Início Rápido: Renderizar um modelo com o Unity](render-model.md).

Verifique se suas credenciais foram salvas corretamente com a cena e se você pode se conectar a uma sessão de dentro do editor do Unity.

## <a name="disable-virtual-reality-support"></a>Desabilitar o suporte à realidade virtual

No momento, há suporte apenas para aplicativos da área de trabalho simples no desktop, portanto o suporte à VR precisa ser desabilitado.

1. Abra *Editar > Configurações do Projeto...*
1. Selecione **Player** à esquerda.
1. Selecione a guia **Configurações da Plataforma Universal do Windows**.
1. Expanda **Configurações de XR**.
1. Desabilite a **Realidade Virtual com Suporte**.
    ![configurações do player](./media/unity-disable-xr.png)
1. Em cima de *Configurações de XR*, expanda **Configurações de Publicação**.
1. Em **Famílias de Dispositivos com Suporte**, verifique se **Área de Trabalho** está selecionada.

## <a name="build-the-sample-project"></a>Criar o projeto de exemplo

1. Abra *Arquivo > Configurações de Build*.
1. Altere *Plataforma* para **Plataforma Universal do Windows**.
1. Defina o *Dispositivo de Destino* como **PC**.
1. Defina *Arquitetura* como **x86**.
1. Defina *Tipo de Build* como **Projeto D3D**.
  ![Configurações de build](./media/unity-build-settings-pc.png)
1. Selecione **Alterar para a Plataforma**.
1. Ao pressionar **Criar** (ou 'Criar e Executar'), você deverá selecionar alguma pasta na qual a solução deve ser armazenada.
1. Abra o **Quickstart.sln** gerado com o Visual Studio.
1. Altere a configuração para **Versão** e **x86**.
1. Alterne o modo do depurador para **Computador Local**.
  ![Configuração da solução](./media/unity-deploy-config-pc.png)
1. Crie a solução (F7).

> [!WARNING]
> Selecione **x86**. O **x64** não é compatível no momento, confira as [limitações de plataforma](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Iniciar o projeto de exemplo

Interrompa o Depurador no Visual Studio (F5). Ele implantará automaticamente o aplicativo no PC.

O aplicativo de exemplo deve ser iniciado e, em seguida, iniciar uma nova sessão. Após alguns instantes, a sessão estará pronta e o modelo renderizado remotamente será exibido na frente de você.
Se desejar iniciar o exemplo uma segunda vez mais tarde, você também poderá encontrá-lo no menu iniciar agora.

## <a name="next-steps"></a>Próximas etapas

No próximo início rápido, veremos a conversão de um modelo personalizado.

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para renderização](convert-model.md)
