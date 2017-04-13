<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer ved hjælp af Azure gendannelse af websteder og PowerShell | Microsoft Azure"
    description="Lær at automatisere gentagelse af Hyper-V virtuelle maskiner i VMM skyer ved hjælp af gendannelse af websteder og PowerShell."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure ved hjælp af Powershell - klassisk

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-azure.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Klassisk Portal](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - klassisk](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Oversigt

Azure gendannelse af websteder bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner i en række installationsscenarier. Du kan finde en komplet liste over installationsscenarier i [Oversigt over Azure gendannelse af websteder](site-recovery-overview.md).

I denne artikel viser dig hvordan du kan bruge PowerShell til at automatisere almindelige opgaver, du skal udføre, når du konfigurerer Azure gendannelse af websteder kan replikeres Hyper-V virtuelle maskiner i System Center VMM skyer til Azure-lager.

I artiklen indeholder forudsætninger for dette scenario, og hvordan du kan konfigurere en samling af legitimationsoplysninger gendannelse af websteder kan installere Azure websted gendannelse udbyder på VMM kildeserveren, registrere serveren i samling af legitimationsoplysninger, tilføje en konto til Azure-lager, installere Azure gendannelse Services agent på Hyper-V værtsservere, konfigurere indstillingerne for dokumentbeskyttelse for VMM sky, der anvendes på alle beskyttede virtuelle maskiner , og derefter aktivere beskyttelse af disse virtuelle computere. Færdigt ved at teste den sekundære at sikre, at alt fungerer som forventet.

Hvis du støder på problemer med at konfigurere dette scenarie skal du sende dine spørgsmål på [Azure gendannelse Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [ressourcestyring og klassisk](../resource-manager-deployment-model.md). Denne artikel omhandler ved hjælp af implementeringsmodel klassisk.



## <a name="before-you-start"></a>Før du starter

Sørg for, at du har disse forudsætninger sted:

### <a name="azure-prerequisites"></a>Azure forudsætninger

- Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- Du skal bruge en Azure-lager-konto til at gemme replikerede data. Kontoen skal geografisk replikering aktiveret. Det skal være i samme område som samling af legitimationsoplysninger Azure gendannelse af websteder og være knyttet til den samme abonnement. [Lær mere om Azure-lager](../storage/storage-introduction.md).
- Du skal kontrollere, at virtuelle maskiner, du vil beskytte i overensstemmelse med [Azure virtuelt forudsætninger](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Forudsætninger for VMM
- Du skal have VMM server, der kører på System Center 2012 R2.
- Du skal have mindst én skyen på VMM serveren, du vil beskytte. Skyen skal indeholde:
    - En eller flere VMM host grupper.
    - En eller flere Hyper-V værtsservere eller grupper i hver host gruppe.
    - En eller flere virtuelle maskiner på kildeserveren Hyper-V.

### <a name="hyper-v-prerequisites"></a>Forudsætninger for Hyper-V

- Værtsservere Hyper-V skal køre mindst **Windows Server 2012** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012** og har installeret de seneste opdateringer.
- Hvis du kører Hyper-V i en klynge note, klynge broker ikke er oprettet automatisk Hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. Du gør dette, i Server Manager > failoverklyngestyring, oprette forbindelse til klyngen skal du klikke på **Konfigurere rolle** og vælge **Hyper-V replika Broker** på skærmbilledet **Vælg rolle** i guiden høj tilgængelighed.
- En hvilken som helst Hyper-V host server eller en klynge, du vil administrere beskyttelse skal medtages i en VMM sky.

### <a name="network-mapping-prerequisites"></a>Forudsætninger, der er tilknytning netværk
Når du har beskyttet virtuelle maskiner i Azure netværk tilknytning kort mellem VM netværk på VMM kildeserveren og målrette Azure netværk for at aktivere følgende:

- Alle computere, som ikke på det samme netværk kan oprette forbindelse til hinanden, uanset hvilken gendannelse plan de er i.
- Hvis en netværksgateway er sat op på destinationen Azure netværk, virtuelle computere kan oprette forbindelse til andre lokale virtuelle computere.
- Hvis du ikke konfigurerer netværk tilknytning kun virtuelle maskiner, der ikke består i den samme plan for gendannelse vil ikke kunne oprette forbindelse til hinanden efter failover til Azure.

Hvis du vil installere kort over netværk skal du bruge følgende:

- De virtuelle maskiner, du vil beskytte på VMM kildeserveren skal forbindes til et VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
- En Azure-netværk, kan oprette forbindelse replikerede virtuelle maskiner efter failover. På tidspunktet ydelse skal du markere dette netværk. Netværket skal være i samme område som abonnementet Azure gendannelse af websteder.
- [Få mere at vide](site-recovery-network-mapping.md) om netværk tilknytning:

###<a name="powershell-prerequisites"></a>Forudsætninger for PowerShell
Sørg for, at du har Azure PowerShell klar til brug. Hvis du allerede bruger PowerShell, skal du opgradere til version 0.8.10 eller nyere. Finde oplysninger om konfiguration af PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). Når du har oprettet og konfigureret PowerShell, du kan få vist alle tilgængelige cmdletter for tjenesten [her](https://msdn.microsoft.com/library/dn850420.aspx).

Se [Introduktion til Azure-cmdletter](https://msdn.microsoft.com/library/azure/jj554332.aspx)for at få mere for at vide om at tip til, hvordan du bruge som hvordan parameterværdier, input og output håndteres typisk i Azure PowerShell-cmdletter.

## <a name="step-1-set-the-subscription"></a>Trin 1: Konfigurere abonnementet

Kør følgende cmdletter i PowerShell:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Erstatte elementerne i "sammen" med din specifikke oplysninger.

## <a name="step-2-create-a-site-recovery-vault"></a>Trin 2: Oprette en samling af legitimationsoplysninger gendannelse af websteder

Erstatte elementerne i "sammen" med din specifikke oplysninger i PowerShell, og Kør følgende kommandoer:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Trin 3: Opret en samling af legitimationsoplysninger registreringsnøgle

Oprette en registreringsnøgle i samling af legitimationsoplysninger. Når du henter Azure websted gendannelse udbyder og installere den på serveren, der VMM, skal du bruge denne tast til at registrere VMM serveren i samling af legitimationsoplysninger.

1.  Få indstillingsfil samling af legitimationsoplysninger og angive konteksten:

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Angiv samling konteksten ved at køre følgende kommandoer:

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Trin 4: Installere provideren Azure websted gendannelse

1.  Opret en mappe på computeren VMM ved at køre følgende kommando:

    ```

    pushd C:\ASR\

    ```

2.  Udtrække filerne ved hjælp af hentede provider ved at køre følgende kommando

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Installere provideren ved hjælp af følgende kommandoer:

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Vent til at afslutte installationen.

4.  Registrere serveren i den samling af legitimationsoplysninger ved hjælp af følgende kommando:

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Trin 5: Oprette en Azure-lager-konto

Hvis du ikke har en Azure-lager-konto, kan du oprette en geografisk replikering aktiveret konto ved at køre følgende kommando:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Bemærk, at kontoen lagerplads skal være i samme område som tjenesten Azure gendannelse af websteder og være knyttet til den samme abonnement.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Trin 6: Installer Azure betroet Services bruger

Installer Azure gendannelse Services agent fra portalen Azure på hver Hyper-V host server, der er placeret i den VMM sky, du vil beskytte.

Kør følgende kommando på alle VMM værter:

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Trin 7: Konfigurere skyen indstillingerne for dokumentbeskyttelse

1.  Oprette en skyen beskyttelse profil til Azure ved at køre følgende kommando:

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Hent en objektbeholder til beskyttelse ved at køre følgende kommandoer:

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Starte tilknytningen af objektbeholderen beskyttelse med skyen:

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Når jobbet er færdig, skal du køre følgende kommando:


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Når jobbet er blevet behandlet, skal du køre følgende kommando:


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



Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).

## <a name="step-8-configure-network-mapping"></a>Trin 8: Konfigurer tilknytning af netværk
Før du begynder at bekræfte kort over netværk, virtuelle maskiner på VMM kildeserveren har forbindelse til en VM netværk. Derudover kan oprette en eller flere Azure virtuelle netværk. Bemærk, at flere VM netværk kan knyttes til en enkelt Azure netværk.

Bemærk, at hvis target netværk har flere undernet og én af disse undernet har samme navn som undernet virtuelt kilde er placeret, og derefter virtuelt replika der skal knyttes til den destinationsadresse-undernet efter failover. Hvis der ikke er et destinationsprogram undernet med et tilsvarende navn, forbindes den virtuelle maskine til det første undernet i netværket.

Den første kommando henter servere for den aktuelle samling af legitimationsoplysninger Azure gendannelse af websteder. Kommandoen gemmer gendannelse af websteder til Microsoft Azure-servere i variablen $Servers matrix.

    $Servers = Get-AzureSiteRecoveryServer


Den anden kommando henter websted gendannelse netværk til den første server i matrixen $Servers. Kommandoen gemmer netværkene i variablen $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

Kommandoen tredje får dine Azure abonnementer ved hjælp af cmdlet'en Get-AzureSubscription til, og derefter gemmer den pågældende værdi i $Subscriptions variablen.

    $Subscriptions = Get-AzureSubscription



Kommandoen fjerde får Azure virtuelle netværk ved hjælp af cmdlet'en Get-AzureVNetSite til, og klik derefter på den pågældende værdi i variablen $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



Den endelige cmdlet opretter en tilknytning mellem det primære netværk og Azure virtuelt netværket. Cmdletten angiver det primære netværk som det første element i $Networks. Cmdletten angiver et virtuelt netværk som det første element i $AzureVmNetworks ved hjælp af dens ID. Kommandoen indeholder dit Azure abonnement-ID.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Trin 9: Aktivér beskyttelse af virtuelle maskiner

Når servere, skyer og netværk er konfigureret korrekt, kan du aktivere beskyttelse af virtuelle maskiner i skyen. Bemærk følgende:

Virtuelle maskiner skal opfylde [Azure virtuelt forudsætninger](site-recovery-best-practices.md#virtual-machines).

Diskegenskaber skal være angivet til den virtuelle maskine, hvis du vil aktivere beskyttelse operativsystem og operativsystem. Når du opretter en virtuel maskine i VMM ved hjælp af et virtuelt skabelon kan du angive egenskaben. Du kan også angive disse egenskaber for eksisterende virtuelle maskiner under fanen **Generelt** og **Hardwarekonfiguration** virtuelt egenskaber. Hvis du ikke angiver disse egenskaber i VMM vil du kunne konfigurere dem i portalen Azure gendannelse af websteder.



1.  For at aktivere beskyttelse skal du køre følgende kommando for at få objektbeholderen beskyttelse:

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Få objektet beskyttelse (VM) ved at køre følgende kommando:


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Aktivere DR af VM ved at køre følgende kommando:



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Teste din installation

Du kan køre en test sekundær server til en enkelt virtuel maskine, eller Opret en gendannelse plan bestående af flere virtuelle maskiner og køre en test sekundær server til planen for at teste din installation. Test failover simulerer din failover og gendannelse ordning i en isolerede netværk. Bemærk, at:

- Hvis du vil oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord efter sekundære, skal du aktivere Fjernskrivebord på den virtuelle maskine, før du kører sekundære test.
- Efter failover, skal du bruge en offentlig IP-adresse til at oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord. Hvis du gør dette, skal du sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.

Hvis du vil kontrollere afslutning af handlingen, skal du følge trinnene i [Overvåge aktivitet](#monitor).

### <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

1. Oprette en .XML-fil som en skabelon til din gendannelse plan ved hjælp af nedenstående dataene, og derefter gemme det som "C:\RPTemplatePath.xml".
2. Ændre RecoveryPlan node Id, navn, PrimaryServerId og SecondaryServerId.
3. Ændre noden ProtectionEntity PrimaryProtectionEntityId (vmid fra VMM).
4. Du kan tilføje flere FOS ved at tilføje flere ProtectionEntity noder.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Udfylde data i skabelonen:


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Oprette RecoveryPlan:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Køre en test failover

1.  Få RecoveryPlan objektet ved at køre følgende kommando:

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Starte test sekundære ved at køre følgende kommando:


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Læs mere](https://msdn.microsoft.com/library/dn850420.aspx) om Azure websted gendannelse PowerShell-cmdletter. </a>.
