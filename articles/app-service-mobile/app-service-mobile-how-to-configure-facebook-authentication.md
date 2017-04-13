<properties
    pageTitle="Sådan konfigureres Facebook godkendelse for din App Services-program"
    description="Lær, hvordan du konfigurerer Facebook godkendelse for din App Services-program."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Sådan konfigureres din App-tjenesteprogrammet bruge Facebook-logon

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dette emne beskrives, hvordan du konfigurerer Azure App Service for at bruge Facebook som en udbyder af godkendelse.

For at fuldføre fremgangsmåden i dette emne, skal du have en Facebook-konto, der indeholder en bekræftet mailadresse og et mobiltelefonnummer. Hvis du vil oprette en ny Facebook-konto, skal du gå til [facebook.com].

## <a name="register"> </a>Registrere dit program med Facebook

1. Logge på [Azure-portalen], og gå til dit program. Kopiér **URL-adresse**. Du skal bruge adgangskoden til at konfigurere din Facebook-app.

2. I en anden browser-vindue, gå til webstedet [Facebook udviklere] og logge på med legitimationsoplysningerne Facebook-konto.

3. (Valgfrit) Hvis du ikke allerede har registreret, klik på **Apps** > **registrere som en udvikler**, derefter acceptere politikken og følge trinnene registrering.

4. Klik på **Mine Apps** > **tilføje en ny App** > **websted** > **springe og oprette App-ID**. 

5. I **Visningsnavn**, Skriv et entydigt navn for din app skal du skrive din **Kontakt mail**, Vælg en **kategori** til din app og derefter klikke på **Opret App-ID** og gennemføre sikkerhed. Du føres til udviklerdashboard til din nye Facebook-app.

6. Klik på **Introduktion**under "Facebook logon,". Tilføje dit program **Omdirigere URI** **gyldige OAuth**omdirigere URI'er, og klik derefter på **Gem ændringer**. 

    > [AZURE.NOTE] Omdirigeringen URI er URL-adressen for dit program, der er føjet med stien, _/.auth/login/facebook/callback_. For eksempel `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Sørg for, at du bruger HTTPS farveskema.

6. Klik på **Indstillinger**på navigationslinjen til venstre. Klik på **Vis**i feltet **App hemmeligt** , giver din adgangskode, hvis anmodes om og derefter skal du notere af værdierne i **App-ID** og **App hemmeligt**. Du bruge disse senere til at konfigurere dit program i Azure.

    > [AZURE.IMPORTANT] Hemmeligheden bag app er et vigtigt legitimationsoplysninger. Undlad at dele denne hemmeligt med alle eller distribuerer den i en-klientprogrammet.

7. Den Facebook-konto som blev brugt til at registrere programmet er en administrator af appen. På dette tidspunkt kan er kun administratorer logge på dette program. Klik på **App Gennemse** for at godkende andre Facebook-konti, og Aktivér **Udgiv < dit-app-navn >** at aktivere generelle offentlig adgang ved hjælp af Facebook-godkendelse.

## <a name="secrets"> </a>Tilføje Facebook-oplysninger til dit program

1. Gå tilbage i [Azure portal], til dit program. Klik på **Indstillinger for** > **godkendelse / godkendelse**, og Sørg for, at **App Service godkendelse** er **aktiveret**.

2. Klik på **Facebook**, indsætte i feltet App-ID og App hemmeligt værdier, som du tidligere har anskaffet, du kan også aktivere alle områder, der kræves af programmet og derefter klikke på **OK**.

    ![][0]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

3. (Valgfrit) Hvis du vil begrænse adgangen til dit websted for at kun brugere, der er godkendt af Facebook, angive **handling skal udføre, når der ikke er godkendt anmodning om** til **Facebook**. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Facebook til godkendelse.

4. Klik på **Gem**, når du er færdig konfiguration af godkendelse.

Du er nu klar til at bruge Facebook til godkendelse i din app.

## <a name="related-content"> </a>Relateret indhold

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook udviklere]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portalen]: https://portal.azure.com/
