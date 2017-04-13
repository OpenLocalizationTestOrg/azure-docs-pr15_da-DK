<properties
    pageTitle="Azure Active Directory-B2C: Facebook konfiguration | Microsoft Azure"
    description="Angive tilmelding og -logon til forbrugere med Facebook-konti i dine programmer, der er sikret af Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory-B2C: Angive tilmelding og -logon til forbrugere med Facebook-konti

## <a name="create-a-facebook-application"></a>Oprette en Facebook-program

Hvis du vil bruge Facebook som identitetsudbyder i Azure Active Directory (Azure AD) B2C, skal du oprette en Facebook-program og leverer den med de rigtige parametre. Du har brug for en Facebook-konto at gøre dette. Hvis du ikke har en, kan du få den på [https://www.facebook.com/](https://www.facebook.com/).

1. Gå til webstedet [Facebook for udviklere](https://developers.facebook.com/) og logge på med legitimationsoplysningerne Facebook-konto.
2. Hvis du ikke allerede har gjort det, skal du registrere som Facebook udvikler. Gør dette, klik på **Registrer** (i øverste højre hjørne af siden), accepterer Facebooks politikker, og Fuldfør trinnene registrering.
3. Klik på **Mine Apps** , og klik derefter på **Tilføj en ny App**. Vælg **websted** som platformen, og klik derefter på **Spring over og oprette App-ID**.

    ![Facebook - Tilføj en ny App](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - Tilføj en ny App - websted](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - oprette App-ID](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. I formularen, giver et **Vist navn**, en gyldig **Kontakt mail**, en relevante **kategori**, og klik på **Opret App-ID**. Dette kræver, at du accepterer Facebook platform politikker og fuldføre en online sikkerhedskontrol.

    ![Facebook - oprette en ny App-ID](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Klik på **Indstillinger** i venstre navigationsrude.
6. Klik på **+ Tilføj Platform** , og vælg derefter **webstedet**.

    ![Facebook - indstillinger](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook - indstillinger - websted](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Angiv [https://login.microsoftonline.com/](https://login.microsoftonline.com/) i feltet **Webstedets URL-adresse** , og klik derefter på **Gem ændringer**.

    ![Facebook - webstedets URL-adresse](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Kopiér værdien af **App-ID**. Klik på **Vis** , og Kopiér værdien af **App hemmeligt**. Du skal begge af dem til at konfigurere Facebook som identitetsudbyder i din lejer. **Hemmeligheden bag App** er et vigtigt legitimationsoplysninger.

    ![Facebook - App-ID og App hemmeligt](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Klik på **+ Tilføj produkt** i venstre navigationsrude og derefter knappen **Introduktion** ud for **Facebook Login**.

    ![Facebook - Facebook-logon](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Angiv `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i feltet **gyldig OAuth omdirigere URI'er** i sektionen **OAuth klientindstillinger** . Erstat **{lejer}** med din lejer navn (for eksempel contosob2c.onmicrosoft.com). Klik på **Gem ændringer** i bunden af siden.

    ![Facebook - OAuth Omdiriger URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. For at gøre programmet Facebook bruges af Azure AD B2C, skal du gøre det offentligt tilgængeligt. Du kan gøre dette ved at klikke på **App Gennemse** i den venstre navigationslinje og ved at slå indstillingen øverst på siden til **Ja** og klikke på **Bekræft**.

    ![Facebook - App offentlige](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurere Facebook som identitetsudbyder i din lejer

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **identitetsudbydere**bladet B2C funktioner.
3. Klik på **+ Tilføj** øverst i bladet.
4. Give et fuldt **navn** til identitet udbyder konfigurationen. For eksempel Angiv "FB".
5. Klik på **identitet udbyder type**, Vælg **Facebook**, og klik på **OK**.
6. Klik på **Konfigurer denne identitetsudbyder** , og Angiv app-ID og app hemmeligt (af Facebook-programmet, som du oprettede tidligere) i **Klient-ID** og **klienten hemmeligt** felter henholdsvis.
7. Klik på **OK**, og klik derefter på **Opret** for at gemme din Facebook-konfiguration.
