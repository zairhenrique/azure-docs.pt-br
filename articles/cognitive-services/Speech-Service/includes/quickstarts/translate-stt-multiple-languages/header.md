---
title: 'Início Rápido: Traduzir uma fala para vários idiomas – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 8c8cbc4e4f531d7a06ae3a33c33df9264c2cc6f2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981308"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir de maneira interativa uma fala de um idioma para uma fala em outro idioma. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 6 etapas para traduzir uma fala em texto em vários idiomas:
> [!div class="checklist"]
> * Crie um objeto ````SpeechTranslationConfig```` a partir de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o idioma de origem do reconhecimento de fala.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar vários idiomas de destino para tradução.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Com o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
