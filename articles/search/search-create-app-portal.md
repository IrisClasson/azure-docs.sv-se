---
title: Skapa en demo-app i Azure Portal
titleSuffix: Azure Cognitive Search
description: Kör guiden skapa demo program (förhands granskning) för att generera HTML-sidor och skript för en operationell webbapp. Sidan innehåller ett sökfält, ett resultat område, en marginal list och stöd för typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/01/2020
ms.openlocfilehash: c6ab5c2cae2bb966c2b040b40dbf36e56a54411b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496761"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Snabb start: skapa en demo-app i portalen (Azure Kognitiv sökning)

Använd guiden **skapa demonstrations program** för Azure Portal för att generera en nedladdnings bar, "localhost"-webbapp som körs i en webbläsare. Beroende på dess konfiguration fungerar den genererade appen vid första användningen, med en Live-skrivskyddad anslutning till ett fjärrindex. En standard-app kan innehålla ett sökfält, ett resultatområde, ett sid reglerings filter och typeahead-stöd.

Demo-appen kan hjälpa dig att visualisera hur ett index fungerar i en klient app, men det är inte avsett för produktions scenarier. Klient program bör omfatta säkerhet, fel hantering och värd logik som den genererade HTML-sidan inte tillhandahåller. När du är redo att skapa en klient app, se [skapa din första Sökapp med .NET SDK](tutorial-csharp-create-first-app.md) för nästa steg.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ [Microsoft Edge (senaste versionen)](https://www.microsoft.com/edge) eller Google Chrome.

+ Ett [sökindex](search-what-is-an-index.md) som ska användas som grund för det genererade programmet. 

  I den här snabb starten används inbyggda exempel data och index för fastighets data, eftersom det innehåller miniatyr bilder (guiden har stöd för att lägga till bilder på resultat sidan). Om du vill skapa ett index som används i den här övningen kör du guiden **Importera data** och väljer *realestate-US-Sample-* data källan.

  ![data källans sida för exempel data](media/search-create-app-portal/import-data-realestate.png)

När indexet är klart att använda går du vidare till nästa steg.

## <a name="start-the-wizard"></a>Starta guiden

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

1. [Hitta Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) och välj **index**på sidan Översikt från länkarna i mitten av sidan. 

1. Välj *realestate-US-Sample-index* i listan över befintliga index.

1. På sidan index, längst upp, väljer du **skapa demo program (för hands version)** för att starta guiden.

1. På den första sidan i guiden väljer du **Aktivera resurs delning mellan ursprung (CORS)** för att lägga till CORS-stöd i index definitionen. Det här steget är valfritt, men din lokala webbapp ansluter inte till fjär indexet utan den.

## <a name="configure-search-results"></a>Konfigurera Sök Resultat

Guiden innehåller en grundläggande layout för åter givning av Sök resultat som innehåller utrymme för en miniatyr bild, en rubrik och en beskrivning. Att säkerhetskopiera vart och ett av dessa element är ett fält i ditt index som innehåller data. 

1. I miniatyr väljer du fältet *miniatyr* i *realestate-US-Sample-* indexet. Det här exemplet inkluderar bild miniatyrer i form av URL-adresserade bilder som lagras i ett fält som kallas *thumbnail*. Lämna fältet tomt om indexet inte innehåller några bilder.

1. I rubrik väljer du ett fält som förmedlar unikheten för varje dokument. I det här exemplet är List-ID ett rimligt val.

1. I Beskrivning väljer du ett fält som innehåller information som kan hjälpa någon att avgöra om du vill klicka till det specifika dokumentet.

   ![data källans sida för exempel data](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Lägg till en marginal List

Sök tjänsten stöder fasett-navigering, som ofta återges som en marginal List. Facets baseras på filter bara och aspekt bara fält, som uttrycks i index schemat.

I Azure Kognitiv sökning är fasettisk navigering en kumulativ filtrerings upplevelse. I en kategori utökar resultatet genom att välja flera filter (till exempel Seattle och Bellevue i stad). I kategorier kan du välja flera filter för att begränsa resultaten.

> [!TIP]
> Du kan visa det fullständiga index schemat i portalen. Leta efter **JSON-länken (index definition)** på varje indexs översikts sida. Fält som kvalificeras för den fasettiska navigeringen har attributen "Filterable: true" och "fasettable: true".

Godkänn det aktuella valet av ansikte och fortsätt till nästa sida.


## <a name="add-typeahead"></a>Lägg till typeahead

Typeahead-funktionen finns i form av förslag för Autoavsluta och frågor. Guiden stöder fråge förslag. Baserat på tangenttryckningar som tillhandahålls av användaren returnerar Sök tjänsten en lista över "slutförda" frågesträngar som kan väljas som indata.

Förslag aktive ras för definitioner av vissa fält. I guiden får du alternativ för att konfigurera hur mycket information som ingår i ett förslag. 

Följande skärm bild visar alternativen i guiden, juxtaposed med en renderad sida i appen. Du kan se hur fält urval används och hur "Visa fält namn" används för att ta med eller undanta etiketter i förslaget.

![Konfiguration av fråge förslag](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Skapa, hämta och köra

1. Skapa HTML-filen genom att välja **skapa demo-app** .

1. När du uppmanas väljer du **Ladda ned appen** för att ladda ned filen.

1. Öppna filen. Du bör se en sida som liknar följande skärm bild. Ange en term och Använd filter för att begränsa resultaten. 

Det underliggande indexet består av fiktiva, genererade data som har duplicerats mellan dokument och beskrivningar som ibland inte matchar avbildningen. Du kan vänta en mer sammanhängande upplevelse när du skapar en app baserat på dina egna index.

![Köra appen](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Även om standard programmet är användbart för inledande utforskning och små aktiviteter, kan du granska API: erna tidigt på så att du förstår begreppen och arbets flödet på en djupare nivå:

> [!div class="nextstepaction"]
> [Skapa ett index med .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)