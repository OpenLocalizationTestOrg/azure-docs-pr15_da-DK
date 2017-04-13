<properties
    pageTitle="Tilføje godkendelse på Android med Mobile Apps | Azure App Service"
    description="Lær at bruge Mobile-Apps i Azure App-tjeneste til at godkende brugere af din Android app gennem en række forskellige identitetsudbydere, herunder Google, Facebook, Twitter og Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Føje godkendelse til din Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Oversigt

I dette selvstudium føjer du godkendelse til projektets todolist Hurtig start på Android ved hjælp af en understøttede identitetsudbyder. Dette selvstudium er baseret på det [komme i gang med Mobile Apps] selvstudium, som du skal udføre først.

##<a name="register"></a>Registrere din app til godkendelse og konfigurere tjenesten App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Åbn projektet, der er den forventede du fuldført med selvstudiet [komme i gang med Mobile-Apps]i Android Studio. Klik på **Kør app** fra menuen **Kør** og bekræfte, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter.

     Denne undtagelse sker, fordi app forsøger at få adgang til back-end som en ikke-godkendte bruger, men tabellen _TodoItem_ nu kræver godkendelse.

Derefter skal opdatere du appen for at godkende brugere inden du anmoder om ressourcer fra Mobile-App backend-version.

## <a name="add-authentication-to-the-app"></a>Føje godkendelse til appen

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Cache godkendelsestokens på klienten

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Næste trin

Nu hvor du har fuldført selvstudiet basisgodkendelse, skal du overveje fortsætter på en af følgende selvstudier:

+ [Tilføj pushmeddelelser til din Android-app](app-service-mobile-android-get-started-push.md) Lær, hvordan du konfigurerer din Mobile-App back-end for at bruge Azure meddelelse Hubs til at sende pushmeddelelser.

+ [Aktivere offlinesynkronisering til din Android-appen](app-service-mobile-android-get-started-offline-data.md) Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App back-end. Offlinesynkronisering gør det muligt for slutbrugere mulighed for at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introduktion til Mobile-Apps]: app-service-mobile-android-get-started.md
