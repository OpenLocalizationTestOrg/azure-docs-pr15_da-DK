<properties
   pageTitle="Service-strukturen og implementering beholdere i Linux | Microsoft Azure"
   description="Service-strukturen og brug af Docker beholdere at implementere microservice programmer. I denne artikel beskrives de funktioner, der indeholder Service-strukturen af objektbeholdere og hvordan du installerer et Docker objektbeholder billede i en klynge"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Anvende en Docker objektbeholder til tjenesten struktur

> [AZURE.SELECTOR]
- [Installere Windows objektbeholder](service-fabric-deploy-container.md)
- [Installere Docker objektbeholder](service-fabric-deploy-container-linux.md)

I denne artikel fører dig gennem oprettelsen bulkvareenhed tjenester i Docker beholdere på Linux.

Tjenesten strukturen har flere objektbeholder-funktioner, der hjælper dig med at opbygge programmer, der består af microservices, der er bulkvareenhed. Disse kaldes bulkvareenhed tjenester.

Funktionerne omfatter;

- Objektbeholder billede installation og aktivering
- Ressource styring
- Lager godkendelse
- Objektbeholder port til host porttilknytning
- Objektbeholder-objektbeholder registrering og kommunikation
- Mulighed for at konfigurere og angive miljøvariabler


## <a name="packaging-a-docker-container-with-yeoman"></a>Pakke en docker objektbeholder med yeoman
Når emballagen en objektbeholder på Linux, kan du vælge enten at bruge en yeoman skabelon eller [oprette programpakken manuelt](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

En tjeneste-strukturen programmet kan indeholde et eller flere beholdere, hver med en bestemt rolle i fremvisning af programmets funktionalitet. Service-strukturen SDK for Linux omfatter en [Yeoman](http://yeoman.io/) generator, der gør det nemt at oprette et program og tilføje en objektbeholder billede. Lad os bruge Yeoman til at oprette et nyt program med en enkelt Docker objektbeholder med navnet *SimpleContainerApp*. Du kan tilføje flere tjenester senere ved at redigere den genererede manifestet filer.

## <a name="create-the-application"></a>Oprette programmet

1. I terminal skal du skrive **yo azuresfguest**.

2. Vælg **objektbeholder** til framework.

3. Navngive dit program fx SimpleContainerApp

4. Angiv URL-adressen til objektbeholder billedet fra en DockerHub repo. Det tager formularen [repo] / [billede navn]

![Tjenesten strukturen Yeoman generator af objektbeholdere][sf-yeoman]

## <a name="deploy-the-application"></a>Installere programmet

Når programmet er oprettet, kan du installere det i den lokale klynge ved hjælp af Azure CLI.

1. Oprette forbindelse til den lokale Service-strukturen klynge.

    ```bash
    azure servicefabric cluster connect
    ```

2. Bruge scriptet Installer angivet i skabelonen til at kopiere programpakken til den klynge billede af store og registrere programtypen og oprette en forekomst af programmet.

    ```bash
    ./install.sh
    ```

3. Åbn en browser, og gå til tjenesten strukturen Explorer på http://localhost:19080/Explorer (Erstat localhost med den private IP-VM Hvis bruger Vagrant på Mac OS X).

4. Udvid noden programmer, og Bemærk, at der nu en post for dit programtype og et andet til den første forekomst af den pågældende type.

5. Bruge scriptet Fjern angivet i skabelonen til at slette forekomsten af programmet og fjerne registreringen af programtypen.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Næste trin

- [Oversigt over Service-strukturen og beholdere](service-fabric-containers-overview.md)
- [Arbejde med tjenesten strukturen klynger ved hjælp af Azure CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

