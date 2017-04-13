<properties
   pageTitle="Ønsket stat konfiguration for Azure oversigt | Microsoft Azure"
   description="Oversigt til brug af Microsoft Azure lokalnummer handler for PowerShell beskedteksten tilstand konfiguration. Herunder forudsætninger, arkitektur, cmdletter.."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion til Azure beskedteksten tilstand konfiguration lokalnummer handler #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure VM Agent og tilknyttede filtypenavne er en del af Microsoft Azure-infrastrukturtjenester. VM udvidelser er softwarekomponenter, der udvider funktionaliteten VM og forenkle forskellige VM management handlinger. For eksempel filtypenavnet VMAccess kan bruges til at nulstille en adgangskode, eller filtypenavnet brugerdefineret Script kan bruges til at udføre et script på VM.

I denne artikel introducerer filtypenavnet PowerShell beskedteksten tilstand konfiguration (DTK) til Azure FOS som en del af Azure PowerShell SDK. Du kan bruge nye cmdletter for at overføre og anvende en PowerShell DTK konfiguration på en Azure VM aktiveret med filtypenavnet PowerShell DTK. PowerShell DTK lokalnummer opkald til PowerShell DTK at træffe konfigurationen af den modtagne DTK på VM. Denne funktionalitet er også tilgængelig via Azure-portalen.

## <a name="prerequisites"></a>Forudsætninger ##
**Lokal computer** For at interagere med filtypenavnet Azure VM, skal du bruge enten portalen Azure eller Azure PowerShell SDK. 

**Agent for gæst** Azure VM, der skal konfigureres ved DTK konfigurationen skal være en OS, der understøtter enten Windows Management Framework (WMF) 4.0 eller 5.0. Den komplette liste over understøttede OS-versioner kan findes på [DTK lokalnummer versionshistorik](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Ord og begreber ##
Denne vejledning forudsætter kendskab til følgende begreber:

Konfiguration – et DTK konfigurationsdokument. 

Node - et mål for en DTK konfiguration. I dette dokument refererer "node" altid til en Azure VM.

Konfigurationsdata - en .psd1 fil, som indeholder påvirkning data til en konfiguration

## <a name="architectural-overview"></a>Oversigt over arkitekturen ##

Filtypenavnet Azure DTK bruger Azure VM Agent framework til at levere, træffe og rapportere på DTK konfigurationer, der kører på Azure FOS. Filtypenavnet DTK forventer en .zip-fil, der indeholder mindst et konfigurationsdokument og et sæt af parametre, der leveres via Azure PowerShell SDK eller via portalen Azure.

Når filtypenavnet kaldes for første gang, kører en installationsprocessen. Denne proces installerer en version af den Windows Management Framework (WMF) ved hjælp af følgende logik:

1. Hvis Azure VM Operativsystemet er Windows Server 2016, ikke foretages nogen handlinger. Windows Server 2016 har allerede den nyeste version af PowerShell, der er installeret.
2. Hvis den `wmfVersion` egenskab er angivet, versionen af WMF er installeret, medmindre den ikke er kompatibel med den VM OS.
3. Hvis ingen `wmfVersion` egenskab er angivet, den seneste gældende version af WMF er installeret.

Installation af WMF kræver en genstart. Efter genstart, overførsler filtypenavnet .zip-filen, der er angivet i den `modulesUrl` egenskab. Hvis denne placering er i Azure blob-lager, der kan angives et SAS token i den `sasToken` egenskab til at få adgang til filen. Når .zip er hentet og pakkes ud, funktionen konfiguration defineret i `configurationFunction` kører for at generere MOF-filen. Derefter køres filtypenavnet `Start-DscConfiguration -Force` på genererede MOF-filen. Filtypenavnet registrerer output og skriver det tilbage til Azure Status kanalen. DTK mindste.fælles.MULTIPLUM håndterer fra nu af, overvågning og korrektion som normalt. 

## <a name="powershell-cmdlets"></a>PowerShell-cmdlet'er ##

PowerShell-cmdlet'er kan bruges med ARM eller ASM til at pakke, udgive og overvåge DTK lokalnummer installationer. Følgende cmdletter angivet er ASM moduler, men kan erstattes "Azure" med "AzureRm" for at kunne bruge ARM-modellen. For eksempel `Publish-AzureVMDscConfiguration` bruger ASM, hvor `Publish-AzureRmVMDscConfiguration` bruger ARM. 

`Publish-AzureVMDscConfiguration`tager i en konfigurationsfil, søger efter afhængige DTK ressourcer og opretter en .zip-fil, der indeholder de konfiguration og DTK ressourcer, der er behov for at træffe konfigurationen. Det kan også oprette pakken lokalt ved hjælp af den `-ConfigurationArchivePath` parameter. Ellers skal publiceres .zip-filen til Azure blob-lager og sikre dem med et SAS token.

Oprettet af denne cmdlet .zip-filen indeholder scriptet til .ps1 konfiguration i roden af arkivmappen. Ressourcer har mappen modul, der er placeret i arkivmappen. 

`Set-AzureVMDscExtension`fra serveren de nødvendige ved filtypenavnet PowerShell DTK til et objekt VM konfiguration, som kan anvendes til en Azure VM med indstillinger `Update-AzureVM`.

`Get-AzureVMDscExtension`Henter DTK lokalnummer status for en bestemt VM. 

`Get-AzureVMDscExtensionStatus`Henter status for DTK konfigurationen vedtaget af DTK lokalnummer handler. Denne handling kan udføres på en enkelt VM eller gruppe af FOS.

`Remove-AzureVMDscExtension`fjerner lokalnummer handler fra en bestemt virtuel maskine. Denne cmdlet betyder **ikke** at fjerne konfigurationen, fjerne WMF eller ændre indstillingerne for anvendt på den virtuelle maskine. Det fjerner kun lokalnummer handler. 

**Vigtige forskelle i ASM og ARM cmdletter**

- ARM-cmdletter er synkron. ASM cmdletter er asynkron.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version og placering er alle nye obligatoriske parametre.
- ArchiveResourceGroupName er en ny valgfri parameter for ARM. Når kontoen lagerplads hører til en anden ressourcegruppe end, hvor den virtuelle maskine er oprettet, kan du angive denne parameter.
- ConfigurationArchive hedder ArchiveBlobName i ARM
- ContainerName hedder ArchiveContainerName i ARM
- StorageEndpointSuffix hedder ArchiveStorageEndpointSuffix i ARM
- Indstillingen Automatiske opdateringer er blevet føjet til ARM til at aktivere automatisk opdatering af lokalnummer handleren til den nyeste version som, og når den er tilgængelig. Denne parameter har kan forårsage indholdsindeksserver genstarter på VM, når en ny version af WMF frigives. 


## <a name="azure-portal-functionality"></a>Azure portalen funktionalitet ##
Gå til en klassisk VM. Under Indstillinger -> generelle Klik på "Udvidelser." Der oprettes en ny rude. Klik på "Tilføj", og vælg PowerShell DTK.

På portalen skal input.
**Konfiguration af moduler eller Script**: Dette felt er obligatorisk. Kræver en .ps1-fil, der indeholder et script til konfiguration eller en .zip-fil med et script til konfiguration af .ps1 i roden og alle efterfølgende ressourcer i modulet mapper i .zip. Det kan oprettes med den `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet, der er inkluderet i Azure PowerShell SDK. .Zip-filen er overført til dit sikret med et SAS token bruger blob-lager. 

**Data PSD1 konfigurationsfil**: Dette felt er valgfrit. Hvis din konfiguration kræver en konfigurationsfil data i .psd1, bruge dette felt til at markere den og overføre den til din bruger blob-lager, hvor det er sikret med et SAS token. 
 
**Module-Qualified navn konfiguration af**: .ps1 filer kan have flere konfiguration funktioner. Skriv navnet på scriptet til konfiguration .ps1 efterfulgt af en '\' og navnet på funktionen konfiguration. Eksempelvis hvis scriptet .ps1 har navnet "configuration.ps1", og konfigurationen er "IisInstall", skal du angive:`configuration.ps1\IisInstall`

**Konfiguration af argumenter**: Hvis funktionen konfiguration tager argumenter, kan du angive dem i her i formatet `argumentName1=value1,argumentName2=value2`. Bemærk dette format er et andet format end hvordan konfiguration argumenter accepteres via PowerShell-cmdlet'er eller Ressourcestyring skabeloner. 

## <a name="getting-started"></a>Kom godt i gang ##

Filtypenavnet Azure DTK i DTK konfigurationsdokumenter og særlige tilsynsforskrifter dem på Azure FOS. Et eksempel på en konfiguration følger. Gemme den lokalt som "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Følgende trin placere scriptet IisInstall.ps1 på den angivne VM, udføre konfigurationen og rapportere tilbage på status.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Logføring ##

Logfilerne er placeret i:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[versionsnummeret]

## <a name="next-steps"></a>Næste trin ##

Du kan finde flere oplysninger om PowerShell DTK, [ved at besøge PowerShell dokumentation center](https://msdn.microsoft.com/powershell/dsc/overview). 

Undersøge [Azure ressourcestyring skabelon til filtypenavnet DTK](virtual-machines-windows-extensions-dsc-template.md
). 

Du kan finde yderligere funktioner kan du administrere med PowerShell DTK, [Gennemse galleriet PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) efter flere DTK ressourcer.

Du kan finde oplysninger om at overføre følsomme parametre i konfigurationer, [Administrer legitimationsoplysninger sikkert med DTK lokalnummer handler](virtual-machines-windows-extensions-dsc-credentials.md).
