<properties
   pageTitle="Overføre legitimationsoplysninger til Azure ved hjælp af DTK | Microsoft Azure"
   description="Oversigt over sikkert overføre legitimationsoplysninger til Azure virtuelle maskiner ved hjælp af PowerShell beskedteksten tilstand konfiguration"
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

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Overføre legitimationsoplysninger til Azure DTK lokalnummer handler #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Denne artikel omhandler beskedteksten tilstand Config-udvidelsen til Azure. En oversigt over DTK lokalnummer handler kan findes i [Introduktion til Azure beskedteksten tilstand konfiguration lokalnummer handler](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Overførsel af legitimationsoplysninger
Som en del af konfigurationsprocessen, du muligvis har brug for til konfiguration af brugerkonti, få adgang til tjenester eller installere et program i en brugerkontekst. Hvis du vil gøre disse ting, skal du angive legitimationsoplysninger. 

DTK giver mulighed for parameteriseret konfigurationer, hvor legitimationsoplysninger overføres til konfigurationen og sikkert gemmes i MOF-filer. Azure lokalnummer Handler forenkler administration af legitimationsoplysninger ved at indsende automatisk administration af certifikater. 

Overvej følgende DTK konfigurationsscript, der opretter en lokal brugerkonto med den angivne adgangskode:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Det er vigtigt at medtage *node localhost* som en del af konfigurationen. Hvis denne erklæring mangler, der følgende trin ikke fungerer som udvidelsen handler specifikt søger efter sætningen node localhost. Det er også vigtigt at medtage typecast *[PsCredential]*, som denne bestemt type udløser filtypenavnet at kryptere legitimationsoplysninger til. 

Publicere dette script til blob-lager:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Opret filtypenavnet Azure DTK, og Angiv legitimationsoplysninger:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Hvordan legitimationsoplysninger er sikret
Kører denne kode beder om en legitimationsoplysninger. Når den er angivet, gemmes i hukommelsen kortvarigt. Når den er blevet publiceret med `Set-AzureVmDscExtension` cmdlet, den overføres via HTTPS til VM, hvor Azure lagrer den krypterede på disk, ved hjælp af den lokale VM certifikat. Det er derefter kortvarigt dekrypteres i hukommelsen og krypteret igen for at sende dem til DTK.

Denne funktionalitet er anderledes end at [bruge secure konfigurationer uden filtypenavn handler](https://msdn.microsoft.com/powershell/dsc/securemof). Azure miljøet giver en metode til at overføre konfigurationsdata sikkert via certifikater. Når du bruger DTK lokalnummer handler, der er ingen grund til at levere $CertificatePath eller en $CertificateID / $Thumbprint post i ConfigurationData.


## <a name="next-steps"></a>Næste trin ##

Se [Introduktion til Azure beskedteksten tilstand konfiguration lokalnummer handler](virtual-machines-windows-extensions-dsc-overview.md)kan finde flere oplysninger om Azure DTK lokalnummer handler. 

Undersøge [Azure ressourcestyring skabelon til filtypenavnet DTK](virtual-machines-windows-extensions-dsc-template.md).

Du kan finde flere oplysninger om PowerShell DTK, [ved at besøge PowerShell dokumentation center](https://msdn.microsoft.com/powershell/dsc/overview). 

Du kan finde yderligere funktioner kan du administrere med PowerShell DTK, [Gennemse galleriet PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) efter flere DTK ressourcer.
