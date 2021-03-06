---
title: A ferramenta de inspeção ArrInspector
description: Manual do usuário da ferramenta ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680070"
---
# <a name="the-arrinspector-inspection-tool"></a>A ferramenta de inspeção ArrInspector

O ArrInspector é uma ferramenta baseada na Web usada para inspecionar uma sessão de renderização remota do Azure em execução. Ela deve ser usada para fins de depuração, para inspecionar a estrutura da cena que está sendo processada, mostrar as mensagens de log e monitorar o desempenho ao vivo no servidor.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Conectando-se ao ArrInspector

Depois de obter o nome de host ( `mixedreality.azure.com`terminando em) do seu servidor ARR, conecte-se usando [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Essa função cria um `StartArrInspector.html` no dispositivo no qual o aplicativo está sendo executado. Para iniciar o ArrInspector, abra esse arquivo com um navegador (Edge, Firefox ou Chrome) em um PC. O arquivo só é válido por 24 horas.

Se o aplicativo que chama `ConnectToArrInspectorAsync` o já estiver em execução em um PC:

* Se você estiver usando a integração do Unity, ela poderá ser iniciada automaticamente para você.
* Caso contrário, você encontrará o arquivo nas *pastas\\de\\usuário LocalAppData [\\your_app\\] AC Temp*.

Se o aplicativo estiver em execução em um HoloLens:

1. Acesse o HoloLens usando o [portal de dispositivo do Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Vá para *sistema > explorador de arquivos*.
1. Navegue até *pastas\\de usuário\\LocalAppData [your_app\\]\\AC Temp*.
1. Salve *StartArrInspector. html* em seu PC.
1. Abra *StartArrInspector. html* para carregar o ArrInspector da sessão.

## <a name="the-performance-panel"></a>O painel de desempenho

![O painel de desempenho](./media/performance-panel.png)

Esse painel mostra grafos de todos os valores de desempenho por quadro expostos pelo servidor. Os valores atualmente incluem o tempo de quadro, FPS, uso de CPU e memória, estatísticas de memória como uso geral de RAM, contagens de objetos, etc.

Para visualizar um desses parâmetros, clique no botão **Adicionar novo** e selecione um dos valores disponíveis mostrados na caixa de diálogo. Essa ação adiciona um novo gráfico de rolagem ao painel, rastreando os valores em tempo real. À direita, você pode ver o valor *mínimo*, *máximo* e *atual* .

Você pode deslocar o grafo, arrastando seu conteúdo com o mouse, no entanto, o movimento panorâmico horizontalmente só é possível quando ArrInspector está no estado pausado.

Manter CTRL ao arrastar, permite que você Aplique zoom. O zoom horizontal também pode ser controlado com o controle deslizante na parte inferior.

O intervalo vertical é, por padrão, calculado com base nos valores exibidos atualmente, e os valores mínimo e máximo são mostrados nas caixas de texto à direita. Quando os valores são definidos manualmente, seja digitando-os diretamente na caixa de texto ou movendo-se o gráfico de forma panorâmica ou zoom, o grafo usará esses valores. Para restaurar o enquadramento vertical automático, clique no ícone no canto superior direito.

![intervalo vertical](./media/vertical-range.png)

## <a name="the-log-panel"></a>O painel de log

![Painel de log](./media/log-panel.png)

O painel log mostra uma lista de mensagens de log geradas no lado do servidor. Na conexão, ele mostrará até 200 mensagens de log anteriores e imprimirá as novas à medida que elas ocorrerem.

Você pode filtrar a lista com base no tipo `[Error/Warning/Info/Debug]` de log usando os botões na parte superior.
![Botões de filtro de log](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>O painel de captura de dados de tempo

![Captura de dados de tempo](./media/timing-data-capture.png)

Esse painel é usado para capturar informações de tempo do servidor e baixá-las. O arquivo usa o [formato JSON de rastreamento do Chrome](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Para inspecionar os dados, abra o Chrome na `Chrome://tracing` URL e arraste e solte o arquivo baixado para a página. Os dados de tempo são coletados continuamente em um buffer de anéis de tamanho fixo. Quando escrito, a captura inclui apenas informações sobre o passado imediato, o que significa alguns segundos a alguns minutos.

## <a name="the-scene-inspection-panel"></a>O painel de inspeção de cena

![Painel de inspeção de cena](./media/scene-inspection-panel.png)

Este painel mostra a estrutura da cena renderizada. A hierarquia de objetos está à esquerda, o conteúdo do objeto selecionado está à direita. O painel é somente leitura e é atualizado em tempo real.

## <a name="the-vm-debug-information-panel"></a>O painel de informações de depuração da VM

![Painel de informações de depuração da VM](./media/state-debugger-panel.png)

Esse painel oferece algumas funcionalidades de depuração.

### <a name="restart-service"></a>Reiniciar o serviço

O botão **reiniciar serviço** reinicia o tempo de execução na máquina virtual à qual o arrInspector está conectado. Qualquer cliente conectado será desconectado e a página arrInspector deverá ser recarregada para se conectar ao serviço reiniciado.

### <a name="collect-debug-information"></a>Coletar informações de depuração

O botão **coletar informações de depuração para VM** abre uma caixa de diálogo que permite disparar a instância arr para coletar informações de depuração na VM:

![Caixa de diálogo informações de depuração da VM](./media/state-debugger-dialog.png)

As informações de depuração ajudam a equipe de renderização remota do Azure a analisar os problemas que ocorrem em uma instância do ARR em execução. A caixa de diálogo tem um campo de texto para fornecer detalhes adicionais, por exemplo, etapas para reproduzir um problema.

Depois de clicar no botão **Iniciar coleta** , a caixa de diálogo será fechada e o processo de coleta começará. A coleta das informações na VM pode levar alguns minutos.

![Coleta de informações de depuração da VM em andamento](./media/state-debugger-panel-in-progress.png)

Depois que a coleta for concluída, você receberá uma notificação na janela ArrInspector. Essa notificação contém uma ID que identifica essa coleção em particular. Lembre-se de salvar essa ID para passá-la para a equipe de renderização remota do Azure.

![Êxito na coleta de informações de depuração da VM](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Não é possível baixar ou acessar informações de depuração da VM. Somente a equipe de renderização remota do Azure tem acesso aos dados coletados. Você precisa entrar em contato conosco e enviar a ID da coleção, para que possamos investigar o problema que você está vendo.

## <a name="pause-mode"></a>Modo de pausa

No canto superior direito, uma opção permite que você Pause a atualização dinâmica dos painéis. Esse modo pode ser útil para inspecionar cuidadosamente um estado específico.

![Modo de pausa](./media/pause-mode.png)

Ao habilitar novamente o Live Update, todos os painéis são redefinidos.

## <a name="host-configuration"></a>Configuração do host

Por padrão, a ferramenta se conecta ao servidor ARR que está em execução no mesmo host que atende ao ArrInspector. No entanto, você pode configurá-lo para inspecionar outro servidor, supondo que esteja executando uma instância ARR com a porta de ferramentas aberta.

Para fazer isso, acesse o menu principal à esquerda da barra de cabeçalho e selecione *configuração do host*. Clique em **Adicionar novo host**e digite o nome e o hostname. Para o *nome de host* , use apenas `.mixedreality.azure.com`o nome de `http://` host que termina em, não inclua ou uma porta.

![Configuração do host](./media/host-configuration.png)

Para alternar rapidamente de um host para outro, use a lista suspensa no canto superior direito.

![Combinação de hosts](./media/host-switch-combo.png)

A lista de hosts é armazenada no armazenamento local do navegador, portanto, será preservada ao reabrir o mesmo navegador.
