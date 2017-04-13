
<properties
    pageTitle="Azure Active Directory betinget teknisk oversigt over Access | Microsoft Azure"
    description="Med betinget adgangskontrol kontrollerer Azure Active Directory de bestemte betingelser, du vælger når godkender brugeren og før du tillader adgang til programmet. Når disse betingelser er opfyldt, kan brugeren er godkendt og har adgang til programmet."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory betinget teknisk oversigt over i Access

## <a name="services-enabled-with-conditional-access"></a>Aktiveret med betinget access Services
Betinget adgangsregler understøttes på tværs af forskellige Azure AD-program. Listen indeholder:

- Samlet programmer fra galleriet Azure AD-program
- Adgangskode SSO-programmer fra galleriet Azure AD-program
- Programmer, der er registreret med proxyen Azure
- Udviklet med linje med business og med flere lejer programmer, der er registreret med Azure AD
- Visual Studio Online
- Azure Remote-App
-   Dynamics CRM
- Microsoft Office 365 Yammer
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (inkluderer OneDrive for Business)


## <a name="enable-access-rules"></a>Aktivere adgangsregler

Hver regel kan aktiveres eller deaktiveres på en per programmet grundtal. Når regler er **på** er de aktiveret og gennemtvinges i forbindelse med brugere, der bruger programmet. Når de er **fra** , bruges ikke og påvirker ikke brugere logger oplevelse.

## <a name="applying-rules-to-specific-users"></a>Anvende regler på bestemte brugere
Regler kan anvendes på bestemte grupper af brugere, der er baseret på sikkerhedsgruppe ved at angive **Anvend på**. **Anvend på** kan angives til **Alle brugere** eller **grupper**. Når den er indstillet for **Alle brugere** på regler gælder for alle brugere med adgang til programmet. Indstillingen **grupper** giver mulighed for bestemte sikkerhed og distributionsgrupper skal være markeret, regler gennemtvinges kun til disse grupper.

Når du installerer en regel, er det almindelige først anvende det et begrænset sæt af brugere, der er medlemmer af en pilotprojekter grupper. Når fuldført kan reglen anvendes på **Alle brugere**. Dette medfører, at reglen er tvungne for alle brugere i organisationen.

Vælg grupper kan også undtaget fra politik ved hjælp af indstillingen **undtagen** . Alle medlemmer af grupperne undtaget, selvom de vises i en medtaget gruppe.

## <a name="at-work-networks"></a>"På arbejdspladsen" netværk


Betinget adgangsregler, der bruger en "på arbejdspladsen" netværk, der er afhængige af der er tillid til IP-adresseområder, der er konfigureret i Azure AD, eller brug af "i corpnet" kravet fra AD FS. Disse regler omfatter:

- Kræv Multi-Factor authentication når de ikke er på arbejde
- Bloker adgang, når de ikke er på arbejde

Indstillinger for hvori "på arbejdspladsen" netværk

1. Konfigurere der er tillid til IP-adresseområder [multi-Factor authentication konfigurationssiden](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Betinget Access-politik bruge feltet områder, der er konfigureret på de enkelte anmodningen om godkendelse og token udstedelse til at evaluere regler. 
2. Konfigurer brugen af indvendige corpnet gøre krav, denne indstilling kan bruges med organisationsnetværket kataloger, ved hjælp af AD FS. [Yderligere oplysninger om indvendige coronet krav](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Konfigurere offentlige IP-adresseområder. Konfigurer under fanen kan til mappen, du angive offentlige IP-adresser. Betinget Access skal bruge disse, når 'på arbejdet' IP-adresser, dette giver mulighed for flere områder til at konfigurere over 50 IP-adresse grænsen, håndhæves af siden MFA indstilling.



## <a name="rules-based-on-application-sensitivity"></a>Regler, der er baseret på programmet følsomhed

Regler er konfigureret i programmet tillade tjenesterne høj værdi skal være sikker uden at påvirke adgang til andre tjenester. Betinget adgangsregler kan konfigureres under fanen **Konfigurer** af programmet. 

Regler, der tilbydes aktuelt:

- **Kræve godkendelse i flere niveauer**
 - Alle brugere, der anvendes denne politik skal godkendes via Multi-Factor authentication mindst én gang.
 
- **Kræv Multi-Factor authentication når de ikke er på arbejde**
 - Hvis denne politik er anvendt, bliver alle brugere skal have udført Multi-Factor authentication mindst én gang, hvis de har adgang til tjenesten fra en fjernplacering ikke arbejde. Hvis der flyttes fra en fjernplacering, skal de udføre multifaktor-godkendelse, når du få adgang til tjenesten.
 
- **Bloker adgang, når de ikke er på arbejde** 
 - Når brugerne flytter fra arbejde på en fjernplacering, blokeres de, hvis der anvendes politikken "Bloker adgang, når de ikke er på arbejde".  De kan igen access når på en arbejde placering.


## <a name="related-topics"></a>Relaterede emner

- [Sikring af adgang til Office 365 og andre apps forbindelse til Azure Active Directory](active-directory-conditional-access.md)
- [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)
