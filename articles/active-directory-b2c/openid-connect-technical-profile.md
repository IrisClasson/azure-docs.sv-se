---
title: Definiera en OpenID Connect-teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en OpenID Connect Technical Profile i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5a83a8ab608e685f43056debe45877965e0c53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204005"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OpenID Connect-teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) har stöd för [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) Protocol Identity Provider. OpenID Connect 1,0 definierar ett identitets lager ovanpå OAuth 2,0 och representerar status för grafiken i moderna autentiseringsprotokoll. Med en OpenID Connect-teknisk profil kan du federera med en OpenID Connect-baserad identitets leverantör, t. ex. Azure AD. Genom att federera med en identitets leverantör kan användare logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `OpenIdConnect` . Till exempel är protokollet för den tekniska profilen **MSA-OIDC** `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

Elementen **InputClaims** och **InputClaimsTransformations** krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitets leverantör. I följande exempel lägger du till parametern **domain_hint** frågesträngparametern med värdet för `contoso.com` auktorisering av begäran.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av OpenID Connect Identity Provider. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också inkludera anspråk som inte returneras av identitets leverantören, så länge du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Microsoft-kontots identitets leverantör:

- Det **under** anspråk som är mappat till **issuerUserId** -anspråket.
- Det **namn** anspråk som är mappat till anspråket **DisplayName** .
- **E-postmeddelandet** utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program identifieraren för identitets leverantören. |
| IdTokenAudience | No | Id_tokenens mål grupp. Om det här alternativet anges kontrollerar Azure AD B2C om `aud` anspråket i en token som returneras av identitets leverantören är lika med det som anges i IdTokenAudience metadata.  |
| METADATATJÄNST | Ja | En URL som pekar på ett konfigurations dokument för OpenID Connect Identity Provider, som även kallas OpenID-känd konfigurations slut punkt. URL: en kan innehålla `{tenant}` uttrycket, som ersätts med klient namnet.  |
| authorization_endpoint | No | En URL som pekar på en OpenID ansluter till en slut punkt för konfigurering av identitets leverantör. Värdet för authorization_endpoint metadata har företräde framför den som `authorization_endpoint` anges i den välkända konfigurations slut punkten för OpenID. URL: en kan innehålla `{tenant}` uttrycket, som ersätts med klient namnet. |
| utfärdare | No | Den unika identifieraren för en OpenID Connect Identity Provider. Värdet för utfärdare metadata har företräde framför den som `issuer` anges i den välkända OpenID konfigurations slut punkten.  Om det här alternativet anges kontrollerar Azure AD B2C om `iss` anspråket i en token som returneras av identitets leverantören är lika med det som anges i utfärdarens metadata. |
| ProviderName | No | Namnet på identitets leverantören.  |
| response_types | No | Svars typen enligt OpenID Connect core 1,0-specifikationen. Möjliga värden: `id_token` , `code` , eller `token` . |
| response_mode | No | Metoden som identitets leverantören använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga värden: `query` , `form_post` (standard) eller `fragment` . |
| omfång | No | Omfattningen av begäran som definieras enligt OpenID Connect core 1,0-specifikationen. Till exempel `openid` , `profile` och `email` . |
| HttpBinding | No | Den förväntade HTTP-bindningen till åtkomsttoken och slut punkter för anspråks-token. Möjliga värden: `GET` eller `POST` .  |
| ValidTokenIssuerPrefixes | No | En nyckel som kan användas för att logga in på varje klient organisation när du använder en identitets leverantör för flera innehavare, till exempel Azure Active Directory. |
| UsePolicyInRedirectUri | No | Anger om en princip ska användas vid konstruktion av omdirigerings-URI. När du konfigurerar ditt program i identitets leverantören måste du ange omdirigerings-URI: n. Omdirigerings-URI: n pekar på Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Om du anger `false` måste du lägga till en omdirigerings-URI för varje princip som du använder. Exempel: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | No | Anger om en begäran till en extern tjänst ska markeras som ett haveri om HTTP-statuskoden finns i 5xx-intervallet. Standardvärdet är `false`. |
| DiscoverMetadataByTokenIssuer | No | Anger om OIDC metadata ska identifieras med hjälp av utfärdaren i JWT-token. |
| IncludeClaimResolvingInClaimsHandling  | No | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` , eller `false`   (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true` . |

### <a name="ui-elements"></a>Element för användargränssnitt
 
Följande inställningar kan användas för att konfigurera fel meddelandet som visas vid fel. Metadata ska konfigureras i den tekniska profilen OpenID Connect. Fel meddelandena kan [lokaliseras](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Meddelandet som ska visas för användaren om ett konto med det angivna användar namnet inte hittades i katalogen. |
| UserMessageIfInvalidPassword | No | Meddelandet som ska visas för användaren om lösen ordet är felaktigt. |
| UserMessageIfOldPasswordUsed| No |  Meddelandet som ska visas för användaren om ett gammalt lösen ord används.|

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klient hemligheten för Identity Provider-programmet. Krypterings nyckeln krävs endast om **response_types** metadata har angetts till `code` . I det här fallet gör Azure AD B2C ett annat anrop till Exchange-auktoriseringskod för en åtkomsttoken. Om metadata har angetts till `id_token` kan du utelämna den kryptografiska nyckeln.  |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar en omdirigerings-URI för din identitetsprovider anger du `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Se till att ersätta `{your-tenant-name}` med klient organisationens namn. Omdirigerings-URI: n måste vara i gemener.

Exempel:

- [Lägg till Microsoft-konto (MSA) som en identitets leverantör med anpassade principer](identity-provider-microsoft-account-custom.md)
- [Logga in med Azure AD-konton](identity-provider-azure-ad-single-tenant-custom.md)
- [Tillåt att användare loggar in på en Azure AD-identitetsprovider med flera innehavare med anpassade principer](identity-provider-azure-ad-multi-tenant-custom.md)
