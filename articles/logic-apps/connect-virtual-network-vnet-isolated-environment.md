---
title: Ansluta till virtuella Azure-nätverk från Azure Logic Apps via en integration service-miljö (ISE)
description: Skapa en integration service-miljö (ISE) så att logic apps och integrationskonton kan komma åt Azure-nätverk (Vnet), utan att förlora privata och isolerade från offentligt eller ”global” Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: bd1f06c93a75673f86f0c52f78cad8a60f7a1a1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65961450"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till Azure-nätverk från Azure Logic Apps med hjälp av en integration service-miljö (ISE)

För scenarier där dina logic apps och integrationskonton behöver åtkomst till en [Azure-nätverk](../virtual-network/virtual-networks-overview.md), skapa en [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). En ISE är en privat och isolerad miljö som använder dedikerade lagringsutrymmen och andra resurser som är åtskilda från den offentliga eller ”global” Logic Apps-tjänsten. Den här separationen minskar också påverkas som andra Azure-klienter kan ha på din apps prestanda. Din ISE *in* till till din Azure-nätverk, som sedan distribuerar Logic Apps-tjänsten till ditt virtuella nätverk. När du skapar ett logic app eller varje konto, Välj den här ISE som deras plats. Ditt logic app eller varje konto kan sedan direkt åtkomst till resurser, till exempel virtuella datorer (VM), servrar, system och tjänster i ditt virtuella nätverk.

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Den här artikeln visar hur du utför dessa uppgifter:

* Kontrollera att alla nödvändiga portar i ett virtuellt nätverk är öppen så att trafik kan skickas genom din integration service-environment (ISE) över undernät i det virtuella nätverket.

* Skapa din integration service-environment (ISE).

* Skapa en logikapp som kan köras i din ISE.

* Skapa ett integrationskonto för logikappar i din ISE.

Läs mer om integreringstjänstmiljöer [åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

  > [!IMPORTANT]
  > Logic apps, inbyggda utlösare, inbyggda åtgärder och kopplingar som körs i ISE-användning av en prisplanen skiljer sig från förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md).

* En [Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du inte har ett virtuellt nätverk kan du lära dig hur du [skapa en Azure-nätverk](../virtual-network/quick-create-portal.md). 

  * Det virtuella nätverket måste ha fyra *tom* undernät för att distribuera och skapa resurser i din ISE. Du kan skapa de här undernäten i förväg eller vänta tills du har skapat din ISE där du kan skapa undernät på samma gång. Läs mer om [undernät krav](#create-subnet). 
  
    > [!NOTE]
    > Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), som innehåller en privat anslutning till Microsofts molntjänster, måste du [skapar en routningstabell](../virtual-network/manage-route-table.md) som har följande dirigera och länka tabellen med varje undernät som används av din ISE:
    > 
    > **Namnet**: <*route-name*><br>
    > **Adressprefixet**: 0.0.0.0/0<br>
    > **Nästa hopp**: Internet

  * Se till att det virtuella nätverket [tillgängliggör dessa portar](#ports) så att din ISE fungerar och är tillgänglig.

* Om du vill använda anpassade DNS-servrar för Azure-nätverk, [konfigurera dessa servrar genom att följa dessa steg](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) innan du distribuerar din ISE till ditt virtuella nätverk. I annat fall behöva varje gång du ändrar din DNS-server du också starta om din ISE som är en funktion som är tillgängliga i offentlig förhandsversion i ISE.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="check-network-ports"></a>Kontrollera nätverksportar

När du använder en integration service-miljö (ISE) med ett virtuellt nätverk ett vanligt installationsproblem har en eller flera blockerade portar. De kopplingar som du använder för att skapa anslutningar mellan dina ISE och målsystemet kan också ha sina egna krav på nätverksportar. Till exempel om du kommunicera med en FTP-system med hjälp av FTP-anslutningen kontrollerar du den port som du använder på att FTP-system, till exempel port 21 för att skicka kommandon och är tillgänglig.

Om du vill styra trafiken över det virtuella nätverkets undernät där du distribuerar ISE kan du ställa in [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) av [filtrerar nätverkstrafik mellan undernät](../virtual-network/tutorial-filter-network-traffic.md). Din ISE måste dock ha specifika portar öppna på det virtuella nätverket som använder nätverkssäkerhetsgrupper. På så sätt kan din ISE förblir tillgängligt och kan fungera korrekt så att du inte förlorar åtkomsten till dina ISE. Annars, om alla nödvändiga portar är otillgängliga din ISE slutar att fungera.

Dessa tabeller beskrivs portarna i ditt virtuella nätverk som använder din ISE och där de portarna som får användas. Den [Resource Manager-tjänsttaggar](../virtual-network/security-overview.md#service-tags) representerar en grupp med IP-adressprefix som syfte att minska komplexiteten när du skapar säkerhetsregler.

> [!IMPORTANT]
> För intern kommunikation inom dina undernät kräver ISE att du öppnar alla portar i dessa undernät.

| Syfte | Direction | Portar | Källtjänsttagg | Måltjänsttagg | Anteckningar |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Kommunikation från Azure Logic Apps | Utgående | 80 & 443 | VirtualNetwork | Internet | Porten är beroende av den externa tjänsten som Logic Apps-tjänsten kommunicerar |
| Azure Active Directory | Utgående | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Beroende av Azure Storage | Utgående | 80 & 443 | VirtualNetwork | Storage | |
| Intersubnet kommunikation | Inkommande och utgående | 80 & 443 | VirtualNetwork | VirtualNetwork | För kommunikation mellan undernät |
| Kommunikation till Azure Logic Apps | Inkommande | 443 | Internet  | VirtualNetwork | IP-adressen för datorn eller tjänsten som anropar en begäransutlösare eller en webhook som finns i din logikapp. Stänga eller blockerar den här porten förhindrar att HTTP-anrop till logikappar med frågeutlösare.  |
| Historik för logikappskörningen | Inkommande | 443 | Internet  | VirtualNetwork | IP-adressen för den dator där du se logikappen körningshistorik. Även om stänga eller blockerar den här porten inte hindra dig från att visa körningshistoriken, du kan inte visa indata och utdata för varje steg i som körningshistorik. |
| Anslutningshanteringen | Utgående | 443 | VirtualNetwork  | Internet | |
| Publicera diagnostikloggar och mått | Utgående | 443 | VirtualNetwork  | AzureMonitor | |
| Kommunikation från Azure Traffic Manager | Inkommande | 443 | AzureTrafficManager | VirtualNetwork | |
| Logikappdesigner – dynamiska egenskaper | Inkommande | 454 | Internet  | VirtualNetwork | Begäranden som kommer från Logic Apps [åt slutpunkten för inkommande IP-adresser i den regionen](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Service Management-appberoendet | Inkommande | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Connector-distribution | Inkommande | 454 & 3443 | Internet  | VirtualNetwork | Krävs för att distribuera och uppdatera kopplingar. Stänga eller blockerar den här porten orsakar ISE distributioner misslyckas och förhindrar anslutningen uppdateringar och korrigeringar. |
| Azure SQL-beroenden | Utgående | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Utgående | 1886 | VirtualNetwork | Internet | För att publicera hälsostatus till Resource Health |
| API Management - hanteringsslutpunkt | Inkommande | 3443 | APIManagement  | VirtualNetwork | |
| Beroende från loggen till Event Hub-principen och övervakningsagent | Utgående | 5672 | VirtualNetwork  | EventHub | |
| Få åtkomst till Azure Cache för Redis-instanser mellan Rollinstanser | Inkommande <br>Utgående | 6379-6383 | VirtualNetwork  | VirtualNetwork | Dessutom för ISE för att arbeta med Azure Cache för Redis, måste du öppna dessa [utgående och inkommande portar som beskrivs i Azure Cache för Redis vanliga frågor och svar](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Inkommande | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

Följ dessa steg för att skapa din integration service-environment (ISE):

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **skapa en resurs**.
I sökrutan anger du ”integreringstjänstmiljön” som filter.

   ![Skapa ny resurs](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I fönstret Integreringstjänstmiljön skapas väljer **skapa**.

   ![Välj ”Skapa”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Ange följande information för din miljö och välj sedan **granska + skapa**, till exempel:

   ![Ange information om miljön](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Azure-prenumeration för din miljö |
   | **Resursgrupp** | Ja | <*Azure-resource-group-name*> | Azure-resursgrupp där du vill skapa en miljö |
   | **Namn på integreringstjänstmiljö** | Ja | <*environment-name*> | Namn för att ge din miljö |
   | **Plats** | Ja | <*Azure-datacenter-region*> | Azure-datacenterregion var du vill distribuera din miljö |
   | **Ytterligare kapacitet** | Ja | 0 och 10 | Antal ytterligare enheter för den här ISE-resursen. Om du vill lägga till kapacitet när du har skapat, se [lägga till ISE kapacitet](#add-capacity). |
   | **Virtuellt nätverk** | Ja | <*Azure-virtual-network-name*> | Azure-nätverket där du vill att mata in din miljö så att logic apps i denna miljö kan komma åt det virtuella nätverket. Om du inte har ett nätverk, [först skapa ett Azure-nätverk](../virtual-network/quick-create-portal.md). <p>**Viktiga**: Du kan *endast* utföra den här inmatning när du skapar din ISE. |
   | **Undernät** | Ja | <*subnet-resource-list*> | En ISE kräver fyra *tom* undernät för att skapa resurser i din miljö. Att skapa varje undernät, [att följa stegen i den här tabellen](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   För att skapa resurser i din miljö, måste din ISE fyra *tom* undernät som inte delegeras till alla tjänster. 
   Du *kan* ändra undernätsadresserna när du har skapat din miljö. Varje undernät måste uppfylla följande kriterier:

   * Har ett namn som börjar med ett alfabetiskt tecken eller ett understreck och inte har följande tecken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Använder den [Classless Inter-Domain Routing CIDR-format](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) och en klass B-adressutrymmet.

   * Använder minst en `/27` i adressen utrymme eftersom varje undernät måste ha 32 adresser som den *minsta*. Exempel:

     * `10.0.0.0/27` har 32 adresser eftersom 2<sup>(32-27)</sup> är 2<sup>5</sup> eller 32.

     * `10.0.0.0/24` har 256 adresser eftersom 2<sup>(32-24)</sup> är 2<sup>8</sup> eller 256.

     * `10.0.0.0/28` har bara 16 adresser och är för liten eftersom 2<sup>(32-28)</sup> är 2<sup>4</sup> eller 16.

     Mer information om hur du beräknar adresser finns [IPv4 CIDR-block som](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), Kom ihåg att [skapar en routningstabell](../virtual-network/manage-route-table.md) som har följande dirigera och länka tabellen med varje undernät som används av din ISE:

     **Namnet**: <*route-name*><br>
     **Adressprefixet**: 0.0.0.0/0<br>
     **Nästa hopp**: Internet

   1. Under den **undernät** väljer **hantera undernätskonfiguration**.

      ![Hantera undernätskonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. På den **undernät** fönstret Välj **undernät**.

      ![Lägga till undernät](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. På den **Lägg till undernät** fönstret anger den här informationen.

      * **Namn på**: Namn för ditt undernät
      * **Adressintervall (CIDR-block)** : Intervall för ditt undernät i det virtuella nätverket och i CIDR-format

      ![Lägg till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. När du är klar väljer du **OK**.

   1. Upprepa dessa steg för tre flera undernät.

      > [!NOTE]
      > Om de undernät som du försöker skapa är ogiltiga, Azure-portalen visas ett felmeddelande, men blockerar inte förloppet.

1. När Azure verifierar har ISE-information, väljer **skapa**, till exempel:

   ![Välj ”Skapa” efter lyckad validering](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, men den här processen *kan* ta upp till två timmar innan du avslutar. 
   Välj meddelandeikonen, vilket öppnar meddelandefönstret för att kontrollera Distributionsstatus i din Azure verktygsfältet.

   ![Kontrollera Distributionsstatus för](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen har slutförts visas det här meddelandet i Azure:

   ![Distributionen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Annars Följ instruktioner för Azure portal för felsökning av distribution.

   > [!NOTE]
   > Om distributionen misslyckas eller om du ta bort din ISE Azure kan ta upp till en timme innan du publicerar dina undernät. Den här fördröjningen innebär innebär att du kan behöva vänta innan du återanvänder dessa undernät i ett annat ISE. 
   >
   > Om du tar bort det virtuella nätverket Azure tar vanligtvis upp till två timmar innan du släpper upp dina undernät, men den här åtgärden kan ta längre tid. 
   > När du tar bort virtuella nätverk, se till att inga resurser är fortfarande ansluten. Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Om du vill visa din miljö, Välj **gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.  

Läs mer om hur du skapar undernät, [lägga till ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Skapa logikapp – ISE

Att skapa logikappar som körs i din integration service-environment (ISE) [skapa logikappar som vanligt](../logic-apps/quickstart-create-first-logic-app-workflow.md) utom när du ställer in den **plats** egenskapen, Välj din ISE från den  **Integreringstjänstmiljöer** avsnittet, till exempel:

  ![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

För skillnader i hur utlösare och åtgärder fungerar och hur de är märkta när du använder en ISE jämfört med globala Logic Apps-tjänsten finns i [isolerade jämfört med globala i ISE-översikt](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Skapa integrationskonto – ISE

Om du vill använda ett konto för integrering med logic apps i en integreringstjänstmiljö (ISE) som integrationskontot måste använda den *samma miljö* som logic apps. Logic apps i en ISE kan referera till integrationskonton i samma ISE.

Du skapar ett integrationskonto som använder en ISE [skapa ditt integrationskonto som vanligt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) utom när du ställer in den **plats** egenskapen, Välj din ISE från den **integrering tjänsten miljöer** avsnittet, till exempel:

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Lägga till ISE-kapacitet

Basenheten ISE har fast kapacitet, så om du behöver större dataflöde kan du lägga till fler skalningsenheter. Du kan skala automatiskt baserat på prestandamått eller baserat på ett antal ytterligare bearbetning-enheter. Om du väljer automatisk skalning baserat på mått du väljer bland olika kriterier och anger tröskelvärdet villkor i syfte att uppfylla dessa villkor.

1. Hitta din ISE i Azure-portalen.

1. Om du vill granska användning och prestandamått för din ISE på din ISE Huvudmeny väljer **översikt**.

   ![Visa användning för ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Du ställer in automatisk skalning och under **inställningar**väljer **skala ut**. På den **konfigurera** fliken **aktivera autoskalning**.

   ![Aktivera automatisk skalning](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. För **namn på Autoskalningsinställning**, ange ett namn för din inställning för.

1. I den **standard** väljer du antingen **skala baserat på ett mått** eller **skala till ett specifikt instansantal**.

   * Om du väljer instans-baserade måste du ange hur många bearbetningsenheter mellan 0 och 10 portintervallet.

   * Om du väljer måttbaserad, gör du följande:

     1. I den **regler** väljer **lägga till en regel**.

     1. På den **skalningsregeln** fönstret har skapat dina villkor och åtgärden ska vidtas när regeln utlöses.

     1. När du är klar väljer **Lägg till**.

1. När du är klar med inställningarna för automatisk skalning kan du spara ändringarna.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
