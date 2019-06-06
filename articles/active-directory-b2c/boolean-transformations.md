---
title: Booleskt värde anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Booleskt värde anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a08849340d19055a03f85ca401757a81cd2c95d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511747"
---
# <a name="boolean-claims-transformations"></a>Booleska anspråksomvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda booleskt anspråksomvandlingar av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Utförs av två booleskt inputClaims och ställer in outputClaim med resultatet av åtgärden.

| Objekt  | TransformationClaimType  | Datatyp  | Anteckningar |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolesk | Första ClaimType att utvärdera. |
| InputClaim | inputClaim2  | boolesk | Andra ClaimType att utvärdera. |
|OutputClaim | outputClaim | boolesk | ClaimTypes som skapas när detta omvandling av anspråk har anropats (SANT eller FALSKT). |

Följande anspråkstransformering visar hur du och två booleskt ClaimTypes: `isEmailNotExist`, och `isSocialAccount`. Utdata-anspråket `presentEmailSelfAsserted` är inställd på `true` om värdet för båda inkommande anspråk är `true`. I ett orchestration-steg kan du använda ett villkor för att ange en självkontrollerad sida, bara om ett e-postmeddelande för socialt konto är tom.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Utgående anspråk:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontrollerar att booleska värden för två anspråk är likvärdiga och utlöser ett undantag om de inte.

| Objekt | TransformationClaimType  | Datatyp  | Anteckningar |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolesk | ClaimType verifieringsvillkor som ska kontrolleras. |
| InputParameter |valueToCompareTo | boolesk | Värde att jämföra (SANT eller FALSKT). |

Den **AssertBooleanClaimIsEqualToValue** anspråkstransformering utförs alltid från en [teknisk verifieringsprofil](validation-technical-profile.md) som anropas av en [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md). Den **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** självkontrollerad tekniska profilens metadata styr det felmeddelande som den tekniska profilen som visas för användaren.

![AssertStringClaimsAreEqual körning](./media/boolean-transformations/assert-execution.png)

Följande anspråkstransformering visar hur du kontrollerar värdet för en boolesk ClaimType med en `true` värde. Om värdet för den `accountEnabled` ClaimType är FALSKT, genereras ett felmeddelande.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Den `login-NonInteractive` verifiering tekniska profilen anrop den `AssertAccountEnabledIsTrue` omvandling av anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den tekniska profilen självkontrollerad anropar verifieringen **inloggning utan interaktivitet** tekniska profilen.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultat: Fel uppstod

## <a name="notclaims"></a>NotClaims

Utför en inte av boolesk inputClaim och ställer in outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolesk | Anspråk att vara i drift. |
| OutputClaim | outputClaim | boolesk | ClaimTypes som genereras när den här ClaimsTransformation har anropats (SANT eller FALSKT). |

Använda den här anspråksomvandling för att utföra logisk negation på ett anspråk.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim**: false
- Utgående anspråk:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims 

Beräknar en Or av två booleskt inputClaims och ställer in outputClaim med resultatet av åtgärden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolesk | Första ClaimType att utvärdera. |
| InputClaim | inputClaim2 | boolesk | Andra ClaimType att utvärdera. |
| OutputClaim | outputClaim | boolesk | ClaimTypes som skapas när den här ClaimsTransformation har anropats (SANT eller FALSKT). |

Följande anspråkstransformering visar hur du `Or` två booleskt ClaimTypes. I orchestration-steg du kan använda ett villkor för att ange en självkontrollerad sida om värdet för ett av anspråken är `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Utgående anspråk:
    - **outputClaim**: true

