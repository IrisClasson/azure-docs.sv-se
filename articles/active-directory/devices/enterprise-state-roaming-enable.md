---
title: Aktivera Enterprise Tillståndsväxling i Azure Active Directory | Microsoft Docs
description: Vanliga frågor om Enterprise State Roaming-inställningar i Windows-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c1fc6340df6a5400864b2e1222a2c65e586232
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482019"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Aktivera enterprise tillståndsväxling i Azure Active Directory
Enterprise State Roaming är tillgänglig för alla företag med en Azure AD Premium eller Enterprise Mobility + Security (EMS)-licens. Mer information om hur du hämtar en Azure AD-prenumeration finns i den [produktsidan för Azure AD](https://azure.microsoft.com/services/active-directory).

När du aktiverar Enterprise State Roaming, beviljas automatiskt en kostnadsfri, begränsad användning licens för Azure Rights Management-skydd från Azure Information Protection i din organisation. Den här kostnadsfria prenumerationen är begränsad till kryptera och dekryptera Företagsinställningar och programdata synkroniseras genom Enterprise State Roaming. Du måste ha [en betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) att använda alla funktioner i Azure Rights Management-tjänsten.

## <a name="to-enable-enterprise-state-roaming"></a>Aktivera Enterprise Tillståndsväxling

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory** &gt; **enheter** &gt; **Enterprise State Roaming**.
1. Välj **användarna kan synkronisera inställningar och AppData på enheter**. Mer information finns i [så här konfigurerar du Enhetsinställningar](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![Bild av enhetsinställning med namnet användare kan synkronisera inställningar och AppData på enheter](./media/enterprise-state-roaming-enable/device-settings.png)
  
Enheten måste autentiseras med hjälp av en Azure AD-identitet för för en Windows 10-enhet du använder Enterprise State Roaming-tjänsten. För enheter som är anslutna till Azure AD är användarens primära inloggning identitet sina Azure AD-identitet, så krävs ingen ytterligare konfiguration. För enheter som använder en lokal Active Directory kan IT-administratören måste [konfigurera Azure Active Directory-anslutna hybridenheter](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Datalagring
Enterprise State Roaming data finns i en eller flera [Azure-regioner](https://azure.microsoft.com/regions/) som bäst överensstämmer med värdet för land/region i Azure Active Directory-instans. Enterprise State Roaming data partitioneras baserat på tre större geografiska områden: Nordamerika, EMEA och APAC. Enterprise State Roaming data för klientorganisationen finns lokalt tillsammans med det geografiska området och har replikerats inte i flera regioner.  Exempel:

| Värdet för land/region | sina data har finns i |
| -------------------- | ------------------------ |
| En EMEA land/region, till exempel Frankrike eller Zambia | En eller flera av Azure-regioner i Europa |
| Nordamerika land/region, till exempel USA eller Kanada | en eller flera av Azure-regioner i USA |
| En APAC land/region, till exempel Australien eller Nya Zeeland | en eller flera av Azure-regioner i Asien |
| Sydamerika och Antarktis regioner | en eller flera Azure-regioner i USA |

Land/region-värdet har angetts som en del av processen för Azure AD-katalogen och kan inte ändras senare. Om du behöver mer information på din plats för lagring av data kan en supportbegäran [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Visa synkroniseringsstatus för per användare-enhet
Följ dessa steg om du vill visa en statusrapport för per användare enheten synkronisering.

1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com/).
1. Välj **Azure Active Directory** &gt; **användare** &gt; **alla användare**.
1. Välj användaren och välj sedan **enheter**.
1. Under **visa**väljer **Enhetssynkroniseringställningar eller AppData** att visa synkroniseringsstatus.
  
   ![Bild av enhetsinställning synkronisera data](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Om det finns enheter synkroniserar för den här användaren kan se enheterna som visas här.
  
   ![Bild av kolumnbaserad enhetsdata för synkronisering](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Datakvarhållning
Data som synkroniseras med Microsoft-molnet med hjälp av Enterprise State Roaming behålls tills den raderas manuellt eller i fråga data bedöms vara inaktuella. 

### <a name="explicit-deletion"></a>Explicit borttagning
Explicit borttagning är när en Azure-administratör tar bort en användare eller en katalog eller annars begär uttryckligen att data som ska tas bort.

* **Användaren tas bort**: När en användare tas bort i Azure AD, tas det användarkonto som nätverksväxling data bort efter 90 till 180 dagar. 
* **Ta bort katalogen**: Tar bort en hel katalog i Azure AD är en omedelbar åtgärd. Alla för inställningsdata som är associerade med den katalogen tas bort efter 90 till 180 dagar. 
* **På begäran om borttagning av**: Om Azure AD-administratören vill ta bort en viss användares data eller inställningsdata manuellt, administratören kan lämna in en biljett med [Azure-supporten](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Ta bort inaktuella data
Data som inte har använts i ett år (”kvarhållningsperioden”) kommer att behandlas som inaktuell och kan tas bort från Microsoft-molnet. Kvarhållningsperioden kan ändras, men inte mindre än 90 dagar. Inaktuella data kan vara en specifik uppsättning Windows/programinställningar eller alla inställningar för en användare. Exempel:

* Om inga enheter åtkomst till en viss inställningar-samling (till exempel om ett program tas bort från enheten eller en grupp med inställningar, till exempel ”tema” är inaktiverad för alla användarens enheter), och sedan samlingen blir inaktuella efter kvarhållningsperioden och kan tas bort . 
* Om en användare har inaktiverat synkronisering på sina enheter, inga inställningsdata kommer att komma åt och alla inställningsdata för den användaren blir inaktuella och kan tas bort efter kvarhållningsperioden. 
* Om Azure AD directory-administratör inaktiverar Enterprise State Roaming för hela katalogen, sedan alla användare i den directory slutar att synkronisera inställningar och inställningsdata för alla för alla användare blir inaktuella och kan tas bort efter kvarhållningsperioden. 

### <a name="deleted-data-recovery"></a>Återställning av borttagna data
Policyn för datalagring i kan inte konfigureras. När data raderas permanent, kan den inte återställas. Dock raderas inställningsdata endast från Microsoft-molnet, inte från slutanvändarens enhet. Om alla enheter senare ska återansluta till tjänsten Enterprise State Roaming, synkroniseras inställningarna igen och lagras i Microsoft-molnet.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Enterprise Tillståndsväxling](enterprise-state-roaming-overview.md)
* [Inställningar och dataväxling vanliga frågor och svar](enterprise-state-roaming-faqs.md)
* [Gruppera princip och MDM-inställningar för synkronisering av inställningar](enterprise-state-roaming-group-policy-settings.md)
* [Centrala inställningsreferens för Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](enterprise-state-roaming-troubleshooting.md)
