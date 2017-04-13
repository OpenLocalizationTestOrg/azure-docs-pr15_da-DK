<properties
    pageTitle="Azure betinget adgang til SaaS Apps | Microsoft Azure"
    description="Betinget adgang i Azure AD kan du konfigurere per programmet Multi-Factor authentication adgangsregler og muligheden for at blokere adgangen for brugere ikke på et netværk, der er tillid til. "
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Introduktion til Azure Active Directory betinget Access

Azure Active Directory betinget adgang til [SaaS](https://azure.microsoft.com/overview/what-is-saas/) apps og Azure AD forbindelse apps kan du konfigurere betinget adgang, der er baseret på gruppen, placering og programmet følsomhed. 

Du kan angive Multi-Factor authentication (MFA) adgangsregler per program med betinget access baseret på programmet følsomhed. MFA per programmet giver mulighed for at blokere adgangen for brugere, der ikke er på et netværk, der er tillid til. Du kan anvende MFA regler for alle brugere, der er tildelt til programmet eller kun til brugere inden for angivne sikkerhedsgrupper.  Brugere kan udelukkes fra MFA kravet, hvis de har adgang til programmet fra en IP-adresse, der er i organisationens netværk.

Disse funktioner vil være tilgængelige for kunder, der har købt en Azure Active Directory Premium-licens.

## <a name="scenario-prerequisites"></a>Scenarie forudsætninger
* Licens til Azure Active Directory Premium

* Medlem af organisationsnetværk eller administreret Azure Active Directory-lejer

* Samlet lejere kræver, at Multi-Factor godkendelse aktiveres.

## <a name="configure-per-application-access-rules"></a>Konfigurere regler for access-program

I dette afsnit beskrives, hvordan du konfigurerer adgangsregler-program.

1. Log på ved hjælp af en konto, der er en global administrator for Azure AD Azure klassisk portalen.
2. Vælg **Active Directory**i den venstre rude.
3. Vælg mappen under fanen mappe.
4. Vælg fanen **programmer** .
5. Vælg det program, som reglen oprettes for.
6. Vælg fanen **Konfigurer** .
7. Rul ned til sektionen adgang til regler. Vælg den ønskede access-regel.
8. Angive, hvilke brugere, som reglen skal gælde for.
9. Aktivere politikken ved at vælge **aktiveret skal på**.

##<a name="understanding-access-rules"></a>Forstå adgangsregler

Dette afsnit indeholder en detaljeret beskrivelse af de adgangsregler, der understøttes i Azure betinget programmet Access.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Brugerne, der angiver gælder adgangsregler for

Som standard gælder politikken for alle brugere, der har adgang til programmet. Du kan også begrænse politikken, til brugere, der er medlem af de angivne sikkerhedsgrupper. Knappen **Tilføj gruppe** bruges til at vælge en eller flere grupper gruppen markering i dialogboksen access-regel skal gælde for. Denne dialogboks kan også bruges til at fjerne markerede grupper. Når reglerne, der er valgt til at anvende til grupper, gennemtvinges på adgangsregler kun for brugere, der tilhører en af de angivne sikkerhedsgrupper.

Sikkerhedsgrupper kan også eksplicit udelukkes fra politikken ved at vælge indstillingen **undtagen** og angive én eller flere grupper. Brugere, der er medlem af en gruppe på listen **undtagen** vil ikke være underlagt kravet om godkendelse i flere niveauer, selvom de er medlem af en gruppe, som access reglen gælder for.
Den access-regel, der er vist nedenfor kræver, at alle brugere i gruppen Administratorer for at kunne bruge Multi-Factor authentication, når du åbner programmet.

![Angive betinget adgangsregler med MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Betinget adgangsregler med MFA
Hvis en bruger, der er konfigureret ved hjælp af funktionen hver bruger Multi-Factor authentication, vil denne indstilling på brugeren kombinere med Multi-Factor authentication regler for appen. Det betyder, at en bruger, der er konfigureret til hver bruger Multi-Factor authentication bliver bedt om at udføre Multi-Factor authentication, selvom de er blevet fritaget programmet Multi-Factor authentication regler. Lær mere om Multi-Factor godkendelse og per bruger indstillinger.

### <a name="access-rule-options"></a>Adgangsindstillinger for regel
Følgende indstillinger understøttes:

* **Kræve godkendelse i flere niveauer**: de brugere, som access regler i kraft, vil blive bedt om at fuldført Multi-Factor authentication før at få adgang til det program, som politikken anvendes på.

* **Kræver Multi-Factor authentication når de ikke er på arbejde**: en bruger, der kommer fra en pålidelig IP-adresse ikke forpligtet til at udføre Multi-Factor authentication. Der er tillid til IP-adresseområder kan konfigureres på siden Multi-Factor authentication indstillinger.

* **Bloker adgang, når de ikke er på arbejde**: en bruger, der ikke kommer fra en IP-adresse, der er tillid til, blokeres. Der er tillid til IP-adresseområder kan konfigureres på siden Multi-Factor authentication indstillinger.

### <a name="setting-rule-status"></a>Angive status for regel
Access regel status kan slå reglerne til eller fra. Når adgangsregler er slået fra, håndhæves ikke kravet om godkendelse i flere niveauer.

### <a name="access-rule-evaluation"></a>Access evalueringen af en regel

Adgangsregler evalueres, når en bruger har adgang til en samlet program, der bruger OAuth 2.0, OpenID Opret forbindelse, SAML eller WS-sammenslutning. Desuden evalueres adgangsregler, når OAuth 2.0 og OpenID forbinde bruger et Opdater token til at få fat på et adgangstoken. Hvis evaluering af politikker mislykkes, når der bruges et Opdater token, returneres fejlen **invalid_grant** , angiver brugeren skal godkendes igen til klienten.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Konfigurere federation services for at give Multi-Factor authentication

MFA til medlem af organisationsnetværket lejere kan udføres af Azure Active Directory eller af lokale AD FS server.

Som standard opstår MFA på en side, der er hostet af Azure Active Directory. For at konfigurere MFA lokalt, skal egenskaben **– SupportsMFA** angives til **Sand** i Azure Active Directory, ved hjælp af Azure AD-modulet til Windows PowerShell.

I følgende eksempel viser, hvordan du aktiverer lokale MFA ved hjælp af [sæt MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) på contoso.com-lejeren:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Ud over at angive dette flag, skal være konfigureret organisationsnetværket lejer AD FS forekomst for at udføre Multi-Factor authentication. Følg instruktionerne til [installation af Azure Multi-Factor Authentication lokalt](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Relaterede artikler

- [Sikring af adgang til Office 365 og andre apps forbindelse til Azure Active Directory](active-directory-conditional-access.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
