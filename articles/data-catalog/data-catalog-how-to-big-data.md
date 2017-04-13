<properties
   pageTitle="Sådan arbejder du med 'big data' datakilder | Microsoft Azure"
   description="Vejledning til artikel fremhæve mønstre for Azure datakatalog med 'big data' datakilder, herunder Azure Blob-lager, Azure Data sø og Hadoop HDFS."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Sådan arbejder du med stor datakilder i Azure datakatalog

## <a name="introduction"></a>Introduktion
**Microsoft Azure datakatalog** er en fuldt administreret skybaseret tjeneste, der fungerer som et system af registrering og system af registrering til enterprise-datakilder. Med andre ord er **Azure datakatalog** alle om hjælp til personer opdage, forstå og bruge datakilder, og hjælp organisationer til at få flere værdi fra deres eksisterende datakilder, herunder stor data.

**Azure datakatalog** understøtter registrering af Azure Blog lager BLOB og mapper samt Hadoop HDFS filer og mapper. Disse datakilder semistrukturerede art giver stor fleksibilitet, men det betyder også, at brugerne skal overveje, hvordan datakilderne er organiseret for at få den største værdi fra registrere dem med **Azure datakatalog**.

## <a name="directories-as-logical-data-sets"></a>Kataloger som logiske datasæt

Et almindeligt mønster for organisere stor datakilder er at behandle kataloger som logiske datasæt. På øverste niveau kataloger bruges til at definere et datasæt, mens undermapper definerer partitioner, og de filer, de indeholder gemmer selve dataene.

Et eksempel på dette mønster kan være:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

I dette eksempel repræsenterer vehicle_maintenance_events og location_tracking_events logiske datasæt. Hver af disse mapper indeholder datafiler, som er organiseret efter år og måned i undermapper. Hver af disse mapper indeholde kan hundredvis eller tusindvis af filer.

I denne mønster registrering af individuelle filer med **Azure datakatalog** sandsynligvis ikke giver mening. I stedet registrere de mapper, der repræsenterer de datasæt, der skal være giver mening for de brugere, der arbejder med data.

## <a name="reference-data-files"></a>Reference-datafiler

Et komplementære mønster er at lagre reference datasæt som individuelle filer. Disse datasæt kan betragtes som "små" side af stor data, og der ofte ligner dimensioner i en analytical datamodel. Reference-datafiler indeholder poster, der bruges til at levere kontekst til flere af de datafiler, der er gemt et andet sted i lageret med stor data.

Et eksempel på dette mønster kan være:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Når en analyse eller data scientist fungerer med de data, der er indeholdt i de større directory-strukturer, kan dataene i filerne reference bruges til at give mere detaljerede oplysninger om enheder, der henvises til kun via navn eller -ID i det store datasæt.

I denne mønster mening det at registrere de enkelte reference datafiler med **Azure datakatalog**. Hver fil repræsenterer et datasæt, og hver enkelt kan kommenterede og opdaget enkeltvis.

## <a name="alternate-patterns"></a>Alternative mønstre

De mønstre, der er beskrevet ovenfor er kun to mulige måder en stor datalager kan være organiseret, men hver implementering vil være forskellige. Uanset hvordan datakilder er struktureret, når du registrerer stor datakilder med **Azure datakatalog**, fokusere på registrering af filer og mapper, der repræsenterer de datasæt, der vil være værdifulde til andre i organisationen. Registrere alle filer og mapper kan rod kataloget, hvilket gør det sværere for brugerne at finde de har brug for.

## <a name="summary"></a>Oversigt
Registrering af datakilder med **Azure datakatalog** gør dem lettere at finde og forstå. Ved at registrere og anmærkninger stor datafiler og mapper, der repræsenterer logiske datasæt, kan du hjælpe brugere med at finde og bruge de skal bruge stor datakilderne.
