<properties
    pageTitle="Føje pushmeddelelser til din Xamarin.Android app | Azure App Service"
    description="Lær at bruge Azure App Service og Azure meddelelse Hubs til at sende pushmeddelelser til din Xamarin.Android-app"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Føje pushmeddelelser til din Xamarin.Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Oversigt


I dette selvstudium føjer du pushmeddelelser til projektet [Xamarin.Android Hurtig start](app-service-mobile-windows-store-dotnet-get-started.md) , så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .


##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ En aktiv Google-konto. Du kan tilmelde dig en Google-konto på [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Google Cloud Messaging klientkomponent](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Konfigurere en besked om Hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Aktivere Firebase Messaging i skyen

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Konfigurere Azure for at sende anmodninger om opslagsnål

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Opdatere server projektet for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Konfigurere klientprojektet til pushmeddelelser

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Føj push-beskeder kode til din app

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Test pushmeddelelser i din app

Du kan teste appen ved hjælp af en virtuel enhed i emulatoren. Der findes flere konfigurationstrin, der er påkrævet, når der benyttes en emulator.

1. Sørg for, at du anvender for eller fejlfinding på en virtuel enhed, der indeholder Google APIs angive som destination, som vist nedenfor i Android virtuelle enhed (AVD) manager.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Tilføje en Google-konto til Android-enhed ved at klikke på **Apps** > **Indstillinger** > **Tilføj konto**, skal du følge instruktionerne.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Køre appen todolist som før, og Indsæt et nyt element opgaveliste. Denne gang, vises et meddelelsesikon i meddelelsesområdet. Du kan åbne meddelelse skuffe for at få vist den fulde tekst i meddelelsen om.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
