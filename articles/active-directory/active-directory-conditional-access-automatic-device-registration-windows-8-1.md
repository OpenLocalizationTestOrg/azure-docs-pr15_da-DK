<properties
    pageTitle="Konfigurere automatiske enhed registrering til Windows 8.1 domæne tilsluttet enheder | Microsoft Azure"
    description=" Trin til at konfigurere Gruppepolitik for Windows 8.1 medlem af et domæne enheder, der automatisk registrere med Azure AD. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Konfigurere automatiske enhed registrering til Windows 8.1 domæne tilsluttet enheder

Du kan bruge en Active Directory-Gruppepolitik til at konfigurere dine Windows 8.1 domæne tilsluttet enheder til at registrere automatisk med Azure AD. Hvis du vil konfigurere den gruppepolitik, skal du have mindst én domæne joinforbundne Windows Server 2012 R2 eller Windows 8.1 maskine med funktionen Administration af gruppepolitik, der er installeret. Når denne Gruppepolitik er aktiveret for dit domæne, registreres en hvilken som helst domænebruger, der logger på maskinen automatisk og uovervåget med en enhedsobjekt i Azure AD. Der vil være et enhedsobjekt i Azure AD for alle registrerede brugere af den fysiske enhed. Sørg for at læse og fuldføre forudsætninger enhed automatisk registrering med Azure Active Directory for Windows Domain-Joined enheder.

>[AZURE.NOTE]
 Seneste oplysninger om, hvordan du konfigurerer automatisk enhed registrering se, [hvordan du konfigurerer automatisk registrering af Windows-domæne joinforbundne enheder med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Konfigurere Gruppepolitik for dine Windows 8.1 domæne tilsluttet enheder

1. Åbn Server Manager, og gå til **Funktioner** > **Administration af gruppepolitik**.
2. Gå til domænenoden, der svarer til det domæne, du vil bruge til at aktivere **Automatisk arbejdsplads deltage**fra administration af gruppepolitik.
3. Højreklik på **Gruppepolitikobjekter** , og klik på **Ny**. Giv Gruppepolitik objektet et navn, for eksempel **Deltage i en automatisk på arbejdspladsen**. Klik på **OK**.
4. Højreklik på nye Gruppepolitik objektet, og vælg derefter **Rediger**.
5. Gå til **Computerkonfiguration** > **politikker** > **Administrative skabeloner** > **Windows-komponenter** > **arbejdsplads deltage i**.
6. Højreklik automatisk arbejdsplads joinforbindelse klientcomputere, og vælg derefter **Rediger**.
7. Vælg alternativknappen aktiveret, og klik derefter på Anvend. Klik på **OK**.
8. Du kan nu sammenkæde Gruppepolitik objektet til et sted, hvor dit valg. For at aktivere denne politik for alle de domæne tilsluttet Windows 8.1 enheder i din organisation skal oprette et link gruppepolitikken til domænet.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Fjerne registreringen af domænet Windows 8.1 joinforbundne enheder

Du kan vælge unregister dine domæne tilsluttet Windows 8.1 enheder ved at gøre følgende: ændre indstillingerne for arbejdsplads deltage i Gruppepolitik, der er oprettet i forrige afsnit. Angiv den automatisk på arbejdspladsen joinforbindelse computere Klientpolitikken til deaktiveret. Dette forhindrer, at nye enheder automatisk deltagelse i arbejdsområdet.

Unregister eksisterende domæne tilsluttet Windows 8.1 maskiner af følgende én af de to indstillinger nedenfor:

* Mulighed 1: **fjerner registreringen af en Windows 8.1 domæne joinforbundne enhed ved hjælp af PC-indstillinger**
  1. Gå til **PC-indstillinger**på enheden Windows 8.1, > **netværk** > **på arbejdspladsen**
  2. Vælg **Forlad**.
Skal gentage denne proces for hver domænebruger, der er logget på computeren og er blevet automatisk arbejdsplads joinforbundne.

* Mulighed 2: Fjerne registreringen af en Windows 8.1 domæne joinforbundne enhed ved hjælp af et script
    1. Åbn en kommandoprompt på Windows 8.1-computer og udføre følgende kommando:` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Denne kommando skal køres i konteksten for hver domænebruger, der er signeret til computeren.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Logbog og fejl til Windows 8.1 domæne tilsluttet enheder

Windows-hændelseslog på en Windows 8.1 maskine viser meddelelser, der er relateret til enhed registrering. Du kan finde meddelelser til både vellykkede og mislykkede hændelser. 

Hændelsesloggen kan være findes i Logbog under tjenester **logfiler**for programmer og > **Microsoft** > **Windows > arbejdsplads deltage i**.

##<a name="additional-details"></a>Få mere at vide

Gruppepolitikken gør det muligt for en planlagt opgave i systemet, der kører i brugerens kontekst og udløses på bruger-logon. Opgaven vil automatisk registrere brugeren og enheden med Azure AD, når logonnavn er fuldført. Den planlagte opgave kan findes på Windows 8.1 enheder i biblioteket opgave Scheduler under **Microsoft** > **Windows** > **Arbejdsplads deltage i**. Opgaven kører og registrere alle Active Directory-brugere, logge på. 

## <a name="additional-topics"></a>Yderligere emner
- [Oversigt over Azure Active Directory enhed registrering](active-directory-conditional-access-device-registration-overview.md)
- [Automatisk enhed registrering med medlem af et domæne Azure Active Directory til Windows 10-enheder](active-directory-conditional-access-automatic-device-registration.md)
- [Konfigurere automatiske enhed registrering for Windows 7 domæne tilsluttet enheder](active-directory-conditional-access-automatic-device-registration-windows7.md)

