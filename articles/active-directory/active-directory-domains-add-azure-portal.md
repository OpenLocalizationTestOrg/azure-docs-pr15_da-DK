<properties
    pageTitle="Føje dit eget domænenavn til Azure Active Directory preview | Microsoft Azure"
    description="Hvordan du føjer dit firmas domænenavne til Azure Active Directory, og hvordan du kan bekræfte domænenavnet."
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
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Føje et brugerdefineret domænenavn til Azure Active Directory preview

> [AZURE.SELECTOR]
- [Azure-portalen](active-directory-domains-add-azure-portal.md)
- [Azure klassisk portal](active-directory-add-domain.md)

Du har en eller flere domænenavne, din organisation bruger til at handle, og dine brugere Log på virksomhedens netværk med virksomhedens domænenavn. Ved hjælp af Azure Active Directory (Azure AD) preview, kan du føje dit virksomhedens domænenavn til Azure AD samt. [Hvad er i preview?](active-directory-preview-explainer.md) Dette kan du tildele brugernavne i kataloget, der er velkendt for dine brugere, som ‘alice@contoso.com.’ det er nemt:

1. Tilføje det brugerdefinerede domænenavn på adresselisten
2. Tilføje en DNS-post til domænenavnet på domæneregistratoren
3. Bekræfte det brugerdefinerede domænenavn i Azure AD

## <a name="how-do-i-add-a-domain-name"></a>Hvordan tilføjer jeg et domænenavn?

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælge **flere tjenester**, Angiv **Azure Active Directory** i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Vælg **Domain names**på bladet ***mappenavn*** .

4. Vælg kommandoen **Tilføj** på bladet ** *mappenavn* - domænenavne** .

  ![Vælge kommandoen Tilføj](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Angiv navnet på dit eget domæne i feltet, som 'contoso.com' på bladet **domænenavn** , og derefter vælge **Tilføj domæne**. Sørg for at medtage .com, .net eller andre på øverste niveau filtypenavn.

6. Få de oplysninger, DNS-posten, Azure AD vil bruge til at bekræfte, at din organisation ejer det brugerdefinerede domænenavn på bladet ***domænenavn*** (det vil sige, bladet, der åbnes, der indeholder din nye domænenavn i titlen).

  ![få oplysninger om DNS-post](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Nu hvor du har føjet domænenavnet, skal Azure AD kontrollere, at din organisation ejer domænenavnet. Før Azure AD kan udføre denne kontrol, skal du tilføje en DNS-post i DNS zone file for domænenavnet. Denne opgave er udført på webstedet for domæneregistrator for domænenavnet.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Tilføj DNS-posten hos domæneregistrator for domænet

Næste trin at bruge dit eget domænenavn med Azure AD er at opdatere DNS zone file for domænet. Dette gør det muligt for Azure AD at bekræfte, at din organisation ejer det brugerdefinerede domænenavn.

1.  Log på domæneregistrator for domænet. Hvis du ikke har adgang til at opdatere DNS-posten, Bed den person eller gruppe, der har denne adgang til at udføre trin 2 og give dig besked, når den er fuldført.

2.  Opdatere DNS zone file for domænet, ved at tilføje DNS-posten fået af Azure AD. DNS-posten aktiverer Azure AD at bekræfte dit ejerskab af domænet. DNS-posten ændre ikke en hvilken som helst funktionsmåder som maildistribution eller webvært.

Hjælp til denne tilføjelse af DNS-post, skal du læse [instruktioner til tilføjelse af en DNS-post på populære DNS-udbyder](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Bekræfte domænenavnet med Azure AD

Når du har tilføjet DNS-post, er du klar til at bekræfte domænenavnet med Azure AD.

Et domænenavn kan bekræftes kun, når DNS-posterne er blevet overført. Denne overførsel ofte tager kun sekunder, men det kan tage nogle gange en time eller mere. Hvis bekræftelsen ikke virker første gang, prøv igen senere.

1.  Log på [Azure-portalen](https://portal.azure.com) med en konto, der er en global administrator for kataloget.

2.  Vælg **Gennemse**, Angiv brugeradministration i tekstfeltet, og tryk derefter på **Enter**.

    ![Åbne brugeradministration](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Vælg det ikke-bekræftede domænenavn, du vil kontrollere på bladet **Brugeradministration - domænenavne** .

4. Vælg **Bekræft** at fuldføre bekræftelsen på bladet ***domænenavn*** (det vil sige, bladet, der åbnes, der indeholder din nye domænenavn i titlen).

Nu kan du [tildele brugernavne, der omfatter dit eget domænenavn](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du ikke kan bekræfte et brugerdefineret domænenavn, kan du prøve følgende. Vi starter med de mest almindelige og arbejde ned til det mindste fælles.

1.  **Vente i timen**. DNS-poster, der har brug at sprede før Azure AD kan bekræfte domænet. Det kan tage en time eller mere.

2.  **Sørg for, at DNS-posten blev angivet, og at den er korrekt**. Udføre dette trin på webstedet for domæneregistrator for domænet. Azure AD kan ikke bekræfte domænenavnet, hvis DNS-post, der ikke findes i DNS zone file, eller hvis den ikke er en nøjagtig match med DNS-post, der Azure AD givet dig. Hvis du ikke har adgang til at opdatere DNS-poster for domænet på domæneregistratoren, dele DNS-posten med den person eller gruppe i din organisation, der har denne adgang, og bed dem om at tilføje DNS-posten.

3.  **Slette domænenavn fra en anden mappe i Azure AD**. Et domænenavn kan bekræftes i en enkelt mappe. Hvis et domænenavn blev tidligere har kontrolleret i en anden mappe, skal den slettes der før den kan bekræftes i den nye mappe. Læs [Administrer brugerdefinerede domænenavne](active-directory-domains-manage-azure-portal.md)for at få mere for at vide om sletning af domænenavne.    

## <a name="add-more-custom-domain-names"></a>Tilføje flere brugerdefinerede domænenavne

Hvis din organisation bruger flere brugerdefinerede domænenavne, som 'contoso.com' og 'contosobank.com', kan du tilføje dem op til maksimalt 900 domænenavne. Følge de samme trin i denne artikel for at tilføje hver af dine domænenavne.

## <a name="next-steps"></a>Næste trin

[Administrere brugerdefinerede domænenavne](active-directory-domains-manage-azure-portal.md)
