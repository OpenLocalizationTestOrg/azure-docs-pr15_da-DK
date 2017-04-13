<properties
    pageTitle="Brug af Windows 10-enheder på din arbejdsplads | Microsoft Azure"
    description="Indeholder et øjebliksbillede af funktioner for brugere og IT, forskellige de forskellige måder, en enhed kan klargjort og bruges i en virksomhed med Windows 10."
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
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>Brug af Windows 10-enheder på din arbejdsplads

Gælder for: Windows 10 pc'er

Windows 10 indeholder tre modeller for organisationer, der giver brugere adgang til arbejdsressourcer på en sikker og nem måde.

- **Deltage i Azure Active Directory** (Azure AD joinforbindelse) for medarbejdere, der få adgang til ressourcer som Office 365 primært i skyen. Azure AD joinforbindelsen er selvbetjening arbejde klargøring oplevelse, der er nyt i Windows 10.
- **Forbindelse til et domæne**, i de organisationer, der har investeringer i lokalt apps og ressourcer. Opret forbindelse til domænet giver en bedre oplevelse i Windows 10, når du har forbindelse til Azure AD.
- **En ny forenklet BYOD oplevelse**for brugere, der vil føje en arbejdskonto eller skolen til Windows og nemt få adgang til ressourcer på personlige enheder.

Følgende tabel viser et øjebliksbillede af funktioner for brugere og IT-administratorer, forskellige de forskellige måder, en enhed kan klargjort og bruges i en virksomhed med Windows 10:

|                                                                                                                                                                 | Forbindelse til et domæne     | Azure AD-joinforbindelse | Personlig enhed |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows-enhed-logon til arbejds- eller skolekonto konti.                                                                                                                      | Ja             | Ja           | Nej              |
| Bruger enkeltlogon (SSO) til Office 365 og Azure AD-apps. SSO er muligheden for at logge på én gang til at få adgang til ressourcer i organisationen. | Ja             | Ja           | Ja             |
| Bruger SSO til Kerberos/NTLM apps.                                                                                                                                  | Ja             | Begrænset       | Ja, via VPN         |
| Stærke godkendelse og praktisk-logon til arbejds- eller skolekonto konti med Microsoft Passport og Windows Hej.                                                                   | Ja             | Ja           | Ja             |
| Adgang til enterprise Windows Store med en arbejds- eller skolekonto (ikke en Microsoft-konto).                                                                                    | Ja             | Ja           | Ja             |
| Virksomhed-kompatibel roaming af brugerindstillinger på tværs af enheder med arbejds- eller skolekonto konti.                                                                                 | Ja             | Ja           | Ja             |
| Muligheden for at begrænse adgangen til organisatoriske apps til enheder, der er kompatible med politikker for organisatoriske enheder.                                                      | Ja             | Ja           | Ja             |
| Bruger selvbetjening klargøring af enheder til "arbejde hvor som helst".                                                                                                | Nej              | Ja           | Ja             |
| Mulighed for at administrere enheder.                                                                                                                                       | Ja, via generel/SCCM | Ja           | Ja             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Brug arbejde ejes enheder med Azure AD deltage i og domæne deltage i Windows 10

Windows 10 indeholder til arbejde ejes enheder at få adgang til arbejdsressourcer på to måder:

- Azure AD-joinforbindelse
- Forbindelse til et domæne

 Begge kan være gyldige indstillinger afhængigt af en organisations behov og krav. I nogle tilfælde kan organisationer drage fordel af aktivering af begge metoder til installation.

## <a name="when-to-use-azure-active-directory-join"></a>Hvornår skal jeg bruge Azure Active Directory Join

Azure AD joinforbindelsen er en ny selvbetjening arbejds klargøring oplevelse i Windows 10.  Det er rettet mod medarbejdere, der har adgang til arbejdsressourcer som Office 365 primært i skyen. Det er en lette metode til at konfigurere computere, tabletter og telefoner for virksomheden. Enheder administreres via administration af mobilenheder, ved hjælp af ensartet kontrolelementer på tværs af Windows-platforme.

**Brug Azure AD deltage på grund af følgende årsager**:

- Du vil aktivere den selvbetjening klargøring af enheder til medarbejdere på farten.
- Du giver brugere arbejde ejes mobilenheder som tabletter og telefoner.
- Du vil administrere en gruppe brugere i Azure AD i stedet for i Active Directory, som sæsonbestemte medarbejdere, entreprenører eller studerende.
- Du vil give tilslutter funktioner til medarbejdere i remote gren kontorer med begrænset lokalt infrastruktur.
- Du har ikke et lokalt Active Directory.

Nogle organisationer bruger Azure AD deltage som den primære metode til at udrulle arbejde ejes enheder, især, som de overføre de fleste eller alle deres ressourcer til skyen. Hybrid organisationer med både Active Directory og Azure AD kan også vælge at implementere en metode eller en anden afhængigt af den bruger eller afdeling.

Skoler og universiteter, kan for eksempel administrere personale i Active Directory og studerende i Azure AD. Virksomheden måske at administrere remote kontorer eller salg afdelinger i Azure AD. Både Azure AD deltage i og domæne joinforbindelse metoder kan bruges i en hybrid organisation. Azure AD-joinforbindelse kan være et godt supplement til forbindelse til et domæne til implementering af enheder i et arbejdsmiljø.

**Hvis den mest almindelige adgang til virksomhedsressourcer er via skyen, din organisation kan nyde yderligere fordele, hvis**:

- Du kan fjerne afhængigheder af lokale identitet infrastruktur.
- Du kan forenkle dine enheder implementeringsmodel få væk fra afbildning løsninger ved at tillade selvbetjening konfiguration.
- Du kan bruge administration af mobilenheder til at administrere alle dine enheder på tværs af forskellige platforme.

Se [udvider skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-overview.md)kan finde flere oplysninger om Azure AD deltage i.

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Hvornår skal jeg bruge domæne joinforbindelse (eller hold ved hjælp af det)

For de sidste 15 år har mange organisationer bruges forbindelse til et domæne til at forbinde arbejde enheder. Det gør det muligt for brugerne at logge på deres enheder med deres Active Directory-arbejds- eller skolekonti. Opret forbindelse til domænet kan også IT for at administrere disse enheder centralt og fuldt ud. Organisationer typisk er afhængige af afbildning metoder til at klargøring enheder, og brug ofte Center Configuration Manager SCCM (System) eller gruppe politik (generel) til at administrere dem.

**Virksomheden skal bruge domæne joinforbindelse (eller hold ved hjælp af den) på grund af følgende årsager**:

- Du har Win32-apps installeret på disse enheder, der bruger NTLM/Kerberos.
- Du har brug for generel eller SCCM/DCM selv at administrere enheder.
- Du vil fortsætte med at bruge afbildning løsninger til at konfigurere enheder til dine medarbejdere.

**Forbindelse til et domæne i Windows 10 giver dig også når du har forbindelse til Azure AD følgende fordele**:

- Stærke enhed bundet godkendelse og praktisk-logon til arbejds- eller skolekonto konti med Microsoft Passport og Windows Hej.
- Adgang til enterprise Windows Store for enheder, der bruger arbejds- eller skolekonti (ingen Microsoft-konto, der har kræves).
- Virksomhed-kompatibel roaming af brugerindstillinger på tværs af enheder, der bruger arbejds- eller skolekonto konti (ingen Microsoft-konto, der har kræves).
- Muligheden for at begrænse adgangen til organisatoriske apps til enheder, der er kompatible med politikker for organisatoriske enheder.

Se [udvider skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-overview.md)kan finde flere oplysninger om Azure AD deltage i.

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Aktivere sammenføjning af personligt ejes enheder til arbejde eller skole

For at understøtte BYOD i virksomheden, giver Windows 10 brugeren mulighed for at føje en arbejds- eller skolekonto til deres computer, tablet eller telefon. Når brugeren føjer arbejds-eller skolekonto, er enheden registreret med Azure AD og eventuelt tilmeldt mobilenhed-administrationssystem, som organisationen har konfigureret. Mappen vises disse enheder som 'Registreret' kontra 'Azure AD joinforbundne'. IT administraors kan anvende forskellige politikker, der er baseret på disse oplysninger, give en lysere touchskærm på personligt ejes enheder end på arbejde ejes enheder, hvis du ønsker.

Brugerne kan tilføje en arbejds- eller skolekonto til deres personlige enhed meget nemt. De kan gøre dette, når du åbner et arbejde program for første gang, eller de kan gøre det manuelt via menuen Indstillinger. Denne konto give SSO til ressourcer i organisationen.

Se [forbinde medlem af et domæne enheder til Azure AD til Windows 10 har](active-directory-azureadjoin-devices-group-policy.md)kan finde flere oplysninger om Azure AD deltage i.

## <a name="enable-domain-join-or-azure-ad-join"></a>Aktivér domæne eller Azure AD joinforbindelse

Alle metoder, der er beskrevet tidligere (forbindelse til et domæne, Azure AD joinforbindelse og Tilføj arbejde eller skolekonto) har indgangspunkter i Windows 10 brugeroplevelsen. Men alle kræver en IT-administrator skal aktivere funktionaliteten i infrastruktur, før oplevelsen fungerer.

## <a name="requirements-for-deploying-azure-ad-join"></a>Krav til installation af Azure AD deltage

Hvis du vil installere Azure AD deltage i en hvilken som helst antal brugere skal du følgende:

- Et Azure AD-abonnement.
- Et Azure AD Premium-abonnement, som mobilenhed management automatisk registrering, hvis du har brug for flere funktioner.
- Administration af mobilenheder – for eksempel, et Microsoft Intune-abonnement, administration af mobilenheder til Office 365 eller en anden partner mobilenhed management leverandører, der integreres med Azure AD. (Se [afsnittet ofte stillede spørgsmål](#frequently-asked-questions) i slutningen af denne artikel for at få mere at vide).

Hvis jeres facilitet er hybrid, anbefaler vi, at du installerer Azure AD-forbindelse for at udvide den lokale mappe til Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Krav til brug af forbindelse til et domæne med Azure AD

Opret forbindelse til domænet fortsat fungerer, som de altid har. Men hvis du vil have Azure AD-fordele skal du følgende:

- Et Azure AD-abonnement.
- En installation af Azure AD-forbindelse til at udvide den lokale mappe til Azure AD.
- En politik, der gør det medlem af et domæne, som skal have betinget adgang til Azure AD.
- En politik, der giver adgang til "medlem af et domæne" enheder, hvis du vil kunne begrænse adgangen for visse enheder.
- System Center Configuration Manager version 1509 til Technical Preview til at aktivere regler for at kræve kompatible enheder. (Se TechNet dokumentation og blogindlæg).

Du kan finde flere oplysninger om forbindelse til et domæne i Windows 10, se [forbinde medlem af et domæne enheder til Azure AD til Windows 10 har](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Krav til brug af BYOD og "Tilføj arbejds-eller skolekonto"

Aktivere "tage dine egne enhed" (BYOD) med arbejds- eller skolekonto konti, du skal bruge følgende:

- Et Azure AD-abonnement.
- Et Azure AD Premium-abonnement, som mobilenhed management automatisk registrering, hvis du har brug for flere funktioner.

## <a name="requirements-for-using-microsoft-passport"></a>Krav til brug af Microsoft Passport

Hvis du vil aktivere Microsoft Passport, skal du følgende:

- En offentlig nøgle infrastruktur til understøttelse af certifikat-baseret godkendelse, der bruger Microsoft Passport.
- Et Intune-abonnement til understøttelse af certifikat-baseret godkendelse, der bruger Microsoft Passport til Azure AD deltage i og arbejds- eller skolekonto konti.
- System Center Configuration Manager version 1509 til Technical Preview (se TechNet dokumentation og blogindlæg) til understøttelse af certifikat-baseret godkendelse, der bruger Microsoft Passport for forbindelse til et domæne.
- En politik for aktivering af Microsoft Passport i organisationen.

Som et alternativ til at bruge en PKI, kan du aktivere nøgle-baseret Microsoft Passport ved at gøre følgende:

- Installere Windows Server 2016 "Fremstilling Preview 1" DCs (ingen grund til domæne eller en skov funktionsniveauer; flere DCs for redundans der tjener tilstrækkeligt hvert Active Directory-websted).
- Angive for at aktivere Microsoft Passport i organisationen.

Du kan finde flere oplysninger om Microsoft Passport og Windows Hej i Windows 10, se < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Hvilke partner mobilenhed management produkter integreres med Azure AD?

Følgende leverandør produkter integrere med Azure AD for samlet tilmelding og betinget access i Windows 10:

- AirWatch ved VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Administration af mobilenheder SOTI lokalt
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Hvad med arbejdsplads deltage i Windows 10?
Deltag i arbejdsplads blev brugt i Windows 8.1 til at aktivere BYOD. I Windows 10 skal aktiveres BYOD via "Tilføj en arbejds- eller skolekonto", som beskrevet tidligere i dette dokument. Organisationer, hvor ikke integrere deres administration af mobilenheder med Azure AD, brugere kan tilmelde enheden under ledelse af manuelt via **Indstillinger** > **konti** > **arbejde adgang**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Brugere kan forbinde deres Microsoft-konto til deres domænekonto i Windows 10?
Ikke i Windows 10. I Windows 8.1, kunne brugere af medlem af et domæne enheder "tilsluttes" deres Microsoft-konto (for eksempel Hotmail, Live, Outlook, Xbox osv.) deres domænekonto for at aktivere bestemte oplevelser som SSO direkte tjenesterne, brug af Windows Store og roaming af brugerindstillinger på tværs af enheder. Microsoft-kontoen "forbinde" funktionalitet findes længere i Windows 10. Brugeren kan tilføje en eller flere Microsoft-konti som flere konti, for at aktivere SSO til forbrugere tjenester som Windows Store. Dette er gjort i **Indstillinger for** > **konti** > **din konto**.

Brugere, der opgraderer fra Windows 8.1 medlem af et domæne enheder, og hvem der har haft deres Microsoft-konto forbindelse, får automatisk deres forbundne Microsoft-konto, der er føjet til listen over flere konti, de bruger.


## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
