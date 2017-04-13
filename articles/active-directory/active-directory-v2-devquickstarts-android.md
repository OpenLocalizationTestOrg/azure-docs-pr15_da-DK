<properties
    pageTitle="Azure Active Directory v2.0 Android-appen | Microsoft Azure"
    description="Sådan oprettes en Android-app, der logger på brugere med både personlige Microsoft-konto, arbejde eller skolekonti og Graph API-opkald ved hjælp af fra tredjepart biblioteker."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

#  <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Føje-logon til en Android-app ved hjælp af en tredjepartsudbyder af bibliotek med Graph API ved hjælp af slutpunktet v2.0

Microsoft identitet platform bruger åbne standarder som OAuth2 og OpenID forbindelse. Udviklere kan bruge enhver type bibliotek, de vil du integrere systemet med vores tjenester. For at hjælpe udviklere med at bruge vores platform med andre biblioteker, har vi skrevet et par gennemgange som denne til at vise, hvordan du konfigurerer tredjeparts-biblioteker til at oprette forbindelse til Microsoft identitet platform. De fleste biblioteker, der implementerer [RFC6749 OAuth2 specifikationen](https://tools.ietf.org/html/rfc6749) kan oprette forbindelse til Microsoft identitet platform.

Brugere kan logge på deres organisation og derefter søge efter selv i deres organisation ved hjælp af Graph API med det program, der opretter denne gennemgang.

Hvis du er ny bruger af OAuth2 eller OpenID oprette forbindelse, kan meget af denne eksempel konfiguration ikke giver mening for dig. Vi anbefaler, at du læser [2.0 protokoller - OAuth 2.0 godkendelse kode dataflow](active-directory-v2-protocols-oauth-code.md) som baggrund.

> [AZURE.NOTE] Nogle funktioner i vores platform, som har et udtryk i OAuth2 eller OpenID forbinde standarderne, såsom betinget adgang og administration af Intune politikker, skal du bruge vores Åbn kilde Microsoft Azure identitet biblioteker.

V2.0 slutpunktet understøtter ikke alle Azure Active Directory scenarier og funktioner.

> [AZURE.NOTE] For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).


## <a name="download-the-code-from-github"></a>Hent koden fra GitHub
Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) eller klone skelet:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Du kan også bare hente stikprøvernes og komme i gang:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [programmet registrering portal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg de detaljerede trin, [hvordan man kan registrere en app til v2.0 slutpunktet](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiere **Program-Id** , der er tildelt din app, fordi du skal bruge den snart.
- Tilføj **Mobile** platformen til din app.

> Bemærk: Programmet registrering portal giver en **Omdirigere URI** -værdi. Men i dette eksempel skal du bruge standardværdien for `https://login.microsoftonline.com/common/oauth2/nativeclient`.


## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Hente biblioteket NXOAuth2 fra tredjepart og oprette et arbejdsområde

I denne gennemgang skal bruge du OIDCAndroidLib fra GitHub, som er et OAuth2 bibliotek, der er baseret på koden OpenID forbinde for Google. Den implementerer den oprindelige programprofil og understøtter godkendelse slutpunktet på brugeren. Dette er alle de ting, du skal integreres med Microsoft identitet platforme.

Klone OIDCAndroidLib repo til din computer.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Konfigurere dit Android Studio-miljø

1. Oprette et nyt Android Studio-projekt, og Accepter standardindstillingerne i guiden.

    ![Oprette nyt projekt i Android Studio](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

    ![Target Android-enheder](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

    ![Føje en aktivitet til mobile](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

2. For at konfigurere din project-moduler skal du flytte klonede repo til projektplacering. Du kan også oprette projektet og klone den direkte til Projektplaceringen.

    ![Project moduler](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

3. Åbn moduler projektindstillingerne ved hjælp af genvejsmenuen eller ved at bruge genvejen Ctrl + Alt + Maj + S.

    ![Indstillinger for Project moduler](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

4. Fjerne modulet standard app, fordi du kun vil objektbeholder projektindstillingerne.

    ![Modulet standard app](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

5. Importere moduler fra klonede repo til det aktuelle projekt.

    ![Importér gradle projekt](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG)
    ![Opret ny modul side](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

6. Gentag disse trin for den `oidlib-sample` modul.

7. Kontroller oidclib afhængigheder på den `oidlib-sample` modul.

    ![oidclib afhængigheder af modulet oidlib stikprøver](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

8. Klik på **OK** , og vent på gradle synkronisering.

    Din settings.gradle skal se sådan ud:

    ![Skærmbillede af settings.gradle](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

9. Opbygge appen eksempel for at sikre dig, at eksemplet kører korrekt.

    Du kan ikke bruges sammen med Azure Active Directory endnu. Vi skal konfigurere nogle slutpunkter først. Dette er at sikre, at du ikke har en Android Studio problemer, før vi går i gang med at tilpasse appen eksempel.

10. Oprette og køre `oidlib-sample` som destination i Android Studio.

    ![Status for oidlib stikprøver build](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

11. Slette den `app ` directory, der er tilbage, når du har fjernet modulet fra projektet, fordi det ikke slettet, Android Studio for sikkerhed.

    ![Filstruktur, der indeholder app-kataloget](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

12. Åbn menuen **Rediger konfigurationer** for at fjerne den køre konfiguration, der er også efterladt, når du har fjernet modulet fra projektet.

    ![Konfigurationer menuen Rediger](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![køre konfiguration af app](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Konfigurere slutpunkter stikprøvens

Nu hvor du har den `oidlib-sample` kører korrekt, Lad os redigere nogle slutpunkter for at få denne arbejde med Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Konfigurer din klient ved at redigere filen oidc_clientconf.xml

1. Fordi du bruger OAuth2 flyder kun at hente et token og kalde Graph API skal du angive klienten til at gøre OAuth2 kun. OIDC kommer i en nyere eksempel.

    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```

2. Konfigurere din klient-ID, du har modtaget fra portalen registrering.

    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```

3. Konfigurere omdirigeringen URI med den nedenfor.

    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```

4. Konfigurere din områder, du skal bruge for at få adgang til API Graph.

    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

Den `User.Read` værdi i `oidc_scopes` gør det muligt at læse den grundlæggende profil den signerede bruger.
Du kan få mere at vide om alle de tilgængelige områder på [Microsoft Graph tilladelse områder](https://graph.microsoft.io/docs/authorization/permission_scopes).

Hvis du vil have forklaringer om `openid` eller `offline_access` som områder i OpenID oprette forbindelse, se [2.0 protokoller - OAuth 2.0 godkendelse kode dataflow](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Konfigurere din klient slutpunkter ved at redigere filen oidc_endpoints.xml

- Åbn den `oidc_endpoints.xml` filen og foretage følgende ændringer:

    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Disse slutpunkter skal aldrig ændre, hvis du bruger OAuth2 som protokol.

> [AZURE.NOTE]
Slutpunkterne for `userInfoEndpoint` og `revocationEndpoint` der i øjeblikket ikke understøttes af Azure Active Directory. Hvis du lader dem med example.com standardværdien, bliver du mindet, at de ikke er tilgængelige i stikprøven :-)


## <a name="configure-a-graph-api-call"></a>Konfigurere et Graph API-opkald

- Åbn den `HomeActivity.java` filen og foretage følgende ændringer:

    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Et enkelt Graph API-kald returnerer her vores oplysninger.

Dette er alle de ændringer, du skal gøre. Køre den `oidlib-sample` -program, og klik på **Log på**.

Når du har godkendt, Vælg knappen **Anmod om beskyttet ressource** til at teste dit opkald til Graph API.

## <a name="get-security-updates-for-our-product"></a>Hente sikkerhedsopdateringer til vores produkt

Vi anbefaler, at få besked om sikkerhedshændelser ved at besøge [Security TechCenter](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
