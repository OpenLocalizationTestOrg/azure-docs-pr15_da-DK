<properties
   pageTitle="Analysere føler data med Apache Storm og HBase | Microsoft Azure"
   description="Lær at oprette forbindelse til Apache Storm med et virtuelt netværk. Bruge Storm med HBase til at behandle føler data fra en hændelse hub og visualisere med D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analysere føler data med Apache Storm, begivenhed Hub og HBase i HDInsight (Hadoop) 

Lær at bruge Apache Storm på HDInsight til at behandle føler data fra Azure begivenhed Hub, gemme den i Apache HBase på HDInsight og visualisere ved hjælp af D3.js kører som en Azure Web App.

Skabelonen Azure ressourcestyring brugt i dette dokument viser, hvordan du kan oprette flere Azure ressourcer i en ressourcegruppe. Nærmere betegnet kan oprettes et virtuelt Azure-netværk, to HDInsight clusters (Storm og HBase) og en Azure Web App. En node.js implementering af et dashboard i realtid web installeres automatisk til online.

> [AZURE.NOTE] Oplysningerne i dette dokument, og det viste eksempel, er testet ved hjælp af Linux-baserede HDInsight 3.3 og 3,4 klynge versioner.

## <a name="prerequisites"></a>Forudsætninger

* Et Azure-abonnement. Se [få Azure gratis prøveversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Du behøver ikke en eksisterende HDInsight klynge; trinnene i dette dokument oprettes i følgende ressourcer:
    >
    > * En Azure virtuelt netværk
    > * En Storm på HDInsight klynge (Linux-baserede, 2 arbejder noder)
    > * En HBase på HDInsight klynge (Linux-baserede, 2 arbejder noder)
    > * En Azure-WebApp, der er vært dashboardet web

* [Node.js](http://nodejs.org/): Dette bruges til at få vist web dashboard lokalt på din udviklingsmiljø.

* [Java og JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): bruges til at udvikle Storm topologien.

* [Maven](http://maven.apache.org/what-is-maven.html): bruges til at opbygge og samle projektet.

* [Ciffer](http://git-scm.com/): bruges til at hente projektet fra GitHub.

* En __SSH__ -klient: bruges til at oprette forbindelse til Linux-baserede HDInsight klynger. Du kan finde flere oplysninger om brug af SSH med HDInsight, følgende dokumenter.

    * [Bruge SSH med HDInsight fra en Windows-klient](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Bruge SSH med HDInsight fra en Linux, Unix eller Mac-klient](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Du skal også have adgang til den `scp` kommandoen, som bruges til at kopiere filer mellem dit lokale udviklingsmiljø og den HDInsight klynge, ved hjælp af SSH.

## <a name="architecture"></a>Arkitektur

![arkitektur diagram](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

I dette eksempel består af følgende komponenter:

* **Azure begivenhed Hubs**: indeholder data, der indsamles fra sensorer. Et program er i dette eksempel skal leveres, genererer dataene.

* **Storm på HDInsight**: giver realtid behandling af data fra begivenhed Hub.

* **HBase på HDInsight**: indeholder et fast NoSQL datalager til data, når den er blevet behandlet af Storm.

* **Azure virtuelle Netværkstjeneste**: giver mulighed for sikker kommunikation mellem Storm på HDInsight og HBase på HDInsight klynger.

    > [AZURE.NOTE] Et virtuelt netværk er påkrævet for at kunne bruge API'EN Java HBase klient, som den ikke vises over offentlige gatewayen til HBase klynger. Installere HBase og Storm klynger i det samme virtuelle netværk kan Storm klynge (eller et andet på det virtuelle netværk) direkte adgang til HBase ved hjælp af klienten API.

* **Dashboard-websted**: et eksempeldashboard, der diagrammer data i realtid.

    * Webstedet er implementeret i Node.js, så den kan køre på operativsystemer klient til test, eller den kan installeres på Azure websteder.

    * [Socket.IO](http://socket.io/) bruges til kommunikation i realtid mellem Storm topologi og det offentlige websted.

        > [AZURE.NOTE] Dette er en implementering detaljer. Du kan bruge en hvilken som helst kommunikation framework såsom rå WebSockets eller SignalR.

    * [D3.js](http://d3js.org/) bruges til at afbilde de data, der sendes til webstedet.

> [AZURE.IMPORTANT] To klynger er påkrævet, som der er ingen understøttet metode til at oprette en HDInsight klynge til både Storm og HBase.

Topologien læser data fra begivenhed Hub ved hjælp af klassen [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) og skriver data til HBase ved hjælp af klassen [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) . Kommunikation med webstedet opnås ved hjælp af [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

Følgende er et diagram over topologien.

![Topologidiagram](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Dette er en meget forenklet visning af topologien. På kørselstidspunktet oprettes en forekomst af hver komponent for hver partition til hubben begivenhed, der læses. Disse forekomster fordeles på tværs af knuderne i klyngen, og data er distribueret mellem dem på følgende måde:
>
> * Data fra tud til parseren er Indlæs afstemmes.
> * Data fra parseren til Dashboard og HBase er grupperet efter enheds-ID, så meddelelser fra den samme enhed flyde altid til den samme komponent.

### <a name="topology-components"></a>Søgetopologikomponenter

* **EventHub tud**: tud er angivet som en del af Apache Storm version 0.10.0 og nyere.

    > [AZURE.NOTE] Den begivenhed Hubs tud i dette eksempel bruges kræver en Storm på HDInsight klynge version 3.3 eller 3.4. Du kan finde oplysninger om, hvordan du bruger begivenhed Hubs med en ældre version af Storm på HDInsight [proces begivenheder fra Azure begivenhed hubber med Storm på HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: de data, der udsendes af tud er rå JSON, og nogle gange mere end én begivenhed udsendes ad gangen. Denne bolt viser, hvordan at læse de data, der udsendes af tud og udsende dem til en ny stream som en tupel, der indeholder flere felter.

* **DashboardBolt.java**: Dette viser hvordan du bruger biblioteket Socket.io klient til Java til at sende data realtid til web dashboard.

I dette eksempel bruges framework [frigivelse](https://storm.apache.org/releases/0.10.0/flux.html) , så topologi definitionen er indeholdt i YAML filer. Der er to:

* __ingen hbase.yaml__ - Brug denne fil, når du tester topologien i dit udviklingsmiljø. Den bruge ikke HBase komponenter, da du ikke har adgang til HBase Java API fra uden for det virtuelle netværk klyngen bor i.

* __med hbase.yaml__ - Brug denne fil, når du installerer topologien på Storm klynge. Det bruger HBase komponenter, da der køres i det samme virtuelle netværk som HBase klynge.

## <a name="prepare-your-environment"></a>Forberede dit miljø

Før du bruger i dette eksempel, skal du oprette en Azure begivenhed Hub, som Storm topologien læser fra.

### <a name="configure-event-hub"></a>Konfigurere begivenhed Hub

Begivenhed Hub er datakilde i dette eksempel. Brug følgende trin til at oprette en ny begivenhed Hub.

1. [Azure-portalen](https://portal.azure.com), Vælg **+ Ny** -> __Internet ting__ -> __Begivenhed Hubs__.

2. På bladet __Oprette Namespace__ du udføre følgende opgaver:

    1. Angiv et __navn__ til navneområdet.
    2. Vælg et priser trin. __Grundlæggende__ er tilstrækkelige til dette eksempel.
    3. Vælg det Azure __abonnement__ til brug.
    4. Enten vælge et eksisterende ressourcegruppe eller oprette en ny.
    5. Vælg __placeringen__ til den begivenhed Hub.
    6. Vælg __Fastgør til dashboard__, og klik derefter på __Opret__.

3. Når processen til oprettelse af er fuldført, vises bladet begivenhed Hubs for din navneområde. Vælg __+ Tilføj begivenhed Hub__her. Angiv et navn på __sensordata__ på bladet __Oprette begivenhed-Hub__ , og vælg derefter __Opret__. Lade de andre felter på standardværdierne.

4. Vælg __Begivenhed Hubs__bladet begivenhed Hubs for din navneområde. Vælg et __sensordata__ element.

5. Vælg __delt access politikker__bladet for sensordata begivenhed Hub. Brug __+ Tilføj__ linket til at tilføje følgende politikker:


  	| Navn på politik | Krav |
  	| ----- | ----- |
  	| enheder | Send |
  	| storm | Lytte |

5. Vælg begge politikker og notere den __PRIMÆRNØGLE__ -værdi. Du skal bruge værdien for begge politikker i fremtidige trin.

## <a name="download-and-configure-the-project"></a>Hente og konfigurere projektet

Du kan bruge følgende til at hente projektet fra GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Når kommandoen er fuldført, kan du bruge følgende mappestrukturen:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Dette dokument går ikke at få flere detaljer i den kode, der er inkluderet i dette eksempel Koden er dog fuldt kommenterede.

Åbn filen **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** og oplysninger om begivenhed Hub føjes til følgende linjer:

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] I dette eksempel antages det, du brugte __storm__ som navnet på politikken, der indeholder et __lytte__ krav og, at din begivenhed Hub hedder __sensordata__.

 Gem filen, når du har tilføjet disse oplysninger.

## <a name="compile-and-test-locally"></a>Samle og teste lokalt

Før test, skal du starte dashboardet for at se output for topologien og generere data til at gemme i begivenhed Hub.

> [AZURE.IMPORTANT] Komponenten HBase af denne topologi er ikke aktiv, når test lokalt, som Java-API til HBase klynge ikke kan åbnes fra uden for det virtuelle Azure-netværk, der indeholder klynger.

### <a name="start-the-web-application"></a>Starte-webprogrammet

1. Åbne en ny kommandoprompt eller terminal, og skifte til **hdinsight-eventhub-eksempel/dashboard**, og derefter bruge følgende kommando til at installere de afhængigheder, der bruges til webprogrammet:

        npm install

2. Brug følgende kommando til at starte webprogrammet:

        node server.js

    Du får vist en meddelelse, der ligner følgende:

        Server listening at port 3000

2. Åbne en webbrowser, og angiv **http://localhost:3000 /** som adressen. Du burde se en side, der ligner følgende:

    ![Web dashboard](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    Lad denne kommandoprompt eller terminal åben. Efter tester, skal du bruge Ctrl + C for at stoppe Webserveren.

### <a name="start-generating-data"></a>Begynde at oprette data

> [AZURE.NOTE] Trinnene i dette afsnit Brug Node.js, så de kan bruges på alle platforme. Finde mappen **SendEvents** på andre sprog eksempler.

1. Åbn en ny kommandoprompt, shell eller terminal, og skifte til **hdinsight-eventhub-eksempel/SendEvents/nodejs**, og derefter bruge følgende kommando til at installere de afhængigheder, der skal bruges af programmet:

        npm install

2. Åbn filen **app.js** i et tekstredigeringsprogram, og Tilføj de begivenhed Hub oplysninger, du har hentet tidligere:

        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
    
    > [AZURE.NOTE] I dette eksempel antages det, at du har brugt __sensordata__ som navnet på din begivenhed Hub og __enheder__ som navnet på politikken, der indeholder et __sende__ krav.

2. Brug følgende kommando til at indsætte nye poster i begivenhed Hub:

        node app.js

    Du bør se flere tekstlinjer output, der indeholder de data, der sendes til begivenhed Hub. Disse vises som følger:

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Starte topologien

2. Åbn en ny kommandoprompt, shell eller terminal og ændre mapper til __Hdinsight-eventhub-eksempel/TemperatureMonitor__, og derefter bruge følgende kommando for at starte topologien:

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Hvis du bruger PowerShell, skal du bruge følgende i stedet:

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Hvis du er på et Unix-Linux/OS X-system, og du har [installeret Storm i dit udviklingsmiljø](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), kan du bruge følgende kommandoer i stedet:
    >
    > `mvn compile package`
    > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

    Dette starter topologien defineret i filen __ingen hbase.yaml__ i lokal tilstand. De værdier, der er indeholdt i filen __dev.properties__ indeholder forbindelsesoplysninger for begivenhed Hubs. Når den er startet, topologien læser poster fra begivenhed Hub og sender dem til dashboard, der kører på din lokale computer. Du burde se linjer, der vises i dashboardet web, som følger:

    ![dashboard med data](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Mens dashboardet kører, kan du bruge den `node app.js` kommando fra de forrige trin til at sende nye data til begivenhed Hubs. Da der oprettes tilfældigt temperaturen værdierne, skal opdatere diagrammet for at vise store ændringer i temperaturen.

    > [AZURE.NOTE] Du skal være i mappen __Hdinsight-eventhub-eksempel/SendEvents/Nodejs__ , når du bruger den `node app.js` kommandoen.

3. Stop topologien ved hjælp af Ctrl + C efter ved at bekræfte, at det fungerer. Du kan bruge Ctrl + C for at stoppe lokale webserveren også.

## <a name="create-a-storm-and-hbase-cluster"></a>Oprette en Storm og HBase klynge

For at køre topologien på HDInsight, og til at aktivere HBase bolt, skal du oprette en ny Storm klynge og HBase klynge. Trinnene i dette afsnit Brug en [Azure ressourcestyring skabelon](../resource-group-template-deploy.md) til at oprette en ny Azure virtuelle Netværks- og en Storm og HBase klynge på det virtuelle netværk. Skabelonen også opretter en Azure-WebApp og installerer en kopi af dashboardet i den.

> [AZURE.NOTE] Et virtuelt netværk bruges, så den kører på Storm klynge topologi direkte kan kommunikere med den HBase klynge, ved hjælp af HBase Java API.

Skabelonen ressourceleder, der bruges i dette dokument er placeret i en offentlig blob objektbeholder på __https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Klik på knappen følgende for at logge på Azure og åbne skabelonen ressourcestyring i portalen Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Angiv følgende oplysninger fra bladet **parametre** :

    ![HDInsight parametre](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: Denne værdi, der skal bruges som basis navnet på Storm og HBase klynger. For eksempel opretter at angive __hdi__ en Storm klynge med navnet __storm hdi__ og en HBase klynge med navnet __hbase hdi__.
    * __CLUSTERLOGINUSERNAME__: admin brugernavnet for Storm og HBase klynger.
    * __CLUSTERLOGINPASSWORD__: brugeren administratoradgangskode til Storm og HBase klynger.
    * __SSHUSERNAME__: feltet SSH bruger til at oprette til Storm og HBase klynger.
    * __SSHPASSWORD__: adgangskoden til brugerens SSH til Storm og HBase klynger.
    * __Placering__: det område, som klynger oprettes i.
    
    Klik på __OK__ for at gemme parametrene.
    
3. Brug sektionen __ressourcegruppe__ til at oprette en ny ressourcegruppe eller vælge en eksisterende liste.

4. Vælg den samme placering i rullemenuen __ressource gruppe placering__ , som du har valgt for parameteren __placering__ .

5. Vælge __juridiske betingelser__, og vælg derefter __Opret__.

6. Til sidst skal __Fastgør til dashboard__ , og vælg derefter __Opret__. Det tager om 20 minutter til at oprette klynger.

Når ressourcerne, der er oprettet, omdirigeres du til en blade for ressourcegruppen, der indeholder klynger og web dashboard.

![Ressource gruppe blade for vnet og klynger](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Bemærk, at navnene på HDInsight klynger er __storm BASENAME__ og __hbase BASENAME__, hvor BASENAME er det navn, du har angivet til skabelonen. Du skal bruge disse navne i efterfølgende trin, når du opretter forbindelse til klynger. Bemærk også, at navnet på webstedet dashboard er __basename-dashboard__. Du skal bruge dette senere, når du får vist dashboard.

## <a name="configure-the-dashboard-bolt"></a>Konfigurere Dashboard bolt

For at kunne sende data til dashboard installeres som en WebApp, skal du ændre følgende linje i filen __dev.properties__ :

    dashboard.uri: http://localhost:3000

Ændre `http://localhost:3000` til `http://BASENAME-dashboard.azurewebsites.net` og gemme filen. Erstatte __BASENAME__ med det grundlæggende navn, du har angivet i ovenstående trin. Du kan også bruge ressourcegruppen tidligere har oprettet for at markere dashboardet og få vist URL-adressen.

## <a name="create-the-hbase-table"></a>Oprette tabellen HBase

For at gemme data i HBase, skal vi først oprette en tabel. Du vil normalt allerede oprette ressourcer, der skal skrives til Storm, som forsøger at oprette flere, fordelt kopier af den kode, der forsøger at oprette den samme ressource kan medføre ressourcer fra i en Storm topologi. Oprette ressourcer uden for topologien og blot bruge Storm til læse/skrive- og analyser.

1. Brug SSH til at oprette forbindelse til den HBase klynge, ved hjælp af det SSH bruger og den adgangskode, du har angivet til skabelonen under oprettelse af klynge. Eksempelvis hvis oprette forbindelse via den `ssh` kommando, du vil bruge følgende syntaks:

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Erstat __brugernavn__ i denne kommando med SSH brugernavnet, du angav, da oprette klynge og __BASENAME__ med det grundlæggende navn, du har angivet. Når du bliver bedt om det, kan du angive adgangskoden til brugerens SSH.

2. Start af HBase shell fra session SSH.

        hbase shell
    
    Når shell har indlæst, får du vist en `hbase(main):001:0>` prompt.

3. Skriv følgende kommando for at oprette en tabel for at gemme føler data fra shell HBase:

        create 'SensorData', 'cf'

4. Kontrollere, at tabellen er blevet oprettet ved hjælp af følgende kommando:

        scan 'SensorData'
        
    Dette returnerer oplysninger ligner i følgende eksempel, der angiver, at der ikke er 0 rækker i tabellen.
    
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Angiv følgende for at afslutte HBase shell:

        exit

## <a name="configure-the-hbase-bolt"></a>Konfigurere HBase bolt

Hvis du vil skrive til HBase fra Storm klynge, skal du angive HBase bolt med oplysninger om konfigurationen af din HBase klynge. Den nemmeste måde at gøre dette er at hente __hbase site.xml__ fra klyngen og medtage dem i dit projekt. Du skal også Fjern kommentar fra flere afhængigheder i filen __pom.xml__ som indlæser storm hbase komponent og nødvendige afhængigheder.

> [AZURE.IMPORTANT] Du skal også hente filen storm hbase.jar findes på din Storm på HDInsight klynge 3.3 eller 3.4 klynge; Denne version er kompileret til at arbejde med HBase 1.1.x, som bruges til HBase på HDInsight 3.3 og 3,4 klynger. Hvis du bruger en storm hbase komponent fra et andet sted, er det måske kompileret mod en ældre version af HBase.

### <a name="download-the-hbase-sitexml"></a>Hente hbase-site.xml

Brug SCP til at hente filen __hbase site.xml__ fra klyngen fra en kommandoprompt. Erstat __brugernavn__ i det følgende eksempel med SSH brugeren du angav, da oprette klynge og __BASENAME__ med det grundlæggende navn, du har angivet tidligere. Når du bliver bedt om det, kan du angive adgangskoden til brugerens SSH. Erstatte den `/path/to/TemperatureMonitor/resources/hbase-site.xml` med stien til filen i TemperatureMonitor projektet.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Dette vil hente __hbase site.xml__ til den angivne sti.

### <a name="download-and-install-the-storm-hbase-component"></a>Downloade og installere komponenten storm hbase

1. Brug SCP til at hente filen __storm hbase.jar__ fra Storm klynge fra en kommandoprompt. Erstat __brugernavn__ i det følgende eksempel med SSH brugeren du angav, da oprette klynge og __BASENAME__ med det grundlæggende navn, du har angivet tidligere. Når du bliver bedt om det, kan du angive adgangskoden til brugerens SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Dette vil hente en fil med navnet `storm-hbase-####.jar`, hvor ### er versionsnummeret for Storm for denne klynge. Skal du notere af tal, som de anvendes senere.

2. Brug følgende kommando for at installere denne komponent i det lokale lager Maven på dit udviklingsmiljø. Dette gør det muligt for Maven til at finde pakken ved sammensætning af projektet. Erstatte __####__ med versionsnummeret inkluderet i filnavnet.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Hvis du bruger PowerShell, kan du bruge følgende kommando:

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Aktivere komponenten storm hbase i projektet

1. Åbn filen __TemperatureMonitor/pom.xml__ og Slet følgende linjer:

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Kun slette disse to linjer Slet ikke nogen af linjerne mellem dem.
    
    Dette gør det muligt for flere komponenter, der skal bruges, når du kommunikerer med HBase ved hjælp af hbase bolt.

2. Find følgende linjer, og derefter erstatte __####__ med versionsnummeret for den storm hbase-fil, du gemte tidligere.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] Versionsnummeret skal stemme overens med den version, du brugte, da installere komponenten til lokale Maven lageret, som Maven bruger disse oplysninger til at indlæse komponenten, når du opbygger projektet.

2. Gem filen __pom.xml__ .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Opbygge, pakke og anvende løsningen til HDInsight

Brug følgende trin til at installere Storm topologien til storm klynge i dit udviklingsmiljø.

1. Brug følgende kommando til at udføre et nyt build og oprette en glas pakke fra projektet fra mappen __TemperatureMonitor__ :

        mvn clean compile package

    Dette vil oprette en fil med navnet **TemperatureMonitor-1.0-SNAPSHOT.jar** i **destinationsmappen for projektet** .

2. Bruge scp til at overføre filen __TemperatureMonitor-1.0-SNAPSHOT.jar__ til din Storm klynge. Erstat __brugernavn__ i det følgende eksempel med SSH brugeren du angav, da oprette klynge og __BASENAME__ med det grundlæggende navn, du har angivet tidligere. Når du bliver bedt om det, kan du angive adgangskoden til brugerens SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Det kan tage flere minutter at overføre filen, da det vil være flere MB.

    Bruge scp til at overføre filen __dev.properties__ , som indeholder de oplysninger, der bruges til at oprette forbindelse til begivenhed hubber og dashboardet.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Når filerne er blevet overført, kan du oprette forbindelse til den klynge ved hjælp af SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Brug følgende kommando for at starte topologien fra session SSH.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Dette starter topologien ved hjælp af topologi definitionen i filen __med hbase.yaml__ og konfiguration af værdierne i filen __dev.properties__ .

3. Når topologien er startet, skal du åbne en browser til det websted, du har publiceret på Azure, derefter bruge den `node app.js` kommandoen til at sende data til begivenhed Hub. Du bør se dashboardet web update for at få vist oplysningerne.

    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Få vist HBase data

Når du har sendt data til den topologi ved hjælp af `node app.js`, følge nedenstående trin for at oprette forbindelse til HBase og bekræfte, at dataene er blevet skrevet til den tabel, du oprettede tidligere.

1. Brug SSH til at oprette forbindelse til HBase klynge.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Start af HBase shell fra session SSH.

        hbase shell
    
    Når shell har indlæst, får du vist en `hbase(main):001:0>` prompt.

2. Få vist rækker fra tabellen:

        scan 'SensorData'
        
    Dette skal returnere oplysninger stil med følgende, der angiver, at der ikke er 0 rækker i tabellen.
    
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Handlingen scanning returnerer kun op til 10 rækker fra tabellen.

## <a name="delete-your-clusters"></a>Slette din klynger

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

For at slette klynger, lager og online ad gangen, skal du slette ressourcegruppen, der indeholder dem.

## <a name="next-steps"></a>Næste trin

Du har nu lært, hvordan du bruger Storm til at læse data fra begivenhed Hubs, gemme den i HBase og visualisere oplysningerne på en ekstern dashboard ved hjælp af Socket.io og D3.js.

* Du kan finde flere eksempler på Storm topologier med HDInsight, i:

    * [Eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md)

* Du kan finde flere oplysninger om Apache Storm, webstedet [Apache Storm](https://storm.incubator.apache.org/) .

* Du kan finde flere oplysninger om HBase på HDInsight [HBase med HDInsight oversigt](hdinsight-hbase-overview.md).

* Du kan finde flere oplysninger om Socket.io, webstedet [socket.io](http://socket.io/) .

* Du kan finde flere oplysninger om D3.js, [D3.js - Data indsatsbaserede dokumenter](http://d3js.org/).

* Du kan finde oplysninger om oprettelse af topologier i Java [udvikle Java topologier for Apache Storm på HDInsight](hdinsight-storm-develop-java-topology.md).

* Du kan finde oplysninger om oprettelse af topologier i .NET [udvikle C# topologier for Apache Storm på HDInsight ved hjælp af Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
