---
title: ta med fil
description: ta med fil
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407078"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generate the certificate-signing request file

The Apple Push Notification Service (APNs) uses certificates to authenticate your push notifications. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generate the Certificate Signing Request (CSR) file, which Apple uses to generate a signed push certificate.

1. Kör Nyckelhanteraren på din Mac. It can be opened from the **Utilities** folder or the **Other** folder on the Launchpad.

1. Select **Keychain Access**, expand **Certificate Assistant**, and then select **Request a Certificate from a Certificate Authority**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Select your **User Email Address**, enter your **Common Name** value, make sure that you specify **Saved to disk**, and then select **Continue**. Leave **CA Email Address** blank as it isn't required.

    ![Nödvändig certifikatsinformation](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Enter a name for the CSR file in **Save As**, select the location in **Where**, and then select **Save**.

    ![Choose a file name for the certificate](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    This action saves the CSR file in the selected location. The default location is **Desktop**. Kom ihåg den plats du valde för filen.

Next, register your app with Apple, enable push notifications, and upload the exported CSR to create a push certificate.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

To send push notifications to an iOS app, register your application with Apple, and also register for push notifications.  

1. If you haven't already registered your app, browse to the [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) at the Apple Developer Center. Sign in to the portal with your Apple ID, and select **Identifiers**. Then select **+** to register a new app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. On the **Register a New Identifier** screen, select the **App IDs** radio button. Välj sedan **Fortsätt**.

    ![iOS Provisioning Portal register new ID page](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Update the following three values for your new app, and then select **Continue**:

   * **Description**: Type a descriptive name for your app.

   * **Bundle ID**: Enter a Bundle ID of the form **Organization Identifier.Product Name** as mentioned in the [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). The *Organization Identifier* and *Product Name* values must match the organization identifier and product name you use when you create your Xcode project. In the following screenshot, the **NotificationHubs** value is used as an organization identifier and the **GetStarted** value is used as the product name. Make sure the **Bundle Identifier** value matches the value in your Xcode project, so that Xcode uses the correct publishing profile.

      ![iOS Provisioning Portal register app ID page](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push Notifications**: Check the **Push Notifications** option in the **Capabilities** section.

      ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      This action generates your App ID and requests that you confirm the information. Select **Continue**, then select **Register** to confirm the new App ID.

      ![Confirm new App ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      After you select **Register**, you see the new App ID as a line item in the **Certificates, Identifiers & Profiles** page.

4. In the **Certificates, Identifiers & Profiles** page, under **Identifiers**, locate the App ID line item that you just created, and select its row to display the **Edit your App ID Configuration** screen.

5. Scroll down to the checked **Push Notifications** option, and then select **Configure** to create the certificate.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. The **Apple Push Notification service SSL Certificates** window appears. Select the **Create Certificate** button under the **Development SSL Certificate** section.

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    The **Create a new Certificate** screen is displayed.

    > [!NOTE]
    > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

1. Select **Choose File**, browse to the location where you saved the CSR file from the first task, and then double-click the certificate name to load it. Välj sedan **Fortsätt**.

1. After the portal creates the certificate, select the **Download** button. Save the certificate, and remember the location to which it's saved.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    The certificate is downloaded and saved to your computer in your **Downloads** folder.

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > By default, the downloaded development certificate is named **aps_development.cer**.

1. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**. Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Although the name in your certificate might be different, the name will be prefixed with **Apple Development iOS Push Services**.

1. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Select **Export**, name the file, select the **.p12** format, and then select **Save**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    You can choose to protect the certificate with a password, but this is optional. Click **OK** if you want to bypass password creation. Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. They are used to enable authentication with APNs.

    > [!NOTE]
    > Your .p12 file name and location might be different than what is pictured in this tutorial.

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. Return to the [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), select **Certificates, Identifiers & Profiles**, select **Profiles** from the left menu, and then select **+** to create a new profile. The **Register a New Provisioning Profile** screen appears.

1. Select **iOS App Development** under **Development** as the provisioning profile type, and then select **Continue**.

    ![Lista med etableringsprofiler](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Next, select the app ID you created from the **App ID** drop-down list, and select **Continue**.

    ![Välj App-ID:et](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. In the **Select certificates** window, select the development certificate that you use for code signing, and select **Continue**. This certificate isn't the push certificate you created. If one does not exist, you must create it. If a certificate does exist, skip to the next step. To create a development certificate if one does not exist:

    1. If you see **No Certificates are available**, select **Create Certificate**.
    2. In the **Software** section, select **Apple Development**. Välj sedan **Fortsätt**.
    3. In the **Create a New Certificate** screen, select **Choose File**.
    4. Browse to the **Certificate Signing Request** certificate you created earlier, select it, and then select **Open**.
    5. Välj **Fortsätt**.
    6. Download the development certificate, and remember the location to which it's saved.

1. Return to the **Certificates, Identifiers & Profiles** page, select **Profiles** from the left menu, and then select **+** to create a new profile. The **Register a New Provisioning Profile** screen appears.

1. In the **Select certificates** window, select the development certificate that you just created. Välj sedan **Fortsätt**.

1. Next, select the devices to use for testing, and select **Continue**.

1. Finally, choose a name for the profile in **Provisioning Profile Name**, and select **Generate**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. When the new provisioning profile is created, select **Download**. Remember the location to which it's saved.

1. Browse to the location of the provisioning profile, and then double-click it to install it on your Xcode development machine.

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

In this section, you create a notification hub and configure authentication with APNs by using the .p12 push certificate that you previously created. If you want to use a notification hub that you've already created, you can skip to step 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configure your notification hub with APNs information

1. Välj **Apple (APNS)** under **Notification Services**.

1. Välj **Certifikat**.

1. Select the file icon.

1. Select the .p12 file that you exported earlier, and then select **Open**.

1. If required, specify the correct password.

1. Välj **Sandbox**-läge. Använd läget **Produktion** enbart om du vill skicka push-meddelanden till användare som har köpt din app i butiken.

    ![Konfigurera APNs-certifikat i Azure-portalen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Välj **Spara**.

You've now configured your notification hub with APNs. You also have the connection strings to register your app and send push notifications.
