---
title: Windows 10 roaming-inställningar | Microsoft Docs
description: En fullständig lista över alla inställningar som ska flyttade eller säkerhetskopierats i Windows 10.
services: active-directory
keywords: Enterprise tillståndsväxling, windows-molnet
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: 6698aa7e16b351190f9a5cdde62766cf5d7e1809
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095999"
---
# <a name="windows-10-roaming-settings-reference"></a>Referens för Windows 10-växlingsinställningar
Följande är en fullständig lista över alla inställningar som ska flyttade eller säkerhetskopierats i Windows 10. 

## <a name="devices-and-endpoints"></a>Enheter och slutpunkter
Se följande tabell för en sammanfattning av enheter och typer som stöds av sync, säkerhetskopiering, och Återställ framework i Windows 10.

| Kontotyp och åtgärd | Skrivbord | Mobil |
| --- | --- | --- |
| Azure Active Directory: synkronisering |Ja |Nej |
| Azure Active Directory: säkerhetskopiering/återställning |Nej |Nej |
| Microsoft-konto: synkronisering |Ja |Ja |
| Microsoft-konto: säkerhetskopiering/återställning |Nej |Ja |

## <a name="what-is-backup"></a>Vad är säkerhetskopiering?
Windows-inställningar som är allmänt synkroniseras som standard, men vissa inställningar bara säkerhetskopieras, till exempel listan över installerade program på en enhet. Backup är för mobila enheter endast och för närvarande inte tillgängligt för Enterprise State Roaming användare. Backup använder ett Microsoft-konto och lagrar inställningar och programdata till OneDrive. Om en användare inaktiverar synkronisering på enheten med appen inställningar, blir programdata som normalt synkroniseras säkerhetskopiering endast. Säkerhetskopierade data kan endast nås via återställningsåtgärden på nytt under den första körning av en ny enhet. Säkerhetskopieringar kan inaktiveras via Enhetsinställningar, och kan hanteras och tas bort via användarens OneDrive-konto.

## <a name="windows-settings-overview"></a>Översikt över Windows-inställningar
Följande inställningsgrupper med är tillgängliga för slutanvändarna att aktivera/inaktivera synkronisering av inställningar på Windows 10-enheter.

* Tema: skrivbordsbakgrund, användarpanel, Aktivitetsfältet plats, osv. 
* Internet Explorer-inställningar: webbhistorik har angett URL: er, Favoriter, osv. 
* Lösenord: Windows Autentiseringshanteraren, inklusive Wi-Fi-profiler 
* Språkinställningar: stavning ordboken, systeminställningar för språk 
* Hjälpmedel: Skärmläsaren, Skärmtangentbordet, Skärmförstoraren 
* Andra inställningar för Windows: se information om Windows-inställningar
* Inställningen för Microsoft Edge-webbläsaren: Microsoft Edge-Favoriter, läsning lista och andra inställningar

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

Inställningen för Microsoft Edge-webbläsare (Favoriter, läsa listan) Gruppsynkronisering kan aktiveras eller inaktiveras av slutanvändare via Microsoft Edge-webbläsaren menyalternativet för inställningar.

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

För Windows 10 version 1803 eller senare, Internet Explorer i gruppen (Favoriter har angett URL: er) synkroniseras kan aktiveras eller inaktiveras av slutanvändare via Internet Explorer-inställningar. 

![](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Information om Windows-inställningar
I följande tabell, andra posterna i kolumnen Enhetsinställningsgruppen refererar till inställningar som kan inaktiveras genom att gå till Inställningar > konton > Synkronisera dina inställningar > andra Windows-inställningar. 

Intern posterna i kolumnen Enhetsinställningsgruppen avser inställningar och program som kan endast inaktiveras synkroniseras i själva appen eller genom att inaktivera synkronisering för hela enheten med hjälp av hantering av mobilenheter (MDM) eller inställningarna för Grupprincip.
Inställningar som inte flyttas eller sync kommer inte tillhör en grupp.

| Inställningar | Skrivbord | Mobil | Grupp |
| --- | --- | --- | --- |
| **Konton**: kontobild |Synkronisering |X |Tema |
| **Konton**: andra kontoinställningar |X |X | |
| **Avancerade mobilt bredband**: Internetanslutningsdelning nätverksnamn (möjliggör automatisk identifiering av mobila Wi-Fi-anslutningar via Bluetooth) |X |X |Lösenord |
| **AppData**: enskilda appar kan synkronisera data |synkronisering av säkerhetskopiering |synkronisering av säkerhetskopiering |Intern |
| **Applista**: listan över installerade appar |X |säkerhetskopiering |Annat |
| **Bluetooth**: alla Bluetooth-inställningar |X |X | |
| **Kommandotolk**: Kommandot prompt ”standard”-inställningar |Synkronisering |X |Intern |
| **autentiseringsuppgifter**: Säker plats för autentiseringsuppgifter |Synkronisering |Synkronisering |lösenord |
| **Datum, tid och Region**: automatiska tiden (Internet tidssynkronisering) |Synkronisering |Synkronisering |language |
| **Datum, tid och Region**: 24-timmarsformat |Synkronisering |X |language |
| **Datum, tid och Region**: datum och tid |Synkronisering |X |language |
| **Datum, tid och Region**: tidszon | |X |language |
| **Datum, tid och Region**: sommartid |Synkronisering |X |language |
| **Datum, tid och Region**: land/region |Synkronisering |X |language |
| **Datum, tid och Region**: första dagen i veckan |Synkronisering |X |language |
| **Datum, tid och Region**: regionsformat (nationella inställningar) |Synkronisering |X |language |
| **Datum, tid och Region**: kort datum |Synkronisering |X |language |
| **Datum, tid och Region**: långt datum |Synkronisering |X |language |
| **Datum, tid och Region**: kort tid |Synkronisering |X |language |
| **Datum, tid och Region**: lång tid |Synkronisering |X |language |
| **Skrivbordsanpassning**: tema (bakgrund, systemfärg, standard systemljud, skärmsläckare) |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: bildspel bakgrundsbild |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: Aktivitetsfältet inställningar (position, Dölj automatiskt osv.) |Synkronisering |X |Tema |
| **Skrivbordsanpassning**: starta skärmlayout |X |säkerhetskopiering | |
| **Enheter**: delade skrivare som du har anslutit till |X |X |övrigt |
| **Microsoft Edge-webbläsaren**: läslista |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: Favoriter |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: de främsta platser <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: har angett URL: er <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: inställningar för Favoriter <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: Visa startknappen <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: blockera popup-fönster <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: Fråga mig vad som ska göras med varje version <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: erbjuder att spara lösenord <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: skicka do not track-begäranden <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: spara formulärposter <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: visa förslag för sökning och när jag skriver <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: inställningar för cookies <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: Låt platser spara skyddade media licenser på min enhet <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Microsoft Edge-webbläsaren**: Skärmläsaren inställningen <sup> [[1]](#footnote-1)</sup> |Synkronisering |Synkronisering |Intern |
| **Högkontrast**: Aktivera eller inaktivera |Synkronisering |X |hjälpmedel |
| **Högkontrast**: Temainställningar för |Synkronisering |X |hjälpmedel |
| **Internet Explorer**: öppna flikar (URL: en och rubrik) |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: läslista |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: har angett URL: er |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: webbhistorik |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: Favoriter |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: undantagna URL: er |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: startsidor |Synkronisering |Synkronisering |Internet Explorer |
| **Internet Explorer**: domän förslag |Synkronisering |Synkronisering |Internet Explorer |
| **Tangentbord**: användare kan slå på/av skärmtangentbordet |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera sticky Ja (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera filter-nycklar (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Tangentbord**: aktivera växla nycklar (inaktiverat som standard) |Synkronisering |X |hjälpmedel |
| **Internet Explorer**: domän språk: Kinesiska (CHS) QWERTY – aktivera lokal utbildning |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY – aktivera dynamisk kandidat rangordning |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - teckenuppsättningen kinesiska (förenklad) |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - teckenuppsättningen traditionell kinesiska |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY - fuzzy pinyin |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY - fuzzy par |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: CHS QWERTY – fullständig pinyin |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY – dubbla pinyin |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY – läser Autokorrigering |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY – C/E-växel nyckel, SKIFT |Synkronisering |X |Språk |
| **Språk**: CHS QWERTY – C/E växla nyckel, Ctrl |Synkronisering |X |Språk |
| **Språk**: CHS WUBI - indata läge för enstaka tecken |Synkronisering |X |Språk |
| **Språk**: CHS WUBI – visa de återstående kodning av kandidaten |Synkronisering |X |Språk |
| **Språk**: CHS WUBI - signal vid 4-kodning är ogiltig |Synkronisering |X |Språk |
| **Språk**: CHT Bopomofo - inkludera CJK Ext-A |Synkronisering |X |Språk |
| **Språk**: Japanska IME - förutsägande skriva och anpassade ord |Synkronisering |Synkronisering |Språk |
| **Språk**: Koreanska (KOR) IME |X |X |Språk |
| **Språk**: handskriftsigenkänning |X |X |Språk |
| **Språk**: språk profil |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: Spell check - Autokorrigering och markera felstavningar |Synkronisering |säkerhetskopiering |Språk |
| **Språk**: lista över tangentbord |Synkronisering |säkerhetskopiering |Språk |
| **Lås skärmen**: alla låsa skärminställningar |X |X | |
| **Skärmförstoraren**: aktivera eller inaktivera (master växla) |X |X |Hjälpmedel |
| **Skärmförstoraren**: aktivera invertering färg eller inaktivera (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: spåra - Följ tangentbordsfokus |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: spåra - Följ markören |Synkronisering |X |Hjälpmedel |
| **Skärmförstoraren**: starta när användare loggar in (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Mus**: ändra storlek på markören |Synkronisering |X |övrigt |
| **Mus**: ändra färg på markören |Synkronisering |X |övrigt |
| **Mus**: alla andra inställningar |X |X | |
| **Skärmläsaren**: Snabbstart |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan ändra Skärmläsaren talar försäljningsargument |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera Skärmläsaren läser tips för vanliga objekt (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra tecken (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: användare kan aktivera eller inaktivera om de kan höra skrivna ord (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: har insert markören följa Skärmläsaren (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: aktivera visuella markeringar för Skärmläsaren markören (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: spela upp ljud (på som standard) |Synkronisering |X |Hjälpmedel |
| **Skärmläsaren**: aktivera nycklar på skärmtangentbordet när du lyfter fingret (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Hjälpmedel**: ange tjockleken på blinkande markör |Synkronisering |X |Hjälpmedel |
| **Hjälpmedel**: tar bort bakgrundsbilder (inaktiverat som standard) |Synkronisering |X |Hjälpmedel |
| **Kraften och strömsparläge**: alla inställningar |X |X | |
| **Starta skärmen personanpassning**: dekorfärg färg (endast mobil) |X |Synkronisering |Tema |
| **Att skriva**: stavningsordlista |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Autokorrigering felstavade ord |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Markera felstavade ord. |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Visa textförslag medan du skriver |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: lägga till ett blanksteg efter att du har valt en textförslag |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: Lägg till en punkt efter att jag dubbelknacka med blanksteg |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: utnyttja den första bokstaven i varje mening |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: använda versaler när jag dubbelknacka SKIFT-tangenten |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: spela upp ljud som nyckel när jag skriver |Synkronisering |säkerhetskopiering |Språk |
| **Att skriva**: anpassningsdata för Skärmtangentbordet |Synkronisering |säkerhetskopiering |Språk |
| **Wi-Fi**: Wi-Fi-profiler (endast WPA) |Synkronisering |Synkronisering |Lösenord |

###### <a name="footnote-1"></a>Fotnot 1
Minsta stöd för OS-versionen av Windows Creators Update (skapa 15063). 

## <a name="next-steps"></a>Nästa steg

En översikt finns i [företagsroaming översikt](enterprise-state-roaming-overview.md).

