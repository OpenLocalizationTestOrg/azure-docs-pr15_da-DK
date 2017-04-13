<properties
    pageTitle="Azure Active Directory-B2C: Konfiguration Microsoft-konto | Microsoft Azure"
    description="Angive tilmelding og -logon til forbrugere med Microsoft-konti i dine programmer, der er sikret af Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory-B2C: Angive tilmelding og -logon til forbrugere med Microsoft-konti

## <a name="create-a-microsoft-account-application"></a>Oprette et Microsoft-konto til computeren

Hvis du vil bruge Microsoft-konto som identitetsudbyder i Azure Active Directory (Azure AD) B2C, skal du oprette et Microsoft-konto til computeren og leverer den med de rigtige parametre. Du har brug for en Microsoft-konto at gøre dette. Hvis du ikke har en, kan du få den på [https://www.live.com/](https://www.live.com/).

1. Gå til [Microsoft program registrering Portal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) og logge på med din Microsoft-kontolegitimationsoplysninger.
2. Klik på **Tilføj en app**.

    ![Microsoft-konto – Tilføj en ny app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Angiv et **navn** for dit program, og klik på **Opret et program**.

    ![Microsoft-konto – App-navn](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Kopiér værdien af **Program-Id**. Du skal bruge den til at konfigurere Microsoft-konto som en identitetsudbyder i din lejer.

    ![Microsoft-konto – program-Id](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Klik på **Tilføj platform** og vælg **Web**.

    ![Microsoft-konto - Tilføj platform](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Microsoft-konto – Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Angiv `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i feltet **Omdirigere URI'er** . Erstat **{lejer}** med din lejer navn (for eksempel contosob2c.onmicrosoft.com).

    ![Microsoft-konto – omdirigere URL-adresse](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Klik på **Opret ny adgangskode** under afsnittet **Programmet hemmeligheder** . Kopiere den nye adgangskode, der vises på skærmen. Du skal bruge den til at konfigurere Microsoft-konto som en identitetsudbyder i din lejer. Denne adgangskode er et vigtigt legitimationsoplysninger.

    ![Microsoft-konto – Opret ny adgangskode](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Microsoft-konto – ny adgangskode](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Markér afkrydsningsfeltet, hvor der står **Live SDK understøtter** under sektionen **Avancerede indstillinger** . Klik på **Gem**.

    ![Microsoft-konto – Live SDK support](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Konfigurere Microsoft-konto som en identitetsudbyder i din lejer

1. Følg disse trin til at [navigere til bladet B2C funktioner](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) på Azure-portalen.
2. Klik på **identitetsudbydere**bladet B2C funktioner.
3. Klik på **+ Tilføj** øverst i bladet.
4. Give et fuldt **navn** til identitet udbyder konfigurationen. Angiv eksempelvis "MSA".
5. Klik på **identitet udbyder type**, Vælg **Microsoft-konto**, og klik på **OK**.
6. Klik på **Konfigurer denne identitetsudbyder** , og Angiv program-Id og adgangskoden til programmet Microsoft-konto, du oprettede tidligere.
7. Klik på **OK** , og klik derefter på **Opret** for at gemme konfigurationen af Microsoft-konto.
