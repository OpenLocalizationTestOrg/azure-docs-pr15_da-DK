<properties
    pageTitle="Nulstil adgangskode eller Fjernskrivebord konfiguration på en Windows-VM | Microsoft Azure"
    description="Få mere at vide, hvordan du nulstiller en kontoadgangskode eller Remote Desktop services på en Windows-VM ved hjælp af Azure portal eller Azure PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Sådan nulstilles tjenesten Remote Desktop eller dens logonadgangskode i en Windows-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Hvis du ikke oprette forbindelse til en Windows virtuel maskine (VM), kan du nulstille den lokale administratoradgangskode eller nulstille tjenestekonfiguration Fjernskrivebord. Du kan bruge enten portalen Azure eller filtypenavnet VM Access i Azure PowerShell til at nulstille adgangskoden. Hvis du bruger PowerShell, skal du kontrollere, du har det seneste PowerShell-modulet, der er installeret på computeren arbejde og er logget på abonnementet Azure. Læs, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)have en detaljeret vejledning.

> [AZURE.TIP] Du kan kontrollere versionen af PowerShell, du har installeret ved hjælp af`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Windows FOS i ressourcestyring implementeringsmodel

### <a name="azure-portal"></a>Azure-portalen
Vælg din VM ved at klikke på **Gennemse** > **virtuelle maskiner** > *din Windows virtuelle maskine* > **alle indstillinger** > **Nulstil adgangskode**. Bladet adgangskode Nulstil vises:

![Siden til nulstilling af adgangskode](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Indtast brugernavnet og en ny adgangskode, og klik derefter på **Gem**. Prøv at oprette forbindelse til din VM igen.

### <a name="vmaccess-extension-and-powershell"></a>VMAccess filtypenavn og PowerShell

Sørg for, at du har Azure PowerShell 1.0 eller nyere installeret, og du har logget på din konto ved hjælp af den `Login-AzureRmAccount` cmdlet.

#### <a name="reset-the-local-administrator-account-password"></a>**Nulstille adgangskoden for lokale administratorkontoen**

Du kan nulstille din adgangskode eller bruger administratornavn ved hjælp af kommandoen [Sæt AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell.

Oprette administratoren lokale legitimationsoplysninger ved hjælp af følgende kommando:

    $cred=Get-Credential

Hvis du skriver et andet navn end den aktuelle konto, følgende kommando for VMAccess lokalnummer omdøber den lokale administratorkonto, tildeler adgangskoden til kontoen, og udsteder hændelsen Fjernskrivebord Log af. Hvis den lokale administratorkonto er deaktiveret, filtypenavnet VMAccess gør det muligt.

Brug filtypenavnet VM access til at angive de nye legitimationsoplysninger på følgende måde:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Erstatte `myRG`, `myVM`, `myVMAccess`, og en placering med værdier, der er relevant for din konfiguration.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Nulstil tjenestekonfiguration Fjernskrivebord**

Du kan nulstille fjernadgang til dine VM ved hjælp af [Sæt AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) eller [Angive AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), på følgende måde. (Erstatte den `myRG`, `myVM`, `myVMAccess` og en placering med dine egne værdier.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Eller:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Begge kommandoer føje en ny navngivet VM access agent til den virtuelle maskine. Når som helst, kan en VM har kun en enkelt VM access agent. For at angive VM access agentegenskaber korrekt, skal du fjerne den tidligere angivet ved hjælp af en agent for access `Remove-AzureRmVMAccessExtension` eller `Remove-AzureRmVMExtension`. Start fra Azure PowerShell version 1.2.2, og du kan undgå dette trin ved brug af `Set-AzureRmVMExtension` med en `-ForceRerun` indstilling. Når du bruger `-ForceRerun`, Sørg for at bruge det samme navn for VM access agent som angivet af den forrige kommando.

Hvis du stadig ikke oprette forbindelse fra en fjernplacering til din virtuelle maskine, kan du se yderligere trin for at prøve på [fejlfinding i forbindelse med Fjernskrivebord forbindelser til en Windows-baseret Azure virtuel maskine](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Windows FOS i modellen Klassisk installation

### <a name="azure-portal"></a>Azure-portalen

Du kan bruge [Azure-portalen](https://portal.azure.com) til virtuelle maskiner, der er oprettet ved hjælp af den klassiske implementeringsmodel, for at nulstille tjenesten Fjernskrivebord. Klik på: **Gennemse** > **virtuelle maskiner (klassisk)** > *din Windows virtuelle maskine* > **Nulstille Remote …**. Den efterfølgende side vises.

![Nulstille RDP konfigurationssiden](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Du kan også prøve nulstilling af brugernavn og adgangskode til den lokale administratorkonto. Klik på: **Gennemse** > **virtuelle maskiner (klassisk)** > *din Windows virtuelle maskine* > **alle indstillinger** > **Nulstil adgangskode**. Den efterfølgende side vises.

![Siden til nulstilling af adgangskode](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Når du angiver den nye brugernavn og adgangskode, kan du klikke på **Gem**.

### <a name="vmaccess-extension-and-powershell"></a>VMAccess filtypenavn og PowerShell

Sørg for, at VM Agent er installeret på den virtuelle maskine. Filtypenavnet VMAccess behøver ikke at være installeret, før du kan bruge den, så længe VM Agent er tilgængelig. Kontrollér, at VM Agent allerede er installeret ved hjælp af følgende kommando. (Erstatte "myCloudService" og "myVM" efter navnene på dine skytjeneste og din VM henholdsvis. Du kan lære disse navne ved at køre `Get-AzureVM` uden parametre.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Hvis kommandoen **Skriv host** viser **Sand**, er VM Agent installeret. Hvis det viser **Falsk**, kan du se vejledningen og et link til download i [VM Agent og udvidelser - del 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure blogindlægget.

Hvis du har oprettet den virtuelle maskine ved hjælp af portalen, se om `$vm.GetInstance().ProvisionGuestAgent` returnerer **True**. Hvis ikke, kan du konfigurere den ved hjælp af denne kommando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Denne kommando forhindrer følgende fejl, når du kører kommandoen **Sæt AzureVMExtension** i de næste trin: "Klargøring gæst Agent skal være aktiveret for objektet VM før du angiver IaaS VM Access filtypenavn."

#### <a name="reset-the-local-administrator-account-password"></a>**Nulstille adgangskoden for lokale administratorkontoen**

Oprette en logon-legitimationsoplysninger med navnet på den aktuelle lokale administrator-konto og en ny adgangskode, og derefter køre den `Set-AzureVMAccessExtension` på følgende måde.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Hvis du skriver et andet navn end den aktuelle konto, filtypenavnet VMAccess omdøber den lokale administratorkonto, tildeler adgangskoden til kontoen, og udsteder et hvor Fjernskrivebord. Hvis den lokale administratorkonto er deaktiveret, filtypenavnet VMAccess gør det muligt.

Disse kommandoer også nulstille tjenestekonfiguration Fjernskrivebord.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Nulstil tjenestekonfiguration Fjernskrivebord**

Hvis du vil nulstille tjenestekonfiguration Fjernskrivebord, skal du køre følgende kommando:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

Filtypenavnet VMAccess kører to kommandoer på den virtuelle maskine:

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Denne kommando aktiverer den indbyggede Windows Firewall-gruppe, der gør det muligt for indgående Fjernskrivebord trafik, som bruger TCP-port 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Denne kommando angiver fDenyTSConnections værdi i registreringsdatabasen til 0, aktivere Fjernskrivebord-forbindelser.


## <a name="next-steps"></a>Næste trin

Hvis filtypenavnet Azure VM access svarer ikke, og du kan ikke nulstille adgangskoden, kan du [nulstille den lokale Windows-adgangskode offline](virtual-machines-windows-reset-local-password-without-agent.md). Denne metode er en mere avanceret proces og kræver, at du skal oprettes forbindelse den virtuelle harddisk problematisk VM til en anden VM. Følg trinnene beskrevet i denne artikel først, og kun forsøg metoden offline adgangskode reset som endelig.

[Azure VM extensions og funktioner](virtual-machines-windows-extensions-features.md)

[Oprette forbindelse til en Azure virtuelt med RDP eller SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Foretage fejlfinding af Fjernskrivebord forbindelser til en Windows-baseret Azure virtuel maskine](virtual-machines-windows-troubleshoot-rdp-connection.md)
