<properties
 pageTitle="Køre OpenFOAM med HPC Pack på Linux FOS | Microsoft Azure"
 description="Installere en Microsoft HPC Pack klynge på Azure og køre en OpenFOAM tingene på flere Linux Beregn noder på tværs af et netværk med RDMA."
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
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Køre OpenFoam med Microsoft HPC Pack på en Linux RDMA klynge i Azure

I denne artikel beskrives en metode til at køre OpenFoam på Azure virtuelle computere. Her kan installere du en Microsoft HPC Pack klynge med Linux Beregn noder på Azure og køre en [OpenFoam](http://openfoam.com/) ledige med Intel MPI. Du kan bruge RDMA-kompatible Azure FOS til noderne Beregn, så noderne Beregn kommunikere via Azure RDMA netværket. Andre indstillinger til at køre OpenFoam i Azure omfatter fuldt konfigureret kommercielle billeder, der findes på markedet, såsom Ubercloud's [OpenFoam 2.3 på CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/), og ved at køre på [Azure batchen](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (for åbne felt handlingen og Strengmanipulation) er et open source-computational flydende dynamics (CFD) software, der anvendes af mange i teknisk og videnskabelige i både kommercielle og akademisk organisationer. Den indeholder værktøjer til meshing, især snappyHexMesh, en parallelized mesher for komplekse CAD-geometrier og for før- og efterbehandling. Næsten alle processer køre parallelt, så brugerne kan udnytte computerens hardware til rådighed.  

Microsoft HPC Pack indeholder funktioner for at køre store HPC og parallelle programmer, herunder MPI programmer, på klynger af virtuelle Microsoft Azure-computere. HPC Pack understøtter også kører Linux HPC programmer på Linux beregne node FOS implementeret i en HPC Pack klynge. Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md) for at få en introduktion til brug af Linux Beregn noder med HPC Pack.

>[AZURE.NOTE] I denne artikel viser, hvordan du kører en Linux MPI arbejdsbyrde med HPC Pack. Det antages, at du har et vist kendskab med Linux systemadministration og med kører MPI arbejdsbelastninger på Linux klynger. Hvis du bruger versioner af MPI og OpenFOAM forskellige fra dem vist i denne artikel, kan det være nødvendigt at ændre nogle ting, installation og konfiguration. 

## <a name="prerequisites"></a>Forudsætninger

*   **HPC Pack klynge med RDMA-kompatible Linux beregne noder** - installere en HPC Pack klynge med størrelse A8, A9, H16r eller H16rm Linux Beregn noder ved hjælp af en [Azure ressourcestyring skabelon](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) eller et [Azure PowerShell-script](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Se [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md) til forudsætninger og trinnene for hver indstilling. Hvis du vælger indstillingen PowerShell-script installation, skal du se eksempel på konfigurationsfil i eksempelfiler i slutningen af denne artikel. Brug denne konfiguration til at installere en Azure-baserede HPC Pack klynge bestående af en størrelse A8 Windows Server 2012 R2 hoved node og 2 størrelse A8 SUSE Linux Enterprise Server 12 Beregn noder. Erstatte relevante værdier for dit abonnement og tjenesten navne. 

    **Yderligere værd at vide**

    *   Se [om H-serien og Beregn-intensivt A-serien FOS](virtual-machines-windows-a8-a9-a10-a11-specs.md)for Linux RDMA netværk forudsætninger i Azure.

    *   Hvis du bruger indstillingen Powershell-script installation, Installer alle Linux Beregn noderne inden for en skybaseret tjeneste til at bruge netværksforbindelse RDMA.

    *   Oprette forbindelse ved SSH til at udføre yderligere administrative opgaver efter implementering noderne Linux. Finde forbindelsesoplysningerne SSH for hver Linux VM i portalen Azure.  
        
*   **Intel MPI** - køres OpenFOAM SLES 12 HPC beregne noder i Azure, skal du installere Intel MPI bibliotek 5 runtime fra [Intel.com websted](https://software.intel.com/en-us/intel-mpi-library/). (En Intel-MPI 5 er forudinstalleret på CentOS-baserede HPC billeder).  Hvis det er nødvendigt, beregne Installer Intel MPI på din Linux på et senere tidspunkt noder. Følg linket i bekræftelse via mail for at forberede dette trin, når du har registreret med Intel, til den relaterede webside. Derefter skal du kopiere linket download for filen .tgz for den relevante version af Intel MPI. I denne artikel er baseret på Intel MPI version 5.0.3.048.

*   **OpenFOAM kilde Pack** – Download softwaren til OpenFOAM kilde Pack for Linux fra [OpenFOAM Foundation-websted](http://openfoam.org/download/2-3-1-source/). I denne artikel er baseret på kilde Pack version 2.3.1, downloades som OpenFOAM 2.3.1.tgz. Følg vejledningen senere i denne artikel for at Pak og samle OpenFOAM på noderne Linux Beregn.

*   **EnSight** (valgfrit) - at få vist resultaterne af din OpenFOAM simulering, hente og installere programmet [EnSight](https://www.ceisoftware.com/download/) visualisering og analyse. Oplysninger om licenser og download er på webstedet EnSight.


## <a name="set-up-mutual-trust-between-compute-nodes"></a>Konfigurere fælles tillid mellem Beregn noder

Køre et job i tværs af noder på flere Linux noder kræver, at noderne, der har tillid til hinanden (ved **rsh** eller **ssh**). Når du opretter HPC Pack klynge med Microsoft HPC Pack IaaS installation script, konfigurerer scriptet automatisk permanent fælles tillid til den administratorkonto, du angiver. Ikke-brugere administratorer du opretter i den klynge domæne, du skal konfigurere midlertidige fælles tillid mellem knuderne, når et job er allokeret til dem, og destroy relationen, når jobbet er fuldført. For at oprette tillid for hver bruger skal du give en RSA vigtige par til den klynge, der bruger HPC Pack til dette tillidsforhold.

### <a name="generate-an-rsa-key-pair"></a>Generere en RSA vigtige par

Det er nemt at oprette en vigtige RSA-par, som indeholder en offentlig nøgle og en privat nøgle, ved at køre kommandoen Linux **ssh keygen** .

1.  Log på en Linux-computer.

2.  Kør følgende kommando:

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Tryk på **Enter** for at bruge standardindstillingerne, indtil kommandoen er fuldført. Angiv ikke en adgangskode. Når du bliver bedt om en adgangskode, skal du trykke på **Enter**.

    ![Generere en RSA vigtige par][keygen]

3.  Skift mappe til mappen ~/.ssh. Privat nøgle er gemt i id_rsa og den offentlige nøgle i id_rsa.pub.

    ![Private og offentlige nøgler][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Tilføje parret nøgle til HPC Pack klynge
1.  Oprette forbindelse til din hoved node med administratorkontoen HPC Pack (administratorkontoen du konfigurere, når du har kørt scriptet installation) Fjernskrivebord.

2. Bruge Windows Server standardprocedurer til at oprette en brugerkonto for domænet i den klynge Active Directory-domæne. For eksempel bruge værktøjet Active Directory-bruger og computere på noden hoved. Eksemplerne i denne artikel forudsætter, at du har oprettet et, der domænebruger med navnet hpclab\hpcuser.

3.  Oprette en fil med navnet C:\cred.xml og kopiere RSA nøgledata til den. Et eksempel på cred.xml-fil er i slutningen af denne artikel.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.  Åbn en kommandoprompt, og Skriv følgende kommando for at angive datatypen legitimationsoplysninger for kontoen hpclab\hpcuser. Du kan bruge parameteren **extendeddata** til at overføre navnet på C:\cred.xml fil du har oprettet for de vigtigste data.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Denne kommando er fuldført uden output. Når du har indstillet legitimationsoplysninger for de brugerkonti, skal du køre job, gemme filen cred.xml i et sikkert sted eller slette den.

5.  Husk at slette de pågældende taster, når du er færdig med at bruge dem, hvis du har oprettet parret RSA nøgle på en af dine Linux noderne. Hvis HPC Pack finder en eksisterende id_rsa- eller id_rsa.pub fil, det ikke konfigurere fælles sikkerhed og rettighedsadministration.

>[AZURE.IMPORTANT] Vi anbefaler ikke køre et Linux-job som Klyngeadministrator af en på en delt klynge, fordi en sag, der er sendt af en administrator kører under kontoen rod på noderne Linux. Et job, der er sendt af en ikke-administrator bruger kører dog under en lokal Linux-brugerkonto med det samme navn som brugeren, job. I dette tilfælde konfigurerer HPC Pack fælles tillid for denne Linux bruger på tværs af noderne allokeret til jobbet. Du kan konfigurere Linux brugeren manuelt på noderne Linux før du kører jobbet eller HPC Pack opretter brugeren automatisk, når jobbet er afsendt. Hvis HPC Pack opretter brugeren, slettes HPC Pack det, når jobbet er fuldført. Hvis du vil reducere trusler, fjerner HPC Pack tasterne efter job fuldførelse.

## <a name="set-up-a-file-share-for-linux-nodes"></a>Konfigurere et filshare for Linux noder

Opret nu en standard små og mellemstore virksomheder share på en mappe på noden hoved. Hvis du vil tillade Linux noder til at få adgang til programfiler med en fælles sti, oprette forbindelse til den delte mappe på noderne Linux. Hvis du vil, kan du bruge en anden fildeling indstillingen del en Azure filer - anbefales til mange scenarier- eller en NFS-share. Se fildeling oplysninger og detaljeret vejledning i at [komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.  Oprette en mappe på noden hoved, og del den til alle ved at angive læse-og skriveadgang rettigheder. For eksempel dele C:\OpenFOAM på noden hoved som \\ \\SUSE12RDMA HN\OpenFOAM. Her er *SUSE12RDMA HN* værtsnavnet for noden hoved.

2.  Åbne et vindue i Windows PowerShell og køre følgende kommandoer:

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Den første kommando opretter en mappe med navnet /openfoam på alle noder i gruppen LinuxNodes. Den anden kommando opretter forbindelse til den delte mappe //SUSE12RDMA-HN/OpenFOAM på noderne Linux med dir_mode og file_mode angivet til 777. Det *brugernavn* og *adgangskode* i kommandoen skal være legitimationsoplysninger for en bruger på noden hoved.

>[AZURE.NOTE]Den "\`" symbolet i den anden kommando er et escape-symbolet til PowerShell. "\`," betyder, at det "," (kommategn) er en del af kommandoen.

## <a name="install-mpi-and-openfoam"></a>Installere MPI og OpenFOAM

Hvis du vil køre OpenFOAM som et MPI job på RDMA netværket, skal du samle OpenFOAM med Intel MPI biblioteker. 

Først skal køre flere **clusrun** kommandoer til at installere Intel MPI biblioteker (hvis det ikke allerede er installeret) og OpenFOAM på din Linux noder. Brug hoved node del konfigureret tidligere til at dele installationsfiler mellem noderne Linux.

>[AZURE.IMPORTANT]Disse installation og kompilerer trin er eksempler. Du har brug for nogle kendskab til Linux systemadministration til at sikre, at afhængige compilere og biblioteker er installeret korrekt. Du kan være nødvendigt at ændre visse miljøvariabler eller andre indstillinger for dine versioner af Intel MPI og OpenFOAM. Yderligere oplysninger finder du [Intel MPI bibliotek til Linux installationsvejledningen](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) og [Installation af OpenFOAM kilde](http://openfoam.org/download/2-3-1-source/) for dit miljø.


### <a name="install-intel-mpi"></a>Installere Intel MPI

Gemme de hentede installationspakken for Intel MPI (l_mpi_p_5.0.3.048.tgz i dette eksempel) i C:\OpenFoam på noden hoved så noderne Linux kan få adgang til denne fil fra /openfoam. Kør derefter **clusrun** for at installere Intel MPI bibliotek på alle noderne Linux.

1.  Følgende kommandoer kopiere installationspakken og Udpak til /opt/intel på hver node.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  For at installere Intel MPI bibliotek uovervåget skal du bruge en silent.cfg fil. Du kan finde et eksempel i eksempelfiler i slutningen af denne artikel. Placere denne fil i den delte mappe /openfoam. Du kan finde oplysninger om filen silent.cfg [Intel MPI biblioteket for at se installationsvejledningen til Linux - uovervåget Installation](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).

    >[AZURE.TIP]Sørg for, at du gemmer filen som en tekstfil med Linux silent.cfg linje slutningen (kun LF, ikke CR LF). Dette trin sikrer, at den kører korrekt på noderne Linux.

3.  Installere Intel MPI bibliotek i baggrunden.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### <a name="configure-mpi"></a>Konfigurere MPI

Til test, skal du føje følgende linjer til /etc/security/limits.conf på hver af Linux noderne:


    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Genstart noderne Linux, når du opdaterer filen limits.conf. For eksempel bruge følgende kommando for **clusrun** :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Når du har genstartet, sikre, at den delte mappe er tilsluttet som /openfoam.

### <a name="compile-and-install-openfoam"></a>Samle og installere OpenFOAM

Gemme de hentede installationspakken til OpenFOAM kilde Pack (OpenFOAM-2.3.1.tgz i dette eksempel) til C:\OpenFoam på noden hoved, så noderne Linux kan få adgang til denne fil fra /openfoam. Kør derefter **clusrun** kommandoer til at samle OpenFOAM på alle Linux noderne.


1.  Oprette en mappe /opt/OpenFOAM på hver Linux node, kopiere pakken kilde til denne mappe og udtrække den der.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
    
    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Hvis du vil samle OpenFOAM med Intel MPI bibliotek, du først konfigurere nogle miljøvariabler til både Intel MPI og OpenFOAM. Brug en fest script kaldet settings.sh til at angive variablerne. Du kan finde et eksempel i eksempelfiler i slutningen af denne artikel. Placer filen (gemmes med Linux linje slutningen) i den delte mappe /openfoam. Denne fil indeholder også indstillinger for den MPI og OpenFOAM eksekverbar kode, som du senere bruge til at køre en OpenFOAM job.

3. Installere afhængige pakker, der kræves til at samle OpenFOAM. Du muligvis først føje et lager afhængigt af din Linux-fordeling. Køre **clusrun** kommandoer ligner følgende:

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Hvis det er nødvendigt, SSH til de enkelte Linux noder til at køre kommandoer til at bekræfte, at de kører korrekt.

4.  Kør følgende kommando for at samle OpenFOAM. Kompilering processen tager lidt tid at fuldføre og genererer en stor mængde logoplysninger til standard output, så Brug indstillingen **/ interleaved** til at vise output interleaved.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]Den "\`" symbol i kommandoen er et escape-symbolet til PowerShell. "\`&" betyder, at det "&" er en del af kommandoen.

## <a name="prepare-to-run-an-openfoam-job"></a>Forberede dig på at køre en OpenFOAM job

Nu er du klar til at køre en MPI sagen med navnet sloshingTank3D, som er en af OpenFoam prøverne på to Linux noder. 

### <a name="set-up-the-runtime-environment"></a>Konfigurere runtime-miljø

For at konfigurere runtime-miljøer til MPI og OpenFOAM på noderne Linux skal du køre følgende kommando i et Windows PowerShell-vindue på noden hoved. (Denne kommando er gyldige for SUSE Linux kun).

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Klargøre eksempeldata

Brug dialogboksen hoved node del, du tidligere har konfigureret til at dele filer mellem noderne Linux (tilsluttet som /openfoam).

1.  SSH til en af dine Linux beregne noder.

2.  Kør følgende kommando for at konfigurere OpenFOAM runtime-miljø, hvis du ikke allerede har gjort dette.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Kopiere eksemplet sloshingTank3D til den delte mappe, og gå til den.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Når du bruger standardparametrene i dette eksempel, kan det tage mange minutter at køre, så du måske ændre nogle af parametrene for at gøre det hurtigere. Et enkelt valg er at ændre tid trin variabler deltaT og writeInterval i filen system/controlDict. Denne fil gemmer alle indtastede data om kontrol af tids- og læse og skrive løsning data. For eksempel kan du ændre værdien af deltaT fra 0,05 til 0,5 og værdien af writeInterval fra 0,05 til 0,5.

    ![Ændre trin variabler][step_variables]

5.  Angive ønskede værdier til variablerne i filen system/decomposeParDict. I dette eksempel bruges to Linux noder hver med 8 kerner, så angivet numberOfSubdomains-16 og n af hierarchicalCoeffs til (1 1 16), hvilket betyder, at køre OpenFOAM parallelt med 16 processer. Du kan finde flere oplysninger, se [OpenFOAM brugervejledningen: 3,4 programmer, som kører parallelt](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Opdele processer][decompose]

6.  Kør følgende kommandoer fra mappen sloshingTank3D til at forberede eksempeldataene.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  På noden hoved, skal du se eksempeldatafilerne kopieres til C:\OpenFoam\sloshingTank3D. (C:\OpenFoam er den delte mappe på noden hoved).

    ![Datafiler på noden hoved][data_files]

### <a name="host-file-for-mpirun"></a>Host fil til mpirun

I dette trin skal oprette du en host-fil (en liste over Beregn noder) som kommandoen **mpirun** bruger.

1.  Opret en fil med navnet hostfile under /openfoam, så denne fil kan ringes til /openfoam/hostfile på alle Linux noder på en af noderne Linux.

2.  Skriv dine Linux nodenavne i denne fil. I dette eksempel indeholder filen følgende navne:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Du kan også oprette denne fil på C:\OpenFoam\hostfile på noden hoved. Hvis du vælger denne indstilling, skal du gemme den som en tekstfil med Linux linje slutningen (kun LF, ikke CR LF). Dette sikrer, at den kører korrekt på noderne Linux.

    **Fest script slikpapir**

    Hvis du har mange Linux noder, og du vil tingene køres kun nogle af dem, er det ikke en god ide at bruge en fast host-fil, fordi du ikke ved, hvilke knuder der allokeres til dit arbejde. I dette tilfælde, skrive en fest script slikpapir til **mpirun** til at oprette filen host automatisk. Du kan finde et eksempel fest script slikpapir kaldet hpcimpirun.sh i slutningen af denne artikel og gemme den som /openfoam/hpcimpirun.sh. Dette eksempel script gør følgende:

    1.  Indstiller miljøvariabler for **mpirun**, og nogle addition kommandoparametre til at køre kørslen MPI via RDMA netværket. I dette tilfælde, angiver det følgende variabler:

        *   I_MPI_FABRICS = shm:dapl
        *   I_MPI_DAPL_PROVIDER = af en-v2-ib0
        *   I_MPI_DYNAMIC_CONNECTION = 0

    2.  Opretter en host fil efter miljøet variable $CCP_NODES_CORES, som er angivet af noden HPC hoved, når jobbet er aktiveret.

        Formatet af $CCP_NODES_CORES følger dette mønster:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        hvor

        * `<Number of nodes>`-antallet af knuder, der er tildelt denne sag.  
        
        * `<Name of node_n_...>`-navnet på hver node, der er tildelt denne sag.
        
        * `<Cores of node_n_...>`-antallet af kerner på den node, der er tildelt denne sag.

        Eksempelvis hvis jobbet skal to noder til at køre, ligner $CCP_NODES_CORES
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.  Kalder kommandoen **mpirun** og føjer to parametre til kommandolinjen.

        * `--hostfile <hostfilepath>: <hostfilepath>`-stien til filen host scriptet opretter

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}`-en miljøvariablen, der er angivet af HPC Pack hoved noden, som lagrer antallet samlede kerner, der er tildelt denne sag. I dette tilfælde angiver antallet af processer for **mpirun**.


## <a name="submit-an-openfoam-job"></a>Sende en OpenFOAM job

Nu kan du sende en sag i HPC klynge Manager. Du har brug for til at videregive script hpcimpirun.sh i linjerne kommandoen for nogle af opgaverne i jobbet.

1. Oprette forbindelse til din klynge hoved node, og start HPC klynge Manager.

2. **I ressourcestyring**, sikre, at Linux Beregn knuderne i tilstanden **Online** . Hvis det er tilfældet, kan du markere dem og klikke på **Få Online**.

3.  **Administration af udskriftsjob**, klik på **Nyt Job**.

4.  Angiv et navn til jobbet som _sloshingTank3D_.

    ![Oplysninger om job][job_details]

5.  Vælg typen ressource som "Node" i **jobbet ressourcer**, og Angiv Minimum til 2. Denne konfiguration kører jobbet på to Linux noder, hver især har otte kerner i dette eksempel.

    ![Jobbet ressourcer][job_resources]

6. Klik på **Rediger opgaver** i venstre navigationsrude, og klik derefter på **Tilføj** for at føje en opgave til jobbet. Føje fire opgaver til jobbet med følgende kommando linjer og indstillinger.

    >[AZURE.NOTE]Kører `source /openfoam/settings.sh` konfigurerer OpenFOAM og MPI runtime miljøerne, så hver af følgende opgaver kalder før kommandoen OpenFOAM.

    *   **Opgave 1**. Køre **decomposePar** for at generere datafiler til kørsel af **interDyMFoam** parallelt.
    
        *   Tildele en node til opgaven

        *   **Kommandolinje** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Arbejdsmappe** - / openfoam/sloshingTank3D
        
        Se figuren nedenfor. Du konfigurerer de resterende opgaver på samme måde.

        ![Opgave 1 detaljer][task_details1]

    *   **Opgave 2**. Køre **interDyMFoam** parallelt til at beregne stikprøvernes.

        *   Tildele to noder til opgaven

        *   **Kommandolinje** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Arbejdsmappe** - / openfoam/sloshingTank3D

    *   **Opgave 3**. Køre **reconstructPar** for at flette sæt af tid mapper fra hver processor_N_ mappe til et enkelt sæt.

        *   Tildele en node til opgaven

        *   **Kommandolinje** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Arbejdsmappe** - / openfoam/sloshingTank3D

    *   **Opgave 4**. Køre **foamToEnsight** parallelt konvertere OpenFOAM resultatet filerne til EnSight format og placere EnSight filerne i en mappe med navnet Ensight i mappen store og små bogstaver.

        *   Tildele to noder til opgaven

        *   **Kommandolinje** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Arbejdsmappe** - / openfoam/sloshingTank3D

6.  Føje afhængigheder til disse opgaver i stigende opgaverækkefølge.

    ![Opgaveafhængigheder][task_dependencies]

7.  Klik på **Send** for at køre dette job.

    Som standard sender HPC Pack jobbet som din aktuelle er logget på konto. Når du klikker på **Send**, kan du muligvis vist en dialogboks, der beder dig om at angive brugernavn og adgangskode.

    ![Jobbet legitimationsoplysninger][creds]

    Under visse omstændigheder husker HPC Pack brugeroplysninger, du indtaster før og ikke viser denne dialogboks. Hvis HPC Pack vist igen, skal du skrive følgende kommando fra en kommandoprompt og derefter sende jobbet.

    ```
    hpccred delcreds
    ```

8.  Jobbet tager fra mange af minutter til flere timer ifølge de parametre, du har angivet for stikprøven. I varmekort, kan du se jobbet kører på noderne Linux. 

    ![Varmekort][heat_map]

    Otte processer er startet på hver node.

    ![Linux processer][linux_processes]

9.  Når jobbet afsluttes, skal du finde tingene resultaterne i mapper under C:\OpenFoam\sloshingTank3D og logfiler på C:\OpenFoam.


## <a name="view-results-in-ensight"></a>Få vist resultater i EnSight

Du kan også bruge [EnSight](https://www.ceisoftware.com/) til at visualisere og analysere resultatet af kørslen OpenFOAM. Se denne [video vejledning](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html)til flere oplysninger om visualisering og animation i EnSight.

1.  Når du installerer EnSight på noden hoved, kan du starte den.

2.  Åbn C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.

    Du ser en beholder i fremviseren.

    ![Beholder i EnSight][tank]

3.  Oprette en **Isosurface** fra **internalMesh**, og vælg derefter den variable **alpha_water**.

    ![Oprette en isosurface][isosurface]

4.  Angive farven for **Isosurface_part** oprettet i ovenstående trin. For eksempel angive den til vand blå.

    ![Redigere isosurface farve][isosurface_color]

5.  Oprette en **Iso-lydstyrken** fra **vægge** ved at vælge **vægge** i panelet **dele** , og klik på knappen **Isosurfaces** på værktøjslinjen.

6.  Vælg **Type** som **Isovolume** i dialogboksen, og Angiv Min i **Isovolume område** til 0,5. Hvis du vil oprette isovolume, klik på **Opret med udvalgte dele**.

7.  Angive farven for **Iso_volume_part** oprettet i ovenstående trin. For eksempel angive den til deep vand blå.

8.  Angive farven for **vægge**. For eksempel angive den til gennemsigtig hvid.

9. Nu skal du klikke på **Afspil** for at få vist resultaterne af simuleringen.

    ![Beholder resultat][tank_result]

## <a name="sample-files"></a>Eksempelfiler

### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Eksempel på XML-konfigurationsfil til installation af klynge ved hjælp af PowerShell-script

 ```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>suse12rdmavnet</VNetName>
    <SubnetName>SUSE12RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>SUSE12RDMA-HN</VMName>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
    <ServiceName>suse12rdma-je</ServiceName>
    <VMSize>A8</VMSize>
    <NodeCount>2</NodeCount>
      <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Eksempel silent.cfg fil for at installere MPI

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no


```

### <a name="sample-settingssh-script"></a>Eksempel på settings.sh-script

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###<a name="sample-hpcimpirunsh-script"></a>Eksempel på hpcimpirun.sh-script

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
    # CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
    ${MPIRUN} $*
else
    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

    # Get every node name and write into the hostfile file
    I=1
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
    done

    # Run the mpirun with hostfile arg
    ${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

    RTNSTS=$?
    rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png
