---
title: Analysera användningsmönster för Azure CDN | Microsoft Docs
description: Den här artikeln beskrivs de olika typerna av analysrapporter som är tillgängliga för Azure CDN-produkter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593674"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster för Azure CDN

När du aktiverar CDN för ditt program kan du övervaka CDN-användning, kontrollerar hälsotillståndet för dina leverans och felsöka potentiella problem. Azure CDN ger de här funktionerna på följande sätt: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Grundläggande analys via Azure diagnostikloggar

Grundläggande analys är tillgänglig för CDN-slutpunkter för alla prisnivåer. Azure-diagnostikloggar Tillåt grundläggande analys som ska exporteras till Azure storage, event hubs eller Azure Monitor-loggar. Azure Monitor-loggar är en lösning med diagram som är användarangiven och anpassningsbara. Läs mer om Azure diagnostikloggar [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon core-rapporter

Som en Azure CDN-användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil, du kan visa Verizon core-rapporter i den kompletterande portalen Verizon. Verizon core-rapporter är tillgängliga via den **hantera** Azure-portalen och erbjuder en mängd olika diagram och vyer. Mer information finns i [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon anpassade rapporter

Som en Azure CDN-användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil, du kan visa Verizon anpassade rapporter i den kompletterande portalen Verizon. Verizon anpassade rapporter är tillgängliga via den **hantera** Azure-portalen. Verizon anpassade rapporter sidan visar antalet träffar eller data överfördes för var och en edge-CNAME-post som hör till en Azure CDN-profil. Data kan grupperas efter http-kod eller cache svarsstatusen över tid. Mer information finns i [anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium från Verizon rapporter

Med **Azure CDN Premium från Verizon**, du kan även använda följande rapporter:
   * [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
   * [Realtidsstatistik](cdn-real-time-stats.md)
   * [Gränsnodsprestanda](cdn-edge-performance.md)

