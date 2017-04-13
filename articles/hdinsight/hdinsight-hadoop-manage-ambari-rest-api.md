<properties
   pageTitle="Overvåge og administrere HDInsight klynger ved hjælp af Apache Ambari REST-API | Microsoft Azure"
   description="Lær, hvordan du bruger Ambari til at overvåge og administrere Linux-baserede HDInsight klynger. I dette dokument, skal du lære, hvordan du bruger API'EN Ambari RESTEN inkluderet med HDInsight klynger."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Administrere HDInsight klynger ved hjælp af Ambari REST-API

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari forenkler for administration og overvågning af en Hadoop-klynge ved at indsende en nem at bruge web Brugergrænsefladen og REST-API. Ambari findes på Linux-baserede HDInsight klynger og bruges til at overvåge klyngen og foretage ændringer i konfigurationen. I dette dokument, kan du lære de grundlæggende færdigheder i at arbejde med Ambari REST-API ved at udføre almindelige opgaver ved hjælp af krøllet.

##<a name="prerequisites"></a>Forudsætninger

* [cURL](http://curl.haxx.se/): krøllet er et værktøj i tværs af platforme, der kan bruges til at arbejde med REST API'er fra kommandolinjen. I dette dokument, er den bruges til at kommunikere med Ambari REST-API.
* [jq](https://stedolan.github.io/jq/): jq er et på tværs af platforme kommandolinjen værktøj til at arbejde med dokumenter, JSON. I dette dokument, er den bruges til at analysere de JSON-dokumenter, der returneres fra Ambari REST-API.
* [Azure CLI](../xplat-cli-install.md): et på tværs af platforme kommandolinjen værktøj til at arbejde med Azure-tjenester.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>Hvad er Ambari?

[Apache Ambari](http://ambari.apache.org) gør Hadoop management enklere ved at indsende en nemt bruge web brugergrænseflade, der kan bruges til at klargøre, administrere og overvåge Hadoop klynger. Udviklere kan integrere disse funktioner i deres programmer ved hjælp af [Ambari REST API'er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari er angivet som standard med Linux-baserede HDInsight klynger.

##<a name="rest-api"></a>REST-API

Den grundlæggende URI for Ambari REST-API på HDInsight er https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, hvor __CLUSTERNAME__ er navnet på din klynge.

> [AZURE.IMPORTANT] Mens klyngenavn i den fulde navn (fulde Domænenavn) del af URI (CLUSTERNAME.azurehdinsight.net) der skelnes ikke mellem, er andre forekomster i URI store og små bogstaver. Eksempelvis hvis din klynge hedder MyCluster, er følgende gyldige URI'er:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> De følgende URI'er returnere en fejl, fordi den anden forekomst af navnet ikke er mellem store og små.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Oprette forbindelse til Ambari på HDInsight kræver HTTPS. Når godkendelse af forbindelsen, skal du bruge det administrator kontonavn (standard er __administrator__), og den adgangskode, du angav, da klyngen blev oprettet.

I følgende eksempel bruges krøllet til at foretage en GET-anmodning mod REST-API. Erstat __adgangskode__ med administratoradgangskode for-klyngen. Erstat __CLUSTERNAME__ med navnet på klyngen:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Svaret er et dokument, JSON, der begynder med oplysninger, der ligner følgende:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Da dette er JSON, er det nemmere at bruge en JSON-parseren til at arbejde med dataene. For eksempel i følgende eksempel bruges jq til kun for at vise den `health_report` element.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Eksempel: Få det fulde Domænenavn på klyngenoder

Når du arbejder med HDInsight, kan du vil kende det fuldstændige domænenavn (fulde Domænenavn) for en klyngenode. Nemt kan du hente det fulde Domænenavn for de forskellige noder i den klynge, ved hjælp af følgende:

* __I afsnit noder__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Arbejder noder__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper noder__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Bemærk, at hver af disse eksempler følger det samme mønster:

1. Forespørgsel en komponent, som vi vide kører på disse noder.
2. Hente den `host_name` elementer, der indeholder det fulde Domænenavn for disse noder.

Den `host_components` element af returnerede dokumentet indeholder flere elementer. Ved hjælp af `.host_components[]`, og derefter angive en sti i elementet som gennemløb hvert element og trække værdien fra den angivne sti. Hvis du kun vil én værdi, som den første post i fulde Domænenavn, kan du returnere elementer som en samling og derefter vælge en bestemt post:

    jq '[.host_components[].HostRoles.host_name][0]'

Dette returnerer det første fulde fra samlingen.

##<a name="example-get-the-default-storage-account-and-container"></a>Eksempel: Får den lagerplads standardkonto og objektbeholder

Når du opretter en HDInsight klynge, skal du bruge en Azure-lager konto og en blob beholder som standard opbevaring for-klyngen. Du kan bruge Ambari til at hente disse oplysninger, når klyngen er blevet oprettet. Eksempelvis hvis du automatisk vil skrive data direkte til objektbeholderen.

Følgende vil hente WASB URI klynger standard-lager:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Returnerer den første konfiguration, der er anvendt på serveren (`service_config_version=1`,) som indeholder disse oplysninger. Hvis du henter en værdi, der er blevet ændret efter oprettelse af klynge, skal du måske at vise konfiguration versionerne og hente den nyeste liste.

Dette returnerer en værdi ligner i følgende eksempel, hvor __BEHOLDEREN__ er objektbeholderen standard og __KONTONAVN__ kontonavnet Azure-lager:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Du kan derefter bruge disse oplysninger med [Azure CLI](../xplat-cli-install.md) at overføre eller hente data fra objektbeholderen.

1. Få ressourcegruppen for kontoen lagerplads. Erstat __KONTONAVN__ med lagerplads kontonavnet hentet fra Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Dette returnerer ressource gruppenavn til kontoen.
    
    > [AZURE.NOTE] Hvis der returneres fra denne kommando, du muligvis ændre Azure CLI til Azure ressourcestyring tilstand og køre kommandoen igen. Hvis du vil skifte til Azure ressourcestyring tilstand, skal du bruge følgende kommando:
    >
    > `azure config mode arm`
    
2. Få nøgle til kontoen lagerplads. Erstat __GRUPPENAVN__ med ressourcegruppe fra det forrige trin. Erstat __KONTONAVN__ med lagerplads kontonavnet:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    I dette eksempel returnerer den primære nøgle for kontoen.
    
3. Bruge kommandoen Overfør til at gemme en fil i beholderen:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Erstat __KONTONAVN__ med lagerplads kontonavnet. Erstat __ACCOUNTKEY__ med tasten hentet tidligere. __FILEPATH__ er stien til den fil, du vil overføre, mens __BLOBPATH__ er stien i objektbeholderen.

    Eksempelvis hvis du vil den fil, der vises i HDInsight wasbs://example/data/filename.txt, derefter __BLOBPATH__ ville være `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Eksempel: Opdater Ambari konfiguration

1. Få den aktuelle konfiguration, som lagrer Ambari som den "ønskede konfiguration":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    I dette eksempel returnerer et JSON-dokument, der indeholder den aktuelle konfiguration (identificeret af værdien _mærke_ ) for de komponenter, der er installeret på klyngen. I følgende eksempel er et uddrag data, der returneres fra en knallertmotor klynge type.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    På denne liste skal du kopiere navnet på komponenten (for eksempel __knallertmotor\_thrift\_sparkconf__ og __tag__ værdi.
    
2. Hente konfigurationen for komponent og mærke ved hjælp af følgende kommando. Erstatte __knallertmotor-thrift-sparkconf__ og __indledende__ med komponent og mærke, du vil hente konfigurationen for.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Krøllet henter JSON dokumentet, og derefter jq bruges til at foretage ændringer af dataene for at oprette en skabelon. Skabelonen bruges derefter til at tilføje/redigere konfigurationsværdier. Specifikt gør følgende:
    
    * Opretter en entydig værdi, der indeholder strengen "version" og den dato, der er gemt i __newtag__.
    * Opretter et rod dokument til den nye ønskede konfiguration.
    * Bliver indholdet af den `.items[]` array og tilføjer den under __desired_config__ elementet.
    * Sletter den __linkreference__, __version__og __Config__ elementer efter disse elementer ikke er behov for at sende en ny konfiguration.
    * Tilføjer et nyt __mærke__ element og sætter dens værdi til __version ###__. Den numeriske del er baseret på den aktuelle dato. Hver konfiguration skal have en entydig kode.
    
    Til sidst skal gemmes dataene til __newconfig.json__ dokumentet. Dokumentstrukturen skal vises som i følgende eksempel:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Åbn __newconfig.json__ dokument og ændre/tilføje værdierne i objektet __Egenskaber__ . I følgende eksempel ændres værdien af __"spark.yarn.am.memory"__ fra __"1 g"__ til __"3 g"__ og, og tilføjer et nyt element til __"spark.kryoserializer.buffer.max"__ med en værdi på __"256 m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Gem filen, når du er færdig med at foretage ændringer.

4. Brug følgende kommando til at sende den opdaterede konfiguration til Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Denne kommando pipes indholdet af filen __newconfig.json__ til krøllet anmodningen, som sender den til klynge som den nye ønskede konfiguration. Krøllet anmodningen returnerer et JSON-dokument. Elementet __versionTag__ i dette dokument skal svare til versionen, du har indsendt, og objektet __konfigurationer__ indeholder de ønskede ændringer i konfigurationen.

###<a name="example-restart-a-service-component"></a>Eksempel: Genstarte en tjeneste-komponent

På dette tidspunkt, hvis du ser på webdelen Ambari brugergrænseflade, indikerer tjenesten knallertmotor, at der skal genstartes, før den nye konfiguration kan træde i kraft. Brug følgende trin til genstart tjenesten.

1. Du kan bruge følgende til at aktivere Vedligeholdelsestilstand for tjenesten knallertmotor:

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Denne kommando sender et JSON-dokument til serveren (findes i den `echo` -sætning,) som slår vedligeholdelsestilstand.
    Du kan kontrollere, at tjenesten er nu i vedligeholdelsestilstand ved hjælp af følgende anmodningen:
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Dette vil returnere en værdi på `"ON"`.

3. Nu skal bruge følgende for at deaktivere tjenesten:

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Denne kommando returnerer et svar, der ligner følgende.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    Den `href` værdi, der returneres af denne URI bruger Klyngenoden interne IP-adresse. For at bruge den fra uden for klyngen skal du erstatte '10.0.0.18:8080' del med det fuldstændige Domænenavn for klyngen. For eksempel henter følgende kommando statussen for din anmodning.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Hvis dette returnerer en værdi af `"COMPLETED"` og derefter anmodningen er færdig.

4. Når den forrige anmodning er fuldført, skal du bruge følgende for at starte tjenesten.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Når tjenesten genstarter, det er de nye konfigurationsindstillinger.

5. Til sidst skal bruge følgende for at deaktivere vedligeholdelsestilstand.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Næste trin

Du kan finde en komplet reference til REST-API [Ambari API Reference V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Nogle Ambari funktionalitet er deaktiveret, som administreres af tjenesten HDInsight skyen; for eksempel tilføje eller fjerne værter fra klyngen eller tilføjer nye tjenester.
