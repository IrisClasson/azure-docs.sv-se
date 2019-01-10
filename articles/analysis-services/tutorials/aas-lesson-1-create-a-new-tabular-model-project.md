---
title: 'Azure Analysis Services självstudiekurs 1: Skapa ett nytt projekt för tabellmodeller | Microsoft Docs'
description: Beskriver hur du skapar ett nytt Azure Analysis Services-självstudieprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3291721847d34b0fa9a6259bfeb6ec6fa06ed2b5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188019"
---
# <a name="create-a-tabular-model-project"></a>Skapa ett projekt för tabellmodeller

I den här lektionen använder du Visual Studio med Analysis Services-projekt eller SQL Server Data Tools (SSDT) för att skapa ett nytt projekt för tabellmodeller på kompatibilitetsnivå 1400. När det nya projektet har skapats kan du börja lägga till data och redigera din modell. Den här lektionen ger dig också en kort introduktion till redigeringsmiljön för tabellmodeller i Visual Studio.  
  
Uppskattad tidsåtgång för den här lektionen: **10 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet är den första lektionen i en självstudie om redigering av tabellmodeller. Det finns flera förutsättningar som måste uppfyllas för att slutföra den här lektionen. Läs mer i [Azure Analysis Services – Självstudiekurs för Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Skapa ett nytt projekt för tabellmodeller  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Så här skapar du ett nytt projekt för tabellmodeller  
  
1.  På **Arkiv**-menyn i Visual Studio klickar du på **Nytt** > **Projekt**.  
  
2.  I dialogrutan **Nytt projekt** expanderar du **Installerad** > **Business Intelligence** > **Analysis Services**, och klickar sedan på **Analysis Services-tabellprojekt**.  
  
3.  Under **Namn** anger du **AW Internet Sales** och sedan anger du en plats för projektfilerna.  
  
    Som standard är **Lösningsnamn** samma som projektnamnet men du kan ange ett annat lösningsnamn.  
  
4.  Klicka på **OK**.  
  
5.  I dialogrutan **Tabellmodelldesigner** väljer du **Integrerad arbetsyta**.  
  
    Arbetsytan har en databas för tabellmodeller med samma namn som projektet under redigeringen av modellen. Integrerad arbetsyta innebär att Visual Studio använder en inbyggd instans vilket eliminerar behovet att installera en separat Analysis Services-serverinstans för redigering av modellen.
      
6.  I **Kompatibilitetsnivå** väljer du **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Om du inte ser SQL Server 2017 / Azure Analysis Services (1400) i listrutan Kompatibilitetsnivå så använder du inte den senaste versionen av SQL Server Data Tools. För att hämta den senaste versionen går du till [Installera SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
      
  
## <a name="understanding-the-visual-studio-tabular-model-authoring-environment"></a>Förstå Visual Studio-tabellmodellen redigeringsmiljön  
Nu när du har skapat ett nytt projekt för tabellmodeller kan vi börja utforska redigeringsmiljön för tabellmodellen i Visual Studio.  
  
När projektet har skapats öppnas det i Visual Studio. Till höger i **Tabellmodellutforskaren** visas en trädvy över objekten i modellen. Eftersom du inte har importerat data ännu är mapparna tomma. Du kan högerklicka på en mapp för objekt för att utföra åtgärder, precis som på menyraden. När du går igenom den här självstudien kan du använda tabellmodellutforskaren för att navigera mellan olika objekt i ditt modellprojekt.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Klicka på fliken **Solution Explorer**. Här kan du se filen **Model.bim**. Om du inte ser designer-fönstret till vänster (det tomma fönstret med fliken Model.bim) i **Solution Explorer** dubbelklickar du på filen **Model.bim** under **AW Internet Sales-projekt**. Filen Model.bim innehåller metadata för ditt modellprojekt. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Klicka på **Model.bim**. I fönstret **Egenskaper** visas modellegenskaper, varav den viktigaste är egenskapen **DirectQuery-läge**. Den här egenskapen anger om modellen är distribuerad i InMemory-läge (av) eller DirectQuery-läge (på). I den här självstudien skapar och distribuerar du din modell i InMemory-läge.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
När du skapar ett modellprojekt ställs vissa modellegenskaper in automatiskt enligt de datamodellinställningar som kan anges på menyn **Verktyg** > dialogrutan **Alternativ**. Egenskaper för säkerhetskopiering av data, kvarhållning av arbetsyta och arbetsyteservern anger hur och var arbetsyteservern (modellredigeringsdatabasen) är säkerhetskopierad, bevarad minnesinternt och skapas. Du kan ändra de här inställningarna senare om det behövs, men lämna dem som de är för tillfället.  

Högerklicka på **AW Internet Sales** (projektet) i **Solution Explorer** och sedan på **Egenskaper**. Dialogrutan **AW Internet Sales Property Pages** (AW Internet Sales egenskapssidor) visas. Du ställer in några av de här egenskaperna senare när du distribuerar modellen.  
  
När du installerade Analysis Services-projekt eller SSDT lades flera nya menyalternativ till Visual Studio-miljön. Klicka på menyn **Modell**. Härifrån kan du importera data, uppdatera data i arbetsytan, bläddra i din modell i Excel, skapa perspektiv och roller, välja modellvyn och ange beräkningsalternativ. Klicka på menyn **Tabell**. Härifrån kan du skapa och hantera relationer, ange inställningar för datumtabell, skapa partitioner och redigera tabellegenskaper. Om du klickar på menyn **Kolumn** kan du lägga till och ta bort kolumner i en tabell, låsa kolumner och ange sorteringsordning. Visual Studio lägger även till några knappar i fältet. En mycket användbar funktion är Autosumma som används för att skapa ett vanligt sammansatt mått för en markerad kolumn. Andra knappar i verktygsfältet ger snabb åtkomst till vanliga funktioner och kommandon.  
  
Utforska några dialogrutor och platser för olika funktioner som är specifika för redigering av tabellmodeller. Även om vissa objekt inte är aktiva ännu så får du en bra uppfattning om redigeringsmiljön för tabellmodeller.  
  

## <a name="whats-next"></a>Nästa steg
[Lektion 2: Hämta data](../tutorials/aas-lesson-2-get-data.md).

  
  
  
