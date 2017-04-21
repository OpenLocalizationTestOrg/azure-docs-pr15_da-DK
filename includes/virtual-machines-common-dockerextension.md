

[Docker](https://www.docker.com/) er et af de mest populære virtualization metoder, der bruger [Linux beholdere](http://en.wikipedia.org/wiki/LXC) i stedet for virtuelle maskiner som en metode til at isolere programmet data og databehandling med på delte ressourcer. Du kan bruge [Azure Docker VM lokalnummer](https://github.com/Azure/azure-docker-extension/blob/master/README.md) til [Azure Linux Agent](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) til at oprette en Docker VM, der er vært et vilkårligt antal beholdere til dine programmer på Azure.

I dette emne beskrives:

+ [Docker og Linux beholdere]
+ [Hvordan du bruger filtypenavnet Docker VM med Azure]
+ [Virtuelt filtypenavne for Linux og Windows]

Hvis du vil oprette Docker aktiverede FOS lige nu, skal du se:

+ [Hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]
+ [Hvordan du bruger filtypenavnet Docker VM ved hjælp af Azure klassisk portal]
+ [Hvordan du kan komme hurtigt i gang med Docker i Azure Marketplace]

Hvis du vil vide mere om filtypenavnet, og hvordan den fungerer, se [Docker lokalnummer brugervejledningen](https://github.com/Azure/azure-docker-extension/blob/master/README.md).

## <a name="docker-and-linux-containers"></a>Docker og Linux beholdere
[Docker](https://www.docker.com/) er et af de mest populære virtualization metoder, der bruger [Linux beholdere](http://en.wikipedia.org/wiki/LXC) i stedet for virtuelle maskiner som en metode til at isolere data og databehandling med på delte ressourcer og giver andre tjenester, der gør det muligt at opbygge eller samle programmer hurtigt og distribuere dem mellem andre Docker beholdere.

Docker og Linux objektbeholdere er ikke [Hypervisors](http://en.wikipedia.org/wiki/Hypervisor) som Windows Hyper-V og [KVM](http://www.linux-kvm.org/page/Main_Page) på Linux (der er mange andre eksempler). Hypervisors Virtualiser det underliggende operativsystem for at aktivere fuldført operativsystemer (kaldet *virtuelle maskiner*) for at køre i hypervisor, som var de et program.

Docker og andre fremgangsmåder, *objektbeholder* har væsentligt formindsket både den opstart tid consumed og behandling og opbevaring indirekte påkrævet ved hjælp af processen, og filen system isolationsniveauet funktioner i Linux kernen til at fremvise kun kernen funktioner til en ellers isolerede objektbeholder.

I følgende tabel beskrives på en meget høj grad slags forskel i funktioner, der findes mellem hypervisors og Linux beholdere. Bemærk, at nogle funktioner måske flere eller færre bør afhængigt af din egen programmet behov.

|   Funktion      | Hypervisors | Objektbeholdere  |
| :------------- |-------------| ----------- |
| Processen isolationsniveauet | Flere eller færre fuldføre | Hvis der opnås rod, kunne objektbeholder host være kompromitteret |
| Hukommelse på disken, der er påkrævet | Komplet Operativsystem plus apps | Kun App krav |
| Tid, det tager at starte | Betydeligt længere tid: Start af OS plus app indlæsning | Væsentligt kortere: kun apps skal starte, fordi kernen allerede kører  |
| Objektbeholder automatisering | Varierer meget afhængigt af OS og apps | [Docker Billedgalleri](https://registry.hub.docker.com/); andre

For at se en overordnet beskrivelse af objektbeholdere og deres fordele skal du se [Docker høj niveau Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Finde flere oplysninger om, hvad Docker er, og hvordan det fungerer virkelig [Hvad er Docker?](https://www.docker.com/whatisdocker/)

#### <a name="docker-and-linux-container-security-best-practices"></a>Bedste fremgangsmåder for docker og Linux objektbeholder sikkerhed

Fordi beholdere deler adgang til host computerens kernen, hvis skadelig kode kan få rod kan det også muligvis til at få adgang ikke blot at host computeren, men andre beholdere. Til at sikre systemet objektbeholder mere kraftigt end standardkonfigurationen, [Docker anbefaler](https://docs.docker.com/articles/security/) ved hjælp af tilføjelsen Gruppepolitik eller [rollebaseret sikkerhed](http://en.wikipedia.org/wiki/Role-based_access_control) samt som [SELinux](http://selinuxproject.org/page/Main_Page) eller [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), for eksempel samt reducere som muligt kerne-funktioner, der er tildelt beholderne. Desuden er der mange andre dokumenter på internettet, der beskriver fremgangsmåder til sikkerhed ved hjælp af objektbeholdere som Docker.

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Hvordan du bruger filtypenavnet Docker VM med Azure

Filtypenavnet Docker VM er en komponent, der er installeret på forekomsten VM, som du opretter hvilke selve installerer Docker program og administrerer den eksterne kommunikation med VM. Der er to måder at installere filtypenavnet VM: Du kan oprette din VM ved hjælp af administrationsportalen, eller du kan oprette den fra Azure kommandolinjen (Azure CLI).

Du kan bruge portalen til at føje Docker VM udvidelsen til en hvilken som helst kompatible Linux VM (i øjeblikket er det kun billede, der understøtter det er det Ubuntu 14.04 LTS billede, der er nyere end juli). Kommandolinje Azure CLI, men kan du installere filtypenavnet Docker VM og oprette og overføre dine Docker kommunikation certifikater alle på samme tid, når du opretter VM forekomsten.

Hvis du vil oprette Docker aktiverede FOS lige nu, skal du se:

+ [Hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]
+ [Hvordan du bruger filtypenavnet Docker VM ved hjælp af Azure klassisk portal]

## <a name="virtual-machine-extensions-for-linux-and-windows"></a>Virtuelt filtypenavne for Linux og Windows
[Docker VM udvidelse til Azure](https://github.com/Azure/azure-docker-extension/blob/master/README.md) er blot et af flere VM filtypenavne, som giver speciel adfærd, og mere er under udvikling. For eksempel gør flere af [Linux VM Agent lokalnummer](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md) funktionerne det muligt at redigere og administrere den virtuelle maskine, herunder sikkerhedsfunktioner, kerne og netværksfunktioner osv. Filtypenavnet VMAccess kan for eksempel du nulstille administratoradgangskode eller SSH nøgle.

Du kan finde en komplet liste [Azure VM filtypenavne](../articles/virtual-machines/virtual-machines-windows-extensions-features.md).

<!--Anchors-->
[Hvordan du bruger filtypenavnet Docker VM fra Azure kommandolinjen (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Hvordan du bruger filtypenavnet Docker VM ved hjælp af Azure klassisk portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Hvordan du kan komme hurtigt i gang med Docker i Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Docker og Linux beholdere]: #Docker-and-Linux-Containers
[Hvordan du bruger filtypenavnet Docker VM med Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtuelt filtypenavne for Linux og Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
