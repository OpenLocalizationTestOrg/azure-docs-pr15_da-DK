<properties
    pageTitle="Installere Symantec Endpoint Protection på en VM | Microsoft Azure"
    description="Lær, hvordan du installerer og konfigurerer filtypenavnet Symantec Endpoint Protection sikkerhed på en ny eller eksisterende Azure VM, der er oprettet med den klassiske implementeringsmodel."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Sådan installerer og konfigurerer Symantec Endpoint Protection på en Windows-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

I denne artikel beskrives, hvordan du installerer og konfigurerer Symantec Endpoint Protection klienten på en eksisterende virtuelt (VM), der kører Windows Server. Dette er den komplette klient, som omfatter tjenester som virus og spywarebeskyttelse, firewall og beskyttelse mod uautoriseret adgang. Klienten installeres som filtypenavnet sikkerhed ved hjælp af VM Agent.

Hvis du har et eksisterende abonnement fra Symantec for en lokal løsning, kan du bruge det til at beskytte din Azure virtuelle computere. Hvis du ikke er en kunde endnu, kan du tilmelde dig til et prøveabonnement. Du kan finde flere oplysninger om denne løsning se [Symantec Endpoint Protection på Microsofts Azure platform][Symantec]. Denne side indeholder også links til oplysninger om volumenlicensering og instruktioner til installation af klienten på computeren, hvis du allerede en Symantec kunde.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installere Symantec Endpoint Protection på en eksisterende VM

Før du begynder, skal du følgende:

- Azure PowerShell-modulet, version 0.8.2 eller nyere på computeren arbejde. Du kan kontrollere versionen af Azure PowerShell, som du har installeret med den **Get-modul azure | Formater tabel version** kommandoen. Instruktioner og et link til den nyeste version, se, [hvordan du installere og konfigurere Azure PowerShell][PS]. Log på din Azure abonnement ved hjælp af `Add-AzureAccount`.

- Den VM Agent, der kører på Azure Virtual Machine.

Først skal bekræfte, at VM Agent allerede er installeret på den virtuelle maskine. Skriv navnet til skyen tjenesten og virtuelt navn og derefter køre følgende kommandoer på en administrator niveau Azure PowerShell kommandoprompt. Erstatte alt i anførselstegn, herunder den < og > tegn.

> [AZURE.TIP] Hvis du ikke kender de skybaseret tjeneste og virtuelt navne, du Kør **Get-AzureVM** for at få vist navnene for alle virtuelle maskiner i dit aktuelle abonnement.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Hvis kommandoen **Skriv host** viser **Sand**, er VM Agent installeret. Hvis det viser **Falsk**, se vejledningen og et link til download i Azure blog, skrive [VM Agent og filtypenavne - del 2][Agent].

Hvis VM Agent er installeret, skal du køre disse kommandoer til at installere Symantec Endpoint Protection agent.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Kontrollere, at filtypenavnet Symantec sikkerhed er blevet installeret og er opdateret:

1.  Log på den virtuelle maskine. Finde en vejledning, se, [hvordan du logger på virtuelt kører Windows Server][Logon].
2.  Windows Server 2008 R2, ved at klikke på **Start > Symantec Endpoint Protection**. Skriv **Symantec**for Windows Server 2012 eller Windows Server 2012 R2, fra startskærmen, og klik derefter på **Symantec Endpoint Protection**.
3.  Anvend opdateringer fra fanen **Status** i vinduet **Status Symantec Endpoint Protection** eller genstart, hvis det er nødvendigt.

## <a name="additional-resources"></a>Yderligere ressourcer

[Hvordan du kan logge på en virtuel maskine, der kører Windows Server][Logon]

[Azure VM Extensions og funktioner][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493