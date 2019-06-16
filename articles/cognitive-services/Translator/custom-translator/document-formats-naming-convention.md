---
title: Dokumentformat och namngivningskonventioner - anpassad Translator
titleSuffix: Azure Cognitive Services
description: Det här är en översikt över dokumentformat och namngivningskonvention i anpassade Translator. Detta begrepp hjälper dig för att hantera dokument namn bättre abd undvika namnkonflikter.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 29b74aeaaae0bcfd5f6ae4a3c38cc00176292899
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66386916"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Dokumentera format och naming convention vägledning

Alla filer som används för anpassade översättning måste vara minst **fyra** tecken långt.

Den här tabellen innehåller alla filformat som stöds som du kan använda för att skapa din översättningssystemet:

| Format            | Tillägg   | Beskrivning                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | En parallell dokumentformat, export av översättningsminnen system. De språk som används definieras i filen.                                                                                                                                                              |
| TMX               | .TMX         | En parallell dokumentformat, export av översättningsminnen system. De språk som används definieras i filen.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP är ett arkivfilformat.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Ett Microsoft-format för parallell dokument                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Microsoft Word-dokument                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Adobe Acrobat bärbar dokumentet                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | HTML-dokument                                                                                                                                                                                                                                                                  |
| Textfil         | .TXT         | UTF-16- eller UTF-8-kodade textfiler. Filnamnet får inte innehålla japanska tecken.                                                                                                                                                                                        |
| Justerade textfil | .ALIGN       | Tillägget `.ALIGN` är ett särskilt tillägg som du kan använda om du vet att meningar i dokumentet paret perfekt justerad. Om du anger en `.ALIGN` fil, anpassade Translator inte anpassas meningarna åt dig. |
| Excel-fil        | .XLSX        | Excel-fil (2013 eller senare). Första raden / raden i kalkylbladet bör vara språkkod.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Ordlista format

Ordlistor stöder anpassade Translator alla filformat som de som stöds för träningsmängden. Om du använder Excel ordlista, kontrollera att den första raden / raden i kalkylbladet ska vara språkkoder.

## <a name="zip-file-formats"></a>ZIP-filformat

Dokument kan grupperas i en enda zip-fil och laddat upp. Anpassad Translator stöder zip-format (ZIP GZ och TGZ).

Varje dokument i zip-filen med tillägget TXT, HTML, HTM, PDF, DOCX, JUSTERA måste följande namnkonvention:

{dokumentnamn} \_{språkkod} där {dokumentnamn} är namnet på dokumentet, {språkkod} är ISO LanguageID (två tecken), som anger att dokumentet innehåller meningar på det språket. Det måste finnas ett understreck (_) innan språkkoden.

Till exempel för att ladda upp två parallella dokumenten i en zip för en engelska till spanska system ska filerna ha namnet ”data_en” och ”data_es”.

Minne översättningsfiler (TMX, XLF, XLIFF, LCL, XLSX) behöver inte följa namngivningskonventionen, specifikt språk.  

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [projekt](workspace-and-project.md#what-is-a-custom-translator-project) att skapa och hantera dem.
