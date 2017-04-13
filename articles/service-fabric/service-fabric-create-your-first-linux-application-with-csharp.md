<properties
   pageTitle="Oprette din første Service-strukturen program på Linux ved hjælp af C# | Microsoft Azure"
   description="Oprette og installere en tjeneste-strukturen programmet på computeren ved hjælp af C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Oprette din første Azure Service strukturen program

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Tjenesten strukturen indeholder SDK'er opbygning af tjenester på Linux i både .NET Core og Java. I dette selvstudium se vi på at oprette et program for Linux og opbygge en tjeneste bruger C# (.NET Core).

## <a name="prerequisites"></a>Forudsætninger

Før du går i gang, skal du kontrollere, at du har [oprettet din Linux udviklingsmiljø](service-fabric-get-started-linux.md). Hvis du bruger Mac OS X, kan du [konfigurere et Linux én boks miljø i et virtuelt ved hjælp af Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Oprette programmet

Et Service-strukturen program kan indeholde et eller flere tjenester, hver med en bestemt rolle i fremvisning af programmets funktionalitet. Service-strukturen SDK for Linux omfatter en [Yeoman](http://yeoman.io/) generator, der gør det nemt at oprette din første tjeneste og for at tilføje flere senere. Lad os bruge Yeoman til at oprette et program med en enkelt tjeneste.

1. I terminal skal du skrive følgende kommando for at begynde at opbygge stilladser:`yo azuresfcsharp`

2. Navngive dit program.

3. Vælg typen din første tjeneste, og kald den. Med henblik på dette selvstudium vælger vi en pålidelig Agent Service.

  ![Tjenesten strukturen Yeoman generator for C#][sf-yeoman]

>[AZURE.NOTE] Se [Service-strukturen programming model oversigt over](service-fabric-choose-framework.md)kan finde flere oplysninger om indstillingerne.

## <a name="build-the-application"></a>Opbygge programmet

Tjenesten strukturen Yeoman skabeloner omfatter et build-script, som du kan bruge til at oprette appen fra terminalen (efter at navigere til programmappen).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Starte test-klienten og udføre en failover

Agent projekter gør ikke noget på egen hånd. De kræver en anden tjeneste eller -klienten til at sende dem meddelelser. Skabelonen Agent omfatter en simpel test-script, som du kan bruge til at interagere med tjenesten Agent.

1. Kør scriptet ved hjælp af værktøjet værdikontrol til at se output fra tjenesten Agent.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Find node, der er vært for den primære replika for tjenesten Agent i tjenesten strukturen Stifinder. I skærmbilledet nedenfor er det node 3.

    ![Finde den primære replika i tjenesten strukturen Explorer][sfx-primary]

3. Klik på noden du fandt i det forrige trin, og derefter vælge **Deaktiver (genstart)** i menuen Handlinger. Denne handling genstarter en af de fem noder i din lokale klynge for at tvinge en failover til en sekundær replika, der kører på en anden node. Når du udfører denne handling, du være opmærksom på output fra test klienten og Bemærk, at tælleren fortsætter med at øge på trods af sekundære.


## <a name="next-steps"></a>Næste trin

- [Lær mere om pålidelig aktører](service-fabric-reliable-actors-introduction.md)
- [Arbejde med tjenesten strukturen klynger ved hjælp af Azure CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
