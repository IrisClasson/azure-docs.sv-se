---
title: Så här säkerställer du verifiering av Azure Database for MySQL data kryptering
description: Lär dig hur du verifierar krypteringen av kryptering av Azure Database for MySQL data med hjälp av den hanterade nyckeln för kunder.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 9cd1db0365cdc4f834516fd4efb0d80dacc75eca
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118536"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Verifierar data kryptering för Azure Database for MySQL

Den här artikeln hjälper dig att kontrol lera att data kryptering med kund hanterad nyckel för Azure Database for MySQL fungerar som förväntat.

## <a name="check-the-encryption-status"></a>Kontrol lera krypterings status

### <a name="from-portal"></a>Från portalen

1. Följ dessa steg om du vill kontrol lera att kundens nyckel används för kryptering:

    * I Azure Portal navigerar du till **Azure Key Vault**  ->  **nycklar**
    * Välj den nyckel som används för server kryptering.
    * Ange status för nyckeln som **är aktive rad** till **Nej**.
  
       Efter en stund (**~ 15 min**) ska Azure Database for MySQL Server **status** vara **otillgänglig**. Alla I/O-åtgärder som utförts mot servern Miss Miss kan verifiera att servern verkligen är krypterad med kund nyckeln och att nyckeln inte är giltig för tillfället.
    
       För att göra servern **tillgänglig** för kan du verifiera om nyckeln. 
    
    * Ange statusen för nyckeln i Key Vault till **Ja**.
    * På Server **data krypteringen**väljer du **revalidate Key**.
    * När nyckeln har återgiltigas ändras Server **statusen** till **tillgänglig**.

2. Om du kan se till att krypterings nyckeln har angetts på Azure Portal, krypteras data med hjälp av den kund nyckel som används i Azure Portal.

  ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Från CLI

1. Vi kan använda *AZ CLI* -kommandot för att verifiera de nyckel resurser som används för Azure Database for MySQL-servern.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    För en server utan data krypterings uppsättning resulterar detta kommando i tom uppsättning [].

### <a name="azure-audit-reports"></a>Azure audit-rapporter

[Gransknings rapporter](https://servicetrust.microsoft.com) kan också granskas som innehåller information om efterlevnaden av data skydds standarder och föreskrifter för regler.

## <a name="next-steps"></a>Nästa steg

Mer information om data kryptering finns i [Azure Database for MySQL data kryptering med kundhanterad nyckel](concepts-data-encryption-mysql.md).