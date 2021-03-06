---
title: Criar um aplicativo de mapa acessível com o Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a criar um aplicativo com recursos de acessibilidade usando mapas de Microsoft Azure.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473312"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Versões anteriores de 20% dos usuários da Internet têm a necessidade de aplicativos Web acessíveis. Assim, é importante ter certeza de que seu aplicativo foi projetado de forma que qualquer usuário possa usá-lo facilmente. Em vez de pensar na acessibilidade como um conjunto de tarefas a serem concluídas, pense nisso como parte da experiência geral do usuário. Quanto mais acessível for seu aplicativo, mais pessoas poderão usá-lo. 

Quando se trata de conteúdo interativo avançado como um mapa, algumas considerações de acessibilidade comuns são:
- Dê suporte ao leitor de tela para usuários que têm dificuldade em ver o aplicativo Web.
- Ter vários métodos para interagir com e navegar pelo aplicativo Web, como mouse, toque e teclado.
- Verifique se o contraste de cores é que as cores não se misturam e se tornam difíceis de distinguir umas das outras. 

O SDK da Web do Azure Maps vem precompilado com muitos recursos de acessibilidade, como:
- Descrições do leitor de tela quando o mapa se move e quando o usuário se concentra em um controle ou pop-up.
- Suporte ao mouse, ao toque e ao teclado.
- Suporte de contraste de cores acessível no estilo de mapa de estrada.

Detalhes completos de conformidade de acessibilidade para todos os produtos da Microsoft podem ser encontrados [aqui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Pesquise "Azure Maps Web" para localizar o documento especificamente para o SDK da Web do Azure Maps. 

## <a name="navigating-the-map"></a>Navegando no mapa

Há várias maneiras diferentes pelas quais o mapa pode ser ampliado, panorâmico, girado e com densidade. Os detalhes a seguir são todas as diferentes maneiras de navegar no mapa.

**Aplicar zoom ao mapa**

- Usando um mouse, clique duas vezes no mapa para aplicar zoom em um nível.
- Usando um mouse, role a roda para aplicar zoom no mapa.
- Usando uma tela sensível ao toque, toque o mapa com dois dedos e aperte-o para reduzir ou espalhar os dedos para ampliar.
- Usando uma tela sensível ao toque, toque duas vezes no mapa para ampliar um nível.
- Com o mapa focalizado, use o sinal de`+`adição () ou o`=`sinal de igual () para aplicar zoom em um nível.
- Com o mapa focalizado, use o sinal de subtração`-`, hífen () ou sublinhado (`_`) para reduzir um nível.
- Usando o controle de zoom com uma tecla de mouse, toque ou teclado/Insira as teclas.
- Pressione e segure o `Shift` botão e pressione o botão esquerdo do mouse para baixo no mapa e arraste para desenhar uma área para aplicar zoom no mapa.

**Deslocar o mapa**

- Usando um mouse, pressione o botão esquerdo do mouse no mapa e arraste em qualquer direção.
- Usando uma tela sensível ao toque, toque no mapa e arraste em qualquer direção.
- Com o mapa focalizado, use as teclas de direção para mover o mapa.

**Girar o mapa**

- Usando um mouse, pressione o botão direito do mouse no mapa e arraste para a esquerda ou para a direita. 
- Usando uma tela sensível ao toque, toque no mapa com dois dedos e girar.
- Com o mapa focalizado, use a tecla Shift e as teclas de seta para a esquerda ou para a direita.
- Usando o controle de rotação com uma tecla de mouse, toque ou teclado/insira chaves.

**Pitch do mapa**

- Usando o mouse, pressione o botão direito do mouse no mapa e arraste para cima ou para baixo. 
- Usando uma tela sensível ao toque, toque o mapa com dois dedos e arraste-os para cima ou para baixo juntos.
- Com o mapa focalizado, use a tecla Shift mais as teclas de seta para cima ou para baixo. 
- Usando o controle de densidade com uma tecla de mouse, toque ou teclado/insira chaves.

## <a name="change-the-map-style"></a>Alterar o estilo do mapa

Nem todos os desenvolvedores querem que todos os estilos de mapa possíveis estejam disponíveis em seu aplicativo. Se o desenvolvedor exibir o controle do seletor de estilo do mapa, o usuário poderá alterar o estilo do mapa usando o mouse, um toque ou o teclado com a tecla TAB ou Enter. O desenvolvedor pode especificar quais estilos de mapa eles desejam disponibilizar no controle do seletor de estilo do mapa. Além disso, o desenvolvedor pode definir e alterar o estilo de mapa de forma programática.

**Usar alto contraste**

- Quando o controle de mapa é carregado, ele verifica se o alto contraste está habilitado e o navegador dá suporte a ele.
- O controle de mapa não monitora o modo de alto contraste do dispositivo. Se o modo do dispositivo for alterado, o mapa não será. Portanto, o usuário precisará recarregar o mapa atualizando a página.
- Quando o alto contraste for detectado, o estilo do mapa mudará automaticamente para alto contraste e todos os controles internos usarão um estilo de alto contraste. Por exemplo, ZoomControl, PitchControl, CompassControl, StyleControl e outros controles internos, usarão um estilo de alto contraste.
- Há dois tipos de alto contraste, claro e escuro. Se o tipo de alto contraste puder ser detectado pelos controles de mapa, o comportamento do mapa será ajustado de acordo. Se Light, o estilo de mapa de grayscale_light será carregado. Se o tipo não puder ser detectado ou for escuro, o estilo de high_contrast_dark será carregado.
- Se você estiver criando controles personalizados, será útil saber se os controles internos estão usando um estilo de alto contraste. Os desenvolvedores podem adicionar uma classe CSS na div contêiner do mapa para verificar. As classes CSS que seriam adicionadas são `high-contrast-dark` e `high-contrast-light`. Para verificar o uso do JavaScript, use:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

ou use:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

O mapa tem vários atalhos de teclado internos que facilitam o uso do mapa. Esses atalhos de teclado funcionam quando o mapa tem foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `Tab` | Navegue pelos controles e pop-ups no mapa. |
| `ESC` | Mova o foco de qualquer elemento no mapa para o elemento de mapa de nível superior. |
| `Ctrl` + `Shift` + `D` | Alternar nível de detalhe do leitor de tela.  |
| Tecla de seta para a esquerda | Deslocar o mapa para a esquerda 100 pixels |
| Tecla de seta para a direita | Deslocar o mapa para a direita 100 pixels |
| Seta para baixo | Deslocar o mapa para baixo 100 pixels |
| Tecla de seta para cima | Deslocar o mapa para cima em 100 pixels |
| `Shift`+ seta para cima | Aumentar a densidade do mapa em 10 graus |
| `Shift`+ seta para baixo | Diminuir o tom do mapa por 10 graus |
| `Shift`+ seta para a direita | Girar o mapa 15 graus no sentido horário |
| `Shift`+ seta para a esquerda | Girar o mapa 15 graus no sentido anti-horário |
| Sinal de adição`+`() <sup>*</sup>ou sinal de`=`igual () | Ampliar |
| Sinal de subtração,`-`hífen () <sup>*</sup>ou sublinhado (`_`) | Reduzir | 
| `Shift`+ arrastar com o mouse sobre o mapa para a área de desenho | Ampliar área |

<sup>*</sup>Esses atalhos-chave geralmente compartilham a mesma chave em um teclado. Esses atalhos foram adicionados para melhorar a experiência do usuário. Também não importa se o usuário usa a tecla Shift ou não para esses atalhos.

## <a name="screen-reader-support"></a>Suporte ao leitor de tela

Os usuários podem navegar o mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário de alterações em seu estado. Por exemplo, os usuários serão notificados das alterações do mapa quando o mapa é deslocado ou ampliado. Por padrão, o mapa fornece descrições simplificadas que excluem o nível de zoom e as coordenadas do centro do mapa. O usuário pode alternar o nível de detalhe dessas descrições usando a pequena recorte `Ctrl`  +  `Shift`  +  `D`do teclado.

Qualquer informação adicional é colocada no mapa de base deve ter informações textuais correspondentes para os usuários de leitores de tela. Certifique-se de adicionar os atributos de [Aria (aplicativos de Internet avançados)](https://www.w3.org/WAI/standards-guidelines/aria/), ALT e título acessíveis quando apropriado. 

## <a name="make-popups-keyboard-accessible"></a>Tornar o teclado de pop-ups acessível

Um marcador ou símbolo geralmente é usado para representar um local no mapa. Informações adicionais sobre o local normalmente são exibidas em um pop-up quando o usuário interage com o marcador. Na maioria dos aplicativos, os pop-ups aparecem quando um usuário clica ou toca em um marcador. No entanto, clicar e tocar exigirá que o usuário use um mouse e uma tela sensível ao toque, respectivamente. Uma boa prática é tornar os pop-ups acessíveis ao usar um teclado. Essa funcionalidade pode ser obtida criando-se um pop-up para cada ponto de dados e adicionando-o ao mapa. 

O exemplo a seguir carrega pontos de interesse no mapa usando uma camada de símbolo e adiciona um pop-up ao mapa para cada ponto de interesse. Uma referência a cada popup é armazenada nas propriedades de cada ponto de dados. Ele também pode ser recuperado para um marcador, como quando um marcador é clicado. Quando focalizado no mapa, pressionar a tecla Tab permitirá que o usuário percorra cada pop-up no mapa.

<br/>

<iframe height='500' scrolling='no' title='Criar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Criar um aplicativo acessível</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Dicas adicionais de acessibilidade

Aqui estão algumas dicas adicionais para tornar o aplicativo de mapeamento da Web mais acessível.

- Ao exibir muitos dados de ponto interativo no mapa, considere a possibilidade de reduzir a desordem e usar o clustering. 
- Verifique se a taxa de contraste de cores entre texto/símbolos e cores de plano de fundo é 4.5:1 ou mais.
- Mantenha suas mensagens de leitor de tela (atributos do ARIA, ALT e title) curtas, descritivas e significativas. Evite um jargão e acrônimos desnecessários.
- Tente otimizar as mensagens enviadas para o leitor de tela para fornecer informações significativas que sejam fáceis para o usuário resumir. Por exemplo, se você quiser atualizar o leitor de tela em uma alta frequência, como quando o mapa está sendo movido, considere fazer os seguintes pontos:
    - Aguarde até que o mapa termine de migrar para atualizar o leitor de tela.
    - Limite as atualizações para uma vez a cada segundos. 
    - Combine mensagens de maneira lógica. 
- Evite usar cores como o único meio de transmitir informações. Use texto, ícones ou padrões para complementar ou substituir a cor. Algumas considerações:
    - Se estiver usando uma camada de bolha para mostrar o valor relativo entre os pontos de dados, considere dimensionar o raio de cada bolha, colorir a bolha ou ambos. 
    - Considere usar uma camada de símbolo com ícones diferentes para categorias de métrica diferentes, como triângulos, estrelas e quadrados. A camada de símbolo também dá suporte ao dimensionamento do tamanho do ícone. Um rótulo de texto também pode ser exibido.
    - Ao exibir dados de linha, a largura pode ser usada para representar peso ou tamanho. Um padrão de matriz Dash pode ser usado para representar diferentes categorias de linhas. Uma camada de símbolo pode ser usada em combinação com uma linha para sobrepor ícones ao longo da linha. O uso de um ícone de seta é útil para mostrar o fluxo ou a direção da linha.
    - Se exibir dados de polígono, um padrão, como faixas, pode ser usado como uma alternativa à cor. 
- Algumas visualizações, como calor, camadas de bloco e camadas de imagem, não são acessíveis para usuários com deficiências visuais. Algumas considerações:
    - Faça com que o leitor de tela Descreva o que a camada está exibindo quando adicionada ao mapa. Por exemplo, se uma camada de bloco de radar de clima for exibida, faça com que o leitor de tela diga que "os dados de radar de clima estão sobrepostos no mapa".
- Limite a quantidade de funcionalidade que exige um foco do mouse. Essas funcionalidades ficarão inacessíveis aos usuários que estão usando um teclado ou dispositivo de toque para interagir com o aplicativo. Observe que ainda é uma boa prática ter um estilo de foco para conteúdo interativo, como ícones clicáveis, links e botões.
- Tente navegar pelo seu aplicativo usando o teclado. Verifique se a ordem de tabulação é lógica.
- Se estiver criando atalhos de teclado, Tente limitá-lo para duas chaves ou menos. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre acessibilidade nos módulos do SDK da Web.

> [!div class="nextstepaction"]
> [Acessibilidade das ferramentas de desenho](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre como desenvolver aplicativos acessíveis com o Microsoft Learn:

> [!div class="nextstepaction"]
> [Caminho de aprendizado de notificação digital de acessibilidade em ação](https://ready.azurewebsites.net/learning/track/2940)

Dê uma olhada nessas ferramentas de acessibilidade úteis:
> [!div class="nextstepaction"]
> [Desenvolvendo aplicativos acessíveis](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-visão geral do ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [ONDA (ferramenta de avaliação de acessibilidade da Web)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verificador de contraste de cores WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Não há simulador de visão de café](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
