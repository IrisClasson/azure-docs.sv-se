---
title: storage-files-create-storage-account-portal
description: Skapa ett lagringskonto för Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: d4054760c77a7a70b7ed84a9f95b88a3bcf2bda3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76020981"
---
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

Skapa ett lagringskonto:

1. På den vänstra menyn väljer **+** du för att skapa en resurs.
2. Skriv **lagringskonto** i sökrutan, välj **Lagringskonto – blob, fil, tabell, kö** och klicka sedan på **Skapa**.
    ![En skärmbild av hur lagringskontoposten bör se ut i dialogrutan för resurssökning](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Skriv *mystorageacct* i **Namn**, följt av några slumptal tills du ser en grön bockmarkering som indikerar att det är ett unikt namn. Ett lagringskontonamn får bara bestå av gemener och måste vara globalt unikt. Anteckna namnet på ditt lagringskonto. Du ska använda det senare. 
4. I **distributions modell**lämnar du standardvärdet **Resource Manager**. Mer information om skillnaderna mellan Azure Resource Manager och den klassiska distributionsmodellen finns i [Understand deployment models and the state of your resources](../articles/azure-resource-manager/management/deployment-models.md) (Distributionsmodeller och dina resursers tillstånd).
5. Välj **StorageV2** i **Kontotyp**. Mer information om de olika typerna av lagringskonton finns i [Alternativ för Azure Storage-konton](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. Behåll standardvärdet för **Standardlagring** i **Prestanda**. Azure Files stöder för närvarande standardlagring. Även om du väljer Azure Premium Storage så lagras din filresurs med standardlagring.
7. Välj **lokalt redundant lagring (LRS)** i **Replikering**. 
8. Vi rekommenderar att du alltid väljer **Aktiverad** i **Säker överföring krävs**. Mer information om det här alternativet finns i [Understand encryption in-transit](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Kryptering under överföring).
9. Välj den prenumeration som användes för att skapa lagringskontot i **Prenumeration**. Om du bara har en prenumeration bör den vara standard.
10. I **Resursgrupp** väljer du **Skapa ny**. Ange *myResourceGroup* för namnet.
11. Välj **USA, östra** i **Plats**.
12. Lämna standardalternativet som **Inaktiverat** i **Virtuella nätverk**. 
13. Välj **Fäst på instrumentpanelen** så att det blir lättare att hitta lagringskontot.
14. När du är klar startar du distributionen genom att välja **Skapa**.