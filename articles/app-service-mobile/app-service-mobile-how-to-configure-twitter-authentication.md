<properties
    pageTitle="Sådan konfigureres Twitter godkendelse for din App Services-program"
    description="Lær, hvordan du konfigurerer Twitter godkendelse for din App Services-program."
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

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Sådan konfigureres din App-tjenesteprogrammet bruge Twitter-logon

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dette emne beskrives, hvordan du konfigurerer Azure App Service for at bruge Twitter som en udbyder af godkendelse.

For at fuldføre fremgangsmåden i dette emne, skal du have en Twitter-konto, der har en bekræftet e-mail-adresse og dit telefonnummer. Hvis du vil oprette en ny Twitter-konto, skal du gå til <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrere dit program med Twitter


1. Logge på [Azure-portalen], og gå til dit program. Kopiér **URL-adresse**. Du skal bruge adgangskoden til at konfigurere din Twitter-app.

2. Gå til webstedet [Twitter-udviklere] , log på med legitimationsoplysningerne Twitter-konto, og klik på **Opret program**.

3. Skriv **navnet** og en **Beskrivelse** af din nye app. Sæt ind i dit program er **URL-adressen** for værdien **websted** . Indsæt **Tilbagekald URL-adresse** , du kopierede tidligere **Tilbagekald URL-adressen**til. Dette er din Mobile-App gateway, der er føjet med stien, _/.auth/login/twitter/callback_. For eksempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Sørg for, at du bruger HTTPS farveskema.

3.  Nederst på siden, Læs og Acceptér vilkårene. Klik derefter på **Opret Twitter-program**. Dette registrerer app, vises der oplysninger om programmet.

4. Klik på fanen **Indstillinger** , Kontrollér **, at programmet skal bruges til at logge på med Twitter**og derefter klikke på **Opdateringsindstillinger**.

5. Vælg fanen **nøgler og Access Tokens** . Skal du notere af værdierne i **Consumer nøgle (API-nøgle)** og **forbrugere hemmeligt (API hemmeligt)**.

    > [AZURE.NOTE] Hemmeligheden bag consumer er et vigtigt legitimationsoplysninger. Undlad at dele denne hemmeligt med alle eller distribuerer den med din app.


## <a name="secrets"> </a>Tilføje Twitter-oplysninger til dit program

13. Gå tilbage i [Azure portal], til dit program. Klik på **Indstillinger**, og derefter **godkendelse / godkendelse**.

14. Hvis godkendelsen / godkendelse funktion er ikke aktiveret skal du slå indstillingen til **på**.

15. Klik på **Twitter**. Indsæt i App-ID og App hemmeligt værdier, som du tidligere har hentet. Klik derefter på **OK**.

    ![][1]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

17. (Valgfrit) Angiv **handling skal udføre, når der ikke er godkendt anmodning** til **Twitter**for at begrænse adgangen til dit websted for at kun brugere, der er godkendt af Twitter. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Twitter til godkendelse.

17. Klik på **Gem**.

Du er nu klar til at bruge Twitter til godkendelse i din app.

## <a name="related-content"> </a>Relateret indhold

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter udviklere]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-portalen]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
