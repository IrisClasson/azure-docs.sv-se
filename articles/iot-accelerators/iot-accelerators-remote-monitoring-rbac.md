---
title: Åtkomstkontroll för fjärransluten övervakning – Azure | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan konfigurera rollbaserad åtkomstkontroll (RBAC) i lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 9accb41cdb4d780bf137d6872cca022226f902e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443132"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurera rollbaserade åtkomstkontroller i lösningsacceleratorn för fjärrövervakning

Den här artikeln innehåller information om hur du konfigurerar rollbaserad åtkomstkontroll i lösningsacceleratorn för fjärrövervakning. Rollbaserad åtkomstkontroll kan du begränsa åtkomsten för enskilda användare till specifika funktioner i lösningen.

## <a name="default-settings"></a>Standardinställningar

Första gången du distribuerar lösningen för fjärrövervakning, finns det två roller: **Administratören** och **skrivskyddad**.

Alla användare i den **Admin** rollen har fullständig åtkomst till lösning, inklusive följande behörigheter nedan. En användare i den **skrivskyddad** rollen har endast åtkomst till visa lösningen.

| Behörighet            | Admin | Skrivskyddad |
|----------------       |-------|-----------|
| Visa lösningen         | Ja   | Ja       |
| Uppdatera larm         | Ja   | Nej        |
| Ta bort larm         | Ja   | Nej        |
| Skapa enheter        | Ja   | Nej        |
| Uppdatera enheter        | Ja   | Nej        |
| Ta bort enheter        | Ja   | Nej        |
| Skapa enhetsgrupper  | Ja   | Nej        |
| Uppdatera enhetsgrupper  | Ja   | Nej        |
| Ta bort enhetsgrupper  | Ja   | Nej        |
| Skapa regler          | Ja   | Nej        |
| Uppdateringsregler          | Ja   | Nej        |
| Ta bort regler          | Ja   | Nej        |
| Skapa jobb           | Ja   | Nej        |
| SIM-hantering av uppdatering | Ja   | Nej        |

Som standard tilldelas automatiskt den användare som distribueras lösningen i **Admin** roll och är en Azure Active Directory-programmets ägare. Ägare program kan tilldela du roller till andra användare via Azure portal. Om du vill att en annan användare att tilldela roller i lösningen, måste de också anges som programägare i Azure-portalen.

> [!NOTE]
> Den användare som distribueras lösningen är den **endast person** som kan visa den direkt efter dess har skapats. Om du vill bevilja andra åtkomst till om du vill visa programmet som en skrivskyddad, administratör, eller en anpassad roll, se följande anvisningarna nedan på Lägg till eller ta bort användare.

## <a name="add-or-remove-users"></a>Lägg till eller ta bort användare

Ägare Azure Active Directory-program kan använda du Azure-portalen för att lägga till eller ta bort en användare till en roll från lösningen för fjärrövervakning. I följande anvisningar används den [företagsprogram i Azure Active Directory](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) som skapades när du har distribuerat av lösningen för fjärrövervakning.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Kontrollera den [användare finns i katalogen](../active-directory/fundamentals/add-users-azure-active-directory.md) du använder. Du har valt katalog när du har loggat in på den [Microsoft Azure IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators) plats. Katalognamnet är synlig i det övre högra hörnet av den [sidan](https://www.azureiotsolutions.com/Accelerators).

1. Hitta den **företagsprogram** för din lösning i Azure-portalen. En gång kan filtrera listan genom att ange **programtyp** till **alla program**. Sök efter namnet på programmet med appen. Programnamnet är namnet på lösningen för fjärrövervakning. Visningsnamn för lösningen och program finns i följande skärmbild **contoso rm4**.

    ![Företagsprogram](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Kontrollera att du är ägare till programmet genom att klicka på programmet och sedan på **ägare**. I följande skärmbild **Contoso admin** äger den **contoso rm4** program:

    ![Ägare](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Om du inte är ägare måste du be en befintlig ägare för att lägga till dig i listan. Endast ägare kan tilldela programroller som **Admin** eller **skrivskyddad** till andra användare.

1. Om du vill se en lista över användare som tilldelas till roller i programmet, klickar du på **användare och grupper**.

1. Om du vill lägga till en användare klickar du på **+ Lägg till användare**, och klicka sedan på **användare och grupper, inga valda** att välja en användare i katalogen.

1. Om du vill tilldela användaren till en roll, klickar du på **Välj roll, inga valda** och välj antingen den **Admin** eller **skrivskyddad** roll för användaren. Klicka på **Välj**, och klicka sedan på **tilldela**.

    ![Välja en roll](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Användaren kan nu komma åt av lösningen för fjärrövervakning med de behörigheter som definierats av rollen.

1. Du kan ta bort användare från programmet på den **användare och grupper** i portalen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Lösningen för fjärrövervakning innehåller den **Admin** och **skrivskyddad** roller när det distribueras först. Du kan lägga till anpassade roller med olika uppsättningar av behörigheter. För att definiera en anpassad roll, måste du:

- Lägga till en ny roll i programmet i Azure-portalen.
- Definiera en princip för den nya rollen i mikrotjänster för autentisering och auktorisering.
- Uppdatera lösningens webbgränssnittet.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definiera en anpassad roll i Azure portal

Följande steg beskriver hur du lägger till en roll till ett program i Azure Active Directory. I det här exemplet skapar du en ny roll som medlemmarna kan skapa, uppdatera och ta bort enheter i lösningen för fjärrövervakning.

1. Hitta den **appregistrering** för din lösning i Azure-portalen. Programnamnet är namnet på lösningen för fjärrövervakning. Visningsnamn för lösningen och program finns i följande skärmbild **contoso rm4**.

    ![Appregistrering](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Välj ditt program och klicka sedan på **Manifest**. Du kan se de två befintliga [approller](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) definierats för programmet:

    ![Visa manifest](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Redigera manifest för att lägga till en roll med namnet **ManageDevices** som visas i följande kodavsnitt. Du behöver en unik sträng, till exempel ett GUID för nytt roll-ID. Du kan generera ett nytt GUID med en tjänst som den [Online GUID-Generator](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Spara ändringarna.

### <a name="define-a-policy-for-the-new-role"></a>Definiera en princip för den nya rollen

När du vill lägga till rollen till appen i Azure-portalen, måste du definiera en princip i [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) för den roll som tilldelar de behörigheter som krävs för att hantera enheter.

1. Klona den [Remote Monitoring Mikrotjänster](https://github.com/Azure/remote-monitoring-services-dotnet) databasen från GitHub till den lokala datorn.

1. Redigera den **auth/Services/data/policies/roles.json** fil att lägga till principen för den **ManageDevices** roll som du ser i följande kodavsnitt. Den **ID** och **rollen** värdena måste matcha rolldefinitionen i appmanifestet i föregående avsnitt. Listan över tillåtna åtgärder gör att någon är i den **ManageDevices** roll för att skapa, uppdatera och ta bort enheter som är anslutna till lösningen:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. När du har redigerat den **Services/data/policies/roles.json** filen, återskapa och distribuera om autentisering och auktorisering mikrotjänst kan utvecklingsacceleratorn.

### <a name="how-the-web-ui-enforces-permissions"></a>Hur webbgränssnittet använder behörigheter

Webb Gränssnittet använder de [autentisering och auktorisering mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) för att avgöra vilka åtgärder en användare kan ta och vilka kontroller som visas i Användargränssnittet. Exempel: om din lösning kallas **contoso rm4**, webbgränssnittet hämtar en lista över tillåtna åtgärder för den aktuella användaren genom att skicka följande begäran:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

För en användare som heter **Enhetshanteraren** i den **ManageDevices** , svaret innehåller följande JSON i brödtext:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Följande kodavsnitt från [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) i den [webbanvändargränssnittet](https://github.com/Azure/pcs-remote-monitoring-webui/) visar hur behörigheterna som tillämpas deklarativt:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Mer information finns i [skyddade komponenter](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Du kan definiera ytterligare behörigheter i den [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) fil.

### <a name="how-the-microservices-enforce-permissions"></a>Hur mikrotjänsterna tillämpa behörigheter

Mikrotjänster kan du också kontrollera behörigheter för att skydda mot obehörig API-begäranden. När en mikrotjänst får en API-begäran, avkodar och verifierar JWT-token för att få det användar-ID och de behörigheter som är associerade med användarens roll.

Följande kodavsnitt från den [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) fil i den [IoTHub Manager mikrotjänst](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), visar hur behörigheterna som används:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur rollbaserad åtkomst kontroller är implementerade i lösningsacceleratorn för fjärrövervakning.

Se [konfigurera åtkomstkontroller för Time Series Insights explorer](iot-accelerators-remote-monitoring-rbac-tsi.md) information om hur du hanterar åtkomst till Time Series Insights explorer i lösningsacceleratorn för fjärrövervakning.

Mer information om lösningsacceleratorn för fjärrövervakning finns [arkitektur för fjärrövervakning](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar lösningen för fjärrövervakning finns [anpassa och distribuera om en mikrotjänst](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->