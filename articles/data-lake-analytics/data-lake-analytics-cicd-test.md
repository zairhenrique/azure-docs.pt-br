---
title: Como testar seu código do Azure Data Lake Analytics
description: Saiba como adicionar casos de teste para código C# estendido e U-SQL para Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913953"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testar o código do Azure Data Lake Analytics

Azure Data Lake fornece a linguagem [U-SQL](data-lake-analytics-u-sql-get-started.md) . O U-SQL combina SQL declarativo com imperativos C# para processar dados em qualquer escala. Neste documento, você aprenderá a criar casos de teste para o U-SQL e C# o código do Udo (operador estendido definido pelo usuário).

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O script U-SQL é compilado e otimizado para que o código executável seja executado no Azure ou em seu computador local. O processo de compilação e otimização trata o script U-SQL inteiro como um todo. Você não pode fazer um teste de unidade tradicional para cada instrução. No entanto, usando o SDK de teste do U-SQL e o SDK de execução local, será possível fazer testes em nível de script.

### <a name="create-test-cases-for-u-sql-script"></a>Criar casos de teste para o script U-SQL

As Ferramentas do Azure Data Lake para Visual Studio permitem que você crie casos de teste de script U-SQL.

1. Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e selecione **Criar Teste de Unidade**.

1. Crie um novo projeto de teste ou insira o caso de teste em um projeto de teste existente.

   ![Ferramentas do Data Lake para Visual Studio - criar uma configuração de projeto de teste U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Gerenciar a fonte de dados de teste

Ao testar scripts U-SQL, serão necessários arquivos de entrada de teste. Para gerenciar os dados de teste, em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto U-SQL e selecione **Propriedades**. Você pode inserir uma fonte na **fonte de dados de teste**.

![Ferramentas do Data Lake para Visual Studio - configurar fonte de dados de teste do projeto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Quando você chama a `Initialize()` interface no SDK de teste do U-SQL, uma pasta raiz de dados local temporário é criada no diretório de trabalho do projeto de teste. Todos os arquivos e pastas na pasta fonte de dados de teste são copiados para a pasta raiz de dados locais temporária antes de executar os casos de teste de script U-SQL. É possível adicionar mais pastas de fonte de dados de teste, separando o caminho da pasta de dados de teste com um ponto e vírgula.

### <a name="manage-the-database-environment-for-testing"></a>Gerenciar o ambiente de banco de dados para testes

Se seus scripts U-SQL usarem ou consultarem com objetos de banco de dados U-SQL, você precisará inicializar o ambiente de banco de dados antes de executar casos de teste do U-SQL. Essa abordagem pode ser necessária ao chamar procedimentos armazenados. A interface `Initialize()` no SDK de teste de U-SQL ajuda a implantar todos os bancos de dados referenciados pelo projeto U-SQL à pasta raiz de dados locais temporários no diretório de trabalho do projeto de teste.

Para obter mais informações sobre como gerenciar referências de projeto de banco de dados U-SQL para um projeto U-SQL, consulte [fazer referência a um projeto de banco de dados u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificar resultados do teste

A interface `Run()` retorna um resultado de execução de trabalho. *0* significa êxito e *1* significa falha. Você também pode usar funções assert do C# para verificar as saídas.

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

Um projeto de teste de script U-SQL é compilado sobre uma estrutura de teste de unidade do C#. Depois de criar o projeto, selecione **testar** > o**Gerenciador de testes**do**Windows** > . Você pode executar casos de teste no **Gerenciador de testes**. Como alternativa, clique com o botão direito do mouse no arquivo. cs em seu teste de unidade e selecione **executar testes**.

## <a name="test-c-udos"></a>Testar UDOs de C#

### <a name="create-test-cases-for-c-udos"></a>Criar casos de teste para UDOs de C#

Você pode usar uma C# estrutura de teste de unidade para C# testar seus operadores definidos pelo usuário (UDOs). Para testar UDOs, será necessário preparar os objetos **IRowset** como entradas.

Há duas maneiras de criar um objeto **IRowset** :

- Carregar dados de um arquivo para criar **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Use dados de uma coleção de dados para criar **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verificar resultados do teste

Após chamar funções UDO, será possível verificar os resultados por meio da verificação de valor do Conjunto de Linhas e esquema, usando funções assert de C#. Você pode adicionar um **projeto de teste C# de unidade Udo do U-SQL** à sua solução. Para fazer isso, selecione **arquivo > novo projeto de >** no Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

Depois de criar o projeto, selecione **testar** > o**Gerenciador de testes**do**Windows** > . Você pode executar casos de teste no **Gerenciador de testes**. Como alternativa, clique com o botão direito do mouse no arquivo. cs em seu teste de unidade e selecione **executar testes**.

## Executar casos de teste no Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Ambos os **projetos de teste do script U-SQL** e **projetos de teste do UDO de C#** herdam os projetos de teste de unidade de C#. A [tarefa de teste do Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) no Azure pipelines pode executar esses casos de teste.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Executar casos de teste do U-SQL no Azure Pipelines

Para um teste do U-SQL, verifique se você `CPPSDK` carregou em seu computador de compilação e, em `CPPSDK` seguida, `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`passe o caminho para.

#### <a name="what-is-cppsdk"></a>O que é o CPPSDK?

O CPPSDK é um pacote que inclui o Microsoft Visual C++ 14 e SDK do Windows 10.0.10240.0. Esse pacote inclui o ambiente necessário para o tempo de execução do U-SQL. É possível obter esse pacote na pasta de instalação das Ferramentas do Azure Data Lake para Visual Studio:

- No Visual Studio 2015, ela está em `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- No Visual Studio 2017, ela está em `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Para o Visual Studio 2019, ele está em`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Preparar o CPPSDK no agente de Build Azure Pipelines

A maneira mais comum de preparar a dependência CPPSDK no Azure Pipelines é a seguinte:

1. Zip a pasta que inclui as bibliotecas CPPSDK.

1. Verifique no arquivo .zip o sistema de controle do código-fonte. O arquivo. zip garante que você faça check-in de todas as bibliotecas na pasta CPPSDK para que os arquivos não `.gitignore` sejam ignorados devido a um arquivo.

1. Descompacte o arquivo .zip no pipeline de build.

1. Aponte `USqlScriptTestRunner` para essa pasta descompactada no computador de compilação.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Executar C# casos de teste do UDO no Azure pipelines

Para um C# teste do Udo, certifique-se de referenciar os seguintes assemblies, que são necessários para o UDOs.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Se você fizer referência a eles por meio [do pacote do Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), não se esqueça de adicionar uma tarefa de restauração do NuGet em seu pipeline de build.

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
- [Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
