---
title: Förhindra att under domäner översätts med Azure DNS Ali Aset-poster och Azure App Service anpassad domän verifiering
description: Lär dig hur du undviker det vanliga hotet om hög allvarlighets grad för under domän överbelastning
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 3d63ccc2c47bca9410b5b9105b90aa1f0cf5854a
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439271"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Förhindra Dangling DNS-poster och Undvik under domän övertag Ande

I den här artikeln beskrivs det vanliga säkerhetshot för under domän Övertagning och de steg du kan vidta för att undvika det.


## <a name="what-is-subdomain-takeover"></a>Vad är under domän överköps?

Under domänens övertag Ande är ett vanligt hot mot hög allvarlighets grad för organisationer som regelbundet skapar och tar bort många resurser. En under domän överköps kan uppstå när du har en DNS-post som pekar på en deetablerad Azure-resurs. Sådana DNS-poster kallas även "Dangling DNS"-poster. CNAME-poster är särskilt sårbara för det här hotet.

Ett vanligt scenario för en under domän överköps:

1. En webbplats skapas. 

    I det här exemplet `app-contogreat-dev-001.azurewebsites.net` .

1. En CNAME-post läggs till i DNS som pekar på webbplatsen. 

    I det här exemplet skapades följande egna namn: `greatapp.contoso.com` .

1. Efter några månader behövs inte längre platsen, så den tas bort **utan att** motsvarande DNS-post tas bort. 

    Posten CNAME DNS är nu "Dangling".

1. Nästan omedelbart efter att webbplatsen har tagits bort, identifierar en hot aktör den saknade platsen och skapar en egen webbplats på `app-contogreat-dev-001.azurewebsites.net` .

    Nu är trafiken avsedd för `greatapp.contoso.com` att gå till hot skådespelarens Azure-webbplats och hot aktörens kontroll av det innehåll som visas. 

    Dangling DNS utnyttjades och Contosos under domän "GreatApp" har varit ett skadelidande för under domän övertag Ande. 

![Under domän överköps från en avetablerad webbplats](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Riskerna med under domäner överköps

När en DNS-post pekar på en resurs som inte är tillgänglig, bör posten tas bort från din DNS-zon. Om den inte har tagits bort är det en "Dangling DNS"-post och skapar möjligheten för under domän övertag Ande.

Dangling DNS-poster gör det möjligt för hot aktörer att ta kontroll över det associerade DNS-namnet för att vara värd för en skadlig webbplats eller tjänst. Skadliga sidor och tjänster i en organisations under domän kan resultera i följande:

- **Förlust av kontroll över innehållet i under domänen** -negativ press om organisationens oförmåga att skydda dess innehåll, samt varumärkes skada och förtroende förlust.

- **Cookie-fångst från misstänkta besökare** – det är vanligt att webbappar exponerar sessionscookies till under domäner (*. contoso.com), vilket innebär att alla under domäner kan komma åt dem. Hot aktörer kan använda under domän uppköp för att bygga en äkta utseende sida, lura obehöriga användare att besöka den och skörda sina cookies (även säkra cookies). En vanlig felbegrepp är att använda SSL-certifikat för att skydda din webbplats, och dina användares cookies, från en övertag Ande. En hot aktör kan dock använda den kapade under domänen som ska användas för och ta emot ett giltigt SSL-certifikat. Giltiga SSL-certifikat ger dem åtkomst till säkra cookies och kan öka den uppfattade giltighet på den skadliga webbplatsen ytterligare.

- **Phishing-kampanjer** – autentiska under domäner kan användas i nät fiske kampanjer. Detta gäller för skadliga webbplatser och även för MX-poster som gör det möjligt för hot aktör att ta emot e-post som är adresserade till en legitim under domän till ett säkert märke.

- **Ytterligare risker** – skadliga webbplatser kan användas för att eskalera till andra klassiska attacker som XSS, CSRF, CORS bypass och mer.



## <a name="preventing-dangling-dns-entries"></a>Förhindra Dangling DNS-poster

Att se till att din organisation har implementerat processer för att förhindra Dangling DNS-poster och den resulterande under domänens övertag Ande är en viktig del av ditt säkerhets program.

De förebyggande åtgärder som är tillgängliga för dig idag visas nedan.


### <a name="use-azure-dns-alias-records"></a>Använd Azure DNS Ali Aset poster

Azure DNSs [Ali Asets](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) kan förhindra Dangling-referenser genom att koppla livs cykeln för en DNS-post med en Azure-resurs. Anta till exempel att du har en DNS-post som är kvalificerad som en aliasresurspost som pekar på en offentlig IP-adress eller en Traffic Manager-profil. Om du tar bort de underliggande resurserna blir DNS-Ali-posten en tom post uppsättning. Den borttagna resursen är inte längre referenser till den. Det är viktigt att Observera att det finns gränser för vad du kan skydda med Ali Aset. I dag är listan begränsad till:

- Azure Front Door
- Traffic Manager-profiler
- Slut punkter för Azure Content Delivery Network (CDN)
- Offentliga IP-adresser

Trots de begränsade tjänst erbjudandena idag rekommenderar vi att du använder Ali Asets för att skydda mot under domän övertag närhelst det är möjligt.

[Lär dig mer](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) om funktionerna i Azure DNSs Ali Asets poster.



### <a name="use-azure-app-services-custom-domain-verification"></a>Använd Azure App Service anpassade domän verifieringen

När du skapar DNS-poster för Azure App Service skapar du en asuid. under domän TXT-post med domän verifierings-ID. Om det finns en sådan TXT-post kan ingen annan Azure-prenumeration verifiera den anpassade domänen som tar den över. 

Dessa poster hindrar inte någon från att skapa Azure App Service med samma namn som finns i din CNAME-post. Utan möjligheten att bevisa ägande av domän namnet kan inte hot aktörer ta emot trafik eller kontrol lera innehållet.

[Läs mer](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) om hur du mappar ett befintligt anpassat DNS-namn till Azure App Service.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Bygg och automatisera processer för att minimera hotet

Det är ofta upp till utvecklare och drift team att köra rensnings processer för att undvika Dangling DNS-hot. Med hjälp av metoderna nedan kan du se till att din organisation inte har drabbats av det här hotet. 

- **Skapa procedurer för förebyggande åtgärder:**

    - Lär dina programutvecklare att omdirigera adresser när de tar bort resurser.

    - Lägg till "ta bort DNS-post" i listan över nödvändiga kontroller vid inaktive ring av en tjänst.

    - Lägg till [borttagnings lås](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) på alla resurser som har en anpassad DNS-post. Ett borttagnings lås fungerar som en indikator att mappningen måste tas bort innan resursen avetableras. Mått som detta kan endast fungera när det kombineras med interna utbildnings program.

- **Skapa procedurer för identifiering:**

    - Granska dina DNS-poster regelbundet för att säkerställa att dina under domäner är mappade till Azure-resurser som:

        - Exist – fråga dina DNS-zoner efter resurser som pekar på Azure-underdomäner som *. azurewebsites.net eller *. cloudapp.azure.com (se [den här referens listan](azure-domains.md)).
        - Du äger – bekräfta att du äger alla resurser som dina DNS-under domäner är riktade till.

    - Underhålla en tjänst katalog för Azures fullständiga kvalificerade domän namn (FQDN) och program ägare. Skapa tjänst katalogen genom att köra följande skript i Azure Resource Graph. Det här skriptet Projects innehåller FQDN-slutpunktens information om de resurser som du har åtkomst till och matar ut dem i en CSV-fil. Om du har åtkomst till alla prenumerationer för din klient, tar skriptet hänsyn till alla prenumerationer som visas i följande exempel skript. Om du vill begränsa resultatet till en speciell uppsättning prenumerationer redigerar du skriptet som det visas.

        >[!IMPORTANT]
        > **Behörigheter** – kör frågan som en användare som har åtkomst till alla dina Azure-prenumerationer. 
        >
        > **Begränsningar** – Azure Resource Graph har begränsnings-och växlings gränser som du bör tänka på om du har en stor Azure-miljö. [Lär dig mer](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) om att arbeta med stora data uppsättningar för Azure-resurser. Följande exempel skript använder prenumerations-batching för att undvika dessa begränsningar.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                   # Output file path and names
                   $date = get-date
                   $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                   $fdate #log to console
                   $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                   $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                   $fpath = $rpath + $rname
                   $fpath #This is the output file of FQDN report.

            # query
            $query = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.apimanagement/service',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.apimanagement/service', properties['hostnameConfigurations']['hostName'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, ['type'], name, FQDN
                        | where isnotempty(FQDN)";

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            # Run the query for each subscription batch with paging.
            foreach ($batch in $subscriptionsBatch)
            { 
                $Skip = 0; #Reset after each batch.

                $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
            }

            # View the completed results of the query on all subscriptions.
            $response | Export-Csv -Path $fpath -Append 

        ```

        Lista över typer och deras `FQDNProperty` värden som anges i föregående resurs diagram fråga:

        |Resursnamn  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|Microsoft. Network/frontdoors|egenskaper. cName|
        |Azure Blob Storage|Microsoft. Storage/storageaccounts|Properties. blobar. blob|
        |Azure CDN|Microsoft. CDN/profiler/slut punkter|egenskaper. hostName|
        |Offentliga IP-adresser|Microsoft. Network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Azure Traffic Manager|Microsoft. Network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Azure Container-instans|Microsoft. containerinstance/containergroups|egenskaper. ipAddress. FQDN|
        |Azure API Management|Microsoft. API Management/Service|Properties. hostnameConfigurations. hostName|
        |Azure App Service|Microsoft. Web/Sites|egenskaper. defaultHostName|
        |Azure App Service-platser|Microsoft. Web/Sites/lotss|egenskaper. defaultHostName|


- **Skapa procedurer för reparation:**
    - När Dangling DNS-poster hittas måste ditt team undersöka om kompromissen har inträffat.
    - Undersök varför adressen inte omdirigerades när resursen avbröts.
    - Ta bort DNS-posten om den inte längre används eller peka på rätt Azure-resurs (FQDN) som ägs av din organisation.
 

## <a name="next-steps"></a>Nästa steg

Mer information om relaterade tjänster och Azure-funktioner som du kan använda för att skydda dig mot under domän övertag ande finns på följande sidor.

- [Azure DNS stöder användning av Ali Aset för anpassade domäner](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Använd domän verifierings-ID: t när du lägger till anpassade domäner i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Snabb start: kör din första resurs diagram fråga med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
