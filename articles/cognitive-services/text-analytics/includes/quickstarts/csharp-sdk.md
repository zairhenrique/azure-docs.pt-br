---
title: 'Início Rápido: Biblioteca de clientes da Análise de Texto para C# | Microsoft Docs'
description: Introdução à biblioteca de clientes da Análise de Texto para C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987863"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)

[Documentação de referência da v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Código-fonte da biblioteca v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Pacote da v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Amostras da v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

[Documentação de referência da v2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Código-fonte da biblioteca v2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pacote da v2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Amostras da v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-net-core-application"></a>Criar um aplicativo .NET Core

Usando o IDE do Visual Studio, crie um aplicativo de console do .NET Core. Isso criará um projeto "Olá, Mundo" com um arquivo de origem C#: *program.cs*.

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes do NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Azure.AI.TextAnalytics`. Clique nele e então em **Instalar**. Você também pode usar o [Console do Gerenciador de Pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Na classe `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Substitua o método `Main` do aplicativo. Você definirá os métodos chamados aqui mais adiante.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Instale a biblioteca de cliente clicando com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes do NuGet**. No gerenciador de pacotes que é aberto, selecione **Procurar** e pesquise `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Clique nele e então em **Instalar**. Você também pode usar o [Console do Gerenciador de Pacotes](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Na classe `Program` do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Substitua o método `Main` do aplicativo. Você definirá os métodos chamados aqui mais adiante.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Modelo de objeto

O cliente de Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave e fornece funções para aceitar texto como cadeias de caracteres únicas ou como um lote. Você pode enviar texto para a API de forma síncrona ou assíncrona. O objeto de resposta conterá as informações de análise para cada documento enviado. 

Se estiver usando a versão `3.0-preview`, use uma instância opcional de `TextAnalyticsClientOptions` para inicializar o cliente com várias configurações padrão (por exemplo, idioma padrão ou dica de país). Autentique-se também usando um token do Azure Active Directory. 

## <a name="code-examples"></a>Exemplos de código

* [Análise de sentimento](#sentiment-analysis)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)

Verifique se o método principal indicado anteriormente cria um objeto de cliente com o ponto de extremidade e a chave. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie uma nova classe `ApiKeyServiceClientCredentials` para armazenar as credenciais e adicione-as às solicitações do cliente. Nela, crie uma substituição para `ProcessHttpRequestAsync()` que adicione sua chave ao cabeçalho `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Crie um método para criar uma instância do objeto [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) com o ponto de extremidade e um objeto `ApiKeyServiceClientCredentials` que contenha a chave.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Análise de sentimento

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)

Crie uma função chamada `SentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função `AnalyzeSentiment()`. O objeto `Response<AnalyzeSentimentResult>` retornado conterá o rótulo e a pontuação de sentimento de todo o documento de entrada, assim como uma análise de sentimento para cada frase se for bem-sucedido e um `Value.ErrorMessage` se não.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `SentimentAnalysisExample()` que use o cliente que você criou anteriormente e chame sua função [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet). O objeto [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) retornado conterá o sentimento `Score` se for bem-sucedido e um `errorMessage` se não. 

Uma pontuação mais próxima de 0 indica um sentimento negativo, enquanto uma pontuação mais próxima de 1 indica um sentimento positivo.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Detecção de idioma

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)


Crie uma função chamada `LanguageDetectionExample()` que use o cliente que você criou anteriormente e chame sua função `DetectLanguage()`. O objeto `Response<DetectLanguageResult>` retornado conterá o idioma detectado em `Value.PrimaryLanguage` se for bem-sucedido e um `Value.ErrorMessage` se não.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão; para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Saída

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `languageDetectionExample()` que use o cliente que você criou anteriormente e chame sua função [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O objeto [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) retornado conterá a lista de idiomas detectados em `DetectedLanguages` se for bem-sucedido e um `errorMessage` se não.  Imprima o primeiro idioma retornado.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro configurando esse valor como uma cadeia de caracteres vazia `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Saída

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>NER (reconhecimento de entidade nomeada)

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)


> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detecção de informações pessoais. 
> * A vinculação de entidade é uma solicitação separada do NER.

O código abaixo é usado para detectar informações pessoais usando o NER v3, que está em versão prévia pública.


Crie uma função chamada `EntityRecognitionExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeEntities()` e itere os resultados. O objeto `Response<RecognizeEntitiesResult>` retornado conterá a lista de entidades detectadas em `Value.NamedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não. Para cada entidade detectada, imprima seu Tipo e seu Subtipo, se houver.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>Como usar o NER para detectar informações pessoais

Crie uma função chamada `EntityPIIExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizePiiEntities()` e itere os resultados. Semelhante à função anterior, o objeto `Response<RecognizeEntitiesResult>` retornado conterá a lista de entidades detectadas no `Value.NamedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Vinculação de entidade

Crie uma função chamada `EntityLinkingExample()` que use o cliente que você criou anteriormente, chame sua função `RecognizeLinkedEntities()` e itere os resultados. O objeto `Response<RecognizeLinkedEntitiesResult>` retornado conterá a lista de entidades detectadas em `Value.LinkedEntities` se for bem-sucedido e um `Value.ErrorMessage` se não. Como as entidades vinculadas são unicamente identificadas, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

> [!NOTE]
> Na versão 2.1, a vinculação de entidade está incluída na resposta do NER.

Crie uma nova função chamada `RecognizeEntitiesExample()` que use o cliente que você criou anteriormente e chame sua função [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). Itere pelos resultados. O objeto [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) retornado conterá a lista de entidades detectadas em `Entities` se for bem-sucedido e um `errorMessage` se não. Para cada entidade detectada, imprima seu tipo, subtipo, nome da Wikipédia (se houver), bem como os locais do texto original.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Extração de frases-chave

#### <a name="version-30-previewtabversion-3"></a>[Versão prévia 3.0](#tab/version-3)

Crie uma função chamada `KeyPhraseExtractionExample()` que use o cliente que você criou anteriormente e chame sua função `ExtractKeyPhrases()`. O resultado conterá a lista de frases-chave detectadas em `Value.KeyPhrases` se for bem sucedido e um `Value.ErrorMessage` se não. Imprima todas as frases-chave detectadas.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21tabversion-2"></a>[Versão 2.1](#tab/version-2)

Crie uma nova função chamada `KeyPhraseExtractionExample()` que use o cliente que você criou anteriormente e chame sua função [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_). O resultado conterá a lista de frases-chave detectadas em `KeyPhrases` se for bem sucedido e um `errorMessage` se não. Imprima todas as frases-chave detectadas.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Saída

```console
Key phrases:
    cat
    veterinarian
```

---