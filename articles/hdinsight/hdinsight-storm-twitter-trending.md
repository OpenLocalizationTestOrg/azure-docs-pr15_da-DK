<properties
   pageTitle="Twitter-cirkulerer emner med Apache Storm på HDInsight | Microsoft Azure"
   description="Lær at bruge Trident til at oprette en Apache Storm søgetopologien, der bestemmer cirkulerer emner på Twitter baseret på hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Bestemme Twitter cirkulerer emner med Apache Storm på HDInsight

Lær at bruge Trident til at oprette en Storm topologi, der bestemmer cirkulerer emner (hash mærker) på Twitter.

Trident er en overordnet fremstilling, der indeholder funktioner, som joinforbindelser, sammenlægninger, gruppering, funktioner og filtre. Desuden tilføjes Trident primitiver for at gøre med høj sikkerhed, trinvis behandling. I dette eksempel vises, hvordan du kan oprette en topologi for brug af en brugerdefineret tud, funktionen og flere indbyggede funktioner, der leveres af Trident.

> [AZURE.NOTE] I dette eksempel er meget baseret på eksemplet [Trident Storm](https://github.com/jalonsoramos/trident-storm) ved Juan Alonso.

##<a name="requirements"></a>Krav

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java og JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Ciffer</a>

* En Twitter udvikler konto

##<a name="download-the-project"></a>Hente projektet

Brug følgende kode til at klone projektet lokalt.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topologi

Topologien i dette eksempel er som følger:

![topologi](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Dette er en forenklet visning af topologien. Flere forekomster af komponenterne fordeles på tværs af knuderne i klyngen.

Trident koden, der implementerer topologien er som følger:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Denne kode gør følgende:

1. Opretter en ny stream fra tud. Tud henter tweets fra Twitter og filtrerer dem til bestemte nøgleord (Kærlighed, musik og kaffe i dette eksempel).

2. HashtagExtractor, en brugerdefineret funktion, der bruges til at udtrække hash mærker fra hver tweet. Disse er udsendt til strømmen.

3. Strømmen er grupperet efter hash-mærke, og overføres til en aggregator. Denne aggregator opretter et antal hvor mange gange hver hash-kode er opstået. Disse data bevares i hukommelsen. Til sidst udsendes en ny stream, der indeholder mærket hash og antallet.

4. Da vi er kun interesseret i de mest populære hash mærker for en given mængde tweets, anvendes samlingen **FirstN** for at vende tilbage kun de øverste 10 værdier baseret på feltet Antal.

> [AZURE.NOTE] End tud og HashtagExtractor bruger vi indbyggede Trident funktionalitet.
>
> Du kan finde oplysninger om indbyggede operationer <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">pakke storm.trident.operation.builtin</a>.
>
> For Trident-tilstand installationer end MemoryMapState, se følgende:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Storm Trident elastiske søgning</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">Trident redis</a>

###<a name="the-spout"></a>Tud

Tud, **TwitterSpout**, bruger <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> til at hente tweets fra Twitter. Der oprettes et filter (Kærlighed, musik og kaffe i dette eksempel), og de indgående tweets (status), der svarer til filteret er gemt i en sammenkædet blokering kø. (Du kan finde flere oplysninger, se <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Klasse LinkedBlockingQueue</a>). Til sidst skal elementer, der er trukket fra køen og udledes til topologien.

###<a name="the-hashtagextractor"></a>HashtagExtractor

Hvis du vil udtrække hash mærker, bruges <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> til at hente alle hash mærker, der er indeholdt i tweet. Disse er derefter udledes til strømmen.

##<a name="enable-twitter"></a>Aktivere Twitter

Brug følgende trin til at registrere et nyt Twitter-program, og få forbruger- og access token oplysningerne bruges til at læse fra Twitter:

1. Gå til <a href="https://apps.twitter.com" target="_blank">Twitter-Apps</a> , og klik på knappen **Opret ny app** . Når udfylde formularen, **Tilbagekald URL-adressen** Lad feltet være tomt.

2. Når appen er oprettet, skal du klikke på fanen **nøgler og Access Tokens** .

3. Kopiér oplysningerne **Consumer nøgle** og **Forbrugere hemmeligt** .

4. Nederst på siden skal du markere **Opret min adgangstoken** , hvis der findes ingen tokens. Kopiér oplysningerne **Access Token** og **Access Token hemmeligt** , når tokens, der er oprettet.

5. Åbn filen **resources/twitter4j.properties** i det **TwitterSpoutTopology** projekt, du tidligere har klonet, Tilføj de oplysninger, du har indsamlet i de forrige trin, og Gem derefter filen.

##<a name="build-the-topology"></a>Opbygge topologien

Bruge følgende kode til at oprette projektet:

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Teste topologien

Brug følgende kommando for at teste topologien lokalt:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Når topologien begynder, skal du se afsnittet fejlfindingsoplysninger, der indeholder giver resultat mærker og tæller emitterede ved topologien. Output skal se ud som følger:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Næste trin

Nu, hvor du har kontrolleret topologien lokalt, se, hvordan du kan installere topologien: [Implementer og administrere Apache Storm topologier på HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Du kan også være interesseret i Storm følgende emner:

* [Udvikle Java topologier for Storm på HDInsight ved hjælp af Maven](hdinsight-storm-develop-java-topology.md)

* [Udvikle C# topologier for Storm på HDInsight ved hjælp af Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Yderligere Storm eksempler for HDinsight:

* [Eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md)
