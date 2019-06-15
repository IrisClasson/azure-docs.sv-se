---
title: Databasvyer i Azure Blockchain Workbench
description: Översikt över Azure Blockchain Workbench SQL DB databasvyer.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9071cf524a0f3d319d108cb5c961fa886cf8747f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399909"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Databasvyer i Azure Blockchain Workbench

Azure Blockchain Workbench levererar data från distribuerade huvudböcker till en *annan* SQL DB-databas. Annan databasen gör det möjligt att använda SQL- och befintliga verktyg som [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), för att interagera med blockchain-data.

Azure Blockchain Workbench tillhandahåller en uppsättning databasvyer som ger åtkomst till data som kommer att vara till hjälp när du utför dina frågor. Dessa vyer är kraftigt Avnormaliserade att göra det enkelt att snabbt komma igång med att skapa rapporter, analyser, och på annat sätt använda blockchain-data med befintliga verktyg och utan att behöva träna om databasen personal.

Det här avsnittet innehåller en översikt över databasvyer och den data de innehåller.

> [!NOTE]
> Direkt för all användning av database-tabeller i databasen utanför dessa vyer, även om det går att hitta stöds inte.
>

## <a name="vwapplication"></a>vwApplication

Den här vyn innehåller information om **program** som har överförts till Azure Blockchain Workbench.

| Namn                             | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                  | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDescription           | nvarchar(255) | Ja         | En beskrivning av programmet |
| ApplicationDisplayName           | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled               | bit           | Nej          | Identifierar om programmet är aktiverat<br /> **Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen. |
| UploadedDtTm                     | datetime2(7)  | Nej          | Datum och tid som ett kontrakt har överförts |
| UploadedByUserId                 | int           | Nej          | ID för den användare som laddats upp programmet |
| UploadedByUserExternalId         | nvarchar(255) | Nej          | Den externa identifieraren för den användare som laddats upp programmet. Som standard är detta ID användare från Azure Active Directory för consortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nej          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användare i databasen<br />2 – användaren är helt etablerad                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ja         | Det första namnet för den användare som har överförts kontraktet |
| UploadedByUserLastName           | nvarchar(50)  | Ja         | Efternamn för den användare som har överförts kontraktet |
| UploadedByUserEmailAddress       | nvarchar(255) | Ja         | E-postadressen för den användare som har överförts kontraktet |

## <a name="vwapplicationrole"></a>vwApplicationRole

Den här vyn innehåller information om de roller som har definierats i Azure Blockchain Workbench-program.

I en *tillgången överföra* program, till exempel roller, exempelvis *köparen* och *försäljning* roller kan definieras.

| Namn                   | Typ             | Kan vara Null | Beskrivning                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nej          | En unik identifierare för programmet           |
| ApplicationName        | nvarchar(50)     | Nej          | Namnet på programmet                       |
| ApplicationDescription | nvarchar(255)    | Ja         | En beskrivning av programmet                  |
| ApplicationDisplayName | nvarchar(255)    | Nej          | Namnet som ska visas i ett användargränssnitt      |
| RoleId                 | int              | Nej          | En unik identifierare för en roll i programmet |
| RoleName               | nvarchar50)      | Nej          | Namnet på rollen                              |
| RoleDescription        | description(255) | Ja         | En beskrivning av rollen                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Den här vyn innehåller information om de roller som har definierats i Azure Blockchain Workbench-program och de användare som är kopplade till standardrisknivåer.

I en *tillgången överföra* program, till exempel *John Smith* kan associeras med den *köparen* roll.

| Namn                       | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nej          | En unik identifierare för programmet                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Nej          | Namnet på programmet                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Ja         | En beskrivning av programmet                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nej          | En unik identifierare för en roll i programmet                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nej          | Namnet på rollen                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ja         | En beskrivning av rollen                                                                                                                                                                                                             |
| Användar-ID                     | int           | Nej          | ID för användaren som är associerad med rollen |
| UserExternalId             | nvarchar(255) | Nej          | Den externa identifieraren för den användare som är associerad med rollen. Som standard är detta ID användare från Azure Active Directory för consortium.                                                                     |
| UserProvisioningStatus     | int           | Nej          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användare i databasen<br />2 – användaren är helt etablerad |
| UserFirstName              | nvarchar(50)  | Ja         | Det första namnet för den användare som är associerad med rollen |
| UserLastName               | nvarchar(255) | Ja         | Efternamn för den användare som är associerad med rollen |
| UserEmailAddress           | nvarchar(255) | Ja         | E-postadressen för den användare som är associerad med rollen |

## <a name="vwconnectionuser"></a>vwConnectionUser

Den här vyn innehåller information om anslutningar i Azure Blockchain Workbench och de användare som är kopplade till standardrisknivåer. För varje anslutning innehåller den här vyn följande data:

-   Associerad redovisning information
-   Associerade användarinformation

| Namn                     | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nej          | Den unika identifieraren för en anslutning i Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Nej          | Slutpunkts-url för en anslutning |
| ConnectionFundingAccount | nvarchar(255) | Ja         | Finansierande kontot som är associerade med en anslutning, om tillämpligt |
| LedgerId                 | int           | Nej          | Den unika identifieraren för en redovisning |
| LedgerName               | nvarchar(50)  | Nej          | Namnet på transaktionsregister |
| LedgerDisplayName        | nvarchar(255) | Nej          | Namnet på det Transaktionsregister ska visas i Användargränssnittet |
| Användar-ID                   | int           | Nej          | ID för den användare som är associerad med anslutningen |
| UserExternalId           | nvarchar(255) | Nej          | Den externa identifieraren för den användare som är associerad med anslutningen. Som standard är detta ID användare från Azure Active Directory för consortium. |
| UserProvisioningStatus   | int           | Nej          |Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användare i databasen<br />2 – användaren är helt etablerad |
| UserFirstName            | nvarchar(50)  | Ja         | Det första namnet för den användare som är associerad med anslutningen |
| UserLastName             | nvarchar(255) | Ja         | Efternamn för den användare som är associerad med anslutningen |
| UserEmailAddress         | nvarchar(255) | Ja         | E-postadressen för den användare som är associerad med anslutningen |

## <a name="vwcontract"></a>vwContract

Den här vyn innehåller information om distribuerade kontrakt. För varje kontraktet innehåller den här vyn följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Associerad redovisning implementering för funktionen
-   Information om användaren som initierade åtgärden
-   Information om blockchain block och transaktioner

| Namn                                     | Typ           | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nej          | Den unika identifieraren för en anslutning i Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Nej          | Slutpunkts-url för en anslutning |
| ConnectionFundingAccount                 | nvarchar(255)  | Ja         | Finansierande kontot som är associerade med en anslutning, om tillämpligt |
| LedgerId                                 | int            | Nej          | Den unika identifieraren för en redovisning |
| LedgerName                               | nvarchar(50)   | Nej          | Namnet på transaktionsregister |
| LedgerDisplayName                        | nvarchar(255)  | Nej          | Namnet på det Transaktionsregister ska visas i Användargränssnittet |
| ApplicationId                            | int            | Nej          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar (50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar (255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                       | bit            | Nej          | Identifierar om programmet är aktiverat.<br /> **Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen.  |
| WorkflowId                               | int            | Nej          | En unik identifierare för arbetsflödet som är associerade med ett kontrakt |
| WorkflowName                             | nvarchar(50)   | Nej          | Namnet på arbetsflödet som är associerade med ett kontrakt |
| WorkflowDisplayName                      | nvarchar(255)  | Nej          | Namnet på arbetsflödet som är associerade med det kontrakt som visas i användargränssnittet |
| WorkflowDescription                      | nvarchar(255)  | Ja         | Beskrivning av arbetsflödet som är associerade med ett kontrakt |
| ContractCodeId                           | int            | Nej          | En unik identifierare för kontraktet koden som är associerade med kontraktet |
| ContractFileName                         | int            | Nej          | Namnet på den fil som innehåller den smarta kontrakt-koden för det här arbetsflödet. |
| ContractUploadedDtTm                     | int            | Nej          | Datum och tid som koden kontrakt har överförts |
| ContractId                               | int            | Nej          | Den unika identifieraren för kontraktet |
| ContractProvisioningStatus               | int            | Nej          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats i redovisningen<br />3 eller 4 - kontraktet kunde inte distribueras till redovisningen<br />5 - kontraktet har distribuerats i redovisningen <br /><br />Från och med version 1.5, stöds värden 0 till 5. För bakåtkompatibilitet kompatibilitet i den aktuella versionen kan visa **vwContractV0** är tillgänglig som stöder endast värden 0 till 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | E-postadressen för den användare som distribuerats kontraktet |
| ContractDeployedByUserId                 | int            | Nej          | En extern identifierare för den användare som distribuerats kontraktet. Som standard är detta ID guid som representerar Azure Active Directory ID för användaren.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nej          | En extern identifierare för användaren som distribuerats kontraktet. Som standard är detta ID guid som representerar Azure Active Directory ID för användaren.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nej          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användare i databasen <br />2 – användaren är helt etablerad                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Ja         | Det första namnet för den användare som distribuerats kontraktet |
| ContractDeployedByUserLastName           | nvarchar(255)  | Ja         | Efternamn för den användare som distribuerats kontraktet |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Ja         | E-postadressen för den användare som distribuerats kontraktet |

## <a name="vwcontractaction"></a>vwContractAction

Den här vyn representerar en majoritet av information som rör åtgärder som vidtas på kontrakt och har utformats för att underlätta lätt vanliga rapporteringsscenarier. För varje åtgärd innehåller i den här vyn följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Associerade smarta kontrakt funktionen och parametern definition
-   Associerad redovisning implementering för funktionen
-   Specifika instansvärden för parametrar
-   Information om användaren som initierade åtgärden
-   Information om blockchain block och transaktioner

| Namn                                     | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                       | bit           | Nej          | Det här fältet identifierar om programmet är aktiverat. Observera – även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa avtal finns kvar i databasen.                                                  |
| WorkflowId                               | int           | Nej          | En unik identifierare för ett arbetsflöde |
| WorkflowName                             | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName                      | nvarchar(255) | Nej          | Namnet på arbetsflödet ska visas i ett användargränssnitt |
| WorkflowDescription                      | nvarchar(255) | Ja         | Beskrivning av arbetsflödet |
| ContractId                               | int           | Nej          | En unik identifierare för kontraktet |
| ContractProvisioningStatus               | int           | Nej          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats i redovisningen<br />3 eller 4 - kontraktet kunde inte distribueras till redovisningen<br />5 - kontraktet har distribuerats i redovisningen <br /><br />Från och med version 1.5, stöds värden 0 till 5. För bakåtkompatibilitet kompatibilitet i den aktuella versionen kan visa **vwContractActionV0** är tillgänglig som stöder endast värden 0 till 2. |
| ContractCodeId                           | int           | Nej          | En unik identifierare för kod implementeringen av kontraktet |
| ContractLedgerIdentifier                 | nvarchar(255) | Ja         | En unik identifierare som är associerade med den distribuerade versionen av ett smarta kontrakt för en specifik distribuerad redovisning. Till exempel Ethereum. |
| ContractDeployedByUserId                 | int           | Nej          | Den unika identifieraren för den användare som distribuerats kontraktet |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Ja         | Förnamnet på den användare som distribuerats kontraktet |
| ContractDeployedByUserLastName           | nvarchar(255) | Ja         | Efternamn för den användare som distribuerats kontraktet |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nej          | Externa identifierare för den användare som distribuerats kontraktet. Som standard är detta ID guid som representerar sin identitet i consortium Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Ja         | E-postadressen för den användare som distribuerats kontraktet |
| WorkflowFunctionId                       | int           | Nej          | En unik identifierare för en arbetsflödesfunktion |
| WorkflowFunctionName                     | nvarchar(50)  | Nej          | Namnet på funktionen |
| WorkflowFunctionDisplayName              | nvarchar(255) | Nej          | Namnet på en funktion som ska visas i användargränssnittet |
| WorkflowFunctionDescription              | nvarchar(255) | Nej          | Beskrivning av funktionen |
| ContractActionId                         | int           | Nej          | Den unika identifieraren för en kontrakt-åtgärd |
| ContractActionProvisioningStatus         | int           | Nej          | Identifierar den aktuella statusen för åtgärden kontrakt etableringen. Möjliga värden: <br />0 – kontrakt-åtgärd har skapats av API: et i databasen<br />1 – instruktionen kontrakt har skickats till redovisningen<br />2 – instruktionen kontrakt har distribuerats i redovisningen<br />3 eller 4 - kontraktet kunde inte distribueras till redovisningen<br />5 - kontraktet har distribuerats i redovisningen <br /><br />Från och med version 1.5, stöds värden 0 till 5. För bakåtkompatibilitet kompatibilitet i den aktuella versionen kan visa **vwContractActionV0** är tillgänglig som stöder endast värden 0 till 2. |
| ContractActionTimestamp                  | datetime(2,7) | Nej          | Tidsstämpel för instruktionen kontrakt |
| ContractActionExecutedByUserId           | int           | Nej          | Unik identifierare för den användare som har kört åtgärden kontrakt |
| ContractActionExecutedByUserFirstName    | int           | Ja         | Förnamnet på den användare som har utfört kontrakt-åtgärd |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Ja         | Efternamn för användaren som utförde åtgärden kontrakt |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Ja         | Externt ID för användaren som utförde åtgärden kontrakt. Som standard är detta ID guid som representerar sin identitet i consortium Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Ja         | E-postadressen för användaren som utförde åtgärden kontrakt |
| WorkflowFunctionParameterId              | int           | Nej          | En unik identifierare för en parameter för funktionen |
| WorkflowFunctionParameterName            | nvarchar(50)  | Nej          | Namnet på en parameter för funktionen |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Nej          | Namnet på en funktionsparameter som ska visas i användargränssnittet |
| WorkflowFunctionParameterDataTypeId      | int           | Nej          | Den unika identifieraren för den datatyp som är associerade med en arbetsflödesparameter funktion |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Nej          | Namnet på en datatyp som är associerade med en arbetsflödesparameter funktion |
| ContractActionParameterValue             | nvarchar(255) | Nej          | Värdet för parametern lagras i smarta kontrakt |
| BlockHash                                | nvarchar(255) | Ja         | Hash-blockets |
| BlockNumber                              | int           | Ja         | Antal block på redovisningen |
| BlockTimestamp                           | datetime(2,7) | Ja         | Tidsstämpeln för blocket |
| TransactionId                            | int           | Nej          | En unik identifierare för transaktion |
| TransactionFrom                          | nvarchar(255) | Ja         | Den part som har sitt ursprung i transaktion |
| TransactionTo                            | nvarchar(255) | Ja         | Den part som bearbetades med |
| TransactionHash                          | nvarchar(255) | Ja         | Hash för en transaktion |
| TransactionIsWorkbenchTransaction        | bit           | Ja         | Lite som identifierar om transaktionen är en Azure Blockchain Workbench-transaktion |
| TransactionProvisioningStatus            | int           | Ja         | Identifierar den aktuella statusen för etableringsprocessen för transaktionen. Möjliga värden: <br />0 – transaktionen har skapats av API: et i databasen<br />1 – transaktionen har skickats till redovisningen<br />2 – transaktionen har distribuerats i redovisningen                 |
| TransactionValue                         | decimal(32,2) | Ja         | Värdet för transaktionen |

## <a name="vwcontractproperty"></a>vwContractProperty

Den här vyn representerar en majoritet av information som rör egenskaperna som är associerade med ett kontrakt och har utformats för att underlätta lätt vanliga rapporteringsscenarier. För varje egenskap som tas innehåller den här vyn följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Information om den användare som distribuerats arbetsflödet
-   Associerade smarta kontrakt egenskapsdefinition
-   Specifika instansvärden för egenskaper
-   Information om egenskapen state för kontraktet

| Namn                               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                 | bit           | Nej          | Identifierar om programmet är aktiverat.<br />**Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen.                      |
| WorkflowId                         | int           | Nej          | Den unika identifieraren för arbetsflödet |
| WorkflowName                       | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName                | nvarchar(255) | Nej          | Namnet på arbetsflödet som visas i användargränssnittet |
| WorkflowDescription                | nvarchar(255) | Ja         | Beskrivning av arbetsflödet |
| ContractId                         | int           | Nej          | Den unika identifieraren för kontraktet |
| ContractProvisioningStatus         | int           | Nej          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats i redovisningen<br />3 eller 4 - kontraktet kunde inte distribueras till redovisningen<br />5 - kontraktet har distribuerats i redovisningen <br /><br />Från och med version 1.5, stöds värden 0 till 5. För bakåtkompatibilitet kompatibilitet i den aktuella versionen kan visa **vwContractPropertyV0** är tillgänglig som stöder endast värden 0 till 2. |
| ContractCodeId                     | int           | Nej          | En unik identifierare för kod implementeringen av kontraktet |
| ContractLedgerIdentifier           | nvarchar(255) | Ja         | En unik identifierare som är associerade med den distribuerade versionen av ett smarta kontrakt för en specifik distribuerad redovisning. Till exempel Ethereum. |
| ContractDeployedByUserId           | int           | Nej          | Den unika identifieraren för den användare som distribuerats kontraktet |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Förnamnet på den användare som distribuerats kontraktet |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Efternamn för den användare som distribuerats kontraktet |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nej          | Externa identifierare för den användare som distribuerats kontraktet. Som standard är detta ID guid som representerar sin identitet i consortium Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | E-postadressen för den användare som distribuerats kontraktet |
| WorkflowPropertyId                 | int           |             | En unik identifierare för en egenskap för ett arbetsflöde |
| WorkflowPropertyDataTypeId         | int           | Nej          | ID för datatypen för egenskapen |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nej          | Namnet på datatypen för egenskapen |
| WorkflowPropertyName               | nvarchar(50)  | Nej          | Namnet på egenskapen arbetsflöde |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nej          | Visningsnamnet för egenskapen arbetsflöde |
| WorkflowPropertyDescription        | nvarchar(255) | Ja         | En beskrivning av egenskapen |
| ContractPropertyValue              | nvarchar(255) | Nej          | Värde för egenskapen avtal |
| StateName                          | nvarchar(50)  | Ja         | Om den här egenskapen innehåller tillståndet för kontraktet, är visningsnamnet för tillståndet. Om det inte är associerad med tillståndet blir värdet null. |
| StateDisplayName                   | nvarchar(255) | Nej          | Om den här egenskapen innehåller tillstånd, är visningsnamnet för tillståndet. Om det inte är associerad med tillståndet blir värdet null. |
| StateValue                         | nvarchar(255) | Ja         | Om den här egenskapen innehåller tillstånd, är det statusvärdet. Om det inte är associerad med tillståndet blir värdet null. |

## <a name="vwcontractstate"></a>vwContractState

Den här vyn representerar en majoritet av information som rör tillståndet för en specifik kontrakt och har utformats för att underlätta lätt vanliga rapporteringsscenarier. Varje post i den här vyn innehåller följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Information om den användare som distribuerats arbetsflödet
-   Associerade smarta kontrakt egenskapsdefinition
-   Information om egenskapen state för kontraktet

| Namn                               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                 | bit           | Nej          | Identifierar om programmet är aktiverat.<br />**Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen. |
| WorkflowId                         | int           | Nej          | En unik identifierare för arbetsflödet |
| WorkflowName                       | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName                | nvarchar(255) | Nej          | Namnet som visas i användargränssnittet |
| WorkflowDescription                | nvarchar(255) | Ja         | Beskrivning av arbetsflödet |
| ContractLedgerImplementationId     | nvarchar(255) | Ja         | En unik identifierare som är associerade med den distribuerade versionen av ett smarta kontrakt för en specifik distribuerad redovisning. Till exempel Ethereum. |
| ContractId                         | int           | Nej          | En unik identifierare för kontraktet |
| ContractProvisioningStatus         | int           | Nej          |Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats i redovisningen<br />3 eller 4 - kontraktet kunde inte distribueras till redovisningen<br />5 - kontraktet har distribuerats i redovisningen <br /><br />Från och med version 1.5, stöds värden 0 till 5. För bakåtkompatibilitet kompatibilitet i den aktuella versionen kan visa **vwContractStateV0** är tillgänglig som stöder endast värden 0 till 2. |
| ConnectionId                       | int           | Nej          | En unik identifierare för arbetsflödet har distribuerats till blockchain-instansen |
| ContractCodeId                     | int           | Nej          | En unik identifierare för kod implementeringen av kontraktet |
| ContractDeployedByUserId           | int           | Nej          | Unik identifierare för den användare som har distribuerats kontraktet |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nej          | Externa identifierare för den användare som distribuerats kontraktet. Som standard är detta ID guid som representerar sin identitet i consortium Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Förnamnet på den användare som distribuerats kontraktet |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Efternamn för den användare som distribuerats kontraktet |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | E-postadressen för den användare som distribuerats kontraktet |
| WorkflowPropertyId                 | int           | Nej          | En unik identifierare för en egenskap för arbetsflöde |
| WorkflowPropertyDataTypeId         | int           | Nej          | ID för datatypen för egenskapen arbetsflöde |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nej          | Namnet på datatypen för egenskapen arbetsflöde |
| WorkflowPropertyName               | nvarchar(50)  | Nej          | Namnet på egenskapen arbetsflöde |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nej          | Visningsnamn för egenskap ska visas i ett gränssnitt |
| WorkflowPropertyDescription        | nvarchar(255) | Ja         | Beskrivning av egenskapen |
| ContractPropertyValue              | nvarchar(255) | Nej          | Värdet för en egenskap som lagras i kontraktet |
| StateName                          | nvarchar(50)  | Ja         | Om den här egenskapen innehåller tillståndet kan det den visningsnamn för tillståndet. Om det inte är associerad med tillståndet blir värdet null. |
| StateDisplayName                   | nvarchar(255) | Nej          | Om den här egenskapen innehåller tillstånd, är visningsnamnet för tillståndet. Om det inte är associerad med tillståndet blir värdet null. |
| StateValue                         | nvarchar(255) | Ja         | Om den här egenskapen innehåller tillstånd, är det statusvärdet. Om det inte är associerad med tillståndet blir värdet null. |

## <a name="vwuser"></a>vwUser

Den här vyn innehåller information om de consortium medlemmar som har etablerats för att använda Azure Blockchain Workbench. Som standard fylls data via den första etableringen av användaren.

| Namn               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Nej          | En unik identifierare för en användare |
| externalID         | nvarchar(255) | Nej          | En extern identifierare för en användare. Som standard är detta ID guid som representerar Azure Active Directory ID för användaren. |
| ProvisioningStatus | int           | Nej          |Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användare i databasen<br />2 – användaren är helt etablerad |
| FirstName          | nvarchar(50)  | Ja         | Det första namnet för användaren |
| LastName           | nvarchar(50)  | Ja         | Efternamn för användaren |
| EmailAddress       | nvarchar(255) | Ja         | E-postadressen för användaren |

## <a name="vwworkflow"></a>vwWorkflow

Den här vyn visar information kärnmetadata arbetsflöde och arbetsflödets funktioner och parametrar. Är utformad för rapportering och innehåller det även metadata om programmet som är associerad med arbetsflödet. Den här vyn innehåller data från flera underliggande tabeller för att underlätta rapportering om arbetsflöden. För varje arbetsflöde innehåller den här vyn följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Associerade arbetsflödesinformation start tillstånd

| Namn                              | Typ          | Kan vara Null | Beskrivning                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                   | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName            | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                | bit           | Nej          | Identifierar om programmet är aktiverat |
| WorkflowId                        | int           | Ja         | En unik identifierare för ett arbetsflöde |
| WorkflowName                      | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName               | nvarchar(255) | Nej          | Namnet som visas i användargränssnittet |
| WorkflowDescription               | nvarchar(255) | Ja         | Beskrivning av arbetsflödet. |
| WorkflowConstructorFunctionId     | int           | Nej          | Identifierare för arbetsflödesfunktion som fungerar som konstruktorn för arbetsflödet |
| WorkflowStartStateId              | int           | Nej          | En unik identifierare för tillstånd |
| WorkflowStartStateName            | nvarchar(50)  | Nej          | Namnet på tillståndet |
| WorkflowStartStateDisplayName     | nvarchar(255) | Nej          | Namnet som ska visas i användargränssnittet för tillstånd |
| WorkflowStartStateDescription     | nvarchar(255) | Ja         | En beskrivning av arbetsflödets tillstånd |
| WorkflowStartStateStyle           | nvarchar(50)  | Ja         | Det här värdet identifierar procentandelen klar att arbetsflödet är i det här tillståndet |
| WorkflowStartStateValue           | int           | Nej          | Värdet för tillståndet |
| WorkflowStartStatePercentComplete | int           | Nej          | En textbeskrivning som ger en ledtråd till klienter på hur du kan visa det här tillståndet i Användargränssnittet. Tillstånd som stöds omfattar *lyckades* och *fel* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Den här vyn visar information kärnmetadata arbetsflöde och arbetsflödets funktioner och parametrar. Är utformad för rapportering och innehåller det även metadata om programmet som är associerad med arbetsflödet. Den här vyn innehåller data från flera underliggande tabeller för att underlätta rapportering om arbetsflöden. Den här vyn innehåller följande data för varje arbetsflödesfunktion:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Information om funktionen för arbetsflöde

| Namn                                 | Typ          | Kan vara Null | Beskrivning                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nej          | En unik identifierare för programmet |
| ApplicationName                      | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName               | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                   | bit           | Nej          | Identifierar om programmet är aktiverat |
| WorkflowId                           | int           | Nej          | En unik identifierare för ett arbetsflöde |
| WorkflowName                         | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName                  | nvarchar(255) | Nej          | Namnet på arbetsflödet som visas i användargränssnittet |
| WorkflowDescription                  | nvarchar(255) | Ja         | Beskrivning av arbetsflödet |
| WorkflowFunctionId                   | int           | Nej          | En unik identifierare för en funktion |
| WorkflowFunctionName                 | nvarchar(50)  | Ja         | Namnet på funktionen |
| WorkflowFunctionDisplayName          | nvarchar(255) | Nej          | Namnet på en funktion som ska visas i användargränssnittet |
| WorkflowFunctionDescription          | nvarchar(255) | Ja         | Beskrivning av funktionen arbetsflöde |
| WorkflowFunctionIsConstructor        | bit           | Nej          | Identifierar om funktionen arbetsflöde är konstruktorn för arbetsflödet |
| WorkflowFunctionParameterId          | int           | Nej          | En unik identifierare för en parameter för en funktion |
| WorkflowFunctionParameterName        | nvarchar(50)  | Nej          | Namnet på en parameter för funktionen |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Nej          | Namnet på en funktionsparameter som ska visas i användargränssnittet |
| WorkflowFunctionParameterDataTypeId  | int           | Nej          | En unik identifierare för den datatyp som är associerade med en arbetsflödesparameter funktion |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Nej          | Namnet på en datatyp som är associerade med en arbetsflödesparameter funktion |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Den här vyn visar egenskaperna som definierats för ett arbetsflöde. Den här vyn innehåller följande data för varje egenskap:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Information om arbetsflöde

| Namn                         | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nej          | En unik identifierare för programmet |
| ApplicationName              | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled           | bit           | Nej          | Identifierar om programmet är aktiverat.<br />**Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen. |
| WorkflowId                   | int           | Nej          | En unik identifierare för arbetsflödet |
| WorkflowName                 | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName          | nvarchar(255) | Nej          | Namnet som ska visas för arbetsflödet i ett användargränssnitt |
| WorkflowDescription          | nvarchar(255) | Ja         | En beskrivning av arbetsflödet |
| WorkflowPropertyID           | int           | Nej          | En unik identifierare för en egenskap för ett arbetsflöde |
| WorkflowPropertyName         | nvarchar(50)  | Nej          | Namnet på egenskapen |
| WorkflowPropertyDescription  | nvarchar(255) | Ja         | En beskrivning av egenskapen |
| WorkflowPropertyDisplayName  | nvarchar(255) | Nej          | Namnet som ska visas i ett användargränssnitt |
| WorkflowPropertyWorkflowId   | int           | Nej          | ID för arbetsflödet som den här egenskapen är associerad |
| WorkflowPropertyDataTypeId   | int           | Nej          | ID för den datatyp som definierats för egenskapen |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Nej          | Namnet på den datatyp som definierats för egenskapen |
| WorkflowPropertyIsState      | bit           | Nej          | Det här fältet identifierar om den här egenskapen för arbetsflödet innehåller tillståndet för arbetsflödet |

## <a name="vwworkflowstate"></a>vwWorkflowState

Den här vyn representerar egenskaperna som är associerade med ett arbetsflöde. För varje kontraktet innehåller den här vyn följande data:

-   Associerade programdefinition
-   Associerade arbetsflödesdefinition
-   Information om arbetsflödets tillstånd

| Namn                         | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nej          | En unik identifierare för programmet |
| ApplicationName              | nvarchar(50)  | Nej          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar(255) | Nej          | En beskrivning av programmet |
| ApplicationEnabled           | bit           | Nej          | Identifierar om programmet är aktiverat.<br />**Obs:** Även om ett program kan återspeglas som inaktiverade i databasen, tillhörande kontrakt finns kvar på blockchain och data om dessa kontrakt förblir i databasen. |
| WorkflowId                   | int           | Nej          | Den unika identifieraren för arbetsflödet |
| WorkflowName                 | nvarchar(50)  | Nej          | Namnet på arbetsflödet |
| WorkflowDisplayName          | nvarchar(255) | Nej          | Namnet som visas i användargränssnittet för arbetsflödet |
| WorkflowDescription          | nvarchar(255) | Ja         | Beskrivning av arbetsflödet |
| WorkflowStateID              | int           | Nej          | Den unika identifieraren för tillstånd |
| WorkflowStateName            | nvarchar(50)  | Nej          | Namnet på tillståndet |
| WorkflowStateDisplayName     | nvarchar(255) | Nej          | Namnet som ska visas i användargränssnittet för tillstånd |
| WorkflowStateDescription     | nvarchar(255) | Ja         | En beskrivning av arbetsflödets tillstånd |
| WorkflowStatePercentComplete | int           | Nej          | Det här värdet identifierar procentandelen klar att arbetsflödet är i det här tillståndet |
| WorkflowStateValue           | nvarchar(50)  | Nej          | Värdet för tillståndet |
| WorkflowStateStyle           | nvarchar(50)  | Nej          | En textbeskrivning som ger en ledtråd till klienter på hur du kan visa det här tillståndet i Användargränssnittet. Tillstånd som stöds omfattar *lyckades* och *fel* |
