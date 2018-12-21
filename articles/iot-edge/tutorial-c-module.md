---
title: Självstudie om att skapa anpassad C-modul – Azure IoT Edge | Microsoft Docs
description: Den här självstudien beskriver hur du skapar en IoT Edge-modul med C-kod och distribuerar den till en gränsenhet.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f1fdffe7b5cd217c61864328830d1eba2cbdc42b
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340733"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Självstudie: Utveckla en C IoT Edge-modul och distribuera till den simulerade enheten

Du kan använda IoT Edge-moduler till att distribuera kod som implementerar din affärslogik direkt på dina IoT Edge-enheter. Den här självstudien vägleder dig genom att skapa och distribuera en IoT Edge-modul som filtrerar sensordata. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använda Visual Studio Code för att skapa en IoT Edge-modul i C
> * Använda Visual Studio Code och Docker för att skapa en Docker-avbildning och publicera den till ett containerregister
> * Distribuera modulen till din IoT Edge-enhet
> * Visa genererade data


IoT Edge-modulen som du skapar i den här självstudien filtrerar temperaturdata som genereras av enheten. Den skickar enbart meddelanden uppströms om temperaturen överskrider ett angivet tröskelvärde. Den här typen av analys är användbar för att minska mängden data som kommuniceras till och lagras i molnet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* C-moduler för Azure IoT Edge har inte stöd för Windows-containrar. Om din IoT Edge-enhet är en Windows-dator konfigurerar du den till att [använda Linux-containrar](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.

Utvecklingsresurser:

* [Visual Studio Code](https://code.visualstudio.com/).
* [C/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) för Visual Studio Code.
* [Azure IoT Edge-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) för Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

>[!Note]
>C-moduler för Azure IoT Edge har inte stöd för Windows-containrar.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du Azure IoT Edge-tillägget för Visual Studio Code för att skapa en modul och skapa en **containeravbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry.

Om du inte redan har ett containerregister följer du dessa steg för att skapa ett nytt i Azure:

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Registernamn | Ange ett unikt namn. |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. |
   | Administratörsanvändare | Ändra värdet till **Aktivera**. |
   | SKU | Välj **Grundläggande**. |

5. Välj **Skapa**.

6. När du har skapat containerregistret går du till det och väljer **Åtkomstnycklar**.

7. Kopiera värdena för **Inloggningsserver**, **Användarnamn** och **Lösenord**. Du kan använda dessa värden senare i självstudien för att ge åtkomst till containerregistret.

## <a name="create-an-iot-edge-module-project"></a>Skapa ett projekt för IoT Edge-modulen
Följande steg visar hur du skapar ett IoT Edge-modulprojekt som baseras på .NET Core 2.0 med Visual Studio Code och Azure IoT Edge-tillägget.

### <a name="create-a-new-solution"></a>Skapa en ny lösning

Skapa en C-lösningsmall som du kan anpassa med din egen kod.

1. Välj **Visa** > **Kommandopalett** för att öppna kommandopaletten i VS Code.

2. I kommandopaletten skriver du och kör kommandot **Azure: Logga in** och följer anvisningarna för att logga in på ditt Azure-konto. Om du redan har loggat in kan du hoppa över det här steget.

3. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**. Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C-modul**. |
   | Ange ett modulnamn | Ge modulen namnet **CModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. Den slutliga strängen ser ut så här: \<registernamn\>.azurecr.io/cmodule. |
 
   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-c-module/repository.png)

VS Code läser in arbetsytan för IoT Edge-lösningen. Lösningens arbetsyta innehåller fem komponenter på den högsta nivån. Mappen **modules** innehåller C-koden för din modul samt Dockerfiles som används för att skapa modulen som en containeravbildning. Filen **\.env** lagrar dina autentiseringsuppgifter för containerregistret. Filen **deployment.template.json** innehåller informationen som IoT Edge-körningen använder för att distribuera moduler på en enhet. Och filen **deployment.debug.template.json** innehåller felsökningsversionen av modulerna. Du kommer inte att redigera mappen **\.vscode** eller filen **\.gitignore** i den här självstudiekursen.

Om du inte angav ett containerregister när du skapade lösningen, men accepterade standardvärdet localhost:5000, har du ingen \.env-fil.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara filen.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

Lägg till kod i din C-modul som gör att den kan läsa data från sensorn, kontrollera om den rapporterade datortemperaturen har överskridit ett säkerhetströskelvärde och skicka informationen till IoT Hub.

5. Data från sensorn i det här scenariot kommer i JSON-format. Om du vill filtrera meddelanden i JSON-format importerar du ett JSON-bibliotek för C. Den här självstudien använder Parson.

   1. Ladda ned den [Parson GitHub-lagringsplatsen](https://github.com/kgabis/parson). Kopiera filerna **parson.c** och **parson.h** till mappen **CModule**.

   2. Öppna **modules** > **CModule** > **CMakeLists.txt**. Längst upp i filen importerar du Parson-filerna som ett bibliotek med namnet **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Lägg till **my_parson** i listan över bibliotek i funktionen **target_link_libraries** i CMakeLists.txt.

   4. Spara filen **CMakeLists.txt**.

   5. Öppna **modules** > **CModule** > **main.c**. Längst ned i listan med include-instruktioner lägger du till ett nytt som ska innehålla `parson.h` för JSON-stöd:

      ```c
      #include "parson.h"
      ```

6. I filen **main.c** lägger du till en global variabel med namnet `temperatureThreshold` efter include-avsnittet. Variabeln anger det värde som den uppmätta temperaturen måste överstiga för att data ska skickas till IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

7. Ersätt hela funktionen `CreateMessageInstance` med följande kod. Den här funktionen allokerar en kontext för återanropet.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Ersätt hela funktionen `InputQueue1Callback` med följande kod. Den här funktionen implementerar själva meddelandefiltret.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Lägg till en `moduleTwinCallback`-funktion. Metoden tar emot uppdateringar av önskade egenskaper från modultvillingen och uppdaterar variabeln **temperatureThreshold** som ska matchas. Alla moduler har en egen modultvilling, vilket innebär att du kan konfigurera den kod som körs i en modul direkt från molnet.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Ersätt funktionen `SetupCallbacksForModule` med följande kod.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Spara filen **main.c**.

12. I VS Code-utforskaren öppnar du filen **deployment.template.json** i arbetsytan för IoT Edge-lösningen. Den här filen instruerar `$edgeAgent` att distribuera två moduler: **tempSensor** och **CModule**. Standardplattformen för din IoT Edge är inställd på **amd64** i VS Code-statusfältet, vilket innebär att **NodeModule** är inställd på Linux amd64-versionen för avbildningen. Ändra standardplattformen i statusfältet från **amd64** till **arm32v7** om det är arkitekturen för din IoT Edge-enhet. Läs mer om distributionsmanifest i avsnittet om att [förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

13. Lägg till CModule-modultvillingen till distributionsmanifestet. Infoga följande JSON-innehåll längst ned i avsnittet `moduleContent` efter `$edgeHub`-modultvillingen:

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Lägga till CModule-tvilling till distributionsmall](./media/tutorial-c-module/module-twin.png)

14. Spara filen **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

I föregående avsnitt skapade du en IoT Edge-lösning och lade till kod till CModule som filtrerar ut meddelanden om att temperaturen för den rapporterade datorn ligger inom det godkända intervallet. Nu behöver du skapa lösningen som en containeravbildning och push-överföra den till ditt containerregister.

1. Öppna den integrerade VS Code-terminalen genom att välja **Visa** > **Integrerad terminal**.

1. Logga in på Docker genom att ange följande kommando i den integrerade Visual Studio Code-terminalen: Du måste logga in med dina Azure Container Registry-autentiseringsuppgifter så att du kan skicka din modulavbildning till registret.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Använd användarnamnet, lösenordet och inloggningsservern som du kopierade från Azure Container Registry i det första avsnittet. Eller hämta dem igen från avsnittet **Åtkomstnycklar** i ditt register i Azure Portal.

2. I VS Code-utforskaren högerklickar du på filen **deployment.template.json** och väljer **Build and Push IoT Edge solution** (Skapa och skicka IoT Edge-lösning).

När du ger Visual Studio Code kommando att skapa din lösning genererar det först en `deployment.json`-fil i en ny **config**-mapp. Informationen för filen deployment.json hämtas från den mallfil som du uppdaterade, den .env-fil som du använde för att lagra dina autentiseringsuppgifter för containerregister samt module.json-filen i CModule-mappen.

Sedan kör Visual Studio Code två kommandon i den integrerade terminalen: `docker build` och `docker push`. Dessa två kommandon skapar koden, lägger `CModule.dll` i behållare och push-överför till det containerregister som du angav när du initierade lösningen.

Den fullständiga adressen med tagg för containeravbildningen finns i den integrerade VS Code-terminalen. Avbildningsadressen skapas utifrån information i filen `module.json` med formatet **\<lagringsplats\>:\<version\>-\<plattform\>**. I den här självstudien bör den se ut så här: **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Distribuera och kör lösningen

I stegen i snabbstartsartikeln som du följde för att konfigurera IoT Edge-enheten distribuerade du en modul med hjälp av Azure Portal. Du kan även distribuera moduler med hjälp av Azure IoT Hub Toolkit-tillägget (tidigare Azure IoT Toolkit-tillägget) för Visual Studio Code. Du har redan ett distributionsmanifest som är förberett för ditt scenario, filen **deployment.json**. Allt du behöver göra nu är att välja en enhet som ska ta emot distributionen.

1. I kommandopaletten i VS Code kör du **Azure IoT Hub: Välj IoT-hubb**.

2. Välj den prenumeration och IoT-hubb som innehåller den IoT Edge-enhet som du vill konfigurera.

3. I VS Code-utforskaren expanderar du avsnittet **Azure IoT Hub-enheter**.

4. Högerklicka på namnet för din IoT Edge-enhet och välj sedan **Create Deployment for Single Device** (Skapa distribution för en enskild enhet).

   ![Skapa distribution för en enskild enhet](./media/tutorial-c-module/create-deployment.png)

5. Välj filen **deployment.json** i mappen **config** och klicka sedan på **Select Edge Deployment Manifest** (Välj distributionsmanifest för Edge). Använd inte filen deployment.template.json.

6. Klicka på uppdateringsknappen. Den nya **CModule**-modulen visas och körs tillsammans med **TempSensor**-modulen och **$edgeAgent** och **$edgeHub**.

## <a name="view-generated-data"></a>Visa genererade data

När du tillämpar distributionsmanifestet till din IoT Edge-enhet samlar IoT Edge-körningen in den nya distributionsinformationen och börjar köra på den. Alla moduler som körs på enheten som inte finns med i distributionsmanifestet har stoppats. Alla moduler som saknas från enheten startas.

Du kan visa statusen för din IoT Edge-enhet i avsnittet om **Azure IoT Hub-enheter** i Visual Studio Code-utforskaren. Expandera enhetsinformationen så ser du en lista med moduler som distribueras och körs.

Med hjälp av kommandot `iotedge list` på själva IoT Edge-enheten kan du se statusen för dina distributionsmoduler. Du bör se fyra moduler: de modulerna för IoT Edge-körning, tempSensor och den anpassade modul du skapade i den här självstudien. Det kan ta några minuter för alla moduler att starta, så kör kommandot igen om du till en början inte ser alla.

Du kan visa de meddelanden som genereras av alla moduler med hjälp av kommandot `iotedge logs <module name>`.

Du kan visa meddelanden när de anländer till IoT-hubben med hjälp av Visual Studio Code.

1. Om du vill övervaka data som inkommer till IoT Hub klickar du på **...** och väljer **Starta övervakning av D2C-meddelanden**.
2. Om du vill övervaka D2C-meddelandet för en specifik enhet högerklickar du på enheten i listan och väljer **Starta övervakning av D2C-meddelanden**.
3. Om du vill stoppa övervakningen av data kör du kommandot **Azure IoT Hub: Sluta övervaka D2C-meddelande** i kommandopaletten.
4. Om du vill visa eller uppdatera modultvillingen högerklickar du på modulen i listan och väljer **Redigera modultvilling**. Om du vill uppdatera modultvillingen sparar du tvilling-JSON-filen och högerklickar på redigeringsområdet och väljer **Uppdatera modultvilling**.
5. Om du vill visa Docker-loggar kan du installera [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) för VS Code och se moduler som körs lokalt i Docker-utforskaren. I kontextmenyn klickar du på **Visa loggar** för att visa i den integrerade terminalen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en IoT Edge-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar en C-modul med Azure IoT Edge för Visual Studio Code](how-to-develop-c-module.md). Du kan fortsätta med någon av följande självstudier om du vill veta mer hur Azure IoT Edge kan hjälpa dig att omvandla dina data till affärsinsikter.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med SQL Server-databaser](tutorial-store-data-sql-server.md)

