<properties
    pageTitle="Azure Mobile aftale Android SDK Integration"
    description="Seneste opdateringer og procedurer for Android SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Hvordan du integrerer GCM med Mobile aftale

> [AZURE.IMPORTANT] Du skal følge fremgangsmåden integration er beskrevet i afsnittet Sådan integrere aftale på Android dokument før følge denne vejledning.
>
> Dette dokument er nyttigt, kun, hvis du allerede er integreret rækkevidde modul og plan til at overføre Google Play-enheder. Læs først for at integrere rækkevidde kampagner i dit program, hvordan du kan integrere aftale når på Android-enhed.

##<a name="introduction"></a>Introduktion

Integrering af GCM gør det muligt for dit program kan installeres.

GCM overførsler overføres til SDK altid indeholder den `azme` vigtige i dataobjektet. Dermed Hvis du bruger GCM til andre formål i dit program, kan du filtrere skubber baseret på tasten.

> [AZURE.IMPORTANT] Kun enheder, der kører Android 2.2 eller ovenfor, har du Google Play installeret og har Google baggrund netværksforbindelse aktiveret kan installeres ved hjælp af GCM; Du kan dog integrere denne kode på en sikker måde på ikke-understøttede enheder (det bruger lige gengivelser).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Oprette et Google Cloud Messaging projekt med API-nøgle

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Integration af SDK

### <a name="managing-device-registrations"></a>Administrere enhed registreringer

Hver enhed skal sende en registrering kommando til Google-serverne, ellers de kan ikke kontaktes.

En enhed kan også unregister fra GCM meddelelser (enheden er ikke registreret automatisk, hvis programmet er fjernet).

Hvis du ikke bruger [Google afspille SDK] , eller du ikke allerede sender registrering formålet dig selv, kan du gøre aftale registrere enheden automatisk for dig.

For at aktivere skal du tilføje følgende for at din `AndroidManifest.xml` fil, inden de `<application/>` mærke:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Kommunikere registrering-id til aftale Push-tjenesten og modtage meddelelser

For at kommunikere kameraet, for at tjenesten aftale Push registrering id og modtage dens meddelelser, skal du tilføje følgende til din `AndroidManifest.xml` fil, inden de `<application/>` mærke (også selvom du selv administrere enhed registreringer):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Sikre, at du har følgende tilladelser i din `AndroidManifest.xml` (efter at den `</application>` mærke).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Give Mobile aftale adgang til din GCM API-nøgle

Følg [denne vejledning](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) for at give Mobile aftale adgang til din GCM API-nøgle.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
