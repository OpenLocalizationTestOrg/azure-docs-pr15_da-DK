<properties
 pageTitle="NAMD med Microsoft HPC Pack på Linux FOS | Microsoft Azure"
 description="Installere en Microsoft HPC Pack klynge på Azure og køre en NAMD simulering med charmrun på flere Linux Beregn noder"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="10/13/2016"
 ms.author="danlep"/>

# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Køre NAMD med Microsoft HPC Pack på Linux Beregn noder i Azure

I denne artikel beskrives en metode til at køre en Linux (HPC) high-performance computing arbejdsbyrde på Azure virtuelle computere. Her kan du konfigurere en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klynge på Azure med Linux Beregn noder og køre en [NAMD](http://www.ks.uiuc.edu/Research/namd/) simulering for at beregne og visualisere strukturen i et stort biomolecular system.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** er en parallelle molekylære dynamics pakke, der er udviklet til høj ydeevne simulering af store biomolecular systemer, der indeholder op til millioner af atomer (for Nanoscale molekylære Dynamics program). Eksempler på disse systemer omfatter virus, celle strukturer og store proteiner. NAMD skaleres til hundredvis af borekerner til typisk simulering og til mere end 500.000 borekerner til de største simulering.

* **Microsoft HPC Pack** indeholder funktioner for at køre store HPC og parallelle programmer i klynger af lokale computere eller Azure virtuelle computere. Oprindeligt blev udviklet som en løsning på Windows HPC arbejdsbelastninger, HPC Pack nu understøtter kører Linux HPC programmer på Linux beregne node FOS implementeret i en HPC Pack klynge. Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md) for at få en introduktion.

For andre indstillinger til at køre Linux HPC arbejdsmængder i Azure, skal du se [tekniske ressourcer for batchen og high-performance computing](../batch/batch-hpc-solutions.md).




## <a name="prerequisites"></a>Forudsætninger

* **HPC Pack klynge med Linux beregne noder** - installere en HPC Pack klynge med Linux Beregn noder på Azure ved hjælp af en [Azure ressourcestyring skabelon](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) eller et [Azure PowerShell-script](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md) til forudsætninger og trinnene for hver indstilling. Hvis du vælger indstillingen PowerShell-script installation, skal du se eksempel på konfigurationsfil i eksempelfiler i slutningen af denne artikel. Denne fil konfigurerer en Azure-baserede HPC Pack klynge bestående af en Windows Server 2012 R2 hoved node og fire størrelse store CentOS 6.6 Beregn noder. Tilpasse denne fil, som det er nødvendigt for dit miljø.


* **NAMD software og selvstudium filer** – Download NAMD softwaren til Linux fra webstedet [NAMD](http://www.ks.uiuc.edu/Research/namd/) (tilmelding kræves). I denne artikel er baseret på NAMD version 2.10 og bruger arkivet [Linux-x86_64 (64-bit Intel/AMD med Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310) . Også hente [filer til Selvstudium i NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Overførslerne er .tar filer, og du skal bruge et Windows til at udtrække filerne på Klyngenoden hoved. Hvis du vil udtrække filerne, skal du følge vejledningen senere i denne artikel. 

* **VMD** (valgfrit) – til at få vist resultaterne af NAMD tingene, hente og installere programmet molekylære visualisering [VMD](http://www.ks.uiuc.edu/Research/vmd/) på en computer efter eget valg. Den aktuelle version er 1.9.2. Se VMD hente websted for at komme i gang.  


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Konfigurere fælles tillid mellem Beregn noder
Køre et job i tværs af noder på flere Linux noder kræver, at noderne, der har tillid til hinanden (ved **rsh** eller **ssh**). Når du opretter HPC Pack klynge med Microsoft HPC Pack IaaS installation script, konfigurerer scriptet automatisk permanent fælles tillid til den administratorkonto, du angiver. Ikke-brugere administratorer du opretter i den klynge domæne, skal du konfigurere midlertidige fælles tillid mellem knuderne, når et job er allokeret til dem. Derefter destroy relationen, når jobbet er fuldført. For at gøre dette for hver bruger, du give en RSA vigtige par til klynge som HPC Pack bruger til at oprette tillidsforholdet. Følg vejledningen.

### <a name="generate-an-rsa-key-pair"></a>Generere en RSA vigtige par
Det er nemt at oprette en vigtige RSA-par, som indeholder en offentlig nøgle og en privat nøgle, ved at køre kommandoen Linux **ssh keygen** .

1.  Log på en Linux-computer.

2.  Kør følgende kommando:

    ```bash
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Tryk på **Enter** for at bruge standardindstillingerne, indtil kommandoen er fuldført. Angiv ikke en adgangskode. Når du bliver bedt om en adgangskode, skal du trykke på **Enter**.

    ![Generere en RSA vigtige par][keygen]

3.  Skift mappe til mappen ~/.ssh. Privat nøgle er gemt i id_rsa og den offentlige nøgle i id_rsa.pub.

    ![Private og offentlige nøgler][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Tilføje parret nøgle til HPC Pack klynge
1.  [Opret forbindelse ved Remote Desktop](virtual-machines-windows-connect-logon.md) til noden hoved VM bruger domænet legitimationsoplysninger leveres når du har installeret klynge (for eksempel hpc\clusteradmin). Du kan administrere klyngen fra noden hoved.

2. Bruge Windows Server standardprocedurer til at oprette en brugerkonto for domænet i den klynge Active Directory-domæne. For eksempel bruge værktøjet Active Directory-bruger og computere på noden hoved. Eksemplerne i denne artikel forudsætter, at du har oprettet et, der domænebruger med navnet hpcuser i hpclab domæne (hpclab\hpcuser).

3. Tilføj domænebrugeren til HPC Pack klynge som en bruger, klynge. Flere oplysninger under [tilføje eller fjerne klynge brugere](https://technet.microsoft.com/library/ff919330.aspx).

2.  Oprette en fil med navnet C:\cred.xml og kopiere RSA nøgledata til den. Du kan finde et eksempel i eksempelfiler i slutningen af denne artikel.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.  Åbn en kommandoprompt, og Skriv følgende kommando for at angive datatypen legitimationsoplysninger for kontoen hpclab\hpcuser. Du kan bruge parameteren **extendeddata** til at overføre navnet på filen C:\cred.xml du har oprettet for de vigtigste data.

    ```command
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Denne kommando er fuldført uden output. Når du har indstillet legitimationsoplysninger for de brugerkonti, skal du køre job, gemme filen cred.xml i et sikkert sted eller slette den.

5.  Husk at slette de pågældende taster, når du er færdig med at bruge dem, hvis du har oprettet parret RSA nøgle på en af dine Linux noderne. HPC Pack ikke konfigureret fælles Hav tillid til, hvis der er angivet en eksisterende id_rsa- eller id_rsa.pub fil.

>[AZURE.IMPORTANT] Vi anbefaler ikke køre et Linux-job som Klyngeadministrator af en på en delt klynge, fordi en sag, der er sendt af en administrator kører under kontoen rod på noderne Linux. Et job, der er sendt af en ikke-administrator bruger kører under en lokal Linux-brugerkonto med det samme navn som brugeren, job. I dette tilfælde konfigurerer HPC Pack fælles tillid for denne Linux bruger på tværs af alle de noder, der er tildelt sag. Du kan konfigurere Linux brugeren manuelt på noderne Linux før du kører jobbet eller HPC Pack opretter brugeren automatisk, når jobbet er afsendt. Hvis HPC Pack opretter brugeren, slettes HPC Pack det, når jobbet er fuldført. For at reducere sikkerhedsrisiko, fjernes de pågældende taster, når jobbet er fuldført på noderne.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Konfigurere et filshare for Linux noder

Nu konfigurere et filshare til små og mellemstore virksomheder, og oprette forbindelse til den delte mappe på alle Linux noder til at tillade Linux noder til at få adgang til NAMD filer med en fælles sti. Følgende er trin til at oprette forbindelse til en delt mappe på noden hoved. En del anbefales til salgsdistributioner som CentOS 6.6, som i øjeblikket ikke understøtter tjenesten Azure-fil. Hvis din Linux noder understøtter en Azure filshare, se, [hvordan du bruger Azure fillagring med Linux](../storage/storage-how-to-use-files-linux.md). For yderligere indstillingerne for fildeling med HPC Pack, kan du se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Oprette en mappe på noden hoved, og del den til alle ved at angive læse-og skriveadgang rettigheder. I dette eksempel \\ \\CentOS66HN\Namd er navnet på den mappe, hvor CentOS66HN er værtsnavnet på noden hoved.

2. Oprette en undermappe med navnet namd2 i den delte mappe. Opret en anden undermappe med navnet namdsample i namd2.

3. Udpak NAMD filerne i mappen ved hjælp af en Windows-versionen af **tjære** eller et andet Windows-værktøj, der kører på .tar Arkiver. 
    * Udtrække NAMD tjære arkiv til \\ \\CentOS66HN\Namd\namd2.
    
    * Udtrække de filer til selvstudium under \\ \\CentOS66HN\Namd\namd2\namdsample.

4. Åbn et vindue i Windows PowerShell og Kør følgende kommandoer for at oprette forbindelse til den delte mappe på noderne Linux.

    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Den første kommando opretter en mappe med navnet /namd2 på alle noder i gruppen LinuxNodes. Den anden kommando opretter forbindelse til den delte mappe //CentOS66HN/Namd/namd2 til mappen med dir_mode og file_mode angivet til 777. Det *brugernavn* og *adgangskode* i kommandoen skal være legitimationsoplysninger for en bruger på noden hoved.

>[AZURE.NOTE]Den "\`" symbolet i den anden kommando er et escape-symbolet til PowerShell. "\`," betyder, at det "," (kommategn) er en del af kommandoen.


## <a name="create-a-bash-script-to-run-a-namd-job"></a>Oprette en fest script for at køre en NAMD job

NAMD tingene skal have *en nodelistefilen for **charmrun** til at bestemme antallet af knuder skal bruges, når du starter NAMD processer* . Du bruger en fest script, der opretter nodelistefilen og kører **charmrun** med denne nodelistefilen. Derefter kan du sende en NAMD sag i HPC klynge Manager, der kalder dette script.

Brug af et tekstredigeringsprogram efter eget valg, oprette en fest script i mappen /namd2, der indeholder programfiler NAMD og kald den hpccharmrun.sh. Kopiere det eksempel hpccharmrun.sh script, der er angivet i slutningen af denne artikel for en hurtig købskvittering konceptet, og gå til [Send en NAMD sag](#submit-a-namd-job).

>[AZURE.TIP] Gem dit script som en tekstfil med Linux linje slutningen (kun LF, ikke CR LF). Dette sikrer, at den kører korrekt på noderne Linux.

Følgende er nogle oplysninger om dette fest script gør. 

1.  Definere nogle variabler.

    ```bash
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.  Få nodeoplysninger fra miljøvariabler. $NODESCORES gemmer en liste over delte ord fra $CCP_NODES_CORES. $COUNT er størrelsen på $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    Formatet for variablen $CCP_NODES_CORES er som følger:

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Denne variabel viser det samlede antal noder, nodenavne og antal kerner på hver node, der er allokeret til jobbet. Hvis jobbet skal 10 kerner for at køre, er værdien af $CCP_NODES_CORES for eksempel minder om:

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.  Hvis variablen $CCP_NODES_CORES ikke er angivet, kan du starte **charmrun** direkte. (Dette skal kun udføres, når du kører denne script direkte på din Linux noder.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
        # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
        #echo ${CHARMRUN} $*
        ${CHARMRUN} $*
    ```

4.  Eller Opret en nodelistefilen for **charmrun**.

    ```
    else
        # Create the nodelist file
        NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

        # Write the head line
        echo "group main" > ${NODELIST_PATH}

        # Get every node name and number of cores and write into the nodelist file
        I=1
        while [ ${I} -lt ${COUNT} ]
        do
            echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
            let "I=${I}+2"
        done
```
5.  Køre **charmrun** med nodelistefilen, få returnerede status, og fjern nodelistefilen i slutningen.

    ${CCP_NUMCPUS} er en anden miljøvariablen, der er angivet af noden HPC Pack hoved. Gemmer antallet samlede kerner, der er tildelt denne sag. Vi bruger den til at angive antallet processer for charmrun.

    ```
    # Run charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
    fi

    ```
6.  Afslut med **charmrun** returnerede status.

    ```
    exit ${RTNSTS}
    ```



Følgende er oplysningerne i nodelistefilen, som scriptet genererer:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Eksempel:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Sende et NAMD-job

Nu er du klar til at sende en NAMD sag i HPC klynge Manager.

1.  Oprette forbindelse til din klynge hoved node, og start HPC klynge Manager.

2.  I **Ressourcestyring**, sikre, at Linux Beregn knuderne i tilstanden **Online** . Hvis det er tilfældet, kan du markere dem og klikke på **Få Online**.

2.  **Administration af udskriftsjob**, klik på **Nyt Job**.

3.  Angiv et navn til jobbet som *hpccharmrun*.

    ![Nyt HPC job][namd_job]

4.  Vælg typen ressource som **Node** på siden **Oplysninger om Job** under **Job ressourcer**, og angiv **Minimum** til 3. , vi kørslen på tre Linux noder og hver node har fire kerner.

    ![Jobbet ressourcer][job_resources]

5. Klik på **Rediger opgaver** i venstre navigationsrude, og klik derefter på **Tilføj** for at føje en opgave til jobbet.    


6. På siden **Detaljer om opgaven og i/o-omdirigering** , skal du angive følgende værdier:

    * **Kommandolinje** -
`/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

        >[AZURE.TIP] Den foregående kommandolinje er en enkelt kommando uden linjeskift. Det ombrydes for at stå på flere linjer under **kommandolinjen**.

    * **Arbejdsmappe** - /namd2

    * **Minimum** - 3

    ![Opgavedetaljer][task_details]

    >[AZURE.NOTE] Du angiver arbejdsmappen her, fordi **charmrun** forsøger at gå til den samme arbejdsmappe på hver node. Hvis den aktuelle mappe ikke er indstillet, starter HPC Pack kommandoen i et vilkårligt navn mappe, der er oprettet på en af noderne Linux. Dette får vist følgende fejlmeddelelse på de andre noder: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` for at undgå dette problem skal du angive en mappesti, der kan åbnes af alle noder som den aktuelle mappe.

5.  Klik på **OK** , og klik derefter på **Send** for at køre dette job.

    Som standard sender HPC Pack jobbet som din aktuelle er logget på konto. En dialogboks du muligvis bedt om at angive det brugernavn og adgangskode, når du klikker på **Send**.

    ![Jobbet legitimationsoplysninger][creds]

    Under visse omstændigheder husker HPC Pack brugeroplysninger, du indtaster før og ikke viser denne dialogboks. Hvis HPC Pack vist igen, skal du skrive følgende kommando fra en kommandoprompt og derefter sende jobbet.

    ```command
    hpccred delcreds
    ```

6.  Jobbet tager flere minutter at afslutte.

7.  Finde tingene loggen på \\ <headnodeName>\Namd\namd2\namd2_hpccharmrun.log og outputfiler i \\ <headnodeName>\Namd\namd2\namdsample\1-2-sphere\.

8.  Du kan også starte VMD for at få vist dine job resultater. Trin til at visualisere NAMD output filer (i dette tilfælde ubiquitin protein molekyle i en vand kugle) er ikke medtaget i denne artikel. Du kan finde oplysninger i [NAMD selvstudium](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .

    ![Jobbet resultater][vmd_view]

## <a name="sample-files"></a>Eksempelfiler

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Eksempel på XML-konfigurationsfil til installation af klynge ved hjælp af PowerShell-script

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

### <a name="sample-credxml-file"></a>Eksempel på cred.xml-fil

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```

### <a name="sample-hpccharmrunsh-script"></a>Eksempel på hpccharmrun.sh-script

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
    #echo ${CHARMRUN} $*
    ${CHARMRUN} $*
else
    # Create the nodelist file
    NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    # Write the head line
    echo "group main" > ${NODELIST_PATH}

    # Get every node name & cores and write into the nodelist file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the charmrun with nodelist arg
    #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
    ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png
