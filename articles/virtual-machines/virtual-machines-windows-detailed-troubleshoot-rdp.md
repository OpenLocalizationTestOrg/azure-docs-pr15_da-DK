<properties
    pageTitle="Detaljeret remote desktop fejlfinding | Microsoft Azure"
    description="Gennemse detaljerede fejlfindingstrin for remote desktop fejl, hvor du kan ikke til en Windows virtuelle maskiner i Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="kan ikke oprette forbindelse til Fjernskrivebord, fejlfinding i forbindelse med Fjernskrivebord, Fjernskrivebord kan ikke oprette forbindelse, remote desktop fejl, fejlfinding af skrivebordet, Fjernskrivebord
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaljerede fejlfindingstrin til problemer med forbindelse til Fjernskrivebord til Windows FOS i Azure

Denne artikel indeholder detaljerede fejlfindingstrin for at diagnosticere og løse komplekse Fjernskrivebord fejl i Windows-baseret Azure virtuelle computere.

> [AZURE.IMPORTANT] Hvis du vil fjerne mere fælles Fjernskrivebord fejlene, Kontrollér, at Læs [grundlæggende artiklen om fejlfinding til Fjernskrivebord](virtual-machines-windows-troubleshoot-rdp-connection.md) , før du fortsætter.

Du kan støde på fejlmeddelelsen Fjernskrivebord, der ikke ligner en af de bestemte fejlmeddelelser, der er dækket i [grundlæggende Fjernskrivebord fejlfinding vejledningen](virtual-machines-windows-troubleshoot-rdp-connection.md). Følg disse trin for at finde ud af, hvorfor klienten RDP (Remote Desktop) er ikke oprette forbindelse til tjenesten RDP på Azure VM.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og forummerne stakoverløb](https://azure.microsoft.com/support/forums/). Alternativt kan du også sende en Azure support-hændelse. Gå til det [Azure supportwebsted](https://azure.microsoft.com/support/options/) og klikke på **Få Support**. Oplysninger om brug af understøttelse af Azure læse [Ofte stillede spørgsmål til Microsoft Azure-Support](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Komponenter i en Fjernskrivebord-forbindelse

Følgende komponenter er involveret i en RDP-forbindelse:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Før du fortsætter, kan det hjælpe at mentalt gennemgå Hvad er blevet ændret siden seneste vellykket Fjernskrivebord forbindelsen til VM. Eksempel:

- Den offentlige IP-adresse VM eller skytjenesten, der indeholder den VM (også kaldet den virtuelle IP-adresse [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) er blevet ændret. RDP fejlen skyldes din DNS-klientcache er stadig den *gamle IP-adresse* , der er registreret DNS-navn. Rens cachen i din DNS-klienten, og prøv at oprette forbindelse VM igen. Eller prøv at oprette forbindelse direkte med den nye VIP.
- Du bruger et tredjepartsprogram til at administrere dine Fjernskrivebord forbindelser i stedet for med genereres af portalen Azure-forbindelsen. Kontrollér, at konfigurationen af programmet, har den korrekte TCP-port til Fjernskrivebord trafik. Du kan kontrollere denne port til en klassisk virtuel maskine i [Azure-portalen](https://portal.azure.com), ved at klikke på den VM Indstillinger > slutpunkter.


## <a name="preliminary-steps"></a>Foreløbige trin

Før du fortsætter til detaljeret fejlfinding

- Kontrollere status for den virtuelle maskine i portalen Azure klassisk eller portalen Azure vedrørende spørgsmål om indlysende.
- Følg [fix hurtige trin til almindelige RDP-fejl i guiden grundlæggende fejlfinding](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Prøve at oprette forbindelse til VM via Fjernskrivebord efter disse trin.


## <a name="detailed-troubleshooting-steps"></a>Detaljerede fejlfindingstrin

Klienten Fjernskrivebord muligvis ikke adgang til tjenesten Fjernskrivebord på Azure VM på grund af problemer på følgende kilder:

- [Remote-klienten på computeren](#source-1-remote-desktop-client-computer)
- [Organisation intranet edge-enhed](#source-2-organization-intranet-edge-device)
- [Tjenesteslutpunkt i skyen og få adgang til listen over objekter (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Netværk sikkerhedsgrupper](#source-4-network-security-groups)
- [Windows-baseret Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Kilde 1: Remote-klienten på computeren

Kontrollér, at computeren kan gøre Fjernskrivebord forbindelser til en anden lokalt, Windows-baseret computer.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Hvis du ikke kan du kontrollere for følgende indstillinger på din computer:

- En lokal firewall-indstilling, der blokerer Fjernskrivebord trafik.
- Lokalt installeret proxy klientsoftware, der forhindrer Fjernskrivebord-forbindelser.
- Lokalt installeret software, der forhindrer Fjernskrivebord overvågning af netværket.
- Andre typer af sikkerhedssoftware, der enten overvåge trafik eller tillade/forbyde bestemte typer af trafik, der forhindrer Fjernskrivebord-forbindelser.

I disse tilfælde midlertidigt deaktivere softwaren og prøve at oprette forbindelse til en lokal computer via Fjernskrivebord. Hvis du kan finde ud af det faktiske årsag denne måde, kan du arbejde med din netværksadministrator til at rette programindstillinger for at tillade forbindelser til Fjernskrivebord.

## <a name="source-2-organization-intranet-edge-device"></a>Kilde 2: Organisation intranet edge-enhed

Kontrollér, at en computer, der er direkte forbindelse til internettet kan gøre Fjernskrivebord forbindelser til din Azure virtuelle maskine.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Hvis du ikke har en computer, der er direkte forbundet til internettet, oprette og teste med en ny Azure virtuel maskine i en ressource gruppe eller skybaseret tjeneste. Se [oprette en virtuel maskine kører Windows i Azure](virtual-machines-windows-hero-tutorial.md)kan finde flere oplysninger. Du kan slette den virtuelle maskine og ressourcegruppen eller skybaseret tjeneste, når du har en test.

Hvis du kan oprette forbindelse til Fjernskrivebord med en computer, der er direkte tilsluttet internettet, kan du kontrollere din organisation intranet edge-enhed til:

- En intern firewall blokerer HTTPS forbindelser til internettet.
- En proxy-server, der forhindrer Fjernskrivebord-forbindelser.
- Registrering af uautoriseret adgang eller overvågning software, der kører på enheder i netværket kant, der forhindrer Fjernskrivebord af netværket.

Arbejde med din netværksadministrator til at rette indstillingerne for din organisation intranet edge-enhed for at tillade HTTPS-baserede Fjernskrivebord forbindelser til internettet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Kilde 3: Skyen tjenesteslutpunkt og ACL

For FOS oprettet ved hjælp af modellen Klassisk installation, kan du kontrollere, at en anden Azure VM, der er i samme skybaseret tjeneste eller virtuelt netværk kan foretage Fjernskrivebord din Azure VM.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Til virtuelle maskiner, der er oprettet i ressourcestyring, gå til [kilde 4: netværk sikkerhedsgrupper](#source-4-network-security-groups).

Hvis du ikke har en anden virtuel maskine i den samme skytjeneste eller virtuelt netværk, oprette en. Følg trinnene i [oprette en virtuel maskine kører Windows i Azure](virtual-machines-windows-hero-tutorial.md). Slette virtuelt test efter testen er fuldført.

Hvis du kan oprette forbindelse via Remote Desktop til en virtuel maskine i den samme skytjeneste eller virtuelt netværk, kan du kontrollere for disse indstillinger:

- Konfigurationen af slutpunktet til Fjernskrivebord trafik på destinationen VM: den private TCP-port på slutpunktet skal svare til den TCP-port, som den VM Fjernskrivebord service lytter (standard er 3389).
- ACL for Fjernskrivebord trafik slutpunktet på destinationen VM: ACLs kan du angive tillades eller afvises indgående trafik fra internettet baseret på dens kildens IP-adresse. Konfigureret forkert ACLs kan forhindre indgående Fjernskrivebord trafik til slutpunktet. Markér din ACLs for at sikre den indgående trafik fra din offentlige IP-adresser for din proxy eller andre edge server er tilladt. Du kan finde flere oplysninger, se [Hvad er et netværk listen ACL (Access Control)?](../virtual-network/virtual-networks-acl.md)

Fjerne det aktuelle slutpunkt for at kontrollere, om slutpunktet er kilden til problemet, og Opret en ny, vælge en tilfældig port i området 49152-65535 til det eksterne portnummer. Se, [hvordan du konfigurerer slutpunkter til en virtuel maskine](virtual-machines-windows-classic-setup-endpoints.md)kan finde flere oplysninger.

## <a name="source-4-network-security-groups"></a>Kilde 4: Netværk sikkerhedsgrupper

Netværk sikkerhedsgrupper Tillad mere detaljeret kontrol over tilladte indgående og udgående trafik. Du kan oprette regler, der strækker sig over undernet og tjenester i en Azure virtuelt netværk i skyen. Kontrollere dine netværk sikkerhedsgruppe regler for at sikre, at Fjernskrivebord trafik fra internettet er tilladt:

- Vælg din VM i portalen Azure
- Klik på **Indstillinger for alle** | **netværksgrænseflader** , og vælg grænsefladen dit netværk.
- Klik på **Indstillinger for alle** | **netværk sikkerhedsgruppe** , og vælg dit netværk sikkerhedsgruppe.
- Klik på **Indstillinger for alle** | **indgående sikkerhedsregler** og sikre, at du har en regel, så RDP på TCP-port 3389.
    - Hvis du ikke har en regel, skal du klikke på **Tilføj** for at oprette en regel. Angiv **TCP** for protokollen og derefter **3389** for port destinationsområde.
    - Kontrollér, at handlingen, der er indstillet til **Tillad** , og klik på OK for at gemme den nye indgående regel.


Du kan finde flere oplysninger, se [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Kilde 5: Windows-baseret Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Brug [Azure IaaS (Windows) diagnostics-pakken](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) til at se, om fejlen er på grund af den Azure virtuelle maskine selve. Hvis denne diagnostics-pakken er i stand til at løse problemet **RDP-forbindelse til en Azure VM (kræver genstart)** , skal du følge vejledningen i [denne artikel](virtual-machines-windows-reset-rdp.md). I denne artikel nulstiller tjenesten Fjernskrivebord på den virtuelle maskine:

- Aktivér "Fjernskrivebord" Windows Firewall standardreglen (TCP-port 3389).
- Aktivere Fjernskrivebord forbindelser ved at angive værdien HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections i registreringsdatabasen til 0.

Prøv at oprette forbindelse fra din computer igen. Hvis du stadig ikke kan oprette forbindelse via Fjernskrivebord, skal du kontrollere følgende mulige problemer:

- Tjenesten Fjernskrivebord kører ikke på destinationen VM.
- Tjenesten Fjernskrivebord lytter ikke på TCP-port 3389.
- Windows Firewall eller en anden lokal firewall har en udgående regel, der forhindrer Fjernskrivebord trafik.
- Registrering af uautoriseret adgang eller netværk overvågning software, der kører på en Azure virtuel maskine forhindrer Fjernskrivebord-forbindelser.

Du kan bruge en Azure PowerShell-fjernsession til Azure virtuelt for FOS oprettet ved hjælp af den klassiske implementeringsmodel. Først skal du installere et certifikat til den virtuelle maskine skytjenester. Gå til [Konfigurer Secure Remote PowerShell adgang til virtuelle Azure-computere](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) og hente filen **InstallWinRMCertAzureVM.ps1** script til din lokale computer.

Dernæst skal installere Azure PowerShell, hvis du ikke allerede har. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Åbn en kommandoprompt Azure PowerShell derefter, og ændre den aktuelle mappe til placeringen for **InstallWinRMCertAzureVM.ps1** script-fil. Hvis du vil køre en Azure PowerShell-script, skal du angive den korrekte udførelse af politik. Kør kommandoen **Get-Udførselspolitikken** til at bestemme dit aktuelle politikniveau. Du kan finde oplysninger om at angive det relevante niveau [Sæt Udførselspolitikken](https://technet.microsoft.com/library/hh849812.aspx).

Udfyld derefter Azure abonnementets navn, navnet på skyen tjenesten og navnet på din virtuelt (fjerne den < og > tegn), og derefter køre disse kommandoer.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Du kan få det korrekte Abonnementsnavnet fra egenskaben _SubscriptionName_ for visningen af kommandoen **Get-AzureSubscription** . Du kan få navnet på cloud-tjenesten for den virtuelle maskine fra kolonnen _ServiceName_ i visningen af kommandoen **Get-AzureVM** .

Kontrollere, om du har det nye certifikat. Åbne en certifikater snap-in for den aktuelle bruger og se i mappen, **Der er tillid til rod certificering Authorities\Certificates** . Skal du se afsnittet et certifikat med din skybaseret tjeneste i kolonnen udstedt til DNS-navn (eksempel: cloudservice4testing.cloudapp.net).

Dernæst skal starte en Azure PowerShell-fjernsession ved hjælp af disse kommandoer.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Når du har angivet legitimationsoplysninger for gyldige administrator, skal du se afsnittet noget lignende følgende Azure PowerShell-prompt:

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

Den første del af denne prompt har kaldt skybaseret tjeneste, der indeholder destinationen VM, som kan være forskellige fra "cloudservice4testing.cloudapp.net". Du kan nu udstede Azure PowerShell kommandoer til denne skybaseret tjeneste for at undersøge problemerne nævnt og rette konfigurationen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Hvis du vil rette manuelt Remote Desktop Services lytte TCP-port

Hvis du ikke kan køre [Azure IaaS (Windows) diagnostics-pakken](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) til problem **RDP-forbindelse til en Azure VM (kræver genstart)** kommandoprompten remote Azure PowerShell session, du Kør denne kommando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Egenskaben portnummer viser nummeret på den aktuelle port. Hvis det er nødvendigt, kan du ændre Fjernskrivebord port tilbage til standardværdien (3389) ved hjælp af denne kommando.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Kontrollér, at port er blevet ændret til 3389 ved hjælp af denne kommando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Afslut Azure PowerShell-fjernsession ved hjælp af denne kommando.

    Exit-PSSession

Kontrollér, Fjernskrivebord slutpunkt til Azure VM også bruger TCP-port 3398 som dens interne port. Genstart Azure VM og forsøg at Fjernskrivebord oprette forbindelse igen.


## <a name="additional-resources"></a>Yderligere ressourcer

[Azure IaaS (Windows) diagnostics-pakken](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Hvordan du nulstiller en adgangskode eller tjenesten Remote Desktop til Windows virtuelle maskiner](virtual-machines-windows-reset-rdp.md)

[Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)

[Fejlfinding i forbindelse med Secure Shell (SSH) forbindelser til en Linux-baserede Azure virtuel maskine](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Foretage fejlfinding af adgang til et program, der kører på en Azure virtuel maskine](virtual-machines-linux-troubleshoot-app-connection.md)
