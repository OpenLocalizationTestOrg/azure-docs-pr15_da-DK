<properties
    pageTitle="HBase Selvstudium: Introduktion til Linux-baserede HBase klynger i Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>HBase Selvstudium: Introduktion til brug af Apache HBase med Linux-baserede Hadoop i HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Lær at oprette en HBase klynge i HDInsight, oprette HBase tabeller og forespørgsler tabeller ved hjælp af Hive. Generelle HBase oplysninger, under [Oversigt over HDInsight HBase][hdinsight-hbase-overview].

Oplysningerne i dette dokument er specifikke for Linux-baserede HDInsight klynger. Brug tabulatorvælgeren øverst på siden til at skifte oplysninger om Windows-baserede klynger.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet HBase, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Oprette HBase klynge

I følgende procedure bruges en Azure ressourcestyring skabelon til at oprette en version 3.4 Linux-baserede HBase klynge og afhængige standard Azure-lager-konto. For at forstå de parametre, bruges i denne procedure og andre metoder til oprettelse af klynge, skal du se [oprette Linux-baserede Hadoop klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klik på følgende billede for at åbne skabelonen i portalen Azure. Skabelonen er placeret i en offentlig blob objektbeholder. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Angiv følgende oplysninger fra bladet **brugerdefineret installation** :

    - **Abonnement**: Vælg abonnementet Azure, der skal bruges til at oprette klyngen.
    - **Ressourcegruppe**: oprette en ny gruppe i Azure ressourcestyring eller bruge en eksisterende.
    - **Placering**: angive placeringen af ressourcegruppen. 
    - **ClusterName**: Angiv et navn til den HBase klynge, du vil oprette.
    - **Klynge login-navn og din adgangskode**: login standardnavnet er **administrator**.
    - **SSH brugernavn og din adgangskode**: standard brugernavn er **sshuser**.  Du kan omdøbe den.
     
    Andre parametre er valgfrit.  
    
    Hver klynge har en konto afhængighed til Azure Blob-lager. Når du sletter en klynge, bevarer dataene i kontoen lagerplads. Klynge lagerplads konto standardnavnet er klyngenavn med "store" føjet. Det er hårdt i sektionen skabelon variabler.
        
3. Vælg **jeg accepterer du vilkår og betingelser, der er anført ovenfor**, og klik derefter på **Køb**. Det tager om 20 minutter om at oprette en klynge.


>[AZURE.NOTE] Når en HBase klynge slettes, kan du oprette en anden HBase klynge ved hjælp af samme standard blob beholder. Ny klynge vil vælge de HBase tabeller, du oprettede i den oprindelige klynge. For at undgå uoverensstemmelser, anbefaler vi, at du deaktiverer tabellerne HBase, før du sletter klyngen.

## <a name="create-tables-and-insert-data"></a>Oprette tabeller og indsætte data

Du kan bruge SSH til at oprette forbindelse til HBase klynger og derefter bruge HBase Shell til at oprette HBase tabeller, indsætte data og forespørgsel efter data. Se oplysninger om brug af SSH [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix, eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md) og [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

For de fleste vises data i den tabelformat:

![HDInsight HBase tabeldata][img-hbase-sample-data-tabular]

I HBase, som er en implementering af BigTable, de samme data, der ser sådan ud:

![HDInsight HBase bigtable data][img-hbase-sample-data-bigtable]

Det vil gøre mere mening, når du er færdig med den næste procedure.  


**Bruge HBase shell**

1. Fra SSH, skal du køre følgende kommando:

        hbase shell

4. Oprette en HBase med to koloner familier:

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

    Du kan finde flere oplysninger om HBase tabel skemaet se [Introduktion til HBase skema Design][hbase-schema]. For at se flere HBase kommandoer, se [Apache HBase oversigtsvejledning][hbase-quick-start].

6. Afslut shell

        exit



**Sådan masseredigeres Indlæs data i tabellen HBase**

HBase indeholder flere forskellige metoder for at indlæse data i tabeller.  Se [flere indlæse](http://hbase.apache.org/book.html#arch.bulk.load)kan finde flere oplysninger.


Et eksempel på datafil er blevet overført til en objektbeholder til offentlige blob *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Indholdet af datafilen er:

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

> [AZURE.NOTE] Denne fremgangsmåde bruger tabellen kontakter HBase, du har oprettet i den seneste procedure.

1. Kør følgende kommando for at transformere datafilen til StoreFiles og store på en relativ sti, der er angivet af Dimporttsv.bulk.output fra SSH,:.  Hvis du er i HBase Shell, skal du bruge kommandoen Afslut for at afslutte.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Kør følgende kommando for at overføre data fra /example/data/storeDataFileOutput til tabellen HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Du kan åbne HBase shell og bruge kommandoen scanning til at få vist indholdet tabel.



## <a name="use-hive-to-query-hbase"></a>Bruge Hive til forespørgsel HBase

Du kan forespørge data i HBase tabeller ved hjælp af Hive. Dette afsnit opretter en Hive-tabel, der knytter til tabellen HBase og bruger det til at forespørge dataene i tabellen HBase.

1. Åbn **trykfarver**, og oprette forbindelse til klyngen.  Se vejledningen i den foregående fremgangsmåde.
2. Åbn Hive shell.

       hive
3. Kør følgende HiveQL script for at oprette en Hive-tabellen, der er tilknyttet tabellen HBase. Sørg for, at du har oprettet eksempeltabellen, der refereres til tidligere i dette selvstudium ved hjælp af HBase shell, før du kører denne sætning.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Kør følgende HiveQL script for at søge i dataene i tabellen HBase:

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Brug HBase REST API'er ved hjælp af krøllet

> [AZURE.NOTE] Når du bruger krøllet eller en hvilken som helst anden RESTEN kommunikation med WebHCat, skal du godkende anmodningerne ved at angive det brugernavn og adgangskode til HDInsight klyngeadministratoren. Du skal også bruge klyngenavnet som en del af den URI Uniform Resource Identifier () bruges til at sende anmodninger til serveren.
>
> Erstatte **brugernavn** med brugeren til at godkende til klyngen for kommandoerne i dette afsnit, og Erstat **adgangskode** med adgangskoden for kontoen. Erstat **CLUSTERNAME** med navnet på din klynge.
>
> REST-API er sikret via [basisgodkendelse](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bør altid lave anmodninger ved hjælp af Secure HTTP (HTTPS) for at sikre, at dine legitimationsoplysninger sikkert er sendt til serveren.

1. Brug følgende kommando for at bekræfte, at du kan oprette forbindelse til din HDInsight klynge fra en kommandolinje:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Du skal modtage et svar, der ligner følgende:

        {"status":"ok","version":"v1"}

    De parametre, bruges i denne kommando er som følger:

    * **-u** - brugernavn og adgangskode, der bruges til at godkende anmodningen.
    * **-G** - angiver, at dette er en GET-anmodning.

2. Brug følgende kommando til at få vist de eksisterende HBase tabeller:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Brug følgende kommando til at oprette en ny HBase tabel med to kolonne familier:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    Skemaet er angivet i formatet JSon.

4. Brug følgende kommando til at indsætte nogle data:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Du skal base64 kodes de værdier, der er angivet i parameteren -d.  I exmaple:

    - MTAwMA ==: 1000
    - UGVyc29uYWw6TmFtZQ ==: Personal: navn
    - Sm9obiBEb2xl: John Dole

    [Falsk-række-nøgle](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) giver dig mulighed at indsætte flere (batchopdelte) værdi.

5. Brug følgende kommando for at få en række:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Du kan finde flere oplysninger om HBase resten, [Apache HBase oversigtsvejledning](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Kontrollere status for klynge

HBase i HDInsight leveres med en Web-brugergrænseflade til overvågning klynger. Bruger Webbrugergrænsefladen, kan du anmode om statistik eller oplysninger om områder.

SSH kan også bruges til at tunnel lokale anmodninger, som web anmodninger om, at HDInsight klyngen. Anmodningen sendes derefter til den ønskede ressource, som om den havde stammer på den HDInsight klynge hovedsæde node. Du kan finde flere oplysninger, se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Du opretter en SSH tunnelføring session**

1. Åbn **trykfarver**.  
2. Hvis du har angivet en SSH nøgle, da du oprettede din brugerkonto under oprettelsesprocessen, skal du udføre følgende trin for at markere den private nøgle skal anvendes ved godkendelse til klyngen:

    Udvid **forbindelse**i **kategori**, udvide **SSH**, og vælg **Auth**. Til sidst, klik på **Gennemse** , og vælg den fil, .ppk, der indeholder din private nøgle.

3. Klik på **Session**i **kategori**.
4. Angiv følgende værdier fra de grundlæggende indstillinger for skærmen trykfarver session:

    - **Værtsnavn**: SSH adressen på dit HDInsight server i Host name (eller IP-adresse) felt. Adressen, SSH er klyngenavnet på din, derefter **-ssh.azurehdinsight.net**. For eksempel, *mycluster ssh.azurehdinsight.net*.
    - **Port**: 22. Den ssh port på den primære headnode er 22.  
5. Udvid **forbindelse**i sektionen **kategori** til venstre i dialogboksen, udvide **SSH**, og klik derefter på **tunneler**.
6. Angiv følgende oplysninger på de indstillinger, der styrer SSH port viderestilling af formularen:

    - **Kildeport** - port på den klient, som du vil videresende. For eksempel 9876.
    - **Dynamisk** – aktiverer dynamisk SOCKS-proxy routing.
7. Klik på **Tilføj** for at tilføje indstillingerne.
8. Klik på **Åbn** nederst i dialogboksen for at åbne en SSH forbindelse.
9. Når du bliver bedt om det, log på serveren ved hjælp af en SSH-konto. Dette vil oprette en SSH session og Aktivér tunnelen.

**Til at finde det fulde Domænenavn på Dyrepasserne ved hjælp af Ambari**

1. Gå til https://<ClusterName>.azurehdinsight.net/.
2. Angiv din klynge brugerlegitimationsoplysninger konto to gange.
3. Klik på **zookeeper**fra menuen til venstre.
4. Klik på en af de tre **ZooKeeper Server** hyperlinks i oversigt.
5. Kopiere **Hostname**. For eksempel zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Til at konfigurere en klientprogrammet (Firefox) og kontrollere status for klynge**

1. Åbn Firefox.
2. Klik på knappen **Åbn Menu** .
3. Klik på **Indstillinger**.
4. Klik på **Avanceret**, klik på **netværk**, og klik derefter på **Indstillinger**.
5. Vælg **Manuel proxykonfiguration**.
6. Angiv følgende værdier:

    - **Socks Host**: localhost
    - **Port**: Brug den samme port, du har konfigureret i den trykfarver SSH tunnelføring.  For eksempel 9876.
    - **SOCKS v5**: (valgt)
    - **Ekstern DNS**: (valgt)
7. Klik på **OK** for at gemme ændringerne.
8. Gå til http://&lt;feltet fulde for en ZooKeeper >: 60010/master-status.

I en klynge med høj tilgængelighed, kan du finde et link til den aktuelle aktive HBase master node, der er vært Webbrugergrænsefladen.

##<a name="delete-the-cluster"></a>Slette klyngen

For at undgå uoverensstemmelser, anbefaler vi, at du deaktiverer tabellerne HBase, før du sletter klyngen.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Næste trin

Du har lært hvordan du opretter en HBase klynge og hvordan du kan oprette tabeller og få vist dataene i disse tabeller fra HBase shell i selvstudiet HBase for HDInsight. Du har også lært hvordan du bruger en Hive forespørgsel på data i HBase tabeller, og hvordan du bruger de HBase C# REST API'er til at oprette en HBase tabel og hente data fra tabellen.

Hvis du vil vide mere, skal du se:

- [Oversigt over HDInsight HBase][hdinsight-hbase-overview]: HBase er en Apache, Åbn-kilde, NoSQL-database, der er bygget på Hadoop, der indeholder RAM og stærke konsistens til store mængder ustrukturerede og semistructured data.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
