---
title: Övervaka Azure Backup skyddade arbets belastningar
description: I den här artikeln får du lära dig mer om övervaknings-och aviserings funktionerna för Azure Backup arbets belastningar med hjälp av Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 645bf701a8f8561b2cb4f19378036ada82c4bedd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054545"
---
# <a name="monitoring-azure-backup-workloads"></a>Övervaka Azure Backup arbets belastningar

Azure Backup innehåller flera säkerhets kopierings lösningar baserade på säkerhets kopierings kravet och infrastruktur sto pol Ogin (lokalt vs Azure). Alla säkerhets kopierings användare och administratörer bör se vad som händer på alla lösningar och kan förvänta sig att bli meddelad i viktiga scenarier. Den här artikeln beskriver de övervaknings-och aviserings funktioner som tillhandahålls av Azure Backups tjänsten.

## <a name="backup-jobs-in-recovery-services-vault"></a>Säkerhets kopierings jobb i Recovery Services valvet

Azure Backup tillhandahåller inbyggda övervaknings-och aviserings funktioner för arbets belastningar som skyddas av Azure Backup. I inställningarna för Recovery Services valvet tillhandahåller **övervaknings** avsnittet inbyggda jobb och aviseringar.

![Inbyggd RS-valv övervakning](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Jobb skapas när åtgärder som att konfigurera säkerhets kopiering, säkerhetskopiera, återställa, ta bort säkerhets kopiering och så vidare utförs.

Jobb från följande Azure Backup lösningar visas här:

- Säkerhetskopiering av virtuell Azure-dator
- Säkerhets kopiering av Azure-filer
- Azure-arbetsbelastningar, till exempel SQL och SAP HANA
- Microsoft Azure Recovery Services (MARS) Agent

Jobb från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte.

> [!NOTE]
> Azure-arbetsbelastningar som SQL och SAP HANA säkerhets kopieringar i virtuella Azure-datorer har ett stort antal säkerhets kopierings jobb. Logg säkerhets kopior kan till exempel köras under var 15: e minut. Så för sådana databas arbets belastningar visas bara åtgärder som utlöses av användaren. Schemalagda säkerhets kopierings åtgärder visas inte.

## <a name="backup-alerts-in-recovery-services-vault"></a>Säkerhets kopierings aviseringar i Recovery Services valv

Aviseringar är främst scenarier där användare meddelas så att de kan vidta relevanta åtgärder. Avsnittet om **säkerhets kopierings aviseringar** visar aviseringar som genererats av tjänsten Azure Backup. De här aviseringarna definieras av tjänsten och användaren kan inte anpassa skapandet av aviseringar.

### <a name="alert-scenarios"></a>Aviserings scenarier

Följande scenarier definieras av service som aviserings bara scenarier.

- Fel vid säkerhetskopiering/återställning
- Säkerhets kopieringen har slutförts med varningar för Microsoft Azure Recovery Services (MARS) Agent
- Stoppa skyddet med Behåll data/stoppa skydd med ta bort data

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Aviseringar från följande Azure Backup lösningar visas här

- Säkerhetskopior av virtuella Azure-datorer
- Säkerhetskopior av Azure-fil
- Azure-arbetsbelastnings säkerhets kopior som SQL, SAP HANA
- Microsoft Azure Recovery Services (MARS) Agent 

> [!NOTE]
> Aviseringar från System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) visas inte här.

### <a name="consolidated-alerts"></a>Konsoliderade aviseringar

För Azures säkerhets kopierings lösningar som SQL och SAP HANA kan logg säkerhets kopior skapas mycket ofta (upp till var 15: e minut enligt principen). Det är också möjligt att logg säkerhets kopierings felen ofta används (upp till var 15: e minut). I det här scenariot blir slutanvändaren överbelastad om en avisering aktive ras för varje förekomst av felet. Så en avisering skickas för den första förekomsten och om de senare felen beror på samma bakomliggande orsak genereras inga fler aviseringar. Den första aviseringen uppdateras med antalet haverier. Men om aviseringen inaktive ras av användaren utlöses nästa förekomst av en annan avisering och detta kommer att behandlas som den första aviseringen för den förekomsten. Detta är hur Azure Backup utför en aviserings konsolidering för SQL och SAP HANA säkerhets kopieringar.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Undantag när en avisering inte höjs

Det finns några undantag när en avisering inte utlöses vid ett fel. De är:

- Användaren avbröt ett jobb som körs uttryckligen
- Det går inte att utföra jobbet eftersom en annan säkerhets kopiering pågår (inget att agera här eftersom vi bara behöver vänta tills det tidigare jobbet har slutförts)
- Den virtuella datorns säkerhets kopierings jobb Miss lyckas eftersom den säkerhetskopierade virtuella Azure-datorn inte längre finns
- [Konsoliderade aviseringar](#consolidated-alerts)

Ovanstående undantag är utformade från förståelsen av att resultatet av dessa åtgärder (främst användare som utlöses) visas direkt på Portal/PS/CLI-klienter. Så användaren är omedelbart medveten och behöver ingen avisering.

### <a name="alert-types"></a>Aviseringstyper

Utifrån aviseringens allvarlighets grad kan aviseringar definieras i tre typer:

- **Kritiskt**: i princip skulle eventuella säkerhets kopierings-eller återställnings problem (schemalagda eller användare som utlöses) leda till generering av en avisering och visas som en kritisk varning och även destruktiva åtgärder som att ta bort säkerhets kopiering.
- **Varning**! om säkerhets kopieringen lyckas men med få varningar visas de som varnings aviseringar. Varnings aviseringar är för närvarande endast tillgängliga för Azure Backup Agent-säkerhetskopieringar.
- **Information**: för närvarande genereras ingen informations avisering av Azure Backup tjänsten.

## <a name="notification-for-backup-alerts"></a>Meddelande om säkerhets kopierings aviseringar

> [!NOTE]
> Konfiguration av aviseringar kan bara göras via Azure Portal. Stöd för PS/CLI/REST API/Azure Resource Manager-mall stöds inte.

När en avisering har Aktiver ATS meddelas användarna. Azure Backup tillhandahåller en inbyggd meddelande funktion via e-post. En kan ange enskilda e-postadresser eller distributions listor som ska meddelas när en avisering genereras. Du kan också välja om du vill bli meddelad om varje enskild avisering eller gruppera dem i en Tim sammandrag och sedan få ett meddelande.

![Inbyggt e-postmeddelande för RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

När ett meddelande har kon figurer ATS får du ett välkomst eller inledande e-postmeddelande. Detta bekräftar att Azure Backup kan skicka e-post till dessa adresser när en avisering utlöses.<br>

Om frekvensen har angetts till en Tim sammandrag och en avisering har genererats och lösts inom en timme, blir den inte en del av den kommande Tim sammandrag.

> [!NOTE]
>
> - Om en destruktiv åtgärd, till exempel **stoppa skydd med ta bort data** , görs, aktive ras en avisering och ett e-postmeddelande skickas till prenumerations ägare, administratörer och medadministratörer även om aviseringar inte har kon figurer ATS för tjänsten för återställning av tjänst.
> - Om du vill konfigurera meddelanden för lyckade jobb använder [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Inaktivera aviseringar

Om du vill inaktivera/lösa en aktiv avisering kan du klicka på listobjektet som motsvarar den avisering som du vill inaktivera. Då öppnas en skärm som visar detaljerad information om aviseringen, med knappen inaktive rad överst. Om du klickar på den här knappen ändras aviseringens status till inaktiv. Du kan också inaktivera en avisering genom att högerklicka på listobjektet som motsvarar aviseringen och välja "inaktive rad".

![Inaktive ring av RS Vault-avisering](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Nästa steg

[Övervaka arbets belastningar för Azure Backup med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
