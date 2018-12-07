---
title: Begränsningar och kända problem i Azure API Management API import | Microsoft Docs
description: Information om kända problem och begränsningar för import till Azure API Management med hjälp av öppna API, WSDL eller WADL-format.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: c55a80749506b0a03af2f8c5f0179b67c8a78d15
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016750"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importrestriktioner och kända problem
## <a name="about-this-list"></a>Om den här listan
När du importerar ett API, kan du stöta på vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. Den här artikeln dokument dessa, ordnade efter importformatet för API: et.

## <a name="open-api"> </a>OpenAPI/Swagger
Om du får fel importera OpenAPI-dokument, se till att du har godkänt den – antingen med hjälp av designern i Azure portal (Design - Front End - OpenAPI-Specifikationsredigerare), eller med en tredjeparts-verktyget som <a href="https://editor.swagger.io">Swagger Editor</a>.

* Endast JSON-format för OpenAPI stöds.
* Obligatoriska parametrar i både sökvägen och frågan måste ha unika namn. (I OpenAPI ett parameternamn endast måste vara unika inom en plats, t.ex. sökväg, fråga, rubrik.  Vi kan dock tillåta åtgärder för att vara discriminated efter både sökvägen och frågan parametrar (som inte har stöd för OpenAPI) i API Management. Därför kräver vi parametern som ska vara unikt inom mallen hela URL: en.)
* Scheman som refereras till med hjälp av **$ref** egenskaper får inte innehålla andra **$ref** egenskaper.
* **$ref** pekare kan inte referera till de externa filerna.
* **x-ms-sökvägar** och **x-servers** är de enda tillägg som stöds.
* Anpassade tillägg ignoreras för import och inte sparas eller bevaras för export.
* **Rekursion** -definitioner som är definierade rekursivt (exempelvis referera till sig själva) stöds inte av APIM.

> [!IMPORTANT]
> Det här [dokumentet](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) innehåller viktig information och tips om OpenAPI-import.

## <a name="wsdl"> </a>WSDL
WSDL-filer används för att generera API: er för SOAP-direkt eller fungera som serverdelen för ett SOAP till REST-API.
* **SOAP-bindningar** -endast SOAP-bindningar style ”dokument” och ”literal”-kodning stöds. Det finns inget stöd för ”RPC”-format eller SOAP-kodning.
* **Platsattribut** – det här attributet stöds inte. Kunder bör samman importen till ett dokument.
* **Meddelanden med flera delar** -dessa typer av meddelanden stöds inte.
* **WCF-wsHttpBinding** -SOAP-tjänster som skapats med Windows Communication Foundation bör använda basicHttpBinding – wsHttpBinding stöds inte.
* **MTOM** - tjänster med hjälp av MTOM <em>kan</em> fungerar. Officiella support erbjuds inte just nu.
* **Rekursion** -typer som är definierade rekursivt (till exempel finns i en matris med själva) stöds inte av APIM.

## <a name="wadl"> </a>WADL
Det finns för närvarande inga kända problem i WADL import.
