---
title: Felsöka data kryptering – Azure Database for MySQL
description: Lär dig hur du felsöker data kryptering i Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 8fba55dcca46b313c7b9a847412615215ad57c72
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118587"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Felsöka data kryptering i Azure Database for MySQL

Den här artikeln beskriver hur du identifierar och löser vanliga problem som kan uppstå i Azure Database for MySQL när de konfigureras med data kryptering med hjälp av en kundhanterad nyckel.

## <a name="introduction"></a>Introduktion

När du konfigurerar data kryptering för att använda en kundhanterad nyckel i Azure Key Vault kräver servrar kontinuerlig åtkomst till nyckeln. Om servern förlorar åtkomsten till den Kundhanterade nyckeln i Azure Key Vault nekar den alla anslutningar, returnerar lämpligt fel meddelande och ändrar dess tillstånd till ***otillgängligt*** i Azure Portal.

Om du inte längre behöver en oåtkomlig Azure Database for MySQL-server kan du ta bort den för att stoppa kostnaderna. Inga andra åtgärder på servern tillåts förrän åtkomst till nyckel valvet har återställts och servern är tillgänglig. Det går inte heller att ändra data krypterings alternativet från `Yes` (kundhanterad) till `No` (service-hanterat) på en oåtkomlig server när den är krypterad med en kundhanterad nyckel. Du måste validera nyckeln manuellt innan servern kan nås igen. Den här åtgärden är nödvändig för att skydda data från obehörig åtkomst medan behörigheter till kundhanterad nyckel återkallas.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Vanliga fel som gör att servern blir otillgänglig

Följande fel konfigurationer orsakar de flesta problem med data kryptering som använder Azure Key Vault nycklar:

- Nyckel valvet är inte tillgängligt eller finns inte:
  - Nyckel valvet har tagits bort av misstag.
  - Ett tillfälligt nätverks fel gör att nyckel valvet inte är tillgängligt.

- Du har inte behörighet att komma åt nyckel valvet eller så finns inte nyckeln:
  - Nyckeln har gått ut eller tagits bort av misstag eller inaktiverats av misstag.
  - Den hanterade identiteten för den Azure Database for MySQL instansen har tagits bort av misstag.
  - Den hanterade identiteten för Azure Database for MySQL-instansen har otillräckliga nyckel behörigheter. Behörigheterna omfattar till exempel inte hämta, Radbryt och packa upp.
  - Hanterade identitets behörigheter till Azure Database for MySQL-instansen har återkallats eller tagits bort.

## <a name="identify-and-resolve-common-errors"></a>Identifiera och lösa vanliga fel

### <a name="errors-on-the-key-vault"></a>Fel i nyckel valvet

#### <a name="disabled-key-vault"></a>Inaktiverat nyckel valv

- `AzureKeyVaultKeyDisabledMessage`
- **Förklaring**: det gick inte att slutföra åtgärden på servern på grund av att Azure Key Vault nyckeln är inaktive rad.

#### <a name="missing-key-vault-permissions"></a>Nyckel valv saknar behörigheter

- `AzureKeyVaultMissingPermissionsMessage`
- **Förklaring**: servern har inte behörighet att hämta, flytta och packa upp Azure Key Vault. Bevilja alla saknade behörigheter till tjänstens huvud namn med ID.

### <a name="mitigation"></a>Åtgärd

- Bekräfta att den Kundhanterade nyckeln finns i nyckel valvet.
- Identifiera nyckel valvet och gå sedan till nyckel valvet i Azure Portal.
- Kontrol lera att nyckel-URI: n identifierar en nyckel som finns.

## <a name="next-steps"></a>Nästa steg

[Använd Azure Portal för att konfigurera data kryptering med en kundhanterad nyckel på Azure Database for MySQL](howto-data-encryption-portal.md)
