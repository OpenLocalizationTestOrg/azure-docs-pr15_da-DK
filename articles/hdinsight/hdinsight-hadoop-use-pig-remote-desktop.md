<properties
   pageTitle="Bruge Hadoop gris med Fjernskrivebord i HDInsight | Microsoft Azure"
   description="Lær at bruge kommandoen gris til at køre gris latinsk sætninger Fjernskrivebord forbindelsen til en Windows-baseret Hadoop klynge i HDInsight."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Køre gris job fra en forbindelse til Fjernskrivebord

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dette dokument indeholder en gennemgang for ved hjælp af kommandoen gris til at køre gris latinsk sætninger Fjernskrivebord forbindelsen til en Windows-baseret HDInsight klynge. Gris latinsk kan du oprette MapReduce programmer ved at beskrive datatransformationer i stedet for at tilknytte og reducere funktioner.

I dette dokument, se, hvordan du

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende.

* En Windows-baseret HDInsight (Hadoop på HDInsight) klynge

* En klientcomputer, der kører Windows 10, Windows 8 eller Windows 7

##<a id="connect"></a>Oprette forbindelse til Fjernskrivebord

Aktivere Fjernskrivebord for HDInsight-klyngen, og derefter oprette forbindelse til den ved at følge vejledningen på [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Bruge kommandoen gris

2. Når du har forbindelse til Fjernskrivebord, kan du starte **Hadoop kommandolinjen** ved hjælp af ikonet på skrivebordet.

2. Brug følgende fremgangsmåde til at starte kommandoen gris:

        %pig_home%\bin\pig

    Du vil få vist en `grunt>` prompt.

3. Angiv følgende sætning:

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Denne kommando indlæser indholdet af filen sample.log i filen LOGFILER. Du kan få vist indholdet af filen ved hjælp af følgende kommando:

        DUMP LOGS;

4. Transformere dataene ved at anvende et søgemønster at udtrække kun logføringsniveauet fra hver post:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Du kan bruge **lagring** til at få vist dataene efter transformationen. I dette tilfælde `DUMP LEVELS;`.

5. Fortsætte med at anvende transformationer ved hjælp af følgende udtalelser. Brug `DUMP` til at få vist resultatet af transformationen efter hvert trin.

    <table>
    <tr>
    <th>Sætningen</th><th>Hvad gør den</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER NIVEAUER af LOGLEVEL ikke er null.</td><td>Fjerner rækker, der indeholder en null-værdi for logføringsniveau og gemmer resultatet i FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = gruppe FILTEREDLEVELS ved LOGLEVEL;</td><td>Grupperer rækker efter logføringsniveau og gemmer resultatet i GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREKVENS = foreach GROUPEDLEVELS generere gruppe som LOGLEVEL, Tæl (FILTEREDLEVELS. LOGLEVEL) som TÆLLE;</td><td>Opretter et nyt sæt af data, der indeholder hver entydige logfil niveau værdi og hvor mange gange den opstår. Dette er gemt i hyppighed</td>
    </tr>
    <tr>
    <td>RESULTATET = rækkefølge hyppighed af Tæl desc;</td><td>Logføringsniveauer er grupperet efter antal (faldende), og gemmer i resultat</td>
    </tr>
    </table>

6. Du kan også gemme resultaterne af en transformering ved hjælp af den `STORE` sætning. For eksempel følgende kommando gemmer den `RESULT` til mappen **/example/data/pigout** i objektbeholderen til lagring af standard for din klynge:

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Data, der er gemt i den angivne mappe i filer med navnet **del nnnnn**. Hvis mappen allerede findes, modtager du en fejlmeddelelse.

7. For at afslutte grunt bliver bedt om det, skal du angive følgende sætning.

        QUIT;

###<a name="pig-latin-batch-files"></a>Gris latinsk batchfiler

Du kan også bruge kommandoen gris til at køre gris latinsk, der er indeholdt i en fil.

3. Åbn **Notesblok** efter at grunt prompten, og Opret en ny fil med navnet **pigbatch.pig** i mappen **% PIG_HOME %** .

4. Skriv eller Indsæt følgende linjer i **pigbatch.pig** filen, og Gem den derefter:

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Brug følgende til at køre filen **pigbatch.pig** ved hjælp af kommandoen gris.

        pig %PIG_HOME%\pigbatch.pig

    Når kørslen er fuldført, skal du se følgende output, som skal være den samme som når du har brugt `DUMP RESULT;` i de forrige trin:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Oversigt

Som du kan se, kommandoen gris giver dig mulighed at køre MapReduce handlinger interaktivt eller køre gris latinsk job, der er gemt i en batchfil.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om gris i HDInsight:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)
