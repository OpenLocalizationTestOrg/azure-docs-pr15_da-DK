<properties
    pageTitle="Udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage | Microsoft Azure"
    description="Indeholder en detaljeret oversigt over, hvordan Windows 10-enheder kan udnytte Azure AD deltage i at få registreret på Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage

## <a name="what-is-azure-active-directory-join"></a>Hvad er Azure Active Directory joinforbindelse?
Azure Active Directory deltage i (Azure AD deltage) er en funktion, der registrerer en firmaet ejet enhed i Azure Active Directory til at aktivere centralt styring af enhed. Det gør det muligt for brugere, som medarbejdere og studerende til at oprette forbindelse til enterprise skyen via Azure Active Directory. Dette gør det muligt for forenklet Windows-installationer og adgang til organisatoriske apps og ressourcer fra en hvilken som helst Windows-enhed, både virksomhedens ejes og personligt-ejes (BYOD).

Azure AD-joinforbindelse er beregnet til virksomheder, der er skyen første/cloud-kun – typisk små og mellemstore virksomheder, der ikke har en lokal Windows Server Active Directory-infrastruktur. Nævnte, Azure AD-joinforbindelse kan og vil også skal bruges i store organisationer på enheder, der er i stand til at gøre en traditionel domæne joinforbindelse (mobile enheder, for eksempel), eller for brugere, der primært skal have adgang til Office 365 eller andre Azure AD SaaS apps.

Selvom den traditionelle domæne joinforbindelse tilbyder stadig de bedste lokalt støder på enheder, der er i stand til for at deltage i domæne skal er Azure AD joinforbindelsen egnet til enheder, der kan ikke forbindelse til et domæne. Azure AD-joinforbindelse egner sig også for at administrere brugere i skyen. Sker det ved hjælp af funktioner til administration af mobile enheder i stedet for med værktøjer til administration af traditionelle domæne som Gruppepolitik og System Center SCCM (Configuration Manager).

![Oversigt over virksomhedens enheder og personlige enheder med lokale Active Directory og Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Hvorfor bør virksomheder med indføre Azure AD deltage?

* **Virksomheder, der er stort set i skyen**: Hvis du har flyttet eller flytter til en model, hvori du reducerer din lokale miljøet og vil arbejde mere i skyen, Azure AD deltage kan du drage fordel. Du har måske oprettet Azure AD-konti, manuelt eller via synkronisering af dine lokale Active Directory. Uanset hvilken mulighed, du har en konto i Azure AD, og du kan bruge den til at logge på Windows 10. Dine brugere kan deltage i deres computere til Azure AD via enten den ud af box experience (OOBE) eller via menuen Indstillinger. Når du deltager i, og får brugerne enkelt enkeltlogon (SSO) adgang til skyen ressourcer som Office 365, i deres browser eller i Office-programmer.

* **Uddannelsesinstitutioner**: en af de scenarier, vi høre om ofte er, uddannelsesinstitutioner har to brugertyper: fakultetsmedarbejdere og studerende. Fakultetsmedarbejdere medlemmer betragtes langsigtede medlemmer af organisationen, så oprette lokale konti for at de er hensigtsmæssigt. Men studerende er shorter-term medlemmer af organisationen og dermed kan administreres i Azure AD. Det betyder, at directory skala kan installeres i skyen i stedet for gemt lokalt. Det betyder også, at studerende kan logge på Windows med deres Azure AD-konti og få adgang til Office 365 ressourcer i deres browser eller i Office-programmer.

* **Detailsalg virksomheder**: et andet scenarie vi har hørt om fra kunder er de ønsker at administrere sæsonbestemte medarbejdere nemmere.  Konti til medarbejdere langsigtede, konstant oprettes igen, som regel, som lokale konti på computere, der er medlem af et domæne. Men sæsonbestemte medarbejdere er shorter-term medlemmer af organisationsdiagram, så det er god ide at administrere dem hvor brugerlicenser kan nemmere flyttes rundt. Oprette disse brugerkonti i skyen med Office 365-licenser gør det muligt for brugerne at udnytte fordelene ved at logge på Windows og Office-programmer med en Azure AD-konto. Du kan bevare større fleksibilitet med deres licenser, når de forlader.
* **Andre virksomheder**: selvom du bevarer brugere i din lokale Active Directory, du kan stadig få glæde af har brugerne til at være Azure AD-medlem af. Det skyldes, at Azure AD tilbyder en forenklet tilslutter oplevelse, effektiv enhedsstyring, automatisk mobilenhed management tilmelding og enkelt sign-on – egenskab for Azure AD og lokale ressourcer.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Hvilke funktioner findes Azure AD deltage?
Med Azure AD deltager i får du følgende:

* **Selv klargøring af virksomhedens ejes enheder**: med Windows 10, brugerne kan konfigurere en helt ny, shrink-wrapped enhed out of box erfaring, uden at involvere IT.


* **Understøttelse af moderne formular faktorer**: Azure AD joinforbindelse fungerer på enheder, der ikke har det traditionelle domæne deltage i funktioner.  


* **Understøttelse af eksisterende organisationskonti**: brugere ikke længere skal bruge til at oprette og vedligeholde en en personlig Microsoft-konto for at få den bedste oplevelse på enheder, der er udstedt af virksomhed, som de gjorde med Windows 8. De kan bruge deres eksisterende arbejde konti i Azure AD i stedet. For mange organisationer betyder det grundlæggende, at brugerne kan konfigurere og logge på Windows med de samme legitimationsoplysninger, som de kan bruge til at få adgang til Office 365.


* **Automatisk mobilenhed management tilmelding**: enheder kan automatisk registreret i administration af mobilenheder, når du har forbindelse til Azure AD. Denne proces fungerer med Microsoft Intune og partner mobilenhed management-løsninger. Når enhedsstyring er færdig med Intune, kan IT-administratorer overvåge/administrere Azure AD-joinforbundne enheder sammen med medlem af et domæne enheder i SCCM management console.


* **Single sign-on til ressourcer i virksomheden**: brugere få glæde af single sign-on fra skrivebordet i Windows til apps og ressourcer i skyen, som Office 365 og tusindvis af business-programmer, der er baseret på Azure AD for godkendelse gennem [Azure AD-forbindelse](active-directory-azureadjoin-deployment-aadjoindirect.md). Firmaer ejes enheder, der er tilsluttet Azure AD også glæde SSO til lokale ressourcer, når enheden er på virksomhedens netværk, og hvor som helst, når disse ressourcer vises via [Azure AD-proxyen](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **OS tilstand Roaming**: indstillinger for hjælp til handicappede, websteder, Wi-Fi adgangskoder og andre indstillinger, der synkroniseres på tværs af virksomhedens ejes enheder uden at kræve en personlig Microsoft-konto.


* **Enterprise klar til Windows Store**: The Windows Store understøtter app acquisition og licenser med Azure AD-konti. Organisationer kan volumenlicens apps og gøre dem tilgængelige for brugerne i organisationen.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Hvordan arbejder forskellige enheder med Azure AD deltage?

| Virksomhedens enhed (tilsluttet lokale domæne)                                                                                                                                                                                         | Virksomhedens enhed (sammenføjet til skyen)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Personlig enhed                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Brugerne kan logge på Windows med arbejde legitimationsoplysninger (som de gør i dag).                                                                                                                                                                        | Brugerne kan logge på Windows med arbejde legitimationsoplysninger, der administreres i Azure AD. Det er relevant for virksomhedens enheder i tre tilfælde: 1) organisationen har ikke Active Directory lokalt (for eksempel en mindre virksomhed). 2) organisationen ikke oprette alle brugerkonti i Active Directory (for eksempel konti til studerende, konsulenter eller sæsonbestemte medarbejdere er ikke oprettet i Active Directory). 3) organisationen har virksomhedens enheder, der ikke kan føjes til et domæne, der (lokalt), som telefoner eller tablets, der kører en Mobile SKU (for eksempel en sekundær enhed ført til et factory/detailsalg gulv). Azure AD-joinforbindelse understøtter sammenføjning af virksomhedens enheder til både administrerede og samlet organisationer. | Brugere Log på Windows med deres personlige Microsoft-kontolegitimationsoplysninger (ikke ændret).                                                |
| Brugere har adgang til globale indstillinger og enterprise Windows Store. Disse tjenester sammen med arbejde konti og kræver ikke en personlig Microsoft-konto. Dette kræver organisationer tilsluttes deres lokale Active Directory til Azure AD.                                        | Brugere kan gøre selvbetjening konfiguration. De kan gå igennem førstegangsoplevelsen (FRX) via deres arbejdskonto som et alternativ til at have IT klargøring enhederne, selvom begge metoder understøttes.                                                                                                                                                                                                                                                                                                                                                                             | Brugerne kan nemt tilføje en arbejdskonto, der administreres i Active Directory eller Azure AD.                                                      |
| Brugere har mulighed for SSO fra skrivebordet til at arbejde apps, websteder og ressourcer – herunder både lokale ressourcer og skyen apps, der bruger Azure AD til godkendelse.                                                                                                            | Enheder er registreret automatisk i mappen enterprise (Azure AD) og automatisk registreret i administration af mobilenheder. (Azure AD-Premium funktion).                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Brugerne har mulighed for SSO på tværs af apps og til websteder/ressourcer med denne arbejdskonto.                                              |
| Brugere kan tilføje deres personlige Microsoft-konto for at få adgang til deres personlige billeder og filer uden at påvirke virksomhedsdata. (Globale indstillinger fortsætte med at arbejde med deres arbejde konti.) Microsoft-kontoen gør det muligt for SSO og ikke længere drev globale indstillinger.  | Brugere kan gøre en selvbetjening nulstilling af adgangskode (SSPR) på winlogon, hvilket betyder, at de kan nulstille en glemt adgangskode. (Azure AD-Premium funktion).                                                                                                                                                                                                                                                                                                                                                                                                                                   | Brugere har adgang til enterprise Windows Store, så de kan få fat på og bruge line of business apps på deres personlige enheder. |                                                               |


## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Godkendelse af identiteter uden adgangskoder via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
