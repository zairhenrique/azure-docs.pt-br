---
title: Políticas de Chave de Conteúdo nos Serviços de Mídia – Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais são as Políticas de Chave de Conteúdo e como elas são usadas pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969897"
---
# <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

Para especificar as opções de criptografia em seu fluxo, você precisa criar uma [política de streaming](streaming-policy-concept.md) e associá-la ao seu [localizador de streaming](streaming-locators-concept.md). Você cria a [política de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) para configurar como a chave de conteúdo (que fornece acesso seguro aos seus [ativos](assets-concept.md)) é entregue aos clientes finais. Você precisa definir os requisitos (restrições) na política de chave de conteúdo que deve ser atendida para que as chaves com a configuração especificada sejam entregues aos clientes. A política de chave de conteúdo não é necessária para streaming ou download claro. 

Normalmente, você associa a política de chave de conteúdo ao seu [localizador de streaming](streaming-locators-concept.md). Como alternativa, você pode especificar a política de chave de conteúdo dentro de uma [política de streaming](streaming-policy-concept.md) (ao criar uma política de streaming personalizada para cenários avançados). 

## <a name="best-practices-and-considerations"></a>Práticas recomendadas e considerações

> [!IMPORTANT]
> Examine as recomendações a seguir.

* Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-las para seus localizadores de streaming sempre que as mesmas opções forem necessárias. Para saber mais, consulte [Cotas e limitações](limits-quotas-constraints.md).
* As políticas de chave de conteúdo são atualizáveis. Pode levar até 15 minutos para que os caches de entrega de chaves sejam atualizados e selecione a política atualizada. 

   Ao atualizar a política, você está substituindo o cache da CDN existente, o que pode causar problemas de reprodução para clientes que estão usando conteúdo armazenado em cache.  
* Recomendamos que você não crie uma nova política de chave de conteúdo para cada ativo. Os principais benefícios de compartilhar a mesma política de chave de conteúdo entre os ativos que precisam das mesmas opções de política são:
   
   * É mais fácil gerenciar um pequeno número de políticas.
   * Se você precisar fazer atualizações na política de chave de conteúdo, as alterações entrarão em vigor em todas as novas solicitações de licença quase que imediatamente.
* Se você precisar criar uma nova política, precisará criar um novo localizador de streaming para o ativo.
* É recomendável permitir que os serviços de mídia geram automaticamente a chave de conteúdo. 

   Normalmente, você usaria uma chave de vida longa e verifica a existência da política de chave de conteúdo com [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Para ter acesso à chave, você precisa chamar um método de ação separado para obter segredos ou as credenciais. Confira o exemplo a seguir.

## <a name="example"></a>Exemplo

Para obter a chave, use `GetPolicyPropertiesWithSecretsAsync`, conforme mostrado no exemplo [obter uma chave de assinatura a partir da política existente](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="additional-notes"></a>Observações adicionais

* As propriedades das políticas de chave de conteúdo do tipo `Datetime` estão sempre no formato UTC.
* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximos passos

* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
