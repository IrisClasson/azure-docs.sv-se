---
title: Vanliga frågor och svar om Azure Backup
description: 'Svar på vanliga frågor om: Azure Backup-funktioner inklusive Recovery Services-valv, vad du kan säkerhetskopiera, hur det fungerar, kryptering och gränser. '
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: aecad4273493cd573935c78cae51bd0f59461e2e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806976"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – vanliga frågor och svar
Den här artikeln innehåller vanliga frågor och svar om Azure Backup-tjänsten.

## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Finns det någon gräns för antalet valv som kan skapas i varje Azure-prenumeration?
Ja. Du kan skapa upp till 500 Recovery Services-valv per region som stöds av Azure Backup per prenumeration. Om du behöver fler valv skapar du ytterligare en prenumeration.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv?
Du kan registrera upp till 1 000 virtuella Azure-datorer per valv. Om du använder Microsoft Azure Backup-agenten kan registrera du upp till 50 MAB-agenter per valv. Och du kan registrera 50 MAB-servrar/DPM-servrar till ett valv.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Min organisation har ett valv, hur kan jag isolera data från olika servrar i valvet när du återställer data?
Server-data som du vill återställa tillsammans bör använda samma lösenfras när du konfigurerar säkerhetskopiering. Om du vill isolera återställning till en specifik server eller servrar kan du använda en lösenfras för den server eller endast servrar. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kan jag flytta mitt valv mellan prenumerationer?
Ja. Att flytta ett Recovery Services-valv finns det [artikel](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Kan jag flytta säkerhetskopieringsdata till ett annat valv?
Nej. Säkerhetskopierade data lagras i ett valv kan inte flyttas till ett annat valv.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Kan jag ändra från GRS till LRS när du har en säkerhetskopia?
Nej. Recovery Services-valvet kan bara ändra lagringsalternativ innan eventuella säkerhetskopior som har lagrats.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kan jag göra ett objekt på Återställ Objektnivå för virtuella datorer till ett Recovery Services-valv?
- Återställning på Objektnivå stöds för virtuella datorer i Azure backas upp av virtuell Azure-säkerhetskopiering. Mer information finns i [artikel](backup-azure-restore-files-from-vm.md)
- Återställning på Objektnivå stöds inte för onlineåterställningspunkter för lokala virtuella datorer som backas upp av Azure backup Server eller System Center DPM.


## <a name="azure-backup-agent"></a>Azure Backup-agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Var hittar jag vanliga frågor om Azure Backup-agenten för säkerhetskopiering av Azure virtuella datorer?

- Agenten som körs på virtuella Azure-datorer finns i det här [vanliga frågor och svar](backup-azure-vm-backup-faq.md).
- För den agent som används för att säkerhetskopiera Azure filmappar, finns i det här [vanliga frågor och svar](backup-azure-file-folder-backup-faq.md).


## <a name="general-backup"></a>Allmän säkerhetskopiering

### <a name="are-there-limits-on-backup-scheduling"></a>Finns det några begränsningar på schemaläggning av säkerhetskopiering?
Ja.
- Du kan säkerhetskopiera Windows Server eller Windows datorer upp till tre gånger per dag. Du kan ange schemaläggningsprincip för dagliga och veckovisa scheman.
- Du kan säkerhetskopiera DPM upp till två gånger per dag. Du kan ange schemaläggningsprincipen för dagliga, veckovisa, månatliga och årliga.
- Du säkerhetskopierar virtuella Azure-datorer en gång om dagen.

### <a name="what-operating-systems-are-supported-for-backup"></a>Vilka operativsystem som stöds för säkerhetskopiering?
Azure Backup stöder dessa operativsystem för att säkerhetskopiera filer och mappar och appar som skyddas av Azure Backup Server och DPM.

**OS** | **SKU** | **Detaljer**
--- | --- | ---
Arbetsstation | |
Windows 10 64-bitars | Enterprise, Pro, Home | Datorer ska köra den senaste services Pack och uppdateringar.
Windows 8.1 64-bitars | Enterprise, Pro | Datorer ska köra den senaste services Pack och uppdateringar.
Windows 8 64-bitars | Enterprise, Pro | Datorer ska köra den senaste services Pack och uppdateringar.
Windows 7 64-bitars | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Datorer ska köra den senaste services Pack och uppdateringar.
Server | |
Windows Server 2019 64-bitars | Standard, Datacenter, Essentials | Med de senaste service Pack/uppdateringarna.
Windows Server 2016 64-bitars | Standard, Datacenter, Essentials | Med de senaste service Pack/uppdateringarna.
Windows Server 2012 R2 64 bit | Standard, Datacenter, Foundation | Med de senaste service Pack/uppdateringarna.
Windows Server 2012 64 bit | Datacenter, Foundation, Standard | Med de senaste service Pack/uppdateringarna.
Windows Storage Server 2016 64 bit | Standard, Workgroup | Med de senaste service Pack/uppdateringarna.
Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential | Med de senaste service Pack/uppdateringarna.
Windows Storage Server 2012 64 bit | Standard, Workgroup | Med de senaste service Pack/uppdateringarna.
Windows Server 2008 R2 SP1 64-bitars | Standard, Enterprise, Datacenter, Foundation | Med de senaste uppdateringarna.
Windows Server 2008 64-bitars | Standard, Enterprise, Datacenter | Med de senaste uppdateringarna.

Azure Backup stöder för Azure VM Linux säkerhetskopior [lista över distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md), undantag för Core OS Linux- och 32-bitars operativsystem. Andra bring-your-own Linux-distributioner kan fungera så länge som den Virtuella datoragenten är tillgänglig på den virtuella datorn och har stöd för Python finns.


### <a name="are-there-size-limits-for-data-backup"></a>Finns det begränsningar för meddelandestorlek för säkerhetskopiering av data?
Storlekar gränser är följande:

OS/dator | Storleksgräns för datakälla
--- | ---
Windows 8 eller senare | 54 400 GB
Windows 7 |1 700 GB
Windows Server 2012 eller senare | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1 700 GB
Azure VM | 16 datadiskar<br/><br/> Upp till 4 095 GB som datadisk

### <a name="how-is-the-data-source-size-determined"></a>Vad är datakällans storlek bestäms?
Följande tabell beskriver hur datakällans storlek bestäms.

**Datakälla** | **Detaljer**
--- | ---
Volym |Mängden data som säkerhetskopieras från en enskild volym virtuella datorn som säkerhetskopieras.
SQL Server-databas |Storleken på enskild SQL-databasens storlek som säkerhetskopieras.
SharePoint | Summan av innehållet och konfigurationsdatabaserna i en SharePoint-servergrupp som säkerhetskopieras.
Exchange |Summan av alla Exchange-databaser i en Exchange-server som säkerhetskopieras.
BMR/systemtillstånd |Varje enskild kopia av BMR eller systemtillstånd på datorn som säkerhetskopieras.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Finns det en gräns för mängden data som säkerhetskopieras med ett Recovery Services-valv?
Det finns ingen gräns för mängden data som du kan säkerhetskopiera med Recovery Services-valvet.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Varför är mängden data som överförs till Recovery Services-valvet mindre än de data som valts för säkerhetskopiering?
Data som säkerhetskopieras från Azure Backup Agent, DPM, och Azure Backup Server komprimeras och krypteras innan de överförs. Vid komprimering och kryptering används kan data i valvet är 30 – 40% mindre.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kan jag ta bort enskilda filer från en återställningspunkt i valvet?
Azure Backup stöder inte, inte ta bort eller rensa enskilda objekt från lagrade säkerhetskopior.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Om jag avbryter ett säkerhetskopieringsjobb när den har startat överförda säkerhetskopieringsdata raderas?
Nej. Alla data som har överförts till valvet innan säkerhetskopieringen var avbröts finns kvar i valvet.

- Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen.
- Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet.
- Nästa säkerhetskopieringsjobb är en inkrementell säkerhetskopiering mot tidigare säkerhetskopierade data. Vid inkrementella säkerhetskopieringar överförs bara nya eller ändrade data, vilket innebär att bandbredden utnyttjas bättre.

Om du avbryter ett säkerhetskopieringsjobb för en virtuella Azure-dator ignoreras alla överförda data. Nästa säkerhetskopieringsjobb överför inkrementella data från det senaste lyckade säkerhetskopieringsjobbet.

## <a name="retention-and-recovery"></a>Kvarhållning och återställning

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Desamma principerna för kvarhållning för DPM och Windows-datorer utan DPM?
Ja, de båda har dagliga, veckovisa, månatliga och årliga bevarandeprinciper.

### <a name="can-i-customize-retention-policies"></a>Kan jag Anpassa bevarandeprinciper?
Ja, har du anpassa principer. Du kan till exempel konfigurera varje vecka och dagligen krav på datalagring, men inte varje år och månad.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kan jag använda olika tidpunkter för schemaläggning av säkerhetskopiering och kvarhållningsprinciper?
Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. Den här bilder visar exempelvis en bevarandeprincip för säkerhetskopieringar på 12: 00 och 18: 00.

![Schemalägga säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Tar det längre tid att återställa en äldre datapunkt om en säkerhetskopia sparas under en längre tid? <br/>
Nej. Är det dags att återställa den äldsta och den senaste punkten. Varje återställningspunkt beter sig som en fullständig punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?
Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter.

- De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa.
- Inkrementella kopior är *effektiv* men kräver att du återställer en kedja med data som påverkar återställningstiden

Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Detta säkerställer att ingående och utgående bandbredden används effektivt. Mängden datalagring och tiden som krävs för att återställa data sparas till ett minimum. Läs mer om [inkrementella säkerhetskopieringar](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Finns det någon gräns för antalet återställningspunkter som kan skapas?
Du kan skapa upp till 9999 återställningspunkter per skyddad instans. En skyddad instans är en dator, en server (fysisk eller virtuell) eller en arbetsbelastning som säkerhetskopierar till Azure.

- Läs mer om [säkerhetskopiering och kvarhållning](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hur många gånger kan jag återställa data som säkerhetskopieras till Azure?
Det finns ingen gräns för antalet återställningar från Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Betalar jag för den utgående trafiken från Azure när jag återställer data?
Nej. Recovery är kostnadsfritt och du debiteras inte för den utgående trafiken.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Vad händer om jag ändrar mitt princip för säkerhetskopiering?
När en ny princip tillämpas följs schemat för och kvarhållningen av den nya principen.

- Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen.
- Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

## <a name="encryption"></a>Kryptering

### <a name="is-the-data-sent-to-azure-encrypted"></a>Krypteras informationen som skickas till Azure?
Ja. Data krypteras på den lokala datorn med hjälp av AES256. Data skickas via en säker HTTPS-anslutning. De data som överförs i molnet skyddas av HTTPS-anslutning endast mellan tjänsten för lagring och återställning. iSCSI-protokollet skyddar de data som överförs mellan dator för återställning i tjänsten och användare. Säkra tunnlar används för att skydda iSCSI-kanalen.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Är säkerhetskopierade data i Azure också krypterade?
Ja. Data i Azure är krypterade i vila.

- För en lokal säkerhetskopiering tillhandahålls kryptering i vila med lösenfras som du anger när du säkerhetskopierar till Azure.
- Data är krypterade i vila med Storage Service Encryption (SSE) för virtuella Azure-datorer.

Microsoft dekrypterar aldrig dina säkerhetskopierade data.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Vad är den minsta längden på kryptering nyckeln används för att kryptera säkerhetskopierade data?
Krypteringsnyckeln ska innehålla minst 16 tecken när du använder Azure-säkerhetskopieringsagenten. För virtuella Azure-datorer finns det ingen begränsning av längden på de nycklar som används av Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Vad händer om jag tappar bort krypteringsnyckeln? Kan jag återställa data? Microsoft kan återställa dina data?
Nyckeln som används för att kryptera säkerhetskopierade data finns endast på webbplatsen. Microsoft sparar ingen kopia i Azure och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan inte Microsoft återställa dina säkerhetskopierade data.

## <a name="next-steps"></a>Nästa steg

Läs de andra vanliga frågor och svar:

- [Vanliga frågor](backup-azure-vm-backup-faq.md) om Virtuella Azure-säkerhetskopieringar.
- [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
