<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer en sekundær VMM-websted ved hjælp af PowerShell (ressourcestyring) | Microsoft Azure"
    description="Beskriver, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af Hyper-V FOS i VMM skyer en sekundær VMM-websted ved hjælp af PowerShell (ressourcestyring)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer en sekundær VMM-websted ved hjælp af PowerShell (ressourcestyring)

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-vmm.md)
- [Klassisk Portal](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil gentage lokale Hyper-V virtuelle maskiner administreres i System Center Virtual Machine Manager (VMM) skyer til en sekundær websted. 

I denne artikel viser dig hvordan du kan bruge PowerShell til at automatisere almindelige opgaver, du skal udføre, når du konfigurerer Azure gendannelse af websteder kan replikeres Hyper-V virtuelle maskiner i System Center VMM skyer til System Center VMM skyer sekundær-webstedet.

I artiklen indeholder forudsætninger for dette scenario, og viser dig 

- Hvordan du konfigurerer en gendannelse Services samling
- Installer Azure websted gendannelse udbyder på VMM kildeserveren og mål VMM server
- Registrere VMM Server(e) i samling af legitimationsoplysninger
- Konfigurere politikken til replikering til VMM skyen. Indstillingerne for gentagelse i politikken anvendes på alle beskyttede virtuelle maskiner 
- Aktivér beskyttelse af virtuelle maskiner. 
- Du kan teste sekundære FOS individuelt eller som en del af en gendannelse plan for at sikre, at alt fungerer som forventet.
- Udføre en planlagt eller en ikke-planlagt failover af FOS individuelt eller som en del af en gendannelse plan for at sikre, at alt fungerer som forventet.

Hvis du støder på problemer med at konfigurere dette scenarie skal du sende dine spørgsmål på [Azure gendannelse Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller. Denne artikel omhandler implementeringsmodel ressourcestyring.



## <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

Her er, hvad du skal bruge i de primære og sekundære lokale websteder til at installere dette scenarie:

**Forudsætninger** | **Detaljer** 
--- | ---
**VMM** | Vi anbefaler, at du installerer en VMM server i det primære websted og en VMM server i det sekundære websted.<br/><br/> Du kan også [gentage mellem skyer på en enkelt VMM-server](site-recovery-single-vmm.md). Hvis du vil gøre dette skal du mindst to skyer konfigureret på VMM serveren.<br/><br/> VMM servere, der skal kører mindst System Center 2012 SP1 med de seneste opdateringer.<br/><br/> Hver VMM server have på en eller flere skyer konfigureret og alle skyer skal have Hyper-V kapacitet profilen. <br/><br/>Skyer skal indeholde en eller flere VMM host grupper.<br/><br/>Få flere oplysninger om konfiguration af VMM skyer i [konfigurere VMM skyen struktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), og [Gennemgang: oprette private skyer med System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> VMM servere bør have adgang til internettet. 
**Hyper-V** | Hyper-V-servere, der skal køre mindst Windows Server 2012 med Hyper-V-rollen og har installeret de seneste opdateringer.<br/><br/> En Hyper-V server skal indeholde et eller flere FOS.<br/><br/>  Hyper-V værtsservere skal være placeret i host grupper i den primære og sekundære VMM sky.<br/><br/> Hvis du kører Hyper-V i en klynge på Windows Server 2012 R2 skal du installere [opdatere 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Hvis du kører Hyper-V i en klynge på Windows Server 2012 note, klynge broker ikke er oprettet automatisk Hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. [Du finder flere oplysninger](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Udbyder** | Under gendannelse af websteder installation du har installeret Azure websted gendannelse provideren på VMM servere. Udbyderen kommunikerer med gendannelse af websteder over HTTPS 443 til dirigerer gentagelse. Datareplikering forekommer mellem de primære og sekundære Hyper-V-servere over LAN eller en VPN-forbindelse.<br/><br/> Den udbyder, der kører på serveren VMM skal have adgang til disse URL-adresser: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Desuden kan firewall kommunikation fra VMM servere til [Azure datacenter IP-intervaller](https://www.microsoft.com/download/confirmation.aspx?id=41653) og tillade HTTPS (443)-protokollen.

### <a name="network-mapping-prerequisites"></a>Forudsætninger, der er tilknytning netværk
Netværk tilknytning kort mellem VMM VM netværk på de primære og sekundære VMM servere til:

- Placer optimalt replika FOS på sekundær Hyper-V-værter efter failover.
- Forbind replika FOS med relevante VM netværk.
- Hvis du ikke konfigurerer netværk tilknytning replika FOS ikke være forbundet med en hvilken som helst netværk efter failover.
- Hvis du vil konfigurere netværk er tilknytning under gendannelse af websteder installation her skal du:

    - Sørg for, at FOS på kildeserveren Hyper-V host er tilsluttet et VMM VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
    - Kontrollere, at den sekundære sky, som du skal bruge for gendannelse har en tilsvarende VM netværk, der er konfigureret. VM netværket skal være knyttet til en logisk netværk, der er knyttet til den sekundære sky.


Få mere at vide mere om at konfigurere VMM netværk i den under artikler

- [Sådan konfigureres logiske netværk i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Sådan konfigureres VM netværk og gateways i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

Du kan [Få mere at vide](site-recovery-network-mapping.md) om, hvordan netværk tilknytning fungerer.

###<a name="powershell-prerequisites"></a>Forudsætninger for PowerShell
Sørg for, at du har Azure PowerShell klar til brug. Hvis du allerede bruger PowerShell, skal du opgradere til version 0.8.10 eller nyere. Du kan finde oplysninger om konfiguration af PowerShell [vejledning til at installere og konfigurere Azure PowerShell](../powershell-install-configure.md). Når du har oprettet og konfigureret PowerShell, du kan få vist alle tilgængelige cmdletter for tjenesten [her](https://msdn.microsoft.com/library/dn850420.aspx). 

Se [vejledning til at komme i gang med Azure cmdletter](https://msdn.microsoft.com/library/azure/jj554332.aspx)for at få mere for at vide om at tip til, hvordan du bruge som hvordan parameterværdier, input og output håndteres typisk i Azure PowerShell-cmdletter.

## <a name="step-1-set-the-subscription"></a>Trin 1: Konfigurere abonnementet 

1. Fra Azure powershell, log på din konto for Azure: ved hjælp af følgende cmdletter
 
        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred 
    

2. Få en liste over dine abonnementer. Også du får vist subscriptionIDs for hver af abonnementerne. Bemærk ned subscriptionID af det abonnement, hvor du vil oprette gendannelse services samling    

        Get-AzureRmSubscription 

3. Angiv det abonnement, hvori gendannelse services samling der skal oprettes ved Sådan omtales abonnement-ID

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Trin 2: Oprette en samling af legitimationsoplysninger gendannelse 

1. Oprette en Azure ressourcestyring ressourcegruppe, hvis du ikke allerede har et

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Oprette en ny gendannelse Services samling og gemme det oprettede ASR samling objekt i en variabel (bruges senere). Du kan også hente ASR samling objekt indlæg oprettelse ved hjælp af cmdlet'en Get-AzureRMRecoveryServicesVault til:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Trin 3: Angive gendannelse Services samling kontekst

1.  Hvis du har en samling af legitimationsoplysninger allerede har oprettet, skal du køre den under kommando for at få samling af legitimationsoplysninger.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Angiv samling konteksten ved at køre den under kommandoen.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault



## <a name="step-4-install-the-azure-site-recovery-provider"></a>Trin 4: Installere provideren Azure websted gendannelse

1.  Opret en mappe på computeren VMM ved at køre følgende kommando:
    
        New-Item c:\ASR -type directory
        
2.  Udtrække filerne ved hjælp af hentede provider ved at køre følgende kommando
    
        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q

    
3.  Installere provideren ved hjælp af følgende kommandoer:
    
        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Vent til at afslutte installationen.
    
4.  Registrere serveren i den samling af legitimationsoplysninger ved hjælp af følgende kommando:
    
        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-and-associate-a-replication-policy"></a>Trin 5: Oprette og knytte en politik for gentagelse

1.  Oprette en Hyper-V 2012 R2 gentagelse politik ved at køre følgende kommando:

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] VMM skyen kan indeholde Hyper-V hosts kører forskellige versioner af Windows Server (som nævnt i Hyper-V forudsætningerne), men politikken replikering er OS version bestemte. Hvis du har forskellige værter, der kører på andet operativsystemversioner, derefter oprette separate gentagelse politikker for hver type af OS-version. For f.eks: Hvis du har fem værter, der kører på Windows-servere 2012 og tre på Windows Server 2012 R2, kan du oprette to replikering politikker – én for hver type af operativsystemversioner.

2.  Får den primære beskyttelse objektbeholder (primære VMM skyen) og gendannelse beskyttelse objektbeholder (gendannelse VMM skyen) ved at køre følgende kommandoer:
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Hente den politik, du oprettede i trin 1 ved hjælp af det fulde navn på politikken

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Starte tilknytningen af objektbeholderen beskyttelse (VMM skyen) med politikken gentagelse:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Vent på kørslen politik tilknytning til at fuldføre. Du kan se, hvis opgaven er afsluttet ved hjælp af de følgende PowerShell-kodestykke.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Når jobbet er blevet behandlet, skal du køre følgende kommando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).

## <a name="step-5-configure-network-mapping"></a>Trin 5: Konfigurere netværk tilknytning

1. Den første kommando henter servere for den aktuelle samling af legitimationsoplysninger Azure gendannelse af websteder. Kommandoen gemmer gendannelse af websteder til Microsoft Azure-servere i variablen $Servers matrix.

        $Servers = Get-AzureRmSiteRecoveryServer

2. Den under kommandoer få websted gendannelse netværk til VMM kildeserveren og mål VMM serveren.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] VMM kildeserveren kan være den første eller den anden opgave i matrixen servere. Markér navnene på VMM serverne og få netværkene korrekt


4. Den endelige cmdlet opretter en tilknytning mellem det primære netværk og netværket gendannelse. Cmdletten angiver det primære netværk som det første element i $PrimaryNetworks og gendannelse netværket som det første element i $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Trin 6: Konfigurer tilknytning af lager

1. Den under kommandoen får liste over lagerplads klassificeringer til $storageclassifications variabel.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. Den under kommandoer få kilde klassificering i $SourceClassificaion variabel- og destinationswebsteder klassificering i $TargetClassification variabel. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Kilde- og destinationswebsteder klassificeringer kan være en hvilken som helst element i matrixen. Refererer til output fra den under kommandoen til at regne indekset for kilde- og destinationswebsteder klassificeringer i $storageclassifications matrix. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Vælg-objekt - egenskaben FriendlyName, Id | Formater-tabel


3. Det opretter en tilknytning mellem kilde klassificering og mål klassificering under cmdlet. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Trin 7: Aktivér beskyttelse af virtuelle maskiner

Når servere, skyer og netværk er konfigureret korrekt, kan du aktivere beskyttelse af virtuelle maskiner i skyen. 


  1. For at aktivere beskyttelse skal du køre følgende kommando for at få objektbeholderen beskyttelse:
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Få objektet beskyttelse (VM) ved at køre følgende kommando:
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Aktivere gentagelse af VM ved at køre følgende kommando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Teste din installation

Du kan køre en test sekundær server til en enkelt virtuel maskine, eller Opret en gendannelse plan bestående af flere virtuelle maskiner og køre en test sekundær server til planen for at teste din installation. Test failover simulerer din failover og gendannelse ordning i en isolerede netværk. 

> [AZURE.NOTE] Du kan oprette en gendannelse plan for dit program Azure-portalen.

Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).


### <a name="run-a-test-failover"></a>Køre en test failover

1.  Køre den under cmdlet'er til at få det VM netværk, du vil teste failover din FOS til.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Udføre en test failover af en VM ved at gøre følgende:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Udføre en test failover af en plan for gendannelse ved at gøre følgende:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Køre en planlagt failover

1. Udføre en planlagt failover af en VM ved at gøre følgende:
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Udføre en planlagt failover af en plan for gendannelse ved at gøre følgende:
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Køre en ikke-planlagt failover

1. Udføre en ikke-planlagt failover af en VM ved at gøre følgende:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2. udføre en ikke-planlagt failover af en plan for gendannelse ved at gøre følgende:
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
## <a name=monitor></a>Overvåge aktivitet

Brug følgende kommandoer til at overvåge aktiviteten. Bemærk, at du skal vente mellem job for behandling til at afslutte.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Næste trin

[Læs mere](https://msdn.microsoft.com/library/azure/mt637930.aspx) om Azure gendannelse af websteder med Azure ressourcestyring PowerShell-cmdlet'er.

