---
title: Felsökning av Azure Data Box-disk | Microsoft Docs
description: Beskriver hur du felsöker problem i Azure Data Box-disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 83b3a271006df38744b9de49ed6350bea3aeef4d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159391"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk"></a>Felsöka problem i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk och beskriver de arbetsflöden som används för att felsöka eventuella problem som du ser när du distribuerar den här lösningen. 

Den här artikeln innehåller följande avsnitt:

- Ladda ned diagnostikloggar
- Frågeaktivitetsloggar
- Fel med verktyget Data Box Disk Unlock
- Fel med verktyget Data Box Disk Split Copy

## <a name="download-diagnostic-logs"></a>Ladda ned diagnostikloggar

Om det uppstår några fel under datakopieringsprocessen visar portalen en sökväg till den mapp där diagnostikloggarna finns. 

Diagnostikloggarna kan vara:
- Felloggar
- Utförliga loggar  

För att navigera till sökvägen för kopieringsloggen går du till det lagringskonto som associerats med din Data Box-beställning. 

1.  Gå till **Allmänt > Beställningsinformation** och anteckna det lagringskonto som är associerat med din beställning.
 

2.  Gå till **Alla resurser** och sök efter det lagringskonto som identifierades i föregående steg. Välj och klicka på lagringskontot.

    ![Kopiera loggar 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Gå till **Blob Service > Bläddra efter blobar** och leta efter den blob som motsvarar lagringskontot. Gå till **diagnosticslogcontainer > waies**. 

    ![Kopiera loggar 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Du bör se både felloggarna och utförliga loggar för datakopiering. Välj och klicka på varje fil och ladda sedan ned en lokal kopia.

## <a name="query-activity-logs"></a>Frågeaktivitetsloggar

Använd aktivitetsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs. Via aktivitetsloggarna kan du fastställa:

- Vilka åtgärder som utfördes på resurserna i din prenumeration.
- Den som initierade åtgärden. 
- När åtgärden utfördes.
- Status för åtgärden.
- Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden.

Aktivitetsloggen innehåller alla skrivåtgärder (till exempel PUT, POST, DELETE) som utförts på dina resurser, men inte läsåtgärderna (till exempel GET). 

Aktivitetsloggar bibehålls i 90 dagar. Du kan fråga efter alla datumintervall förutsatt att startdatumet inte är mer än 90 dagar bakåt i tiden. Du kan även filtrera med någon av de inbyggda frågorna i Insights. Till exempel kan du klicka på ett fel och sedan välja och klicka på specifika fel för att förstå rotorsaken.

## <a name="data-box-disk-unlock-tool-errors"></a>Fel med verktyget Data Box Disk Unlock


| Felmeddelande/verktygsbeteende      | Rekommendationer                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Ingen<br><br>Upplåsningsverktyget för Data Box-disk kraschar.                                                                            | BitLocker är inte installerat. Kontrollera att den värddator som kör upplåsningsverktyget för Data Box-disk har BitLocker installerat.                                                                            |
| Aktuellt .NET Framework stöds inte. De versioner som stöds är 4.5 och senare.<br><br>Verktyget avslutas med ett meddelande.  | .NET 4.5 är inte installerat. Installera .NET 4.5 eller senare på den värddator som kör upplåsningsverktyget för Data Box-disk.                                                                            |
| Det gick inte att låsa upp eller verifiera några volymer. Kontakta Microsoft-supporten.  <br><br>Verktyget misslyckas med att låsa upp eller verifiera låsta enheter. | Verktyget kunde inte låsa upp någon av de låsta enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp.                                                |
| Följande volymer är olåsta och verifierade. <br>Enhetsbeteckningar för volymen: E:<br>Det gick inte att låsa upp alla volymer med följande nycklar: werwerqomnf, qwerwerqwdfda <br><br>Verktyget låser upp vissa enheter och visar lyckade och misslyckade enhetsbeteckningar.| Slutfördes delvis. Det gick inte att låsa upp några av enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Det gick inte att hitta låsta volymer. Kontrollera att den disk som mottogs från Microsoft är korrekt ansluten och är låst.          | Verktyget kan inte hitta några låsta enheter. Antingen är enheterna redan upplåsta eller så identifieras de inte. Kontrollera att enheterna är anslutna och att de är låsta.                                                           |
| Allvarligt fel: Ogiltig parameter<br>Parameternamn: invalid_arg<br>ANVÄNDNING:<br>DataBoxDiskUnlock /PassKeys:<lista_över_nycklar_separerade_med_semikolon><br><br>Exempel: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Exempel: DataBoxDiskUnlock /SystemCheck<br>Exempel: / Help DataBoxDiskUnlock<br><br>/ Nycklar:       Hämta den här nyckeln från Azure DataBox diskbeställning. Nyckeln låser upp dina diskar.<br>/ Hjälp:           Det här alternativet innehåller hjälp om cmdlet-syntax och exempel.<br>/ SystemCheck:    Det här alternativet kontrollerar om datorn uppfyller kraven för att köra verktyget.<br><br>Tryck på valfri tangent för att avsluta. | En ogiltig parameter har angetts. De enda tillåtna parametrarna är /SystemCheck, /PassKey och /Help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Fel med verktyget Data Box Disk Split Copy

|Felmeddelande/varningar  |Rekommendationer |
|---------|---------|
|[Info] Hämta bitlocker-lösenordet för volymen: m <br>[Fel] Undantag inträffade vid hämtning av bitlocker-nyckel för volymen m:<br> Sekvensen innehåller inga element.|Det här felet utlöses om Data Box-måldisken är offline. <br> Använd verktyget `diskmgmt.msc` till onlinediskar.|
|[Fel] Ett undantag uppstod: WMI-åtgärden misslyckades:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = Formatet för det angivna återställningslösenordet är ogiltigt. <br>BitLocker-återställningslösenord är 48 siffror. <br>Kontrollera att återställningslösenordet har rätt format och försök sedan igen.|Använda verktyget Data Box Disk Unlock för att först låsa upp diskarna och prova kommandot igen. Mer information finns i <li> [Låsa upp Data Box-disk för Windows-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Låsa upp Data Box-disk för Linux-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fel] Ett undantag uppstod: Det finns en DriveManifest.xml-fil på målenheten. <br> Detta anger att målenheten kanske har förberetts med en annan journalfil. <br>För att lägga till mer data till samma enhet använder du föregående journalfil. För att ta bort befintliga data och återanvända målenheten för ett nytt importjobb tar du bort DriveManifest.xml på enheten. Kör det här kommandot igen med en ny journalfil.| Det här felet tas emot när du försöker använda samma uppsättning enheter för flera importsessioner. <br> Använda endast en uppsättning enheter för endast en delnings- och kopieringssession.|
|[Fel] Ett undantag uppstod: CopySessionId importdata-sept-test-1 refererar till en tidigare session för kopiering och inte kan återanvändas för en ny session för kopiering.|Det här felet rapporteras när du försöker använda samma jobbnamn för ett nytt jobb som använts för ett tidigare slutfört jobb.<br> Tilldela det nya jobbet ett unikt namn.|
|[Info] Målfilens eller katalogens namn överskrider NTFS-längdbegränsningen. |Det här meddelandet rapporteras när målfilen bytte namn på grund av en lång filsökväg.<br> Ändra dispositionsalternativet i `config.json`-filen för att styra det här beteendet.|
|[Fel] Ett undantag uppstod: Felaktiga JSON-escape-sekvensen. |Det här meddelandet rapporteras när config.json har ett format som inte är giltigt. <br> Verifiera din `config.json` med hjälp av [JSONlint](https://jsonlint.com/) innan du sparar filen.|



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar Data Box-disk via Azure-portalen](data-box-portal-ui-admin.md).
