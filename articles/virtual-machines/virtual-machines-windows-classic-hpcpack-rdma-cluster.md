<properties
 pageTitle="Konfigurere en Windows RDMA klynge til at køre MPI programmer | Microsoft Azure"
 description="Lær at oprette en Windows HPC Pack klynge med størrelse H16r, H16mr, A8 eller A9 FOS bruge Azure RDMA netværk til at køre MPI apps."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Konfigurere en Windows RDMA klynge med HPC Pack til at køre MPI programmer

Konfigurere en Windows RDMA klynge i Azure med [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) og [H-serien eller store beregninger A-serien forekomster](virtual-machines-windows-a8-a9-a10-a11-specs.md) at køre parallelle programmer, der passerer MPI (Message Interface). Når du konfigurerer RDMA-kompatible, Windows Server-baserede noder i en HPC Pack klynge, kommunikere MPI programmer effektivt over en lav ventetid, høj overførselshastighed netværk i Azure, der er baseret på remote direkte hukommelse access (RDMA) teknologi.

Hvis du vil køre MPI arbejdsbelastninger på Linux FOS, få adgang til Azure RDMA netværket, skal du se [konfigurere en Linux RDMA klynge for at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack klynge installationsindstillinger
Microsoft HPC Pack er et værktøj, forudsat gratis til at oprette HPC klynger lokalt eller i Azure til at køre Windows eller Linux HPC programmer. HPC pakken indeholder et runtime-miljø til Microsofts implementering af den meddelelse der passerer Interface til Windows (MS-MPI). Når den bruges med RDMA-kompatible forekomster, der kører et understøttet Windows Server-operativsystem, indeholder HPC Pack en effektiv indstilling til at køre Windows MPI programmer, få adgang til Azure RDMA netværket. 

I denne artikel introducerer to scenarier og links til detaljerede vejledning til at konfigurere en Winodws RDMA klynge med Microsoft HPC Pack. 

* Scenarie 1. Installere Beregn-intensivt arbejder rolle forekomster (PaaS)

* Scenarie 2. Installere Beregn knuder på Beregn-intensivt FOS (IaaS)

Se [om H-serien og Beregn-intensivt A-serien FOS](virtual-machines-windows-a8-a9-a10-a11-specs.md) for generelle forudsætninger for at bruge Beregn-intensivt forekomster med Windows.



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenarie 1. Installere Beregn-intensivt arbejder rolle forekomster (PaaS)

Tilføje ekstra Beregn ressourcer i Azure arbejder rolle forekomster (Azure noder), kører i en skybaseret tjeneste (PaaS) fra en eksisterende HPC Pack klynge. Denne funktion, også kaldet "burst til Azure" fra HPC Pack understøtter et udvalg af størrelser efter arbejder rolle forekomster. Når du tilføjer noderne Azure, du skal blot angive en af de RDMA-kompatible størrelser.

Følgende er overvejelser og trin til at burst til RDMA-kompatible Azure forekomster fra en eksisterende (typisk lokalt) klynge. Brug lignende procedurer til at føje arbejder rolle forekomster til en HPC Pack hoved node, der er implementeret i en Azure VM.

>[AZURE.NOTE] Se [konfigurere en hybrid klynge med HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)et selvstudium til burst til Azure med HPC Pack. Bemærk overvejelser i nedenstående fremgangsmåde, der gælder specifikt for RDMA-kompatible Azure noder.

![Burst til Azure][burst]

### <a name="steps"></a>Trin

4. **Installere og konfigurere en hoved node HPC Pack 2012 R2**

    Hente den nyeste HPC Pack installationspakken fra [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Se [HPC Pack Introduktion](https://technet.microsoft.com/library/jj884144.aspx) og [Burst til Azure arbejder forekomster med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)krav og vejledninger til forberedelse af en Azure burst-installation.

5. **Konfigurere en management-certifikat i Azure-abonnement**

    Konfigurere et certifikat for at sikre forbindelsen mellem hoved node og Azure. Se [scenarier for at konfigurere Azure Management-certifikat til HPC Pack](http://technet.microsoft.com/library/gg481759.aspx)til indstillinger og procedurer. For test-installationer installeres HPC Pack et standard Microsoft HPC Azure Management certifikat du hurtigt kan overføre til abonnementet Azure.

6. **Oprette en ny tjeneste i skyen og en lagerplads-konto**

    Bruge Azure klassisk portal til at oprette en skybaseret tjeneste og en lagerplads kontoen for installationen i et område, hvor RDMA-kompatible forekomster er tilgængelige.

7. **Oprette en skabelon til Azure node**

    Brug den Node skabelonguiden Opret i HPC klynge Manager. Trin til at se [oprette en skabelon til Azure node](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) i "Trin til at installere Azure noder med Microsoft HPC Pack".

    Vi anbefaler, at du konfigurerer en manuel tilgængelighed politik i skabelonen for indledende test.

8. **Tilføje noder til klyngen**

    Brug den Node guiden Tilføj i HPC klynge Manager. Se [Tilføje Azure noder til Windows HPC klynge](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)kan finde flere oplysninger.

    Når du angiver størrelsen på noderne, Vælg en af RDMA-kompatible forekomst størrelsen.
    
    >[AZURE.NOTE]I hver burst til Azure-installation med Beregn-intensivt forekomster installerer HPC Pack automatisk et minimum af 2 RDMA-kompatible forekomster (såsom A8) som proxy noder, ud over de Azure arbejder rolle forekomster, du angiver. Noderne proxy Brug kerner, der er allokeret til abonnementet, og der påløber gebyrer sammen med Azure arbejder rolle forekomster.

9. **Starte (klargøre) noderne og få dem online til at køre job**

    Markér noderne og bruge handlingen **Start** i HPC klynge Manager. Når klargøring er fuldført, vælge noderne og bruge handlingen **Sætter Online** i HPC klynge Manager. Noderne er klar til at køre job.

10. **Sende job til klyngen**

    Bruge HPC Pack job bidrag værktøjer til at køre klynge job. Se [Microsoft HPC Pack: Job Management](http://technet.microsoft.com/library/jj899585.aspx).

11. **Stop (deprovision) noderne**

    Når du er færdig, der kører job, tage noderne offline og bruge handlingen **Stop** i HPC klynge Manager.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenarie 2. Installere Beregn knuder på Beregn-intensivt FOS (IaaS)

I dette scenarie skal du installere noden HPC Pack hoved og klynge beregne noder på VM'er, der er knyttet til en Active Directory-domæne i en Azure virtuelt netværk. HPC Pack indeholder en række [installationsindstillinger i Azure FOS](virtual-machines-linux-hpcpack-cluster-options.md), herunder automatiseret udrulning scripts og Azure Hurtig start skabeloner. Som et eksempel, overvejelser og trinnene nedenfor fører dig til at bruge [HPC Pack IaaS installation script](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) til at automatisere de fleste af denne proces.

![Klynge i Azure FOS][iaas]



### <a name="steps"></a>Trin

1. **Oprette en klynge hoved node og beregne node FOS ved at køre scriptet HPC Pack IaaS installation på en klientcomputer**

    Hent pakken scriptet til implementering af HPC Pack IaaS fra [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922).

    For at forberede klientcomputeren skal oprette konfigurationsfil script og køre scriptet, skal du se [oprette en HPC-klynge med scriptet HPC Pack IaaS installation](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Bemærk følgende yderligere overvejelser, du skal installere RDMA-kompatible Beregn noder:
    
    * **Virtuel netværk** - angive et nyt virtuelt netværk i et område, hvor du vil bruge RDMA-kompatible forekomst størrelsen er tilgængelig.

    * **Operativsystemet Windows Server** - understøtter RDMA forbindelse, skal du angive et Windows Server 2012 R2 eller Windows Server 2012 operativsystem for noden Beregn FOS.

    * **Cloud services** – vi anbefaler, at du anvender dit hoved node i en skybaseret tjeneste, og din Beregn noder i en anden skybaseret tjeneste.

    * **I afsnit node størrelse** - dette scenario, kan du overveje at en størrelse på mindst A4 (ekstra stor) for noden hoved.

    * **HpcVmDrivers lokalnummer** - installation scriptet installerer Azure VM Agent og filtypenavnet HpcVmDrivers automatisk når du installerer størrelse A8 eller A9 beregne noder med et Windows Server-operativsystem. HpcVmDrivers installerer drivere på noden Beregn FOS, så de kan oprette forbindelse til netværket RDMA.

    * **Klynge netværkskonfiguration** - installation scriptet konfigurerer automatisk HPC Pack klynge i topologi 5 (alle noder i Enterprise-netværk). Denne topologi er påkrævet for alle HPC Pack klynge installationer i FOS. Undlad at ændre netværkstopologi klynge senere.

2. **Få noderne Beregn online til at køre job**

    Markér noderne og bruge handlingen **Sætter Online** i HPC klynge Manager. Noderne er klar til at køre job.

3. **Sende job til klyngen**

    Oprette forbindelse til noden hoved til at sende job eller konfigurere en lokale computer til at gøre dette. Du kan finde oplysninger [Sende job til en HPC klynge i Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Tage noderne offline og stop (deallocate) dem**

    Når du er færdig, der kører job, kan du tage noderne offline i HPC klynge Manager. Brug derefter Azure administrationsværktøjer til at lukke dem.



## <a name="run-mpi-applications-on-the-cluster"></a>Køre MPI programmer på klyngen

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Eksempel: Køre mpipingpong på en HPC Pack klynge

Kør kommandoen HPC Pack **mpipingpong** på klyngen for at bekræfte en HPC Pack installation af RDMA-kompatible forekomster. **mpipingpong** sender pakker af data mellem parvis noder gentagne gange for at beregne ventetid og overførselshastighed mål og statistik for netværket, RDMA aktiverede program. I dette eksempel vises et typisk mønster til kørsel af et MPI job (i dette tilfælde **mpipingpong**) ved hjælp af kommandoen klynge **mpiexec** .

Dette eksemplet antages, du har tilføjet Azure noder i en "burst til Azure" konfiguration ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Hvis du har installeret HPC Pack på en klynge af Azure FOS, skal du ændre kommandosyntaksen for at angive en anden node gruppe og angive yderligere miljøvariabler til at dirigere netværkstrafik til RDMA netværket.


Sådan køres mpipingpong på klyngen:


1. Noden hoved eller på en korrekt konfigurerede klientcomputer, Åbn en kommandoprompt.

2. Skriv følgende kommando for at sende et job for at køre mpipingpong med en lille pakkestørrelse og et stort antal gentagelser for at anslå ventetid mellem par af knuder på en Azure burst installation af 4 noder:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    Kommandoen returnerer ID'ET for den opgave, der er sendt.

    Hvis du har installeret den HPC Pack klynge installeres på Azure FOS, angive en node gruppe, der indeholder beregne node FOS implementeret i en enkelt skybaseret tjeneste, og rediger kommandoen **mpiexec** på følgende måde:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Når jobbet er fuldført, for at få vist output (i dette tilfælde output fra opgave 1 på sag), Skriv følgende

    ```
    task view <JobID>.1
    ```

    hvor &lt; *job-id* &gt; er ID'ET for den opgave, der blev sendt.

    Output medtages ventetid resultater som følger.

    ![Ping pong ventetid][pingpong1]

4. Skriv følgende kommando for at sende et job for at køre **mpipingpong** med en stor pakkestørrelse og et lille antal gentagelser for at anslå overførselshastighed mellem par af Azure burst noder:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    Kommandoen returnerer ID'ET for den opgave, der er sendt.

    På en HPC Pack klynge installeres på Azure FOS, ved at redigere kommandoen, der er angivet i trin 2.

5. Når jobbet er fuldført, for at få vist output (i dette tilfælde output fra opgave 1 på sag), Skriv følgende:

    ```
    task view <JobID>.1
    ```

  Output medtages overførselshastighed resultater som følger.

  ![Ping pong overførselshastighed][pingpong2]


### <a name="mpi-application-considerations"></a>Overvejelser i forbindelse med MPI program


Følgende er overvejelser i forbindelse med til at køre MPI programmer med HPC Pack i Azure. Nogle gælder kun for installationer af Azure knuder (arbejder rolle forekomster tilføjet i en konfiguration med "burst til Azure").

* Arbejder rolle forekomster i en skybaseret tjeneste er regelmæssigt reprovisioned uden varsel ved Azure (for eksempel til vedligeholdelse af systemet eller i tilfælde, der er en forekomst mislykkes). Hvis en forekomst er reprovisioned, mens den kører et MPI job, mister dataene forekomsten, og returnerer til tilstanden, når det er først installeret, hvilket kan medføre MPI jobbet mislykkes. Flere knuderne, at du bruger til et enkelt MPI job, og den længere jobbet kører, jo mere sandsynligt, at en af forekomsterne være reprovisioned under et job kører. Du skal også overveje følgende, hvis du angiver en enkelt node i installationen som en filserver.


* Hvis du vil køre MPI job i Azure, skal behøver du ikke at bruge RDMA-kompatible forekomster. Du kan bruge en hvilken som helst forekomst størrelse, der understøttes af HPC Pack. Dog er RDMA-kompatible forekomster anbefalet til kørsel af relativt store MPI job, der er følsomme over for ventetid og båndbredde på det netværk, der forbinder noderne. Hvis du bruger andre størrelser til at køre ventetid og båndbredde følsomme MPI job, anbefaler vi kører mindre opgaver, hvor en enkelt opgave kører på kun nogle få noder.

* Programmer, der er installeret på forekomster af Azure er underlagt licensvilkårene der er knyttet til programmet. Kontakte forhandleren af enhver kommercielle ansøgning om licenser og andre begrænsninger for at køre i skyen. Ikke alle leverandører tilbyder overkommelige licenser.


* Azure forekomster skal yderligere konfiguration til access lokale noder, aktier og licensservere. Hvis du vil aktivere Azure knuderne at få adgang til en lokal licensserveren, kan du konfigurere et websted til Azure netværk til virtuelle.


* Hvis du vil køre MPI programmer på Azure forekomster, du registrere hvert MPI program med Windows Firewall på forekomster ved at køre kommandoen **hpcfwutil** . Dette giver mulighed for MPI kommunikation finder sted på en port, der er tildelt dynamisk af firewallen.

    >[AZURE.NOTE] For burst til Azure-installationer, kan du også konfigurere en firewall undtagelse kommando til at køre automatisk på alle nye Azure noder, der er føjet til din klynge. Når du kører kommandoen **hpcfwutil** og kontrollere, at dit program fungerer, kan du føje kommandoen til et script til Start for din Azure noder. Se [Brug af et Script til Start for Azure noder](https://technet.microsoft.com/library/jj899632.aspx)kan finde flere oplysninger.



* HPC Pack bruger miljøvariablen CCP_MPI_NETMASK klynge til at angive et interval af acceptable adresser til MPI kommunikation. Starter i HPC Pack 2012 R2, miljøvariablen CCP_MPI_NETMASK klynge påvirker kun MPI kommunikation mellem medlem af et domæne klynge Beregn noder (enten lokalt eller i Azure VM'er). Variablen ignoreres af noder, der er tilføjet i en burst Azure konfigurationen.


* MPI job kan ikke køres på tværs af Azure forekomster, der er implementeret i forskellige skytjenester (for eksempel i burst til Azure-installationer med forskellige node skabeloner eller Azure VM Beregn noder implementeret i flere skytjenester). Hvis du har flere Azure node-installationer, der er i gang med anden node skabeloner, skal MPI jobbet køre på kun ét sæt Azure noder.


* Når du tilføjer Azure noder til din klynge og få dem vist online, forsøger tjenesten HPC Job Scheduler straks at starte job på noderne. Hvis der kun en del af din arbejdsbyrde kan køre på Azure, kan du sikre dig, at du opdaterer eller oprette jobskabeloner for at definere, hvad jobtyper kan køre på Azure. For eksempel for at sikre, at job sendes med en jobskabelon kun køre på Azure noder, føje egenskaben nodegrupper til jobskabelonen, og vælg AzureNodes som den nødvendige værdi. Brug af Tilføj HpcGroup HPC PowerShell-cmdlet til at oprette brugerdefinerede grupper for din Azure noder.


## <a name="next-steps"></a>Næste trin

* Som alternativ til at bruge HPC Pack kan du udvikle med tjenesten Azure batchen at køre MPI programmer på administrerede grupper af Beregn knuder på Azure. Se [bruge flere forekomster opgaver til at køre der passerer MPI (Message Interface) programmer i Azure Batch](../batch/batch-mpi.md).

* Hvis du vil køre Linux MPI programmer, få adgang til Azure RDMA netværket, skal du se [konfigurere en Linux RDMA klynge for at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png