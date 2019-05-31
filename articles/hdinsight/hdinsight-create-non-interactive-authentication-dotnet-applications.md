---
title: Skapa .NET-program för icke-interaktiv autentisering i Azure HDInsight
description: Lär dig mer om att skapa icke-interaktiv autentisering Microsoft .NET-program i Azure HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 8b96c38d5bb24a267ad0203083e485d1780f28c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241472"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Skapa en icke-interaktiv autentisering .NET HDInsight-program
Du kan köra ditt Microsoft .NET Azure HDInsight-program under programmets egen identitet (icke-interaktivt) eller under identiteten för den inloggade användaren av programmet (interaktivt). Den här artikeln visar hur du skapar en icke-interaktiv autentisering .NET-program för att ansluta till Azure och hantera HDInsight. Ett exempel på ett interaktivt program, se [Anslut till Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Från ditt icke-interaktiv .NET-program behöver du:

* Ditt Azure-prenumeration klient-ID (även kallat en *katalog-ID*). Se [hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Azure Active Directory (Azure AD)-programmets klients-ID. Se [skapar ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) och [hämta ett program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Den Azure AD hemlig nyckeln för program. Se [autentiseringsnyckeln för Get-programmet](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Nödvändiga komponenter
* Ett HDInsight-kluster. Se den [komma igång-självstudiekurs](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Tilldela en roll till Azure AD-programmet
Tilldela din Azure AD-program en [rollen](../role-based-access-control/built-in-roles.md), för att bevilja behörigheter för att utföra åtgärder. Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheterna som ärvs till lägre nivåer av omfång. (Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att programmet kan läsa resursgruppen och alla resurser i den.) I den här självstudien får ange du omfånget på resursgruppsnivå. Mer information finns i [använda rolltilldelningar för att hantera åtkomst till din Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md).

**Att lägga till rollen ägare till Azure AD-programmet**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Resursgrupper** på den vänstra menyn.
3. Välj den resursgrupp som innehåller HDInsight-klustret som du ska köra Hive-frågan senare i den här självstudien. Om du har ett stort antal resursgrupper kan använda du filtret för att hitta det du vill använda.
4. På resursgruppmenyn, väljer **åtkomstkontroll (IAM)** .
5. Välj den **rolltilldelningar** fliken för att se de aktuella rolltilldelningarna.
6. Längst ned på sidan Välj **Lägg till rolltilldelning**.
7. Följ anvisningarna för att lägga till rollen ägare till din Azure AD-program. När du har lagt till rollen kan visas programmet under rollen ägare. 

## <a name="develop-an-hdinsight-client-application"></a>Utveckla ett HDInsight-klientprogram

1. Skapa ett C#-konsolprogram
2. Lägg till följande [NuGet](https://www.nuget.org/) paket:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Kör följande kod:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Nästa steg
* [Skapa en Azure Active Directory program och tjänstens huvudnamn i Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md).
* Lär dig hur du [autentisera tjänstens huvudnamn med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Lär dig mer om [Azure rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md).
