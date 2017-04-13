<properties
   pageTitle="Sammenligning af funktioner til administration af eksterne identiteter med Azure Active Directory | Microsoft Azure"
   description="Sammenlignes Azure Active Directory B2B samarbejde, B2C og med flere lejer App for at understøtte godkendelse og autorisation for eksterne identiteter"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Sammenligning af funktioner til administration af eksterne identiteter med Azure Active Directory

Ud over administration af mobile medarbejdere adgang til SaaS apps, kan Azure Active Directory (Azure AD) hjælpe din organisation, som deler ressourcer med forretningsforbindelser og leverer programmer til virksomheder og forbrugere.

## <a name="developing-applications-for-businesses"></a>Udviklingsprogrammer til virksomheder

Leverer du en tjeneste eller et program, som en løn-tjeneste til virksomheder? Azure AD udgør den identity platform, hvor du kan bygge programmer, der problemfrit integreres med millioner af organisationer, som allerede har konfigureret Azure AD som en del af installation af Office 365 eller andre enterprise-tjenester.

**Eksempel:** En farmaceutiske forhandler indeholder læge forsyninger og oplysninger systemer til sundhedssektoren. De skal bruges til feltet et analytics-program til medicinsk praksis og ønskede kunder til at administrere deres egne identiteter. Dette firma valgte Azure AD som identitet platform til deres øvelse administrationsprogram indsende Azure AD-identiteter til kunderne snabel op, når det er nødvendigt. Du kan finde yderligere oplysninger finder [Azure Active Directory udvikler vejledning](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Aktivere business partneradgang til din virksomhedsressourcer

Har du forretningsforbindelser eller andre uden for virksomheden, som skal have adgang til virksomhedsressourcer, som et SharePoint-websted eller din ERP-system? Azure AD gør det muligt for administratorer til at give eksterne brugere (der kan eller ikke kan findes i Azure AD) single sign-on adgang til virksomhedens programmer. Dette forbedrer sikkerhed, som brugerne miste adgang, når de forlader organisationen partner, mens du styrer access politikker i din organisation. Dette forenkler også administration, som du ikke behøver at administrere en ekstern partner mappe eller per partner sammenslutning relationer.

**Eksempel:** En afbildning virksomhed giver forhandlere foto imaging services og fungerer det største detailsalg netværk af udskrivning kiosker. De valgte Azure AD til at aktivere tusindvis af brugere på deres detailsalg forretningsforbindelser bruge deres egen legitimationsoplysninger til at hente de nyeste partner marketingmaterialer og omarrangere foto behandling af forsyninger fra virksomhedens leverandør ekstranet. Du kan finde yderligere oplysninger finder [Azure AD B2B samarbejde](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Udviklingsprogrammer til forbrugere

Har du brug for sikkert og rentabel publicere online programmer, som et detailsalg store forsiden til millioner af forbrugerne? Azure AD giver en platform aktivere sociale samt logon brugernavn og adgangskode, branded selvbetjening Tilmeld dig og nulstilling af adgangskode selvbetjening til forbrugere af dit program. Nemmere øges til dine brugere samtidig reducere belastning på udviklerne.

**Eksempel:** Den \#1, du bruger voksende i verden behov for at deltage direkte med dens 450 million blæsere. For at gøre dette, bygget de en mobilapp med Azure AD til bruger godkendelse og profil lagerplads. Ventilatorer få forenklet registrering og log på ved hjælp af konti til sociale som Facebook, eller de kan bruge traditionelle brugernavn/adgangskoder for at få en problemfri oplevelse på tværs af iOS-, Android- og Windows telefoner. Bygger på den eksisterende Azure AD platform betydeligt nedsat brugerdefineret kode, mens giver voksende tilpasset branding og lette problemstillinger om sikkerhed, brud på data og skalerbarhed. Du kan finde flere oplysninger [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Sammenligning af Azure AD-funktioner

Hver af de scenarier, der allerede er beskrevet i denne artikel er rettet af funktioner i Azure AD. I denne tabel skal være en hjælp til at gøre det klart, hvilke funktioner er mest relevante for dig:

| **Overvej dette produkt...**       | [Azure AD med flere lejer SaaS app](active-directory-developers-guide.md)    | [Azure AD B2B samarbejde](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD-B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Hvis jeg har brug at give...** | en tjeneste til virksomheder | partneradgang til mine apps  | en tjeneste til forbrugere |
| **Og jeg ligner...**  | Pharma forhandler      | Afbildning virksomhed            | Sports voksende       |
| **Implementere en app til...**  | Øvelse administration     | Leverandør ekstranet          | Fodboldfans            |
| **Målretning af...**        | Læge kontorer        | Godkendte forretningsforbindelser | Alle med mail      |
| **Tilgængelige når...**      | Kunde administrator tilladelser | Min administrator invitationer           | Forbrugeren tilmelder sig      |
