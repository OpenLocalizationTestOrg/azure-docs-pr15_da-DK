<properties
 pageTitle="Køre STJERNE-CCM + med HPC Pack på Linux FOS | Microsoft Azure"
 description="Installere en Microsoft HPC Pack klynge på Azure og køre en STJERNE-CCM + jobbet på flere Linux beregne noder på tværs af et netværk med RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/13/2016"
 ms.author="xpillons"/>

# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Køre STJERNE-CCM + med Microsoft HPC Pack på en Linux RDMA klynge i Azure
I denne artikel beskrives, hvordan du installerer en Microsoft HPC Pack klynge på Azure og køre en [CD-adapco STJERNE-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) jobbet på flere Linux Beregn noder, der er forbundet med InfiniBand.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack indeholder funktioner for at køre en række forskellige store HPC og parallelle programmer, herunder MPI programmer, på klynger af virtuelle Microsoft Azure-computere. HPC Pack understøtter også programmer, der kører Linux HPC på Linux Beregn-node VM'er, der er implementeret i en HPC Pack klynge. For at få en introduktion til brug af Linux beregne noder med HPC Pack, skal du se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Konfigurere en HPC Pack klynge
Hente HPC Pack IaaS installation scripts fra [Overførselscenter](https://www.microsoft.com/en-us/download/details.aspx?id=44949) og uddrage dem lokalt.

Azure PowerShell er en betingelse. Hvis PowerShell ikke er konfigureret på din lokale computer, skal du læse artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Linux billeder fra Azure Marketplace (som indeholder InfiniBand driverne til Azure) er samtidig med denne skrivning til SLES 12, CentOS 6.5 og CentOS 7.1. I denne artikel er baseret på brugen af SLES 12. Hvis du vil hente navnet på alle Linux-billeder, der understøtter HPC på markedet, kan du køre følgende PowerShell-kommando:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

Output viser den placering, hvor disse billeder er tilgængelige og navnet, billede (**Procesnavn**) skal bruges i skabelonen installation senere.

Før du installerer klyngen, er det nødvendigt at opbygge en skabelonfil HPC Pack installation. Da vi målretning af en lille klynge, er domænecontrolleren noden hoved og være vært for en lokal SQL-database.

Følgende skabelon skal installere en hoved node, oprette en XML-fil med navnet **MyCluster.xml**, og Erstat værdierne af **SubscriptionId**, **StorageAccount**, **placering**, **VMName**og **ServiceName** med dine.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Starte oprettelsen af hoved-node ved at køre PowerShell-kommandoen i en kommandoprompt:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Noden hoved skal være klar efter 20-30 minutter. Du kan oprette forbindelse til den fra Azure-portalen ved at klikke på ikonet **Opret forbindelse** på den virtuelle maskine.

Du kan til sidst skal løse DNS domænes. Gør du ved at starte DNS Manager.

1.  Højreklik på navnet på serveren i DNS Manager, vælge **Egenskaber**, og klik derefter på fanen **videresendelse** .

2.  Klik på knappen **Rediger** for at fjerne en hvilken som helst videresendelse, og klik derefter på **OK**.

3.  Sørg for, at afkrydsningsfeltet **Brug rodtip, hvis ingen videresendelse findes** er markeret, og klik derefter på **OK**.

## <a name="set-up-linux-compute-nodes"></a>Konfigurere Linux beregne noder
Du kan installere noderne Linux Beregn ved hjælp af den samme installation skabelon, du brugte til at oprette noden hoved.

Kopiér filen **MyCluster.xml** fra din lokale computer til noden hoved og opdatere mærket **NodeCount** med antallet af knuder, du vil installere (< = 20). Sørg for at har tilstrækkelig tilgængelig kerner i din Azure kvote, fordi hver A9 forekomst vil forbruge 16 kerner i dit abonnement. Du kan bruge A8 forekomster (8 kerner) i stedet for A9, hvis du vil bruge flere FOS i det samme budget.

Kopiere HPC Pack IaaS installation scriptene på noden hoved.

Kør følgende Azure PowerShell-kommandoer i en kommandoprompt:

1.  Køre **Tilføj AzureAccount** til at oprette forbindelse til dit Azure-abonnement.

2.  Hvis du har flere abonnementer, Kør **Get-AzureSubscription** for at få vist dem.

3.  Angive et standard-abonnement ved at køre den **Vælg AzureSubscription - SubscriptionName xxxx-standard** kommandoen.

4.  Køre **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** til at starte implementering af Linux beregne noder.

    ![Hoved-node installation i praksis][hndeploy]

Åbn værktøjet HPC Pack klynge Manager. Efter nogle minutter vises Linux Beregn noder regelmæssig i listen over klynge Beregn noder. Med tilstanden Klassisk installation oprettes IaaS FOS sekventielt. Så hvis antallet af knuder er vigtige, kan få dem alle installeret tage lang tid.

![Linux noder i HPC Pack klynge Manager][clustermanager]

Nu, hvor alle noder er oppe at køre i klyngen, er der ekstra infrastruktur til indstillingerne for at sikre.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Konfigurere en Azure filshare for Windows og Linux noder
Du kan bruge tjenesten Azure-fil til at gemme scripts, programpakker og datafiler. Azure fil indeholder CIFS funktioner oven på Azure Blob-lager som en fast butik. Vær opmærksom på, at dette er ikke den mest SVG løsning, men det er den nemmeste, og kræver ikke dedikeret FOS.

Oprette en Azure filshare ved at følge vejledningen i artiklen [Introduktion til Azure fillagring i Windows](..\storage\storage-dotnet-how-to-use-files.md).

Behold navnet på din lagerplads konto som **saname**, del filnavn som **sharenavn**og tasten lagerplads konto som **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Oprette forbindelse til Azure filshare på noden hoved
Åbn en kommandoprompt, og Kør følgende kommando for at gemme legitimationsoplysningerne i den lokale computer samling af legitimationsoplysninger:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Kør derefter for at tilslutte Azure filshare:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Oprette forbindelse til Azure filshare på Linux Beregn noder
Et nyttigt værktøj, der følger med HPC Pack er værktøjet clusrun. Du kan bruge dette værktøj på kommandolinjen til at køre kommandoen samme samtidigt på et sæt Beregn noder. I dette tilfælde bruges det til at tilslutte Azure filshare og fastholdes den klare genstarter.
Kør følgende kommandoer i en kommandoprompt på noden hoved.

Sådan oprettes tilslutningsmappen:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

At tilslutte Azure filshare:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Bevares tilslutningen dele:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Installere STJERNE-CCM +
Azure VM forekomster A8 og A9 giver InfiniBand support og RDMA egenskaber. De kerne-drivere, der aktiverer disse funktioner er tilgængelige for Windows Server 2012 R2, SUSE 12, CentOS 6.5 og CentOS 7.1 billeder i Azure Marketplace. Microsoft MPI og Intel MPI (version 5.x) er de to MPI biblioteker, der understøtter disse drivere i Azure.

CD-adapco STJERNE-CCM + slip 11.x og senere, følger med Intel MPI version 5.x, så InfiniBand understøttelse af Azure er inkluderet.

Få Linux64 STJERNE-CCM + pakke fra [CD-adapco portal](https://steve.cd-adapco.com). I dette tilfælde brugte vi version 11.02.010 i blandede præcision.

Opret et shell script med navnet **setupstarccm.sh** med følgende indhold i sektionen **/hpcdata** Azure fil dele på noden hoved. Dette script køres på hver beregningsnode til konfiguration af STJERNE-CCM + lokalt.

#### <a name="sample-setupstarcmsh-script"></a>Eksempel på setupstarcm.sh-script
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Nu skal du konfigurerer STJERNE-CCM + på alle dine Linux beregne noder, Åbn en kommandoprompt og køre følgende kommando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Mens kommandoen kører, kan du overvåge CPU-brug ved hjælp af varmekort af klynge Manager. Efter nogle minutter, skal alle noder være konfigureret korrekt.

## <a name="run-star-ccm-jobs"></a>Køre STJERNE-CCM + job
HPC Pack bruges til dens job scheduler muligheder for at køre STJERNE-CCM + job. Hvis du vil gøre det, vi har brug for understøttelse af et par scripts, som bruges til at starte jobbet og køre STJERNE-CCM +. De indtastede data er gemt på Azure filshare første for enkel.

Følgende PowerShell-script bruges til køen en STJERNE-CCM + job. Det tager tre argumenter:

*  Navnet på model

*  Antallet af knuder der skal bruges

*  Antallet kerner på hver node skal bruges

Fordi STJERNE-CCM + kan udfylde hukommelsesbåndbredde, er det normalt bedre at bruge færre kerner per Beregn noder og tilføje nye noder. Det nøjagtige antal kerner per node afhænger processorfamilie og interconnect hastigheden.

Noderne allokeres udelukkende til jobbet og kan ikke deles med andre job. Jobbet er ikke startet som en MPI job direkte. **Runstarccm.sh** shell script starter starteren MPI.

Indtast modellen og scriptet **runstarccm.sh** gemmes i **/hpcdata** del, der var tidligere tilsluttet.

Logfiler navngives med job-ID og gemmes i den **/hpcdata dele**, sammen med STJERNEN-CCM + output filer.


#### <a name="sample-submitstarccmjobps1-script"></a>Eksempel på SubmitStarccmJob.ps1-script
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job    
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Erstatte **runner.java** med din foretrukne STJERNE-CCM + Java model launcher og logføring kode.

#### <a name="sample-runstarccmsh-script"></a>Eksempel på runstarccm.sh-script
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Vi har brugt et Power On Demand licens token i vores test. For at få id, du har angivet miljøvariablen **$CDLMD_LICENSE_FILE** til **1999@flex.cd-adapco.com** og tasten i indstillingen **- podkey** i kommandolinjen.

Når du har nogle initialisering scriptet henter – fra **$CCP_NODES_CORES** miljøvariablerne, der angives HPC Pack – på listen over noder til at opbygge en hostfile, der bruger starteren MPI. Denne hostfile indeholder listen over Beregn nodenavne, der bruges til jobbet, ét navn hver linje.

Formatet af **$CCP_NODES_CORES** følger dette mønster:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Hvor:

* `<Number of nodes>`er antallet af knuder, der er tildelt denne sag.

* `<Name of node_n_...>`er navnet på hver node, der er tildelt denne sag.

* `<Cores of node_n_...>`er antallet kerner på den node, der er tildelt denne sag.

Antallet kerner (**$NBCORES**) beregnes også afhængigt af antallet af knuder (**$NBNODES**) og antallet kerner per node (angivet parameter **$NBCORESPERNODE**).

For MPI indstillingerne er dem, der bruges med Intel MPI på Azure:

*   `-mpi intel`angive Intel MPI.

*   `-fabric UDAPL`bruge Azure InfiniBand verber.

*   `-cpubind bandwidth,v`optimere båndbredde til MPI med STJERNE-CCM +.

*   `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"`gøre Intel MPI arbejde med Azure InfiniBand, og til at angive det påkrævede antal kerner per node.

*   `-batch`starte STJERNE-CCM + i batch-tilstand med ingen brugergrænseflade.


Endelig, for at starte et job, Kontrollér, at din noder er oppe at køre og er online i klynge Manager. Fra en PowerShell-kommandoprompt, Kør derefter dette:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Stoppe noder
Nyere på når du er færdig med din test, kan du bruge følgende HPC Pack PowerShell-kommandoer til at stoppe og starte noder:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Næste trin
Prøv at køre andre Linux arbejdsmængder. Se, f.eks.:

* [Køre NAMD med Microsoft HPC Pack på Linux Beregn noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Køre OpenFOAM med Microsoft HPC Pack på en Linux RDMA klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png
