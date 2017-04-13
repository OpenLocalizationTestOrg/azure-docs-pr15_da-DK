<properties
    pageTitle="Sådan konfigureres Azure Active Directory-godkendelse til din App Services-program"
    description="Lær, hvordan du konfigurerer Azure Active Directory-godkendelse til din App Services-program."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Sådan konfigureres din App-tjenesteprogrammet bruge Azure Active Directory-logon

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Dette emne beskrives, hvordan du konfigurerer Azure App Services til at bruge Azure Active Directory som en udbyder af godkendelse.

## <a name="express"> </a>Konfigurere Azure Active Directory ved hjælp af hurtig indstillinger

13. Gå til dit program i [Azure-portalen]. Klik på **Indstillinger**, og klik derefter **Godkendelse/godkendelse**.

14. Hvis godkendelsen / godkendelse funktion er ikke aktiveret skal du slå indstillingen til **på**.

15. Klik på **Azure Active Directory**, og klik derefter på **Express** under **Administrationstilstand**.

16. Klik på **OK** for at registrere programmet i Azure Active Directory. Dette vil oprette en ny registrering. Hvis du vil vælge en eksisterende registrering i stedet, klik på **Vælg en eksisterende app** , og Søg efter navnet på en tidligere oprettet registrering i din lejer.
Klik på registrering for at markere det og klikke på **OK**. Klik derefter på **OK** i bladet Azure Active Directory-indstillinger.

    ![][0]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

17. (Valgfrit) For at begrænse adgangen til dit websted for at kun brugere, der er godkendt af Azure Active Directory, angive **handling skal udføre, når der ikke er godkendt anmodning om** at **logge på med Azure Active Directory**. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Azure Active Directory til godkendelse.

17. Klik på **Gem**.

Du er nu klar til at bruge Azure Active Directory til godkendelse i din app.

## <a name="advanced"> </a>(Alternativ metode) manuelt konfigurere Azure Active Directory med avancerede indstillinger
Du kan også vælge at angive indstillinger for søgekonfiguration manuelt. Dette er den foretrukne løsning, hvis du vil bruge AAD lejeren er forskellig fra lejeren, som du logge på Azure. For at fuldføre konfigurationen, skal du først oprette en registrering i Azure Active Directory, og derefter skal du angive nogle af registreringsoplysninger til App-tjenesten.

### <a name="register"> </a>Registrere dit program med Azure Active Directory

1. Logge på [Azure-portalen], og gå til dit program. Kopiér **URL-adresse**. Du skal bruge adgangskoden til at konfigurere din Azure Active Directory-app.

3. Log på [Azure klassisk portal] , og gå til **Active Directory**.

    ![][2]

4. Vælg mappen, og vælg derefter fanen **programmer** øverst. Klik på **Tilføj** nederst til at oprette en ny app registrering.

5. Klik på **Tilføj et program, udvikling af min organisation**.

6. Angiv et **navn** for dit program, og klik på den **Web Application og/eller Web API** i guiden Tilføj programmet på computeren. Klik for at fortsætte.

7. Indsæt programmet URL-adressen, som du kopierede tidligere, i boksen **SIGN-ON URL-adresse** . Angiv den samme URL-adresse i feltet **App ID URI** . Klik for at fortsætte.

8. Når programmet er blevet tilføjet, kan du klikke på fanen **Konfigurer** . Redigere **Svar URL-adresse** under **Single Sign-on** skal være URL-adressen for dit program, der er føjet med stien, _/.auth/login/aad/callback_. For eksempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Sørg for, at du bruger HTTPS farveskema.

    ![][3]

9. Klik på **Gem**. Kopiér **Klient-ID** for appen. Du skal konfigurere dit program til senere brug dette.

10. På kommando-linjen, skal du klikke på **Vis slutpunkter**, og derefter kopiere **Sammenslutning Metadata dokument** URL-adressen og hente dokumentet eller gå til det i en browser.

11. I **EntityDescriptor** rodelementet, der skal være en **enheds-id** -attribut i formularen `https://sts.windows.net/` efterfulgt af et bestemt GUID til din lejer (kaldet et "lejer-ID"). Kopiér denne værdi – den skal fungere som **Udsteder URL-adresse**. Du skal konfigurere dit program til senere brug dette.

### <a name="secrets"> </a>Tilføje Azure Active Directory-oplysninger i dit program

13. Gå tilbage i [Azure portal], til dit program. Klik på **Indstillinger**, og klik derefter **Godkendelse/godkendelse**.

14. Hvis funktionen godkendelse/godkendelse ikke er aktiveret, kan du slå Skift til **på**.

15. Klik på **Azure Active Directory**, og klik derefter på **Avanceret** under **Administrationstilstand**. Indsæt værdien klient-ID og udsteder URL-adresse, du har hentet tidligere. Klik derefter på **OK**.

    ![][1]

    App Service giver godkendelse som standard men forhindrer ikke autoriserede adgang til dine webstedsindhold og API'er. Du skal godkende brugere i din app-kode.

17. (Valgfrit) For at begrænse adgangen til dit websted for at kun brugere, der er godkendt af Azure Active Directory, angive **handling skal udføre, når der ikke er godkendt anmodning om** at **logge på med Azure Active Directory**. Dette kræver, at alle anmodninger godkendes, og alle ikke-godkendte anmodninger omdirigeres til Azure Active Directory til godkendelse.

17. Klik på **Gem**.

Du er nu klar til at bruge Azure Active Directory til godkendelse i din app.

## <a name="optional-configure-a-native-client-application"></a>(Valgfrit) Konfigurere et native client-program

Azure Active Directory kan du også til at registrere oprindelige klienter, der giver større kontrol over tilladelser, der er tilknytning. Du skal bruge denne, hvis du vil udføre logon ved hjælp af et bibliotek som **Active Directory Authentication Library**.

1. Gå til **Active Directory** i [Azure klassisk portal].

2. Vælg mappen, og vælg derefter fanen **programmer** øverst. Klik på **Tilføj** nederst til at oprette en ny app registrering.

3. Klik på **Tilføj et program, udvikling af min organisation**.

4. Angiv et **navn** for dit program, og klik på den **Oprindelige klientprogrammet** i guiden Tilføj programmet på computeren. Klik for at fortsætte.

5. Angiv dit websteds _/.auth/login/done_ slutpunkt, ved hjælp af HTTPS-skemaet i feltet **Omdirigere URI** . Denne værdi skal ligne _https://contoso.azurewebsites.net/.auth/login/done_. Hvis et Windows-program du opretter, i stedet bruge [pakke SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) som URI.

6. Når du har tilføjet det oprindelige program, klik på **Konfigurer** . Find **Klient-ID** og notere denne værdi.

7. Rul ned til sektionen **tilladelser til andre programmer** på siden, og klik på **Tilføj program**.

8. Søg efter webprogrammet, du har registreret tidligere, og klik på plusikonet. Klik derefter på Søg for at lukke dialogboksen. Hvis webprogrammet blev ikke fundet, gå til dens registrering og tilføje en ny svar URL-adresse (f.eks., HTTP-versionen af din aktuelle URL-adresse), klik på Gem, og gentag derefter disse trin - programmet skal vises på listen.

9. Åbn rullemenuen **Delegerede tilladelser** på den nye post, du lige har tilføjet, og vælg **Access (programnavn)**. Klik derefter på **Gem**.

Du har nu konfigureret en oprindelig klientprogrammet som kan få adgang til din App-tjenesteprogrammet.

## <a name="related-content"> </a>Relateret indhold

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
[Azure klassisk portal]: https://manage.windowsazure.com/
[alternative method]:#advanced
