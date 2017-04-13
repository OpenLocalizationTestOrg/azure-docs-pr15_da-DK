<properties
    pageTitle="Udforske data i Hive tabeller med Hive forespørgsler | Microsoft Azure"
    description="Udforske data i Hive tabeller ved hjælp af Hive forespørgsler."
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
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Udforske data i Hive tabeller med Hive forespørgsler

Dette dokument indeholder eksempel Hive-scripts, som bruges til at udforske data i Hive tabeller i en HDInsight Hadoop-klynge.

Følgende **menuen** links til emner, der beskriver, hvordan du bruger værktøjer til at udforske data fra forskellige lagerplads miljøer.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har:

* Oprettet en Azure-lager-konto. Hvis du har brug for instruktioner, se [oprette en Azure-lager-konto](../storage/storage-create-storage-account.md#create-a-storage-account)
* Klargjort en tilpasset Hadoop klynge med tjenesten HDInsight. Hvis du har brug for instruktioner, kan du se [Tilpasse Azure HDInsight Hadoop klynger for avancerede analyser](machine-learning-data-science-customize-hadoop-cluster.md).
* Dataene er blevet overført til Hive tabeller i Azure HDInsight Hadoop klynger. Hvis det ikke er tilfældet, skal du følge vejledningen i [oprette og Indlæs data til Hive tabeller](machine-learning-data-science-move-hive-tables.md) for at overføre data til Hive tabeller først.
* Aktiveret fjernadgang til klyngen. Hvis du har brug for instruktioner, kan du se [få adgang til den i afsnit af Hadoop klynge noder](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Hvis du har brug for oplysninger om, hvordan du sender Hive forespørgsler, se, [hvordan du sender Hive forespørgsler](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Eksempel Hive forespørgsel scripts til undersøgelse af data

1. Få antallet af observationer per partition `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Få antallet af observationer dagen `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Få niveauer i en kategoriske kolonne  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Få antallet niveauer i kombination af to kategoriske kolonner `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Få en fordeling for numeriske kolonner  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Udtrække poster fra deltagelse i to tabeller

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Yderligere forespørgsel scripts for taxi forretningsrejse data scenarier

Eksempler på forespørgsler, der er specifikke for [NYC Taxi forretningsrejse Data](http://chriswhong.com/open-data/foil_nyc_taxi/) scenarier findes også i [Github lager](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Disse forespørgsler allerede har data skema angivet og er klar til at blive sendt til at køre.
