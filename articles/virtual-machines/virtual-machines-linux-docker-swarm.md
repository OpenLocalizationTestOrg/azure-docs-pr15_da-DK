<properties
   pageTitle="Introduktion til brug af docker med sværme på Azure"
   description="I denne artikel beskrives, hvordan du opretter en gruppe af FOS med filtypenavnet Docker VM og bruge sværme til at oprette en Docker klynge."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>Sådan bruges docker med sværme

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Dette emne vises en meget simpel måde at bruge [docker](https://www.docker.com/) med [swarm](https://github.com/docker/swarm) til at oprette en sværme rettighedsstyret klynge på Azure. Det opretter fire virtuelle maskiner i Azure, en skal fungere som sværme manager, og tre som en del af klynge af docker hosts. Når du er færdig, kan du bruge sværme at se klyngen og derefter begynde at bruge docker på den. Desuden bruge Azure CLI opkald i dette emne tilstanden service management (asm). 

> [AZURE.NOTE] Dette emne bruges docker med sværme og den Azure CLI *uden* brug af **docker computer** for at vise, hvordan de forskellige værktøjer fungerer sammen, men fortsat uafhængige. **docker computer** har **– swarm** parametre, der gør det muligt at bruge **docker machine** til at føje direkte noder til en sværme. Et eksempel, i dokumentationen til [docker-computer](https://github.com/docker/machine) . Hvis du ikke har set **docker maskine** kører mod Azure FOS, se, [hvordan du bruger docker computer med Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Oprette docker hosts med virtuelle Azure-computere

Dette emne opretter fire FOS, men du kan bruge en hvilken som helst tal, du ønsker. Ringe til følgende med * &lt;adgangskode&gt; * erstattes af den adgangskode, du har valgt.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Når du er færdig, skal du kunne bruge **azure vm liste** for at se din Azure FOS:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>Installerer sværme på sværme master VM

I dette emne bruges den [objektbeholder model af installationen fra docker swarm dokumentation](https://github.com/docker/swarm#1---docker-image) – men du kan også SSH **sværme master**. I denne model hentes **swarm** som docker beholder kører sværme. Nedenfor udføre vi dette trin *ved hjælp fra vores bærbare computer ved hjælp af docker* for at oprette forbindelse til den **sværme master** VM og beder om at bruge kommandoen klynge id oprettelse, **sværme oprette**. Klynge id'et er, hvordan **swarm** opdager medlemmerne af gruppen sværme. (Du kan også klone lageret og opbygge det selv, hvilket giver dig fuld kontrol og aktivere fejlfinding.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Den sidste linje er klynge-id. kopiere det sted, fordi du skal bruge det igen, når du deltager i noden FOS sværme master til at oprette "sværme". I dette eksempel er klynge id **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Kun, hvis du vil rene bruger vi vores lokale docker installation at oprette forbindelse til **sværme master** VM i Azure og instruktion **sværme master** for at hente, installere og køre kommandoen **Opret** , som returnerer vores klynge-id, som vi bruger til registrering formål senere.
<!-- -->
> For at bekræfte, at køre `docker -H tcp://` * &lt;hostname&gt; * ` images` med listen over objektbeholder processerne på maskinen **sværme master** og på en anden node til sammenligning (da vi stødte kommandoen forrige sværme med parameteren **– ressourcestyring** , beholderen er blevet fjernet, når den er færdig, så bruger **docker ps - a** ikke vil returnere noget).:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Hvis du kender til **docker**, kan du se, at de andre noder har ingen poster, fordi ingen billeder har hentet og køre endnu.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Deltage i noden FOS til vores docker klynge

Få vist slutpunkt oplysninger ved hjælp af Azure CLI for hver node. Nedenfor gøre vi det til værten for docker **sværme-node-1** for at få den node docker port.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Brug af **docker** og `-H` mulighed for at pege docker klienten på din node VM, deltage i pågældende knude til den sværme, du opretter ved at overføre det klynge-id og den node docker port (disse ved hjælp af **– adresse**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Der ser godt. Sådan kontrollerer du, **swarm** der kører på **sværme-node-1** vi Skriv:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Gentag for alle de andre noder i klyngen. I dette tilfælde gøre vi det til **sværme-node-2** og **sværme-node-3**.

## <a name="begin-managing-the-swarm-cluster"></a>Begynde at administrere sværme klynge

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

og derefter kan du angive ud af dine noder i din klynge:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Gå køre ting på din sværme. Du kan lede efter inspiration, se [https://github.com/docker/swarm/](https://github.com/docker/swarm/)eller måske en [video](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
