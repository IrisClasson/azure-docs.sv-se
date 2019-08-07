---
title: Använd Python för att skicka och ta emot meddelanden - Azure Event Hubs | Microsoft Docs
description: Lär dig hur du skickar händelser för att ta emot händelser från och samla in händelser som strömmas via Event Hubs med hjälp av Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 0960862da9e65cff4d957e97a7488dc7a245651a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742129"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Hur du använder Azure Event Hubs från ett Python-program
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Mer information finns i [introduktion till Event Hubs](event-hubs-what-is-event-hubs.md). 

Den här artikeln innehåller länkar till artiklar om hur du kan utföra följande uppgifter från ett program som skrivits i **Python**:

- [Skicka händelser till en händelsehubb](#send-events-to-event-hubs)
- [Ta emot händelser från en event hub](#receive-events-from-event-hubs)
- Läsa insamlade händelse data från en Azure-lagring. 

## <a name="prerequisites"></a>Förutsättningar
- Skapa en Event Hub genom att följa någon av följande snabb starter: [Azure Portal](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Azure Resource Manager mall](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 och senare är installerad på datorn.

## <a name="install-python-package"></a>Installera Python-paketet

Öppna en kommandotolk med Python i dess sökväg för att installera Python-paketet för Event Hubs, och sedan köra det här kommandot: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Skicka händelser till Event Hubs
Följande kod visar hur man skickar händelser till en händelsehubb från ett Python-program: 

1. Skapa variabler för att lagra URL: en för event hub, nyckelnamn och värdet för nyckeln. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Skapa en händelsehubbklient, lägga till en avsändare, kör-klienten, skicka händelsen med hjälp av avsändaren och sedan stoppa klienten när du är klar. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

En fullständig genomgång om hur du skickar händelser till en händelsehubb från ett program som skrivits i Python finns [i den här artikeln](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Ta emot händelser från Event Hubs
Följande kod visar hur du tar emot händelser från en händelsehubb från ett Python-program: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

En fullständig genomgång om hur du tar emot händelser från en händelsehubb från ett program som skrivits i Python kan se [i den här artikeln](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Läs capture händelsedata från Azure Storage
Följande kod visar hur du läser fångade händelse data som lagras i **Azure Blob Storage** från ett python-program: Aktivera funktionen **Capture** för Event Hub genom att följa anvisningarna nedan: [Aktivera Event Hubs avbildning med hjälp av Azure Portal](event-hubs-capture-enable-through-portal.md). Skicka sedan vissa händelser till event hub innan du testar koden. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

En fullständig genomgång om hur du läser avbildade Event Hubs-data i Azure blob storage från ett program som skrivits i Python finns [i den här artikeln](event-hubs-capture-python.md)

## <a name="github-samples"></a>GitHub-exempel
Du kan hitta mer Python-exempel i den [Git-lagringsplatsen för azure-event-hubs-python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs).

## <a name="next-steps"></a>Nästa steg
Läs igenom artiklarna i avsnittet begrepp från [översikt över Event Hubs-funktioner](event-hubs-features.md).
