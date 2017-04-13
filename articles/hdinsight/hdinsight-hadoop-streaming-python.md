<properties
   pageTitle="Udvikle Python MapReduce sager med HDInsight | Microsoft Azure"
   description="Lær at oprette og køre Python MapReduce job på Linux-baserede HDInsight klynger."
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

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Udvikle Python streaming programmer til HDInsight

Hadoop indeholder en streaming API til MapReduce, der gør det muligt at skrive kort og reducere funktioner på andre sprog end Java. I denne artikel kan lære du, hvordan du bruger Python til at udføre MapReduce handlinger.

> [AZURE.NOTE] Mens Python koden i dette dokument kan bruges sammen med en Windows-baseret HDInsight klynge, er trinnene i dette dokument er specifikke for Linux-baserede klynger.

I denne artikel er baseret på oplysninger og eksempler, der er udgivet af Michael Noll til at [skrive et Hadoop MapReduce Program i Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Linux-baserede Hadoop på HDInsight klynge

* Et tekstredigeringsprogram

    > [AZURE.IMPORTANT] Tekstredigeringsprogrammet skal bruge LF som linje slutningen. Hvis den bruger CRLF, medfører dette fejl, når der benyttes MapReduce jobbet Linux-baserede HDInsight klynger. Hvis du ikke er sikker på, kan du bruge det valgfrie trin i sektionen [Køre MapReduce](#run-mapreduce) til at konvertere en hvilken som helst CRLF til LF.

* For Windows-klienter, trykfarver og PSCP. Disse funktioner er tilgængelige fra <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Trykfarver hente side</a>.

##<a name="word-count"></a>Antallet af ord

I dette eksempel skal vil du implementere en grundlæggende Ordtælling ved hjælp af en mapper og reduktionsrør. Mapperen opdeler sætninger i enkeltord, og reduktionsrør samler ord og tæller for at frembringe output.

I nedenstående rutediagram illustreres, hvad der sker under kortet og reducere faser.

![Illustration af kort reducere](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Hvorfor Python?

Python er et generelt, overordnet programmeringssprog, der gør det muligt at udtrykkelig begreber i færre kodelinjer end mange andre sprog. Det har for nylig blev populære med data forskere som et sprog i prototyper, fordi dens fortolket art, dynamisk at skrive og elegante syntaksen gør det passer til udvikling af hurtig programmer.

Python er installeret på alle HDInsight klynger.

##<a name="streaming-mapreduce"></a>Streaming MapReduce

Hadoop kan du angive en fil, der indeholder kortet og reducere logik, der bruges af en sag. De specifikke krav til kortet og reducere logik er:

* **Input**: kortet og reducere komponenter skal læse indtastede data fra STDIN.

* **Output**: kortet og reducere komponenter skal skrives outputdata til STDOUT.

* **Dataformat**: dataene consumed og produceret skal være et tasten/værdi-par, adskilt af et tabulatortegn.

Python kan nemt håndtere kravene ved ved hjælp af modulet **for** at læse fra STDIN og **udskrive** udskrive til STDOUT. Resterende opgaven er blot formatering af data med en fane (`\t`) tegnet mellem nøgle og værdi.

##<a name="create-the-mapper-and-reducer"></a>Oprette mapper og reduktionsrør

Mapper og reduktionsrør er tekstfiler, i dette tilfælde **mapper.py** og **reducer.py** (for at gøre det klart, der gør hvad). Du kan oprette disse ved hjælp af editor efter eget valg.

###<a name="mapperpy"></a>Mapper.py

Oprette en ny fil med navnet **mapper.py** og bruge følgende kode, som indeholder:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Tage et øjeblik på at læse koden, så du kan forstå, hvad gør den.

###<a name="reducerpy"></a>Reducer.py

Oprette en ny fil med navnet **reducer.py** og bruge følgende kode, som indeholder:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Overføre filerne

Både **mapper.py** og **reducer.py** skal være på noden hoved af klyngen, før vi kan køre dem. Den nemmeste måde at overføre dem er at bruge **scp** (**pscp** Hvis du bruger en Windows-klient).

Brug følgende kommando i den samme mappe som **mapper.py** og **reducer.py**, fra klienten. Erstat **brugernavn** med en SSH bruger og **clustername** med navnet på din klynge.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Derved kopieres filerne fra det lokale system til noden hoved.

> [AZURE.NOTE] Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om adgangskoden. Hvis du har brugt en SSH nøgle, kan du skal bruge den `-i` parameter og stien til den private nøgle, for eksempel `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Køre MapReduce

1. Oprette forbindelse til klyngen ved hjælp af SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Hvis du brugte en adgangskode til at sikre kontoen SSH, bliver du bedt om adgangskoden. Hvis du har brugt en SSH nøgle, kan du skal bruge den `-i` parameter og stien til den private nøgle, for eksempel `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Valgfrit) Hvis du har brugt et tekstredigeringsprogram, der bruger CRLF som linjer, der slutter ved oprettelse af mapper.py og reducer.py filer, eller ikke ved, hvad linje-slut editor bruger skal du bruge følgende kommandoer til at konvertere forekomster af CRLF i mapper.py og reducer.py til LF.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Brug følgende kommando for at starte MapReduce jobbet.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Denne kommando består af følgende dele:

    * **hadoop-streaming.jar**: bruges, når der udføres streaming MapReduce handlinger. Det har en grænseflade Hadoop med den eksterne MapReduce kode, du angiver.

    * **-filer**: fortæller Hadoop, der er behov for de angivne filer til dette MapReduce job, og de skal kopieres til alle arbejder noder.

    * **-mapper**: fortæller Hadoop hvilke fil, der skal bruges som mapperen.

    * **-reduktionsrør**: fortæller Hadoop hvilke fil, der skal bruges som reduktionsrør.

    * **-input**: filen input, som vi bør tælle ord fra.

    * **-output**: den mappe, der skal skrives output til.

        > [AZURE.NOTE] Denne mappe oprettes af jobbet.

Du skal se en masse **oplysninger om** sætninger når jobbet starter, og til sidst se handlingen **kort** og **reducere** vises som procent.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Du modtager statusoplysninger om kørslen, når den er fuldført.

##<a name="view-the-output"></a>Få vist output

Når jobbet er fuldført, skal du bruge følgende kommando til at få vist output:

    hdfs dfs -text /example/wordcountout/part-00000

Dette bør vise en liste over ord, og hvor mange gange ordet opstod. Følgende er et eksempel på outputdataene:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger streaming MapRedcue job med HDInsight, kan du bruge følgende links for at undersøge andre måder at arbejde med Azure HDInsight.

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Brug MapReduce sager med HDInsight](hdinsight-use-mapreduce.md)
