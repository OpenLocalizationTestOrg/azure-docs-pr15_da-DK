<properties
    pageTitle="Importere data til Machine Learning Studio fra online datakilder | Microsoft Azure"
    description="Sådan importeres dataene kursus Azure Machine Learning Studio fra forskellige kilder online."
    keywords="importere data, dataformat, datatyper, datakilder, kursus data"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />


# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importere data til Azure Machine Learning Studio fra forskellige online datakilder med modulet importdata

I denne artikel beskrives understøttelse af importere online-data fra forskellige kilder og de oplysninger, der er behov for at flytte data fra disse kilder til Azure Machine Learning forsøg.

> [AZURE.NOTE] I denne artikel indeholder generelle oplysninger om at [Importere Data] [ import-data] modul. Kan finde mere detaljerede oplysninger om typerne data, du kan få adgang til formater, parametre og svar på ofte stillede spørgsmål, under modul referenceemnet for [Importdata] [ import-data] modul.

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introduktion

Du kan få adgang til data fra Azure Machine Learning Studio fra en af flere online datakilder mens din forsøg kører ved hjælp af [Importdata] [ import-data] modul:

- En URL-adresse med HTTP
- Hadoop ved hjælp af HiveQL
- Azure blob-lager
- Azure-tabel
- Azure SQL-database eller SQL Server på Azure VM
- Lokal SQL Server-database
- En datafeeddataprovider, OData i øjeblikket
 
Arbejdsprocessen for ledende forsøg i Azure Machine Learning Studio består af at trække og slippe komponenter over på lærredet. For at få adgang til online-datakilder, tilføje [Importdata] [ import-data] modulet til din forsøg, Markér den **datakilde**, og angiv derefter de parametre, der er behov for at få adgang til dataene. De online datakilder, der understøttes er specificeret i tabellen nedenfor. I denne tabel opsummerer også de filformater, der understøttes og parametre, der bruges til at få adgang til dataene.

Bemærk, fordi disse kurser data bruges, mens din forsøg kører, det er kun tilgængelig i pågældende forsøg. Sammenligning er data, der er gemt i et datasæt modul tilgængelige for et forsøg i arbejdsområdet.

> [AZURE.IMPORTANT] I øjeblikket, [Importere Data] [ import-data] og [Eksportér Data] [ export-data] moduler kan læse og skrive data fra Azure lagerplads, der er oprettet ved hjælp af implementeringsmodel klassisk. Det vil sige, er den nye kontotype til Azure Blob-lager, der indeholder en genvejstast lagerplads access niveau eller smarte lagerplads access niveau endnu ikke understøttet. 

> Generelt Azure lagerplads konti, du muligvis har oprettet, inden denne indstilling for tjenesten blev tilgængeligt bør ikke berøres. Hvis du vil oprette en ny konto, Vælg **Klassisk** for implementeringsmodel, eller brug ressourcestyring og for **type konto**, skal du vælge **generelle formål** i stedet for **Blob-lager**. 

> Du kan finde flere oplysninger, se [Azure Blob-lager: varmt og smarte lagerplads niveauer](../storage/storage-blob-storage-tiers.md).



## <a name="supported-online-data-sources"></a>Understøttede online datakilder
Azure Machine Learning **Importdata** modul understøtter følgende datakilder:

Datakilde | Beskrivelse | Parametre |
---|---|---|
URL-adresse via HTTP |Læser data i kommaseparerede værdier (CSV), tabulatorsepareret værdier (TSV), attribut-relation-filformat (ARFF) og understøttelse af vektor maskiner (SVM – lys) formater fra en hvilken som helst URL-adresse, der bruger HTTP | <b>URL-adresse</b>: Angiver det fulde navn på den fil, herunder webstedets URL-adresse og filnavnet, med en hvilken som helst filtypenavn. <br/><br/><b>Dataformat</b>: Angiver en af de understøttede data formater: CSV, TSV, ARFF eller SVM light. Hvis data, der har en kolonneoverskrift, bruges den til at tildele kolonnenavne.|
Hadoop/HDFS|Læser data fra fordelt lageret i Hadoop. Du angive de ønskede ved hjælp af HiveQL, en SQL-lignende forespørgselssprog data. HiveQL kan også bruges til at aggregere data og udføre datafiltrering, før du tilføjer dataene til Machine Learning Studio.|<b>Hive databaseforespørgsel</b>: Angiver den Hive forespørgsel, der bruges til at generere dataene.<br/><br/><b>HCatalog URI-serveren</b> : angivet navnet på din klynge ved hjælp af formatet * &lt;klyngenavnet på din&gt;. azurehdinsight.net.*<br/><br/><b>Navnet på Hadoop-brugerkonto</b>: Angiver Hadoop navnet på brugerkonto bruges til at klargøre klyngen.<br/><br/><b>Hadoop-adgangskode til brugerkonto</b> : Angiver de legitimationsoplysninger, der bruges, når klyngen. Se [oprette Hadoop klynger i HDInsight](../hdinsight/hdinsight-provision-clusters.md)kan finde flere oplysninger.<br/><br/><b>Placeringen af outputdata</b>: Angiver, om dataene er gemt i et Hadoop-distribueret filsystem (HDFS) eller i Azure. <br/><ul>Hvis du gemmer outputdata i HDFS, angive URI HDFS-serveren. (Skal du angive navnet på HDInsight klynge uden præfikset HTTPS://). <br/><br/>Hvis du gemmer dine outputdata i Azure, skal du angive kontonavn Azure lager, lagerplads hurtigtast og lagerplads objektbeholder navn.</ul>|
SQL-database |Læser data, der er gemt i en Azure SQL-database eller i en SQL Server-database, der kører på en Azure virtuelt. | <b>Navnet databaseserveren</b>: Angiver navnet på den server, som databasen kører.<br/><ul>Angiv navnet på serveren, der er oprettet i tilfælde af Azure SQL-Database. Den indeholder normalt formularen * &lt;generated_identifier&gt;. database.windows.net.* <br/><br/>I tilfælde af en SQL server hostes på en Azure virtuel maskine Angiv *tcp:&lt;virtuelt DNS-navn&gt;, 1433*</ul><br/><b>Databasenavnet </b>: Angiver navnet på databasen på serveren. <br/><br/><b>Navnet på Server brugerkonto</b>: Angiver et brugernavn for en konto, der har adgangstilladelser til databasen. <br/><br/><b>Server adgangskode til brugerkonto</b>: Angiver adgangskoden for kontoen.<br/><br/><b>Acceptere en hvilken som helst servercertifikat</b>: Brug denne indstilling (mindre sikkert), hvis du vil springe over Gennemse webstedets certifikat, inden du læser dine data.<br/><br/><b>Databaseforespørgsel</b>: Indtast en SQL_sætning, der beskriver de data, du vil læse.|
Lokal SQL-database |Læser data, der er gemt i en lokal SQL-database. | <b>Datastyringsgateway</b>: Angiver navnet på den Datastyringsgateway, der er installeret på en computer, hvor det kan få adgang til SQL Server-databasen. Finde oplysninger om konfiguration af gatewayen, [Udfør avancerede analytics med Azure Machine Learning ved hjælp af data fra en lokal SQL server](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Navnet databaseserveren</b>: Angiver navnet på den server, som databasen kører.<br/><br/><b>Databasenavn </b>: Angiver navnet på databasen på serveren. <br/><br/><b>Navnet på Server brugerkonto</b>: Angiver et brugernavn for en konto, der har adgangstilladelser til databasen. <br/><br/><b>Brugernavn og din adgangskode</b>: Klik på <b>Enter værdier</b> for at angive dine databaselegitimationsoplysninger. Du kan bruge Windows-integreret godkendelse eller SQL Server-godkendelse afhængigt af hvordan din lokale SQL Server er konfigureret.<br/><br/><b>Databaseforespørgsel</b>: Indtast en SQL_sætning, der beskriver de data, du vil læse.|
Azure Table|Læser data fra tjenesten tabel i Azure-lager.<br/><br/>Hvis du læser store datamængder sjældent, kan du bruge tjenesten Azure-tabel. Den indeholder en fleksibel, ikke-relationelle (NoSQL), stort omfang SVG, billige og meget tilgængelige lagerplads løsning.| Indstillingerne i dialogboksen **Importdata** ændres afhængigt af om du får adgang til offentlige oplysninger eller en privat lagerplads, der kræver logonoplysninger. Dette bestemmes af den <b>Godkendelsestype</b> som kan have værdien af "PublicOrSAS" eller "Kontoindstillinger", hver især har sit eget sæt af parametre. <br/><br/><b>Offentlige eller delte Access signatur (SAS) URI</b>: parametrene er:<br/><br/><ul><b>Tabel URI</b>: Angiver den offentlige eller SAS URL-adressen for tabellen.<br/><br/><b>Angiver rækkerne til at søge efter egenskabsnavne</b>: værdierne er <i>TopN</i> at scanne det angivne antal rækker eller <i>ScanAll</i> at få alle rækker i tabellen. <br/><br/>Hvis dataene er ensartet og mere forudsigelige, anbefales det, at du vælger *TopN* og angive et tal for N. Dette kan medføre hurtigere læse tidspunkter for store tabeller.<br/><br/>Hvis dataene er struktureret med sæt af egenskaber, der varierer baseret på dybde og placeringen af tabellen, skal du vælge indstillingen *ScanAll* at scanne alle rækker. Dette sikrer integriteten af din resulterende egenskaben og metadata omstilling.<br/><br/></ul><b>Privat lagerplads konto</b>: parametrene, er: <br/><br/><ul><b>Kontonavn</b>: Angiver navnet på den konto, der indeholder tabellen for at læse.<br/><br/><b>Kontonøgle</b>: Angiver tasten lagerplads, der er knyttet til kontoen.<br/><br/><b>Tabelnavn</b> : Angiver navnet på den tabel, der indeholder dataene, at læse.<br/><br/><b>Rækker til at søge efter egenskabsnavne</b>: værdierne er <i>TopN</i> at scanne det angivne antal rækker eller <i>ScanAll</i> at få alle rækker i tabellen.<br/><br/>Hvis dataene er ensartet og mere forudsigelige, anbefaler vi, at du vælger *TopN* og angive et tal for N. Dette kan medføre hurtigere læse tidspunkter for store tabeller.<br/><br/>Hvis dataene er struktureret med sæt af egenskaber, der varierer baseret på dybde og placeringen af tabellen, skal du vælge indstillingen *ScanAll* at scanne alle rækker. Dette sikrer integriteten af din resulterende egenskaben og metadata omstilling.<br/><br/>|
Azure Blob-lager | Læser data, der er gemt i tjenesten Blob i Azure-lager, herunder billeder, ustrukturerede tekst eller binære data.<br/><br/>Du kan bruge tjenesten Blob offentligt vist data eller privat lagre programmet data. Du kan få adgang til dine data fra et vilkårligt sted ved hjælp af HTTP eller HTTPS forbindelser. | Indstillingerne i modulet **Importdata** ændres afhængigt af om du får adgang til offentlige oplysninger eller en privat lagerplads, der kræver logonoplysninger. Dette bestemmes af den <b>Godkendelsestype</b> , som kan have en værdi på "PublicOrSAS" eller "Konto".<br/><br/><b>Offentlige eller delte Access signatur (SAS) URI</b>: parametrene er:<br/><br/><ul><b>URI</b>: Angiver den offentlige eller SAS URL-adressen for blob storage.<br/><br/><b>Filformat</b>: Angiver formatet på dataene i Blob-tjenesten. Understøttede formater er csv-TSV og ARFF.<br/><br/></ul><b>Privat lagerplads konto</b>: parametrene, er: <br/><br/><ul><b>Kontonavn</b>: Angiver navnet på den konto, der indeholder den blob, du vil læse.<br/><br/><b>Kontonøgle</b>: Angiver tasten lagerplads, der er knyttet til kontoen.<br/><br/><b>Stien til objektbeholder, mappe, eller blob</b> : Angiver navnet på den blob, der indeholder dataene, at læse.<br/><br/><b>BLOB-filformat</b>: Angiver formatet på dataene i blob-tjenesten. Understøttede dataformater er csv-TSV ARFF, og CSV-med et angivet kodning og Excel. <br/><br/><ul>Hvis formatet er csv- eller TSV, skal du sørge for at angive, om filen indeholder en kolonneoverskrift.<br/><br/>Du kan bruge indstillingen Excel til at læse data fra Excel-projektmapper. I indstillingen <i>Excel dataformat</i> , angive, om dataene er i et Excel-regnearksområde eller i en Excel-tabel. Angiv navnet på det ark eller den tabel, du vil læse fra i indstillingen <i>Excel-regneark eller en integreret tabel </i>.</ul><br/>|
Datafeeddataprovider | Læser data fra en understøttet feed udbyder. I øjeblikket kun formatet Open Data Protocol (OData) understøttes. | <b>Data indholdstype</b>: Angiver formatet OData.<br/><br/><b>Kildens URL-adresse</b>: Angiver den fulde URL-adresse på datafeedet. <br/>For eksempel følgende URL-adressen læser fra Northwind-eksempeldatabasen: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
