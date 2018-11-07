---
title: Azure SQL Database JSON-funktioner | Microsoft Docs
description: Azure SQL Database kan du parsa-, fråge- och formatera data i JavaScript Object Notation (JSON)-notation.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: c3f1cb7499be57be94cc387eb40d37c1710f2f75
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230537"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Komma igång med JSON-funktioner i Azure SQL Database
Azure SQL Database kan du parsa och skicka frågor till data som visas i JavaScript Object Notation [(JSON)](http://www.json.org/) formatera och exportera din relationsdata som JSON-texten.

JSON är ett populärt dataformat som används för utbyte av data i moderna webbprogram och mobilappar. JSON används också för att lagra halvstrukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Många REST-webbtjänsterna sökresultaten formaterade som JSON-texten eller acceptera data formaterade som JSON. De flesta Azure-tjänster som [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), och [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) har REST-slutpunkter som returnerar eller använda JSON.

Azure SQL Database kan du enkelt arbeta med JSON-data och få din databas med moderna tjänster.

## <a name="overview"></a>Översikt
Azure SQL Database innehåller följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Om du har JSON-text, du kan extrahera data från JSON eller kontrollera att JSON är korrekt formaterat med hjälp av de inbyggda funktionerna [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), och [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Den [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funktionen låter dig uppdatera värdet i JSON-texten. För mer avancerade frågor och analys, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funktion kan omvandla en matris av JSON-objekt till en uppsättning rader. Alla SQL-frågor kan köras på den returnerade resultatuppsättningen. Slutligen finns en [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) satsen där du kan formatera data som lagras i din Relationstabeller som JSON-text.

## <a name="formatting-relational-data-in-json-format"></a>Formatering i relationsdata i JSON-format
Om du har en webbtjänst som tar data från databasen layer och ger ett svar i JSON-format eller klientens JavaScript-ramverk och bibliotek som accepterar data formaterade som JSON, kan du formatera innehållet i databasen som JSON direkt i en SQL-fråga. Du inte längre skriva programkod som formaterar resultaten från Azure SQL Database som JSON eller innehåller vissa JSON-serialiseringsbiblioteket för att konvertera tabellfråga resultat och sedan serialisera objekt till JSON-format. Använd istället FOR JSON-satsen för att formatera resultatet för SQL-frågan som JSON i Azure SQL-databas och använda den direkt i ditt program.

I följande exempel formaterade rader från tabellen Sales.Customer som JSON med FOR JSON-sats:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Instruktionen FOR JSON PATH formaterar resultatet av frågan som JSON-texten. Kolumnnamn som används som nycklar, medan cellvärden genereras som JSON-värden:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Resultatet formateras som en JSON-matris där varje rad formateras som ett separat JSON-objekt.

SÖKVÄG anger att du kan anpassa utdataformat JSON-resultat med hjälp av punktnotation i kolumnalias. Följande fråga som ändrar namnet på nyckeln ”kundnamn” i JSON-formatet för utdata och placerar telefon-och faxnummer i det underordnade objektet ”kontakta”:

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Utdata från den här frågan ser ut så här:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

I det här exemplet vi returnerade ett enda JSON-objekt i stället för en matris genom att ange den [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) alternativet. Du kan använda det här alternativet om du vet att du returnerar ett enda objekt till följd av frågan.

Det huvudsakliga värdet för FOR JSON-satsen är att du kan returnera komplexa hierarkiska data från databasen formaterade som kapslad JSON-objekt eller matriser. I följande exempel visas hur du inkluderar beställningar som hör till kunden som en kapslad matris av order:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

I stället för att separera frågor och få kunddata och för att hämta en lista över relaterade order, kan du sedan hämta alla nödvändiga data med en enda fråga, enligt följande exempel på utdata:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Arbeta med JSON-data
Om du inte har strikt strukturerade data, om du har komplexa underordnade objekt, matriser eller hierarkiska data, eller om din datastrukturer utvecklas över tid, kan JSON-format hjälpa dig att representera alla komplexa datastruktur.

JSON är ett text-format som kan användas som en annan strängtyp i Azure SQL Database. Du kan skicka eller lagra JSON-data som en standard NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

JSON-data som används i det här exemplet representeras med hjälp av typen NVARCHAR(MAX). JSON kan infogas i den här tabellen eller anges som ett argument av den lagrade proceduren med standard Transact-SQL-syntax som du ser i följande exempel:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Alla på klientsidan språk eller biblioteket som fungerar med strängdata i Azure SQL Database fungerar även med JSON-data. JSON kan lagras i en tabell som har stöd för typen NVARCHAR, till exempel en minnesoptimerad tabell eller en systemversionstabell. JSON införs inte eventuella villkor i koden för klientsidan eller i databas-lagret.

## <a name="querying-json-data"></a>Köra frågor mot JSON-data
Om du har data formaterade som JSON som lagras i Azure SQL-tabeller, kan du använda dessa data i en SQL-fråga i JSON-funktioner.

JSON-funktioner som är tillgängliga i Azure SQL database kan du hantera data formaterade som JSON som alla andra SQL-datatyper. Du kan enkelt extrahera värden från JSON-texten och använda JSON-data i alla frågor:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funktionen JSON_VALUE extraherar ett värde från JSON-texten som lagras i kolumnen Data. Den här funktionen använder en JavaScript-liknande sökväg för att referera till ett värde i JSON-texten att extrahera. Det extrahera värdet kan användas i någon del av SQL-fråga.

Funktionen JSON_QUERY liknar JSON_VALUE. Till skillnad från JSON_VALUE extraherar komplexa underordnade objekt, till exempel matriser eller objekt som är placerade i JSON-texten i den här funktionen.

Funktionen JSON_MODIFY kan du ange sökvägen till värdet i JSON-texten som ska uppdateras, samt ett nytt värde som skriver över den gamla servern. Det här sättet kan du enkelt uppdatera JSON-texten utan reparsing hela strukturen.

Eftersom JSON finns i en standardtext, finns det inga garantier att värdena som lagrats i kolumner med text är korrekt formaterade. Du kan verifiera att text som lagras i JSON-kolumnen är korrekt formaterade med hjälp av standard Kontrollbegränsningar för Azure SQL Database och funktionen ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Om den inmatade texten är korrekt formaterad JSON, returnerar funktionen ISJSON värdet 1. På varje insert eller uppdatering av JSON-kolumn, kommer den här begränsningen Kontrollera att nya textvärdet inte är felaktiga JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformera JSON i tabellformat
Azure SQL Database kan du omvandla JSON-samlingar till tabelldata för JSON-format och belastning eller frågan.

OPENJSON är en tabell / värde-funktion som Parsar JSON-text, söker efter en matris av JSON-objekt, upprepas element i matrisen och returnerar en rad i utdata resultatet för varje element i matrisen.

![JSON tabular](./media/sql-database-json-features/image_2.png)

I exemplet ovan kan vi ange var du vill placera den JSON-matris som ska öppnas (i $. Order sökväg), vilka kolumner som ska returneras som resultat och var du hittar de JSON-värden som returneras som celler.

Vi kan omvandla en JSON-matris på den @orders variabel till en uppsättning rader, analysera resultat eller infoga rader i en standardtabell:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Insamling av order formaterade som en JSON-matris och tillhandahålls som en parameter till den lagrade proceduren kan parsas och infogas i tabellen Order.

## <a name="next-steps"></a>Nästa steg
Kolla in dessa resurser om du vill veta hur du integrerar JSON i ditt program:

* [TechNet-bloggen](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9-video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Mer information om olika scenarier för att integrera JSON i ditt program, se demonstrationer i det här [Channel 9-video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) eller hitta ett scenario som passar ditt användningsområde i [JSON blogginlägg](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

