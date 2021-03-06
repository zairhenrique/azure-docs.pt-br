---
title: Proteger APIs usando a autenticação de certificado do cliente no gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como proteger o acesso às APIs usando certificados do cliente
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713141"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Como proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API

O Gerenciamento de API fornece a capacidade de proteger o acesso às APIs (isto é, cliente para Gerenciamento de API) usando certificados do cliente. Você pode validar o certificado de entrada e verificar as propriedades do certificado em relação aos valores desejados usando expressões de política.

Para obter informações sobre como proteger o acesso ao serviço de back-end de uma API usando certificados de cliente (ou seja, gerenciamento de API para back-end), consulte [como proteger serviços de back-end usando autenticação de certificado de cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Para receber e verificar certificados de cliente por HTTP/2 nas camadas Developer, Basic, Standard ou Premium, você deve ativar a configuração "negociar certificado de cliente" na folha "domínios personalizados", conforme mostrado abaixo.

![Negociar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Para receber e verificar os certificados do cliente na camada de consumo, você deve ativar a configuração "solicitar certificado do cliente" na folha "domínios personalizados", conforme mostrado abaixo.

![Solicitar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Verificando o emissor e a entidade

As políticas abaixo podem ser configuradas para verificar o emissor e a entidade de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desabilitar a verificação da lista de certificados revogados, use `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` funcionar.

## <a name="checking-the-thumbprint"></a>Verificando a impressão digital

Veja abaixo que as políticas podem ser configuradas para verificar a impressão digital de um certificado do cliente:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Para desabilitar a verificação da lista de certificados revogados, use `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` funcionar.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Verificação de uma impressão digital em relação a certificados carregados no Gerenciamento de API

O exemplo a seguir mostra como verificar a impressão digital de um certificado do cliente em relação a certificados carregados no Gerenciamento de API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Para desabilitar a verificação da lista de certificados revogados, use `context.Request.Certificate.VerifyNoRevocation()` em vez de `context.Request.Certificate.Verify()`.
> Se o certificado do cliente for autoassinado, os certificados de autoridade de certificação raiz (ou intermediário) deverão ser [carregados](api-management-howto-ca-certificates.md) no gerenciamento de API para `context.Request.Certificate.Verify()` e `context.Request.Certificate.VerifyNoRevocation()` funcionar.

> [!TIP]
> O problema de deadlock de certificado de cliente descrito neste [artigo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) pode se manifestar de várias maneiras, por exemplo, as solicitações congeladas, resultam em `403 Forbidden` código de status após o tempo limite `context.Request.Certificate` é `null`. Esse problema geralmente afeta `POST` e `PUT` solicitações com tamanho de conteúdo de aproximadamente 60KB ou maior.
> Para evitar que esse problema ocorra, ative a configuração "negociar certificado de cliente" para os nomes de host desejados na folha "domínios personalizados", conforme mostrado abaixo. Este recurso não está disponível na camada de consumo.

![Negociar certificado do cliente](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

-   [Como garantir serviços de back-end usando autenticação de certificado do cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Como carregar certificados](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
