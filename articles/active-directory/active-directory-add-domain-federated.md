<properties
    pageTitle="Tilføje dit eget domænenavn og konfigurere organisationsnetværket sign-on til Azure Active Directory | Microsoft Azure"
    description="Sådan tilføjes dit firmas domænenavne til Azure Active Directory, og hvordan konfigurere samlet enkeltlogon mellem Azure Active Directory og din lokale sammenslutning løsning."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Føje dit eget domænenavn til Azure Active Directory

Du kan konfigurere et brugerdefineret domænenavn, som 'contoso.com,', så brugerne i contoso.com kan have en samlet enkelt sign-on – oplevelse fra virksomhedens netværk. Hvis du allerede har Active Directory Federation Services (AD FS) eller en anden sammenslutningsserver, der kører på virksomhedens netværk, kan du konfigurere Azure AD for at bruge dit eget domænenavn ved hjælp af værktøjet Azure AD-forbindelse. Du kan også bruge Azure AD-forbindelse til at installere en ny AD FS-miljø, og konfigurere, til medlem af organisationsnetværket single sign-on til Azure AD.

Hvis du ikke har og har ikke planer om at installere AD FS eller en anden sammenslutningsserver, skal du følge disse instruktioner: [Tilføj et brugerdefineret domænenavn til Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Tilføje et brugerdefineret domænenavn til dit bibliotek

1. Log på [Azure klassisk portal](https://manage.windowsazure.com/) til en brugerkonto, der er en global administrator for din Azure AD-mappe.

2. Åbn mappen i **Active Directory**, og vælg fanen **Domains** .

3. På kommandolinjen skal du vælge **Føj**og derefter angive navnet på dit eget domæne, som 'contoso.com'. Sørg for at medtage .com, .net eller andre på øverste niveau filtypenavn.

4. Markér afkrydsningsfeltet **jeg har planer om at konfigurere dette domæne til single sign-on med min lokale Active Directory** .

5. Vælg **Tilføj**.

Køre værktøjet Azure AD-forbindelse for at få den DNS-post, der Azure AD vil bruge til at bekræfte domænet. Du vil se DNS-poster i **Azure AD-domæne** trin i guiden. Du kan se, hvad trin i guiden ser ud [i disse instruktioner](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Hvis du ikke har værktøjet Azure AD-forbindelse, kan du [hente det her](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Tilføj DNS-posten hos domæneregistrator for domænet

Næste trin at bruge dit eget domænenavn med Azure AD er at opdatere DNS zone file for domænet. Dette gør det muligt for Azure AD at bekræfte, at din organisation ejer det brugerdefinerede domænenavn.

1. Log på webstedet for domæneregistrator for dit domænenavn. Hvis du ikke har adgang til at gøre dette, Bed den person eller gruppe i din organisation, som har denne adgang til at udføre trin 2 og give dig besked, når den er fuldført.

2. Opdatere DNS zone file for domænet, ved at tilføje DNS-posten fået af Azure AD. DNS-posten aktiverer Azure AD at bekræfte dit ejerskab af domænet. DNS-posten ændre ikke en hvilken som helst funktionsmåder som maildistribution eller webvært.

Hjælp til dette trin, skal du læse [instruktioner til tilføjelse af en DNS-post på populære DNS-udbyder](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Bekræfte domænenavnet med Azure AD

Når du har tilføjet DNS-post, er du klar til at bekræfte domænenavnet med Azure AD.

For at bekræfte domænet, klik på **Næste** på trinnet **Azure AD-domæne** i guiden Azure AD-forbindelse. Azure AD søger efter DNS-post på DNS zone file for domænet. Azure AD bekræfte kun navnet på det domæne, når DNS-posterne er blevet overført. Overførsel af ofte tager kun sekunder, men det kan tage nogle gange en time eller mere. Hvis bekræftelsen ikke virker første gang, prøv igen senere.

Gå derefter til de resterende trin i guiden Azure AD-forbindelse. Dette synkroniserer brugere fra din Windows Server AD til Azure AD. Synkroniserede brugere i det domæne, du har konfigureret til sammenslutning vil kunne få en samlet enkelt sign-on – oplevelse fra virksomhedens netværk til Azure AD.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du ikke kan bekræfte et brugerdefineret domænenavn, kan du prøve følgende. Vi starter med de mest almindelige og arbejde ned til det mindste fælles.

1.  **Vente i timen**. DNS-poster, der har brug at sprede før Azure AD kan bekræfte domænet. Det kan tage en time eller mere.

2.  **Sørg for, at DNS-posten blev angivet, og at den er korrekt**. Udføre dette trin på webstedet for domæneregistrator for domænet. Azure AD kan ikke bekræfte domænenavnet, hvis DNS-post, der ikke findes i DNS zone file, eller hvis den ikke er en nøjagtig match med DNS-post, der Azure AD givet dig. Hvis du ikke har adgang til at opdatere DNS-poster for domænet på domæneregistratoren, dele DNS-posten med den person eller gruppe i din organisation, der har denne adgang, og bed dem om at tilføje DNS-posten.

3.  **Slette domænenavn fra en anden mappe i Azure AD**. Et domænenavn kan bekræftes i en enkelt mappe. Hvis et domænenavn blev tidligere har kontrolleret i en anden mappe, skal den slettes der før den kan bekræftes i den nye mappe. Læs [Administrer brugerdefinerede domænenavne](active-directory-add-manage-domain-names.md)for at få mere for at vide om sletning af domænenavne.

## <a name="add-more-custom-domain-names"></a>Tilføje flere brugerdefinerede domænenavne

Hvis din organisation bruger flere brugerdefinerede domænenavne, som 'contoso.com' og 'contosobank.com', kan du tilføje dem op til maksimalt 900 domænenavne. Følge de samme trin i denne artikel for at tilføje hver af dine domænenavne.

## <a name="next-steps"></a>Næste trin

-   [Administrere brugerdefinerede domænenavne](active-directory-add-manage-domain-names.md)
-   [Få mere at vide om domænet management begreber i Azure AD](active-directory-add-domain-concepts.md)
-   [Vise dit firmalogo når dine brugere logger på](active-directory-add-company-branding.md)
-   [Bruge PowerShell til at administrere domænenavne i Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
