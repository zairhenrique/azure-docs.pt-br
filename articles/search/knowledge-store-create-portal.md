---
title: Criar um repositório de conhecimento (versão prévia) no portal do Azure
titleSuffix: Azure Cognitive Search
description: Use o assistente de Importação de dados para criar um repositório de conhecimento usado para persistir conteúdo enriquecido. Conecte-se a um repositório de conhecimento para obter análise de outros aplicativos ou enviar conteúdo enriquecido para processos downstream. Esse recurso está atualmente em visualização pública.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: b75b760704511627c74301ae3fff82c24a262e17
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904892"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Início Rápido: Criar um repositório de conhecimento da Pesquisa Cognitiva do Azure no portal do Azure

> [!IMPORTANT] 
> O repositório de conhecimento está atualmente em versão prévia pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

O repositório de conhecimento é um recurso da Pesquisa Cognitiva do Azure que persiste a saída de um pipeline de habilidades cognitivas para análise posterior ou outro processamento downstream. 

Um pipeline aceita texto não estruturado e imagens como conteúdo bruto, aplica IA por meio dos Serviços Cognitivos (como OCR, análise de imagem e processamento de linguagem natural), extrai informações e gera novas estruturas e informações. Um dos artefatos físicos criados por um pipeline é um [repositório de conhecimento](knowledge-store-concept-intro.md), que pode ser acessado por meio de ferramentas para analisar e explorar o conteúdo.

Neste início rápido, você combinará serviços e dados na nuvem do Azure para criar um repositório de conhecimento. Quando tudo estiver configurado, você executará o assistente **Importar dados** no portal para reunir tudo. O resultado final é um conteúdo de texto original mais conteúdo gerado por IA que você pode exibir no portal ([Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md)).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este início rápido usa a Pesquisa Cognitiva do Azure, o Armazenamento de Blobs do Azure e os [Serviços Cognitivos do Azure](https://azure.microsoft.com/services/cognitive-services/) para IA. 

Como a carga de trabalho é muito pequena, os Serviços Cognitivos são acionados nos bastidores para fornecer processamento gratuito para até 20 transações diárias quando invocados pela Pesquisa Cognitiva do Azure. Desde que use os dados de exemplo que fornecemos, você pode ignorar a criação ou anexação de um recurso dos Serviços Cognitivas.

1. [Baixe HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Esses são dados de resenhas de hotel salvos em um arquivo CSV (originário do Kaggle.com) que contêm 19 resenhas de clientes de um hotel. 

1. [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [localize uma conta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) na assinatura atual. Você usará o armazenamento do Azure para o conteúdo bruto a ser importado e para o repositório de conhecimento que é o resultado final.

   Escolha o tipo de conta **StorageV2 (V2 de uso geral)** .

1. Abra as páginas dos serviços de Blob e crie um contêiner chamado *hotel-reviews*.

1. Clique em **Carregar**.

    ![Carregar os dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as resenhas de hotéis")

1. Carregue o arquivo **HotelReviews-Free.csv** que você baixou na primeira etapa.

    ![Criar o contêiner de Blob do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de Blob do Azure")

1. Você quase terminou de trabalhar com esse recurso, mas antes de sair dessas páginas, use um link no painel de navegação esquerdo para abrir a página **Chaves de Acesso**. Obtenha uma cadeia de conexão para recuperar dados do armazenamento de Blobs. A cadeia de conexão é semelhante ao seguinte exemplo: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ainda no portal, alterne para o Azure Cognitive Search. [Criar um novo serviço](search-create-service-portal.md) ou [encontrar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). É possível usar um serviço gratuito para este início rápido.

Agora, você está pronto prosseguir para assistente de Importação de dados.

## <a name="run-the-import-data-wizard"></a>Executar o assistente de Importação de dados

Na página de Visão geral do serviço de pesquisa, clique em **Importar dados** na barra de comandos para criar um repositório de conhecimento em quatro etapas.

  ![Comando Importar de dados](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

1. Em **Conectar-se aos seus dados**, escolha **Armazenamento de Blobs do Azure** e selecione a conta e o contêiner criados. 
1. Para o **Nome**, insira `hotel-reviews-ds`.
1. Para **Modo de análise**, selecione **Texto delimitado** e, em seguido, marque a caixa de seleção **A Primeira Linha Contém Cabeçalho**. Verifique se o **Caractere delimitador** é uma vírgula (,).
1. Em **Cadeia de Conexão**, cole a cadeia de conexão que você copiou da página **Chaves de Acesso** no Armazenamento do Azure.
1. Em **Contêineres**, insira o nome do contêiner de blob que contém os dados.

    Esta página deve ser semelhante à seguinte captura de tela.

    ![Criar um objeto de fonte de dados](media/knowledge-store-create-portal/hotel-reviews-ds.png "Criar um objeto de fonte de dados")

1. Continue para a próxima página.

### <a name="step-2-add-cognitive-skills"></a>Etapa 2: Adicionar habilidades cognitivas

Nesta etapa do assistente, você criará um conjunto de habilidades com aprimoramentos de habilidades cognitivas. Os dados de origem consistem em análises de clientes em vários idiomas. As habilidades relevantes para esse conjunto de dados incluem extração de frases-chave, detecção de opiniões e tradução de texto. Em uma etapa posterior, esses aprimoramentos serão “projetados” em um repositório de conhecimento como tabelas do Azure.

1. Expanda **Anexar Serviços Cognitivos**. **Gratuito (Aprimoramentos limitados)** é selecionado por padrão. É possível usar esse recurso porque o número de registros em HotelReviews-Free.csv é 19 e esse recurso gratuito permite até 20 transações por dia.
1. Expanda **Adicionar aprimoramentos**.
1. Em **Nome do conjunto de habilidades**, insira `hotel-reviews-ss`.
1. Em **Campo de dados de origem**, selecione **reviews_text**.
1. Para **Nível de granularidade do aprimoramento**, selecione **Páginas (5 mil partes de caracteres)**
1. Selecione estas habilidades cognitivas:
    + **Extraia frases-chave**
    + **Traduzir o texto**
    + **Detectar sentimento**

      ![Criar um conjunto de habilidades](media/knowledge-store-create-portal/hotel-reviews-ss.png "Criar um conjunto de habilidades")

1. Expanda **Salvar aprimoramentos no repositório de conhecimento**.
1. Selecione estas **projeções de tabela do Azure**:
    + **Documentos**
    + **Páginas**
    + **Frases-chave**
1. Insira a **Cadeia de Conexão da conta de armazenamento** salva em uma etapa anterior.

    ![Configurar um repositório de conhecimento](media/knowledge-store-create-portal/hotel-reviews-ks.png "Configurar um repositório de conhecimento")

1. Opcionalmente, baixe um modelo do Power BI. Quando você acessa o modelo por meio do assistente, o arquivo local .pbit é adaptado para refletir a forma de seus dados.

1. Continue para a próxima página.

### <a name="step-3-configure-the-index"></a>Etapa 3: Configurar o índice

Nesta etapa do assistente, você configurá um índice para consultas de pesquisa de texto completo opcionais. O assistente criará um exemplo de sua fonte de dados para inferir campos e tipos de dados. Só é necessário selecionar os atributos do seu comportamento desejado. Por exemplo, o atributo **Recuperável** permitirá que o serviço de pesquisa retorne um valor de campo enquanto o **Pesquisável** habilitará a pesquisa de texto completo no campo.

1. Em **Nome do índice**, insira `hotel-reviews-idx`.
1. Para atributos, aceite as seleções padrão: **Recuperável** e **Pesquisável** para os novos campos que o pipeline está criando.

    O índice deve ser semelhante à imagem a seguir. Como a lista é longa, nem todos os campos estão visíveis na imagem.

    ![Configurar um índice](media/knowledge-store-create-portal/hotel-reviews-idx.png "Configurar um índice")

1. Continue para a próxima página.

### <a name="step-4-configure-the-indexer"></a>Etapa 4: Configurar o indexador

Nesta etapa do assistente, você configurará um indexador que reunirá a fonte de dados, o conjunto de habilidades e o índice definidos nas etapas anteriores do assistente.

1. Para **Nome**, insira `hotel-reviews-idxr`.
1. Para **Agenda**, mantenha o padrão **Uma vez**.
1. Clique em **Enviar** para executar o indexador. A extração de dados, a indexação e a aplicação de habilidades cognitivas acontecem nesta etapa.

## <a name="monitor-status"></a>Monitorar status

A indexação de habilidades cognitivas leva mais tempo para ser concluída em comparação à indexação típica baseada em texto. O assistente deverá abrir a lista Indexador na página de visão geral, de modo que você possa acompanhar o progresso. Para a autonavegação, acesse a página Visão Geral e clique em **Indexadores**.

No portal do Azure, também é possível monitorar o log de atividades de Notificações para obter um link de status de **notificação da Pesquisa Cognitiva do Azure** clicável. A execução pode levar vários minutos para ser concluída.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprimorou seus dados usando os serviços cognitivos e projetou os resultados em um repositório de conhecimento, é possível usar o Gerenciador de Armazenamento ou o Power BI para explorar seu conjunto de dados aprimorado.

Você pode exibir o conteúdo no Gerenciador de Armazenamento ou ir um passo adiante com o Power BI para obter informações sobre a visualização.

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)
> [Conectar-se ao Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Se desejar repetir esse exercício ou experimentar um passo a passo de aprimoramento de IA diferente, exclua o indexador *hotel-reviews-idx*. A exclusão do indexador redefine o contador de transações diárias gratuito para zero para o processamento dos Serviços Cognitivos.
