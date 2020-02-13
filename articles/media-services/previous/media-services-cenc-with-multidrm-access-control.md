---
title: Designen av ett system för innehållsskydd med åtkomstkontroll med Azure Media Services | Microsoft Docs
description: Läs mer om hur du licensierar Microsoft Smooth Streaming-klienten porta Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162998"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Designen av ett system för innehållsskydd med åtkomstkontroll med Azure Media Services 

## <a name="overview"></a>Översikt

Utforma och skapa en digital rights management (DRM) undersystem för en over-the-top (OTT) eller online streaming lösning är en komplicerad uppgift. Operatörer/online video providers vanligtvis lägga ut den här uppgiften till specialiserade DRM-leverantörer. Målet med det här dokumentet är att presentera en referensdesign och implementering av en DRM-undersystemet för slutpunkt till slutpunkt i en OTT eller en lösning för liveuppspelning.

Riktade läsare för det här dokumentet är tekniker som arbetar i DRM delsystem av OTT eller strömning flera/skärmar onlinelösningar eller läsare som är intresserade av DRM-undersystem. Antas att läsaren känner till minst en av DRM-tekniker på marknaden, till exempel PlayReady, Widevine, FairPlay eller Adobe åtkomst.

I den här diskussionen av DRM innefatta vi också gemensam kryptering (CENC) med multi-DRM. En större trend i online strömning och OTT-branschen är att använda CENC med flera interna DRM på olika klientplattformar. Denna trend är en förändring från det föregående objekt som används av en enda DRM och dess klient-SDK för olika klientplattformar. När du använder CENC med multi-Native DRM krypteras både PlayReady och Widevine enligt specifikationen [common Encryption (ISO/IEC 23001-7 Cenc)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Fördelarna med CENC med multi-DRM är att den:

* Minskar kostnaden för kryptering eftersom ett enda krypteringsprocessen används för att fokusera på olika plattformar med dess inbyggda DRM: er.
* Minskar kostnaden för att hantera krypterade tillgångar eftersom bara en enda kopia av krypterade tillgångar krävs.
* Eliminerar DRM klienten licenskostnaderna eftersom den inbyggda DRM-klienten är vanligtvis kostnadsfritt på sin interna plattform.

Microsoft är en aktiv förslagsställare DASH och CENC tillsammans med vissa viktiga bransch-spelare. Azure Media Services har stöd för DASH och CENC. Senaste nyheterna finns i följande bloggar:

*  [Vi ber att få presentera Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services lägger till Google Widevine-paket för att leverera en multi-DRM-ström](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Målen för artikeln

Målen för den här artikeln är att:

* Ange en för referensdesign av en DRM-undersystemet som använder CENC med multi-DRM.
* Ange en referensimplementering på en Azure/Media Services-plattformen.
* Diskutera några design och implementering av ämnen.

I artikeln täcker termen ”multi-DRM” följande produkter:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

I följande tabell sammanfattas de interna plattform/internt app och webbläsare som stöds av varje DRM.

| **Klient plattform** | **Inbyggt DRM-stöd** | **Webbläsare/app** | **Strömmande format** |
| --- | --- | --- | --- |
| **Smarta TV-apparater, operatorer STBs, OTT STBs** |PlayReady främst och/eller Widevine och/eller andra |Linux, Opera, WebKit, andra |Olika format |
| **Windows 10-enheter (Windows-dator, Windows-surfplattor, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Universell Windows-plattform |STRECK (för HLS eller PlayReady stöds inte)<br/><br/>DASH, Smooth Streaming (HLS, PlayReady inte stöds) |
| **Android-enheter (telefon, surfplatta, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), OS X-klienter och Apple TV** |FairPlay |Safari 8 +/ EME |HLS |

Utifrån det aktuella tillståndet för varje DRM-distribution, en tjänst vanligtvis vill implementera två eller tre DRM: er att kontrollera att du har gått alla typer av slutpunkter på bästa sätt.

Det finns en kompromiss mellan komplexitet service logiken och komplexiteten på klientsidan för att nå en viss nivå av användarupplevelsen på olika klienter.

Om du vill göra ditt val att ha i åtanke:

* PlayReady implementeras internt på varje Windows-enhet på vissa Android-enheter och är tillgängliga via programvara SDK: er på i stort sett valfri plattform.
* Widevine implementeras internt i varje Android-enhet, Chrome och vissa andra enheter.
* FairPlay finns endast i iOS och Mac OS x-klienter eller via iTunes.

Det finns två alternativ för en typisk multi-DRM:

* PlayReady och Widevine
* PlayReady, Widevine och FairPlay

## <a name="a-reference-design"></a>En referensdesign
Det här avsnittet presenteras en referensdesign som är oberoende av de tekniker som används för att implementera den.

DRM-undersystemet kan innehålla följande komponenter:

* Nyckelhantering
* DRM-paketering
* DRM-licensleverans
* Berättigande kontroll
* Autentisering/auktorisering
* Spelare
* Ursprung/nätverk för innehållsleverans (CDN)

Följande diagram illustrerar övergripande interaktionen mellan komponenterna i en DRM-undersystemet:

![DRM-undersystemet med CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Designen har tre grundläggande lager:

* Ett BackOffice-lager (svart) exponeras inte externt.
* DMZ-lagret (mörkblå) innehåller alla slutpunkter som möter allmänheten.
* Offentliga internet-nivå (ljusblå) innehåller CDN och spelare med trafik över det offentliga internet.

Det bör även finnas ett innehållshantering-verktyg för att styra DRM-skydd, oavsett om det är statisk eller dynamisk kryptering. Indata för DRM-kryptering är:

* MBR videoinnehåll
* Innehållsnyckel
* URL: er för licens-förvärv

Här är det övergripande flödet under uppspelning tiden:

* Användaren har autentiserats.
* En autentiseringstoken skapas för användaren.
* DRM-skyddat innehåll (manifest) laddas ned till spelaren.
* Spelaren skickar en begäran för anskaffning av licens till servrar för fjärrskrivbordslicenser tillsammans med en nyckel-ID och en autentiseringstoken.

Följande avsnitt beskriver utformningen av nyckelhantering.

| **ContentKey-till-till-gång** | **Scenario** |
| --- | --- |
| 1-till-1 |Det enklaste fallet. Det ger finest kontrollen. Men leder detta till allmänt kostnaden för leverans av högsta licens. Minimum krävs licensbegäran om en för varje skyddad tillgång. |
| 1-till-många |Du kan använda samma innehållsnyckeln för flera tillgångar. Till exempel för alla tillgångar i en logisk grupp, kan till exempel en genre eller del av en genre (eller film gene) du använda en enda innehållsnyckel. |
| Många-till-1 |Flera nycklar behövs för varje resurs. <br/><br/>Om du ska använda skydd för dynamiska CENC med multi-DRM för MPEG-DASH och dynamisk AES-128-kryptering för HLS måste till exempel två separata nycklar. Varje innehållsnyckeln måste ha sin egen ContentKeyType. (För innehållsnyckeln används för dynamiska CENC skydd, använda ContentKeyType.CommonEncryption. För innehållsnyckeln används för dynamisk kryptering med AES-128, använder du ContentKeyType.EnvelopeEncryption.)<br/><br/>Ett annat exempel är kan CENC skydda DASH innehåll, i teorin, du använda en innehållsnyckel för att skydda videoströmmen och en annan innehållsnyckeln att skydda ljudströmmen. |
| Många-till-många |Kombinationen av de föregående två scenarierna. En uppsättning nycklar används för var och en av flera resurser i tillgångsgruppen samma. |

En annan viktig faktor är att använda beständiga och ickebeständig licenser.

Varför är detta viktigt?

Om du använder ett offentligt moln för licensleverans har permanent eller ickebeständig licenser en direkt inverkan på licenskostnaden för leverans. Följande två olika design-fall fungerar för att illustrera:

* Månatlig prenumeration: använda en beständig licens och en 1-till-många innehåll nyckeln till tillgången mappning. Vi kan till exempel använda en enda innehållsnyckel för kryptering för alla barnens filmer. I det här fallet:

    Totalt antal licenser som krävs för alla barn filmer/enhet = 1

* Månatlig prenumeration: använda en ickebeständig licens och 1-till-1-mappning mellan innehållsnyckeln och tillgången. I det här fallet:

    Totalt antal licenser som krävs för alla barn filmer/enhet = [antal filmer sett] x [antalet sessioner]

De två olika konstruktionerna resultera i mycket olika licens begäran mönster. De olika mönstren resultera i olika licensleverans om licensleveranstjänst tillhandahålls av ett offentligt moln, till exempel Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mappa design till teknik för implementering
Därefter är allmän designen mappad till tekniker i Azure/Media Services-plattformen genom att ange vilken teknik som ska användas för varje byggblock.

I följande tabell visar mappningen.

| **Bygg block** | **Teknik** |
| --- | --- |
| **Spela** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitets leverantör (IDP)** |Azure Active Directory (Azure AD) |
| **Säkerhetstokentjänst (STS)** |Azure AD |
| **Arbets flöde för DRM-skydd** |Media Services dynamisk skydd |
| **DRM-licensleverans** |* Media Services-licensleverans (PlayReady, Widevine, FairPlay) <br/>* Axinom licensserver <br/>* Anpassade PlayReady-licensserver |
| **Kommer** |Slutpunkten för direktuppspelning av Media Services |
| **Nyckelhantering** |Inte behövs för referensimplementering |
| **Innehållshantering** |En C#-konsolprogram |

Med andra ord används både IDP och STS med Azure AD. [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) : t används för spelaren. Både Media Services och Media Player stöder DASH och CENC med multi-DRM.

Följande diagram visar övergripande struktur och flödet med föregående teknik mappningen:

![CENC på Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Om du vill konfigurera dynamisk CENC kryptering använder verktyget innehållshantering följande indata:

* Öppna innehåll
* Innehållsnyckeln från nyckeln generation och hantering
* URL: er för licens-förvärv
* En lista med information från Azure AD

Här är utdata för verktyget innehållshantering:

* ContentKeyAuthorizationPolicy innehåller specifikation på hur licensleverans verifierar en JSON Web Token (JWT) och DRM-licens specifikationer.
* AssetDeliveryPolicy innehåller specifikationer direktuppspelning format, DRM-skydd och licens förvärv URL: er.

Här är flödet under körning:

* Vid autentisering av användare skapas en JWT.
* En av de anspråk som ingår i JWT är ett anspråk för grupper som innehåller gruppobjektet-ID EntitledUserGroup. Det här anspråket används för att skicka rätt kontrollen.
* Spelaren hämtar klienten manifestet av CENC-skyddat innehåll och identifierar följande:
   * Nyckel-ID.
   * Innehållet är CENC skyddas.
   * Licens-förvärv URL: er.
* Spelaren gör en begäran för anskaffning av licens baserat på webbläsaren/DRM som stöds. I licens förvärv begäran, nyckeln ID och JWT skickas också. Av licensleveranstjänst verifierar JWT och vilka anspråk som ingår innan den utfärdar den nödvändiga licensen.

## <a name="implementation"></a>Implementering
### <a name="implementation-procedures"></a>Implementering procedurer
Implementering omfattar följande steg:

1. Förbered test tillgångar. Koda/paketet en testvideo till flera bithastigheter fragmenterad MP4 i Media Services. Den här till gången är *inte* DRM-skyddad. DRM-skydd gör du genom dynamisk protection senare.

2. Skapa en nyckel-ID och en innehållsnyckel (du kan också från en nyckel seed). I den här instansen nyckelhanteringssystemet är inte nödvändigt, eftersom bara en enda nyckel-ID och innehållsnyckeln måste anges för ett par test tillgångar.

3. Använd Media Services-API för att konfigurera multi-DRM-licensleveranstjänster för test-tillgången. Om du använder anpassade licensservrar av ditt företag eller företagets leverantörer i stället för licens services i Media Services kan du hoppa över det här steget. Du kan ange URL: er för licens-förvärv i steg när du konfigurerar licensleverans. Media Services-API krävs för att ange vissa detaljerade konfigurationer, till exempel principbegränsning för auktorisering och licens svar mallar för olika tjänster för DRM-licens. För tillfället tillhandahåller inte Azure-portalen nödvändiga Användargränssnittet för den här konfigurationen. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).

4. Använd Media Services-API för att konfigurera tillgångsleveransprincip för test-tillgången. Information om API-nivå och exempel kod finns i [använda PlayReady och/eller Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).

5. Skapa och konfigurera en Azure AD-klient i Azure.

6. Skapa några användarkonton och grupper i Azure AD-klienten. Skapa minst en ”berättigade användare”-grupp och Lägg till en användare till den här gruppen. Användare i den här gruppen Skicka kontrollen rätt licenser. Användare inte i den här gruppen gick inte att skicka kontrollen och det går inte att hämta en licens. Medlemskap i gruppen ”berättigade användare” är ett obligatoriskt grupper anspråk i JWT som utfärdats av Azure AD. Du kan ange detta anspråk i steg när du konfigurerar multi-DRM-licensleveranstjänster.

7. Skapa en ASP.NET MVC-app ska vara värd för din videospelaren. Den här ASP.NET-program är skyddat med användarautentisering mot Azure AD-klient. Rätt anspråk som ingår i åtkomsttoken som hämtats efter autentisering av användare. Vi rekommenderar OpenID Connect API för det här steget. Installera följande NuGet-paket:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Skapa en spelare med hjälp av [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd [Azure Media Player ProtectionInfo-API](https://amp.azure.net/libs/amp/latest/docs/) för att ange vilken DRM-teknik som ska användas på olika DRM-plattformar.

9. I följande tabell visas test-matrisen.

    | **Rights** | **Webbläsare** | **Resultat för behörig användare** | **Resultat för berättigade användare** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge eller Internet Explorer 11 på Windows 10 |Lyckades |Misslyckades |
    | **Widevine** |Chrome, Firefox, Opera |Lyckades |Misslyckades |
    | **FairPlay** |Safari på macOS      |Lyckades |Misslyckades |
    | **AES-128** |De flesta moderna webbläsare  |Lyckades |Misslyckades |

Information om hur du konfigurerar Azure AD för en ASP.NET MVC Player-app finns i [integrera en Azure Media Services OWIN MVC-baserad app med Azure Active Directory och begränsa leverans av innehålls nycklar baserat på JWT-anspråk](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Mer information finns i [JWT token-autentisering i Azure Media Services och dynamisk kryptering](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Mer information om Azure AD:

* Du kan hitta information om utvecklare i [Azure Active Directory Developer ' s guide](../../active-directory/azuread-dev/v1-overview.md).
* Du kan hitta administratörs information i [administrera din Azure AD-klient katalog](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Vissa problem i implementeringen
Använd följande felsökningsinformation om du behöver hjälp med problem med principimplementering.

* Utfärdaren URL: en måste sluta med ”/”. Målgruppen måste vara player programmets klients-ID. Lägg även till ”/” i slutet av utfärdar-URL.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    I [JWT-avkodaren](http://jwt.calebb.net/)ser du **AUD** och **ISS**, som du ser i JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Lägg till behörigheter för programmet i Azure AD på fliken **Konfigurera** i programmet. Behörigheter som krävs för varje program, både lokala och distribuerade versioner.

    ![Behörigheter](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Använd rätt utfärdaren när du konfigurerar dynamisk CENC skydd.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Följande fungerar inte:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Det är Azure AD-klient-ID. Du hittar GUID på popup-menyn för **slut punkter** i Azure Portal.

* Bevilja gruppmedlemskap anspråk privilegier. Kontrollera att följande finns i Azure AD program-manifestfilen: 

    ”groupMembershipClaims”: ”alla” (standardvärdet är null)

* Ange rätt TokenType när du skapar begränsning av krav.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Eftersom du lägger till stöd för JWT (Azure AD) utöver SWT (ACS) är standard TokenType TokenType.JWT. Om du använder SWT/ACS måste du ange token till TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Ytterligare artiklar om implementering
Det här avsnittet beskrivs vissa ytterligare avsnitt i design och implementering.

### <a name="http-or-https"></a>HTTP eller HTTPS?
ASP.NET MVC player-program måste ha stöd för följande:

* Användarautentisering via Azure AD, som finns under HTTPS.
* JWT exchange mellan klienten och Azure AD, som är under HTTPS.
* DRM-licenser genom klienten, som måste vara under HTTPS om licensleverans tillhandahålls av Media Services. Produktsvit PlayReady säkerhetsbehov inte HTTPS för leverans av licens. Om licensservern PlayReady är utanför Media Services kan använda du antingen HTTP eller HTTPS.

Player ASP.NET-programmet använder HTTPS som bästa praxis så Media Player är på en sida under HTTPS. HTTP är prioriterade för strömning, så du behöver tänka på problemet av blandat innehåll.

* Blandat innehåll tillåts inte i webbläsaren. Men Tillåt att av plugin-program som Silverlight och OSMF plugin-program för smooth och bindestreck. Blandat innehåll är en säkerhetsfunktion på grund av hotet att möjligheten att mata in skadliga JavaScript, vilket kan orsaka kunddata ska vara utsatt för risk. Webbläsare blockera den här funktionen som standard. Det är det enda sättet att åtgärda problemet på serversidan (ursprungliga) genom att tillåta alla domäner (oavsett HTTPS eller HTTP). Detta är förmodligen inte bra antingen.
* Undvik att blandat innehåll. Både player-program och Media Player bör använda HTTP eller HTTPS. När du spelar upp blandat innehåll kräver silverlightSS teknisk att du avmarkerar en varning om blandat innehåll. FlashSS teknisk hanterar blandat innehåll utan att en varning om blandat innehåll.
* Om slutpunkten för direktuppspelning har skapats före augusti 2014, stöd inte det för HTTPS. I det här fallet, skapa och använda en ny slutpunkt för direktuppspelning för HTTPS.

I referensimplementering för DRM-skyddat innehåll, både programmet och direktuppspelning är under HTTPS. För att öppna innehållet behöver spelaren inte autentisering eller en licens, så du kan använda antingen HTTP eller HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory signeringsnyckel
Signeringsnyckel är en viktig aspekt att tänka på i din implementering. Om du ignorerar det upphör färdiga systemet så småningom att fungera helt, inom sex veckor högst.

Azure AD använder branschstandarder för att upprätta förtroende mellan själva och program som använder Azure AD. Mer specifikt använder Azure AD en signeringsnyckel som består av en offentlig och privat nyckel. När Azure AD skapar en säkerhetstoken som innehåller information om användaren, är det signerat av Azure AD med en privat nyckel innan den skickas tillbaka till programmet. Om du vill verifiera att token är giltig och har sitt ursprung från Azure AD genom måste programmet Validera token signatur. Programmet använder den offentliga nyckeln som exponeras av Azure AD som finns i klientens federationsmetadatadokumentet. Den offentliga nyckeln och signeringsnyckeln varifrån det kommer, är samma som används för alla klienter i Azure AD.

Mer information om förnyelse av Azure AD Key finns i [viktig information om förnyelse av signerings nyckel i Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mellan det [offentliga privata nyckel paret](https://login.microsoftonline.com/common/discovery/keys/):

* Den privata nyckeln används av Azure AD för att generera en JWT.
* Den offentliga nyckeln används av ett program, till exempel DRM-licensleveranstjänster i Media Services för att verifiera JWT.

Av säkerhetsskäl roterar certifikatet regelbundet (varje sex veckor) i Azure AD. När det gäller säkerhetsbrott, kan förnyelse av nycklar inträffa när som helst. Därför måste licensleveranstjänster i Media Services du uppdatera den offentliga nyckeln som används som Azure AD roterar nyckelparet. Annars misslyckas tokenautentisering i Media Services och ingen licens har utfärdats.

Om du vill konfigurera den här tjänsten måste ange du TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument, när du konfigurerar DRM-licensleveranstjänster.

Här är JWT-flödet:

* Azure AD utfärdar JWT med den aktuella privata nyckeln för en autentiserad användare.
* När en spelare ser en CENC med multi-DRM-skyddat innehåll, eftersöks JWT som utfärdats av Azure AD.
* Spelaren skickar en begäran för anskaffning av licens med JWT till licensleveranstjänster i Media Services.
* Licensleveranstjänster i Media Services kan du använda den aktuella/giltig offentlig nyckeln från Azure AD för att verifiera JWT innan du utfärdar licenser.

DRM-licensleveranstjänster Kontrollera alltid för den aktuella/giltig offentlig nyckeln från Azure AD. Den offentliga nyckeln som presenteras av Azure AD är den nyckel som används för att verifiera en JWT som utfärdats av Azure AD.

Vad händer om förnyelse av nycklar händer när du genererar en JWT för Azure AD men innan JWT skickas av spelare till DRM-licensleveranstjänster i Media Services för verifiering?

Eftersom en nyckel kan rullas när som helst, finns alltid mer än en giltig offentlig nyckel i federationsmetadatadokumentet. Media Services-licensleverans kan använda någon av nycklarna som anges i dokumentet. Eftersom en nyckel kan återställas snart, kanske en annan vara har ersatts och så vidare.

### <a name="where-is-the-access-token"></a>Var är den åtkomst-token?
Om du tittar på hur en webbapp anropar en API-app under [program identitet med OAuth 2,0-klientens autentiseringsuppgifter](../../active-directory/azuread-dev/web-api.md), är autentiseringsläget följande:

* En användare loggar in på Azure AD i webbprogrammet. Mer information finns i [webbläsare till webb program](../../active-directory/azuread-dev/web-app.md).
* Auktoriseringsslutpunkten för Azure AD omdirigerar användaragenten tillbaka till klientprogrammet med en auktoriseringskod. Användaragenten returnerar Auktoriseringskoden klientprogrammets omdirigerings-URI.
* Webbprogrammet måste hämta en åtkomsttoken så att den kan autentisera till webb-API och hämta önskad resurs. Den gör en begäran till tokenslutpunkten Azure AD och ger autentiseringsuppgifter, klient-ID och-API: er webbprogram-ID-URI. Den anger Auktoriseringskoden för att bevisa att användaren har godkänt.
* Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
* Över HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en beteckning ”ägar” i ”Authorization”-huvud för begäran till webb-API. Webb-API: verifierar sedan JWT. Om verifieringen lyckas returnerar önskad resurs.

I det här programmet identitet flödet litar webb-API på att webbprogrammet autentiserade användaren. Därför är det här mönstret kallas ett betrodda undersystem. [Diagrammet Flow-flöde](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) beskriver hur ett flöde för auktoriserings kod tilldelning fungerar.

Licenser med tokenbegränsningar följer samma mönster för betrodda undersystem. Licensleveranstjänst i Media Services är webb-API-resursen, eller ”backend-resursen” som behöver åtkomst till ett webbprogram. Så var är den åtkomst-token?

Åtkomst-token hämtas från Azure AD. Efter lyckad användarautentisering returneras en auktoriseringskod. Auktoriseringskoden används sedan, tillsammans med klient-ID och appnyckeln till exchange för åtkomst-token. Åtkomsttoken används för att få åtkomst till ett ”pointer”-program som pekar på eller representerar av Media Services licensleveranstjänst.

För att registrera och konfigurera pekaren-app i Azure AD, gör du följande:

1. I Azure AD-klient:

   * Lägg till ett program (resurs) med inloggnings-URL: en https://[resource_name].azurewebsites.net/. 
   * Lägg till en app-ID med URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Lägg till en ny nyckel för resursappen.

3. Uppdatera manifestfilen app så att groupMembershipClaims-egenskap har värdet ”groupMembershipClaims”: ”alla”.

4. I Azure AD-appen som pekar på Player-webbappen, i avsnittet **behörigheter till andra program**, lägger du till resurs programmet som lades till i steg 1. Under **delegerad behörighet**väljer du **åtkomst [resource_name]** . Det här alternativet ger web appen behörighet att skapa åtkomst-token som har åtkomst till resursappen. Gör detta för både lokala och distribuerade versionen av appen om du utvecklar med Visual Studio och Azure-webbappen.

JWT som utfärdats av Azure AD är den åtkomst-token som används för resursåtkomsten pekaren.

### <a name="what-about-live-streaming"></a>Vad gäller direktsänd strömning?
Föregående diskussion som fokuserar på tillgångar på begäran. Vad gäller direktsänd strömning?

Du kan använda exakt samma design och implementering för att skydda liveströmning i Media Services genom att behandla tillgången som associeras med ett program som en VOD tillgång.

Mer specifikt för att liveströmning i Media Services, måste du skapa en kanal och sedan skapa ett program under kanalen. För att skapa programmet, måste du skapa en tillgång som innehåller live-arkivet för programmet. För att ge CENC med multi-DRM-skydd av direktsänt innehåll, gäller samma installationen/bearbetning tillgången som om det vore en VOD-tillgång innan programmet startas.

### <a name="what-about-license-servers-outside-media-services"></a>Vad gäller servrar för fjärrskrivbordslicenser utanför Media Services?
Ofta kunder har investerat i en licens-servergrupp i sina egna Datacenter eller en med DRM-tjänstleverantörer. Med Media Services Content Protection, kan du arbeta i hybridläge. Innehållet kan som värd och dynamiskt skyddas i Media Services, medan DRM-licenser som levereras av servrar utanför Media Services. I det här fallet ska du tänka på följande ändringar:

* STS måste utfärda token som accepteras och kan verifieras av licens-servergruppen. Widevine-licens-servrar som tillhandahålls av Axinom kräver till exempel en specifik JWT som innehåller rätt meddelanden. Därför måste ha en STS att utfärda en sådan JWT. Information om den här typen av implementering finns i [Azures dokumentations Center](https://azure.microsoft.com/documentation/) och se [använda Axinom för att leverera Widevine-licenser till Azure Media Services](media-services-axinom-integration.md).
* Du behöver inte längre konfigurera licensleveranstjänst (ContentKeyAuthorizationPolicy) i Media Services. Du måste ange licensen förvärv URL: er (för PlayReady, Widevine och FairPlay) när du konfigurerar AssetDeliveryPolicy konfigurerar CENC med multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Vad händer om jag vill använda en anpassad STS?
En kund kan välja att använda en anpassad STS för att tillhandahålla JWTs. Orsaker är:

* IDP: N som används av kunden stöder inte STS. I det här fallet kan en anpassad STS vara ett alternativ.
* Kunden kan behöver flexibla eller bättre kontroll att integrera STS med kundens prenumerant faktureringssystem. Exempelvis kan operatören MVPD erbjuder flera OTT-prenumerant paket, som till exempel premium och basic-, och sport. Operatorn vilja matchar anspråk i en token med en prenumerant paketet så att bara innehållet i ett visst paket är tillgängliga. I det här fallet innehåller en anpassad STS nödvändiga flexibilitet och kontroll.

När du använder en anpassad STS måste två ändras:

* När du konfigurerar licensleveranstjänst för en tillgång kan behöva du ange säkerhetsnyckeln används för verifiering av anpassade STS i stället för den aktuella nyckeln från Azure AD. (Mer information följer.) 
* När en JTW-token genereras en säkerhetsnyckel har angetts i stället för den privata nyckeln för den aktuella X509 certifikat i Azure AD.

Det finns två typer av säkerhetsnycklar:

* Symmetrisk nyckel: samma nyckel används för att generera och verifiera en JWT.
* Asymmetrisk nyckel: ett offentligt / privat nyckelpar i en X509 används certifikat med en privat nyckel för att kryptera/Generera en JWT och med den offentliga nyckeln för att verifiera token.

> [!NOTE]
> Om du använder .NET Framework / C# som din utvecklingsplattform, X509 certifikatet som används för en asymmetrisk säkerhetsnyckel måste ha en nyckellängd på minst 2 048. Det här är ett krav för System.IdentityModel.Tokens.X509AsymmetricSecurityKey i .NET Framework-klassen. Annars genereras följande undantag:
> 
> IDX10630: System.IdentityModel.Tokens.X509AsymmetricSecurityKey för signering får inte vara mindre än '2048-bitar.

## <a name="the-completed-system-and-test"></a>Slutförda system och testning
Det här avsnittet vägleder dig genom följande scenarier i slutförda slutpunkt till slutpunkt-systemet så att du kan ha en grundläggande bild av beteendet innan du får ett inloggningskonto:

* Om du behöver ett icke-integrerat scenario:

    * För video tillgångar i Media Services som är antingen oskyddade eller DRM-skyddat men utan autentisering med enhetstoken (utfärdar en licens till den begärda det), kan du testa den utan att logga in. Växla till HTTP om din videoströmning är över HTTP.

* Om du behöver en integrerad slutpunkt till slutpunkt-scenario:

    * Du måste logga in för video-tillgångar under dynamisk DRM-skydd i Media Services med tokenautentisering och JWT som genereras av Azure AD.

För Player-webbprogrammet och dess inloggning, se [den här webbplatsen](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Användarinloggning
Du måste ha ett konto eller har lagts till för att testa det integrerade DRM-systemet för slutpunkt till slutpunkt.

Vilket konto?

Även om Azure ursprungligen åtkomst endast av Microsoft-kontoanvändare, tillåts nu åtkomst av användare från båda systemen. Alla Azure-egenskaper nu lita på Azure AD för autentisering och Azure AD autentiserar användare i organisationer. En federationsrelation skapades där Azure AD litar på Microsoft-konto-konsumentidentitetssystemet konsumentanvändare. Azure AD kan därmed autentisera gästkonton Microsoft-konton samt enligt intern Azure AD.

Eftersom Azure AD litar på domänen för Microsoft-konto, kan du lägga till alla konton från någon av följande domäner med anpassade Azure AD-klient och använda kontot för att logga in:

| **Domän namn** | **Domänsuffix** |
| --- | --- |
| **Anpassad Azure AD-klient domän** |somename.onmicrosoft.com |
| **Företags domän** |Microsoft.com |
| **Microsoft-konto domän** |Outlook.com, live.com, hotmail.com |

Du kan kontakta någon av författarna till ett konto eller har lagts till för dig.

De följande skärmbilderna visar olika inloggningssidorna används av olika domänkonton:

**Anpassat Azure AD-klientens domän konto**: den anpassade inloggnings sidan för den anpassade Azure AD-klient domänen.

![Domänkonto för anpassat Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft-domännamn med smartkort**: inloggnings sidan som anpassats av Microsoft Corporate IT med tvåfaktorautentisering.

![Domänkonto för anpassat Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-konto**: inloggnings sidan för Microsoft-konto för konsumenter.

![Domänkonto för anpassat Azure AD-klient](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Använda krypterade Media-tilläggen för PlayReady
PlayReady finns på en modern webbläsare med tillägg EME (Encrypted Media) för PlayReady-support, till exempel Internet Explorer 11 på Windows 8.1 eller senare och Microsoft Edge-webbläsaren på Windows 10, den underliggande DRM för EME.

![Använd EME för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Mörk player-området beror på PlayReady-skydd som hindrar dig från att göra en skärmdump av skyddade video.

Följande skärmbild visar player plugin-program och Microsoft Security Essentials (MSE) / EME stöd:

![Player-plugin-program för PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME i Microsoft Edge och Internet Explorer 11 i Windows 10 gör att [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kan anropas på Windows 10-enheter som har stöd för det. PlayReady SL3000 låser upp flödet av förbättrad premiuminnehåll (4K, HDR) och nytt innehåll leveransmodell (för förbättrat innehåll).

För att fokusera på de Windows-enheterna, är PlayReady den enda DRM i maskinvaran som är tillgängliga på Windows-enheter (PlayReady SL3000). En direktuppspelningstjänst kan använda PlayReady via EME eller via en Universal Windows Platform-program och erbjuda en högre videokvalitet genom att använda PlayReady SL3000 än en annan DRM. Normalt innehåll upp till 2K flödar Chrome eller Firefox, och upp till 4K-flöden via Microsoft Edge/Internet Explorer 11 eller en Universal Windows Platform-program på samma enhet. Hur mycket beror på tjänstinställningar och implementering.

#### <a name="use-eme-for-widevine"></a>Använd EME för Widevine
På en modern webbläsare med EME/Widevine-support, till exempel Chrome 41 + på Windows 10, Windows 8.1-, Mac OS x Yosemite och Chrome för Android 4.4.4 är Google Widevine DRM bakom EME.

![Använd EME för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine hindra inte dig från att göra en skärmdump av skyddade video.

![Player-plugin-program för Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled användare
Om en användare inte är medlem i gruppen ”berättigade användare”, klarade användaren inte rättigheten. Multi-DRM-Licenstjänsten sedan vägrar att utfärda begärda licensen som visas. Den detaljerade beskrivningen är ”licens hämta misslyckades”, som är som avsett.

![Unentitled användare](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Kör en anpassad säkerhetstokentjänst
Om du kör en anpassad STS har i JWT utfärdats av anpassade STS med hjälp av en symmetrisk eller en asymmetrisk nyckel.

Följande skärmbild visas ett scenario som använder en symmetrisk nyckel (med Chrome):

![Anpassade STS med en symmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

I följande skärmbild visas ett scenario som använder en asymmetrisk nyckel via en X509 certifikat (med en modern webbläsare Microsoft):

![Anpassade STS med en asymmetrisk nyckel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

I båda tidigare fall förblir användarautentisering densamma. Det sker via Azure AD. Den enda skillnaden är att JWTs utfärdas av anpassade STS i stället för Azure AD. När du konfigurerar dynamisk CENC protection anger licens delivery service begränsningen vilken typ av JWT, en symmetrisk eller en asymmetrisk nyckel.

## <a name="summary"></a>Sammanfattning

Det här dokumentet beskrivs CENC med flera interna DRM och åtkomstkontroll via tokenautentisering, dess design och dess implementering med hjälp av Azure Media Services och Media Player.

* En referensdesign angavs som innehåller alla de nödvändiga komponenterna i ett DRM/CENC undersystem.
* En referensimplementering angavs på Azure Media Services och Media Player.
* Vissa ämnen som är direkt inblandade i design och implementering beskrivs också.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
