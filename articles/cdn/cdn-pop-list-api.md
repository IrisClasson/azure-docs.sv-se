---
title: Hämta den aktuella Verizon POP-listan för Azure CDN | Microsoft Docs
description: Lär dig hur du hämtar den aktuella Verizon POP-listan med hjälp av REST-API.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c8316b994dac6b859f019bea1aac6b6a5c2c5b2d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593568"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Hämta den aktuella Verizon POP-listan för Azure CDN

Du kan använda REST API för att hämta uppsättning IP-adresser för Verizons närvaro (POP) servrar. Dessa POP-servrar gör förfrågningar till ursprungsservrar som är associerade med Azure Content Delivery Network (CDN) slutpunkter på en Verizon-profil (**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**). Observera att den här uppsättningen med IP-adresser skiljer sig från IP-adresser som en klient skulle se vid begäranden till POP: erna. 

Syntaxen för REST API-åtgärd för hämtning av POP-listan, se [Kantnoder - listan](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Vanligt användningsfall

Du kan använda den här IP-listan för att genomdriva att begäranden till ursprungsservern görs endast från en giltig Verizon POP av säkerhetsskäl. Om någon identifieras värdnamnet eller IP-adress för en CDN-slutpunkt ursprungsservern, göra någon till exempel begäranden direkt till ursprungsservern därför kringgår den skalning och säkerhetsfunktioner som tillhandahålls av Azure CDN. Det här scenariot kan förhindras genom att ange de IP-adresserna i den returnerade listan som den enda tillåtna IP-adresser på en ursprungsserver. För att säkerställa att du har den senaste POP-listan, hämta minst en gång om dagen. 

## <a name="next-steps"></a>Nästa steg

Information om REST API finns i [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
