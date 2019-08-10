---
title: Självstudie – Ansluta till Azure Analysis Services med Power BI Desktop | Microsoft Docs
author: minewiskan
manager: kfile
description: Lär dig att ansluta till Azure Analysis Services med Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: daebc3e720eec8d958697288922c788a44156778
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932386"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Självstudie: Ansluta med Power BI Desktop

I den här självstudien använder du Power BI Desktop för att ansluta till exempelmodelldatabasen adventureworks på din server. Uppgifterna du utför simulerar en vanlig användaranslutning till modellen och skapar en enkel rapport från modelldata.

> [!div class="checklist"]
> * Hämta servernamnet från Portal
> * Anslut med hjälp av Power BI Desktop
> * Skapa en enkel rapport

## <a name="prerequisites"></a>Förutsättningar

- [Lägg till exempelmodelldatabasen adventureworks](../analysis-services-create-sample-model.md) på din server.
- Se till att du har [*läsbehörighet*](../analysis-services-server-admins.md) för exempelmodelldatabasen adventureworks.
- [Installera senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
I den här självstudien loggar du in på Portal för att hämta servernamnet. Normalt skulle användarna få servernamnet från serveradministratören.

Logga in på [portalen](https://portal.azure.com/).

## <a name="get-server-name"></a>Hämta servernamnet
För att kunna ansluta till servern från Power BI Desktop behöver du först servernamnet. Du kan hämta servernamnet på Portal.

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
   ![Hämta servernamnet i Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. I Power BI Desktop klickar du på **Hämta data** > **Azure** > **Azure Analysis Services-databas**.

   ![Ansluta i Hämta data](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. I **Server** klistrar du in servernamnet. I **Databas** skriver du sedan **adventureworks** och klickar på **OK**.

   ![Ange servernamn och modelldatabas](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Ange dina autentiseringsuppgifter när du uppmanas att göra det. Kontot du anger måste ha minst läsbehörighet för exempelmodelldatabasen adventureworks.

    Modellen adventureworks öppnas i Power BI Desktop med en tom rapport i rapportvyn. I listan **Fält** visas alla modellobjekt som inte är dolda. Anslutningsstatus visas i det nedre högra hörnet.

4. I **VISUALISERINGAR** väljer du **Grupperat liggande stapeldiagram**, klickar på **Format** (färgrollerikonen) och aktiverar **Data Labels** (Dataetiketter). 

   ![Visualiseringar](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. I **FÄLT** > **Internet Sales**-tabellen (Internetförsäljning) väljer du måtten **Internet Sales Total** (Total Internetförsäljning) och **Margin** (Marginal). Välj **Product Category Name** (Namn på produktkategori) i tabellen **Product Category** (Produktkategori).

   ![Slutför rapporten](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Ägna en stund åt att utforska exempelmodellen adventureworks genom att skapa olika visualiseringar och uppdelningar av data och mått. Glöm inte att spara när du är nöjd med rapporten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver rapporten längre behöver du inte spara den. Du kan också ta bort filen om du har sparat den.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att använda Power BI Desktop för att ansluta till en datamodell på en server och skapa en enkel rapport. Om du inte är bekant med hur du skapar en data modell kan du läsa [själv studie kursen om data modellering i Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) i SQL Server Analysis Services-dokumenten.