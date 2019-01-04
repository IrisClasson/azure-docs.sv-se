---
title: Lägg till klienter för användning och fakturering i Azure Stack | Microsoft Docs
description: De steg som krävs för att lägga till en användare i Azure Stack som hanteras av en Cloud Service Provider (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: e319c803b610005bc130e9c5d314d23c48c1e9a3
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717202"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Lägg till klient för användning och fakturering i Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskrivs vad du behöver lägga till en användare i Azure Stack som hanteras av en Cloud Service Provider (CSP). När den nya innehavaren använder resurser, kommer Azure Stack rapportera användning till CSP-prenumerationen.

CSP: er erbjuda ofta tjänster till flera slutkunder (klienter) på deras Azure Stack-distribution. Att lägga till klienter i Azure Stack-registrering säkerställer att varje klientanvändning ska rapporteras och debiteras för den motsvarande CSP-prenumerationen. Om du inte slutföra stegen i den här artikeln, debiteras klientanvändning till den första registreringen av Azure Stack-prenumerationen. Innan du kan lägga till en slutkunden till Azure Stack för spårning av användning och för att hantera sina klienten, måste du konfigurera Azure Stack som en Kryptografiprovider. Steg och resurser finns i [Hantera användning och fakturering för Azure Stack som en Molntjänstleverantör](azure-stack-add-manage-billing-as-a-csp.md).

I följande diagram visas de steg som en CSP måste du följa om du vill aktivera en ny kund att använda Azure Stack och ställa in användningsspårning för kunden. Genom att lägga till slutkunden, kommer du också att hantera resurser i Azure Stack. Har du två alternativ för att hantera sina resurser:

1. Du kan underhålla slutkunden och ange autentiseringsuppgifter för lokala Azure Stack-prenumerationen till slutkunden.  
2. Eller slutkunden kan arbeta med sin prenumeration lokalt och lägga till CSP: N som en gäst med ägarbehörigheter.  

**Hur du lägger till en slutkunden**

![Ställ in Molntjänstleverantören för spårning av användning och för att hantera slutet kund-ID](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Skapa en ny kund i Partner Center

Skapa en ny Azure-prenumeration för kunden Partner Center. Anvisningar finns i [lägga till en ny kund](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Skapa en Azure-prenumeration för slutkunden

När du har skapat en post för din kund i Partner Center kan sälja du dem på prenumerationer till produkter i katalogen. Anvisningar finns i [skapa, pausa eller avsluta kundprenumerationer](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Skapa en gästanvändare i katalogen end-kund

Om slutkunden ska hantera sina egna konton, skapa en gästanvändare i sin katalog och skicka dem till informationen. Användaren sedan lägger till gästen och höjer behörigheten Gäst till **ägare** till CSP: N för Azure Stack-konto.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Uppdatera registreringen med kundprenumerationen slutet

Uppdatera din registrering med nya kundens prenumeration. Azure rapporterar kundens användning med hjälp av kunden identiteten från Partner Center. Det här steget säkerställer att varje kunds användning rapporteras under kundens enskilda CSP-prenumeration. Detta gör att spåra användaren användning och fakturering är mycket enklare.

> [!Note]  
> Om du vill utföra det här steget måste du ha [registrerad Azure Stack](azure-stack-register.md).

1. Öppna Windows PowerShell med en upphöjd kommandotolk och kör:  
    `Add-AzureRmAccount`
2. Ange dina autentiseringsuppgifter för Azure.
3. I PowerShell-session kör du:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>Ny AzureRmResource PowerShell-parametrar
| Parameter | Beskrivning |
| --- | --- | 
|registrationSubscriptionID | Azure-prenumerationen som har använts för den första registreringen av Azure Stack.|
| customerSubscriptionID | Azure-prenumerationen (inte Azure Stack) som hör till kunden som ska registreras. Måste vara skapas i CSP-erbjudandet; i praktiken innebär detta via Partnercenter. Om en kund har mer än en Azure Active Directory-klient, måste den här prenumerationen skapas i den klient som ska användas för att logga in på Azure Stack. Kundens prenumerations-ID måste använda gemener. |
| resourceGroup | Resursgruppen i Azure som är lagrade i din registrering. |
| registrationName | Namnet på registreringen av din Azure Stack. Det är ett objekt som lagras i Azure. | 
| Egenskaper | Anger egenskaper för resursen. Använd den här parametern om du vill ange värden för egenskaper som är specifika för resurstypen.


> [!Note]  
> Klienter måste vara registrerad med varje Azure Stack som de använder. Om du har två Azure Stack-distributioner och en klient kommer att använda båda, måste du uppdatera de inledande registreringarna för varje distribution med klientprenumeration.

## <a name="onboard-tenant-to-azure-stack"></a>Publicera klient till Azure Stack

Konfigurera Azure Stack för att hantera användare från flera Azure AD-klienter att använda tjänster i Azure Stack. Anvisningar finns i [aktivera flera innehavare i Azure Stack](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Skapa en lokal resurs i slutet kund-klienten i Azure Stack

När du har lagt till den nya kunden till Azure Stack eller kundklient slutet har aktiverat din gästkontot med ägarprivilegier, kontrollera att du kan skapa en resurs i deras klienter. De kan till exempel [skapa en Windows-dator med Azure Stack portal](user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Nästa steg

 - Felmeddelanden om de aktiveras i din registreringen finns i [klient registrering felmeddelanden](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](azure-stack-billing-and-chargeback.md).
 - Om du vill granska hur en slutkund kan lägga till dig, som CSP: N, chef för deras Azure Stack-klientorganisation, se [aktivera en Molntjänstleverantör att hantera din prenumeration för Azure Stack](user/azure-stack-csp-enable-billing-usage-tracking.md).
