<properties
    pageTitle="Føje Push-beskeder til iOS-App med Azure Mobile-Apps"
    description="Lær, hvordan du bruger Azure Mobile-Apps til at sende pushmeddelelser til din iOS-app."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Føje Push-beskeder til din iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Oversigt
I dette selvstudium føjer du pushmeddelelser til projektet [iOS Hurtig start] , så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

Den [iOS simulator understøtter ikke pushmeddelelser](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Du har brug for en fysisk iOS-enhed og en [Apple udvikler Program medlemskab](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Konfigurere meddelelse Hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrere app til pushmeddelelser

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurere Azure for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Opdatere back-end for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Føje pushmeddelelser til app

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Test pushmeddelelser

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Mere

* Skabeloner giver dig mulighed for at sende på tværs af platforme skubber og oversatte skubber. [Sådan Brug iOS klientbibliotek til Azure Mobile-Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) viser, hvordan du registrere skabeloner.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS Hurtig start]: app-service-mobile-ios-get-started.md
