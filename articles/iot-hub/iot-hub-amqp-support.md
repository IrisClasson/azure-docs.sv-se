---
title: Förstå Azure IoT Hub AMQP support | Microsoft Docs
description: 'Utvecklarguide – stöd för enheter som ansluter till IoT Hub enheten kund- och service-riktade slutpunkter med hjälp av AMQP-protokollet. Innehåller information om inbyggda stöd för AMQP i SDK: er för Azure IoT-enheter.'
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: f39f184bdc09677e347a2691351309dd6483f467
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965402"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Kommunicera med IoT-hubben med hjälp av AMQP-protokollet

Stöds av IoT Hub [AMQP version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) att leverera en mängd funktioner via enheten kund- och service-riktade slutpunkter. Det här dokumentet beskriver hur du använder AMQP-klienter ansluter till IoT Hub för att använda IoT Hub-funktionen.

## <a name="service-client"></a>Klienten för tjänsten

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Anslutning och autentisera till IoT Hub (service-klient)
Om du vill ansluta till IoT Hub med AMQP en klient kan använda den [anspråk baserade säkerhet (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) eller [enkel autentisering och säkerhet Layer (SASL) autentisering](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Följande krävs för tjänstklienten:

| Information | Värde | 
|-------------|--------------|
| Värdnamn för IoT-hubb | `<iot-hub-name>.azure-devices.net` |
| Nyckelnamn | `service` |
| Åtkomstnyckel | Primär eller sekundär nyckel som är kopplade till tjänsten |
| Signatur för delad åtkomst | Tillfällig SAS i följande format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (koden för att generera den här signaturen finns [här](./iot-hub-devguide-security.md#security-token-structure)).


Kodfragmentet nedan används [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att ansluta till IoT hub via en avsändare-länk.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Anropar meddelanden från moln till enhet (service-klient)
Moln-till-enhetsmeddelande exchange mellan tjänsten och IoT-hubben och mellan enheten och IoT Hub beskrivs [här](iot-hub-devguide-messages-c2d.md). Service-klienten använder två länkar som beskrivs nedan för att skicka meddelanden och ta emot feedback för tidigare skickade meddelanden från enheter.

| Skapad av | Länktyp | Länk | Beskrivning |
|------------|-----------|-----------|-------------|
| Tjänst | Avsändaren länk | `/messages/devicebound` | C2D-meddelanden som är avsedd för enheter som skickas till den här länken av tjänsten. Meddelanden som skickas via den här länken har sina `To` -egenskapen angetts till den målenheten mottagare länksökväg: d.v.s. `/devices/<deviceID>/messages/devicebound`. |
| Tjänst | Mottagare länk | `/messages/serviceBound/feedback` | Slutförande, avvisande och överlåtelse meddelanden kommer från enheter som tagits emot på den här länken av tjänsten. Mer information om meddelanden finns i [här](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Kodfragmentet nedan visar hur du skapar ett C2D-meddelande och skicka den till en enhet med [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

För att få feedback, skapar tjänstklienten en länk för mottagare. Kodfragmentet nedan visar hur du gör detta med hjälp av [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Enligt ovan meddelandet C2D feedback har innehållstyp `application/vnd.microsoft.iothub.feedback.json` och egenskaper i dess JSON-texten kan användas för att härleda leveransstatus av det ursprungliga meddelandet:
* Nyckeln `statusCode` i feedback har något av värdena: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Nyckeln `deviceId` i feedback har ID för målenhet.
* Nyckeln `originalMessageId` i feedback har ID för det ursprungliga C2D-meddelandet som skickas av tjänsten. Detta kan användas för att korrelera feedback till C2D-meddelanden.

### <a name="receive-telemetry-messages-service-client"></a>Ta emot telemetrimeddelanden (service-klient)
Som standard lagrar IoT Hub inmatade enheten telemetrimeddelanden i en inbyggd händelsehubb. Service-klienten kan använda AMQP-protokollet för att ta emot de lagrade händelserna.

För detta ändamål måste tjänstklienten först ansluta till IoT Hub-slutpunkten och ta emot en adress för omdirigering till den inbyggda Event Hubs. Service-klienten använder sedan den angivna adressen för att ansluta till den inbyggda händelsehubben.

I varje steg måste klienten finns i följande uppgifter:
* Giltigt service autentiseringsuppgifter (service SAS-token).
* En välutformat sökväg till konsumenten gruppartitionen som den har för avsikt att hämta meddelanden från. För en viss konsument grupp och partitions-ID, sökvägen har följande format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (förinställd konsumentgrupp är `$Default`).
* Ett valfritt filtrering predikat att utse en startpunkt i partition (det kan vara i form av en sekvens nummer, offset eller köas tidsstämpel).

Kodfragmentet nedan används [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att demonstrera stegen ovan.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

IoT Hub använder en hash av enhets-ID för en viss enhets-ID, för att avgöra vilken partition som ska lagra meddelanden i. Kodfragmentet ovan visar mottagandet av händelser från en enda sådana partition. Observera att ett typiskt program ofta behöver hämta händelser som lagras i alla händelsenavspartitioner.


### <a name="additional-notes"></a>Ytterligare information
* AMQP-anslutningar kan avbrytas på grund av glapp eller utgången av autentiseringstoken (genereras i koden). Tjänstklienten måste hantera dessa fall och återupprätta anslutningen och länkar om det behövs. För fall av förfallodatum för token för autentisering, kan klienten också proaktivt förnya token innan dess förfallodatum för att undvika att släppa en anslutning.
* I vissa fall måste klienten kunna hanterar länk omdirigeringar. I AMQP klient-dokumentationen om hur du hanterar den här åtgärden.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Ta emot meddelanden från molnet till enheten (enheten och modulen klient)
AMQP-länkar som används på enheten är följande:

| Skapad av | Länktyp | Länk | Beskrivning |
|------------|-----------|-----------|-------------|
| Enheter | Mottagare länk | `/devices/<deviceID>/messages/devicebound` | C2D-meddelanden som är avsedd för enheter som tas emot på den här länken av varje målenheten. |
| Enheter | Avsändaren länk | `/messages/serviceBound/feedback` | C2D meddelande feedbacken har skickats till tjänsten via den här länken av enheter. |
| Moduler | Mottagare länk | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | C2D-meddelanden som är avsedd för moduler tas emot på den här länken av varje mål-modul. |
| Moduler | Avsändaren länk | `/messages/serviceBound/feedback` | C2D meddelande feedbacken har skickats till tjänsten via den här länken av moduler. |


## <a name="next-steps"></a>Nästa steg

Läs mer om AMQP-protokollet i den [AMQP v1.0 specifikationen](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Mer information om IoT Hub-meddelanden finns:

* [Meddelanden från moln till enhet](./iot-hub-devguide-messages-c2d.md)
* [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Stöd för MQTT-protokollet](./iot-hub-mqtt-support.md)
