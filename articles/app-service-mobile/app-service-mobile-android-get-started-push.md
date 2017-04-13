<properties
    pageTitle="Føje Pushmeddelelser til Android-Appen med Azure-Mobilapps"
    description="Lær, hvordan du bruger Azure Mobile-Apps til at sende pushmeddelelser til din Android-app."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Føje Pushmeddelelser til din Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Oversigt
I dette selvstudium føjer du pushmeddelelser til [Android Hurtig start] -projekt, så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Få mere at vide under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Forudsætninger

Du skal bruge følgende:

* En IDE afhængigt af dit projekts backend-version:

    * Hvis denne app har en Node.js back-end [Android Studio](https://developer.android.com/sdk/index.html) .

    * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) eller nyere, hvis denne app har en .net back-end.

* Android 2.3 eller nyere, Google lager revision 27 eller nyere og Google Play tjenester 9.0.2 eller nyere til Firebase skyen Messaging.

* Udføre [Android Hurtig start].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Oprette et projekt, der understøtter Firebase skyen Messaging

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurere en besked om Hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurere Azure for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Aktivér pushmeddelelser for projektets server

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Føje pushmeddelelser til din app

I dette afsnit, skal opdatere du din klient Android app for at håndtere pushmeddelelser.

### <a name="verify-android-sdk-version"></a>Kontrolleres versionen af Android SDK

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Det næste trin er at installere Google Play services. Google Cloud Messaging har nogle API niveau minimumskravene til udvikling og afprøvning, hvor egenskaben **minSdkVersion** i manifestet skal overholde.

Hvis du tester med en ældre enhed, skal du se [Angive op Google afspille Services SDK] til at bestemme, hvordan lavt kan du angive denne værdi og angive den korrekt.

### <a name="add-google-play-services-to-the-project"></a>Tilføje Google afspille tjenester til projektet

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Tilføje kode

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Teste app'en med den publicerede mobile service

Du kan teste app, ved at knytte en Android-telefon med et USB-kabel direkte eller ved hjælp af en virtuel enhed i emulatoren.

## <a name="more"></a>Mere

<!-- URLs -->
[Startvejledning til Android]: app-service-mobile-android-get-started.md

[Konfigurere Google Play Services SDK]:https://developers.google.com/android/guides/setup
