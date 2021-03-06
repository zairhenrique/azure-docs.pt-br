---
title: Registrar Azure Functions extensões de associação
description: Saiba como registrar uma extensão de associação de Azure Functions com base em seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484716"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrar Azure Functions extensões de associação

No Azure Functions versão 2. x, as [associações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do tempo de execução do functions. Enquanto as funções do .NET acessam associações por meio de pacotes NuGet, os pacotes de extensão permitem que outras funções acessem todas as associações por meio de um parâmetro de configuração.

Considere os seguintes itens relacionados a extensões de associação:

- As extensões de associação não são explicitamente registradas no functions 1. x, exceto ao [criar uma biblioteca de classes usando C# o Visual Studio](#local-csharp).

- Os gatilhos HTTP e Timer têm suporte por padrão e não exigem uma extensão.

A tabela a seguir indica quando e como você registra as associações.

| Ambiente de desenvolvimento |Registro<br/> em funções de 1. x  |Registro<br/> em funções 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Idiomas Non-.NET ou desenvolvimento de ferramentas principais do Azure local|Automático|[Usar Azure Functions Core Tools e pacotes de extensão](#extension-bundles)|
|C#biblioteca de classes usando o Visual Studio|[Usar as ferramentas do NuGet](#vs)|[Usar as ferramentas do NuGet](#vs)|
|Biblioteca de classes C# usando o código do Visual Studio|N/D|[Use o .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>Pacotes de extensão para desenvolvimento local

Os pacotes de extensão são uma tecnologia de implantação que permite adicionar um conjunto compatível de extensões de associação de funções ao seu aplicativo de funções. Um conjunto predefinido de extensões é adicionado quando você cria seu aplicativo. Os pacotes de extensão definidos em um pacote são compatíveis entre si, o que ajuda a evitar conflitos entre pacotes. Você habilita pacotes de extensão no arquivo host. JSON do aplicativo.  

Você pode usar pacotes de extensão com a versão 2. x e versões posteriores do tempo de execução do functions. Ao desenvolver localmente, verifique se você está usando a versão mais recente do [Azure Functions Core Tools](functions-run-local.md#v2).

Use pacotes de extensão para o desenvolvimento local usando Azure Functions Core Tools, Visual Studio Code e quando você cria remotamente.

Se você não usar pacotes de extensão, deverá instalar o SDK do .NET Core 2. x em seu computador local antes de instalar qualquer extensão de associação. Os pacotes de extensão eliminam esse requisito para o desenvolvimento local. 

Para usar pacotes de extensão, atualize o arquivo *host. JSON* para incluir a seguinte entrada para `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>C\# biblioteca de classes com o Visual Studio

No **Visual Studio**, você pode instalar pacotes do console do Gerenciador de pacotes usando o comando [install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote usado para uma determinada associação é fornecido no artigo de referência para essa associação. Para obter um exemplo, consulte o [pacotes seção do artigo de referência de associação do barramento de serviço](functions-bindings-service-bus.md#functions-1x).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas são listadas nas páginas de pacote individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem ao Functions Runtime 1. x ou 2. x são especificadas no artigo de referência para a associação.

Se você usar `Install-Package` para fazer referência a uma associação, não precisará usar [pacotes de extensão](#extension-bundles). Essa abordagem é específica para bibliotecas de classes criadas no Visual Studio.

## <a name="vs-code"></a>C# biblioteca de classes com Visual Studio Code

No **Visual Studio Code**, instale pacotes para um C# projeto de biblioteca de classes do prompt de comando usando o comando [dotnet adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) na CLI do .NET Core. O exemplo a seguir demonstra como você adiciona uma associação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O .NET Core CLI só pode ser usado para o desenvolvimento do Azure Functions 2.x.

Substitua `<BINDING_TYPE_NAME>` pelo nome do pacote que contém a associação de que você precisa. Você pode encontrar o artigo de referência de associação desejada na [lista de associações com suporte](./functions-triggers-bindings.md#supported-bindings).

Substituir `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, como `3.0.0-beta5`. As versões válidas são listadas nas páginas de pacote individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem ao Functions Runtime 1. x ou 2. x são especificadas no artigo de referência para a associação.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Exemplo de gatilho e associação do Azure function](./functions-bindings-example.md)
