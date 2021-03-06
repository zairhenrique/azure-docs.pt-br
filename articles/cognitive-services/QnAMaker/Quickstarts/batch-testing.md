---
title: 'Início Rápido: Testar a base de dados de conhecimento com o perguntas em lotes'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: e16166c741b99c1af5b36f2c7ccd25b01f7544ba
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109002"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Início Rápido: Testar a base de dados de conhecimento com perguntas em lotes e respostas esperadas

Use a ferramenta de teste em lotes do QnA Maker para testar as bases de dados de conhecimento em seu recurso do QnA Maker para respostas esperadas, pontuações de confiança e prompts de vários turnos.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Crie um serviço do QnA Maker](create-publish-knowledge-base.md) ou use um serviço existente, que usa o idioma inglês.
* Baixar o [arquivo `.docx` de exemplo de vários turnos](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Baixe a [ferramenta de teste em lotes](https://aka.ms/qnamakerbatchtestingtool) e extraia o arquivo executável do arquivo `.zip`.

## <a name="sign-into-qna-maker-portal"></a>Entrar no portal do QnA Maker

[Entre](https://www.qnamaker.ai/) no portal do QnA Maker.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Criar uma base de dados de conhecimento por meio do arquivo .docx de exemplo de vários turnos

1. Selecione **Criar uma base de dados de conhecimento** na barra de ferramentas.
1. Ignore a **Etapa 1** porque você já deve ter um recurso do QnA Maker, passando para a **Etapa 2** para selecionar suas informações de recurso existentes:
    * ID do Azure Active Directory
    * Nome da Assinatura do Azure
    * Nome do Serviço QnA do Azure
    * Idioma – o idioma inglês
1. Insira o nome `Multi-turn batch test quickstart` como o nome da sua base de dados de conhecimento.

1. Na **Etapa 4**, defina as configurações com a seguinte tabela:

    |Configuração|Valor|
    |--|--|
    |**Habilitar a extração de vários turnos de URLs, arquivos .pdf ou .docx.**|Verificado|
    |**Texto de resposta padrão**| `Batch test - default answer not found.`|
    |**+ Adicionar Arquivo**|Selecione a listagem de arquivos `.docx` baixada nos pré-requisitos.|
    |**Bate-papo**|Selecione **Professional**|

1. Na **Etapa 5**, selecione **Criar sua KB**.

    Quando o processo de criação for concluído, o portal exibirá a base de dados de conhecimento editável.

## <a name="save-train-and-publish-knowledge-base"></a>Salvar, treinar e publicar a base de dados de conhecimento

1. Selecione **Salvar e Treinar** na barra de ferramentas para salvar a base de dados de conhecimento.
1. Selecione **Publicar** na barra de ferramentas e, em seguida, selecione **Publicar** novamente para publicar a base de dados de conhecimento. A publicação disponibiliza a base de dados de conhecimento para consultas de um ponto de extremidade de URL pública. Quando a publicação for concluída, salve as informações de chave do ponto de extremidade e URL do host mostradas na página **Publicar**.

    |Dados necessários| Exemplo|
    |--|--|
    |Host publicado|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Chave publicada|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (cadeia de 32 caracteres mostrada após `Endpoint`)|
    |ID do Aplicativo|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (cadeia de 36 caracteres mostrada como parte de `POST`) |

## <a name="create-batch-test-file-with-question-ids"></a>Criar arquivo de teste de lote com IDs de pergunta

Para usar a ferramenta de teste de lote, crie um arquivo chamado `batch-test-data-1.tsv` com um editor de texto. O arquivo precisa ter as colunas a seguir separadas por uma tabulação.

|Campos do arquivo de entrada TSV|Observações|Exemplo|
|--|--|--|
|ID da base de dados de conhecimento|Sua ID da base de dados de conhecimento foi encontrada na página Publicar. Teste várias bases de dados de conhecimento no mesmo serviço de uma só vez em um arquivo usando IDs de base de dados de conhecimento diferentes em um arquivo.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (cadeia de 36 caracteres mostrada como parte de `POST`) |
|Pergunta|O texto da pergunta que um usuário digitaria. Tamanho máximo de 1.000 caracteres.|`How do I sign out?`|
|Marcas de metadados|opcional|`topic:power` usa o formato de _chave:valor_|
|Parâmetro superior|opcional|`25`|
|ID de resposta esperada|opcional|`13`|

Para essa base de dados de conhecimento, adicione três linhas ao arquivo, pertencentes apenas às duas colunas necessárias. A primeira coluna é sua ID da base de dados de conhecimento e a segunda coluna deve ser a seguinte lista de perguntas:

|Coluna 2 – perguntas|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Essas perguntas são redigidas exatamente do mesmo modo que aquelas na base de dados de conhecimento e devem retornar uma pontuação de confiança igual a 100.

Em seguida, adicione algumas outras perguntas semelhantes a essas, mas não idênticas, em mais três linhas, usando a mesma ID da base de dados de conhecimento:

|Coluna 2 – perguntas|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Verifique se cada coluna está separada apenas por um delimitador de tabulação. Espaços à esquerda ou à direita são adicionados aos dados da coluna e fazem com que o programa gere exceções quando o tipo ou o tamanho está incorreto.

O arquivo de teste de lote, quando aberto no Excel, é semelhante à imagem a seguir. A ID da base de dados de conhecimento foi substituída por `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por questões de segurança. Para seu teste de lote, verifique se a coluna exibe sua ID da base de dados de conhecimento.

> [!div class="mx-imgBorder"]
> ![Primeira versão de entrada do arquivo .tsv do teste de lote](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testar o arquivo em lotes

Execute o programa de teste de lote usando o formato de CLI a seguir na linha de comando.

Substitua `YOUR-RESOURCE-NAME` e `ENDPOINT-KEY` pelos seus valores para nome do serviço e chave do ponto de extremidade. Esses valores são encontrados na página **Configurações** no portal do QnA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
O teste é concluído e gera o arquivo `out.tsv`:

> [!div class="mx-imgBorder"]
> ![Primeira versão de saída do arquivo .tsv do teste de lote](../media/batch-test/batch-test-1-output.png)

A ID da base de dados de conhecimento foi substituída por `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por questões de segurança. Para seu teste de lote, a coluna exibe sua ID da base de dados de conhecimento.

A saída de teste de Pontuação de confiança, na quarta coluna, mostra que as três primeiras perguntas retornaram uma pontuação igual a 100 como esperado, pois cada pergunta é exatamente igual à que aparece na base de dados de conhecimento. As três últimas perguntas, tendo sido redigidas de maneira diferente, não retornam 100 como a pontuação de confiança. Para aumentar a pontuação para o teste e seus usuários, você precisa adicionar mais perguntas alternativas à base de dados de conhecimento.

## <a name="testing-with-the-optional-fields"></a>Testar com os campos opcionais

Depois de entender o formato e o processo, você pode gerar, de fonte de dados, como de logs de chats, um arquivo de teste para executar em sua base de dados de conhecimento.

Como a fonte de dados e o processo são automatizados, o arquivo de teste pode ser executado muitas vezes com configurações diferentes para determinar os valores corretos.

Por exemplo, se você tiver um log de chat e desejar determinar qual texto de log de chat aplica-se a quais campos de metadados, crie um arquivo de teste e defina os campos de metadados para cada linha. Execute o teste e examine as linhas que correspondem aos metadados. Em geral, as correspondências devem ser positivas, mas você deve examinar os resultados para ver se há falsos positivos. Um falso positivo é uma linha que corresponde aos metadados mas, com base no texto, não deveria corresponder.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Usar campos opcionais no arquivo de teste de lote de entrada

Use o gráfico a seguir para entender como localizar os valores de campo para dados opcionais.

|Número da coluna|Coluna opcional|Local dos dados|
|--|--|--|
|3|metadata|Exportar a base de dados de conhecimento existente para os pares _chave:valor_ existentes.|
|4|top|Um valor padrão de `25` é recomendado.|
|5|ID do conjunto de perguntas e respostas|Exporte a base de dados de conhecimento existente para valores de ID. Observe também que as IDs foram retornadas no arquivo de saída.|

## <a name="add-metadata-to-the-knowledge-base"></a>Adicionar metadados à base de dados de conhecimento

1. No portal do QnA, na página **Editar**, adicione metadados de `topic:power` às seguintes perguntas:

    |Perguntas|
    |--|
    |Carregar seu Surface Pro 4|
    |Verificar o nível da bateria|

    Dois conjuntos do QnA têm os metadados definidos.

    > [!TIP]
    > Para ver os metadados e as IDs do QnA de cada conjunto, exporte a base de dados de conhecimento. Selecione a página **Configurações** e, em seguida, selecione **Exportar** como um arquivo `.xls`. Localize esse arquivo baixado e abra-o com o Excel, revisando os metadados e a ID.

1. Selecione **Salvar e treinar** e, em seguida, selecione a página **Publicar**. Em seguida, selecione o botão **Publicar**. Essas ações tornam a alteração disponível para o teste de lote. Baixe a base de dados de conhecimento na página **Configurações**.

    O arquivo baixado tem o formato correto para os metadados e a ID correta de conjunto de perguntas e respostas. Usar esses campos na próxima seção

    > [!div class="mx-imgBorder"]
    > ![Base de dados de conhecimento exportada com metadados](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Criar um segundo teste de lote

Há dois cenários principais para testes de lote:
* **Processar arquivos de log de chat** – determinar a resposta principal para uma pergunta anteriormente não vista – a situação mais comum é quando você precisa processar o arquivo de log de consultas, como as perguntas de usuário de um chatbot. Crie um teste de arquivo em lotes, com apenas as colunas necessárias. O teste retorna a primeira resposta para cada pergunta. Isso não significa que a primeira resposta é a resposta correta. Depois de concluir este teste, vá para o teste de validação.
* **Teste de validação** – valide a resposta esperada. Esse teste exige que todas as perguntas e respostas esperadas correspondentes no teste de lote tenham sido validadas. Isso pode exigir algum processamento manual.

O procedimento a seguir pressupõe que o cenário é processar os logs de chat com

1. Crie um arquivo de teste de lote para incluir dados opcionais, `batch-test-data-2.tsv`. Adicione as seis linhas do arquivo de entrada de teste de lote original e, em seguida, adicione a ID de conjunto de metadados, de conjunto superior e de conjunto de QnA para cada linha.

    Para simular o processo automatizado de verificar novo texto de logs de chat na base de dados de conhecimento, defina os metadados para cada coluna com o mesmo valor: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![Segunda versão de entrada do arquivo .tsv do teste de lote](../media/batch-test/batch-test-2-input.png)

1. Execute o teste novamente, alterando os nomes de arquivo de entrada e saída para indicar que este se trata do segundo teste.

    > [!div class="mx-imgBorder"]
    > ![Segunda versão de saída do arquivo .tsv do teste de lote](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Resultados de teste e um sistema de teste automatizado

Esse arquivo de saída de teste pode ser analisado como parte de um pipeline de teste contínuo automatizado.

Esta saída de teste específica deve ser lida como: cada linha foi filtrada com metadados e, já que não houve correspondência entre cada linha e os metadados na base de dados de conhecimento, a resposta padrão para as linhas não correspondentes retorna ("nenhuma correspondência adequada encontrada na base de dados de conhecimento"). Das linhas que corresponderam, a ID do QnA e a pontuação foram retornadas.

Todas as linhas retornaram o rótulo de incorreta porque nenhuma linha correspondeu à ID de resposta esperada.

Com esses resultados, deve ser possível perceber que você pode fazer um log de chat e usar o texto como a consulta de cada linha. Sem saber nada sobre os dados, os resultados permitem tirar muitas conclusões sobre os dados, que você pode usar posteriormente:

* metadados
* ID do QnA
* score

A filtragem com metadados foi uma boa ideia para o teste? Sim e não. O sistema de teste deveria criar arquivos de teste para cada par de metadados, bem como um teste sem pares de metadados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não for continuar testando a base de dados de conhecimento, exclua a ferramenta de arquivo em lotes e os arquivos de teste.

Se você não quiser continuar a usar essa base de dados de conhecimento, exclua-a seguindo estas etapas:

1. No portal do QnA Maker, selecione **Minhas bases de dados de conhecimento** no menu superior.
1. Na lista de bases de dados de conhecimento, selecione o ícone **Excluir** na linha da base de dados de conhecimento deste início rápido.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
