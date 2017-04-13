<properties
    pageTitle="Sådan konfigureres Google godkendelse for din App Services-program"
    description="Lær at konfigurere Google godkendelse til din App Services-program."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Sådan konfigureres din App-tjenesteprogrammet bruge Google-logon

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dette emne beskrives, hvordan du konfigurerer Azure App Service for at bruge Google som en udbyder af godkendelse.

For at fuldføre fremgangsmåden i dette emne, skal du have en Google-konto, der har en bekræftet mailadresse. Hvis du vil oprette en ny Google-konto, skal du gå til [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrere dit program med Google

1. Logge på [Azure-portalen], og gå til dit program. Kopiér **URL-adresse**, som du senere bruge til at konfigurere din Google-app.

2. Gå til webstedet [Google API'er](http://go.microsoft.com/fwlink/p/?LinkId=268303) , log på med legitimationsoplysningerne Google-konto, skal du klikke på **Opret projekt**, Angiv et **navn på projekt**og derefter klikke på **Opret**.

3. Klik på **Google + API** og derefter **aktivere**under **Sociale API'er** .

4. I venstre navigationsrude, **legitimationsoplysninger** > **OAuth samtykke skærm**, derefter vælge din **mailadresse**, Angiv et **Produktnavn**og klikke på **Gem**.

5. Klik på **Opret legitimationsoplysninger**under fanen **legitimationsoplysninger**  > **OAuth klient-ID**og derefter vælge **-webprogrammet**.

6. Indsætte den App Service **URL-adresse** , du kopierede tidligere **Godkendt JavaScript baggrunden**og derefter indsætte omdirigeringen URI i **Godkendt omdirigere URI**. Omdiriger URI er URL-adressen for dit program, der er føjet med stien, _/.auth/login/google/callback_. For eksempel `https://contoso.azurewebsites.net/.auth/login/google/callback`. Sørg for, at du bruger HTTPS farveskema. Klik derefter på **Opret**.

7. På det næste skærmbillede skal du notere af værdierne i de klient-ID og klienten hemmeligt.


    > [AZURE.IMPORTANT]
    Hemmeligheden bag klient er et vigtigt legitimationsoplysninger. Undlad at dele denne hemmeligt med alle eller distribuerer den i en-klientprogrammet.


## <a name="secrets"> </a>Tilføje Google oplysninger til dit program

8. Gå tilbage i [Azure portal], til dit program. Klik på **Indstillinger**, og derefter **godkendelse / godkendelse**.

9. Hvis godkendelsen / godkendelse funktion er ikke aktiveret skal du slå indstillingen til **på**.

10. Klik på **Google**. Indsæt i feltet App-ID og App hemmeligt værdier, som du tidligere har hentet, og du kan også aktivere alle områder, der kræver, at dit program. Klik derefter på **OK**.

    ![][1]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

17. (Valgfrit) For at begrænse adgangen til dit websted for at kun brugere, der er godkendt af Google, du Angiv **handling skal udføre, når der ikke er godkendt anmodning om** til **Google**. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Google til godkendelse.

12. Klik på **Gem**.

Du er nu klar til at bruge Google til godkendelse i din app.

## <a name="related-content"> </a>Relateret indhold

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portalen]: https://portal.azure.com/

