<properties
   pageTitle="Tip til brug af Hadoop på Linux-baserede HDInsight | Microsoft Azure"
   description="Få tip til implementering ved brug af Linux-baserede HDInsight (Hadoop) klynger på et velkendte Linux-miljø, der kører i Azure skyen."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Oplysninger om brug af HDInsight på Linux

Linux-baserede Azure HDInsight klynger giver Hadoop på et velkendte Linux-miljø, kører i Azure skyen. Det skal fungere nøjagtigt, som andre Hadoop på Linux-installation for de fleste ting. Dette dokument opkald ud specifikke forskelle, du skal være opmærksom på.

##<a name="prerequisites"></a>Forudsætninger

Mange af trinnene i dette dokument bruge følgende værktøjer, som kan være nødvendigt at være installeret på din computer.

* [cURL](https://curl.haxx.se/) - bruges til at kommunikere med webbaserede tjenester
* [jq](https://stedolan.github.io/jq/) - bruges til at analysere JSON dokumenter
* [Azure CLI](../xplat-cli-install.md) - bruges til at administrere eksternt Azure tjenester

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Domænenavne

Fuldstændige domænenavn (fulde Domænenavn), hvis du vil bruge, når du opretter forbindelse til klyngen fra internettet er ** &lt;clustername >. azurehdinsight.net** eller (for kun SSH) ** &lt;clustername-ssh >. azurehdinsight.net**.

Internt, har hver node i klyngen et navn, der er tildelt under klyngekonfigurationen af. For at finde klynge navnene, kan du gå til siden __Hosts__ på Ambari Webbrugergrænsefladen eller bruge følgende for at returnere en liste over værter fra Ambari REST-API:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Erstat __adgangskode__ med adgangskode af administratorkonto og __CLUSTERNAME__ med navnet på din klynge. Dette vil returnere en JSON-dokument, der indeholder en liste over værter i klyngen, og klik derefter jq trækker på `host_name` elementværdi for hver vært i klyngen.

Hvis du har brug at finde navnet på knuden til en bestemt tjeneste, kan du forespørge Ambari for den pågældende komponent. For eksempel for at finde hosts for noden HDFS navn, skal bruge følgende.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Returnerer et JSON-dokument, der beskriver tjenesten, og derefter jq trækker ud af, kun den `host_name` værdi for værterne.

## <a name="remote-access-to-services"></a>Ekstern adgang til tjenester

* **Ambari (web)** - https://&lt;clustername >. azurehdinsight.net

    Godkende ved hjælp af klynge administratorbruger og adgangskode, og derefter logge på Ambari. Det bruges også klynge administratorbruger og adgangskode.

    Godkendelse er almindelig tekst – Brug altid HTTPS for at sikre, at forbindelsen er beskyttet.

    > [AZURE.IMPORTANT] Mens Ambari for din klynge er tilgængelige direkte via internettet, er visse funktioner afhængig af adgang til noder ved bruges af klyngen interne domænenavnet. Da dette er en intern domæne navn, og ikke offentlig, får du "server blev ikke fundet" fejl, når du forsøger at få adgang til visse funktioner via internettet.
    >
    > Hvis du vil bruge alle funktionerne i webdelen Ambari brugergrænseflade, kan bruge en SSH tunnel til proxy webtrafik til Klyngenoden hoved. Se [Brug SSH tunnel til adgang til Ambari web brugergrænseflade, Ressourcestyring, JobHistory, NameNode, Oozie, og andre web Brugergrænsefladens](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (RESTEN)** - https://&lt;clustername >.azurehdinsight.net/ambari

    > [AZURE.NOTE] Godkende ved hjælp af klynge administratorbruger og en adgangskode.
    >
    > Godkendelse er almindelig tekst – Brug altid HTTPS for at sikre, at forbindelsen er beskyttet.

* **WebHCat (Templeton)** - https://&lt;clustername >.azurehdinsight.net/templeton

    > [AZURE.NOTE] Godkende ved hjælp af klynge administratorbruger og en adgangskode.
    >
    > Godkendelse er almindelig tekst – Brug altid HTTPS for at sikre, at forbindelsen er beskyttet.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net på port 22 eller 23. Port 22 bruges til at oprette forbindelse til den primære headnode, mens 23 bruges til at oprette forbindelse til sekundært. Se [tilgængelighed og pålidelighed af Hadoop klynger i HDInsight](hdinsight-high-availability-linux.md)kan finde flere oplysninger om noderne hoved.

    > [AZURE.NOTE] Du kan kun åbne klyngenoderne hoved gennem SSH fra en klientcomputer. Når forbindelse, kan du derefter åbne noderne arbejder ved hjælp af SSH fra en headnode.

## <a name="file-locations"></a>Filplaceringer

Hadoop-relaterede filer kan findes på de klyngenoder på `/usr/hdp`. Denne mappe indeholder følgende undermapper:

* __2.2.4.9-1__: denne mappe har navnet for versionen af Hortonworks Data Platform bruges af HDInsight, så nummeret på din klynge kan være anderledes end den, der er angivet her.
* __aktuelle__: denne mappe indeholder links til mapperne under mappen __2.2.4.9-1__ og skyldes, at du ikke behøver at skrive et versionsnummer (, der kan ændres,), hver gang du vil have adgang til en fil.

Eksempeldataene og glas filer kan findes på Hadoop-distribueret fil System (HDFS) eller Azure Blob-lager på ' / eksempel ' eller ' wasbs: / / / eksempel '.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, Azure Blob-lager og lagerplads bedste fremgangsmåder

I de fleste Hadoop salgsdistributioner understøttes HDFS af lokale lager på computere i klyngen. Det er effektivt, kan det være koste dyrt en skybaseret løsning sted, hvor du betaler time- eller minut for Beregn ressourcer.

HDInsight bruger Azure Blob-lager som standard butik, som indeholder følgende fordele:

* Billige langsigtede lagerplads

* Hjælp til handicappede fra eksterne tjenester som websteder, fil upload/download værktøjer, forskellige sprog SDK'er og webbrowsere

Eftersom den er standard store HDInsight, behøver du normalt gøre noget for at bruge den. Følgende kommando kan for eksempel-filer i mappen **/example/data** , som er gemt på Azure Blob-lager:

    hdfs dfs -ls /example/data

Visse kommandoer kan kræve, at du kan angive, at du bruger Blob-lager. Du kan angive i så fald præfiks kommandoen med **wasb: / /**, eller **wasbs: / /**.

HDInsight kan du også skal tilknyttes en klynge flere Blob storage konti. For at få adgang til data på en ikke-standard Blob storage-konto, kan du bruge formatet **wasbs: / /&lt;container-name>@&lt;kontonavn >.blob.core.windows.net/**. For eksempel viser følgende indholdet af mappen **/example/data** for den angivne beholder og Blob storage konto:

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Hvilke Blob-lager bruger klyngen?

Under oprettelse af klynge, du har valgt at enten bruge en eksisterende konto Azure-lager og beholder eller oprette en ny. Derefter skal du sandsynligvis har glemt om den. Du kan finde lagerplads standardkonto og beholder ved hjælp af Ambari REST-API.

1. Brug følgende kommando til at hente HDFS konfigurationsoplysninger ved hjælp af krøllet og filtrere den ved hjælp af [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Dette vil returnere den første konfiguration, der er anvendt på serveren (`service_config_version=1`,), der indeholder disse oplysninger. Hvis du henter en værdi, der er blevet ændret efter oprettelse af klynge, skal du måske at vise konfiguration versionerne og hente den nyeste liste.

    Dette vil returnere en værdi stil med følgende, hvor __BEHOLDEREN__ er objektbeholderen standard og __KONTONAVN__ kontonavnet Azure-lager:

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Få ressourcegruppen for lagerplads konto ved at bruge [Azure CLI](../xplat-cli-install.md). Erstat __KONTONAVN__ i den følgende kommando med lagerplads kontonavnet hentet fra Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Dette vil returnere ressource gruppenavn til kontoen.
    
    > [AZURE.NOTE] Hvis der returneres fra denne kommando, du muligvis ændre Azure CLI til Azure ressourcestyring tilstand og køre kommandoen igen. Hvis du vil skifte til Azure ressourcestyring tilstand, skal du bruge følgende kommando.
    >
    > `azure config mode arm`
    
2. Få tasten for kontoen lagerplads. Erstat __GRUPPENAVN__ med ressourcegruppe fra det forrige trin. Erstat __KONTONAVN__ med lagerplads kontonavnet:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Dette vil returnere den primære nøgle for kontoen.

Du kan også finde lagerplads oplysningerne ved hjælp af portalen Azure:

1. Vælg din HDInsight klynge på [Azure-portalen](https://portal.azure.com/).

2. Vælg __alle indstillinger__i afsnittet med __vigtige oplysninger__ .

3. Under __Indstillinger__, vælge __Azure lagerplads taster__.

4. __Azure lagerplads taster__, Vælg en af lagerplads konti, der vises. Derved vises oplysninger om kontoen lagerplads.

5. Vælg ikonet nøgle. Derved vises taster til denne konto lagerplads.

### <a name="how-do-i-access-blob-storage"></a>Hvordan får jeg adgang til Blob-lager?

Der er andet end en række forskellige måder at få adgang til BLOB via kommandoen Hadoop fra klyngen:

* [Azure CLI til Mac, Linux og Windows](../xplat-cli-install.md): kommandolinjen kommandoer til at arbejde med Azure. Efter installation, skal du bruge den `azure storage` kommando for hjælp til brug lagring, eller `azure blob` for blob-specifikke kommandoer.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): en python script til at arbejde med BLOB i Azure-lager.

* En række forskellige SDK'er:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Fonetisk](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [Lagerplads REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Skalering din klynge

Klynge skalering funktion kan du ændre antallet data noder bruges af en klynge, der kører på Azure HDInsight uden at slette og gendanne klyngen.

Du kan udføre skalering handlinger under andre job eller processer kører på en klynge.

De forskellige klynge typer påvirkes af skalering på følgende måde:

* __Hadoop__: Når skalering ned antallet af knuder i en klynge, er nogle af tjenesterne i klyngen genstartet. Dette kan medføre job løbende eller afventer mislykkes ved afslutningen af handlingen skalering. Du kan sende job igen, når handlingen er fuldført.

* __HBase__: regionale servere automatisk afstemmes inden for et par minutter, efter fuldførelse af handlingen skalering. Du kan manuelt saldo regionale servere, kan du gøre følgende:

    1. Oprette forbindelse til den HDInsight klynge, ved hjælp af SSH. Du kan finde flere oplysninger om brug af SSH med HDInsight du se en af følgende dokumenter:

        * [Bruge SSH med HDInsight fra Linux, Unix og Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Bruge SSH med HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Brug følgende fremgangsmåde til at starte HBase shell:

            hbase shell

    2. Når HBase shell har indlæst, skal du bruge følgende for at manuelt saldo de regionale servere:

            balancer

* __Storm__: Du skal genoprette en hvilken som helst, der kører Storm topologier, når der er blevet udført en skalering handling. Dette giver mulighed for topologi at justere parallelitet indstillinger baseret på den nye antallet af knuder på klyngen. Benyt en af følgende indstillinger for at genoprette, der kører topologier:

    * __SSH__: oprette forbindelse til serveren og bruge følgende kommando for at genoprette en topologi:

            storm rebalance TOPOLOGYNAME

        Du kan også angive parametre for at tilsidesætte tip til parallelitet oprindeligt blev leveret af topologien. For eksempel `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` vil omkonfigurere topologien 5 arbejdsprocesser, 3 bobestyreren for komponenten blå tud og 10 bobestyreren for komponenten gul bolt.

    * __Storm brugergrænseflade__: følge nedenstående trin for at genoprette en topologi ved hjælp af Storm Brugergrænsefladen.

        1. Åbn __https://CLUSTERNAME.azurehdinsight.net/stormui__ i din webbrowser, hvor CLUSTERNAME er navnet på din Storm klynge. Hvis du bliver bedt om det, kan du angive HDInsight klynge administrator (admin) brugernavn og adgangskode, du har angivet, når du opretter klyngen.

        3. Vælg den topologi, du ønsker at genoprette og derefter vælge knappen __genoprette__ . Angiv forsinkelsen, før udførelse af handlingen rebalance.

Specifikke oplysninger om skalering din HDInsight klynge, i:

* [Administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Administrere Hadoop klynger i HDinsight ved hjælp af Azure PowerShell](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hvordan installerer jeg farvetone (eller en anden Hadoop-komponent)?

HDInsight er en administreret tjeneste, hvilket betyder, at noderne i en klynge kan ødelagt og reprovisioned ved Azure, hvis der registreres et problem. På grund af dette, er det ikke anbefales manuelt installere ting direkte på klyngenoderne. I stedet bruge [HDInsight scripthandlinger](hdinsight-hadoop-customize-cluster.md) , når du har brug at installere følgende:

* En tjeneste eller et websted som knallertmotor eller farvetone.
* En komponent, kræver ændringer i konfigurationen på flere noder i klyngen. For eksempel en påkrævet miljøvariablen, oprettelse af en mappe til logføring eller oprettelse af en konfigurationsfil.

Scripthandlinger er fest scripts, der har kørt under klynge klargøringen og kan bruges til at installere og konfigurere yderligere komponenter på klyngen. Eksempel-scripts, der leveres til installation af følgende komponenter:

* [Farvetone](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Du kan finde oplysninger om udvikling af dit eget scripthandlinger [scripthandling udvikling med HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Glas filer

Der findes nogle Hadoop-teknologier i selvstændig glas filer, der er indeholder funktioner, der bruges som en del af et MapReduce job eller fra i gris eller Hive. Mens disse kan installeres ved hjælp af scripthandlinger, kan de ofte kræver ikke en hvilken som helst konfiguration og kun overføres til klyngen efter klargøring af og bruges direkte. Hvis du vil makle sikker komponenten survives reimaging af klyngen, kan du gemme filen glas i WASB.

Eksempelvis hvis du vil bruge den nyeste version af [DataFu](http://datafu.incubator.apache.org/), kan du hente en glas, der indeholder projektet og overføre den til HDInsight klyngen. Følg derefter DataFu dokumentationen til, hvordan du bruger funktionen gris eller Hive.

> [AZURE.IMPORTANT] Visse komponenter, der er enkeltstående glas filer er forsynet med HDInsight, men er ikke i stien. Hvis du leder efter en bestemt komponent, kan du bruge følge for at søge efter den på din klynge:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Dette vil returnere stien til en tilsvarende glas-filer.

Hvis klyngen allerede indeholder en version af en komponent som en enkeltstående glas fil, men du vil bruge en anden version, kan du overføre en ny version af komponenten til klyngen og prøve at bruge det i dine sager.

> [AZURE.WARNING] Komponenter i HDInsight klynge understøttes fuldt ud, og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.
>
> Brugerdefinerede komponenter modtager kommercielt begrundet support for at hjælpe dig med at foretage yderligere fejlfinding af problemet. Dette kan medføre løse problemet eller beder dig om at deltage tilgængelige kanaler for de Åbn kilde-teknologier, hvor deep ekspertise i forbindelse med teknologien, der er fundet. For eksempel, der er mange communitywebsteder, der kan bruges, ønsker: [MSDN-forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Også Apache projekter har projektwebsteder på [http://apache.org](http://apache.org), for eksempel: [Hadoop](http://hadoop.apache.org/), [gnister](http://spark.apache.org/).

## <a name="next-steps"></a>Næste trin

* [Overføre fra Windows-baseret HDInsight til Linux-baserede](hdinsight-migrate-from-windows-to-linux.md)
* [Bruge Hive med HDInsight](hdinsight-use-hive.md)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Brug MapReduce sager med HDInsight](hdinsight-use-mapreduce.md)
