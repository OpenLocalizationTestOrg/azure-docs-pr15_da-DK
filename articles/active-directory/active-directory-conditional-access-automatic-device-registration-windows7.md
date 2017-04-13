<properties
    pageTitle="# Konfigurere automatiske enhed registrering for Windows 7 domæne tilsluttet enheder | Microsoft Azure"
    description="Trin til at konfigurere dit domæne i Windows 7 sammenføjet automatisk registrere med Azure AD-enheder. og trin til at udrulle pakken enhed registrering software til dit domæne i Windows 7 joinforbundne enheder, der bruger et software fordeling system som System Center Configuration Manager."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Konfigurere automatiske enhed registrering for Windows 7 domæne tilsluttet enheder

Du kan konfigurere dine Windows 7 domæne tilsluttet enheder til at registrere automatisk med Azure AD som IT-administrator. Hvis du vil gøre dette, skal du installere enhed registrering programpakke på dit domæne i Windows 7 sammenføjet enheder, der bruger et software fordeling system som System Center Configuration Manager. Sørg for at læse og fuldføre de forudsætninger, der er angivet i automatisk registrering af enhed med Azure Active Directory til Windows-domæne, der er joinforbundne enheder.

>[AZURE.NOTE]
 Seneste oplysninger om, hvordan du konfigurerer automatisk enhed registrering se, [hvordan du konfigurerer automatisk registrering af Windows-domæne joinforbundne enheder med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Installere enhed registrering softwarepakken på Windows 7 joinforbundne domæne enheder

Enhed registrering for Windows 7 findes som en [MSI-programpakker](https://connect.microsoft.com/site1164). Pakken skal være installeret på Windows 7-computere, der er tilsluttet et Active Directory-domæne. Du skal installere pakken ved hjælp af et software fordeling system som System Center Configuration Manager. MSI-pakken understøtter indstillingerne uovervåget standardinstallation ved hjælp af parameteren/quiet parameter.
Softwarepakken kan hentes på [Microsoft Connect websted](https://connect.microsoft.com/site1164). Her kan du vælge og derefter kan du hente arbejdsplads deltage for Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Arbejdsplads Deltag med Azure Active Directory
Enhed registrering for Windows 7 domæne tilsluttet enheder kræver ikke eller omfatter en brugergrænseflade. Når installeret på computeren, skal registreres alle domæne brugere, der logger på maskinen automatisk og uovervåget med en enhedsobjekt i Azure AD. Der vil være et enhedsobjekt i Azure AD for alle registrerede brugere af den fysiske enhed.

Installationsprogrammet opretter en planlagt opgave på systemet, der kører i brugerens kontekst og udløses på bruger-logon. Opgaven registrerer automatisk brugeren og enheden med Azure AD, når brugeren tegn i er fuldført.
Du kan finde den planlagte opgave i biblioteket opgave Scheduler under **Microsoft** > **Arbejdsplads deltage i**.
Opgaven kører og registrere alle Active Directory-brugere, log på computeren.
Følgende illustration viser trinvise processen til automatisk enhed registrering.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. En bruger (it-medarbejder) logger på en Windows 7 klientcomputer ved hjælp af Active Directory domain legitimationsoplysninger.
1. Den arbejdsplads deltage i planlagte opgave udføres.
1. Brugeren godkendes automatisk med AD FS ved hjælp af Windows-integreret godkendelse.
1. PC med Windows 7 er registreret for brugeren i Azure AD.
1. En enhedsobjekt og certifikat oprettes i Azure AD. Objektet, der repræsenterer den user@device.
1. Arbejdsplads deltage i certifikatet er gemt på computeren.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Fjerne registreringen af domænet Windows 7 joinforbundne enheder

Du kan vælge unregister dine domæne tilsluttet Windows 7 enheder ved at gøre følgende: fjerne arbejdsplads deltage programpakke fra dit domæne i Windows 7 joinforbundne enheder, der bruger et software fordeling system som System Center Configuration Manager.

Åbn en kommandoprompt på Windows 7-computer og udføre følgende kommando for at fjerne registreringen af enheden:

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Denne kommando skal køres i konteksten for hver domænebruger, der er signeret til computeren.
Logbog og fejl for Windows 7 domæne sammenføjet enheder.

Windows-hændelsesloggen på Windows 7-computer, vises meddelelser med relation til arbejdsplads deltage i. Du kan finde meddelelser om både vellykkede og mislykkede arbejdsplads deltage i begivenheder. Hændelsesloggen kan være findes i Logbog under tjenester logfiler for programmer og > deltage i Microsoft-arbejdsplads.

## <a name="additional-topics"></a>Yderligere emner

- [Oversigt over Azure Active Directory enhed registrering](active-directory-conditional-access-device-registration-overview.md)
- [Automatisk enhed registrering med Azure Active Directory for Windows Domain-Joined enheder](active-directory-conditional-access-automatic-device-registration.md)
- [Konfigurere automatiske enhed registrering til Windows 8.1 domæne tilsluttet enheder](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Automatisk enhed registrering med medlem af et domæne Azure Active Directory til Windows 10-enheder](active-directory-azureadjoin-devices-group-policy.md)
