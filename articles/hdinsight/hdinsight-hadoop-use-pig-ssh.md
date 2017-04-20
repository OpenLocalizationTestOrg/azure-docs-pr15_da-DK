<properties
   pageTitle="Bruge Hadoop svin med SSH på en klynge med HDInsight | Microsoft Azure"
   description="Lær, hvordan du opretter forbindelse til en Hadoop Linux-baseret klynge med SSH og derefter bruge kommandoen svin at køre svin Latin sætninger interaktivt eller som et batchjob."
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

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Køre svin job på en Linux-baseret klynge med kommandoen svin (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

I dette dokument gennemgå du processen med at oprette forbindelse til en Linux-baserede Azure HDInsight-klynge ved hjælp af Secure Shell (SSH), derefter ved hjælp af kommandoen svin til at køre svin Latin sætninger interaktivt, eller som et batchjob.

Programmeringssproget svin Latin gør det muligt at beskrive transformeringer, der anvendes til at producere en ønsket afgang input data.

> [AZURE.NOTE] Hvis du er bekendt med brug af Hadoop Linux-baserede servere, men ikke kender HDInsight, se [Tip til Linux-baserede HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Forudsætninger

Hvis du vil udføre trinnene i denne artikel, skal du følgende.

* En klynge af Linux-baserede HDInsight (Hadoop på HDInsight).

* En SSH-klient. Linux, Unix og Mac OS skal komme med en SSH-klient. Windows-brugere skal hente en klient, som [trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Oprette forbindelse til SSH

Oprette forbindelse til det fuldt kvalificerede domænenavn (FQDN) i HDInsight-klynge ved hjælp af kommandoen SSH. Det fuldt kvalificerede Domænenavn er det navn, du gav klyngen, derefter **. azurehdinsight.net**. For eksempel følgende ville oprette forbindelse til en klynge med navnet **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en certifikatnøgle til SSH godkendelse** , da du oprettede-klyngen HDInsight, kan skal du angive placeringen af den private nøgle på klientsystemet.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Hvis du har angivet en adgangskode til godkendelse af SSH** , da du oprettede-klyngen HDInsight, skal du angive adgangskoden, når du bliver spurgt.

Finde flere oplysninger om brug af SSH med HDInsight, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X og Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Trykfarver (Windows-baserede klienter)

Windows indeholder ikke en indbygget SSH-klient. Vi anbefaler brug af **trykfarver**, der kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Brug kommandoen svin

2. Når tilsluttet, kan du starte svin kommandolinjegrænsefladen (CLI) ved hjælp af følgende kommando.

        pig

    Efter et øjeblik, bør du se en `grunt>` prompt.

3. Angiv følgende sætning.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Denne kommando indlæser indholdet af filen sample.log i LOGFILER. Du kan få vist indholdet af filen ved at bruge følgende.

        DUMP LOGS;

4. Derefter omdanne dataene ved hjælp af et regulært udtryk for at udtrække logføringsniveauet fra hver post ved hjælp af følgende.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Du kan bruge **DUMP** for datavisningen efter transformeringen. I dette tilfælde skal du bruge `DUMP LEVELS;`.

5. Fortsætte med at anvende transformeringer ved hjælp af følgende udsagn. Brug `DUMP` til at få vist resultatet af transformationen efter hvert trin.

    <table>
    <tr>
    <th>Sætning</th><th>Hvad gør</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER NIVEAUER af LOGLEVEL er ikke null.</td><td>Fjerner de rækker, der indeholder en null-værdi for logføringsniveau og gemmer resultaterne i FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = gruppe FILTEREDLEVELS af LOGLEVEL;</td><td>Grupperer rækker af logføringsniveau og gemmer resultaterne i GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREKVENSER = foreach GROUPEDLEVELS generere gruppe som LOGLEVEL, antal (FILTEREDLEVELS. LOGLEVEL) som TÆLLE;</td><td>Opretter et nyt sæt af data, der indeholder hver entydig log niveau, og hvor mange gange det sker. Dette er gemt i FREKVENSER.</td>
    </tr>
    <tr>
    <td>RESULTAT = ordre FREKVENSER af antal desc;</td><td>Standardlogfilen er grupperet efter antal (faldende) og gemmer i RESULTATET.</td>
    </tr>
    </table>

6. Du kan også gemme resultaterne af en transformering ved hjælp af den `STORE` sætning. For eksempel følgende gemmer den `RESULT` til mappen **/example/data/pigout** på objektbeholderen til lagring af standard for din klynge.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Dataene gemmes i den angivne mappe i filer med navnet **del nnnnn**. Hvis mappen allerede findes, modtager du en fejlmeddelelse.

7. Angiv følgende sætning for at afslutte grunt-prompten.

        QUIT;

###<a name="pig-latin-batch-files"></a>Svin Latin batchfiler

Du kan også bruge kommandoen svin til at køre svin Latin, der er indeholdt i en fil.

3. Når du har afsluttet grunt-prompten, skal du bruge følgende kommando til STDIN-pipe i en fil med navnet **pigbatch.pig**. Denne fil oprettes i hjemmemappen for den konto, du er logget på for SSH-session.

        cat > ~/pigbatch.pig

4. Skriv eller Indsæt de følgende linjer, og brug derefter Ctrl + D, når du er færdig.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Du kan bruge følgende for at køre filen **pigbatch.pig** ved hjælp af kommandoen svin.

        pig ~/pigbatch.pig

    Når kørslen er færdig, skal du se følgende output, som skal være det samme som når du har brugt `DUMP RESULT;` i de forrige trin.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Oversigt

Som du kan se, er kommandoen svin kan du interaktivt køre MapReduce operationer ved hjælp af svin Latin samt køre sætninger, der er gemt i en batchfil.

##<a id="nextsteps"></a>Næste trin

Generelle oplysninger om svin i HDInsight.

* [Du kan bruge svin med Hadoop på HDInsight](hdinsight-use-pig.md)

Oplysninger om andre måder, kan du arbejde med Hadoop på HDInsight.

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)
