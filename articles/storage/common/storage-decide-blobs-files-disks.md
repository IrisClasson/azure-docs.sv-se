---
title: Avgöra när du ska använda Azure Blobs, Azure Files eller Azure-diskar
description: Läs mer om de olika sätten att lagra och komma åt data i Azure för att du bestämmer dig för vilken teknik du använder.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30c7c1c50e59162817d7cfab0d852d8e034457d0
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969417"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Avgöra när du ska använda Azure Blobs, Azure Files eller Azure-diskar

Microsoft Azure tillhandahåller flera funktioner i Azure Storage för att lagra och komma åt dina data i molnet. Den här artikeln täcker Azure Files, BLOB-objekt och diskar och har utformats för att hjälpa dig att välja mellan de här funktionerna.

## <a name="scenarios"></a>Scenarier

I följande tabell jämförs filer, Blobbar och diskar och visar exempelscenarier som passar bäst för respektive.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure Files** | Tillhandahåller ett gränssnitt för SMB, klientbibliotek och en [REST-gränssnittet](/rest/api/storageservices/file-service-rest-api) som tillåter åtkomst överallt till lagrade filer. | Du vill ”lift and shift” ett program till molnet som redan använder filsystemet interna API: er för att dela data mellan dem och andra program som körs i Azure.<br/><br/>Du vill lagra utvecklings- och felsökningsverktyg som måste kunna nås från många virtuella datorer. |
| **Azure Blobs** | Tillhandahåller klientbibliotek och en [REST-gränssnittet](/rest/api/storageservices/blob-service-rest-api) som gör att Ostrukturerade data kan lagras och används i massiv skala i blockblobar.<br/><br/>Stöder också [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) för enterprise lösningar för analys av stordata. | Du vill att program som stöder strömning och scenarier för direktåtkomst.<br/><br/>Du vill kunna komma åt programdata från var som helst.<br/><br/>Du vill skapa ett data lake för enterprise på Azure och utföra analyser av stordata. |
| **Azure Disks** | Tillhandahåller klientbibliotek och en [REST-gränssnittet](/rest/api/compute/manageddisks/disks/disks-rest-api) som gör att data kan lagras beständigt och nås från en ansluten virtuell hårddisk. | Du vill flytta över program som använder interna filsystemet API: er för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte behövs för åtkomst från utanför den virtuella datorn som disken är ansluten. |

## <a name="comparison-files-and-blobs"></a>Jämförelse: Filer och Blobar

I följande tabell jämförs Azure Files med Azure Blobs.  
  
||||  
|-|-|-|  
|**Attributet**|**Azure Blobs**|**Azure Files**|  
|Alternativ för hållbarhet|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Hjälpmedel|REST-API:er|REST-API:er<br /><br /> SMB 2.1 och SMB 3.0 (standardfilsystemet API: er)|  
|Anslutningar|REST API: er, över hela världen|REST API: er – i hela världen<br /><br /> SMB 2.1 – i region<br /><br /> SMB 3.0 -- Worldwide|  
|Slutpunkter|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Kataloger|Ostrukturerad namnrymd|SANT katalogobjekt|  
|Skiftlägeskänslighet i namn|Skiftlägeskänslig|Skiftlägeskänsliga, men bevarar användningsfall|  
|Kapacitet|Upp till 2 PiB konto gräns |5 TiB-filresurser|  
|Dataflöde|Upp till 60 MiB/s per blockblob|Upp till 60 MiB/s per resurs|  
|Objektstorlek|Upp till ungefär 4,75 TiB per blockblob|Upp till 1 TiB per fil|  
|Fakturerade kapacitet|Baserat på skrivna byte|Baserat på filstorlek|  
|Klientbibliotek|Flera språk|Flera språk|  
  
## <a name="comparison-files-and-disks"></a>Jämförelse: Filer och diskar

Azure Files kompletterar Azure-diskar. En disk kan endast kopplas till en Azure-dator i taget. Diskar är fast format virtuella hårddiskar lagras som sidblobar i Azure Storage och används av den virtuella datorn för att lagra beständiga data. Filresurser i Azure Files kan nås på samma sätt som den lokala disken används (med hjälp av inbyggda filsystem API: er) och kan delas mellan många virtuella datorer.  

I följande tabell jämförs Azure Files med Azure-diskar.  

||||  
|-|-|-|  
|**Attributet**|**Azure Disks**|**Azure Files**|  
|Scope|Exklusivt för en enskild virtuell dator|Delad åtkomst över flera virtuella datorer|  
|Ögonblicksbilder och kopiera|Ja|Ja|  
|Konfiguration|Ansluten vid start av den virtuella datorn|Ansluta till efter den virtuella datorn har startats|  
|Autentisering|Inbyggt|Konfigurera med net use|  
|Åtkomst med hjälp av REST|Filer i den virtuella Hårddisken kan inte nås|Filer som lagras på en resurs kan nås|  
|Maxstorlek|32 TiB-disk|5 TiB-filresurser och 1 TiB-fil i resursen|  
|Maximalt antal IOps|20 000 IOps|1 000 IOps|  
|Dataflöde|Upp till 900 MiB/s per Disk|Målet är 60 MiB/s per filresurs (hämta högre för högre i/o-storlekar)|  

## <a name="next-steps"></a>Nästa steg

När du fattar beslut om hur dina data lagras och nås, kan du också kostnaderna som ingår. Mer information finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Vissa SMB-funktioner kan inte användas för molnet. Mer information finns i [funktioner som inte stöds av Azure-filtjänsten](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Mer information om diskar finns i vår [introduktion till hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md) och [hur du ansluter en datadisk till en Windows-dator](../../virtual-machines/windows/attach-managed-disk-portal.md).
