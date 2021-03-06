---
title: 'Snabb start: data strömning med Azure Event Hubs med Kafka-protokollet'
description: 'Snabb start: den här artikeln innehåller information om hur du strömmar till Azure Event Hubs med hjälp av Kafka-protokollet och API: erna.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4a63aeec7b282ba9b707a1521c51fd944ca24411
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314432"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Snabb start: data strömning med Event Hubs med Kafka-protokollet
Den här snabb starten visar hur du strömmar till Event Hubs utan att ändra protokoll klienter eller köra egna kluster. Du lär dig hur du använder dina producenter och användare för att prata med Event Hubs med bara en konfigurations ändring i dina program. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här snabbstarten behöver du följande:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
När du skapar en **standard** nivå Event Hubs-namnrymd aktive ras Kafka-slutpunkten för namn området automatiskt. Du kan strömma händelser från dina program som använder Kafka-protokollet till standard nivå Event Hubs. Följ steg-för-steg-instruktionerna i [skapa en Event Hub med Azure Portal](event-hubs-create.md) för att skapa ett **standard** -nivå Event Hubs-namnområde. 

> [!NOTE]
> Event Hubs för Kafka är bara tillgängligt på **standard** -och **dedikerade** nivåer. **Basic** -nivån har inte stöd för Kafka på Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Skicka och ta emot meddelanden med Kafka i Event Hubs

1. Klona [Azure Event Hubs för Kafka-lagringsplats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigera till `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Uppdatera konfigurationsinformationen för tillverkare i `src/main/resources/producer.config` på följande sätt:

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Arbeta**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Du hittar käll koden för exempel hanterarens klass CustomAuthenticateCallbackHandler på GitHub [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Kör Producer-koden och Stream-händelserna i Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigera till `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Uppdatera konfigurationsinformationen för konsument i `src/main/resources/consumer.config` på följande sätt:
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Arbeta**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Du hittar käll koden för exempel hanterarens klass CustomAuthenticateCallbackHandler på GitHub [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Du kan hitta alla OAuth-exempel för Event Hubs för Kafka [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Kör konsument koden och process händelserna från händelsehubben med Kafka-klienterna:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Om dina Kafka-kluster för Event Hubs har händelser börjar du nu att få dem från konsumenten.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du strömmar till Event Hubs utan att ändra protokoll klienter eller köra egna kluster. Mer information finns i [Apache Kafka Developer Guide for Azure Event Hubs](apache-kafka-developer-guide.md). 
