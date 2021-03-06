---
title: Introdução ao controle de mapeamento do Android | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a começar a usar o controle de mapeamento do Android usando o SDK do Android de mapas de Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6e0f0f311b7ec8adae6ddb25e01046141adadfa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548549"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao Azure Maps SDK do Android

O Azure Maps SDK do Android é uma biblioteca de mapas de vetor para Android. Este artigo orienta você pelos processos de instalação do Azure Maps SDK do Android e carregamento de um mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para concluir os procedimentos deste artigo, primeiro você precisa [criar uma conta do Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) no tipo de preço S1 e [obter a chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) da sua conta.

Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Baixar Android Studio

Baixe Android Studio e crie um projeto com uma atividade vazia antes de instalar o Azure Maps SDK do Android. Você pode [baixar Android Studio](https://developer.android.com/studio/) gratuitamente do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, crie um novo projeto com uma atividade vazia. Conclua estas etapas para criar um projeto de Android Studio:

1. Em **escolher seu projeto**, selecione **telefone e Tablet**. Seu aplicativo será executado neste fator forma.
2. Na guia **telefone e Tablet** , selecione **atividade vazia**e, em seguida, selecione **Avançar**.
3. Em **Configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Esta é a versão mais antiga com suporte do Azure Maps SDK do Android.
4. Aceite o padrão `Activity Name` e `Layout Name` selecione **concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda sobre como instalar Android Studio e criar um novo projeto.

![Criar um projeto no Android Studio ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite configurar um dispositivo Android virtual no computador. Isso pode ajudá-lo a testar seu aplicativo durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gerenciador de dispositivo virtual Android (AVD) no canto superior direito da tela do projeto e, em seguida, selecione **criar dispositivo virtual**. Você também pode acessar o AVD Manager selecionando **ferramentas** > **Android** > **AVD Manager** na barra de ferramentas. Na categoria **telefones** , selecione **Nexus 5x**e, em seguida, selecione **Avançar**.

Você pode saber mais sobre como configurar um AVD na [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK do Android do Azure Maps

A próxima etapa na criação do aplicativo é instalar o SDK do Android do Azure Maps. Conclua estas etapas para instalar o SDK:

1. Abra o arquivo de nível superior **build.gradle** e adicione o código a seguir à seção do bloco **todos os projetos**, **repositórios**:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize o **app/build.gradle** e adicione a ele o seguinte código:
    
    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o código a seguir à seção do Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicione uma nova linha de dependência de implementação para o mais recente SDK do Android do Azure Mapas:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Vá para **Arquivo** na barra de ferramentas e clique em **Sincronizar Projeto com Arquivos Gradle**.
3. Adicione um fragmento de mapa à atividade principal (res \> layout \> activity\_main.xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. No arquivo **MainActivity.java**, você precisará:
    
    * adicionar importações para o SDK do Azure Mapas
    * definir as informações de autenticação do Azure Mapas
    * obter a instância de controle de mapa no método **OnCreate**

    Definir as informações de autenticação globalmente na classe `AzureMaps` usando os métodos `setSubscriptionKey` ou `setAadProperties` faz com que você não precise adicionar suas informações de autenticação a cada exibição. 

    O controle de mapa contém os próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android. Esses métodos de ciclo de vida devem ser chamados diretamente da atividade que a contém. Para que seu aplicativo chame corretamente os métodos de ciclo de vida do controle de mapa, você deve substituir os seguintes métodos de ciclo de vida na atividade que contém o controle de mapa. E, você deve chamar o respectivo método de controle de mapa. 

    * onCreate (pacote) 
    * OnStart () 
    * OnContinue () 
    * OnPause () 
    * onStop () 
    * OnDestroy () 
    * onSaveInstanceState (pacote) 
    * onLowMemory() 

    Edite o arquivo **MainActivity.java** da seguinte maneira:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

## <a name="import-classes"></a>Importar classes

Depois de concluir as etapas anteriores, você provavelmente receberá avisos de Android Studio sobre parte do código. Para resolver esses avisos, importe as classes referenciadas `MainActivity.java`no.

Você pode importar automaticamente essas classes selecionando Alt + Enter (opção + retornar em um Mac).

Selecione o botão Executar, conforme mostrado no gráfico a seguir (ou pressione Control + R em um Mac) para compilar seu aplicativo.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio levará alguns segundos para criar o aplicativo. Depois que a compilação for concluída, você poderá testar seu aplicativo no dispositivo Android emulado. Você deverá ver um mapa como este:

<center>

![Mapas do Azure no aplicativo Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Como localizar o mapa

O Azure Maps SDK do Android fornece três maneiras diferentes de definir o idioma e a exibição regional do mapa. O código a seguir mostra como definir o idioma como francês ("fr-FR") e a exibição regional como "auto". 

A primeira opção é passar o idioma e exibir informações regionais na `AzureMaps` classe usando os métodos estáticos `setLanguage` e `setView` globalmente. Isso definirá o idioma padrão e a exibição regional em todos os controles do Azure Maps carregados em seu aplicativo.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

A segunda opção é passar as informações de idioma e de exibição para o XML de controle de mapeamento.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

A terceira opção é definir programaticamente o idioma e a exibição regional do mapa usando o método `setStyle` dos mapas. Isso pode ser feito a qualquer momento para alterar o idioma e a exibição regional do mapa.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Aqui está um exemplo de mapas do Azure com a linguagem definida como "fr-FR" e exibição regional definida como "auto".

<center>

![Mapas do Azure, imagem de mapa mostrando rótulos em francês](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Uma lista completa de idiomas e exibições regionais compatíveis está documentada [aqui](supported-languages.md).

## <a name="navigating-the-map"></a>Navegando no mapa

Há várias maneiras diferentes pelas quais o mapa pode ser ampliado, panorâmico, girado e com densidade. Os detalhes a seguir são todas as diferentes maneiras de navegar no mapa.

**Aplicar zoom ao mapa**

- Toque o mapa com dois dedos e aperte-o para reduzir ou espalhar os dedos para ampliar.
- Toque duas vezes no mapa para aplicar zoom em um nível.
- Toque duas vezes com dois dedos para ampliar o mapa um nível.
- Toque duas vezes; no segundo toque, mantenha seu dedo no mapa e arraste para cima para ampliar ou reduzir para reduzir.

**Deslocar o mapa**

- Toque no mapa e arraste em qualquer direção.

**Girar o mapa**

- Toque no mapa com dois dedos e girar.

**Pitch do mapa**

- Toque no mapa com dois dedos e arraste-os para cima ou para baixo juntos.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo a um mapa do Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa do Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Alterar estilos de mapa em mapas do Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
