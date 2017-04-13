<properties
    pageTitle="Brugsscenarier og overvejelser om installation for Azure AD deltage | Microsoft Azure"
    description="Forklarer, hvordan administratorer kan konfigurere Azure AD deltage for deres slutbrugere (medarbejdere, studerende, andre brugere). Det også beskrives forskellige reale scenarier for brug af Azure AD deltage i."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< koder ms.service= "active directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/27/2016"

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Brugsscenarier og overvejelser om installation for Azure AD deltage

## <a name="usage-scenarios-for-azure-ad-join"></a>Brugsscenarier til Azure AD deltage
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scenarie 1: Virksomheder stort set i skyen

Azure Active Directory deltage i (Azure AD deltage) kan du drage fordel Hvis du aktuelt betjene og administrerer identiteter til din virksomhed i skyen eller flytter til skyen snart. Du kan bruge en konto, du har oprettet i Azure AD til at logge på Windows 10. Gennem [processen første køre oplevelse (FRX)](active-directory-azureadjoin-user-frx.md), eller ved at sammenkæde Azure AD fra [menuen Indstillinger](active-directory-azureadjoin-user-upgrade.md), kan dine brugere kan deltage i deres computere til Azure AD.  Brugerne kan også få glæde enkelt enkeltlogon (SSO) adgang til skyen ressourcer som Office 365, i deres browser eller i Office-programmer.

### <a name="scenario-2-educational-institutions"></a>Scenarie 2: Uddannelsesinstitutioner

Uddannelsesinstitutioner har normalt to brugertyper: fakultetsmedarbejdere og studerende. Fakultetsmedarbejdere medlemmer betragtes som langsigtede medlemmer af organisationen. Oprette lokale konti for at de er hensigtsmæssigt. Men studerende er shorter-term medlemmer af organisationen, og deres konti kan administreres i Azure AD. Det betyder, at directory skala kan installeres i skyen i stedet for at blive gemt lokalt. Det betyder også, at studerende skal kunne logge på Windows med deres Azure AD-konti og få adgang til Office 365 ressourcer i Office-programmer.

### <a name="scenario-3-retail-businesses"></a>Scenarie 3: Detailsalg virksomheder

Detailsalg virksomheder har sæsonbestemte medarbejdere og langsigtede medarbejdere. Du typisk oprette lokale konti og bruge medlem af et domæne maskiner for langsigtede fuld tid medarbejdere. Men sæsonbestemte medarbejdere er shorter-term medlemmer af organisationen, og det er god ide at administrere deres konti, hvor brugerlicenser kan nemmere flyttes rundt. Når du opretter deres brugerkonti i skyen med Office 365-licenser, får disse brugere fordelene ved at logge på Windows og Office-programmer med en Azure AD-konto, mens du stadig større fleksibilitet med deres licenser, når de forlader.

### <a name="scenario-4-additional-scenarios"></a>Scenario 4: Ekstra scenarier

Sammen med de fordele, som gennemgået tidligere, du drage fordel af har brugerne deltage i deres enheder til Azure AD på grund af en forenklet tilslutter oplevelse, effektiv enhedsstyring, automatisk mobilenhed management tilmelding og single sign-on til Azure AD og lokale ressourcer.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Overvejelser om installation for Azure AD deltage

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Aktivere dine brugere til at deltage i en firmaet ejet enhed direkte til Azure AD


Virksomheder kan give kun skyen konti partnere og organisationer. Disse partnere kan derefter nemt få adgang til virksomhedens apps og ressourcer med enkeltlogon. Dette scenario er gældende for brugere, få adgang til ressourcer primært i skyen, som Office 365- eller SaaS apps, der er afhænger af Azure AD til godkendelse.

### <a name="prerequisites"></a>Forudsætninger
**På niveauet for enterprise (administrator)**

*   Azure-abonnement med Azure Active Directory  

**På brugerniveau**

*   Windows 10 (Professional og Enterprise-versioner)

### <a name="administrator-tasks"></a>Administratoropgaver
* [Konfigurere enheden registrering](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Brugeropgaver
* [Konfigurere en ny Windows 10-enhed med Azure AD under installationen](active-directory-azureadjoin-user-frx.md)
* [Konfigurere en Windows 10-enhed med Azure AD fra menuen Indstillinger](active-directory-azureadjoin-user-upgrade.md)
* [Deltage i en personlig Windows 10-enhed til din organisation](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Aktivere BYOD i din organisation til Windows 10
Du kan konfigurere dine brugere og medarbejdere til at bruge deres personlige Windows enheder (BYOD) for at få adgang til virksomhedens apps og ressourcer. Brugerne kan tilføje deres Azure AD-konti (arbejds- eller skolekonto-konti) til en personlige Windows-enhed for at få adgang til ressourcer i en sikker og kompatibel måde.

### <a name="prerequisites"></a>Forudsætninger
**På niveauet for enterprise (administrator)**

*   Azure AD-abonnement

**På brugerniveau**

*   Windows 10 (Professional og Enterprise-versioner)


### <a name="administrator-tasks"></a>Administratoropgaver

* [Konfigurere enheden registrering](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Brugeropgaver
* [Deltage i en personlig Windows 10-enhed til din organisation](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
