---
title: Konfigurera tjänst slut punkter för virtuella nätverk för Azure Service Bus
description: Den här artikeln innehåller information om hur du lägger till en tjänst slut punkt för Microsoft. Service Bus i ett virtuellt nätverk.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f902c77c3c7e614247abd4f8af50b8ed37b7e574
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552993"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Tillåt åtkomst till Azure Service Bus namnrymd från vissa virtuella nätverk

Integreringen av Service Bus med [tjänst slut punkter för virtuella datorer med Virtual Network (VNet)][vnet-sep] ger säker åtkomst till meddelande funktioner från arbets belastningar som virtuella datorer som är kopplade till virtuella nätverk, med den nätverks trafik väg som skyddas i båda ändar.

När den har kon figurer ATS för att bindas till minst en tjänst slut punkt för ett virtuellt nätverk, accepterar inte namn området för Service Bus längre trafik från var som helst men auktoriserade virtuella nätverk och eventuellt vissa Internet-IP-adresser. I det virtuella nätverkets perspektiv binder du en Service Bus namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten.

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Service Bus-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall.

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Service Bus.
>
> Betrodda Microsoft-tjänster stöds inte när virtuella nätverk implementeras.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan **inte** är fullständig) –
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> De Microsoft-tjänster som behövs nedan måste finnas i ett virtuellt nätverk
> - Azure App Service
> - Azure Functions
> - Azure Monitor (diagnostisk inställning)

> [!IMPORTANT]
> Virtuella nätverk stöds endast på [Premium-nivå](service-bus-premium-messaging.md) Service Bus namn områden.
> 
> När du använder VNet-tjänstens slut punkter med Service Bus bör du inte aktivera de här slut punkterna i program som blandar standard-och Premium-nivån Service Bus namn områden. Eftersom standard nivån inte stöder virtuella nätverk. Slut punkten är begränsad till endast Premium-nivåns namn område.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhets scenarier som aktive ras av VNet-integrering 

Lösningar som kräver tätt och compartmentalized säkerhet, och där undernät för virtuella nätverk tillhandahåller segmentering mellan compartmentalized-tjänsterna, behöver vanligt vis kommunikations vägar mellan tjänster som finns i dessa fack.

Alla omedelbara IP-vägar mellan avdelningarna, inklusive de som driver HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätverks skiktet. Meddelande tjänster tillhandahåller fullständigt isolerade kommunikations vägar, där meddelanden är jämnt skrivna till disk när de övergår mellan parter. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Service Bus instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som kan uppnås med valfri peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

## <a name="binding-service-bus-to-virtual-networks"></a>Binda Service Bus till virtuella nätverk

*Virtuella nätverks regler* är brand Väggs säkerhetsfunktionen som styr om din Azure Service Bus-Server accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Service Bus namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **Virtual Network tjänst slut punkt** i ett Virtual Network undernät och aktivera den för **Microsoft. Service Bus** enligt beskrivningen i [Översikt över tjänstens slut punkt][vnet-sep]. När du har lagt till tjänst slut punkten binder du Service Bus namn området till den med en **regel för virtuellt nätverk**.

Den virtuella nätverks regeln är en associering av Service Bus-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Service Bus namn området. Service Bus själva upprättar aldrig utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att lägga till en tjänst slut punkt för virtuellt nätverk. Om du vill begränsa åtkomsten måste du integrera slut punkten för det virtuella nätverks tjänsten för Event Hubs namn området.

1. Navigera till **Service Bus namn området** i [Azure Portal](https://portal.azure.com).
2. På den vänstra menyn väljer du alternativet **nätverk** under **Inställningar**.  

    > [!NOTE]
    > Fliken **nätverk** visas endast för **Premium** -namnområden.  
    
    Som standard är alternativet **valda nätverk** markerat. Om du inte lägger till minst en IP-brandväggsregel eller ett virtuellt nätverk på den här sidan kan namn området nås via offentliga Internet (med hjälp av åtkomst nyckeln).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Sidan nätverk – standard" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Om du väljer alternativet **alla nätverk** accepterar Service Bus namn området anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Brand vägg – alternativet alla nätverk är valt](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Om du vill begränsa åtkomsten till vissa virtuella nätverk väljer du alternativet **valda nätverk** om det inte redan är valt.
1. I avsnittet **Virtual Network** på sidan väljer du **+ Lägg till befintligt virtuellt nätverk**. 

    ![lägga till ett befintligt virtuellt nätverk](./media/service-endpoints/add-vnet-menu.png)
3. Välj det virtuella nätverket i listan över virtuella nätverk och välj sedan **under nätet**. Du måste aktivera tjänstens slut punkt innan du lägger till det virtuella nätverket i listan. Om tjänstens slut punkt inte är aktive rad uppmanas du att aktivera den.
   
   ![välj undernät](./media/service-endpoints/select-subnet.png)

4. Du bör se följande meddelande när tjänst slut punkten för under nätet har Aktiver ATS för **Microsoft. Service Bus**. Välj **Lägg till** längst ned på sidan för att lägga till nätverket. 

    ![välj undernät och aktivera slutpunkt](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Om du inte kan aktivera tjänstens slut punkt kan du ignorera den saknade slut punkten för virtuella nätverks tjänster med Resource Manager-mallen. Den här funktionen är inte tillgänglig i portalen.
6. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas i Portal meddelanden. Knappen **Spara** bör inaktive ras. 

    ![Spara nätverk](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Anvisningar om hur du tillåter åtkomst från vissa IP-adresser eller intervall finns i [Tillåt åtkomst från vissa IP-adresser eller intervall](service-bus-ip-filtering.md).

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Service Bus-namnområde.

Mallparametrar:

* **namespaceName**: Service Bus namnrymd.
* **virtualNetworkingSubnetId**: fullständigt kvalificerad Resource Manager-sökväg för det virtuella nätverkets undernät; till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standard under nätet för ett virtuellt nätverk.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
> 
> Från
> ```json
> "defaultAction": "Allow"
> ```
> till
> ```json
> "defaultAction": "Deny"
> ```
>

Mall:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vnet-sep]
- [Azure Service Bus IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
