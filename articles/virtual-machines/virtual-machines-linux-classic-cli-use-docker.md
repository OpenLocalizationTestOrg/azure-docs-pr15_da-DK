<properties
    pageTitle="Ved hjælp af filtypenavnet Docker VM til Linux på Azure"
    description="Beskriver Docker og udvidelser de virtuelle Azure-computere og viser, hvordan du automatisk oprette virtuelle maskiner på Azure, der er docker hosts fra kommandolinjen ved hjælp af Azure CLI."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Med filtypenavnet Docker VM fra den kommandolinjen Azure grænsefladen (Azure CLI)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Dette emne beskrives, hvordan du opretter en VM med filtypenavnet Docker VM fra tilstanden service management (asm) i Azure CLI på alle platforme. [Docker](https://www.docker.com/) er et af de mest populære virtualization metoder, der bruger [Linux beholdere](http://en.wikipedia.org/wiki/LXC) i stedet for virtuelle maskiner som en metode til at isolere data og databehandling med på delte ressourcer. Du kan bruge filtypenavnet Docker VM og [Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) til at oprette en Docker VM, der er vært et vilkårligt antal beholdere til dine programmer på Azure. For at se en overordnet beskrivelse af objektbeholdere og deres fordele skal du se [Docker høj niveau Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Hvordan du bruger filtypenavnet Docker VM med Azure
Hvis du vil bruge filtypenavnet Docker VM med Azure, skal du installere en version af [Azure kommandolinjen Interface](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) højere end 0.8.6 (som dette blev skrevet er den aktuelle version 0.10.0). Du kan installere Azure CLI på Mac, Linux og Windows.


Du kan bruge Docker på Azure hele processen er det nemt:

+ Installer Azure CLI og dens afhængigheder på den computer, hvorfra du vil kontrollere Azure (i Windows, det vil være en Linux fordeling, der kører som en virtuel maskine)
+ Brug kommandoerne Azure CLI Docker til at oprette en VM Docker vært i Azure
+ Du kan bruge lokale Docker kommandoerne til at administrere din Docker beholdere i din Docker VM i Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installere på Azure kommandolinjen (Azure CLI)

For at installere og konfigurere Azure CLI, se, [hvordan du installerer Azure kommandolinjen](../xplat-cli-install.md). For at bekræfte installationen, Skriv `azure` ved kommandoprompten og efter et kort øjeblik, skal du se afsnittet Azure CLI ASCII-grafik, som viser de grundlæggende kommandoer, der er tilgængelige for dig. Hvis installationen virker korrekt, skal du kunne skrive `azure help vm` og se, at en af de viste kommandoer er "docker".

> [AZURE.NOTE] Docker har værktøjer til Windows, [Docker maskine](https://docs.docker.com/installation/windows/), som du kan også bruge til at automatisere oprettelse af en docker klient, som du kan bruge til at arbejde med Azure FOS som docker hosts.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Oprette forbindelse Azure CLI til din Azure-konto
Før du kan bruge Azure CLI skal du knytte legitimationsoplysningerne Azure-konto til Azure CLI på din platform. Sektionen, [hvordan du opretter forbindelse til dit Azure abonnement](../xplat-cli-connect.md) forklarer, hvordan du hente og importere **.publishsettings** filen eller knytte din Azure CLI med en organisations-id.

> [AZURE.NOTE] Der er nogle forskelle i funktionsmåde, når du bruger en eller de andre metoder til godkendelse, så skal du sørge for at læse dokumentet ovenfor for at forstå de forskellige funktioner.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installere Docker og bruge filtypenavnet Docker VM til Azure
Følg [Docker installationsvejledning](https://docs.docker.com/installation/#installation) for at installere Docker lokalt på din computer.

For at bruge Docker med en Azure Virtual Machine skal have Linux billedet bruges til VM [Azure Linux VM Agent](virtual-machines-linux-agent-user-guide.md) installeret. Der er på nuværende tidspunkt kun to typer billeder, som giver dette:

+ Et Ubuntu billede fra galleriet Azure billede eller

+ En brugerdefineret Linux-billede, du har oprettet med Azure Linux VM Agent installeret og konfigureret. Du kan finde flere oplysninger om, hvordan du opretter en brugerdefineret Linux VM med Azure VM Agent i [Azure Linux VM Agent](virtual-machines-linux-agent-user-guide.md) .

### <a name="using-the-azure-image-gallery"></a>Ved hjælp af galleriet Azure billede

Fra en fest eller Terminal session, skal du bruge kommandoen følgende Azure CLI til at finde den seneste Ubuntu billedet i galleriet VM ved at skrive

`azure vm image list | grep Ubuntu-14_04`

og vælg et af navnene, billede, som f.eks `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`, og brug følgende kommando til at oprette en ny VM ved hjælp af billedet.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

hvor:

+ * &lt;vm cloudservice navn&gt; * er navnet på den VM, som bliver Docker objektbeholder host computer i Azure

+  * &lt;brugernavn&gt; * er brugernavnet for standard rodbrugeren VM

+ * &lt;adgangskode&gt; * er adgangskoden til kontoen *brugernavn* , der opfylder standarderne for kompleksitet til Azure

> [AZURE.NOTE] I øjeblikket adgangskode skal være mindst 8 tegn, indeholder en små bogstaver og et stort bogstav, et tal og et specialtegn som et af følgende tegn: `!@#$%^&+=`. Nej, perioden i slutningen af den foregående sætning er ikke et specialtegn.

Hvis kommandoen lykkedes, skal du se noget i retning af følgende, afhængigt af de nøjagtige argumenter og indstillinger, du har brugt:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Oprette en virtuel maskine kan tage et par minutter, men når den er klargjort (værdien tilstand er `ReadyRole`) Docker daemonen (tjenesten Docker) starter, og du kan oprette forbindelse til værten Docker objektbeholder.

For at teste den Docker VM, du har oprettet i Azure, Skriv

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

hvor * &lt;vm navn-du-anvendte&gt; * er navnet på den virtuelle maskine, som du har brugt i dit opkald til `azure vm docker create`. Du bør se noget, der ligner følgende oplysninger, der angiver, at din Docker Host VM er op og køre i Azure og venter på dine kommandoer. 

Nu kan du prøve at oprette forbindelse ved hjælp af kunden docker til at hente oplysninger (i nogle Docker klient konfigurationer, som, på Mac, skal du muligvis bruge `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Kun for at være sikker på, at det er alle arbejde, kan du undersøge VM for filtypenavnet Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker Host VM godkendelse

Ud over at oprette VM Docker den `azure vm docker create` kommandoen opretter også automatisk de nødvendige certifikater for at tillade klientcomputeren Docker til at oprette forbindelse til værten Azure objektbeholder ved hjælp af HTTPS, og certifikaterne, der er gemt på både klient- og host maskiner, efter behov. På efterfølgende forsøg, er de eksisterende certifikater igen og deles med den nye vært.

Som standard certifikater er placeret i `~/.docker`, og Docker bliver konfigureret til at køre på port **2376**. Hvis du vil skifte til en anden port eller en mappe og derefter kan du bruge en af følgende `azure vm docker create` kommandolinjeparametre til at konfigurere din Docker objektbeholder hoste VM til at bruge en anden port eller forskellige certifikater til at oprette forbindelse klienter:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Docker daemonen på værten er konfigureret til at lytte til og godkende klientforbindelser på den angivne port ved hjælp af certifikater genereres af den `azure vm docker create` kommandoen. Klientcomputeren skal have disse certifikater til at få adgang til værten for Docker.

> [AZURE.NOTE] En netværksforbundet vært kører uden certifikaterne bliver udsatte for alle, der kan oprette forbindelse til computeren. Før du ændrer standardkonfigurationen, skal du sikre, at du forstår risici til computere og programmer.

## <a name="next-steps"></a>Næste trin

* Du er klar til at gå til [Docker brugervejledningen] og bruge din Docker VM. For at oprette en Docker aktiverede VM i den nye portal, se, [hvordan du bruger filtypenavnet Docker VM ved hjælp af Portal].

* Filtypenavnet Azure Docker VM understøtter også Docker skriver, som bruger en deklarativ YAML-fil til at tage et program, der udvikler er baseret på tværs af en hvilken som helst miljø og generere en ensartet installation. Se [Introduktion til Docker og Opret til at definere og køre en med flere objektbeholder program på en Azure virtuel maskine].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Hvordan du bruger filtypenavnet Docker VM ved hjælp af Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker brugervejledningen]: https://docs.docker.com/userguide/
 
[Introduktion til Docker og Opret for at definere og køre et program med flere objektbeholder på en Azure virtuelt]:virtual-machines-linux-docker-compose-quickstart.md