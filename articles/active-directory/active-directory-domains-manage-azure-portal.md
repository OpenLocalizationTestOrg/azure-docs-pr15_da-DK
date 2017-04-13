<properties
    pageTitle="Administrere brugerdefinerede domænenavne i din Azure Active Directory-visning | Microsoft Azure"
    description="Management-begreber og vejledning til administration af et domænenavn på Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Administrere brugerdefinerede domænenavne i din Azure Active Directory-visning

Et domænenavn er en vigtig del af et id for mange directory-ressourcer: det er en del af en bruger navnet eller mailadressen for en bruger, en del af adressen for en gruppe, og kan være en del af ID URI-app til et program. En ressource i Azure Active Directory (Azure AD) preview kan indeholde et domænenavn, der allerede er godkendt til ejes af den mappe, hvor ressourcen. [Hvad er i preview?](active-directory-preview-explainer.md) En global administrator kan udføre domæne administrationsopgaver i Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Angiv det primære domænenavn til din Azure AD-mappe

Når mappen er oprettet, er navnet på det indledende domæne, som 'contoso.onmicrosoft.com', også det primære domænenavn. Det primære domæne er standarddomænenavnet for en ny bruger, når du opretter en ny bruger. Det strømliner for en administrator til at oprette nye brugere i portalen. Sådan ændrer du det primære domænenavn:

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **Azure Active Directory** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Vælg **Domain names**på bladet ***mappenavn*** .

4. Vælg det domænenavn, du vil gøre det primære domænenavn på bladet ** *mappenavn* - domænenavne** .

5.  Vælg kommandoen **Benyt som primær** på bladet ***domænenavn*** (det vil sige, bladet, der åbnes, der indeholder din nye domænenavn i titlen). Bekræfte dit valg, når du bliver bedt om.

    ![Foretage et domænenavn primære](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Du kan ændre det primære domænenavn til dit bibliotek skal være bekræftet eget domæne, der ikke er i organisationsnetværk. Ændre det primære domæne til dit bibliotek ændres ikke brugernavne for eksisterende brugere.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Føje brugerdefinerede domænenavne til din Azure AD

Du kan føje op til 900 brugerdefinerede domænenavne til hver Azure AD-mappe. Processen til at [tilføje en ekstra brugerdefineret domænenavn](active-directory-domains-add-azure-portal.md) er det samme for det første brugerdefinerede domænenavn.

## <a name="add-subdomains-of-a-custom-domain"></a>Tilføje underdomæner af et brugerdefineret domæne

Hvis du vil tilføje et tredje niveau domænenavn som 'europe.contoso.com' på adresselisten, skal du først tilføje og bekræfte domænet på andet niveau, som contoso.com. Underdomænet bliver automatisk godkendt af Azure AD. For at se, at det underdomæne, du lige har tilføjet er blevet bekræftet, at opdatere siden i browseren, der viser domænerne.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Hvad du skal gøre, hvis du ændrer DNS-registrator for dit brugerdefinerede domænenavn

Hvis du ændrer DNS-registrator for dit brugerdefinerede domænenavn, kan du fortsætte med at bruge dit eget domænenavn med Azure AD selve uden afbrydelser og uden yderligere konfigurationsopgaver. Hvis du bruger dit eget domænenavn med Office 365, Intune eller andre tjenester, der er baseret på brugerdefinerede domænenavne i Azure AD, kan du se i dokumentationen til disse tjenester.

## <a name="delete-a-custom-domain-name"></a>Slette et brugerdefineret domænenavn

Du kan slette et brugerdefineret domænenavn fra din Azure AD, hvis din organisation bruger ikke længere pågældende domænenavn, eller hvis du vil bruge denne domænenavn med en anden Azure AD.

Hvis du vil slette et brugerdefineret domænenavn, skal du kontrollere, at ingen ressourcer i mappen, der er afhængige af domænenavnet. Du kan ikke slette et domænenavn fra din adresseliste, hvis:

-   Alle brugere har et brugernavn, mailadresse eller proxyadresse, der indeholder navnet på det domæne.

-   En gruppe har en mailadresse eller proxyadresse, der indeholder navnet på det domæne.

-   Et program i din Azure AD har en app-ID-URI, der indeholder navnet på det domæne.

Du skal ændre eller slette alle disse ressourcer i Azure AD-biblioteket, før du kan slette det brugerdefinerede domænenavn.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Bruge PowerShell eller Graph API til at administrere domænenavne

De fleste administrationsopgaver for domænenavne i Azure Active Directory kan også være fuldført, ved hjælp af Microsoft PowerShell eller automatisk med Azure AD Graph API (i offentlige preview).

-   [Bruge PowerShell til at administrere domænenavne i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Bruge Graph API til at administrere domænenavne i Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Næste trin

-   [Tilføje brugerdefinerede domænenavne](active-directory-domains-add-azure-portal.md)
