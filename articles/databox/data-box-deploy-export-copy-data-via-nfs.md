---
title: Självstudie för att kopiera data från Azure Data Box via NFS | Microsoft Docs
description: Lär dig hur du kopierar data från din Azure Data Box via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 301c75df6bedf430af64bbeff63f2eb759691355
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210478"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>Självstudie: kopiera data från Azure Data Box via NFS (för hands version)

I den här självstudien beskrivs hur du ansluter till och kopierar data från det lokala webb gränssnittet för din Data Box-enhet till en lokal data server via NFS. Data på din Data Box-enhet exporteras från ditt Azure Storage-konto.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Förutsättningar
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har placerat ordern för Azure Data Box.
    - En import ordning finns i [Självstudier: beställ Azure Data Box](data-box-deploy-ordered.md).
    - En export ordning finns i [Självstudier: beställ Azure Data Box](data-box-deploy-export-ordered.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har en värddator som du vill kopiera data från Data Box-enhet. Värddatorn måste
   * Köra ett [operativsystem som stöds](data-box-system-requirements.md).
   * Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas.

## <a name="connect-to-data-box"></a>Ansluta till Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Om du använder en Linux-värddator utför du stegen nedan för att konfigurera Data Box att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**. 

    ![Konfigurera NFS-klientåtkomst 1](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution. 

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-resurs. Data Box-enhetens IP-adress är `10.161.23.130`, resursen `Mystoracct_Blob` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    För Mac-klienter måste du lägga till ytterligare ett alternativ på följande sätt: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

## <a name="copy-data-from-data-box"></a>Kopiera data från Data Box-enhet

När du är ansluten till Data Box-resurser är nästa steg att kopiera data.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Nu kan du börja kopiera data. Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av alternativen som är tillgängliga i Linux är [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

Om du använder rsync-alternativet för en flertrådig kopia följer du dessa riktlinjer:

* Installera **CIFS Utils**- eller **NFS Utils**-paketet, beroende på vilket filsystem din Linux-klient använder.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installera **rsync** och **Parallel** (varierar beroende på den distribuerade Linux-versionen).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Skapa en monteringspunkt.

    `sudo mkdir /mnt/databox`

* Montera volymen.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Spegla mappkatalogstrukturen.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Kopiera filerna.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     där j anger antal parallelliseringar, X = antal parallella kopior

     Vi rekommenderar att du börjar med 16 parallella kopior och öka antalet trådar beroende på tillgängliga resurser.

> [!IMPORTANT]
> Följande Linux-filtyper stöds inte: symboliska länkar, paketfiler, blockera filer, Sockets och pipes. Dessa filtyper resulterar i problem under **Förbered för att skicka** steget.

När kopieringen är klar går du till **instrument panelen** och kontrollerar det använda utrymmet och det lediga utrymmet på enheten.

Nu kan du fortsätta med att leverera Data Box-enhet till Microsoft.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-export-picked-up.md)
