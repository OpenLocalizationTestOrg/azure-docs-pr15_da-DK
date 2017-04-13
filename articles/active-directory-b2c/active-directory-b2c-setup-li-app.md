<properties
    pageTitle="Azure Active Directory-B2C: LinkedIn konfiguration | Microsoft Azure"
    description="Angive tilmelding og -logon til forbrugere med LinkedIn-konti i dine programmer, der er sikret af Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory-B2C: Angive tilmelding og -logon til forbrugere med LinkedIn-konti

## <a name="create-a-linkedin-application"></a>Oprette et LinkedIn-tjenesteprogram

Hvis du vil bruge LinkedIn som identitetsudbyder i Azure Active Directory (Azure AD) B2C, skal du oprette en LinkedIn-program og leverer den med de rigtige parametre. Du har brug for en LinkedIn-konto for at gøre dette. Hvis du ikke har en, kan du få den på [https://www.linkedin.com/](https://www.linkedin.com/).

1. Gå til [LinkedIn udviklere websted](https://www.developer.linkedin.com/) , og log på med legitimationsoplysningerne LinkedIn-konto.
2. Klik på **Mine Apps** i den øverste menulinje, og klik derefter på **Opret program**.

    ![LinkedIn - ny app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Udfyld de relevante oplysninger (**Firmanavn**, **navn**, **Beskrivelse**, **URL-adressen webprogrammet Logo**, **-Programmer**, **Webstedets URL-adresse**, **Business mail** og **Telefon (arbejde)**) i formularen **Opret et nyt program** .
4. Acceptere **LinkedIn API vilkår for anvendelse** , og klik på **Send**.

    ![LinkedIn - Register-app](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Kopiér værdierne for **Klient-ID** og **Klienten hemmeligt**. (Du kan finde dem under **Godkendelsesnøgler**.) Du skal begge af dem til at konfigurere LinkedIn som identitetsudbyder i din lejer.

    >[AZURE.NOTE] **Hemmeligheden bag klient** er et vigtigt legitimationsoplysninger.

6. Angiv `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i feltet **Godkendt omdirigere URL-adresser** (under **OAuth 2.0**). Erstat **{lejer}** med din lejer navn (for eksempel contoso.onmicrosoft.com). Klik på **Tilføj**, og klik derefter på **Opdater**. **{Lejer}** værdien er store og små bogstaver.

    ![LinkedIn - konfiguration app](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Konfigurere LinkedIn som identitetsudbyder i din lejer

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **identitetsudbydere**bladet B2C funktioner.
3. Klik på **+ Tilføj** øverst i bladet.
4. Give et fuldt **navn** til identitet udbyder konfigurationen. For eksempel Angiv "LI".
5. Klik på **identitet udbyder type**, Vælg **LinkedIn**, og klik på **OK**.
6. Klik på **Konfigurer denne identitetsudbyder** , og Angiv klient-ID og klienten hemmeligt af programmet LinkedIn, du oprettede tidligere.
7. Klik på **OK** , og klik derefter på **Opret** for at gemme din LinkedIn-konfiguration.
