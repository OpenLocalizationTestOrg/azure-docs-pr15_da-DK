<properties
pageTitle="Bruge DataFu med gris på HDInsight"
description="DataFu er en samling af biblioteker til brug sammen med Hadoop. Få mere at vide, hvordan du kan bruge DataFu med gris på din HDInsight klynge."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Bruge DataFu med gris på HDInsight

DataFu er en samling af Open Source biblioteker til brug sammen med Hadoop. I dette dokument lærer du, hvordan du bruger DataFu på din HDInsight klynge, og hvordan du bruger DataFu bruger defineret funktioner (UDF) med gris.

##<a name="prerequisites"></a>Forudsætninger

* Et Azure-abonnement.

* En Azure HDInsight klynge (Linux eller Windows-baseret)

* Et grundlæggende kendskab til [ved hjælp af gris på HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Installere DataFu på Linux-baserede HDInsight

> [AZURE.NOTE] DataFu er installeret på Linux-baserede klynger version 3.3 eller nyere, og på Windows-baserede klynger. Det er ikke installeret på Linux-baserede klynger tidligere end 3.3.
>
> Hvis du bruger en Linux-baserede klynge version 3.3 eller nyere eller en Windows-baseret klynge, kan du springe dette afsnit.

DataFu kan hentes og installeres fra Maven lager. Brug følgende trin til at føje DataFu til din HDInsight klynge:

1. Oprette forbindelse til din Linux-baserede HDInsight klynge, ved hjælp af SSH. Du kan finde flere oplysninger om brug af SSH med HDInsight du se en af følgende dokumenter:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X og Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Brug følgende kommando til at hente filen DataFu glas, ved hjælp af værktøjet wget eller Kopiér og Indsæt linket i din browser for at starte overførslen.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Derefter skal du overføre filen til standard lagerplads på din HDInsight klynge. Det gør filen tilgængelig for alle noder i klyngen, og filen forbliver i lagerplads, selvom du slette og gendanne klyngen.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] Ovenstående eksempel gemmer glas i `wasbs:///example/jars` da denne mappe, der allerede findes på den klynge lagerplads. Du kan bruge enhver placering, du vil på HDInsight klynge lagerplads.

##<a name="use-datafu-with-pig"></a>Bruge DataFu med gris

Trinnene i dette afsnit antager, at du er vant til at bruge gris på HDInsight og giver kun gris latinsk sætninger, ikke trin til at bruge dem med klyngen. Du kan finde flere oplysninger om brug af gris med HDInsight, [Brug gris med HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Når du bruger DataFu fra gris på en Linux-baserede HDInsight klynge, skal du først registrere glas filen ved hjælp af følgende gris latinsk-sætning:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu er registreret som standard på Windows-baseret HDInsight klynger.

Du kan normalt definerer et alias for DataFu funktioner. Eksempel:

    DEFINE SHA datafu.pig.hash.SHA();
    
Dette definerer et alias navngivne `SHA` for SHA krypteres funktionen. Du kan derefter bruge dette i et gris latinsk script til at oprette en hash for inputdataene. For eksempel erstatter følgende navnene i inputdataene med en hashværdi:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Hvis denne funktion bruges med følgende input data:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
I følgende eksempel oprettes:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om DataFu eller gris følgende dokumenter:

* [Apache DataFu gris vejledning](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Brug gris med HDInsight](hdinsight-use-pig.md)
