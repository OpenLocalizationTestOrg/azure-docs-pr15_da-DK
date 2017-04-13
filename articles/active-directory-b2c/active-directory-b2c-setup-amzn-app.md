<properties
    pageTitle="Azure Active Directory-B2C: Amazon konfiguration | Microsoft Azure"
    description="Angive tilmelding og -logon til forbrugere med Amazon konti i dine programmer, der er sikret af Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory-B2C: Angive tilmelding og -logon til forbrugere med Amazon konti

## <a name="create-an-amazon-application"></a>Oprette et Amazon program

Hvis du vil bruge Amazon som identitetsudbyder i Azure Active Directory (Azure AD) B2C, skal du oprette et Amazon program og leverer den med de rigtige parametre. Du har brug for en Amazon-konto for at gøre dette. Hvis du ikke har en, kan du få den på [http://www.amazon.com/](http://www.amazon.com/).

1. Gå til [Amazon Developer Center](https://login.amazon.com/) , og log på med legitimationsoplysningerne Amazon konto.
2. Hvis du ikke allerede har gjort det, skal du klikke på **Tilmeld dig**, skal du benytte følgende udvikler registrering og acceptere politikken.
3. Klik på **registrering af nye program**.

    ![Registrere et nyt program på webstedet Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Giver programoplysninger (**navn**, **Beskrivelse**og **Beskyttelse af personlige oplysninger om URL-adresse**), og klik på **Gem**.

    ![Give programoplysninger til registrering af et nyt program hos Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Kopiér værdierne for **Klient-ID** og **Klienten hemmeligt**i sektionen **Indstillinger for websted** . (Du skal klikke på knappen **Vis hemmeligt** for at se det.) Du skal bruge dem til at konfigurere Amazon som identitetsudbyder i din lejer begge. Klik på **Rediger** nederst i sektionen. **Hemmeligheden bag klient** er et vigtigt legitimationsoplysninger.

    ![Give klient-ID og klienten hemmeligt til dit nye program hos Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Angiv `https://login.microsoftonline.com` i feltet **Tilladt JavaScript baggrunden** og `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i feltet **Tilladt returnere URL-adresser** . Erstat **{lejer}** med din lejer navn (for eksempel contoso.onmicrosoft.com). Klik på **Gem**. **{Lejer}** værdien er store og små bogstaver.

    ![Give JavaScript baggrunden og returnere URL-adresser til dit nye program hos Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurere Amazon som identitetsudbyder i din lejer

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **identitetsudbydere**bladet B2C funktioner.
3. Klik på **+ Tilføj** øverst i bladet.
4. Give et fuldt **navn** til identitet udbyder konfigurationen. Angiv eksempelvis "Amzn".
5. Klik på **identitet udbyder type**, Vælg **Amazon**, og klik på **OK**.
6. Klik på **Konfigurer denne identitetsudbyder** , og Angiv klient-ID og klienten hemmeligt af Amazon programmet, som du oprettede tidligere.
7. Klik på **OK** , og klik derefter på **Opret** for at gemme din Amazon konfiguration.
