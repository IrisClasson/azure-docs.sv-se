---
title: Kör extraherade data för flera klienter av analyser | Microsoft Docs
description: Flera klienter analytics-frågor med hjälp av data som extraheras från flera Azure SQL Database-databaser i en enda klient-app.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 6115d7f70c2c75898b18a27af298a44ca87ca1bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240872"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Flera klienter analytics med hjälp av extraherade data - enda klient
 
I den här självstudien får gå igenom ett scenario med fullständig analytics för en enskild klient-implementering. Scenariot visar hur analytics kan aktivera företag att fatta smarta beslut. Med data som extraheras från varje klientdatabas kan använda du analytics och få insikter om klient beteendet, inklusive deras användning av Wingtip biljetter SaaS-exempelprogram. Det här scenariot omfattar tre steg: 

1.  **Extrahera** data från varje klientdatabas och **belastningen** till en analytics-arkivet.
2.  **Omvandla de extraherade data** för bearbetning av trafikanalys.
3.  Använd **affärsinformation** verktyg för att rita ut användbara insikter och hjälper beslutsfattande. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa analytics lagra för att extrahera data till klienten.
> - Använd elastiska jobb för att extrahera data från varje klientdatabas till arkivet analytics.
> - Optimera extraherade data (reorganize i ett star-schema).
> - Fråga databas för analys.
> - Använda Power BI för datavisualisering av för att markera trender i klientdata och ge rekommendation för förbättringarna.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline klient analytics mönster

Delade SaaS-program har vanligtvis mängder med klientdata lagras i molnet. Dessa data ger insikter om åtgärden omfattande källan och användning av programmet och beteendet för dina klienter. Dessa insikter hjälper funktionsutveckling, förbättringar av användbarhet och andra investeringar i appen och plattformen.

Det är enkelt att komma åt data för alla klienter när alla data är i en databas för flera innehavare. Men åtkomsten är mer komplext när du har distribuerat tusentals databaser. Ett sätt att fördjupad insikt i hantering komplexiteten och för att minimera effekten av analysfrågor på transaktionsdata är att extrahera data till ett syfte som utformats för analytics-databasen eller data warehouse.

Den här kursen behandlas en fullständig analytics-scenario för Wingtip biljetter SaaS-program. Först *elastiska jobb* används för att extrahera data från varje klientdatabas och läser in dem till mellanlagringstabellerna i ett Arkiv för analys. Arkivet analytics kan antingen vara en SQL-databas eller ett SQL Data Warehouse. För storskaliga dataextrahering [Azure Data Factory](../data-factory/introduction.md) rekommenderas.

Därefter sammanställda data omvandlas till en uppsättning [star-schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna består av en central faktatabell plus relaterade dimensionstabeller.  För Wingtip biljetter:

- Den centrala faktatabellen i star-schema innehåller data för biljetten.
- Dimensionstabellerna beskriver platser, händelser, kunder och köpa datum.

Central fakta- och dimensionstabeller tabellerna aktivera tillsammans effektiv analytisk bearbetning. I följande bild visas ett star-schema som används i den här självstudien:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Slutligen arkivet analytics efterfrågas med **PowerBI** att lyfta fram insikter om klient beteende och deras användning av programmet Wingtip biljetter. Du kör frågor som:
 
- Visa relativa popularitet för varje plats
- Markera mönster i efter olika händelser
- Visa relativa kan användas i olika platser i sälja ut sina händelse

Förstå hur varje klient använder tjänsten används för att utforska alternativ för varumärket tjänsten och förbättra tjänsten för att hjälpa klienter att lyckas. Den här självstudiekursen innehåller grundläggande exempel på typer av insikter som kan finns i klientdata.

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Nödvändiga komponenter

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

- Wingtip biljetter SaaS databas Per klient-programmet har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
- Wingtip biljetter SaaS databas Per klient skript och programmet [källkod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) laddas ned från GitHub. Se Hämta instruktioner. Se till att *avblockera zip-filen* innan du extrahera dess innehåll. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.
- Power BI Desktop har installerats. [Hämta Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Batch med ytterligare klienter har etablerats kan du läsa den [ **etablera klienter självstudien**](saas-dbpertenant-provision-and-catalog.md).
- Ett jobbkonto och jobbkonto-databasen har skapats. Se instruktionerna i den [ **schemat självstudiekurs om enhetshantering**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Skapa data för presentationen

I de här självstudierna utförs analysen på försäljningsdata för biljett. I det aktuella steget genererar du biljett data för alla klienter.  Dessa data extraheras senare för analys. *Se till att du har etablerat batch med klienter enligt beskrivningen ovan, så att du har en meningsfull mängd data*. En tillräckligt stor mängd data kan exponera en mängd olika biljett inköpsmönster.

1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, och ange följande värde:
    - **$DemoScenario** = **1** köp biljetter för evenemang på alla platser
2. Tryck på **F5** att köra skriptet och skapa biljettköpshistorik för varje händelse i varje plats.  Skriptet körs i flera minuter att generera tiotusentals biljetter.

### <a name="deploy-the-analytics-store"></a>Distribuera arkivet analytics
Det finns ofta ett stort antal transaktionella databaser som tillsammans innehåller alla klientdata. Du måste slå samman data för klientorganisationen från många transaktionella databaser till en analytics store. Aggregeringen kan effektivt fråga data. I de här självstudierna används en Azure SQL Database-databas för att lagra sammanställda data.

I följande steg ska du distribuera analytics store, som kallas **tenantanalytics**. Du kan också distribuera fördefinierade tabeller som är ifyllda senare i självstudien:
1. I PowerShell ISE öppnar *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Ställ in variabeln $DemoScenario i skriptet så att den matchar ditt val av analytics store:
    - Om du vill använda SQL-databas utan columnstore **$DemoScenario** = **2**
    - Om du vill använda SQL-databas med kolumnen store **$DemoScenario** = **3**  
3. Tryck på **F5** att köra demo-skriptet (som anropar den *distribuera TenantAnalytics\<XX > .ps1* skript) som skapar klient analytics store. 

Nu när du har distribuerat programmet och fyllt med intressanta klientdata, använda [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) att ansluta **tenants1-dpt -&lt;användaren&gt;**  och **catalog-dpt -&lt;användaren&gt;**  servrar med hjälp av inloggning = *developer*, lösenord = *P\@ssword1*. Se den [inledande självstudien](saas-dbpertenant-wingtip-app-overview.md) för mer information.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

I Object Explorer kan du utföra följande steg:

1. Expandera den *tenants1-dpt -&lt;användaren&gt;*  server.
2. Expandera noden databaser och se en lista över klientdatabaser.
3. Expandera den *catalog-dpt -&lt;användaren&gt;*  server.
4. Kontrollera att du ser arkivet analytics och jobbkonto-databasen.

Se följande databasen i SSMS Object Explorer expanderar noden analytics store:

- Tabeller **TicketsRawData** och **EventsRawData** håller extraherade rådata från klientdatabaserna.
- Star-schema-tabeller är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, och **dim_Dates** .
- Den lagrade proceduren används för att fylla i tabellerna star-schema från rådata-tabeller.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrahering av data 

### <a name="create-target-groups"></a>Skapa målgrupper 

Kontrollera att du har distribuerat den jobb konto och jobbkonto-databasen innan du fortsätter. I nästa uppsättning steg används elastiska jobb för att extrahera data från varje klientdatabas och för att lagra data i arkivet för analys. Sedan det andra jobbet shreds data och lagrar den till tabeller i star-schema. Dessa två jobb körs mot två olika målgrupper, nämligen **TenantGroup** och **AnalyticsGroup**. Extrahera jobbet körs mot TenantGroup som innehåller alla klientdatabaser. Förstöring jobbet körs mot AnalyticsGroup som innehåller bara arkivet analytics. Skapa målgrupper med hjälp av följande steg:

1. I SSMS, ansluta till den **jobaccount** databasen i katalogen-dpt -&lt;användaren&gt;.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Ändra den @User variabeln överst i skriptet ersätter `<User>` med det användarvärde som används när du distribuerade Wingtip SaaS-appen.
4. Tryck på **F5** att köra skriptet som skapar två målgrupper.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahera rådata från alla klienter

Omfattande dataändringar uppstå oftare för *biljetter och kunden* data än för *händelse och jurisdiktionsort* data. Därför kan du extrahera biljetter och kundens data separat och oftare än du extrahera händelse-och plats. I det här avsnittet definierar och schemalägga jobb för två separata:

- Extrahera biljetter och kundens data.
- Extrahera händelse-och plats.

Varje jobb extraherar data och skickar det till arkivet analytics. Ett separat jobb shreds det extraherade data i analytics star-schema.

1. I SSMS, ansluta till den **jobaccount** databasen i katalogen-dpt -&lt;användaren&gt; server.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Ändra @User överst i skriptet och Ersätt `<User>` med användarnamnet som används när du distribuerade Wingtip SaaS-appen 
4. Tryck på F5 för att köra skriptet som skapar och kör jobb som hämtar data för biljetter och kunder från varje klientdatabas. Jobbet sparar data till arkivet analytics.
5. Fråga tabellen TicketsRawData i tenantanalytics databasen, så att tabellen fylls med biljetter information från alla klienter.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Upprepa föregående steg, förutom den här gången Ersätt **\ExtractTickets.sql** med **\ExtractVenuesEvents.sql** i steg 2.

Att köra jobbet fyller tabellen EventsRawData i arkivet analytics med nya händelser och platser information från alla klienter. 

## <a name="data-reorganization"></a>Data om

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Hjälp extraherade data för att fylla i star-schema-tabeller

Nästa steg är att bevisa extraherade rådata till en uppsättning tabeller som är optimerade för analysfrågor. Ett star-schema används. En central faktatabell innehåller enskild biljett försäljning poster. Andra tabeller fylls med relaterade data om platser, händelser och kunder. Och det finns tid dimensionstabeller. 

I det här avsnittet av självstudiekursen ska du definiera och köra ett jobb som sammanfogar extraherade rådata med data i tabellerna star-schema. När merge-jobbet är klart att rådata har tagits bort, lämnar tabellerna som är redo att fyllas med nästa klientdata extrahera jobbet.

1. I SSMS, ansluta till den **jobaccount** databasen i katalogen-dpt -&lt;användaren&gt;.
2. Öppna i SSMS, *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Tryck på **F5** att köra skriptet för att definiera ett jobb som anropar sp_ShredRawExtractedData lagrad procedur i arkivet för analys.
4. Tillåt tillräckligt med tid för att jobbet ska köras.
    - Kontrollera den **livscykel** kolumnen jobs.jobs_execution tabellen för jobbets status. Kontrollera att jobbet **lyckades** innan du fortsätter. En lyckad körning visar data som liknar följande diagram:

![fragmentering](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Datautforskning

### <a name="visualize-tenant-data"></a>Visualisera data för klientorganisationen

Data i tabellen star-schema innehåller alla biljett försäljningsdata behövs för din analys. Om du vill göra det lättare att se trender i stora datamängder, måste du visualisera den grafiskt.  I det här avsnittet får du lära dig hur du använder **Power BI** att manipulera och visualisera data för klientorganisationen du har extraherats och ordnas.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI desktop.
2. Välj Home-menyfliksområdet och **hämta Data**, och välj **mer...** på menyn.
3. I den **hämta Data** fönstret, väljer Azure SQL Database.
4. Ange namnet på servern i inloggningsfönstret databasen (catalog-dpt -&lt;användaren&gt;. database.windows.net). Välj **Import** för **läge för dataanslutning**, och klicka sedan på OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **databasen** i den vänstra rutan, ange användarnamn = *developer*, och ange lösenord = *P\@ssword1*. Klicka på **Anslut**.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I den **Navigator** rutan under analysdatabas, Välj tabellerna som star-schema: fact_Tickets dim_Events, dim_Venues, dim_Customers och dim_Dates. Välj sedan **belastningen**. 

Grattis! Data har lästs in på Power BI. Nu kan du börja utforska intressanta visualiseringar för att få insikter om dina klienter. Sedan gå igenom hur analytics gör det möjligt att tillhandahålla datadrivna rekommendationer till Wingtip biljetter business-teamet. Rekommendationer kan hjälpa till att optimera företag modell och kundernas upplevelse.

Börja med att analysera försäljningsdata för biljett för att se variationen i användning på platser. Välj följande alternativ i Power BI för att rita ett stapeldiagram av det totala antalet ärenden som säljs av varje plats. Dina resultat kan skilja sig på grund av slumpmässiga variation i biljett generator.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Föregående området bekräftar att Antal biljetter som säljs av varje lokal varierar. Platser som säljer mer biljetter använder tjänsten tyngre än platser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resursallokering efter behov för annan klient.

Du kan ytterligare analysera data om du vill se hur biljettförsäljningar variera över tid. Välj följande alternativ i Power BI för att rita det totala antalet ärenden som säljs varje dag under en period på 60 dagar.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Föregående diagram visar den biljett försäljning topp för vissa platser. Dessa toppar förstärka idé att vissa platser kan förbruka systemresurser oproportionerligt. Än så länge finns det inget uppenbart mönster i när toppar som inträffar.

Nästa som du vill undersöka betydelsen av dessa dagar för försäljning med hög belastning. När uppstår dessa toppar när biljetter? Välj följande alternativ i Power BI om du vill rita biljetter som säljs per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Föregående diagram visar att vissa platser sälja biljetter mycket på den första dagen i försäljning. När biljetter går vid försäljning på dessa platser, verkar det vara får gott om tid. Den här burst av aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan se data igen för att se om det här får gott om tid gäller för alla händelser som är värd för dessa platser. I föregående områden observerade du att Contosos Konserthall säljer mycket biljetter och att Contoso har också en topp i biljettförsäljningar på vissa dagar. Experimentera med Power BI-alternativ för att rita kumulativ biljettförsäljningar för Contosos Konserthall fokusera på försäljningen trender för var och en av händelserna. Följ samma mönster för försäljningen i alla händelser?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Föregående området för Contosos Konserthall visar att den får gott om tid inte sker för alla händelser. Experimentera med filteralternativ för att se försäljning trender för andra platser.

Insikter om biljett sälja mönster kan leda Wingtip biljetter för att optimera sin affärsmodell. I stället för att debitera alla klienter lika ska kanske Wingtip införa tjänstnivåer med olika storlekar. Större platser som behöver för att sälja mer biljetter per dag skulle kunna erbjudas en högre nivå med ett högre servicenivåavtal (SLA). Dessa platser kan ha sina databaser som placerats i pool med högre resursgränser för per databas. Varje tjänstnivå kan ha en försäljning per timme allokering, med ytterligare avgifter som debiteras för som överskrider allokeringen. Större platser som har periodiska toppar för försäljning skulle ha nytta av de högsta nivåerna och Wingtip biljetter kan tjäna pengar på deras tjänst mer effektivt.

Vissa kunder Wingtip biljetter börjar under tiden kan klaga att de behöva kämpa för att sälja tillräckligt med biljetter för att motivera kostnaden för tjänsten. Kanske i dessa insikter finns en möjlighet att öka efter presterar som förväntat platser. Högre försäljning ökar upplevd värdet av tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet namnet **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Välj följande alternativ för visualisering att rita de procentandel biljetter som säljs av varje plats för att fastställa deras relativa framgång.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Föregående diagram visar att även om de flesta platser sälja mer än 80% för biljetten, vissa kämpar för att fylla i mer än hälften platser. Experimentera med de värden bra att välja högsta eller lägsta procentandel av biljetter som säljs för varje plats.

Tidigare deepened du dina analyser för att identifiera att biljettförsäljningar tenderar att följer förutsägbara mönster. Den här identifieringen kan Wingtip biljetter hjälp presterar som förväntat platser boost biljettförsäljningar av rekommenderar dynamisk prissättning. Den här identifiera kan visa en möjlighet att använda machine learning-teknik för att förutsäga efter varje händelse. Förutsägelser kan också göras för påverkan på intäkter på erbjuder rabatter på biljettförsäljningar. Power BI Embedded kan integreras i ett program för hantering av händelsen. Integrationen kan bidra till att visualisera förväntad försäljning och effekten av olika rabatter. Programmet kan bidra till att utforma en optimal rabatt tillämpas direkt från analytics visningen.

Du har observerats trender i klientdata från WingTip-programmet. Du kan överväga andra sätt som appen kan informera affärsbeslut för leverantörer för SaaS-program. Leverantörer kan bättre tillgodose behoven för sina klienter. Den här självstudien har förhoppningsvis utrustade du med verktyg som behövs för att utföra analyser på klientdata till att låta ditt företag att fatta datadrivna beslut.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Distribuerat en klientanalysdatabas med fördefinierade star-schema-tabeller
> - Används för elastiska jobb för att extrahera data från alla klientdatabasen
> - Sammanfoga de extraherade data i tabeller i ett star-schema som utformats för analys
> - Fråga en databas för analys 
> - Använd Power BI för datavisualisering av för att se trender i klientdata 

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastiska jobb](elastic-jobs-overview.md).
- [Flera klienter analytics med hjälp av extraherade data - app för flera klienter](saas-multitenantdb-tenant-analytics.md)