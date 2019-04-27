---
title: Ansluta ett Google Cloud Platform-konto till Cloudyn i Azure | Microsoft Docs
description: Ansluta ett Google Cloud Platform-konto för att visa data för kostnader och användning i Cloudyn-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 7f63293900e116fd3175b0ea6d704993a2dcf591
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576311"
---
# <a name="connect-a-google-cloud-platform-account"></a>Ansluta ett Google Cloud Platform-konto

Du kan ansluta ditt befintliga Google Cloud Platform-konto till Cloudyn. När du ansluter ditt konto till Cloudyn, är kostnader och användning data tillgängliga i Cloudyn-rapporter. Den här artikeln hjälper dig att konfigurera och ansluta ditt Google-konto med Cloudyn.


## <a name="collect-project-information"></a>Samla in projektinformation

Du startar genom att samla in information om ditt projekt.

1. Logga in på Google Cloud Platform-konsolen i [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Granska informationen som du vill att publicera till Cloudyn och anteckna den **projektnamn** och **projekt-ID**. Spara informationen till hands för senare steg.  
    ![Projektnamn och projekt-ID som visas i konsolen Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Om fakturering inte är aktiverat och länka till ditt projekt, skapa ett faktureringskonto. Mer information finns i [skapa en ny faktureringskonto](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Aktivera storage bucket fakturering export

Cloudyn hämtar Google faktureringsinformation från en bucket för lagring. Behåll den **bucketnamnet** och **rapporten prefix** informationen till hands för senare användning under Cloudyn-registreringen.

Använda Google Cloud Storage för att lagra användningsrapporter medför minimal avgifter. Mer information finns i [Cloud Storage-priser](https://cloud.google.com/storage/pricing).

1. Om du inte har aktiverat fakturering export till en fil, följer du anvisningarna i [hur du aktiverar fakturering export till en fil](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Du kan använda JSON- eller CSV fakturering exportformat.
2. Annars i Google Cloud Platform-konsolen navigerar du till **fakturering** > **fakturering export**. Observera faktureringen **bucketnamnet** och **rapporten prefix**.  
    ![Fakturering export informationen som visas på sidan fakturering export](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Aktivera Google Cloud Platform API: er

För att samla in information om användning och tillgången Cloudyn måste du ha följande Google Cloud Platform API: erna aktiverat:

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- JSON-API: et för Google Cloud Storage
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>Aktivera eller verifiera API: er

1. Välj det projekt som du vill registrera med Cloudyn i Google Cloud Platform-konsolen.
2. Gå till **API: er och tjänster** > **biblioteket**.
3. Använda sökning var tidigare har listats API.
4. För varje API: et, kontrollerar du att **API aktiverat** visas. Annars klickar du på **aktivera**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Lägg till ett Google Cloud-konto i Cloudyn

1. Öppna Cloudyn-portalen från Azure-portalen eller navigera till [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) och logga in.
2. Klicka på **inställningar** (kugghjulet symbol) och välj sedan **Molnkonton**.
3. I **kontohantering**väljer den **Google-konton** fliken och klicka sedan på **Lägg till ny +**.
4. I **Google kontonamn**, ange den e-postadressen för fakturering kontot och klicka sedan på **nästa**.
5. Välj eller ange ett Google-konto i dialogrutan för Google-autentisering och sedan **TILLÅT** cloudyn.com åtkomst till ditt konto.
6. Lägga till projektinformation begäran som användes tidigare anges. De innehåller **projekt-ID**, **projekt** namn, **fakturering** bucketnamnet, och **fakturering filen** rapportera prefix och klicka sedan på  **Spara**.  
    ![Lägga till Google-projekt i Cloudyn-kontot](./media/connect-google-account/add-project.png)

Ditt Google-konto visas i listan över konton och det ska stå **autentiserad**. Ditt projektnamn i Google och ID ska visas och har en grön bock symbol under det. Kontostatus som ska stå **slutförd**.

Inom några timmar visar Cloudyn-rapporter information för kostnader och användning av Google.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om Cloudyn kan fortsätta att den [granska användning och kostnader](./tutorial-review-usage.md) självstudiekursen för Cloudyn.
