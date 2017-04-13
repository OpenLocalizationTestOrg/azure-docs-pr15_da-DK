<properties
    pageTitle="Beskytte-servere at Azure ved hjælp af PowerShell Azure med Azure ressourcestyring | Microsoft Azure"
    description="Automatisere beskyttelse af servere til Azure med Azure gendannelse af websteder ved hjælp af PowerShell og Azure ressourcestyring."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Gentage mellem lokale Hyper-V virtuelle maskiner og Azure ved hjælp af PowerShell og Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - ressourcestyring](site-recovery-deploy-with-powershell-resource-manager.md)
- [Klassisk Portal](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Oversigt

Azure gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner i en række installationsscenarier. Du kan finde en komplet liste over installationsscenarier i [Oversigt over Azure gendannelse af websteder](site-recovery-overview.md).

Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure via Windows PowerShell. Det kan bruges sammen med to typer moduler: modulet Azure profil eller modulet Azure ressourcestyring.

Websted gendannelse PowerShell-cmdlet'er, tilgængelige med Azure PowerShell til Azure Resource Manager hjælpe dig med at beskytte og gendanne dine servere i Azure.

I denne artikel beskrives, hvordan du bruger Windows PowerShell, sammen med Azure Resource Manager til at installere gendannelse af websteder for at konfigurere og dirigerer server beskyttelse til Azure. Eksemplet bruges i denne artikel viser, hvordan du beskytter, mislykkes og gendanne virtuelle maskiner på en Hyper-V vært til Azure, ved hjælp af PowerShell Azure med Azure ressourcestyring.

> [AZURE.NOTE] Websted gendannelse PowerShell-cmdletter i øjeblikket giver dig mulighed at konfigurere følgende: Virtual Machine Manager-websteder til en anden, et virtuelt Manager websted til Azure og et Hyper-V-websted til Azure.

Du behøver ikke at være en ekspert PowerShell til at bruge denne artikel, men du har brug for at forstå de grundlæggende begreber, som moduler, cmdletter og sessioner. Du kan finde flere oplysninger om Windows PowerShell, kan du se [Komme i gang med Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Du kan også læse mere om [Brug af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Microsoft-partnere, der er en del af programmet skyen løsning CSP'EN (Provider) kan konfigurere og administrere beskyttelse af deres kunders servere til deres kunders respektive CSP abonnementer (lejer abonnementer).

## <a name="before-you-start"></a>Før du starter

Sørg for, at du har disse forudsætninger sted:

- En [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Desuden kan du læse om [Azure gendannelse webstedsadministrator priser](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Finde oplysninger om denne version, og hvordan du installerer programmet, [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) og [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) moduler. Du kan finde de nyeste versioner af disse moduler via [PowerShell-galleri](https://www.powershellgallery.com/)

I denne artikel viser, hvordan du bruger Azure Powershell med Azure ressourcestyring til at konfigurere og administrere beskyttelse af dine servere. Eksemplet bruges i denne artikel beskrives, hvordan du beskytter en virtuel maskine, der kører på en Hyper-V vært til Azure. De følgende forudsætninger er specifikke for dette eksempel. Se dokumentationen for gruppepolitikken pågældende scenarie for et mere omfattende sæt af krav til de forskellige scenarier gendannelse af websteder.

- En Hyper-V-vært, der kører Windows Server 2012 R2 eller Microsoft Hyper-V Server 2012 R2, der indeholder en eller flere virtuelle computere.
- Hyper-V servere forbindelse til internettet, skal du enten direkte eller via en proxy.
- De virtuelle maskiner, du vil beskytte skal være i overensstemmelse med [virtuelt forudsætninger](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Trin 1: Log på din Azure-konto


1. Åbn en PowerShell-konsollen og kører denne kommando til at logge på din Azure-konto. Cmdletten viser en webside, bliver du bedt om dine legitimationsoplysninger.

        Login-AzureRmAccount

    Alternativt kan du også kan medtage legitimationsoplysningerne konto som en parameter til den `Login-AzureRmAccount` cmdlet ved hjælp af den `-Credential` parameter.

    Hvis du er CSP partner arbejde på vegne af en lejer, skal du angive kunden som en lejer, ved hjælp af deres primære domænenavn tenantID eller lejer.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Et firma kan indeholde flere abonnementer, så du skal knytte det abonnement, du vil bruge til kontoen.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Kontrollere, at abonnementet er registreret til at bruge udbyderne Azure til gendannelse tjenesterne og gendannelse af websteder, ved hjælp af følgende kommandoer:

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    I outputtet af disse kommandoer, hvis **RegistrationState** er indstillet til **registreret**, kan du fortsætte til trin 2. Hvis ikke, skal du registrere den manglende udbyder i dit abonnement.

    For at registrere provideren Azure for gendannelse af websteder og tjenester til genoprettelse skal du køre følgende kommandoer:

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Kontrollér, at udbyderne, der blev registreret ved hjælp af følgende kommandoer: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` og `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Trin 2: Konfigurer gendannelse Services samling

1. Opret en Azure ressourcestyring ressourcegruppe, hvor du skal oprette samling af legitimationsoplysninger, eller brug en eksisterende ressourcegruppe. Du kan oprette en ny ressourcegruppe ved hjælp af følgende kommando:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    hvor variablen $ResourceGroupName indeholder navnet på den ressourcegruppe, du vil oprette, og variablen $Geo indeholder det Azure område, hvor du kan oprette ressourcegruppe (eksempelvis "Brasilien Syd").

    Du kan få en liste over grupper i dit abonnement ved hjælp af den `Get-AzureRmResourceGroup` cmdlet.

2. Oprette en ny Azure gendannelse Services-samling på følgende måde:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Du kan hente en liste over eksisterende vaults ved hjælp af den `Get-AzureRmRecoveryServicesVault` cmdlet.

> [AZURE.NOTE] Hvis du vil udføre handlinger på gendannelse af websteder vaults oprettet ved hjælp af portalen klassisk eller Azure Service Management PowerShell-modulet, kan du hente en liste over sådanne vaults ved hjælp af den `Get-AzureRmSiteRecoveryVault` cmdlet. Du skal oprette en ny gendannelse Services samling af legitimationsoplysninger for alle nye handlinger. De gendannelse af websteder vaults, du har oprettet tidligere er understøttet, men har ikke de nyeste funktioner.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Trin 3: Angive gendannelse Services samling kontekst

1.  Angiv samling konteksten ved at køre følgende kommando:

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Trin 4: Oprette et websted til Hyper-V og Opret en ny samling registreringsnøgle for webstedet.

1. Oprette et nyt Hyper-V-websted på følgende måde:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Denne cmdlet starter en gendannelse af websteder jobstatus for at oprette webstedet, og returnerer et gendannelse af websteder jobstatus objekt. Vent et job skal udføre, og Kontrollér, at jobbet blev fuldført.

    Du kan hente jobobjektet og dermed kontrollere den aktuelle status for jobbet, ved hjælp af cmdlet'en Get-AzureRmSiteRecoveryJob til.
2. Oprette og hente en registreringsnøgle for webstedet på følgende måde:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Kopiere den hentede nøgle til værten for Hyper-V. Du skal bruge tasten til at registrere værten Hyper-V til webstedet.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Trin 5: Installere Azure gendannelse af websteder udbyder og Azure betroet Services bruger på værten Hyper-V

1. Hente installationsprogrammet til den nyeste version af provideren fra [Microsoft](https://aka.ms/downloaddra).

2. Kør installationsprogrammet på værten Hyper-V og i slutningen af installationen fortsætte til trin registrering.

3. Når du bliver bedt om det, give den hentede websted registreringsnøgle og fuldført registrering af værten Hyper-V til webstedet.

4. Kontrollere, at værten Hyper-V er registreret til webstedet, ved hjælp af følgende kommando:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Trin 6: Oprette en politik for gentagelse og knytte den til objektbeholderen beskyttelse

1. Opret en politik for gentagelse på følgende måde:

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Markér det returnerede job for at sikre, at oprettelsen af replikering politik lykkes.

    >[AZURE.IMPORTANT] Den angivne lagerplads konto skal være i samme Azure område som din samling af legitimationsoplysninger gendannelsestjenester og bør have geografisk replikering aktiveret.
    >
    > - Hvis den angivne gendannelse lagerplads konto er af typen Azure-lager (klassisk), gendanne failover af de beskyttede maskiner machine til Azure IaaS (klassisk).
    > - Hvis den angivne gendannelse lagerplads konto er af typen Azure-lager (Azure Resource Manager), gendanne failover af de beskyttede maskiner machine til Azure IaaS (Azure Resource Manager).

2. Få objektbeholderen beskyttelse svarer til webstedet, på følgende måde:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Starte tilknytningen af objektbeholderen beskyttelse med politikken replikering, på følgende måde:

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Vent på kørslen tilknytning til at fuldføre, og Sørg for, at den blev fuldført.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Trin 7: Aktivér beskyttelse af virtuelle maskiner

1. Få objektet beskyttelse svarer til den VM, du vil beskytte, på følgende måde:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Begynde at beskytte den virtuelle maskine, som følger:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] Den angivne lagerplads konto skal være i samme Azure område som din samling af legitimationsoplysninger gendannelsestjenester og bør have geografisk replikering aktiveret.
    >
    > - Hvis den angivne gendannelse lagerplads konto er af typen Azure-lager (klassisk), gendanne failover af de beskyttede maskiner machine til Azure IaaS (klassisk).
    > - Hvis den angivne gendannelse lagerplads konto er af typen Azure-lager (Azure Resource Manager), gendanne failover af de beskyttede maskiner machine til Azure IaaS (Azure Resource Manager).

    > Hvis du beskytter VM har mere end én disk, der er knyttet til den, kan du angive operativsystem disken ved hjælp af parameteren *OSDiskName* .

3. Vent virtuelle maskiner når en beskyttede tilstand efter den indledende gentagelse. Det kan tage et stykke tid, afhængigt af faktorer som mængden data til replikeres og den tilgængelige mod strømmen båndbredde til Azure. Jobbet tilstand og StateDescription opdateres, ved at kontakte en beskyttede tilstand VM.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Opdatere gendannelse egenskaber, som VM rolle størrelsen og Azure netværket for at vedhæfte den virtuelle maskine netværkskort til på failover.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Trin 8: Køre en test failover

1. Køre en test failover sag, på følgende måde:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Kontrollér, at testen VM oprettes i Azure. (Test failover jobbet afbrydes midlertidigt, når du har oprettet en test VM i Azure. Jobbet er fuldført ved at rydde op på oprettede skrammer og på genoptage jobbet, som vist i næste trin.)

3. Udføre sekundære test, som følger:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Næste trin

[Læs mere](https://msdn.microsoft.com/library/azure/mt637930.aspx) om Azure gendannelse af websteder med Azure ressourcestyring PowerShell-cmdlet'er.
