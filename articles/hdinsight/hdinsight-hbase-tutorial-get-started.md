<properties
    pageTitle="HBase Selvstudium: Introduktion til HBase i Hadoop | Microsoft Azure"
    description="Følg selvstudiet HBase at komme i gang ved hjælp af Apache HBase med Hadoop i HDInsight. Oprette tabeller fra HBase shell og få vist dem ved hjælp af Hive."
    keywords="Apache hbase, hbase, hbase shell, hbase selvstudium"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>HBase Selvstudium: Introduktion til brug af Apache HBase med Windows-baseret Hadoop i HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Lær at oprette HBase klynger i HDInsight, oprette tabeller på HBase og forespørgselstabeller ved hjælp af Apache Hive. Generelle HBase oplysninger, under [Oversigt over HDInsight HBase][hdinsight-hbase-overview].

Oplysningerne i dette dokument er specifikke for Windows-baseret HDInsight klynger. Brug tabulatorvælgeren øverst på siden til at skifte oplysninger om Windows-baserede klynger.

> [AZURE.NOTE] HBase (version 0.98.0) på Windows-baseret HDInsight er kun tilgængelig til brug med HDInsight 3.1 klynger (baseret på Apache Hadoop og GARN 2.4.0). Finde oplysninger om dokumentversion [Nyheder i de Hadoop klynge versioner, der leveres af HDInsight?][hdinsight-versions]

## <a name="before-you-begin"></a>Inden du går i gang

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Inden du starter selvstudiet HBase, skal du have følgende:

- **Et Microsoft Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **En arbejdsstation** med Visual Studio 2013 eller nyere: finde flere oplysninger under [Installere Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Oprette HBase klynge

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Oprette en HBase klynge ved hjælp af portalen Azure**

1. Log på [Azure portal][azure-management-portal].
2. Klik på **Ny** eller **+** i øverste venstre hjørne, og klik derefter på **Data + Analytics**, **HDInsight**.
3. Angiv følgende værdier:

    - **Klyngenavn** - Angiv et navn til at identificere denne klynge.
    - **Klynge Type** - Vælg **HBase**.
    - **Klynge operativsystem** – Vælg **Windows**.  For at oprette Linux-baserede HBase klynge, skal du se [HBase Selvstudium: Introduktion til brug af Apache HBase med Hadoop i HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Version** - Vælg en HBase version.
    - **Abonnement** - Vælg abonnementet Azure, der bruges til at oprette denne klynge.
    - **Ressourcegruppe** - Opret en ny Azure ressourcegruppe eller Vælg et eksisterende dokument. Få mere at vide under [Oversigt over Azure ressourcestyring](azure-resource-manager/resource-group-overview.md)
    - **Legitimationsoplysninger** – til Windows-baseret klynge, kan du oprette en klynge bruger (a.k.a HTTP bruger, HTTP web Servicebruger) og en Fjernskrivebord bruger. Klik på **Aktivér Fjernskrivebord** for at tilføje remote desktop brugerlegitimationsoplysningerne. Næste afsnit kræver RDP.
    - **Datakilde** - oprette en ny firmapost Azure-lager, eller Vælg en eksisterende Azure lagerplads konto skal bruges som standardfilsystemet for-klyngen. Standardplaceringen konto bestemmer placeringen af den klynge placering. Standardkontoen lager og klyngen skal samtidig Find i den samme datacenter.
    - **Node priser niveauer** - Vælg antallet af region servere for HBase klynge

        > [AZURE.WARNING] For høj tilgængelighed af HBase tjenester, skal du oprette en klynge, der indeholder mindst **tre** noder. Dette sikrer, at hvis en node går ned, HBase data områder er tilgængelige på andre noder.

        > Hvis du lærer HBase, altid vælge 1 for klyngestørrelse, og Slet klyngen efter hver brug at reducere omkostningerne.

    - **Valgfri konfiguration** – konfigurere Azure virtuelt netværk og konfigurere scripthandlinger, og tilføje yderligere lagerplads konti.

4. Klik på **Opret**.

>[AZURE.NOTE] Når en HBase klynge slettes, kan du oprette en anden HBase klynge ved hjælp af samme lagerplads standardkontoen og objektbeholderen standard blob. Ny klynge vil vælge de HBase tabeller, du oprettede i den oprindelige klynge. For at undgå uoverensstemmelser, anbefaler vi, at du deaktiverer tabellerne HBase, før du sletter klyngen.

## <a name="create-tables-and-insert-data"></a>Oprette tabeller og indsætte data

I øjeblikket, er der to måde at få adgang til HBase. Dette afsnit beskrives ved hjælp af HBase shell. Næste afsnit dækker ved hjælp af .NET SDK.

For de fleste vises data i den tabelformat:

![hdinsight hbase tabeldata][img-hbase-sample-data-tabular]

I HBase, som er en implementering af BigTable, de samme data, der ser sådan ud:

![hdinsight hbase bigtable data][img-hbase-sample-data-bigtable]

Det får giver mere mening, når du er færdig med den næste procedure.  

**Bruge HBase shell**

1. Bruge RDP til at oprette forbindelse til din HBase klynge i HDInsight. Se [administrere Hadoop klynger i portalen til Azure HDInsight]vejledning RDP[hdinsight-manage-portal].
2. Klik på genvejen **Hadoop kommandolinjen** findes på skrivebordet i din RDP-session.
3. Åbn HBase shell:

        cd %HBASE_HOME%\bin
        hbase shell

4. Oprette en HBase med to kolonne familier:

        create 'Contacts', 'Personal', 'Office'
        list
5. Indsætte nogle data:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Få en enkelt række

        get 'Contacts', '1000'

    Du får vist det samme resultat som ved hjælp af kommandoen scanning, fordi der er kun én række.

    Du kan finde flere oplysninger om Hbase tabel skemaet se [Introduktion til HBase skema Design][hbase-schema]. For at se flere HBase kommandoer, se [Apache HBase oversigtsvejledning][hbase-quick-start].


6. Afslut shell

        exit

**Sådan masseredigeres Indlæs data i tabellen HBase**

HBase indeholder flere forskellige metoder for at indlæse data i tabeller. Se [flere indlæse](http://hbase.apache.org/book.html#arch.bulk.load)kan finde flere oplysninger.


Et eksempel på datafil er blevet overført til en objektbeholder til offentlige blob wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Indholdet af datafilen er:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Du kan oprette en tekstfil og overføre filen til din egen lagerplads konto, hvis du vil have. Yderligere oplysninger finder du [overføre data til Hadoop-job i HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Denne fremgangsmåde bruger tabellen kontakter HBase, du oprettede i den seneste procedure.

1. Klik på genvejen **Hadoop kommandolinjen** findes på skrivebordet i din RDP-session.
2. Skift mappe:

        cd %HBASE_HOME%\bin

3. Kør følgende kommando for at transformere datafilen til StoreFiles og store på en relativ sti, der er angivet af Dimporttsv.bulk.output:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Kør følgende kommando for at overføre data fra /example/data/storeDataFileOutput til tabellen HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Du kan åbne HBase shell og bruge kommandoen scanning til at få vist indholdet tabel.



## <a name="use-hive-to-query-hbase-tables"></a>Bruge Hive til HBase forespørgselstabeller

Du kan forespørge data gemt i HBase ved hjælp af Hive. Dette afsnit opretter en Hive-tabel, der knytter til tabellen HBase og bruger det til at forespørge dataene i tabellen HBase.

**Åbne dashboardet klynge**

1. Gå til **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Angiv Hadoop-brugerkonto og adgangskode. Standardnavnet er **admin** og adgangskoden er, hvad du har angivet under oprettelsesprocessen. Der åbnes en ny browserfane.
6. Klik på **Hive Editor** øverst på siden. Redigeringsprogrammet Hive ser sådan ud:

    ![HDInsight klynge dashboard.][img-hdinsight-hbase-hive-editor]

**Køre Hive forespørgsler**

1. Angiv følgende HiveQL script i Hive Editor, og klik på **Send** for at oprette en Hive-tabellen, der er tilknyttet tabellen HBase. Sørg for, at du har oprettet eksempeltabellen, der refereres til tidligere i dette selvstudium ved hjælp af HBase shell, før du kører denne sætning.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Vent, indtil **statusopdateringer til **fuldført**** .

2. Angiv følgende HiveQL script i Hive Editor, og klik derefter på **Send**. Forespørgslen Hive forespørger dataene i tabellen HBase:

        SELECT count(*) FROM hbasecontacts;

4. Klik på linket **Vis detaljer** i vinduet **Job Session** for at hente resultaterne af forespørgslen Hive, når jobbet er afsluttet. Der vil være kun én job outputfilen, da du sætte én post i tabellen HBase.




**Gennemse outputfilen**

1. Klik på **Fil browseren**i konsollen forespørgsel.
2. Klik på kontoen Azure-lager, der bruges som standard-filsystemet for HBase klynge.
3. Klik på HBase klyngenavnet. Objektbeholderen standard Azure lagerplads konto bruger klyngenavn.
4. Klik på **bruger**, og klik derefter på **Administration**. (Dette er brugernavnet Hadoop).
6. Klik på jobbet navnet med tiden **Senest ændret** , der svarer til den tid, når forespørgslen Vælg Hive kørte.
4. Klik på **stdout**. Gem filen og åbne filen i Notesblok. Der vil være én outputfilen.

    ![HDInsight HBase Hive Editor fil Browser][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Brug biblioteket .NET HBase REST-API-klienten

Du skal hente biblioteket HBase REST-API-klienten til .NET fra GitHub og opbygge projektet, så du kan bruge HBase .NET SDK. Følgende procedure indeholder vejledning til denne opgave.

1. Oprette et nyt C# Visual Studio Windows Desktop Console-program.
2. Åbne konsollen NuGet Package Manager ved at klikke på **værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**.
3. Kør følgende kommando for NuGet i konsollen:

        Install-Package Microsoft.HBase.Client

5. Tilføj følgende **ved hjælp af** udtalelser øverst i filen:

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Erstat funktionen **Main** med følgende:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Angive de første tre variabler i funktionen **Main** .
8. Tryk på **F5** for at køre programmet.

## <a name="check-cluster-status"></a>Kontrollere status for klynge

HBase i HDInsight leveres med en Web-brugergrænseflade til overvågning klynger. Bruger Webbrugergrænsefladen, kan du anmode om statistik eller oplysninger om områder.

For at åbne den Web-brugergrænseflade, skal du RDP i klynge, og derefter klikke på genvejen HMaster oplysninger Webbrugergrænsefladen på dit skrivebord eller bruge følgende URL-adressen i en webbrowser:

    http://zookeeper[0-2]:60010/master-status

I en klynge med høj tilgængelighed finder du et link til den aktuelle aktive HBase master node, der er vært Webbrugergrænsefladen.

##<a name="delete-the-cluster"></a>Slette klyngen
For at undgå uoverensstemmelser, anbefaler vi, at du deaktiverer tabellerne HBase, før du sletter klyngen.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>Hvad skal der ske nu?
Du har lært hvordan du opretter en HBase klynge og hvordan du kan oprette tabeller og få vist dataene i disse tabeller fra HBase shell i selvstudiet HBase for HDInsight. Du har også lært hvordan bruger en Hive forespørgsel på data i HBase tabeller, og hvordan du bruger HBase C# REST API'er til at oprette en HBase tabel og hente data fra tabellen.

Du kan finde flere oplysninger i:

- [Oversigt over HDInsight HBase][hdinsight-hbase-overview].
HBase er en Apache, Åbn-kilde, NoSQL-database, der er bygget på Hadoop, der indeholder RAM og stærke konsistens til store mængder ustrukturerede og semistructured data.
- [Oprette HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet].
HBase klynger kan installeres på det samme virtuelle netværk som dine programmer med virtuelt netværksintegration, så programmer kan kommunikere med HBase direkte.
- [Konfigurere HBase gentagelse i HDInsight](hdinsight-hbase-geo-replication.md). Lær, hvordan du konfigurerer HBase gentagelse på tværs af to Azure datacentre.
- [Analysere Twitter synspunkt med HBase i HDInsight][hbase-twitter-sentiment].
Få mere at vide, hvordan du gør realtid [synspunkt analyse](http://en.wikipedia.org/wiki/Sentiment_analysis) af big data ved hjælp af HBase i en Hadoop klynge i HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
