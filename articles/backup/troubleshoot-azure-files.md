---
title: Felsöka säkerhetskopiering av Azure-filresurser
description: Den här artikeln kan användas som felsökningsinformation om det skulle uppstå problem när du skyddar dina Azure (filresurser).
ms.service: backup
author: dcurwin
ms.author: dacurwin
ms.date: 08/20/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 1182c7d4ac9a103e752a8cd0c392c5e57f1eebd0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637571"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Felsöka problem med att säkerhetskopiera Azure-filresurser
Du kan felsöka problem och fel vid användning av säkerhetskopiering av Azure-filresurser med hjälp av informationen i följande tabeller.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-filresurser i förhandsversionen
Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-konton stöds. Följande säkerhetskopieringsscenarier stöds inte för Azure-filresurser:
- Det finns ingen tillgänglig CLI som skyddar Azure Files med hjälp av Azure Backup.
- Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
- Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
- Förhindra att säkerhetskopior i Recovery Services-valvet oavsiktligt tas bort genom att använda [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot.
- Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.
- Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicksbilder som har tagits av Azure Backup när filresursen har tagits bort och förlorar därför alla återställningspunkter

Säkerhetskopiering av Azure-filresurser i lagringskonton med replikering med [zonredundant lagring](../storage/common/storage-redundancy-zrs.md) (ZRS) är för närvarande endast tillgängligt i USA, centrala (CUS), USA, östra (EUS), USA, östra 2 (EUS2), Europa, norra (NE), Sydostasien (SEA), Europa, västra (WE) och USA, västra 2 (WUS2).

## <a name="configuring-backup"></a>Konfigurera säkerhetskopiering
Följande tabell används för att konfigurera säkerhetskopieringen:

| Felmeddelanden | Lösningstips |
| ------------------ | ----------------------------- |
| Jag kunde inte hitta mitt lagringskonto när jag skulle konfigurera säkerhetskopiering för en Azure-filresurs | <ul><li>Vänta tills identifieringen har slutförts. <li>Kontrollera om någon filresurs i lagringskontot redan skyddas med ett annat Recovery Services-valv. **Obs!** Alla filresurser i ett lagringskonto kan endast skyddas med ett Recovery Services-valv. <li>Kontrollera att filresursen inte finns i något av de lagringskonton som inte stöds.|
| Fel i Portal anger att identifieringen av lagringskonton misslyckades. | Om din prenumeration är CSP-aktiverad (partner) kan du ignorera felet. Kontakta support om prenumerationen inte är CSP-aktiverad och dina lagringskonton inte kan identifieras.|
| Vald lagringskontoverifiering eller registrering misslyckades.| Försök utföra åtgärden på nytt. Kontakta support om problemet kvarstår.|
| Det gick inte att visa eller hitta filresurserna i det valda lagringskontot. | <ul><li> Kontrollera att lagringskontot finns i resursgruppen (och inte har tagits bort eller flyttats efter den senaste verifieringen/registreringen i valvet).<li>Kontrollera att filresursen du vill skydda inte har tagits bort. <li>Kontrollera att ditt lagringskonto är ett lagringskonto som stöder säkerhetskopiering av filresurser.<li>Kontrollera om filresursen redan skyddas i samma Recovery Services-valv.|
| Det går inte att konfigurera säkerhetskopiering av filresursen (eller konfigurera skyddsprincipen). | <ul><li>Försök igen och se om problemet kvarstår. <li> Kontrollera att filresursen du vill skydda inte har tagits bort. <li> Om du skyddar flera filresurser samtidigt och säkerhetskopieringen av vissa av filresurserna misslyckas, kan du prova igen att konfigurera säkerhetskopieringen för de filresurser som misslyckas. |
| Det går inte att ta bort Recovery Services-valvet efter att skyddet för en filresurs har tagits bort. | I Azure Portal öppnar du ditt valv > **Infrastruktur för säkerhetskopiering** > **Lagringskonton** och klickar på **Avregistrera** för att ta bort lagringskontot från Recovery Services-valvet.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Felmeddelanden för säkerhetskopierings- eller återställningsjobb

| Felmeddelanden | Lösningstips |
| -------------- | ----------------------------- |
| Åtgärden misslyckades eftersom det inte går att hitta filresursen. | Kontrollera att filresursen du vill skydda inte har tagits bort.|
| Lagringskontot hittades inte eller stöds inte. | <ul><li>Kontrollera att lagringskontot finns i resursgruppen, och inte har tagits bort eller flyttats från resursgruppen efter den senaste verifieringen. <li> Kontrollera att lagringskontot är ett lagringskonto som stöder säkerhetskopiering av filresurser.|
| Du har nått maxgränsen för ögonblicksbilder för den här filresursen. Du kan ta fler när de äldsta förfaller. | <ul><li> Det här felet kan uppstå när du skapar flera säkerhetskopieringar på begäran för en fil. <li> Det finns en gräns på 200 ögonblicksbilder per filresurs, inklusive de som tas av Azure Backup. Äldre schemalagda säkerhetskopieringar (eller ögonblicksbilder) rensas automatiskt. Säkerhetskopieringar på begäran (eller ögonblicksbilder) måste tas bort om den maximala gränsen har nåtts.<li> Ta bort säkerhetskopieringar på begäran (ögonblicksbilder av Azure-filresurser) från Azure Files-portalen. **Obs!** Återställningspunkterna går förlorade om du tar bort ögonblicksbilder som skapats av Azure Backup. |
| Säkerhetskopieringen eller återställningen av filresurser misslyckades på grund av begränsningar i lagringstjänsten. Detta kan bero på att lagringstjänsten är upptagen med andra begäranden för det angivna lagringskontot.| Försök igen efter en stund. |
| Återställningen misslyckades med ett felmeddelande om att det inte gick att hitta målfilresursen. | <ul><li>Kontrollera att det valda lagringskontot finns och att målfilresursen inte har tagits bort. <li> Kontrollera att ditt lagringskonto är ett lagringskonto som stöder säkerhetskopiering av filresurser. |
| Säkerhetskopierings- eller återställningsjobb misslyckas på grund av att lagringskontot är i ett låst tillstånd. | Ta bort låset för lagringskontot eller använd raderingslås i stället för läslås och försök igen. |
| Återställningen misslyckades på grund av att antalet filer som misslyckades överskred tröskelvärdet. | <ul><li> Orsaker till återställningsfel visas i en fil (sökvägen finns i Jobbinformation). Åtgärda felen och försök att köra återställningsåtgärden på nytt för bara de filer som misslyckades. <li> Vanliga orsaker till filåterställningsfel: <br/> – Kontrollera att filerna som misslyckades inte används för närvarande. <br/> – En katalog med samma namn som filen som misslyckades finns i den överordnade katalogen. |
| Återställningen misslyckades eftersom ingen fil kunde återställas. | <ul><li> Orsaker till återställningsfel visas i en fil (sökvägen finns i Jobbinformation). Åtgärda felen och försök att köra återställningsåtgärden på nytt för bara de filer som misslyckades. <li> Vanliga orsaker till filåterställningsfel: <br/> – Kontrollera att filerna som misslyckades inte används för närvarande. <br/> – En katalog med samma namn som filen som misslyckades finns i den överordnade katalogen. |
| Återställningen misslyckas eftersom en av filerna i källan inte finns. | <ul><li> De markerade objekten finns inte i informationen för återställningspunkten. Ange rätt fillista för att återställa filerna. <li> Ögonblicksbilden av filresursen som motsvarar återställningspunkten tas bort manuellt. Välj en annan återställningspunkt och försök att utföra återställningsåtgärden på nytt. |
| Ett återställningsjobb pågår till samma mål. | <ul><li>Säkerhetskopiering av filresurser stöder inte parallell återställning till samma målfilresurs. <li>Vänta tills den pågående återställningen har slutförts och prova sedan igen. Om du inte hittar ett återställningsjobb i Recovery Services-valvet kontrollerar du andra Recovery Services-valv i samma prenumeration. |
| Återställningsåtgärden misslyckades eftersom målfilresursen är full. | Öka storlekskvoten för målfilresursen så att återställningsdata får plats och prova sedan åtgärden igen. |
| Återställningsåtgärden misslyckades eftersom ett fel uppstod när de förberedande återställningsåtgärderna på filsynkroniseringstjänstens resurser associerades med målfilresursen. | Försök igen senare och kontakta Microsofts support om problemet kvarstår. |
| En eller flera filer kunde inte återställas. Mer information finns i listan med filer som misslyckats på den sökväg som anges ovan. | <ul> <li> Orsakerna till återställningsfel visas i filen (sökvägen finns i Jobbinformation). Åtgärda orsakerna och prova att köra återställningsåtgärden på nytt för bara de filer som misslyckades. <li> Vanliga orsaker till filåterställningsfel: <br/> – Kontrollera att de filer som misslyckas inte används för närvarande. <br/> – En katalog med samma namn som filen som misslyckades finns i den överordnade katalogen. |


## <a name="modify-policy"></a>Ändra princip
| Felmeddelanden | Lösningstips |
| ------------------ | ----------------------------- |
| En annan ”Konfigurera skydd”-åtgärd pågår för det här objektet. | Vänta tills den föregående ”Ändra princip”-åtgärden är klar och försök igen efter en stund.|
| En annan åtgärd körs på det valda objektet. | Vänta tills den andra pågående åtgärden är klar och försök igen efter en stund |


## <a name="see-also"></a>Se även
Mer information om hur du säkerhetskopierar Azure-filresurser finns i:
- [Säkerhetskopiera Azure-filresurser](backup-azure-files.md)
- [Säkerhetskopiera Azure-filresurser – Vanliga frågor och svar](backup-azure-files-faq.md)
