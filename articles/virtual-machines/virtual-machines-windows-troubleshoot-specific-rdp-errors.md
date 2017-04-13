<properties
    pageTitle="Bestemte RDP-fejlmeddelelser til Azure FOS | Microsoft Azure"
    description="Forstå bestemte fejlmeddelelser, der muligvis vises, når du forsøger bruge Fjernskrivebord forbindelse til en Windows virtuel maskine i Azure"
    keywords="Remote desktop fejl, forbindelse til Fjernskrivebord fejl, kan ikke oprette forbindelse til VM, fejlfinding af computeren"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Fejlfinding i forbindelse med bestemte RDP til en Windows-VM i Azure
Du modtager muligvis en bestemt fejlmeddelelse, når du bruger Fjernskrivebord forbindelse til en Windows virtuel maskine (VM) i Azure. I denne artikel indeholder oplysninger om nogle af de mest almindelige fejlmeddelelser, der er stødt på, sammen med fejlfindingstrin for at løse dem.. Hvis du har problemer med at oprette forbindelse til din VM ved hjælp af RDP, men ikke opstår en bestemt fejlmeddelelse, skal du se [fejlfindingsvejledningen til Fjernskrivebord](virtual-machines-windows-troubleshoot-rdp-connection.md).

Finde oplysninger om bestemte fejlmeddelelser, se følgende:

- [Den fjernsession blev afbrudt, fordi der er ingen Remote Desktop licensservere til rådighed med en licens](#rdplicense).
- [Fjernskrivebord kan ikke finde computeren "navn"](#rdpname).
- Der opstod fejl i [en godkendelse. Lokale sikkerhed myndigheder kan ikke kontaktes](#rdpauth).
- [Windows sikkerhed fejl: dine legitimationsoplysninger ikke fungerer](#wincred).
- [Denne computer kan ikke oprette forbindelse til fjerncomputeren](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Den fjernsession blev afbrudt, fordi der er ingen Remote Desktop licensservere til rådighed med en licens.

Årsag: 120-dages licensering perioden for rollen skrivebord fjernserver er udløbet, og du skal installere licenser.

Gem en lokal kopi af filen RDP fra portalen som en løsning, og Kør denne kommando kommandoprompt PowerShell til at oprette forbindelse. Dette trin deaktiverer licenser til kun denne forbindelse:

        mstsc <File name>.RDP /admin

Hvis du ikke faktisk har brug for mere end to samtidige Fjernskrivebord forbindelser til VM, kan du bruge Server Manager til at fjerne rollen Remote Desktop-Server.

Du kan finde flere oplysninger, kan du se blogindlægget [Azure VM mislykkes med "Ingen Remote Desktop licensservere tilgængelig"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Fjernskrivebord kan ikke finde computeren "navn".

Årsag: Remote Desktop-klienten på computeren ikke identificere navnet på computeren i indstillingerne for RDP-fil.

Mulige løsninger:

- Hvis du bruger en organisations intranet, skal du kontrollere, at computeren har adgang til proxyserveren og kan sende HTTPS trafik til den.

- Hvis du bruger en gemt lokalt RDP-fil, kan du prøve at bruge det, der genereres af portalen. Dette trin sikrer, at du har den korrekte DNS-navn til den virtuelle maskine eller skytjenesten og slutpunkt port VM. Her er et eksempel RDP-fil, der genereres af portalen:

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Den adresse del af denne RDP-fil er:
- Det fuldstændige domænenavn for skytjenesten, der indeholder VM ("Hold-azdatatier.cloudapp.net" i dette eksempel).

- Den eksterne TCP-port på slutpunktet for Fjernskrivebord trafik (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Der opstod en godkendelsesfejl. Lokale sikkerhed myndigheder kan ikke kontaktes.

Årsag: Destinationen VM kan ikke finde sikkerhed nøglecenter i bruger navn del af dine legitimationsoplysninger.

Når dit brugernavn er i formularen *SecurityAuthority*\\*brugernavn* (eksempel: CORP\User1), *SecurityAuthority* del er enten den VM computernavn (for lokal sikkerhed nøglecenter) eller en Active Directory-domænenavn.

Mulige løsninger:

- Hvis firmaet er lokale til VM, skal du kontrollere, at navnet VM er stavet forkert.

- Hvis kontoen er på et Active Directory-domæne, kan du kontrollere stavningen af domænenavnet.

- Hvis det er en Active Directory-domænekonto og domænenavnet er stavet korrekt, skal du kontrollere, et domænenavn fra domænecontrolleren er tilgængelig i det pågældende domæne. Det er et almindelige fejl i Azure virtuelle netværk, der indeholder domæne enheder, en domænecontrolleren ikke er tilgængelig, fordi den ikke er blevet startet. Som en løsning, kan du bruge en lokal administratorkonto i stedet for en domænekonto.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-sikkerhed fejl: dine legitimationsoplysninger ikke fungerer.

Årsag: Destinationen VM kan ikke bekræfte dit kontonavn og din adgangskode.

En Windows-baseret computer kan validere legitimationsoplysningerne for en lokal konto eller en domænekonto.

- Lokale konti for brug af *computernavn*\\*brugernavn* syntaks (eksempel: SQL1\Admin4798).
- Domænekonti for brug af *DomainName*\\*brugernavn* syntaks (eksempel: CONTOSO\peterodman).

Hvis du har forfremmet din VM til et domænenavn fra domænecontrolleren i et nyt Active Directory-område, konverteres den lokale administratorkonto, som du har logget på med en tilsvarende konto med den samme adgangskode i ny område og domæne. Den lokale konto slettes derefter.

Eksempelvis hvis du er logget på med den lokale konto DC1\DCAdmin, og derefter fremhævede den virtuelle maskine som et domænenavn fra domænecontrolleren i en ny skov for domænet, corp.contoso.com, den lokale konto DC1\DCAdmin bliver slettet, og der oprettes en ny domænekonto (CORP\DCAdmin) med den samme adgangskode.

Sørg for, at kontonavnet er et navn, kan du kontrollere den virtuelle maskine som en gyldig konto, og, at adgangskoden er korrekte.

Hvis du vil ændre adgangskoden til den lokale administratorkonto skal se, [hvordan du nulstiller en adgangskode eller tjenesten Remote Desktop til Windows virtuelle computere](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Denne computer kan ikke oprette forbindelse til fjerncomputeren.

Årsag: Den konto, der bruges til at forbinde har ikke Remote Desktop logon rettigheder.

Alle Windows-computer har en Fjernskrivebord lokale brugergruppe, som indeholder de konti og grupper, der kan logge på den fra en fjernplacering. Medlemmer af gruppen af lokale administratorer også have adgang, selvom disse konti ikke vises i den lokale gruppe Fjernskrivebord brugere. Til medlem af et domæne maskiner indeholder den lokale administratorgruppe også domæneadministratorer for domænet.

Sørg for, at den konto, du bruger til at oprette forbindelse med har Fjernskrivebord logon rettigheder. Som du kan løse problemet ved at bruge et domæne eller lokale administratorkonto til at oprette forbindelse via Fjernskrivebord. Hvis du vil tilføje den ønskede konto til den lokale gruppe til brugere af Fjernskrivebord, bruge snap-in til Microsoft Management Console (**Systemværktøjer > lokale brugere og grupper > Grupper > brugere af Fjernskrivebord**).


## <a name="next-steps"></a>Næste trin
Hvis ingen af disse fejl opstod, og du har et ukendt problem med at oprette forbindelse via RDP, skal du se [fejlfindingsvejledningen til Fjernskrivebord](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Azure IaaS (Windows) diagnostics-pakken](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Trin til fejlfinding i at få adgang til programmer, der kører på en VM, under [fejlfinding i forbindelse med adgang til et program, der kører på en Azure VM](virtual-machines-linux-troubleshoot-app-connection.md).
- Hvis du har problemer med brug af Secure Shell (SSH) til at oprette forbindelse til en Linux VM i Azure, se [fejlfinding i forbindelse med SSH forbindelser til en Linux VM i Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).