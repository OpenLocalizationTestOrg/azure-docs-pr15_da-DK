<properties
    pageTitle="Installere Trend Micro Deep Security på en VM | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du installerer og konfigurerer Trend Micro sikkerhed på en VM, der er oprettet med den klassiske implementeringsmodel i Azure."
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


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Sådan installerer og konfigurerer Trend Micro Deep Security som en tjeneste på en Windows-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

I denne artikel beskrives, hvordan du installerer og konfigurerer Trend Micro Deep Security som en tjeneste på en ny eller eksisterende virtuel maskine (VM) kører Windows Server. Deep sikkerhed som en tjeneste indeholder antimalwarebeskyttelse, en firewall, en uautoriseret adgang datakørsel-systemet og integritet overvågning.

Klienten installeres som filtypenavnet sikkerhed via VM Agent. På en ny virtuel maskine, kan du installere VM Agent sammen med den Deep Agent for sikkerhed. På en eksisterende virtuel maskine, som ikke har VM Agent, skal du downloade og installere den først. Denne artikel omhandler begge situationer.

Hvis du har eksisterende abonnement fra Trend Micro for en lokal løsning, kan du bruge det til at beskytte din Azure virtuelle maskiner. Hvis du ikke er en kunde endnu, kan du tilmelde dig til et prøveabonnement. Du kan finde flere oplysninger om denne løsning, kan du se Trend Micro blogindlægget [Microsoft Azure VM Agent lokalnummer til Deep sikkerhed](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installere den Deep Agent for sikkerhed på en ny VM

[Azure klassisk portal](http://manage.windowsazure.com) kan du installere VM Agent og filtypenavnet Trend Micro sikkerhed, når du bruger indstillingen **Fra galleriet** til at oprette den virtuelle maskine. Hvis du opretter en enkelt virtuel maskine, er ved hjælp af portalen en nem måde at føje beskyttelse fra Trend Micro.

Denne indstilling for **Fra galleriet** åbnes en guide, der hjælper med at konfigurere den virtuelle maskine. Du kan bruge den sidste side i guiden til at installere VM Agent og Trend Micro sikkerhed filtypenavn. Se [oprette en virtuel maskine kører Windows Azure klassisk portalen](virtual-machines-windows-classic-tutorial.md)generelle instruktioner. Når du får til den sidste side i guiden skal du gøre følgende:

1.  Se **Installere VM Agent**under **VM Agent**.

2.  Markér **Tendens Micro Deep sikkerhed Agent**under **Sikkerhed filtypenavne**.

    ![Installere VM Agent og Deep sikkerhed Agent](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Klik på afkrydsningsfeltet for at oprette den virtuelle maskine.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installere den Deep Agent for sikkerhed på en eksisterende VM

Hvis du vil installere agenten på en eksisterende VM, skal bruge du følgende:

- Azure PowerShell-modulet, version 0.8.2 eller nyere, skal være installeret på din lokale computer. Du kan kontrollere versionen af Azure PowerShell, du har installeret ved hjælp af den **Get-modul azure | Formater tabel version** kommandoen. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)instruktioner og et link til den nyeste version. Log på din Azure abonnement ved hjælp af `Add-AzureAccount`.

- Den VM Agent er installeret på en målliste virtuel maskine.

Først skal kontrollere, VM Agent allerede er installeret. Skriv navnet til skyen tjenesten og virtuelt navn og derefter køre følgende kommandoer på en administrator niveau Azure PowerShell kommandoprompt. Erstatte alt i anførselstegn, herunder den < og > tegn.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Hvis du ikke kender de skybaseret tjeneste og virtuelle computernavn, Kør **Get-AzureVM** for at få vist disse oplysninger til de virtuelle maskiner i dit aktuelle abonnement.

Hvis kommandoen **Skriv host** returnerer **Sand**, er VM Agent installeret. Hvis den returnerer **Falsk**, kan du se vejledningen og et link til download i Azure blog, skrive [VM Agent og filtypenavne - del 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Hvis VM Agent er installeret, skal du køre disse kommandoer.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Næste trin

Det tager et par minutter for agent at begynde at køre, når den er installeret. Herefter skal du aktivere Deep sikkerhed på den virtuelle maskine, så den kan administreres af en Deep sikkerhed-Manager. Se følgende for at få yderligere instruktioner:

- Tendenss artikel om denne løsning, [Instant-On skyen sikkerhed i forbindelse med Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Et [Eksempel på Windows PowerShell-script](http://go.microsoft.com/fwlink/?LinkId=404100) til at konfigurere den virtuelle maskine
- [Instruktioner](http://go.microsoft.com/fwlink/?LinkId=404099) til stikprøvernes

## <a name="additional-resources"></a>Yderligere ressourcer

[Sådan logger en virtuel maskine, der kører Windows Server]

[Azure VM Extensions og funktioner]


<!--Link references-->
[Sådan logger en virtuel maskine, der kører Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Azure VM Extensions og funktioner]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
