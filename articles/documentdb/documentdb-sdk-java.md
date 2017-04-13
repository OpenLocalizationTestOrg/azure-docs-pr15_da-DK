
<properties
    pageTitle="DocumentDB Java API og SDK | Microsoft Azure"
    description="Få mere at vide om Java API og SDK, herunder release datoer, lukning af datoer og ændringer mellem hver version af DocumentDB Java SDK."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API'er og SDK'er

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTEN](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>DocumentDB Java API og SDK

<table>
<tr><td>**Henter SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**API dokumentation**</td><td>[Java API referencedokumentation](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Bidrage til SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Komme i gang**</td><td>[Introduktion til Java SDK](documentdb-java-application.md)</td></tr>
<tr><td>**Aktuelle understøttede runtime**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Produktbemærkninger

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Understøttelse af BoundedStaleness konsistens niveau tilføjet.
  - Tilføjet understøttelse af direkte forbindelse for CRUD handlinger for partitioneret af websteder.
  - Fast en fejl i forespørgsler en database med SQL.
  - Fast en fejl i sessionscachen hvor sessionstoken kan angives forkert.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Understøttelse af tilføjet på tværs af partition parallelle forespørgsler.
  - Understøttelse af TOP/ORDER BY forespørgsler til partitioneret samlinger tilføjet.
  - Understøttelse af stærke konsistens tilføjet.
  - Understøttelse af navn tilføjet baseret anmodninger, når du bruger direkte forbindelse.
  - Fast at gøre ActivityId holde ensartede på tværs af alle anmodningsforsøg.
  - Fast en fejl, der er relateret til sessionscachen, når du genopretter en samling med samme navn.
  - Tilføjede Polygon og LineString DataTypes mens angivelse af websteder indeksering politik for geografisk indhegning geografiske forespørgsler.
  - Fast problemer med Java dokument til Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Fast en fejl i PartitionKeyDefinitionMap til lagring af enkelt partition af websteder og ikke til at foretage ekstra Hent partition vigtige anmodninger.
  - Fast en fejlrapport direkte til ikke igen, når en forkert partition nøgleværdi leveres.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Tilføjet understøttelse af flere områder database konti.
  - Understøttelse af automatisk igen på begrænset anmodninger med mulighed for at tilpasse Maks forsøg forsøg og Maks forsøg ventetiden tilføjet.  Se RetryOptions og ConnectionPolicy.getRetryOptions().
  - Frarådede IPartitionResolver baseret brugerdefineret leverandør kode. Brug partitioneret af websteder til højere lager og overførselshastighed.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Tilføjede forsøg politik for understøttelse af (throttling).  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Tilføjede tid til live (TTL) support til dokumenter.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementeret [partitioneret af websteder](documentdb-partition-data.md) og [brugerdefinerede ydeevne](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Fast en fejl i HashPartitionResolver til at generere hash-værdier i små endian er konsistente med andre SDK'er.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Tilføj Hash & område partition oversættelsesfunktionerne til at hjælpe med at sharding programmer på tværs af flere partitioner.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implementere Upsert. Nye upsertXXX metoder tilføjet for at understøtte Upsert funktion.
- Implementere ID baseret Routing. Ingen offentlige API ændringer, interne alle ændringer.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- Release ignoreret til at hente versionsnummeret i justering med andre SDK'er

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Understøtter geospatiale indeks
- Validerer id-egenskaben for alle ressourcer. Id'er for de ressourcer, må ikke indeholde ?, /, #, \, tegn eller slutter med et mellemrum.
- Føjer nye sidehoved "indeks status for transformeringen" til ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementerer V2 indeksering politik

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## <a name="release--retirement-dates"></a>Udgave og lukning af datoer
Microsoft giver besked om mindst **12 måneder** før pension en SDK for at udjævne overgangen til en nyere/understøttet version.

Nye funktioner og funktionalitet og optimeringer føjes kun til den aktuelle SDK, som f.eks det er derfor anbefaler vi, at du altid har opgraderet til den nyeste SDK version så tidligt som muligt.

En anmodning om at DocumentDB ved hjælp af en udgåede SDK afvises af tjenesten.

> [AZURE.WARNING]
Alle versioner af Azure DocumentDB SDK til Java før version **1.0.0** skal udgå på **29 februar 2016**.

<br/>

| Version | Udgivelsesdato | Lukning af dato
| ---     | ---          | ---
| [1.9.1](#1.9.1) | Oktober 28 2016 |---
| [1.9.0](#1.9.0) | Oktober 03 2016 |---
| [1.8.1](#1.8.1) | Juni 30 2016 |---
| [1.8.0](#1.8.0) | Juni 14 2016 |---
| [1.7.1](#1.7.1) | April 30 2016 |---
| [1.7.0](#1.7.0) | April 27 2016 |---
| [1.6.0](#1.6.0) | Marts 29 2016 |---
| [1.5.1](#1.5.1) | 31 december 2015 |---
| [1.5.0](#1.5.0) | December 04 2015 |---
| [1.4.0](#1.4.0) | Oktober 05 2015 |---
| [1.3.0](#1.3.0) | Oktober 05 2015 |---
| [1.2.0](#1.2.0) | August 05 2015 |---
| [1.1.0](#1.1.0) | Juli 09 2015 |---
| [1.0.1](#1.0.1) | 12 maj 2015 |---
| [1.0.0](#1.0.0) | 07 april 2015 |---
| 0.9.5-prelease | Mar 09 2015 | Februar 29 2016
| 0.9.4-prelease | 17 februar 2015 | Februar 29 2016
| 0.9.3-prelease | 13 januar 2015 | Februar 29 2016
| 0.9.2-prelease | 19 december 2014 | Februar 29 2016
| 0.9.1-prelease | 19 december 2014 | Februar 29 2016
| 0.9.0-prelease | 10 december 2014 | Februar 29 2016

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Se også

Hvis du vil vide mere om DocumentDB skal du se [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) service side.
