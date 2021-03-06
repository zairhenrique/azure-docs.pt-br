---
title: Definir um perfil técnico autodeclarado em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico autodeclarado em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f2a86f72f16a23b0133601cfe41b9e636d8866d
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425588"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico autodeclarado em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Todas as interações em Azure Active Directory B2C (Azure AD B2C) em que o usuário deve fornecer entrada são perfis técnicos autodeclarados. Por exemplo, uma página de inscrição, entrada ou redefinição de senha.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C, para autodeclaração: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico autodeclarado para email de inscrição:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Declarações de entrada

Em um perfil técnico autodeclarado, você pode usar os elementos **InputClaims** e **InputClaimsTransformations** para pré-popular o valor das declarações que aparecem na página autodeclarada (Exibir declarações). Por exemplo, na política de edição de perfil, o percurso do usuário primeiro lê o perfil do usuário do serviço de diretório do Azure AD B2C. Em seguida, o perfil técnico autodeclarado define as declarações de entrada com os dados do usuário armazenados no perfil do usuário. Essas declarações são coletadas do perfil do usuário e, em seguida, apresentadas a ele, que poderá editar os dados existentes.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Exibir declarações

O recurso Exibir declarações está em **Visualização**no momento.

O elemento **DisplayClaims** contém uma lista de declarações a serem apresentadas na tela para coletar dados do usuário. Para preencher previamente os valores de declarações de exibição, use as declarações de entrada que foram descritas anteriormente. O elemento também pode conter um valor padrão.

A ordem das declarações em **DisplayClaims** especifica a ordem na qual Azure ad B2C renderiza as declarações na tela. Para forçar o usuário a fornecer um valor para uma declaração específica, defina o atributo **Required** do elemento **DisplayClaim** como `true`.

O elemento **ClaimType** na coleção **DisplayClaims** precisa definir o elemento **userinputtype** como qualquer tipo de entrada de usuário com suporte pelo Azure ad B2C. Por exemplo, `TextBox` ou `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Adicionar uma referência a um DisplayControl

Na coleção exibir declarações, você pode incluir uma referência a um [DisplayControl](display-controls.md) que você criou. Um controle de exibição é um elemento de interface do usuário que tem funcionalidade especial e interage com o serviço de back-end Azure AD B2C. Ele permite que o usuário execute ações na página que invocam um perfil técnico de validação no back-end. Por exemplo, verificar um endereço de email, número de telefone ou número de fidelidade do cliente.

O exemplo a seguir `TechnicalProfile` ilustra o uso de declarações de exibição com controles de exibição.

* A primeira declaração de exibição faz uma referência para o `emailVerificationControl` controle de exibição que coleta e verifica o endereço de email.
* A quinta declaração de exibição faz uma referência para o `phoneVerificationControl` controle de exibição que coleta e verifica um número de telefone.
* As outras declarações de exibição são ClaimTypes a serem coletadas do usuário.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Como mencionado, uma declaração de exibição com uma referência a um controle de exibição pode executar sua própria validação, por exemplo, verificar o endereço de email. Além disso, a página autodeclarada dá suporte ao uso de um perfil técnico de validação para validar a página inteira, incluindo qualquer entrada do usuário (tipos de declaração ou controles de exibição), antes de passar para a próxima etapa de orquestração.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combine o uso de declarações de exibição e declarações de saída com cuidado

Se você especificar um ou mais elementos **DisplayClaim** em um perfil técnico autodeclarado, deverá usar um DisplayClaim para *cada* declaração que você deseja exibir na tela e coletar do usuário. Nenhuma declaração de saída é exibida por um perfil técnico autodeclarado que contém pelo menos uma declaração de exibição.

Considere o exemplo a seguir no qual uma declaração de `age` é definida como uma declaração de **saída** em uma política de base. Antes de adicionar qualquer declaração de exibição ao perfil técnico autodeclarado, a declaração de `age` é exibida na tela para coleta de dados do usuário:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se uma política de folha que herda essa base subsequentemente especificar `officeNumber` como uma declaração de **exibição** :

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

A declaração de `age` na política de base não é mais apresentada na tela ao usuário; ela é efetivamente "oculta". Para exibir a declaração de `age` e coletar o valor de idade do usuário, você deve adicionar um `age` **DisplayClaim**.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações a serem retornadas para a próxima etapa de orquestração. O atributo **DefaultValue** terá efeito somente se a declaração nunca tiver sido definida. Se ele foi definido em uma etapa de orquestração anterior, o valor padrão não terá efeito mesmo que o usuário deixe o valor vazio. Para forçar o uso de um valor padrão, defina o atributo **AlwaysUseDefaultValue** como `true`.

> [!NOTE]
> Em versões anteriores do IEF (Identity Experience Framework), as declarações de saída eram usadas para coletar dados do usuário. Para coletar dados do usuário, use uma coleção **DisplayClaims** em vez disso.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="when-you-should-use-output-claims"></a>Quando você deve usar declarações de saída

Em um perfil técnico autodeclarado, a coleção de declarações de saída retorna as declarações para a próxima etapa de orquestração.

Você deve usar declarações de saída quando:

- As **declarações são saídas pela transformação declarações de saída**.
- **Definir um valor padrão em uma declaração de saída** sem coletar dados do usuário ou retornar os dados do perfil técnico de validação. O perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` define a declaração **executed-SelfAsserted-Input** como `true`.
- **Um perfil técnico de validação retorna as declarações de saída**: seu perfil técnico pode chamar um perfil técnico de validação que retorna algumas declarações. Talvez você queira juntar as declarações e retorná-las para as próximas etapas de orquestração no percurso do usuário. Por exemplo, ao entrar com uma conta local, o perfil técnico autodeclarado de nome `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação chamado `login-NonInteractive`. Esse perfil técnico valida as credenciais do usuário e também retorna o perfil do usuário. Como 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Um controle de exibição retorna as declarações de saída** – seu perfil técnico pode ter uma referência a um [controle de exibição](display-controls.md). O controle de exibição retorna algumas declarações, como o endereço de email verificado. Talvez você queira juntar as declarações e retorná-las para as próximas etapas de orquestração no percurso do usuário. O recurso de controle de exibição está atualmente em **Visualização**.

O exemplo a seguir demonstra o uso de um perfil técnico autodeclarado que usa declarações de exibição e declarações de saída.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Declarações de persistência

Se o elemento **PersistedClaims** estiver ausente, o perfil técnico autodeclarado não persistirá os dados para o Azure AD B2C. Em vez disso, é feita uma chamada para um perfil técnico de validação responsável por persistir os dados. Por exemplo, a política de inscrição usa o perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` para coletar o novo perfil do usuário. O perfil técnico `LocalAccountSignUpWithLogonEmail` chama o perfil técnico de validação para criar a conta no Azure AD B2C.

## <a name="validation-technical-profiles"></a>Perfis técnicos de validação

Um perfil técnico de validação é usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. As declarações de entrada do perfil técnico de validação precisam aparecer nas declarações de saída do perfil técnico autodeclarado. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro ao usuário.

O perfil técnico de validação pode ser qualquer perfil técnico na política, como perfis técnicos do [Azure Active Directory](active-directory-technical-profile.md) ou da [API REST](restful-technical-profile.md). No exemplo anterior, o perfil técnico `LocalAccountSignUpWithLogonEmail` valida que o signinName não existe no diretório. Caso contrário, o perfil técnico de validação cria uma conta local e retorna objectId, authenticationSource e newUser. O perfil técnico `SelfAsserted-LocalAccountSignin-Email` chama o perfil técnico de validação `login-NonInteractive` para validar as credenciais do usuário.

Também é possível chamar um perfil técnico da API REST com a lógica de negócios, substituir as declarações de entrada ou aprimorar os dados do usuário integrando ainda mais com aplicativos de linha de negócios corporativos. Para saber mais, confira [Perfil técnico de validação](validation-technical-profile.md)

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| Setting. Operations <sup>1</sup>| Não | Em uma página de entrada, essa propriedade controla o comportamento do campo nome de usuário, como validação de entradas e mensagens de erro. Valores esperados: `Username` ou `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Não| Permitir para gerar uma declaração com valor nulo. Por exemplo, em um caso, o usuário não marca uma caixa de seleção.|
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| EnforceEmailVerification | Não | Na inscrição ou edição de perfil, reforça a verificação de email. Valores possíveis: `true` (padrão) ou `false`. |
| setting.retryLimit | Não | Controla a quantidade de vezes que um usuário pode tentar fornecer os dados verificados pelo perfil técnico de validação. Por exemplo, quanto um usuário tenta se inscrever com uma conta que já existe e continua tentando até alcançar o limite.
| SignUpTarget <sup>1</sup>| Não | O identificador de troca do destino da inscrição. Quando o usuário clica no botão de inscrição, o Azure AD B2C executa o identificador de troca especificado. |
| setting.showCancelButton | Não | Mostra o botão cancelar. Valores possíveis: `true` (padrão) ou `false` |
| setting.showContinueButton | Não | Mostra o botão continuar. Valores possíveis: `true` (padrão) ou `false` |
| configuração. showSignupLink <sup>2</sup>| Não | Mostra o botão de inscrição. Valores possíveis: `true` (padrão) ou `false` |
| configuração. forgotPasswordLinkLocation <sup>2</sup>| Não| Exibe o link esqueceu a senha. Valores possíveis: `AfterInput` (padrão) o link é exibido na parte inferior da página ou `None` remove o link de senha esquecida.| 
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true`ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina isso como `true`. |

Observações:
1. Disponível para definição de conteúdo [DataUri](contentdefinitions.md#datauri) tipo de `unifiedssp`ou `unifiedssd`.
1. Disponível para definição de conteúdo [DataUri](contentdefinitions.md#datauri) tipo de `unifiedssp`ou `unifiedssd`. [Layout de página versão](page-layout.md) 1.1.0 e posterior.

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** não será usado.
