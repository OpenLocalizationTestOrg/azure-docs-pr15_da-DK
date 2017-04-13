<properties
    pageTitle="Azure Active Directory-B2C: Oprette en Azure Active Directory B2C lejer | Microsoft Azure"
    description="Et emne om, hvordan du opretter en Azure Active Directory B2C lejer"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory-B2C: Oprette en Azure AD B2C lejer

Hvis du vil begynde at bruge Microsoft Azure Active Directory (Azure AD) B2C, skal du følge de tre trin, der er beskrevet i denne artikel.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Trin 1: Tilmeld dig til et Azure-abonnement

Hvis du allerede har et Azure-abonnement, kan du springe dette trin over. Hvis ikke, kan du tilmelde dig en [Azure-abonnement](../active-directory/sign-up-organization.md) og få adgang til Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Trin 2: Oprette en Azure AD B2C lejer

Brug følgende trin til at oprette en ny Azure AD B2C lejer. I øjeblikket kan ikke B2C funktioner være slået til i din eksisterende lejere.

1. Log på [Azure klassisk portal](https://manage.windowsazure.com/) som abonnement Administrator. Dette er den samme arbejds- eller skolekonto eller den samme Microsoft-konto, du har brugt til at tilmelde sig Azure.
2. Klik på **nyt** > **App Services** > **Active Directory** > **Directory** > **brugerdefineret oprette**.

    ![Skærmbillede af at starte til at oprette en lejer](./media/active-directory-b2c-get-started/new-directory.png)

3. Vælg **navnet**, **Domænenavnet** og **land eller område** for din lejer.
4. Markér den indstilling, der siger, at **dette er en B2C-mappe**.
5. Klik på markeringen i afkrydsningsfeltet for at fuldføre handlingen.

    ![Skærmbillede af markering til at oprette en B2C-mappe](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Din lejer er nu oprettet og vises i Active Directory-filtypenavn. Du er også har lavet en Global Administrator i lejeren. Du kan tilføje andre globale administratorer efter behov.

    > [AZURE.IMPORTANT]
    Hvis du planlægger at bruge en B2C lejer til en fremstilling app, kan du læse artiklen på [fremstilling skalering kontra preview B2C lejere](active-directory-b2c-reference-tenant-type.md). Bemærk, at der er kendte problemer, når du sletter en eksisterende B2C lejer og oprette den igen med det samme domænenavn. Du skal oprette en B2C lejer med et andet domænenavn.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Trin 3: Gå til bladet B2C funktioner på Azure-portalen

1. Gå til i Active Directory-udvidelse på navigationslinjen i venstre side.
2. Find din lejer under fanen **mappe** og klikke på den.
3. Klik på **Konfigurer** .
4. Klik på linket **Administrer B2C indstillinger** i sektionen **B2C administration** .

    ![Skærmbillede af directory konfiguration for B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Azure-portalen med bladet B2C funktioner, der viser åbnes i en ny fane i browseren eller et vindue.

    > [AZURE.IMPORTANT]
    Det kan tage op til 2-3 minutter for din lejer er tilgængelige på Azure-portalen. Gentage disse trin, når stykke tid kan løse dette problem. Hvis ikke, skal du kontakte Support.

6. Fastgøre denne blade til din Startboard for nem adgang. (Værktøjet PIN-kode er markeret med rødt i øverste højre hjørne af bladet funktioner).

    ![Skærmbillede af bladet B2C funktioner](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Du kan administrere brugere og grupper, selvbetjening Nulstil konfiguration og virksomhed mærkning funktioner i din lejer på [Azure klassisk portal](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Nem adgang til bladet B2C funktioner på Azure-portalen

For at forbedre synligheden, har vi tilføjet en genvej til bladet B2C funktioner på Azure-portalen.

1. Log på portalen Azure som den globale Administrator af din B2C lejer. Hvis du allerede er logget på en anden lejer, kan du skifte lejere (i øverste højre hjørne).
2. Klik på **Gennemse** i den venstre navigationslinje.
3. Klik på **Azure AD B2C** for at få adgang til bladet B2C funktioner.

    ![Skærmbillede af Gennemse til B2C funktioner blade](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Næste trin

Lær at registrere et program med Azure AD B2C og for at opbygge et program i Hurtig Start ved at læse [Azure Active Directory B2C: registrere dit program](active-directory-b2c-app-registration.md).
