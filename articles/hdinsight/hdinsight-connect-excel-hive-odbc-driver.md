<properties
   pageTitle="Oprette forbindelse Excel til Hadoop med Hive ODBC-Driver | Microsoft Azure"
   description="Lær, hvordan du konfigurerer og bruger Microsoft Hive ODBC-driver til Excel til at forespørge om data i en HDInsight klynge."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Oprette forbindelse til Excel til Hadoop med Microsoft Hive ODBC-driver

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Big Data løsning integrerer Microsoft Business Intelligence (BI) komponenter med Apache Hadoop klynger, der er implementeret ved Azure HDInsight. Et eksempel på denne integration er muligheden for at oprette forbindelse Excel til et Hadoop-klynge i HDInsight ved hjælp af Microsoft Hive Open Database Connectivity (ODBC)-Driver Hive datawarehouse.

Det er også muligt at oprette forbindelse til de data, der er knyttet til en HDInsight klynge og andre datakilder, herunder andre (ikke-HDInsight) Hadoop-klynger, fra Excel ved hjælp af tilføjelsesprogrammet Microsoft Power-forespørgsel til Excel. Oplysninger om installation og brug af Power-forespørgsel, du [Forbinder du Excel til HDInsight med Power-forespørgsel][hdinsight-power-query].

> [AZURE.NOTE] Mens trinnene i denne artikel kan bruges med en Linux eller Windows-baseret HDInsight klynge, er Windows påkrævet for klientarbejdsstationen.

**Forudsætninger**:

Før du begynder i denne artikel, skal du have følgende:

- **En HDInsight klynge**. Hvis du vil oprette et, se [Introduktion til Azure HDInsight][hdinsight-get-started].
- **A arbejdsstation** med Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone eller Office 2010 Professional Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Installere Microsoft Hive ODBC-driver

Hent og Installer Microsoft Hive ODBC-Driver fra [Overførselscenter][hive-odbc-driver-download].

Denne driver kan installeres på 32-bit eller 64-bit versioner af Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 og Windows Server 2012 og vil tillade forbindelsen til Azure HDInsight (version 1,6 eller nyere) og Azure HDInsight Emulator (v.1.0.0.0 og nyere). Du skal installere den version, der stemmer overens med versionen af det program, hvor du vil bruge ODBC-driver. Dette selvstudium, der skal bruges driveren fra Office Excel.

##<a name="create-hive-odbc-data-source"></a>Oprette Hive ODBC-datakilde

Følgende trin viser, hvordan du opretter en Hive ODBC-datakilde.

1. Tryk på Windows for at åbne skærmbilledet Start fra Windows 8 eller Windows 10, og skriv derefter **datakilder**.
2. Klik på **konfigurere ODBC-datakilder (32-bit)** eller **konfigurere ODBC-datakilder (64-bit)** afhængigt af din version af Office. Hvis du kører Windows 7, skal du vælge **ODBC-datakilder (32-bit)** eller **ODBC-datakilder (64-bit)** fra **Administration**. Dette vil starte dialogboksen **ODBC-Data Source Administrator** .

    ![ODBC datakildeadministrator][img-hdi-simbahiveodbc-datasource-admin]

3. Klik på **Tilføj** for at åbne guiden **Opret ny datakilde** fra bruger DNS.
4. Vælg **Microsoft Hive ODBC-Driver**, og klik derefter på **Udfør**. Dette vil starte dialogboksen **Microsoft Hive ODBC-Driver DNS-installation** .

5. Skriv eller Vælg følgende værdier:

    Egenskaben|Beskrivelse
    ---|---
    Datakildenavn|Give et navn til din datakilde
    Host|Angiv &lt;HDInsightClusterName >. azurehdinsight.net. For eksempel myHDICluster.azurehdinsight.net
    Port|Brug <strong>443</strong>. (Denne port er ændret fra 563 til 443).
    Database|Du kan bruge <strong>som standard</strong>.
    Hive servertype|Vælg <strong>Hive Server 2</strong>
    Ordning|Vælg <strong>Tjeneste, Azure HDInsight</strong>
    HTTP-sti|Lad den stå tomt.
    Brugernavn|Angiv HDInsight klynge bruger brugernavn. Dette er det brugernavn, der er oprettet under processen klynge klargøring. Hvis du har brugt indstillingen Hurtig Opret, er standard brugernavn <strong>administrator</strong>.
    Adgangskode|Skriv HDInsight klynge brugerens adgangskode.
    </table>

    Der er nogle vigtige parametre for at være opmærksom på, når du klikker på **Avancerede indstillinger**:

    Parameter|Beskrivelse
    ---|---
    Brug Oprindelig forespørgsel|Når den er markeret, forsøger ODBC-driver ikke at konvertere TSQL til HiveQL. Du skal bruge det kun, hvis du er 100% for, at du er ved at sende ren HiveQL sætninger. Når du opretter forbindelse til SQL Server eller Azure SQL-Database, skal du lade det være umarkerede.
    Rækker hentet hver blok|Når du henter en stor mængde poster, justering af denne parameter muligvis at sikre, at opnå optimal ydeevne.
    Standard strenglængde kolonne, binær kolonnelængde, Decimal kolonne skala|Datatypen længde og modellerne kan påvirke, hvordan data returneres. De kan medføre forkerte oplysninger der skal returneres på grund af tab af præcision og/eller afkortning af.


    ![Avancerede indstillinger][img-HiveOdbc-DataSource-AdvancedOptions]

6. Klik på **Test** for at teste datakilden. Når først datakilden er konfigureret korrekt, der vises for *test af fuldført!*.
7. Klik på **OK** for at lukke dialogboksen Test. Den nye datakilde skulle nu være angivet på **ODBC-Data Source Administrator**.
8. Klik på **OK** for at lukke guiden.

##<a name="import-data-into-excel-from-hdinsight"></a>Importere data til Excel fra HDInsight

Følgende fremgangsmåde beskriver den måde at importere data fra en hive-tabel til en Excel-projektmappe med ODBC-datakilde, du har oprettet i ovenstående trin.

1. Åbn en ny eller eksisterende projektmappe i Excel.
2. Klik på **Fra andre datakilder**fra fanen **Data** , og klik derefter på **Fra guiden Dataforbindelse** for at starte **Guiden Dataforbindelse**.

    ![Åbn guiden Dataforbindelse][img-hdi-simbahiveodbc.excel.dataconnection]

3. Vælg **ODBC DSN** som datakilde, og klik derefter på **Næste**.
4. Vælg navnet på datakilden, du har oprettet i ovenstående trin fra ODBC-datakilder, og klik derefter på **Næste**.
5. Angive adgangskoden for-klyngen i guiden igen, og klik derefter på **Test** for at bekræfte konfigurationen igen, hvis det er nødvendigt.
6. Klik på **OK** for at lukke dialogboksen test.
7. Klik på **OK**. Vente på dialogboksen **Vælg Database og tabel** at åbne. Det kan tage et par sekunder.
8. Markér den tabel, du vil importere, og klik derefter på **Næste**. *Hivesampletable* er et eksempel hive på tabellen, der følger med HDInsight klynger.  Du kan vælge den, hvis du ikke har oprettet et. Du kan finde flere oplysninger under kørsel Hive forespørgsler og oprette Hive tabeller, skal du se [Brug Hive med HDInsight][hdinsight-use-hive].
8. Klik på **Udfør**.
9. I dialogboksen **Importdata** , kan du ændre eller angive forespørgslen. Hvis du vil gøre det, skal du klikke på **Egenskaber**. Det kan tage et par sekunder.
10. Klik på fanen **Definition** , og derefter føje **GRÆNSE 200** til Hive select-sætningen i tekstfeltet **Kommandotekst** . Ændringen vil begrænse de returnerede postsæt til 200.

    ![Egenskaber for forbindelse][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Klik på **OK** for at lukke dialogboksen Egenskaber for forbindelse.
12. Klik på **OK** for at lukke dialogboksen **Importdata** .  
13. Angive adgangskoden igen, og klik derefter på **OK**. Det tager et par sekunder, før data bliver importeret til Excel.

##<a name="next-steps"></a>Næste trin

I denne artikel lært du, hvordan du bruger Microsoft Hive ODBC-driver til at hente data fra HDInsight Service i Excel. På samme måde, kan du hente data fra HDInsight Service i SQL-Database. Det er også muligt at overføre data til en HDInsight Service. Hvis du vil vide mere, skal du se:

- [Analysere flight forsinkelse data ved hjælp af HDInsight][hdinsight-analyze-flight-data]
- [Overføre Data til HDInsight][hdinsight-upload-data]
- [Bruge Sqoop med HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
