<properties
    pageTitle="Installere skabeloner med PowerShell Azure stablede | Microsoft Azure"
    description="Få mere at vide, hvordan du kan installere en virtuel maskine, ved hjælp af en ressourcestyring skabelon og et PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Installere skabeloner Azure stablede ved hjælp af PowerShell

Bruge PowerShell til at installere Azure ressourcestyring skabeloner til Azure stak Konceptet.  Ressourcestyring skabeloner implementere og klargøre alle de ressourcer for dit program i en enkelt, koordineret handling.

## <a name="run-azurerm-powershell-cmdlets"></a>Køre AzureRM PowerShell-cmdlet'er

I dette eksempel skal køre du et script for at installere en virtuel maskine til Azure stak Konceptet ved hjælp af en ressourcestyring skabelon.  Før du fortsætter, sikre, at du har [installeret og konfigureret PowerShell](azure-stack-connect-powershell.md)  

Den virtuelle harddisk bruges i dette eksempelskabelon er et standardbillede marketplace (WindowsServer-2012-R2-Datacenter).

1.  Gå til <http://aka.ms/AzureStackGitHub>, søge efter **101-enkle-windows-vm** skabelonen, og Gem den til følgende placering: c:\\skabeloner\\azuredeploy-101-enkle-windows-vm.json.

2.  Kør følgende installation script i PowerShell. Erstat *brugernavn* og *din adgangskode* med dit brugernavn og din adgangskode. Øge værdien for parameteren *$myNum* til at undgå at overskrive din installation på efterfølgende bruger.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Åbn Azure stak-portalen, klik på **Gennemse**, klik på **virtuelle maskiner**, og se efter din nye virtuelle maskine (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Eksempel på video: virtuelt hybridinstallation

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Næste trin

[Installere skabeloner med Visual Studio](azure-stack-deploy-template-visual-studio.md)
