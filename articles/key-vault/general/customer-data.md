---
title: Azure Key Vault kund data funktioner – Azure Key Vault | Microsoft Docs
description: Lär dig mer om kund information, som Azure Key Vault ta emot vid skapande eller uppdatering av valv, nycklar, hemligheter, certifikat och hanterade lagrings konton.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: e7cfc707aa4bccdcd72e45efa3693ebd8f88a211
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88189928"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault kund data funktioner

Azure Key Vault tar emot kund information under skapandet eller uppdateringen av valv, nycklar, hemligheter, certifikat och hanterade lagrings konton. Den här kund informationen är direkt synlig i Azure Portal och via REST API. Kund information kan redige ras eller tas bort genom att uppdatera eller ta bort objektet som innehåller data.

System åtkomst loggar genereras när en användare eller ett program har åtkomst till Key Vault. Detaljerade åtkomst loggar är tillgängliga för kunder som använder Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kund information

Följande information identifierar kund information inom Azure Key Vault:

- Åtkomst principer för Azure Key Vault innehåller objekt-ID: n som representerar användare, grupper eller program
- Certifikat ämnen kan innehålla e-postadresser eller andra användar-eller organisations identifierare
- Certifikat kontakter kan innehålla användares e-postadresser, namn eller telefonnummer
- Certifikat utfärdare kan innehålla e-postadresser, namn, telefonnummer, kontoautentiseringsuppgifter och organisations information
- Godtyckliga taggar kan tillämpas på objekt i Azure Key Vault. Dessa objekt omfattar valv, nycklar, hemligheter, certifikat och lagrings konton. De taggar som används kan innehålla personliga data
- Azure Key Vault åtkomst loggar innehåller objekt-ID: n, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md): er och IP-adresser för varje REST API-anrop
- Azure Key Vault diagnostikloggar kan innehålla objekt-ID: n och IP-adresser för REST API-anrop

## <a name="deleting-customer-data"></a>Tar bort kund information

Samma REST-API: er, Portal upplevelse och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagrings konton kan också uppdatera och ta bort dessa objekt.

Med mjuk borttagning kan du återställa borttagna data i 90 dagar efter borttagningen. När du använder mjuk borttagning kan data tas bort permanent innan perioden 90 dagar för kvarhållning går ut genom att utföra en rensnings åtgärd. Om valvet eller prenumerationen har kon figurer ATS för att blockera rensnings åtgärder går det inte att ta bort data permanent förrän den schemalagda kvarhållningsperioden har passerat.

## <a name="exporting-customer-data"></a>Exportera kund information

Samma REST-API: er, Portal upplevelse och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagrings konton gör det också möjligt att visa och exportera dessa objekt.

Azure Key Vault åtkomst loggning är en valfri funktion som kan aktive ras för att generera loggar för varje REST API-anrop. Loggarna överförs till ett lagrings konto i prenumerationen där du tillämpar den bevarande princip som uppfyller organisationens krav.

Azure Key Vault diagnostikloggar som innehåller personliga data kan hämtas genom att göra en export förfrågan i användar sekretess portalen. Den här begäran måste göras av klient organisationens administratör.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault loggning](logging.md))

- [Översikt av mjuk borttagning för Azure Key Vault](soft-delete-cli.md)

- [Azure Key Vault nyckel åtgärder](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault hemliga åtgärder](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault certifikat och principer](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault lagrings konto åtgärder](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
