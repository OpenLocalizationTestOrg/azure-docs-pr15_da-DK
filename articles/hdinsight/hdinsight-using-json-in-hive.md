<properties
   pageTitle="Analysere og proces JSON dokumenter med Hive i HDInsight | Microsoft Azure"
   description="Lær at bruge JSON-dokumenter og analysere dem ved hjælp af Hive i HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Behandle og analysere JSON dokumenter ved hjælp af Hive i HDInsight

Lær at behandle og analysere JSON filer ved hjælp af Hive i HDInsight. Følgende JSON dokument, der skal bruges i selvstudiet

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

Filen kan findes på wasbs://processjson@hditutorialdata.blob.core.windows.net/. Du kan finde flere oplysninger om brug af Azure Blob-lager med HDInsight, [Brug HDFS-kompatible Azure Blob-lager med Hadoop i HDInsight](hdinsight-hadoop-use-blob-storage.md). Du kan kopiere filen til objektbeholderen standard af din klynge, hvis du vil have.

I dette selvstudium bruger du Hive-konsollen.  Finde en vejledning til åbning af Hive-konsollen, kan du se [Brug Hive med Hadoop på HDInsight med Fjernskrivebord](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Udjævne JSON dokumenter

Metoderne i næste afsnit kræver JSON dokumentet i en enkelt række. Så skal du udjævne JSON dokumentet til en streng. Hvis dokumentet JSON er allerede flad, kan du springe dette trin over og gå direkte til den næste sektion på JSON analyse af data.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

Raw JSON-filen er placeret i **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. Tabellen *StudentsRaw* Hive peger på rå ophævelse flad JSON-dokumentet.

Tabellen *StudentsOneLine* Hive gemme dataene i filsystemet HDInsight standard under ** /json/studerende/sti.

Sætningen INSERT udfylde tabellen StudentOneLine med de flad JSON-data.

SELECT-sætningen skal kun returnere 1 række.

Her er et output fra SELECT-sætningen:

![Udjævning af JSON dokumentet.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analysere JSON dokumenter i Hive

Hive indeholder tre forskellige funktioner til at køre forespørgsler på JSON dokumenter:

- Brug\_JSON\_objekt UDF (brugerdefineret funktionen)
- Brug JSON_TUPLE UDF
- bruge brugerdefinerede SerDe
- skrive, du ejer UDF ved hjælp af Python eller andre sprog. Se [denne artikel] [ hdinsight-python] på kører din egen Python kode med Hive.

### <a name="use-the-getjsonobject-udf"></a>Brug\_JSON_OBJECT UDF
Hive indeholder en indbygget UDF kaldet [Hent json objekt](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) som kan udføres JSON forespørgsler på kørselstidspunktet. Denne metode tager to argumenter – tabelnavnet og metodenavn, som har flad JSON dokumentet og feltet JSON, der skal overføres. Lad os se på et eksempel for at se, hvordan denne UDF fungerer.

Få fornavn og efternavn for hver enkelt studerende

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Her er output, når du kører denne forespørgsel i console-vinduet.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Der er nogle begrænsninger for get-json_object UDF.

- Da hvert felt i forespørgslen kræver igen under fortolkning af forespørgslen, påvirker ydeevnen.
- FÅ\_JSON_OBJECT() returnerer Strengrepræsentationen af en matrix. Hvis du vil konvertere det til en Hive-matrix, skal du nødt til at bruge regulære udtryk til at erstatte de kantede parenteser ' [' og ']' og kald derefter også opdeling for at få matrixen.


Det er derfor Hive wikien anbefaler at bruge json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Brug JSON_TUPLE UDF

En anden UDF leveres af Hive kaldes [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) som udfører bedre end [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Denne metode tager en række taster og en JSON-streng, og returnerer en tupel værdier ved hjælp af en funktion. Følgende forespørgsel returnerer student-id og kategori fra JSON dokumentet:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Output fra dette script i konsollen Hive:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPEL bruger [sideretningen visning](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntaksen i Hive, som giver mulighed for json\_tupel til at oprette en virtuel tabel ved at anvende funktionen UDT til hver række i den oprindelige tabel.  Kompleks JSONs bliver for uhåndterlig på grund af gentagne brugen af SIDERETNINGEN visning. Desuden kan ikke JSON_TUPLE håndtere indlejrede JSONs.


###<a name="use-custom-serde"></a>Bruge brugerdefinerede SerDe

SerDe er den bedste løsning til parsing af indlejrede JSON-dokumenter, du kan definere JSON-skema, og brug skemaet til at analysere dokumenterne. I dette selvstudium skal du bruge en af det mere populært SerDe, der er blevet udviklet af [rcongiu](https://github.com/rcongiu).

**For at bruge den brugerdefinerede SerDe:**

1. Installere [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Vælg Windows X64-versionen af JDK, hvis du skal bruge Windows-installation af HDInsight

    >[AZURE.WARNING] JDK 1,8 fungerer ikke med denne SerDe.

    Når installationen er fuldført, kan du tilføje en ny bruger miljøvariablen:

    1. Åbn **visningen Avancerede systemindstillinger** fra skærmbilledet Windows.
    2. Klik på **miljøvariabler**.  
    3. Tilføje en ny **JAVA_HOME** miljøvariablen peger på **C:\Program Files\Java\jdk1.7.0_55** eller hvorend din JDK er installeret.

    ![Konfiguration af korrekte config værdier i JDK][image-hdi-hivejson-jdk]

2. Installere [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Føje mappen bin til din sti ved at gå til kontrolelementet Panel--> Rediger systemvariabler for din konto Environment variabler. Skærmbilledet nedenfor viser, hvordan du gør dette.

    ![Konfiguration af Maven][image-hdi-hivejson-maven]

3. Klone projektet fra [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github websted. Du kan gøre dette ved at klikke på knappen "Hent Zip", som vist i skærmbilledet nedenfor.

    ![Klone projektet][image-hdi-hivejson-serde]

4: gå til den mappe, hvor du har hentet denne pakke og type "mvn pakke". Dette bør oprette de nødvendige glas filer, som du kan derefter kopiere til klyngen.

5: gå til destinationsmappen under rodmappen, hvor du har hentet pakken. Overføre filen json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar til hoved-node for din klynge. Jeg normalt Placer det under mappen hive binære: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin eller noget lignende.

6: i hive bliver spurgt, skal du skrive "Tilføj glas /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Da i mit tilfælde er krukken i mappen C:\apps\dist\hive-0.13.x\bin, kan jeg direkte tilføje glas med navnet, som vist nedenfor:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Nu er du klar til at bruge SerDe til at køre forespørgsler i JSON dokumentet.

Følgende sætning opretter en tabel med et defineret skema

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Med listen over fornavnet og efternavnet på student

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Her er resultatet fra konsollen Hive.

![SerDe forespørgsel 1][image-hdi-hivejson-serde_query1]

Til at beregne summen af resultater af JSON-dokument

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

Den ovenstående forespørgsel bruger [sideretningen visning adskille](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF til at udvide matrixen af pointtal, så de kan lægges.

Her er et output fra konsollen Hive.

![SerDe forespørgsel 2][image-hdi-hivejson-serde_query2]

Til at finde ud af, hvilke emner, der er mere end 80 punkter har karakter for en given studerende Vælg  
      JT. StudentClassCollection.ClassId fra json_table jt sideretningen visning adskille (jt. StudentClassCollection.Score) af websteder som resultat hvor resultat > 80.

Den ovenstående forespørgsel returnerer en Hive matrix i modsætning til få\_json\_objekt, der returnerer en streng.

![SerDe forespørgsel 3][image-hdi-hivejson-serde_query3]

Hvis du vil skil forkert udformet JSON, og derefter som beskrevet i den [wikiside](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) i denne SerDe kan du opnå, ved at indtaste koden nedenfor:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Oversigt
Du kan altså typer JSON operator i Hive, som du vælger, afhænger af scenariet. Hvis du har en simpel JSON-dokument, og du kun har ét felt til at slå op på – du kan vælge at brug Hive UDF\_json\_objekt. Hvis du har mere end én taster til at slå op kan du bruge json_tuple. Hvis du har en indlejret dokument, skal du bruge JSON-SerDe.

Finde andre relaterede artikler

- [Bruge Hive og HiveQL med Hadoop i HDInsight til at analysere et eksempel Apache log4j-fil](hdinsight-use-hive.md)
- [Analysere flight forsinkelse data ved hjælp af Hive i HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analysere Twitter data ved hjælp af Hive i HDInsight](hdinsight-analyze-twitter-data.md)
- [Køre et Hadoop-job ved hjælp af DocumentDB og HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
