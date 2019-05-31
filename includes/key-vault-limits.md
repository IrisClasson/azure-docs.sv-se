---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238825"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transaktioner-nyckeln (maximalt antal transaktioner tillåts om 10 sekunder per valv per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>SKAPA nyckel|HSM-nyckel<br>Alla andra transaktioner|Programvarunyckel<br>SKAPA nyckel|Programvarunyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2 048-bitars|5|1,000|10|2,000|
|RSA 3 072-bitars|5|250|10|500|
|RSA 4 096-bitars|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> I föregående tabell ser vi att 2 000 GET transaktioner per 10 sekunder är tillåtna för RSA 2 048-bitars programvarunycklar. 1 000 GET transaktioner per 10 sekunder är tillåtna för RSA 2 048-bitars HSM-nycklar.
>
> Tröskelvärden för begränsningar viktas och tvingande finns i deras summa. Till exempel som visas i föregående tabell är när du utför du GET-åtgärder med HSM-RSA-nycklar, det åtta gånger dyrare att använda 4 096-bitars nycklar jämfört med 2 048-bitars nycklar. Det beror på 1 000/125 = 8.
>
> I ett givet intervall om 10 sekunder, en Azure Key Vault-klient kan göra *endast en* av följande åtgärder innan Övervakaren påträffar en `429` begränsning HTTP-statuskod:
> - 2 000 RSA 2 048-bitars programvarunyckel GET-transaktioner
> - 1 000 transaktioner för RSA 2 048-bitars HSM-nyckel GET
> - 125 RSA 4 096-bitars HSM-nyckel GET-transaktioner
> - 124 RSA 4 096-bitars HSM-nyckel GET-transaktioner och 8 RSA 2 048-bitars HSM-nyckel GET-transaktioner

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Hemligheter, hanterade nycklar för lagringskonton och vault-transaktioner:
| Transaktioner-typ | Maximalt antal transaktioner tillåts om 10 sekunder per valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2,000 |

Information om hur du hanterar begränsning när gränserna överskrids finns i [Azure Key Vault-begränsning vägledning](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> en prenumeration hela gräns för alla transaktionstyper är fem gånger per nyckelvalv gränsen. HSM-andra transaktioner per prenumeration är till exempel begränsad till 5 000 transaktioner om 10 sekunder per prenumeration.
