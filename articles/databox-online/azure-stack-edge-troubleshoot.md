---
title: Använd Azure Portal för att felsöka Azure Stack Edge | Microsoft Docs
description: Lär dig hur du felsöker problem på din Azure Stack Edge. Du kan köra diagnostik, samla in information för support och använda loggar för att felsöka.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: f9dabdfd9df77822f06c41fa4355a1657bceb76c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134572"
---
# <a name="troubleshoot-your-azure-stack-edge-issues"></a>Felsök problem med Azure Stack Edge

I den här artikeln beskrivs hur du felsöker problem på din Azure Stack Edge. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Kör diagnostik
> * Hämta supportpaket
> * Använd loggar för att felsöka

## <a name="run-diagnostics"></a>Kör diagnostik

Du kan köra de diagnostiska testerna för att diagnostisera och felsöka eventuella enhetsfel. Utför följande steg i det lokala webbgränssnittet på din enhet för att köra diagnostiska tester.

1. I det lokala webbgränssnittet går du till **Felsökning > Diagnostiska tester**. Välj det test du vill köra och välj **Kör test**. Tester för att diagnostisera möjliga problem med nätverk, enhet, webbproxy, tid eller molninställningar körs då. Du får ett meddelande om att enheten kör tester.

    ![Välj test](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. När testerna har slutförts visas resultatet.

    ![Granska test resultat](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Om testet inte kan genomföras visas en URL för rekommenderad åtgärd. Välj URL: en för att visa den rekommenderade åtgärden.

    ![Granska varningar för misslyckade tester](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Hämta supportpaket

Ett loggpaket består av alla relevanta loggar som kan hjälpa Microsoft Support att felsöka eventuella enhetsproblem. Du kan generera ett loggpaket via det lokala webbgränssnittet.

Utför följande steg för att hämta ett supportpaket.

1. I det lokala webbgränssnittet går du till **Felsökning > Support**. Välj **skapa support paket**. Systemet börjar samla in supportpaket. Paketinsamlingen kan ta flera minuter.

    ![Klicka på lägg till användare](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. När du har skapat support paketet väljer du **Hämta support paket**. Ett komprimerat paket har laddats ned till den sökväg som du har valt. Du kan extrahera paketet och visa systemloggfilerna.

    ![Klicka på lägg till användare](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Använd loggar för att felsöka

Eventuella fel som uppstod under uppladdnings- och uppdateringsprocesserna ingår i respektive felfiler.

1. Om du vill visa felfilerna går du till resursen och väljer den för att visa innehållet. 

      ![Ansluta till och Visa resurs innehåll](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Välj _mappen Microsoft Azure Stack Edge_. Den här mappen innehåller två undermappar:

    * Ladda upp mappen som innehåller loggfiler för uppladdningsfelen.
    * Uppdatera mappen för fel som uppstått under uppdateringen.

    Här är en exempelloggfil för uppdatering.

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. När du ser ett fel i den här filen (som är markerat i exemplet) noterar du felkoden, i det här fallet är den 16001. Leta upp beskrivningen av den här felkoden mot följande felkodsreferens.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="next-steps"></a>Nästa steg

* Läs mer om [kända problem i den här versionen](data-box-gateway-release-notes.md).
