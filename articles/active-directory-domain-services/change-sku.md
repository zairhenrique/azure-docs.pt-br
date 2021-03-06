---
title: Alterar a SKU de um Azure AD Domain Services | Microsoft Docs
description: Saiba como a camada de SKU para um domínio Azure AD Domain Services gerenciado se os requisitos de negócios forem alterados
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 46557d802222190c0ed82f6243dd2a9b997ecaa5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960562"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Alterar a SKU de um domínio gerenciado Azure AD Domain Services existente

No Azure Active Directory Domain Services (AD DS do Azure), o desempenho e os recursos disponíveis são baseados no tipo de SKU. Essas diferenças de recursos incluem a frequência de backup ou o número máximo de relações de confiança de floresta de saída unidirecionais (atualmente em versão prévia). Você seleciona uma SKU ao criar o domínio gerenciado e pode alternar SKUs conforme suas necessidades de negócios mudam depois que o domínio gerenciado tiver sido implantado. As alterações nos requisitos de negócios podem incluir a necessidade de backups mais frequentes ou a criação de relações de confiança de floresta adicionais. Para obter mais informações sobre os limites e os preços das diferentes SKUs, consulte [conceitos do azure AD DS SKU][concepts-sku] e páginas de [preços do Azure AD DS][pricing] .

Este artigo mostra como alterar a SKU de um domínio gerenciado AD DS do Azure existente usando o portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitações de alteração de SKU

Há algumas limitações para a operação de alteração de SKU se você usar uma floresta de recursos (atualmente em visualização) e tiver criado relações de confiança de floresta de saída unidirecionais do Azure AD DS para um ambiente de AD DS local. As SKUs *Premium* e *Enterprise* definem um limite no número de relações de confiança que você pode criar. Não é possível alterar para uma SKU com um limite máximo inferior do que você configurou no momento.

Por exemplo, se você tiver criado duas relações de confiança de floresta no SKU *Premium* , não poderá mudar para o SKU *Standard* . O SKU *Standard* não dá suporte a relações de confiança de floresta. Ou, se você tiver criado sete relações de confiança no SKU *Premium* , não poderá mudar para o SKU *corporativo* . O SKU *empresarial* dá suporte a um máximo de cinco relações de confiança.

Para obter mais informações sobre esses limites, consulte [recursos e limites de SKU AD DS do Azure][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar a SKU de um domínio gerenciado AD DS do Azure usando o portal do Azure, conclua as seguintes etapas:

1. Na parte superior da portal do Azure, procure e selecione **Azure AD Domain Services**. Escolha o domínio gerenciado na lista, como *aadds.contoso.com*.
1. No menu no lado esquerdo da página AD DS do Azure, selecione **configurações > SKU**.

    ![Selecione a opção de menu SKU para seu domínio gerenciado AD DS do Azure no portal do Azure](media/change-sku/overview-change-sku.png)

1. No menu suspenso, selecione o SKU que você deseja para seu domínio gerenciado do Azure AD DS. Se você tiver uma floresta de recursos, não poderá selecionar SKU *padrão* , pois as relações de confiança de floresta só estarão disponíveis no SKU *corporativo* ou superior.

    Escolha o SKU desejado no menu suspenso e, em seguida, selecione **salvar**.

    ![Escolha a SKU necessária no menu suspenso na portal do Azure](media/change-sku/change-sku-selection.png)

Pode levar um minuto ou dois para alterar o tipo de SKU.

## <a name="next-steps"></a>Próximos passos

Se você tiver uma floresta de recursos e quiser criar relações de confiança adicionais após a alteração de SKU, consulte [criar uma relação de confiança de floresta de saída para um domínio local no Azure AD DS (versão prévia)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
