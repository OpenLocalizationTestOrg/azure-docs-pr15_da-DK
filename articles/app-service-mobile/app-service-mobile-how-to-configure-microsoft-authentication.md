<properties
    pageTitle="Sådan konfigureres Microsoft Account godkendelse for din App Services-program"
    description="Lær, hvordan du konfigurerer Microsoft Account godkendelse for din App Services-program."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Sådan konfigureres din App-tjenesteprogrammet bruge Microsoft Account logon

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dette emne beskrives, hvordan du konfigurerer Azure App Service for at bruge Microsoft-Account som en udbyder af godkendelse. 

## <a name="register-microsoft-account"> </a>Registrere din app til Microsoft-konto

1. Logge på [Azure-portalen], og gå til dit program. Kopiér **URL-adresse**, som bruges til at konfigurere din app til Microsoft-Account senere.

2. Gå til siden [Mine programmer] i Microsoft Account Developer Centers, og log på med din Microsoft-konto, hvis det er nødvendigt.

3. Klik på **Tilføj en app**, og derefter skrive navnet på et program, og klik på **Opret et program**.

4. Skal du notere **Program-ID**, som du skal bruge den senere. 

5. Klik på **Tilføj Platform** under "Platforme", og vælg "Web".

6. Under "Omdirigere URI'er" leverer slutpunktet for dit program, og klik derefter på **Gem**. 
 
    >[AZURE.NOTE]Omdirigeringen URI er URL-adressen for dit program, der er føjet med stien, _/.auth/login/microsoftaccount/callback_. For eksempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Sørg for, at du bruger HTTPS farveskema.

7. Klik på **Opret ny adgangskode**under "Programmet hemmeligheder,". Noter den værdi, der vises. Når du forlader siden, vises den ikke igen.


    > [AZURE.IMPORTANT] Adgangskoden er et vigtigt legitimationsoplysninger. Undlad at dele adgangskoden med alle eller distribuerer den i en-klientprogrammet.

## <a name="secrets"> </a>Tilføj Microsoft-konto oplysninger til din App-tjenesteprogram

1. Tilbage i [Azure-portalen], gå til dit program skal du klikke på **Indstillinger for** > **godkendelse / godkendelse**.

2. Hvis godkendelsen / godkendelse funktion ikke er aktiveret, skal du skifte den **på**.

3. Klik på **Microsoft-konto**. Indsæt værdierne program-ID og din adgangskode, du tidligere har hentet, og du kan også aktivere alle områder, der kræver, at dit program. Klik derefter på **OK**.

    ![][1]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

4. (Valgfrit) For at begrænse adgangen til dit websted for at kun brugere, der er godkendt af Microsoft-konto, du Angiv **handling skal udføre, når der ikke er godkendt anmodning om** **Microsoft**-konto. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Microsoft-konto til godkendelse.

5. Klik på **Gem**.

Nu er du klar til at bruge Microsoft Account til godkendelse i din app.

## <a name="related-content"> </a>Relateret indhold

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mine-programmer]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portalen]: https://portal.azure.com/
