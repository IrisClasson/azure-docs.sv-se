---
title: Så här skapar du uppdaterings distributioner för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du schemalägger uppdaterings distributioner och granskar deras status.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 2a81376b284e0d1df84a69b969335c0e63999a00
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450689"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Så här distribuerar du uppdateringar och granskar resultat

Den här artikeln beskriver hur du schemalägger en uppdaterings distribution och granskar processen när distributionen är klar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

När du schemalägger en uppdaterings distribution skapas en [schema](../shared-resources/schedules.md) resurs som är länkad till **MicrosoftOMSComputers-** runbooken som hanterar uppdaterings distributionen på mål datorerna. Du måste schemalägga en distribution som följer ditt versions schema och service fönster för att installera uppdateringar. Du kan välja vilka uppdaterings typer som ska ingå i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

>[!NOTE]
>Om du tar bort schema resursen från Azure Portal eller använder PowerShell när du har skapat distributionen bryts den schemalagda uppdaterings distributionen och ett fel uppstår när du försöker konfigurera om schema resursen från portalen. Du kan bara ta bort schema resursen genom att ta bort motsvarande distributions schema.  

Så här schemalägger du en ny uppdaterings distribution:

1. I ditt Automation-konto går du till **uppdaterings hantering** under **uppdaterings hantering**och väljer sedan **Schemalägg uppdaterings distribution**.

2. Under **ny uppdaterings distribution**anger du ett unikt namn för distributionen i fältet **namn** .

3. Välj det operativ system som ska vara mål för uppdaterings distributionen.

4. I området **grupper att uppdatera (för hands version)** definierar du en fråga som kombinerar prenumeration, resurs grupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i distributionen. Läs mer i [använda dynamiska grupper med uppdateringshantering](update-mgmt-groups.md).

5. I området **datorer att uppdatera** väljer du en sparad sökning, en importerad grupp eller väljer **datorer** från List menyn och väljer enskilda datorer. Med det här alternativet kan du se beredskap för Log Analytics agenten för varje dator. Om du vill veta mer om olika metoder för att skapa dator grupper i Azure Monitor loggar, se [dator grupper i Azure Monitor loggar](../../azure-monitor/platform/computer-groups.md).

6. Använd området **uppdaterings klassificeringar** för att ange [uppdaterings klassificeringar](update-mgmt-view-update-assessments.md#work-with-update-classifications) för produkter. För varje produkt avmarkerar du alla uppdaterings klassificeringar som stöds, men de som ska inkluderas i uppdaterings distributionen.

7. Använd området **Inkludera/exkludera uppdateringar** om du vill välja vissa uppdateringar för distribution. Sidan inkludera/exkludera visar uppdateringar av KB-artikel-ID-nummer som ska tas med eller undantas.
    
   > [!IMPORTANT]
   > Kom ihåg att undantag åsidosätter inkluderingar. Om du till exempel definierar en undantags regel för `*` uppdateringshantering undantas alla korrigeringar eller paket från installationen. Undantagna uppdateringar visas fortfarande som saknade från datorerna. För Linux-datorer, om du inkluderar ett paket som har ett beroende paket som har uteslutits, installerar Uppdateringshantering inte huvud paketet.

   > [!NOTE]
   > Du kan inte ange uppdateringar som har ersatts av med i uppdaterings distributionen.

8. Välj **schema inställningar**. Starttiden är som standard 30 minuter efter den aktuella tiden. Du kan ange starttiden till helst från 10 minuter i framtiden.

9. Använd fältet **upprepning** för att ange om distributionen ska ske en gång eller använder ett återkommande schema. Välj sedan **OK**.

10. I området **före skript + efter skript (för hands version)** väljer du de skript som ska köras före och efter distributionen. Läs mer i [Hantera för-skript och post-skript](update-mgmt-pre-post-scripts.md).
    
11. Använd fältet **underhålls period (minuter)** för att ange hur lång tid som tillåts för uppdateringar som ska installeras. Tänk på följande när du anger en underhålls period:

    * Underhålls fönster styr hur många uppdateringar som är installerade.
    * Uppdateringshantering slutar inte att installera nya uppdateringar om slutet av en underhålls period närmar sig.
    * Uppdateringshantering avslutar inte pågående uppdateringar om underhålls perioden har överskridits.
    * Om underhålls perioden överskrids i Windows beror det ofta på att en service pack uppdatering tar lång tid att installera.

    > [!NOTE]
    > Om du vill undvika att uppdateringar tillämpas utanför en underhålls period på Ubuntu konfigurerar du om `Unattended-Upgrade` paketet för att inaktivera automatiska uppdateringar. Information om hur du konfigurerar paketet finns i [avsnittet automatiska uppdateringar i Ubuntu Server guide](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Använd fältet **alternativ för omstart** för att ange hur omstarter ska hanteras under distributionen. Följande alternativ är tillgängliga: 
    * Starta om vid behov (standard)
    * Starta alltid om
    * Starta aldrig om
    * Endast omstart; Det här alternativet installerar inte uppdateringar

    > [!NOTE]
    > De register nycklar som listas under [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka händelsen omstart om **alternativen för omstart** är inställd på **Starta aldrig om**.

13. När du är klar med att konfigurera distributions schemat väljer du **skapa**.

    ![Inställningsfönster för uppdateringsschema](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. Du kommer tillbaka till statusinstrumentpanelen. Välj **schemalagda uppdaterings distributioner** för att visa det distributions schema som du har skapat.

## <a name="schedule-an-update-deployment-programmatically"></a>Schemalägga en uppdaterings distribution program mässigt

Information om hur du skapar en uppdaterings distribution med REST API finns i [program uppdaterings konfiguration – skapa](/rest/api/automation/softwareupdateconfigurations/create).

Du kan använda en exempel-Runbook för att skapa en veckovis uppdaterings distribution. Mer information om denna Runbook finns i [skapa en veckovis uppdaterings distribution för en eller flera virtuella datorer i en resurs grupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Kontrol lera distributions status

När den schemalagda distributionen har startats kan du se dess status på fliken **uppdaterings distributioner** under **uppdaterings hantering**. Statusen är **Pågår** när distributionen körs. När distributionen har slutförts ändras statusen till **slutförd**. Om det uppstår fel med en eller flera uppdateringar i distributionen, **misslyckades statusen delvis**.

## <a name="view-results-of-a-completed-update-deployment"></a>Visa resultatet av en slutförd uppdaterings distribution

När distributionen är färdig kan du välja den för att se resultatet.

[![Uppdatera distributions status instrument panel för en angiven distribution](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Under **uppdaterings resultat**innehåller en sammanfattning det totala antalet uppdateringar och distributions resultat på de virtuella mål datorerna. Tabellen till höger visar en detaljerad analys av uppdateringarna och installations resultaten för var och en.

De tillgängliga värdena är:

* **Inget försök har gjorts** – uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid, baserat på den definierade varaktigheten för underhålls perioden.
* **Inte valt** – uppdateringen har inte valts för distribution.
* **Lyckades** – uppdateringen är klar.
* **Misslyckades** -uppdateringen misslyckades.

Välj **alla loggar** om du vill se alla logg poster som har skapats i distributionen.

Välj **utdata** om du vill se jobb strömmen för den Runbook som ansvarar för att hantera uppdaterings distributionen på de virtuella mål datorerna.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar aviseringar för att meddela dig om uppdaterings distributions resultat finns i [skapa aviseringar för uppdateringshantering](update-mgmt-configure-alerts.md).