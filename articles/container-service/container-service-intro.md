<properties
   pageTitle="Azure objektbeholder tjenesten Introduktion | Microsoft Azure"
   description="Azure objektbeholder tjenesten giver en metode til at forenkle oprettelse, konfiguration og administration af en klynge af virtuelle maskiner, der er forudkonfigureret for at køre bulkvareenhed programmer."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="azure-container-service-introduction"></a>Azure objektbeholder tjenesten Introduktion

Azure objektbeholder tjenesten gør det nemmere for dig at oprette, konfigurere og administrere en klynge af virtuelle maskiner, der er forudkonfigureret for at køre bulkvareenhed programmer. Det bruger en optimeret konfiguration af populære open source-planlægning og organisering værktøjer. Dette gør det muligt at bruge din eksisterende viden eller tegne på et stort og voksende brødteksten i community ekspertise, til at installere og administrere objektbeholder-baserede programmer på Microsoft Azure.


![Azure objektbeholder tjenesten giver mulighed for at administrere bulkvareenhed programmer på flere værter på Azure.](./media/acs-intro/acs-cluster.png)


Azure objektbeholder tjenesten anvender formatet Docker objektbeholder for at sikre, at dit program objektbeholdere er fuldt portable. Det understøtter også valget af Marathon og DC/OS eller Docker Swarm, så du kan tilpasse disse programmer til tusindvis af objektbeholdere eller lige titusindvis.

Ved hjælp af Azure objektbeholder tjenesten, kan du drage fordel af professionel og funktionerne i Azure, og stadig bevare programmet mobilitet – herunder mobilitet på organisering lag.

<a name="using-azure-container-service"></a>Brug af Azure objektbeholder tjenesten
-----------------------------

Vores mål med Azure objektbeholder tjenesten er at tilvejebringe en objektbeholder, hosting-miljø ved hjælp af open source-værktøjer og -teknologier, der er populære blandt vores kunder i dag. Denne slutningen fremvise vi standard API slutpunkterne for din valgte orchestrator (DC/OS eller Docker Swarm). Ved hjælp af disse slutpunkter, kan du udnytte software, der kan taler med disse slutpunkter. For eksempel i tilfælde af slutpunktet Docker Swarm, kan du vælge at bruge kommandolinjen Docker (CLI). DC/OS, kan du vælge at bruge DCOS CLI.

<a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Oprette en Docker klynge ved hjælp af Azure objektbeholder tjenesten
-------------------------------------------------------

Hvis du vil begynde at bruge Azure objektbeholder tjenesten, kan du installere en Azure objektbeholder tjenesten klynge via portalen (Søg efter 'Azure objektbeholder tjenesten'), ved hjælp af en Azure ressourcestyring-skabelon ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm) eller [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)) eller med [CLI](/documentation/articles/xplat-cli-install/). De medfølgende Hurtig start skabeloner kan ændres for at medtage ekstra eller avancerede Azure konfiguration. Du kan finde flere oplysninger om installation af en Azure objektbeholder tjenesten klynge, [Implementer en Azure objektbeholder tjenesten klynge](container-service-deployment.md).

<a name="deploying-an-application"></a>Implementere et program
------------------------

Azure objektbeholder tjenesten giver valget mellem Docker Swarm eller DC/OS for organisering. Hvordan du installerer programmet, afhænger af dit valg af orchestrator.

### <a name="using-dcos"></a>Brug af DC/OS

DC/OS er et distribueret operativsystem baseret på kernen i distribuerede systemer Apache Mesos. Apache Mesos er placeret på Apache Software Foundation og viser nogle af de [største navne i IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) som brugere og bidragydere.

![Azure objektbeholder tjenesten er konfigureret til sværme, der viser supportmedarbejdere og mastere.](media/acs-intro/dcos.png)

DC/OS og Apache Mesos omfatter en imponerende funktionssæt:

-   Dokumenteret skalerbarhed

-   Fejlsikret replikeres master og slaves ved hjælp af Apache ZooKeeper

-   Understøttelse af Docker formateret objektbeholdere

-   Oprindelig isolationsniveauet mellem opgaver med Linux objektbeholdere

-   Multiresource planlægning (hukommelse, CPU, disk og porte)

-   Java, Python og C++ APIs til udvikling af nye parallelle programmer

-   En web brugergrænseflade til visning af klynge tilstand

Som standard omfatter DC/OS kører på Azure objektbeholder tjenesten Marathon organisering platform til planlægning af arbejdsbelastninger. Inkluderet med DC/OS installation af ACS er dog Mesosphere univers over tjenester, der kan føjes til din tjeneste dette omfatter tænding, Hadoop, Cassandra og meget mere.

![DC/OS univers i Azure objektbeholder tjenesten](media/dcos/universe.png)

#### <a name="using-marathon"></a>Brug af Marathon

Marathon er en klynge hele initialisering og kontrolelementet system for cgroups – eller, hvis det er Azure objektbeholder tjenesten, formateret som Docker beholdere-tjenester. Marathon indeholder en Webbrugergrænseflade, hvorfra du kan installere dine programmer. Du kan få adgang til det på en URL-adresse, der ser nogenlunde således `http://DNS_PREFIX.REGION.cloudapp.azure.com` hvor DNS\_PRÆFIKS og område defineres alle fra det øjeblik. Du kan naturligvis også angive dine egne DNS-navn. Du kan finde flere oplysninger om at køre en objektbeholder, ved hjælp af webdelen Marathon brugergrænseflade, [objektbeholder administration ved hjælp af internettet brugergrænseflade](container-service-mesos-marathon-ui.md).

![Listen over programmer Marathon](media/dcos/marathon-applications-list.png)

Du kan også bruge REST API'er til at kommunikere med Marathon. Der findes et antal klientbiblioteker, der er tilgængelige for hvert værktøj. De dækker en lang række sprog - og naturligvis du kan bruge HTTP-protokollen på alle sprog. Desuden understøttelse mange populære DevOps værktøjer af Marathon. Dette giver størst mulige fleksibilitet for teamet handlinger, når du arbejder med en Azure objektbeholder tjenesten klynge. Du kan finde flere oplysninger om at køre en objektbeholder ved hjælp af Marathon REST-API i [objektbeholder administration med REST-API](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Brug af Docker sværme

Docker sværme indeholder oprindelige databaseserver for Docker. Da Docker Swarm tjener standard Docker API, kan et værktøj, der allerede kommunikerer med en Docker daemon bruge sværme transparent skalere til flere værter på Azure objektbeholder tjenesten.

![Azure objektbeholder tjenesten er konfigureret til at bruge DC/OS – viser jumpbox, supportmedarbejdere og mastere.](media/acs-intro/acs-swarm2.png)

Understøttede værktøjer til administration af objektbeholdere på en sværme klynge omfatter, men er ikke begrænset til, følgende:

-   Dokku

-   Docker CLI og Docker Skriv

-   Krane

-   Jenkins

<a name="videos"></a>Videoer
------

Introduktion til Azure objektbeholder tjenesten (101):  

> [AZURE.VIDEO azure-container-service-101]

Opbygning af programmer ved hjælp af Azure objektbeholder tjenesten (Build 2016)

> [AZURE.VIDEO build-2016-building-applications-using-the-azure-container-service]
