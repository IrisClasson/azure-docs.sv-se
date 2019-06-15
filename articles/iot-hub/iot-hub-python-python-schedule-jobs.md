---
title: Schemalägg jobb med Azure IoT Hub (Python) | Microsoft Docs
description: Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Du kan använda Azure IoT SDK för Python för att implementera appar för simulerade enheter och en tjänstapp för att köra jobbet.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569298"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Schemalägg och Sänd jobb (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub är en helt hanterad tjänst som gör att en backend-app för att skapa och spåra jobb som schemalägger och uppdatera miljontals enheter.  Jobb kan användas för följande åtgärder:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direktmetoder

Den övergripande ett jobb omsluter på sådana åtgärder och spårar förloppet för körning mot en uppsättning enheter, som definieras av en enhet twin fråga.  En backend-app kan till exempel använda ett jobb för att anropa en metod för omstart på 10 000 enheter, som angetts av en enhet twin fråga och schemalagda vid ett senare tillfälle.  Programmet kan sedan spåra förloppet som var och en av dessa enheter tar emot och körningsmetod omstart.

Mer information om var och en av de här funktionerna i de här artiklarna:

* Enhetstvillingen och egenskaper: [Kom igång med enhetstvillingar](iot-hub-python-twin-getstarted.md) och [självstudien: Så här använder du tvillingegenskaper](tutorial-device-twins.md)

* Direkta metoder: [IoT Hub developer guide - direkta metoder](iot-hub-devguide-direct-methods.md) och [självstudie: direkta metoder](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

I den här självstudiekursen lär du dig att:

* Skapa en simulerad enhet Python-app som har en direkt metod, vilket gör att **lockDoor**, som kan anropas av lösningens serverdel.

* Skapa en Python-konsolapp som anropar den **lockDoor** direkt metod i den simulerade enhetsappen med hjälp av ett jobb och uppdateringar önskade egenskaper med hjälp av ett enhetsjobb.

I slutet av den här självstudien har du två Python-appar:

**simDevice.py**, som ansluter till IoT hub med enhetsidentiteten och tar emot en **lockDoor** direkt metod.

**scheduleJobService.py**, som anropar en direkt metod i den simulerade enhetsappen och uppdaterar enhetstvillingen önskade egenskaper med hjälp av ett jobb.

För att kunna genomföra den här kursen behöver du följande:

* [Python 2.x eller 3.x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)](https://pip.pypa.io/en/stable/installing/).

* Om du använder Windows OS installerar du [Visual C++ redistributable package](https://www.microsoft.com/download/confirmation.aspx?id=48145) så att du kan använda native-DLL:er från Python.

* Ett aktivt Azure-konto. (Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

> [!NOTE]
> Den **Azure IoT SDK för Python** stöder inte **jobb** funktioner. Den här självstudien är i stället en alternativ lösning genom att använda asynkrona trådar och timers. Ytterligare uppdateringar finns i den **klient-SDK** funktionslistan på den [Azure IoT SDK för Python](https://github.com/Azure/azure-iot-sdk-python) sidan. 
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Python-konsolapp som svarar på en direkt metod som anropas av moln, vilket utlöser en simulerad **lockDoor** metod.

1. I Kommandotolken, kör du följande kommando för att installera den **azure-iot-device-client** paketet:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Använd en textredigerare och skapa en ny **simDevice.py** fil i din arbetskatalog.

3. Lägg till följande `import` instruktioner och variabler i början av den **simDevice.py** fil. Ersätt `deviceConnectionString` med anslutningssträngen för den enhet som du skapade ovan:

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Lägg till följande funktion motringningen att hantera den **lockDoor** metod:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Lägg till en annan funktion återanrop för att hantera device twins uppdateringar:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Lägg till följande kod för att registrera hanteraren för den **lockDoor** metod. Även innehålla de `main` rutinen:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. Spara och Stäng den **simDevice.py** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff) vilket rekommenderas i artikeln, [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Schemalägga jobb för att anropa en direkt metod och uppdatera egenskaperna för en enhetstvilling

I det här avsnittet skapar du en Python-konsolapp som initierar en fjärransluten **lockDoor** med hjälp av en direkt metod på en enhet och uppdatera den enhetstvillingen egenskaper.

1. I Kommandotolken, kör du följande kommando för att installera den **azure-iot-service-client** paketet:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. Använd en textredigerare och skapa en ny **scheduleJobService.py** fil i din arbetskatalog.

3. Lägg till följande `import` instruktioner och variabler i början av den **scheduleJobService.py** fil:

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Lägg till följande funktion som används för att fråga efter enheter:

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. Lägg till följande metoder för att köra de jobb som anropar den direkta metod och enheten läsningen:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Lägg till följande kod för att schemalägga jobb och uppdatera jobbstatus. Även innehålla de `main` rutinen:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Spara och Stäng den **scheduleJobService.py** fil.

## <a name="run-the-applications"></a>Köra programmen

Nu är det dags att köra programmen.

1. Kör följande kommando för att börja lyssna efter omstart direkt metod i Kommandotolken i din arbetskatalog:

    ```cmd/sh
    python simDevice.py
    ```

2. Kör följande kommando för att utlösa jobb för att låsa dörren och uppdatera läsningen i en annan kommandotolk i din arbetskatalog:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Du ser enheten svar på den direkta metoden och enhetstvillingar uppdateras i konsolen.

    ![IoT Hub Job exempel 1 – enheten utdata](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job 2 – exempelenhet](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du ett jobb för att schemalägga en direkt metod att en enhet och varje uppdatering av enheten tvillingegenskaper.

Om du vill komma igång med IoT Hub och enhetshanteringsmönster som via luften firmware-uppdatering, [hur du gör en firmware-uppdatering](tutorial-firmware-update.md).