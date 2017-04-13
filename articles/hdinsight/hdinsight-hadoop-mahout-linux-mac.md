<properties
    pageTitle="Generere anbefalinger ved hjælp af Mahout og Linux-baserede HDInsight | Microsoft Azure"
    description="Lær at bruge Apache Mahout maskinen læ bibliotek til at generere film anbefalinger med Linux-baserede HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Generere anbefalinger film ved hjælp af Apache Mahout med Linux-baserede Hadoop i HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Lær at bruge [Apache Mahout](http://mahout.apache.org) maskinen læ bibliotek med Azure HDInsight til at generere film anbefalinger.

Mahout er en [maskine læ] [ ml] bibliotek til Apache Hadoop. Mahout indeholder algoritmer til at behandle data, som filtrerer, klassificering, og klynge. I denne artikel kan bruge du en anbefaling program til at generere film anbefalinger, der er baseret på dine venner har set-film.

> [AZURE.NOTE] Trinnene i dette dokument kræver en Linux-baserede Hadoop på HDInsight klynge. Oplysninger om brug af Mahout med en Windows-baseret klynge, se [Generer film anbefalinger ved hjælp af Apache Mahout med Windows-baseret Hadoop i HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Forudsætninger

* Et Linux-baserede Hadoop på HDInsight klynge. Oplysninger om oprettelse af en finder du i [gang med at bruge Linux-baserede Hadoop i HDInsight][getstarted]

##<a name="mahout-versioning"></a>Mahout versionsstyring

Se [HDInsight versioner og Hadoop-komponenter](hdinsight-component-versioning.md)kan finde flere oplysninger om versionen af Mahout inkluderet med din HDInsight klynge.

> [AZURE.WARNING] Selvom det er muligt at overføre en anden version af Mahout til HDInsight klynge, kun komponenter, der er angivet med HDInsight klynge understøttes fuldt ud og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.
>
> Brugerdefinerede komponenter modtager kommercielt begrundet support for at hjælpe dig med at foretage yderligere fejlfinding af problemet. Dette kan medføre løse problemet eller beder dig om at deltage tilgængelige kanaler for de Åbn kilde-teknologier, hvor deep ekspertise i forbindelse med teknologien, der er fundet. For eksempel, der er mange communitywebsteder, der kan bruges, ønsker: [MSDN-forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Også Apache projekter har projektwebsteder på [http://apache.org](http://apache.org), for eksempel: [Hadoop](http://hadoop.apache.org/), [gnister](http://spark.apache.org/).

##<a name="recommendations"></a>Forstå anbefalinger

En af de funktioner, som leveres af Mahout er en anbefaling program. Dette program accepterer data i formatet af `userID`, `itemId`, og `prefValue` (indstillingen brugere for elementet). Mahout kan derefter udføre samtidig forekomst analyse til at bestemme: _brugere, der har en indstilling for et element har også en indstilling for disse andre elementer_. Mahout bestemmer derefter brugere med indstillinger i synes godt om-element, som kan bruges til at foretage anbefalinger.

Følgende er en meget simpel eksempel, der bruger film:

* __Samtidig forekomst__: Joe, Bent og Bob trykket synes godt om _stjerne krige_, _Tilbage slår ned til feltet imperium_og _returnere af Jedi_. Mahout bestemmer, at brugere der synes godt om et af disse film også som de andre to.

* __Samtidig forekomst__: Bob og Bent også synes godt om _Feltet Fantombillede truslen fra rummet_, _angreb af både_og _hævn af Sith_. Mahout bestemmer, at brugere, der synes godt om de forrige tre film også som disse tre.

* __Lighed anbefaling__: fordi Joe synes godt om de tre første film, Mahout ser på film, som andre med samme indstillinger synes godt om, men Joe ikke har set (synes godt om/klassificeret). I dette tilfælde anbefaler Mahout _Feltet Fantombillede truslen fra rummet_, _angreb af både_og _hævn af Sith_.

###<a name="understanding-the-data"></a>Forstå dataene

Når jeg [GroupLens opslag] [ movielens] giver bedømmelse data til film i et format, der er kompatibelt med Mahout. Disse data er tilgængelige på din klynge standard lagerplads på `/HdiSamples/HdiSamples/MahoutMovieData`.

Der findes to filer `moviedb.txt` (oplysninger om filmene,) og `user-ratings.txt`. Bruger ratings.txt filen og bruges under analyse, mens moviedb.txt bruges til at give brugervenlig tekst oplysninger, når viser resultaterne af analysen.

Data i bruger-ratings.txt har en struktur af `userID`, `movieID`, `userRating`, og `timestamp`, som oplyser, hvor meget hver bruger bedømt en film. Her er et eksempel på dataene:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Køre analysen

Brug følgende kommando for at køre jobbet anbefaling:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] Jobbet kan tage flere minutter at gennemføre, og kan køre flere MapReduce job.

##<a name="view-the-output"></a>Få vist output

1. Når jobbet er fuldført, kan du bruge følgende kommando til at få vist genererede output:

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    Output vises på følgende måde:

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Den første kolonne er den `userID`. De værdier, der er indeholdt i ' [' og ']' er `movieId`:`recommendationScore`.

2. Du kan bruge output, sammen med moviedb.txt, til at vise mere brugervenligt. Først skal vi brug at kopiere filerne lokalt ved hjælp af følgende kommandoer:

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Dette kopierer outputdataene til en fil med navnet **recommendations.txt** i den aktuelle mappe, sammen med film datafilerne.

3. Brug følgende kommando til at oprette et nyt Python script, der ser film navne til dataene i anbefalinger output:

        nano show_recommendations.py

    Når editoren åbnes, skal du bruge følgende som indholdet af filen:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Tryk på **Ctrl + X**, **Y**og til sidst **Enter** for at gemme dataene.

3. Brug følgende kommando for at gøre filen eksekverbare:

        chmod +x show_recommendations.py

4. Kør scriptet Python. Følgende fremgangsmåde forudsætter, at du er i den mappe, hvor alle filerne er blevet hentet:

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Dette vil se på de anbefalinger, der er oprettet for bruger-ID 4.

    * Filen **bruger ratings.txt** bruges til at hente film, som brugeren har bedømt
    * Filen **moviedb.txt** bruges til at hente navnene på filmene
    * **Recommendations.txt** bruges til at hente film anbefalinger til denne bruger

    Output fra denne kommando bliver ligner følgende:

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Slette midlertidige data

Mahout job fjerner ikke midlertidige data, der oprettes under behandling af jobbet. Den `--tempDir` parameter er angivet i eksempel jobbet til at isolere de midlertidige filer til en bestemt sti til nem sletning. Brug følgende kommando til at fjerne midlertidige filer:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Hvis du vil køre kommandoen igen, skal du også slette outputmappen. Brug følgende fremgangsmåde til at slette denne mappe:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger Mahout, opdage andre måder at arbejde med data på HDInsight:

* [Hive med HDInsight](hdinsight-use-hive.md)
* [Gris med HDInsight](hdinsight-use-pig.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
