<properties
    pageTitle="Oprette funktioner til data i et Hadoop-klynge, ved hjælp af Hive forespørgsler | Microsoft Azure"
    description="Eksempler på Hive-forespørgsler, der genererer funktioner i data, der er gemt i en Azure HDInsight Hadoop-klynge."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Oprette funktioner til data i et Hadoop-klynge, ved hjælp af Hive forespørgsler

Dette dokument viser, hvordan du opretter funktioner til data, der er gemt i en Azure HDInsight Hadoop-klynge, ved hjælp af Hive forespørgsler. Disse Hive forespørgsler brug integrerede Hive brugerdefineret funktioner, scripts, som leveres.

De handlinger, der bruges til at oprette funktioner kan være hukommelse intensivt. Ydeevnen for Hive forespørgsler bliver mere afgørende i så fald og kan forbedres ved justering af visse parametre. Justering af disse parametre er beskrevet i afsnittet færdig.

Eksempler på de forespørgsler, der præsenteres er specifikke for [NYC Taxi forretningsrejse Data](http://chriswhong.com/open-data/foil_nyc_taxi/) scenarier findes også i [Github lager](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Disse forespørgsler allerede har data skema angivet og er klar til at blive sendt til at køre. I sektionen endelige gennemgås også parametre, som brugere kan finjustere, så du kan forbedre ydeevnen af Hive forespørgsler.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]I denne **menu** links til emner, der beskriver, hvordan du opretter funktioner til data i forskellige miljøer. Denne opgave er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har:

* Oprettet en Azure-lager-konto. Hvis du har brug for instruktioner, se [oprette en Azure-lager-konto](../storage/storage-create-storage-account.md#create-a-storage-account)
* Klargjort en tilpasset Hadoop klynge med tjenesten HDInsight.  Hvis du har brug for instruktioner, kan du se [Tilpasse Azure HDInsight Hadoop klynger for avancerede analyser](machine-learning-data-science-customize-hadoop-cluster.md).
* Dataene er blevet overført til Hive tabeller i Azure HDInsight Hadoop klynger. Hvis det ikke er tilfældet, skal du følge [Opret og Indlæs data til Hive tabeller](machine-learning-data-science-move-hive-tables.md) for at overføre data til Hive tabeller først.
* Aktiveret fjernadgang til klyngen. Hvis du har brug for instruktioner, kan du se [få adgang til den i afsnit af Hadoop klynge noder](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Generering af funktion

I dette afsnit beskrives flere eksempler på de måder, hvori funktioner kan genererende ved hjælp af Hive forespørgsler. Når du har oprettet flere funktioner, kan du tilføje dem som kolonner til den eksisterende tabel eller oprette en ny tabel med yderligere funktioner og primær nøgle, der kan sammenkædes med den oprindelige tabel. Her er eksemplerne:

1. [Frekvens baseret generering af funktion](#hive-frequencyfeature)
2. [Risici kategoriske variabler i binær klassifikation](#hive-riskfeature)
3. [Udtrække funktioner fra Datetime-Field](#hive-datefeatures)
4. [Udtrække funktioner fra tekstfelt](#hive-textfeatures)
5. [Beregne afstanden mellem GPS-koordinaterne](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Frekvens baseret generering af funktion

Det er ofte nyttigt til at beregne hyppigheden af niveauer af en kategoriske variabel eller hyppighed af visse kombinationer af niveauer fra flere kategoriske variabler. Brugere kan bruge følgende script til at beregne disse hyppighed:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Risici kategoriske variabler i binær klassifikation

I binær klassificering, har vi brug at konvertere ikke-numerisk kategoriske variabler til numeriske funktioner, når anvendes kun modeller tager numeriske funktioner. Det gør du ved at erstatte alle ikke-numerisk niveauer med en numerisk risiko. I dette afsnit viser vi nogle generisk Hive-forespørgsler, der beregner risikoen værdier (log sandsynligheden) af en kategoriske variabel.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

I dette eksempel variabler `smooth_param1` og `smooth_param2` er angivet til at udjævne risikoen værdierne beregnes ud fra dataene. Risici, som har et interval mellem -Inf og Inf. En risici > 0 angiver, at sandsynligheden for, at destinationen er lig med 1 er større end 0,5.

Efter risikoen beregnes tabel brugere kan tildele risikoen værdier til en tabel ved at sammenkæde det med tabellen risikoen. Forespørgslen Hive tilslutter blev angivet i forrige afsnit.

###<a name="hive-datefeatures"></a>Udtrække funktioner fra Datetime-Fields

Hive leveres med et sæt af brugerdefinerede funktioner til at behandle datetime-felter. I Hive, standardformatet for dato/klokkeslæt er ' åååå-MM-dd 00:00:00 "(" 1970-01-01 12:21:32 ' for eksempel). I dette afsnit viser vi eksempler, der udtrækkes dagen i måneden, måneden fra en dato / klokkeslætsfelt, og andre eksempler, der konverterer en dato/klokkeslæt-streng i et format end standardformatet til en dato/klokkeslæt-streng i formatere.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Denne Hive forespørgslen antages, at den *& #60; datetime-felt >* er i standardformatet for dato/klokkeslæt.

Hvis et dato / klokkeslætsfelt ikke er i standardformatet, skal du konvertere feltet Dato/klokkeslæt til Unix tidsstempel først, og Konverter derefter tidsstempel Unix til en dato/klokkeslæt-streng, der er i standardformatet. Når datetime er i format, kan brugerne anvende den integrerede datetime brugerdefinerede funktioner til at uddrage funktioner.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

I denne forespørgsel, hvis den *& #60; datetime-felt >* har mønster som *03-26-2015 12:04:39*, *' & #60; anvendelse af feltet Dato/klokkeslæt >'* skal være `'MM/dd/yyyy HH:mm:ss'`. For at teste den, kan brugere køre

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* i denne forespørgsel er forudinstalleret på alle Azure HDInsight Hadoop klynger som standard når klynger er klargjort.


###<a name="hive-textfeatures"></a>Udtrække funktioner fra tekstfelter

Når Hive-tabellen har et tekstfelt, der indeholder en streng med ord, der er adskilt af mellemrum, henter følgende forespørgsel længden af strengen og antallet af ord i strengen.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Beregne afstand mellem sæt af GPS-koordinater

Den forespørgsel, der er angivet i dette afsnit kan anvendes direkte til NYC Taxi forretningsrejse Data. Formålet med denne forespørgsel er at se, hvordan til at anvende en integreret matematiske funktioner i Hive til at generere funktioner.

De felter, der bruges i denne forespørgsel er GPS-koordinaterne for afhentning og dropoff placeringer, der hedder *afhentning\_breddegrader*, *afhentning\_længde*, *dropoff\_breddegrader*, og *dropoff\_længde*. De forespørgsler, der beregner direkte afstanden mellem afhentning og dropoff koordinaterne er:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Matematiske ligninger, der beregner afstanden mellem to GPS-koordinaterne kan findes på webstedet <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Flytbar Type Scripts</a> , skrevet af Peter Lapisu. I sin Javascript, funktionen `toRad()` er lige *lat_or_lon*pi/180*, der konverterer grader til radianer. Her *lat_or_lon * er længde eller længdegrader. Da Hive ikke giver funktionen `atan2`, men giver funktionen `atan`, `atan2` funktionen er implementeret af `atan` -funktionen i den ovenstående Hive-forespørgsel ved hjælp af den definition, der er angivet på <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Opret arbejdsområde](./media/machine-learning-data-science-create-features-hive/atan2new.png)

En komplet liste over Hive integrerede brugerdefinerede funktioner kan findes i afsnittet **Indbyggede funktioner** på <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Avancerede emner: finjustere Hive parametre for at forbedre hastigheden i forespørgslen

Parameter standardindstillingerne af Hive klynge muligvis ikke passer til Hive-forespørgsler og de data, der behandler forespørgsler. I dette afsnit beskriver vi nogle parametre, som brugere kan finjustere, forbedrer ydeevnen for Hive forespørgsler. Brugere skal tilføje parameteren justering af forespørgsler, før forespørgsler for behandling af data.

1. **Java heap mellemrum**: For forespørgsler, der involverer deltagelse i store datasæt eller behandling lange poster, **løbe tør for plads heap** er et af de almindelige fejl. Det kan være indstillet til ved at indstille parametre *mapreduce.map.java.opts* og *mapreduce.task.io.sort.mb* til ønskede værdier. Her er et eksempel:

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Denne parameter allokerer 4GB hukommelse til Java heap linjeafstand og også gør sortering mere effektiv ved at tildele mere hukommelse til den. Det er en god ide at lege med disse tildelinger, hvis der ikke er et job mislykkedes fejl, der er relateret til heap mellemrum.

2. **DFS blokere størrelse** : denne parameter angiver den mindste enhed af data, der gemmer filsystemet. Som et eksempel, hvis DFS blokstørrelse er 128MB, vil dataene, der er angivet af størrelse mindre end og op til er 128MB gemt i en enkelt blok, mens du data, som er større end 128MB tildeles ekstra dokumentkomponenter. Vælge en meget lille blokere størrelse medfører store omkostninger i Hadoop, da noden navn har mange flere anmodninger til at finde relevante blokering for gruppepolitikken filen skal behandles. En anbefalede indstilling, når omhandler de gigabyte (eller større) data er:

        set dfs.block.size=128m;

3. **Optimizing deltage i handlingen i Hive** : mens joinhandlinger i kort/reducere framework tager typisk sted i reducere fasen, nogle gange optager enormt meget plads gevinst kan der opnås ved at planlægge joinforbindelser i fasen kort (også kaldet "mapjoins"). For at dirigere Hive du gør dette, hvis det er muligt, kan vi angive:

        set hive.auto.convert.join=true;

4. **Angive antallet af mappers til hive-filen** : mens Hadoop gør det muligt at angive antallet reducers, antallet af mappers er typisk ikke angives af brugeren. Et stik, der gør det muligt for en vis grad Kontrolelementtypen i dette nummer er at vælge Hadoop variabler, *mapred.min.split.size* og *mapred.max.split.size* som størrelsen af hver enkelt opgave kort bestemmes af:

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    Typisk er af standardværdien for *mapred.min.split.size* 0, som *mapred.max.split.size* er **Long.MAX** og, *dfs.block.size* er 64 MB. Som vi kan se, givet datastørrelsen justering af disse parametre ved hjælp af "indstillingen" dem giver os mulighed at finjustere antallet mappers bruges.

5. Et par andre mere **Avancerede indstillinger** til optimering af ydeevne Hive er nævnt nedenfor. Disse kan du angive den hukommelse, der er allokeret for at tilknytte og reducere opgaver og kan være nyttige i tilpasse ydeevne. Skal du huske på, at *mapreduce.reduce.memory.mb* ikke være større end den fysiske hukommelsesstørrelse for hver arbejder node i Hadoop-klynge.

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
