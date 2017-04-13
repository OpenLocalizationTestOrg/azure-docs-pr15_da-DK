<properties
 pageTitle="Linux RDMA klynge for at køre MPI programmer | Microsoft Azure"
 description="Oprette en Linux-klynge af størrelse H16r, H16mr, A8 eller A9 FOS bruge Azure RDMA netværk til at køre MPI apps"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Konfigurere en Linux RDMA klynge til at køre MPI programmer


Lær, hvordan du konfigurerer en Linux RDMA klynge i Azure med [H-serien eller store beregninger A-serien FOS](virtual-machines-linux-a8-a9-a10-a11-specs.md) at køre parallelle programmer, der passerer MPI (Message Interface). I denne artikel indeholder trin til at forberede et Linux HPC billede til at køre Intel MPI på en klynge. Derefter skal installere du en klynge af FOS ved hjælp af dette billede og en af de RDMA-kompatible Azure VM størrelser (aktuelt H16r, H16mr, A8 eller A9). Bruge-klyngen køre MPI programmer, der kommunikerer effektivt via en lav ventetid, høj overførselshastighed netværk, der er baseret på remote direkte hukommelse access (RDMA) teknologi.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="cluster-deployment-options"></a>Klynge installationsindstillinger

Følgende er metoder, du kan bruge til at oprette en Linux RDMA klynge med eller uden en Opgavestyring.


* **Azure CLI scripts** - som vist senere i denne artikel bruge [Azure kommandolinjen](../xplat-cli-install.md) (CLI) til at oprette scripts installation af en klynge af RDMA-kompatible FOS. CLI i Service Management tilstand opretter klyngenoderne serielt i modellen Klassisk installation, så anvender mange Beregn noder kan tage flere minutter. For at aktivere netværksforbindelsen RDMA, når du bruger den klassiske implementeringsmodel skal du installere FOS i den samme skybaseret tjeneste.

* **Azure ressourcestyring skabeloner** - du kan også bruge implementeringsmodel ressourcestyring til at installere en klynge af RDMA-kompatible VM'er, der opretter forbindelse til netværket RDMA. Du kan [oprette din egen skabelon](../resource-group-authoring-templates.md), eller se [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/) til skabeloner, som Microsoft eller community'et til at anvende den ønskede løsning bidrager. Ressourcestyring skabeloner kan give en hurtig og pålidelig metode til at installere en Linux klynge. For at aktivere netværksforbindelsen RDMA, når du bruger implementeringsmodel ressourcestyring skal du installere FOS i det samme sæt tilgængelighed.

* **HPC Pack** – oprette en Microsoft HPC Pack klynge i Azure og derefter tilføje RDMA-kompatible Beregn noder, der kører en understøttet Linux-fordeling for at få adgang til RDMA netværket. Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-classic-model"></a>Eksempler på installation trin i klassisk model

Følgende trin viser, hvordan du bruger Azure CLI til at installere en SUSE Linux Enterprise Server (SLES) 12 SP1 HPC VM fra Azure Marketplace, tilpasse den og oprette et brugerdefineret billede, VM. Brug derefter billedet til scripts installation af en klynge af RDMA-kompatible FOS. 

>[AZURE.TIP]Bruge lignende trin til at installere en klynge af RDMA-kompatible FOS baseret på andre understøttede HPC billeder i Azure Marketplace. Nogle trin kan variere, som angivet. For eksempel er Intel MPI inkluderet og konfigureres i nogle af disse billeder. Og hvis du installerer en SLES 12 HPC VM i stedet for en SLES 12 SP1 HPC VM, skal du opdatere RDMA driverne. Du kan finde oplysninger [om A8, A9, A10, og A11 Beregn-intensivt forekomster](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).


### <a name="prerequisites"></a>Forudsætninger

*   **Klientcomputeren** – du har brug for en Mac, Linux eller Windows-baserede klient computer for at kommunikere med Azure. Disse trin forudsætter, at du bruger en Linux-klient.

*   **Azure abonnement** - Hvis du ikke har et abonnement, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter. Større klynger, kan du overveje et overkommelige abonnement eller andre Købsmuligheder. 

*   **VM størrelse tilgængelighed** - følgende forekomst størrelsen er i øjeblikket kan RDMA: H16r, H16mr, A8 og A9. Se [produkter, der er tilgængelige efter område](https://azure.microsoft.com/regions/services/) for tilgængelighed i Azure områder. 

*   **Kerner kvote** - det være nødvendigt at øge kvoten af kerner for at installere en klynge af Beregn-intensivt FOS. For eksempel skal du mindst 128 kerner, hvis du vil installere 8 A9 FOS, som vist i denne artikel. Dit abonnement kan også begrænse antallet kerner, du kan installere i visse VM størrelse familier, herunder H-serien. Hvis du vil have en kvote øge, [Åbn en anmodning om online kundesupport](../azure-supportability/how-to-create-azure-support-request.md) gratis. 

*   **Azure CLI** - [installere](../xplat-cli-install.md) Azure CLI og [oprette forbindelse til dit Azure abonnement](../xplat-cli-connect.md) fra klientcomputeren.


### <a name="step-1-provision-a-sles-12-sp1-hpc-vm"></a>Trin 1. Klargøre en SLES 12 SP1 HPC VM

Når du logger på Azure med Azure CLI, køre `azure config list` for at bekræfte, at resultatet viser Service Management tilstand. Hvis det ikke er, kan du angive tilstanden ved at køre denne kommando:


    azure config mode asm


Skriv følgende for at få vist alle de abonnementer, du har tilladelse til at bruge:


    azure account list

Den aktuelt aktive abonnement er identificeret med `Current` indstillet til `true`. Hvis dette abonnement ikke er det, du vil bruge til at oprette klyngen, skal du angive abonnementets relevante Id som den aktive abonnement:

    azure account set <subscription-Id>

For at se offentligt tilgængeligt SLES 12 SP1 HPC billederne i Azure skal køre en kommando, der ligner følgende, hvis din shell-miljøet understøtter **grep**:


    azure vm image list | grep "suse.*hpc"

Klargør nu en RDMA-kompatible VM med et SLES 12 SP1 HPC billede ved at køre en kommando, der ligner følgende:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

hvor

* Størrelse (A9 i dette eksempel) er et af de RDMA-kompatible VM størrelser.

* Eksterne SSH portnummeret (22 i dette eksempel, som er standard SSH) er et gyldigt portnummer. Det interne SSH portnummer er angivet til 22.

* Der oprettes en ny tjeneste i skyen i Azure område angivet af placeringen. Angiv en placering, hvor du vælger VM størrelsen er tilgængelig.

* SLES 12 SP1 billede navnet i øjeblikket kan være `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824` eller `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824` efter SUSE Prioritetssupport (yderligere gebyrer anvender).

### <a name="step-2-customize-the-vm"></a>Trin 2. Tilpasse VM

Når VM afsluttes klargøring SSH til VM med VM ekstern IP-adresse (eller DNS-navn), og den eksterne port tal du konfigureret og tilpasse den. Se, [hvordan du logge på et virtuelt kører Linux](virtual-machines-linux-mac-create-ssh-keys.md)for detaljer om forbindelse. Udføre kommandoer som den bruger, du konfigurerede på VM, medmindre adgang til roden er nødvendige for at fuldføre et trin.

>[AZURE.IMPORTANT]Microsoft Azure giver ikke adgang til roden til Linux FOS. For at få administratoradgang, når forbindelsen er oprettet som en bruger til VM skal køre kommandoer ved hjælp af `sudo`.

* **Opdateringer** – Installer opdateringer ved hjælp af **zypper**. Du kan også at installere NFS værktøjer. 

    >[AZURE.IMPORTANT]I en SLES 12 SP1 HPC VM anbefaler vi, at du ikke anvender kerneopdateringer, som kan medføre problemer med Linux RDMA drivere.

* **Intel MPI** - fuldføre installationen af Intel MPI på SLES 12 SP1 HPC VM ved at køre følgende kommando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

* **Lås hukommelse** - koder MPI For at låse den tilgængelige hukommelse til RDMA, tilføje eller ændre følgende indstillinger i filen /etc/security/limits.conf. (Du skal rodadgang til at redigere denne fil). 

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]Du kan også angive memlock til ubegrænset til testformål. For eksempel: `<User or group name>    hard    memlock unlimited`. Du kan finde yderligere oplysninger finder [Kendte bedste metoder til indstillingen låst hukommelsesstørrelse](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).

* **SSH taster til SLES FOS** - generere SSH taster til for at skabe tillid til din brugerkonto mellem noderne Beregn i SLES klynge, når du kører MPI job. (Hvis du har installeret en CentOS-baserede HPC VM, ikke følge dette trin. Se vejledningen senere i denne artikel for at konfigurere passwordless SSH tillid mellem klyngenoderne, når du tage et skærmbillede og installere klyngen). 

    Kør følgende kommando for at oprette SSH taster. Når du bliver bedt om input, skal du trykke på Enter for at generere de pågældende taster på standardplaceringen uden at angive en adgangskode.

        ssh-keygen

    Føje den offentlige nøgle til filen authorized_keys for kendte offentlige nøgler.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Redigere eller oprette "config"-filen i mappen ~/.ssh. Angiv IP-adresseområder for det private netværk, du vil bruge i Azure (10.32.0.0/16 i dette eksempel):

        host 10.32.0.*
        StrictHostKeyChecking no

    Du kan også listen hver VM i din klynge privat netværk IP-adressen på følgende måde:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]Konfiguration af `StrictHostKeyChecking no` kan oprette en potentiel sikkerhedsrisiko, når en bestemt IP-adresse eller et område ikke er angivet.

* **Programmer** - installere de programmer, du skal på denne VM eller foretage andre tilpasninger, før du tage et skærmbillede.

### <a name="step-3-capture-the-image"></a>Trin 3. Tage et skærmbillede

Hvis du vil tage et skærmbillede, først køre følgende kommando i Linux VM. Denne kommando deprovisions VM, men bevarer brugerkonti og SSH taster, du har angivet.

```
sudo waagent -deprovision
```

Kør følgende Azure CLI kommandoer for at tage et skærmbillede fra din computer. Se, [hvordan du registrere en klassisk Linux virtuel maskine som et billede](virtual-machines-linux-classic-capture-image.md) for at få oplysninger.  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Når du har kørt disse kommandoer, VM billedet hentes til brug og VM slettes. Nu har du den brugerdefinerede afbildning, der er klar til at installere en klynge.

### <a name="step-4-deploy-a-cluster-with-the-image"></a>Trin 4. Installere en klynge med et billede

Ændre følgende fest script med værdier, der er relevante for dit miljø, og kør den fra din computer. Fordi Azure installerer FOS serielt i modellen Klassisk installation, kræver det et par minutter at installere den 8 A9 FOS foreslås i dette script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Overvejelser i forbindelse med en CentOS HPC klynge

Hvis du vil konfigurere en klynge, der er baseret på et af billederne i Azure Marketplace i stedet for SLES 12 CentOS-baserede HPC for HPC, skal du benytte følgende generelle i det forrige afsnit. Bemærk følgende forskelle, når du klargør og konfigurerer VM:

1. Intel MPI er allerede installeret på en VM klargjort fra et CentOS-baserede HPC billede. 

2. Indstillinger for Lås hukommelse der allerede er tilføjet i de VM /etc/security/limits.conf fil.

2. Generer ikke SSH taster på VM du klargør til hentning. Vi anbefaler i stedet til at konfigurere bruger-baseret godkendelse, når du installerer cluser. Se følgende afsnit.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Konfigurere passwordless SSH Hav tillid til i klyngen

På en CentOS-baserede HPC klynge, der er to metoder til at oprette et tillidsforhold mellem noderne Beregn: host-baseret godkendelse og bruger-baseret godkendelse. Host-baseret godkendelse er uden for omfanget af denne artikel og Generelt skal udføres via et lokalnummer script under installationen. Bruger-baseret godkendelse er praktisk til at oprette et tillidsforhold efter installation og kræver, at oprettelsen og deling af SSH taster mellem noderne Beregn i klyngen. Denne metode kaldes også passwordless SSH logon og er påkrævet, når du kører MPI job. 

Et eksempel på script bidraget fra community'et er tilgængelig på [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) til at aktivere nemt brugergodkendelse på en CentOS-baserede HPC klynge. Hente og bruge dette script at benytte følgende fremgangsmåde. Du kan også ændre dette script eller bruge en anden metode til at etablere passwordless SSH godkendelse mellem noderne klynge Beregn.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
For at køre scriptet, skal du vide præfikset for dit undernet IP-adresser. Få præfikset ved at køre følgende kommando på en af klyngenoderne. Din output skal ligne 10.1.3.5, og præfikset er 10.1.3 del.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Nu køre scriptet ved hjælp af tre parametre: almindelige brugernavnet på noderne Beregn, almindelige adgangskoden for den pågældende bruger på noderne Beregn og undernetpræfikset, der blev returneret fra den forrige kommando.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Dette script gør følgende:

* Opretter en mappe på noden host med navnet .ssh, som kræves til passwordless logon. 
* Opretter en konfigurationsfil i mappen .ssh, der får passwordless login tillade login fra en node i klyngen. 
* Opretter filer, der indeholder nodenavne og node IP-adresser for alle noder i klyngen. Disse filer er tilbage, når du har kørt scriptet til senere brug. 
* Opretter et private og offentlige vigtige par for hver enkelt klyngenode, herunder noden host og opretter poster i filen authorized_keys.

>[AZURE.WARNING]Kører dette script kan oprette en potentiel sikkerhedsrisiko. Sørg for, at den offentlige vigtige oplysninger i ~/.ssh ikke er distribueret.


## <a name="configure-intel-mpi"></a>Konfigurere Intel MPI

Hvis du vil køre MPI programmer på Azure Linux RDMA, skal du konfigurere bestemte miljøvariabler, der er specifikke for Intel MPI. Her er et eksempel på fest script til at konfigurere variablerne, der kræves for at køre et program. Ændre stien til mpivars.sh efter behov for din installation af Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Formatet af filen host er som følger. Tilføje en linje for hver enkelt node i din klynge. Angiv private IP-adresser fra VNet definerede tidligere, ikke DNS-navne. For eksempel til to værter med IP-adresser 10.32.0.1 og 10.32.0.2 indeholder filen følgende:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Køre MPI på en grundlæggende klynge med to noder

Hvis du ikke allerede har gjort det, du først konfigurere miljøet for Intel MPI. 

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-a-simple-mpi-command"></a>Køre en simpel MPI-kommando

Køre en simpel MPI kommando på en af Beregn noderne til at vise, MPI er installeret korrekt og kan kommunikere mellem mindst to beregne noder. Følgende kommando for **mpirun** kører kommandoen **hostname** på to noder.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Din output skal indeholde navnene på alle noder, som du overføres som input til `-hosts`. For eksempel returnerer en **mpirun** -kommando med to noder output ligner følgende:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Køre en MPI benchmark

Følgende kommando for Intel MPI kører en pingpong benchmark for at bekræfte klynge konfiguration og forbindelse til netværket RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

På en arbejde klynge med to noder, skal du se output, der svarer til følgende. På Azure RDMA netværket, du forvente ventetid på eller under 3 mikrosekunder for meddelelse størrelse op til 512 byte.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Næste trin

* Prøv implementering og kører din Linux MPI programmer på din Linux klynge.

* Se [dokumentationen til Intel MPI bibliotek](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) for at få vejledning på Intel MPI.

* Prøv en [Hurtig start skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) til at oprette en Intel blank klynge ved hjælp af en CentOS-baserede HPC billede. Yderligere oplysninger finder du denne [blogindlæg](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
