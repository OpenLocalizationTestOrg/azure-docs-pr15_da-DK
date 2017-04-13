<properties
    pageTitle="Azure Mobile aftale Android SDK Integration"
    description="Seneste opdateringer og procedurer for Android SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-integrate-adm-with-engagement"></a>Hvordan du integrerer ADM med aftale

> [AZURE.IMPORTANT] Du skal følge fremgangsmåden integration er beskrevet i afsnittet Sådan integrere aftale på Android dokument før følge denne vejledning.
>
> Dette dokument er nyttigt, kun, hvis du allerede er integreret rækkevidde modul og planlægger at push Amazon enheder. Læs først for at integrere rækkevidde kampagner i dit program, hvordan du kan integrere aftale når på Android-enhed.

##<a name="introduction"></a>Introduktion

Integrering af ADM gør det muligt for dit program kan installeres, når målretning af Amazon Android-enheder.

ADM overførsler overføres til SDK altid indeholder den `azme` vigtige i dataobjektet. Dermed Hvis du bruger ADM til andre formål i dit program, kan du filtrere skubber baseret på tasten.

> [AZURE.IMPORTANT] Kun Amazon Kindle enheder, der kører Android 4.0.3 eller derover understøttes af Amazon enhed meddelelser. Du kan dog integrere denne kode på en sikker måde på andre enheder.

##<a name="sign-up-to-adm"></a>Tilmeld dig for at ADM

Hvis du ikke allerede er gjort, skal du aktivere ADM på kontoen Amazon.

Fremgangsmåden er detaljeret på: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Når fremgangsmåden, får du vist:

-   OAuth legitimationsoplysninger (en klient-ID og en klient hemmeligt) for at aftale skal kunne push dine enheder.
-   En API-nøgle, der skal integreres i dit program.

##<a name="sdk-integration"></a>Integration af SDK

### <a name="managing-device-registrations"></a>Administrere enhed registreringer

Hver enhed skal sende en registrering kommando til ADM servere, ellers de kan ikke kontaktes.

Hvis du allerede bruger [ADM klientbibliotek], og allerede har [integreret ADM] kan du direkte gå til android-sdk-adm-modtager.

Hvis du ikke har integreret ADM endnu, har aftale en nemmere måde at aktivere det i dit program:

Redigere din `AndroidManifest.xml` fil:

-   Tilføje navneområdet Amazon filen skal begynde således:

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   I den `<application/>` mærke, skal du tilføje dette afsnit:

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Når du har tilføjet mærket amazon, kan du muligvis har fejlen build, hvis dit projekt opbygge mål er under Android 2.1. Du skal bruge et **Android 2.1 +** build mål (bare rolig, kan du stadig få en `minSdkVersion` indstillet til 4).
-   Integrere ADM API-nøgle som et aktiv ved følgende [denne procedure].

Følg derefter instruktionerne i næste afsnit.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Kommunikere registrering-id til aftale Push-tjenesten og modtage meddelelser

For at kommunikere kameraet, for at tjenesten aftale Push registrering id og modtage dens meddelelser, skal du tilføje følgende til din `AndroidManifest.xml` fil, inden de `<application/>` mærke (også selvom du bruger ADM uden aftale):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Sikre, at du har følgende tilladelser i din `AndroidManifest.xml` (før den `</application>` mærke).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Giv aftale OAuth-legitimationsoplysninger

Sende legitimationsoplysningerne OAuth (klient-ID og klienten hemmeligt) i aftale Portal.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM klientbibliotek]:https://developer.amazon.com/sdk/adm/setup.html
[integreret ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Denne procedure]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
