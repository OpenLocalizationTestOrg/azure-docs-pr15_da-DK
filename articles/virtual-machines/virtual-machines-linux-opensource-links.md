<properties
    pageTitle="Linux og Open Source-Computing på Azure | Microsoft Azure"
    description="En liste over Linux og Open Source Computing artikler om Azure, herunder brug af grundlæggende Linux nogle grundlæggende begreber om kører eller overføre Linux billeder på Azure og andet indhold om bestemte teknologier og optimeringer."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux og open source-computing på Azure

Find alle dokumentation, du har brug for at oprette og administrere Linux-baserede virtuelle maskiner i modellen Klassisk installation.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Komme i gang
- [Introduktion til Linux på Azure](virtual-machines-linux-intro-on-azure.md)
- [Ofte stillede spørgsmål om Azure virtuelle maskiner, der er oprettet med den klassiske implementeringsmodel](virtual-machines-linux-classic-faq.md)
- [Om billeder til virtuelle maskiner](virtual-machines-linux-classic-about-images.md)
- [Overførsel af dit eget Distro billede](virtual-machines-linux-classic-create-upload-vhd.md) (og også instruktioner ved hjælp af en [Azure-Endorsed fordeling](virtual-machines-linux-endorsed-distros.md))
- [Logge på en Linux VM ved hjælp af portalen Azure klassisk](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Konfigurere

- [Installer Azure kommandolinjen (Azure CLI)](../xplat-cli-install.md)


## <a name="tutorials"></a>Selvstudier

- [Installere lys stablen på en Linux virtuel maskine i Azure](virtual-machines-linux-create-lamp-stack.md)
- [Fonetisk på gør webprogram på en Azure VM](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Sådan: Installer Apache Qpid Proton-C til AMQP og Service Bus](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Databaser
- [Optimere ydeevnen af MySQL på Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [MySQL klynger](virtual-machines-linux-classic-mysql-cluster.md)
- [Køre Cassandra med Linux på Azure og få adgang til den fra Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Oprette en med flere klynge af MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Komme i gang med Linux Beregn noder i en HPC Pack klynge i Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Køre NAMD med Microsoft HPC Pack på Linux Beregn noder i Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Konfigurere en Linux RDMA klynge til at køre MPI programmer](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [Med filtypenavnet Docker VM fra den kommandolinjen Azure grænsefladen (Azure CLI)](virtual-machines-linux-classic-cli-use-docker.md)
- [Ved hjælp af filtypenavnet Docker VM fra Azure-portalen](virtual-machines-linux-classic-portal-use-docker.md)
- [Hvordan du bruger docker maskine på Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Sådan: MySQL klynger](virtual-machines-linux-classic-mysql-cluster.md)
- [Sådan: Node.js og Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Sådan: installere og køre MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Sådan: bruge CoreOS på Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planlægning
- [Azure infrastruktur services implementering retningslinjer](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Valg af Linux brugernavne](virtual-machines-linux-usernames.md)
- [Sådan konfigureres en tilgængelighed, der er angivet for virtuelle maskiner i modellen Klassisk installation](virtual-machines-linux-classic-configure-availability.md)
- [Sådan planlægges planlagt vedligeholdelse på Azure FOS](virtual-machines-linux-planned-maintenance-schedule.md)
- [Administrere tilgængeligheden af virtuelle maskiner](virtual-machines-linux-manage-availability.md)
- [Planlagt vedligeholdelse for Linux virtuelle maskiner i Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Installation
- [Oprette en brugerdefineret virtuel maskine, der kører Linux](virtual-machines-linux-classic-createportal.md)
- [Grundlæggende: hentning af en Linux VM for at oprette en skabelon](virtual-machines-linux-classic-capture-image.md)
- [Oplysninger om ikke-godkendt fordeling](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Administration

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Hvordan du nulstiller en adgangskode eller SSH egenskaber for Linux](virtual-machines-linux-classic-reset-access.md)
- [Brug af rod](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Azure ressourcer

- [Azure Linux-Agent](virtual-machines-linux-agent-user-guide.md)
- [Azure VM Extensions og funktioner](virtual-machines-windows-extensions-features.md)
- [Indsprøjtning Custom Data i en VM til brug med skyen initialisering](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Lagerplads

- [Vedhæfte en datadisk til en Linux VM](virtual-machines-linux-classic-attach-disk.md)
- [Fjernelse af en datadisk, fra en Linux VM](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Netværk
- [Hvordan du konfigurerer slutpunkterne på en klassisk virtuel maskine i Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
- [Fejlfinding i forbindelse med Secure Shell (SSH) forbindelser til en Linux-baserede Azure virtuel maskine](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Foretage fejlfinding af problemer med klassisk implementeringen til at oprette en ny Linux virtuel maskine i Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Fejlfinding i forbindelse med klassisk installation problemer med at genstarte eller ændre størrelsen på en eksisterende Linux virtuel maskine i Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Reference

- [Azure CLI-kommandoer i Azure Service Management (asm) tilstand](../virtual-machines-command-line-tools.md)
- [Azure Service Management REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Generelt Links
Følgende links er til Microsoft blogs, Technet sider, og eksterne websteder i stedet for Azure.com dokumentation som ovenfor. Som både Azure og open source-databehandling verden er hurtige destinationer, er det næsten sikker på, at følgende links er forældet *trods* fakultet, skal vi gør vores bedste til kontinuerligt at tilføje nyere emner og fjerne forældede dem. Hvis vi har mistede en, skal du Lad os kender i kommentarerne, eller sende en anmodning om hente til vores [GitHub repo](https://github.com/Azure/azure-content/).

- [Kører ASP.NET 5 på Linux ved hjælp af Docker objektbeholdere](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Sådan installeres et CentOS VM billede fra OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [SUSE opdatering infrastruktur](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server for SAP skyen maskinen bibliotek](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Opbygning af meget tilgængelige Linux på Azure i 12 trin](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatisere klargøring Linux på Azure med Azure CLI, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS på Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [Køre FreeBSD i Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Nemt installere FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implementere et tilpasset FreeBSD billede](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV for Linux filserver](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 open source NoSql databaser til Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Oplevelser med CouchDb på Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Kører CouchDB-som-en-Service med node.js, CORS og Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis i Windows i tjenesten Azure Redis Cache](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Præsentation af ASP.NET Session tilstand-Provider til Redis Preview-versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ-nu tilgængelig på Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Big Data
- [Installerer Hadoop på Azure Linux FOS](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Relationsdatabase
- [MySQL høj tilgængelighed arkitektur i Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Installation af Postgres med corosync, pg_bouncer ved hjælp af ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux høj ydeevne HPC (computing)

- [Hurtig start skabelon: rotation af en SLURM klynge](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (og [blogindlæg](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Skabelon til hurtig start: oprette en HPC-klynge med Linux Beregn noder](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Devops, administration og optimering

Som en verden med devops, administration og optimering er helt udvidelig og ændrer meget hurtigt, bør du overveje at på listen herunder udgangspunkt.

- [Video: Azure virtuelle maskiner: ved hjælp af kok, Puppet og Docker til administration af Linux FOS](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Komplet vejledning til automatiseret Kubernetes klynge-installation med CoreOS og store](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Jenkins Slave Plug-in til Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo: Jenkins lagerplads Plug-in til Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Fra tredjepart: Hudson Slave Plug-in til Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Fra tredjepart: Hudson lagerplads Plug-in til Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Video: Hvad er kok, og hvordan fungerer det?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Video: Sådan bruges Azure automatisering med Linux FOS](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Hvordan du gør Powershell DTK for Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker klient DTK](https://github.com/anweiss/DockerClientDSC)

- [Pakker plug-in til Azure](https://github.com/msopentech/packer-azure)
