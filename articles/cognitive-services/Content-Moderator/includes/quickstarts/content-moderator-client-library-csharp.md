---
title: Início rápido da biblioteca de clientes .NET do Content Moderator
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de clientes do Content Moderator para .NET com este início rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 50572c037cc7927a06bcf8d95305353238989ae9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186510"
---
Introdução à biblioteca de clientes do Content Moderator para .NET. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Content Moderator é um serviço cognitivo que verifica conteúdo de textos, imagens e vídeos para verificar material que possa ser ofensivo, de risco ou, de alguma maneira, indesejável. Quando tal material é encontrado, o serviço aplica rótulos (sinalizadores) apropriados ao conteúdo. Seu aplicativo pode tratar de conteúdo sinalizado para cumprir as normas ou manter o ambiente desejado para os usuários.

Use a biblioteca de clientes do Content Moderator para .NET para:

* [Moderar texto](#moderate-text)
* [Moderar imagens](#moderate-images)
* [Criar uma revisão](#create-a-review)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Exemplos](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-content-moderator-azure-resource"></a>Criar um recurso do Azure no Content Moderator

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Content Moderator usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Também é possível:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir o recurso no [portal do Azure](https://portal.azure.com/)

Depois de obter uma chave da assinatura ou do recurso de avaliação, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e a URL de ponto de extremidade, chamadas `CONTENT_MODERATOR_SUBSCRIPTION_KEY` e `CONTENT_MODERATOR_ENDPOINT`, respectivamente.

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Criar um aplicativo .NET Core em seu IDE ou editor de texto preferido. 

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `content-moderator-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes declarações de `using` :

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Na classe **Programa**, crie variáveis para a chave e a localização do ponto de extremidade do recurso como variáveis de ambiente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Se você criou as variáveis de ambiente depois de iniciar o aplicativo, feche e abra novamente o editor, o IDE ou o shell que o executa para acessar as variáveis.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Content Moderator para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos da biblioteca de clientes do .NET do Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Essa classe é necessária para toda a funcionalidade do Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo para adulto, informações pessoais ou rostos humanos.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Análises](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Essa classe fornece a funcionalidade das APIs de Revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|

## <a name="code-examples"></a>Exemplos de código


Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Content Moderator para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Moderar texto](#moderate-text)
* [Moderar imagens](#moderate-images)
* [Criar uma revisão](#create-a-review)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, crie uma instância de objetos do cliente com o ponto de extremidade e a chave. Você não precisa de um cliente diferente para cada cenário, mas isso pode ajudar a manter seu código organizado.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Moderar texto

O código a seguir usa um cliente do Content Moderator para analisar um corpo de texto e imprimir os resultados no console. Na raiz de sua classe **Program**, defina os arquivos de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Em seguida, na raiz do seu projeto, adicione um arquivo *TextFile.txt*. Adicione seu próprio texto a este arquivo ou use o seguinte texto de exemplo:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Adicione a seguinte chamada de método ao seu método `Main`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

Em seguida, defina o método de moderação de texto em algum lugar de sua classe **Program**:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Moderar imagens

O seguinte código usa um cliente do Content Moderator, em conjunto com um objeto [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet), para analisar imagens remotas para detectar conteúdo adulto e obsceno.

> [!NOTE]
> Você também pode analisar o conteúdo de uma imagem local. Consulte a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) para ver métodos e operações que funcionam com imagens locais.

### <a name="get-sample-images"></a>Obter imagens de exemplo

Defina seus arquivos de entrada e saída:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Em seguida, crie o arquivo de entrada, *ImageFiles.txt*, na raiz do projeto. Nesse arquivo, você adiciona as URLs de imagens para analisar&mdash;uma URL em cada linha. Use as seguintes imagens de exemplo:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Passe os arquivos de entrada e saída para a seguinte chamada de método no método `Main`. Você definirá esse método em uma etapa posterior.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definir a classe auxiliar

Adicione a seguinte definição de classe dentro da classe **Program**. Essa classe interna manipulará os resultados da moderação de imagem.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definir o método de moderação de imagem

O método a seguir itera as URLs de imagem em um arquivo de texto, cria uma instância de **EvaluationData** e analisa a imagem para detectar conteúdo adulto/erótico, texto e rostos humanos. Em seguida, ele adiciona a instância final de **EvaluationData** a uma lista e grava a lista completa de dados retornados no console.

#### <a name="iterate-through-image-urls"></a>Iterar as URLs de imagens

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analisar conteúdo

Para obter mais informações sobre os atributos de imagem que o Content Moderator avalia, consulte o guia [Conceitos de moderação de imagem](../../image-moderation-api.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Gravar os resultados da moderação em arquivo

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Criar uma revisão

Você pode usar a biblioteca de clientes do .NET do Content Moderator para alimentar conteúdo na [Ferramenta de análise](https://contentmoderator.cognitive.microsoft.com) para que os moderadores humanos possam analisá-lo. Para saber mais sobre a ferramenta Revisão, confira o [Guia conceitual da ferramenta Revisão](../../review-tool-user-guide/human-in-the-loop.md).

O método nesta seção usa a classe [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) para criar uma revisão, recuperar sua ID e verificar seus detalhes depois de receber a entrada humana por meio do portal da Web da Ferramenta de análise. Ele registra todas essas informações em um arquivo de texto de saída. Chame o método de seu método `Main`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Obter imagens de exemplo

Declare a seguinte matriz na raiz de sua classe **Program**. Essa variável faz referência a uma imagem de exemplo a ser usada para criar a revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Obter credenciais de revisão

Entre na [Ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) e recupere o nome da equipe. Em seguida, atribua-o à variável apropriada na classe **Program**. Como alternativa, você pode configurar um ponto de extremidade de retorno de chamada para receber atualizações sobre a atividade da revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definir a classe auxiliar

Adicione a seguinte definição de classe dentro da classe **Program**. Essa classe será usada para representar uma única instância de revisão que é enviada para a Ferramenta de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definir o método auxiliar

Adicione o seguinte método à classe **Programa**. Esse método gravará os resultados das consultas de revisão no arquivo de texto de saída.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definir o método de criação da revisão

Agora, você está pronto para definir o método que manipulará a criação e a consulta da revisão. Adicione um novo método, **CreateReviews**, e defina as variáveis locais a seguir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Postar revisões na Ferramenta de revisão

Em seguida, adicione o código a seguir para iterar pelas imagens de exemplo fornecidas, adicionar metadados e enviá-los para a Ferramenta de revisão em um único lote. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

O objeto retornado da chamada à API conterá valores de ID exclusivos para cada imagem carregada. O código a seguir analisa essas IDs e, em seguida, as usa para consultar o Content Moderator quanto ao status de cada imagem no lote.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Obter detalhes de revisão

O código a seguir faz com que o programa aguarde a entrada do usuário. Quando chega a esta etapa no runtime, você pode acessar a [Ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) por conta própria, verificar se a imagem de exemplo foi carregada e interagir com ela. Para saber mais sobre como interagir com uma revisão, confira o [Guia de instruções de revisões](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Quando terminar, você poderá pressionar qualquer tecla para dar continuidade ao programa e recuperar os resultados do processo de revisão.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Se você usou um ponto de extremidade de retorno de chamada neste cenário, ele deverá receber um evento neste formato:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a usar a biblioteca .NET do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [O que é o Azure Content Moderator?](../../overview.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
