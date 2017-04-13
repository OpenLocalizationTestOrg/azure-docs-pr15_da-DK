<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer ved hjælp af Azure gendannelse af websteder og PowerShell (ressourcestyring) | Microsoft Azure"
    description="Gentage Hyper-V virtuelle maskiner i VMM skyer ved hjælp af Azure gendannelse af websteder og PowerShell"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure ved hjælp af PowerShell og Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-azure.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Klassisk Portal](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - klassisk](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Oversigt

Azure gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner i en række installationsscenarier. Du kan finde en komplet liste over installationsscenarier i [Oversigt over Azure gendannelse af websteder](site-recovery-overview.md).

I denne artikel viser dig hvordan du kan bruge PowerShell til at automatisere almindelige opgaver, du skal udføre, når du konfigurerer Azure gendannelse af websteder kan replikeres Hyper-V virtuelle maskiner i System Center VMM skyer til Azure-lager.

I artiklen indeholder forudsætninger for dette scenario, og viser dig

- Hvordan du konfigurerer en gendannelse Services samling
- Installer Azure websted gendannelse udbyder på VMM kildeserveren
- Registrere serveren i samling af legitimationsoplysninger, skal du tilføje en konto til Azure-lager
- Installer Azure gendannelse Services agent på Hyper-V værtsservere
- Konfigurere indstillinger for beskyttelse for VMM skyer, der skal anvendes til alle beskyttede virtuelle maskiner
- Aktivér beskyttelse til disse virtuelle computere.
- Test over fejl at sikre, at alt fungerer som forventet.

Hvis du støder på problemer med at konfigurere dette scenarie skal du sende dine spørgsmål på [Azure gendannelse Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [ressourcestyring og klassisk](../resource-manager-deployment-model.md). Denne artikel omhandler ved hjælp af Ressourcestyring implementeringsmodel.

## <a name="before-you-start"></a>Før du starter

Sørg for, at du har disse forudsætninger sted:

### <a name="azure-prerequisites"></a>Azure forudsætninger

- Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Hvis du ikke har en, kan du starte med en [gratis konto](https://azure.microsoft.com/free). Desuden kan du læse om [Azure gendannelse webstedsadministrator priser](https://azure.microsoft.com/pricing/details/site-recovery/).
- Hvis du afprøver gentagelse til et scenarie med CSP abonnement, skal du bruge et CSP abonnement. Lær mere om programmet CSP i [hvordan du tilmelder dig i programmet CSP](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Du skal bruge en Azure v2 lagerplads (ressourcestyring) konto til at gemme data replikeres til Azure. Kontoen skal geografisk replikering aktiveret. Det skal være i samme område som tjenesten Azure gendannelse af websteder, og du kan være forbundet med det samme abonnement eller CSP abonnementet. Hvis du vil vide mere om konfiguration af Azure-lager, skal du se [Introduktion til Microsoft Azure-lager](../storage/storage-introduction.md) til reference.
- Du skal kontrollere, at virtuelle maskiner, du vil beskytte i overensstemmelse med de [Azure virtuelt forudsætninger](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] På nuværende tidspunkt er kun VM niveau handlinger mulige via Powershell. Understøttelse af gendannelse plan niveau handlinger bliver snart.  Nu skal er du begrænset til at udføre fejl overs kun på en 'beskyttet VM' granularitet og ikke på en gendannelse planlægge niveau.

### <a name="vmm-prerequisites"></a>Forudsætninger for VMM
- Du skal have VMM server, der kører på System Center 2012 R2.
- En hvilken som helst VMM server, der indeholder virtuelle maskiner, du vil beskytte skal køre Azure websted gendannelse udbyder. Dette er installeret under implementeringen Azure gendannelse af websteder.
- Du skal have mindst én skyen på VMM serveren, du vil beskytte. Skyen skal indeholde:
    - En eller flere VMM host grupper.
    - En eller flere Hyper-V værtsservere eller grupper i hver host gruppe.
    - En eller flere virtuelle maskiner på kildeserveren Hyper-V.
- Lære mere om konfiguration af VMM skyer:
    - Læs mere om private VMM skyer i [Nyheder i privat skyen med System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) og i [VMM 2012 og sky](http://go.microsoft.com/fwlink/?LinkId=324956).
    - Få mere at vide om at [konfigurere VMM skyen struktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Når dine skyen strukturen elementer er på plads få mere at vide om at oprette private skyer i at [oprette en privat skyen i VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) og i [Gennemgang: oprette private skyer med System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Forudsætninger for Hyper-V

- Værtsservere Hyper-V skal køre mindst **Windows Server 2012** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012** og har installeret de seneste opdateringer.
- Hvis du kører Hyper-V i en klynge note, klynge broker ikke er oprettet automatisk Hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. For
- Se, [hvordan du konfigurerer Hyper-V replika Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx)vejledning.
- En hvilken som helst Hyper-V host server eller en klynge, du vil administrere beskyttelse skal medtages i en VMM sky.

### <a name="network-mapping-prerequisites"></a>Forudsætninger, der er tilknytning netværk
Når du har beskyttet virtuelle maskiner i Azure, netværk tilknytning kort den virtuelle maskine netværk på VMM kildeserveren og målrette Azure netværk for at give mulighed for følgende:

- Alle computere, som ikke på det samme netværk kan oprette forbindelse til hinanden, uanset hvilken gendannelse plan de er i.
- Hvis en netværksgateway er sat op på destinationen Azure netværk, virtuelle computere kan oprette forbindelse til andre lokale virtuelle computere.
- Hvis du ikke konfigurerer kort over netværk, vil kun de virtuelle maskiner, der ikke består i den samme plan for gendannelse kunne oprette forbindelse til hinanden efter fejl over til Azure.

Hvis du vil installere kort over netværk skal du bruge følgende:

- De virtuelle maskiner, du vil beskytte på VMM kildeserveren skal forbindes til et VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
- En Azure-netværk, kan oprette forbindelse replikerede virtuelle maskiner efter fejl over. På tidspunktet for fejl over skal du markere dette netværk. Netværket skal være i samme område som abonnementet Azure gendannelse af websteder.

Lær mere om kort over netværk i

- [Sådan konfigureres logiske netværk i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Sådan konfigureres VM netværk og gateways i VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Sådan konfigureres og overvåge virtuelle netværk i Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


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

1. Oprette en ressourcegruppe i Azure Ressourcestyring, hvis du ikke allerede har et

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Oprette en ny gendannelse Services samling og gemme det oprettede ASR samling objekt i en variabel (bruges senere). Du kan også hente ASR samling objekt indlæg oprettelse ved hjælp af cmdlet'en Get-AzureRMRecoveryServicesVault til:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Trin 3: Angive gendannelse Services samling kontekst

1.  Angiv samling konteksten ved at køre den under kommandoen.

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


## <a name="step-5-create-an-azure-storage-account"></a>Trin 5: Oprette en Azure-lager-konto

1. Hvis du ikke har en Azure-lager-konto, kan du oprette en geografisk replikering aktiveret konto i det samme geografisk som samling af legitimationsoplysninger ved at køre følgende kommando:

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Bemærk, at kontoen lagerplads skal være i samme område som tjenesten Azure gendannelse af websteder og være knyttet til den samme abonnement.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Trin 6: Installer Azure betroet Services bruger

1. Hent Azure gendannelse Services agent på [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) , og installer det på hver Hyper-V host server, der er placeret i den VMM sky, du vil beskytte.

2. Kør følgende kommando på alle VMM værter:

    marsagentinstaller.exe/q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Trin 7: Konfigurere skyen indstillingerne for dokumentbeskyttelse

1.  Oprette en politik for gentagelse til Azure ved at køre følgende kommando:


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Hent en objektbeholder til beskyttelse ved at køre følgende kommandoer:

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Få at vide om politik til en variabel ved hjælp af den sag, som blev oprettet, og Sådan omtales fulde politikkens navn:

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Starte tilknytningen af objektbeholderen beskyttelse med politikken gentagelse:

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Når jobbet er færdig, skal du køre følgende kommando:

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Når jobbet er blevet behandlet, skal du køre følgende kommando:

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).

## <a name="step-8-configure-network-mapping"></a>Trin 8: Konfigurer tilknytning af netværk

Før du begynder at bekræfte kort over netværk, virtuelle maskiner på VMM kildeserveren har forbindelse til en VM netværk. Derudover kan oprette en eller flere Azure virtuelle netværk.

Få mere at vide mere om, hvordan du opretter et virtuelt netværk ved hjælp af Azure ressourcestyring og PowerShell, i [oprette et virtuelt netværk med en - til-websted VPN-forbindelse ved hjælp af Azure ressourcestyring og PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Bemærk, at flere virtuelt netværk kan knyttes til en enkelt Azure netværk. Hvis target netværket har flere undernet og én af disse undernet har samme navn som undernet, hvor virtuelt kilde er placeret, der derefter virtuelt replika skal knyttes til pågældende target undernet efter fejl over. Hvis der er ingen target undernet med et tilsvarende navn skal forbindes den virtuelle maskine til det første undernet i netværket.

1. Den første kommando henter servere for den aktuelle samling af legitimationsoplysninger Azure gendannelse af websteder. Kommandoen gemmer gendannelse af websteder til Microsoft Azure-servere i variablen $Servers matrix.

        $Servers = Get-AzureRmSiteRecoveryServer

2. Den anden kommando henter websted gendannelse netværk til den første server i matrixen $Servers. Kommandoen gemmer netværkene i variablen $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. Kommandoen tredje får Azure virtuelle netværk, og klik derefter på den pågældende værdi i $AzureVmNetworks variablen.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. Den endelige cmdlet opretter en tilknytning mellem det primære netværk og Azure virtuelt netværket. Cmdletten angiver det primære netværk som det første element i $Networks. Cmdletten angiver et virtuelt netværk som det første element i $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Trin 9: Aktivér beskyttelse af virtuelle maskiner

Når servere, skyer og netværk er konfigureret korrekt, kan du aktivere beskyttelse af virtuelle maskiner i skyen.

 Bemærk følgende:

 - Virtuelle maskiner skal opfylde Azure krav. Se disse i [forudsætninger og Support](site-recovery-best-practices.md) i den Planlægningsvejledning.

 - Hvis du vil aktivere beskyttelse, operativsystem og operativsystem skal diskegenskaber være angivet til den virtuelle maskine. Når du opretter en virtuel maskine i VMM ved hjælp af et virtuelt skabelon kan du angive egenskaben. Du kan også angive disse egenskaber for eksisterende virtuelle maskiner under fanen **Generelt** og **Hardwarekonfiguration** virtuelt egenskaber. Hvis du ikke angiver disse egenskaber i VMM vil du kunne konfigurere dem i portalen Azure gendannelse af websteder.


  1. For at aktivere beskyttelse skal du køre følgende kommando for at få objektbeholderen beskyttelse:

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Få objektet beskyttelse (VM) ved at køre følgende kommando:

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Aktivere DR af VM ved at køre følgende kommando:

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Teste din installation

Du kan køre en test fail-over for en enkelt virtuel maskine, eller Opret en gendannelse plan bestående af flere virtuelle maskiner og køre en test fail-over for planen for at teste din installation. Test fejl over simulerer din fail-over og gendannelse ordning i en isolerede netværk. Bemærk, at:

- Hvis du vil oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord efter over den fejl, skal du aktivere Fjernskrivebord på den virtuelle maskine, før du kører sekundære test.
- Efter over fejl, skal du bruge en offentlig IP-adresse til at oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord. Hvis du gør dette, skal du sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.

Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).


### <a name="run-a-test-failover"></a>Køre en test failover

1.  Starte test sekundære ved at køre følgende kommando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Køre en planlagt failover

1. Starte den planlagte sekundære ved at køre følgende kommando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Køre en ikke-planlagt failover

1. Starte den ikke-planlagte sekundære ved at køre følgende kommando:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


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
