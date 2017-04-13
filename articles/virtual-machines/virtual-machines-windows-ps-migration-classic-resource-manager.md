<properties
    pageTitle="Overføre til Ressourcestyring med PowerShell | Microsoft Azure"
    description="I denne artikel indeholder en gennemgang af overførslen platform understøttes af IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure PowerShell-kommandoer"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure PowerShell

Disse trin viser dig, hvordan du bruger Azure PowerShell-kommandoer til at overføre infrastruktur som en tjeneste (IaaS) ressourcer fra den klassiske implementeringsmodel til implementeringsmodel Azure ressourcestyring. 

Hvis du vil, kan du også overføre ressourcer ved hjælp af [Azure Command Line Interface (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Se [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager.md)få baggrundsoplysninger om understøttede overførselsscenarier. 
- Se [tekniske undersøgelse på platform understøttes overførsel fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)til detaljeret vejledning og en gennemgang for overførsel.

## <a name="step-1-plan-for-migration"></a>Trin 1: Planlægge i forhold til overførsel

Her er nogle få bedste fremgangsmåder, som vi anbefaler, at når du evaluerer overføre IaaS ressourcer fra klassisk til Ressourcestyring:

- Læs via [understøttede og ikke-understøttede funktioner og konfigurationer](virtual-machines-windows-migration-classic-resource-manager.md). Hvis du har virtuelle maskiner, der bruger ikke-understøttede konfigurationer eller funktioner, anbefaler vi, at du venter på understøttelse af konfiguration/funktioner til meddeles. Du kan også hvis den passer til dine behov, fjerne denne funktion eller flytte fra denne konfiguration til at aktivere overførsel.
-   Hvis du har automatiseret scripts, der implementerer din infrastruktur og programmer i dag, du forsøge at oprette et lignende test konfigurationen ved hjælp af disse scripts til overførsel. Alternativt kan du konfigurere eksempel miljøer ved hjælp af portalen Azure.

>[AZURE.IMPORTANT]Virtuelt netværk gateways (-til-websted, Azure ExpressRoute, programmet gateway-, punkt-til-websted), understøttes ikke i øjeblikket for overførsel fra klassisk til Ressourcestyring. Fjerne gatewayen for at overføre et klassisk virtuelt netværk med en gateway, før du kører en allokeringen for at flytte netværket (du kan køre trinnet Forbered uden at slette gatewayen). Når du har fuldført overførslen, Genopret gatewayen i Azure ressourcestyring.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Trin 2: Installer den seneste version af Azure PowerShell

Der er to primære muligheder for at installere Azure PowerShell: [PowerShell galleriet](https://www.powershellgallery.com/profiles/azure-sdk/) eller [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI modtager månedlige opdateringer. Galleri med PowerShell modtager opdateringer løbende. I denne artikel er baseret på Azure PowerShell version 2.1.0.

Installationsvejledning, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Trin 3: Angive dit abonnement og tilmelde sig til overførsel

Først skal starte en PowerShell-prompt. For overførslen, du har brug at konfigurere dit miljø til begge klassisk og ressourcestyring.

Log på din konto til Ressourcestyring-modellen.

```powershell
    Login-AzureRmAccount
```

Få de tilgængelige abonnementer ved hjælp af følgende kommando:

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Angive abonnementet Azure for den aktuelle session. I dette eksempel angiver abonnement standardnavnet til **Mine Azure-abonnement**. Erstat Abonnementsnavnet eksempel med din egen. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] Registrering er et enkelt trin, men du skal gøre det én gang før du forsøger overførsel. Uden at registrere, du får vist følgende fejlmeddelelse: 

>   *BadRequest: Abonnement er ikke registreret for overførsel.* 

Registrere med provideren overførsel ressource ved hjælp af følgende kommando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vent fem minutter for tilmelding til at afslutte. Du kan kontrollere status for godkendelse ved hjælp af følgende kommando:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Sørg for, at RegistrationState er `Registered` før du fortsætter. 

Log nu på din konto til klassisk-modellen.

```powershell
    Add-AzureAccount
```

Få de tilgængelige abonnementer ved hjælp af følgende kommando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Angive abonnementet Azure for den aktuelle session. I dette eksempel angiver standard-abonnement til **Mine Azure-abonnement**. Erstat Abonnementsnavnet eksempel med din egen. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Trin 4: Sørg for, at du har tilstrækkelig Azure ressourcestyring Virtual Machine kerner i det Azure område for din aktuelle installation eller VNET

Du kan bruge følgende PowerShell-kommando til at kontrollere det aktuelle antal kerner, du har i Azure ressourcestyring. Hvis du vil vide mere om core kvoter, se [grænser og ressourcestyring Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

I dette eksempel kontrollerer tilgængelighed i området **Vest USA** . Erstat område eksempelnavn med dine egne. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Trin 5: Kør kommandoer til at overføre dine IaaS ressourcer

>[AZURE.NOTE] Alle de handlinger, der er beskrevet her er idempotent. Hvis du har et problem end ikke-understøttede funktioner eller en konfigurationsfejl, anbefaler vi, at du prøv igen på Forbered afbrydelse eller bekræftelse handling. Platformen forsøger derefter handlingen igen.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Overføre virtuelle maskiner i en skybaseret tjeneste (ikke i et virtuelt netværk)

Hente en liste over skytjenester ved hjælp af følgende kommando, og vælg derefter den skybaseret tjeneste, du vil overføre. Hvis FOS i skytjenesten er i et virtuelt netværk, eller hvis de har web eller arbejder roller, returnerer kommandoen en fejlmeddelelse.

```powershell
    Get-AzureService | ft Servicename
```

Få installation navn til skytjenesten. I dette eksempel er navnet på tjenesten **Min tjeneste**. Erstat navnet på tjenesten eksempel med tjenestenavnet på din egen. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Forberede virtuelle maskiner i skytjenesten for overførsel. Du har to muligheder for at vælge mellem.

* **Indstilling 1. Overføre FOS til et platform oprettet virtuelt netværk**

    Validere først, hvis du kan overføre skytjenesten ved hjælp af følgende kommandoer:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Kommandoen foregående viser alle advarsler og fejl, der blokerer for overførsel. Hvis datavalidering lykkes, og du kan gå videre til **Forbered** trinnet:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Mulighed 2. Overføre til et eksisterende virtuelt netværk i implementeringsmodel ressourcestyring**

    I dette eksempel angiver ressource gruppenavn til **myResourceGroup**, navnet på det virtuelle netværk til **myVirtualNetwork** og undernet navnet til **mySubNet**. Erstatte navnene i eksemplet med navnene på dine egne ressourcer.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Validere først, hvis du kan overføre skytjenesten ved hjælp af følgende kommando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Kommandoen foregående viser alle advarsler og fejl, der blokerer for overførsel. Hvis datavalidering er gået igennem, kan du fortsætte med følgende Forbered trin:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Når handlingen Forbered er oprettet med en af de foregående indstillinger, kan du forespørge tilstanden overførsel af FOS. Sørg for, at de er i den `Prepared` tilstand.

I dette eksempel angiver VM navnet til **myVM**. Erstat eksempelnavnet med din egen VM navn.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Kontrollere konfigurationen for de ressourcer, der er udarbejdet ved hjælp af PowerShell eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Overføre virtuelle maskiner i et virtuelt netværk

Hvis du vil overføre virtuelle maskiner i et virtuelt netværk, kan du overføre netværket. Automatisk overføre virtuelle maskiner med netværket. Vælg det virtuelle netværk, du vil overføre. 

I dette eksempel angiver navnet på det virtuelle netværk til **myVnet**. Erstat virtuelt netværk eksempelnavn med dine egne. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Hvis det virtuelle netværk indeholder internettet eller arbejder roller eller FOS med ikke-understøttede konfigurationer, får du en fejl Valideringsmeddelelse.

Validere først, hvis du kan overføre det virtuelle netværk ved hjælp af følgende kommando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Kommandoen foregående viser alle advarsler og fejl, der blokerer for overførsel. Hvis datavalidering er gået igennem, kan du fortsætte med følgende Forbered trin:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrollere konfigurationen for forberedt virtuelle maskiner ved hjælp af Azure PowerShell eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Overføre en lagerplads konto

Når du er færdig med overføre virtuelle maskiner, anbefales det du overfører kontiene lagerplads.

Forberede hver lagerplads konto til overflytning ved hjælp af følgende kommando. I dette eksempel er kontonavn lager **myStorageAccount**. Erstat eksempelnavnet med navnet på din egen lagerplads konto. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Kontrollere konfigurationen for kontoen forberedt lagerplads ved hjælp af Azure PowerShell eller Azure portalen. Hvis du ikke er klar til overførsel, og du vil gå tilbage til den gamle tilstand, skal du bruge følgende kommando:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Hvis forberedt konfigurationen, kan du flytte fremad og Bekræft ressourcerne ved hjælp af følgende kommando:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Næste trin

- Du kan finde yderligere oplysninger om overførsel [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager.md).
- For at overføre flere netværksressourcer til Ressourcestyring ved hjælp af Azure PowerShell kan du bruge lignende trin med [Flyt AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Flyt AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)og [Flyt AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
- Se [Community værktøjerne til overførsel til Azure ressourcestyring overførsel](virtual-machines-windows-migration-scripts.md) for open source-scripts, som du kan bruge til at overføre Azure ressourcer fra klassisk til Ressourcestyring
