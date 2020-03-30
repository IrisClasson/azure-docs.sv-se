---
title: Konfigurera OPC Publisher – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar OPC Publisher för att ange OPC UA-noddataändringar, OPC UA-händelser att publicera och även telemetriformatet.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0ebbf0d41c05f71c571d9665903ba4ba44f71bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198811"
---
# <a name="configure-opc-publisher"></a>Konfigurera OPC Publisher

Du kan konfigurera OPC Publisher så att du anger:

- OPC UA-noddata ändras för publicering.
- OPC UA-händelser att publicera.
- Telemetriformatet.

Du kan konfigurera OPC Publisher med hjälp av konfigurationsfiler eller med hjälp av metodanrop.

## <a name="use-configuration-files"></a>Använda konfigurationsfiler

I det här avsnittet beskrivs alternativ för att konfigurera OPC UA-nodpublicering med konfigurationsfiler.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Använda en konfigurationsfil för att konfigurera publiceringsdataändringar

Det enklaste sättet att konfigurera OPC UA-noder för publicering är med en konfigurationsfil. Konfigurationsfilformatet dokumenteras i [publishednodes.json](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) i databasen.

Syntaxen för konfigurationsfil har ändrats med tiden. OPC Publisher läser fortfarande gamla format, men konverterar dem till det senaste formatet när konfigurationen kvarstår.

I följande exempel visas konfigurationsfilens format:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Använda en konfigurationsfil för att konfigurera publiceringshändelser

Om du vill publicera UA-händelser för OPC använder du samma konfigurationsfil som för dataändringar.

I följande exempel visas hur du konfigurerar publicering för händelser som genereras av [SimpleEvents-servern](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). SimpleEvents-servern finns i [OPC Foundation-databasen](https://github.com/OPCFoundation/UA-.NETStandard) är:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Använda metodanrop

I det här avsnittet beskrivs de metodanrop som du kan använda för att konfigurera OPC Publisher.

### <a name="configure-using-opc-ua-method-calls"></a>Konfigurera med OPC UA-metodanrop

OPC Publisher innehåller en OPC UA-server som kan nås på port 62222. Om värdnamnet är **utgivare**är slutpunkten URI: `opc.tcp://publisher:62222/UA/Publisher`.

Den här slutpunkten exponerar följande fyra metoder:

- PubliceraNod
- AvpubliceraNod
- Fåpubliceradenoder
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Konfigurera med hjälp av IoT Hub direct-metodanrop

OPC Publisher implementerar följande IoT Hub direct-metodanrop:

- PubliceraNoder
- Avpubliceranoder
- AvpublicEraAllNoder
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo (på)

Formatet för JSON-nyttolasten för metodbegäran och svar definieras i [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Om du anropar en okänd metod på modulen svarar den med en sträng som säger att metoden inte implementeras. Du kan anropa en okänd metod som ett sätt att pinga modulen.

### <a name="configure-username-and-password-for-authentication"></a>Konfigurera användarnamn och lösenord för autentisering

Autentiseringsläget kan ställas in via ett IoT Hub-direktmetodanrop. Nyttolasten måste innehålla **egenskapen OpcAuthenticationMode** och användarnamn och lösenord:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Lösenordet krypteras av IoT Hub Workload Client och lagras i utgivarens konfiguration. Om du vill ändra autentiseringen till anonym använder du metoden med följande nyttolast:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Om **egenskapen OpcAuthenticationMode** inte anges i nyttolasten förblir autentiseringsinställningarna oförändrade i konfigurationen.

## <a name="configure-telemetry-publishing"></a>Konfigurera telemetripublicering

När OPC Publisher får ett meddelande om en värdeändring i en publicerad nod genereras ett JSON-formaterat meddelande som skickas till IoT Hub.

Du kan konfigurera innehållet i det här JSON-formaterade meddelandet med hjälp av en konfigurationsfil. Om ingen konfigurationsfil anges `--tc` med alternativet används en standardkonfiguration som är kompatibel med [acceleratorn ansluten fabrikslösning](https://github.com/Azure/azure-iot-connected-factory).

Om OPC Publisher är konfigurerat för batchmeddelanden skickas de som en giltig JSON-matris.

Telemetrin härleds från följande källor:

- OPC Publisher-nodkonfigurationen för noden
- **MonitoredItem-objektet** i OPC UA-stacken som OPC Publisher fick ett meddelande om.
- Argumentet skickas till det här meddelandet, som innehåller information om ändringen av datavärdet.

Den telemetri som läggs i JSON-formaterat meddelande är ett urval av viktiga egenskaper för dessa objekt. Om du behöver fler egenskaper måste du ändra OPC Publisher-kodbasen.

Syntaxen för konfigurationsfilen är följande:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is omitted (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du konfigurerar OPC Publisher, det föreslagna nästa steget är att lära dig hur [du kör OPC Publisher](howto-opc-publisher-run.md).
