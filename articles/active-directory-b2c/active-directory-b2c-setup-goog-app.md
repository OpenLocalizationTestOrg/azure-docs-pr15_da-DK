<properties
    pageTitle="Azure Active Directory-B2C: Google + konfiguration | Microsoft Azure"
    description="Angive tilmelding og -logon til forbrugere med Google + konti i dine programmer, der er sikret af Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory-B2C: Angive tilmelding og -logon til forbrugere med Google + konti

## <a name="create-a-google-application"></a>Oprette et Google +-program

Hvis du vil bruge Google + som identitetsudbyder i Azure Active Directory (Azure AD) B2C, skal du oprette et Google +-program og leverer den med de rigtige parametre. Du har brug for en Google +-konto for at gøre dette. Hvis du ikke har en, kan du få den på [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Gå til [Google udviklere Console](https://console.developers.google.com/) og logge på med din Google + kontolegitimationsoplysninger.
2. Klik på **Opret projekt**, Angiv et **Projektnavn**, og klik derefter på **Opret**.

    ![Google + - Introduktion](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Klik på **Styring af API** , og klik derefter på **legitimationsoplysninger** i venstre navigationsrude.
4. Klik på fanen **OAuth samtykke skærmen** øverst.

    ![Google + - legitimationsoplysninger](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Vælge eller angive en gyldig **mailadresse**, Angiv et **Produktnavn**, og klik på **Gem**.

    ![Google + - OAuth samtykke skærmen](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Klik på **Ny legitimationsoplysninger** , og vælg derefter **OAuth klient-ID**.

    ![Google + - OAuth samtykke skærmen](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Vælg **webprogram**under **programtype**.

    ![Google + - OAuth samtykke skærmen](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Angiv et **navn** for dit program, skal du angive `https://login.microsoftonline.com` i feltet **godkendt JavaScript baggrunden** og `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i feltet **tilladte omdirigere URI'er** . Erstat **{lejer}** med din lejer navn (for eksempel contosob2c.onmicrosoft.com). **{Lejer}** værdien er store og små bogstaver. Klik på **Opret**.

    ![Google + - oprette klient-ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Kopiér værdierne for **Klient-ID** og **klienten hemmeligt**. Du skal begge af dem til at konfigurere Google + som identitetsudbyder i din lejer. **Hemmeligheden bag klient** er et vigtigt legitimationsoplysninger.

    ![Google + - klient hemmeligt](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Konfigurere Google + som identitetsudbyder i din lejer

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **identitetsudbydere**bladet B2C funktioner.
3. Klik på **+ Tilføj** øverst i bladet.
4. Give et fuldt **navn** til identitet udbyder konfigurationen. For eksempel Angiv "G +".
5. Klik på **identitet udbyder type**, Vælg **Google**, og klik på **OK**.
6. Klik på **Konfigurer denne identitetsudbyder** , og Angiv klient-ID og klienten hemmeligt af Google + programmet, som du oprettede tidligere.
7. Klik på **OK** , og klik derefter på **Opret** for at gemme din Google + konfiguration.
