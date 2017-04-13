<properties
    pageTitle="Deling af konti med Azure AD |  Microsoft Azure"
    description="I denne artikel beskrives, hvordan Azure Active Directory giver organisationer mulighed for sikker deling konti til lokale apps og forbrugere skytjenester."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Dele konti med Azure AD

## <a name="overview"></a>Oversigt
Nogle gange for organisationer at bruge en enkelt brugernavn og din adgangskode til flere personer. Dette sker typisk i to tilfælde:

- Når du åbner programmer, der kræver et entydigt logon og en adgangskode for hver bruger, om lokale apps eller forbruger cloud services (fx virksomhedens sociale medier konti).
- Når du opretter flere brugere miljøer. Du kan have en enkelt, lokale konto, der med udvidede rettigheder og kan bruges til at core konfiguration, administration og genoprettelse aktiviteter (f.eks. den lokale "global administrator" konto til Office 365) eller den rod i Salesforce.

Traditionelt, vil blive delt disse konti ved at distribuere legitimationsoplysninger (brugernavn og adgangskode) til de rette personer eller gemme dem i en delt placering, hvor flere der er tillid til supportmedarbejdere kan få adgang til dem.

Traditionelle deling modellen har flere ulemper:

- Aktivere adgang til nye programmer, skal du distribuere legitimationsoplysninger til alle, der skal have adgang.
- Hver delte program kan kræve sin egen entydigt sæt delte legitimationsoplysninger, der kræver brugere at huske flere sæt af legitimationsoplysninger. Når brugerne har til at huske mange legitimationsoplysninger, øger risikoen, de vil anvende risikabel fremgangsmåder. (fx skriver adgangskoder ned).
- Du kan ikke se, hvem der har adgang til et program.
- Du kan ikke se, hvem der har *adgang til* et program.
- Når du har brug at fjerne adgangen til et program, skal du opdatere legitimationsoplysningerne og distribuere dem igen til alle, der skal have adgang til dette program.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory konto Deling

Azure AD indeholder en ny metode til at bruge delte konti, der fjerner disse ulemper.

Azure AD-administratoren konfigurerer hvilke programmer, der er en bruger kan få adgang til ved at bruge panelet adgang og vælge typen enkelt sign-on – bedst egnet til det pågældende program. En af typerne, *baseret på adgangskoder enkelt Log på*, kan Azure AD fungere som en slags "broker" under processen enkeltlogon til denne app.

Brugere logger på én gang med deres organisationskonto. Dette er den samme konto, som de jævnligt bruge til at få adgang til deres skrivebord eller mail. De kan opdage og få adgang til kun de programmer, som de er tildelt til. Denne liste over programmer kan omfatte en hvilken som helst antal delte legitimationsoplysninger med delte konti. Slutbrugerens behøver ikke at huske eller Notér de forskellige konti, de kan bruge.

Delte konti ikke kun øge tilsyn med og forbedre brugervenligheden, skal de også forbedre din sikkerhed. Brugere med tilladelse til at bruge legitimationsoplysningerne, der ikke kan se den delte adgangskode, men hellere får tilladelse til at bruge adgangskoden som en del af et orchestrated godkendelse flow. Desuden med nogle adgangskode SSO-programmer, du har mulighed for at have Azure AD med jævne mellemrum overgang (Opdater) adgangskoden med store, komplekse adgangskoder, du vil forbedre sikkerheden for kontoen. Administratoren kan nemt give eller tilbagekalde adgang til et program og også ved, hvem der har adgang til kontoen, og hvem der har åbnet det i fortiden.

Azure AD understøtter konti, der er delt til en hvilken som helst Enterprise mobilitet pakke (EMS), Premium eller grundlæggende brugere med licens, på tværs af alle de typer af adgangskode enkelt logge på programmer. Du kan dele konti på grund af tusindvis af allerede integrerede programmer i galleriet programmet og kan tilføje dine egne godkendelse af adgangskode program med [brugerdefinerede SSO-apps](active-directory-sso-integrate-saas-apps.md).

Azure AD-funktioner, der aktiverer konto deling omfatter:

- [Adgangskode enkeltlogon](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Adgangskode enkelt sign-on – agent
- [Gruppetildeling](active-directory-accessmanagement-self-service-group-management.md)
- Brugerdefinerede adgangskode apps
- [App dashboard/brugsrapporter](active-directory-passwords-get-insights.md)
- Slutbrugerens access portaler
- [App-proxy](active-directory-application-proxy-get-started.md)
- [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Dele en konto
Bruge Azure AD til at dele en konto, skal du:

- Tilføje et program [appgalleri](https://azure.microsoft.com/marketplace/active-directory/) eller [brugerdefineret program](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Konfigurere programmet for adgangskode enkelt enkeltlogon (SSO)
- Bruge [gruppe baseret tildeling](active-directory-accessmanagement-group-saasapps.md) , og vælg mulighed for at åbne en delt legitimationsoplysninger
- Valgfrit: i nogle programmer, som Facebook, Twitter eller LinkedIn, kan du aktivere indstillingen til [Azure AD automatiseret adgangskode filmrulle over](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Du kan også gøre din delte konto mere sikker med Multi-Factor Authentication (MFA) (få mere at vide om [sikring af programmer med Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) og kan du uddelegere mulighed for at styre, hvem der har adgang til programmet ved hjælp af [Azure AD selvbetjening](active-directory-accessmanagement-self-service-group-management.md) Gruppeadministration.

## <a name="related-articles"></a>Relaterede artikler

- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
- [Beskytte apps med betinget adgang](active-directory-conditional-access.md)
- [Selvbetjening gruppe administration/SSAA](active-directory-accessmanagement-self-service-group-management.md)
