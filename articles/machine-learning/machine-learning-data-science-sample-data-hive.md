<properties
    pageTitle="Eksempel på data i Azure HDInsight Hive tabeller | Microsoft Azure"
    description="Ned indsamle data i Azure HDInsight (Hadopop) Hive tabeller"
    services="machine-learning,hdinsight"
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

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Eksempeldata i Azure HDInsight Hive tabeller

I denne artikel gennemgås Sådan ned stikprøver data gemt i Azure HDInsight Hive tabeller ved hjælp af Hive forespørgsler. Dækker vi tre popularly anvendte udvalg metoder:

* Ensartet stikprøver
* Stikprøver efter grupper
* Stratified udvalg

**Hvorfor Lyt til dine data?**
Hvis du planlægger at analysere datasættet er stort, er det som regel er en god ide at ned stikprøver data for at reducere den til en mindre, men repræsentant og mere håndterbare størrelse. Dette gør det nemmere at forstå data, udforske og funktion teknisk. Dens rolle i Team Data videnskabelige processen er at aktivere hurtig prototyper af databehandlingsfunktioner og machine learning-modeller.

I **menuen** under links til emner, der beskriver, hvordan Indsaml data fra forskellige lagerplads miljøer.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Denne stikprøver, hvor opgaven er et trin i [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Hvordan du sender Hive forespørgsler
Hive forespørgsler kan sendes fra kommandolinjen Hadoop-konsollen på noden hoved af Hadoop-klynge. For at gøre dette, log på noden hoved af Hadoop-klynge, åbne konsollen Hadoop kommandolinjen og sende forespørgslerne Hive derfra. Finde en vejledning i at sende Hive forespørgsler i konsollen Hadoop kommandolinjen, se, [hvordan du sender Hive forespørgsler](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Ensartet stikprøver
Ensartet stikprøver betyder, at hver række i datasættet har en lig risikoen for, at der tages prøver fra. Dette kan implementeres ved at tilføje en ekstra felt funktionerne rand() til datasættet i indre "select" forespørgslen og i forespørgslen ydre "select" pågældende betingelse på det pågældende tilfældigt felt.

Her er et eksempel på forespørgsel:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Her `<sample rate, 0-1>` angiver del af poster, som brugerne vil indsamle.

## <a name="group"></a>Stikprøver efter grupper

Når udvalg kategoriske data, kan du enten at medtage eller udelade alle forekomster af en bestemt værdi af en kategoriske variabel. Dette er, hvad er meningen ved "stikprøver efter gruppe".
Eksempelvis hvis du har en kategoriske variabel "Stater", som har værdier NY, glidende gennemsnit, CA, NJ, PA osv, du vil poster i den samme stat er altid sammen, om de er stikprøver eller ej.

Her er et eksempel på forespørgsel, der prøver efter gruppe:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Stratified udvalg

Stikprøver er stratified i forhold til en kategoriske variabel når de indsamlede har værdier for, at kategoriske, der er i det samme størrelsesforhold som overordnede populationen, er der fås eksemplerne. Ved hjælp af det samme eksempel som ovenfor, Antag, at dine data har underordnede populationer ved stater, sig NJ har 100 observationer, NY har 60 observationer og WA har 300 observationer. Hvis du angiver rente stratified stikprøver, hvor der skal være 0,5, skal bede der fås stikprøvernes cirka 50, 30 og 150 observationer af NJ, NY og WA henholdsvis.

Her er et eksempel på forespørgsel:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Du kan finde oplysninger om mere avancerede udvalg metoder, der er tilgængelige i Hive [LanguageManual udvalg](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
