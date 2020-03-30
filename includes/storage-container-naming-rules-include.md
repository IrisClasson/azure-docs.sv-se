---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187958"
---
Varje blob i Azure-lagring måste befinna sig i en container. Containern utgör en del av blobnamnet. ph x="1" /&gt; är till exempel namnet på containern i de här exempelblob-URI:erna:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Ett containernamn måste vara ett giltigt DNS-namn som överensstämmer med följande namngivningsregler:

1. Containernamnet måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).
2. Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. Flera bindestreck i följd är inte tillåtna i containernamn.
3. Alla bokstäver i ett containernamn måste vara gemener.
4. Containernamn måste vara mellan 3 och 63 tecken långa.

> [!IMPORTANT]
> Observera att containernamn får innehålla endast gemener. Om du tar med en versal i ett containernamn, eller på annat sätt bryter mot namngivningsreglerna, kan du få ett 400-fel (felaktig begäran). 
> 
> 

