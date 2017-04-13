<properties 
    pageTitle="DocumentDB Python API og SDK | Microsoft Azure" 
    description="Få mere at vide om Python API og SDK, herunder release datoer, lukning af datoer og ændringer mellem hver version af DocumentDB Python SDK." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API'er og SDK'er

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTEN](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>DocumentDB Python API og SDK

<table>
<tr><td>**Hente SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**API dokumentation**</td><td>[Python API referencedokumentation](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**SDK installationsvejledning**</td><td>[Python SDK installationsvejledning](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Bidrage til SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Komme i gang**</td><td>[Komme i gang med Python SDK](documentdb-python-application.md)</td></tr>
<tr><td>**Aktuelle understøttet platform**</td><td>[Python 2.7](https://www.python.org/downloads/) og [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Produktbemærkninger

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Understøttelse af Python 3.5 tilføjet.
- Understøttelse af gruppering af forbindelse ved hjælp af et anmodninger modul tilføjet.
- Understøttelse af session konsistens tilføjet.
- Understøttelse af TOP/SORTEREFTER forespørgsler til partitioneret samlinger tilføjet.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Tilføjede forsøg politik for understøttelse af begrænset anmodninger. (Begrænset anmodninger modtager en anmodning om rente for stor undtagelse, fejlkode 429). Som standard DocumentDB nye forsøg ni tidspunkter for hver enkelt anmodning når fejlkode 429 er stødt på, honoring retryAfter tid i svarheaderen. Gangen fast forsøg interval kan nu angives som en del af egenskaben RetryOptions for objektet ConnectionPolicy Hvis du vil ignorere den retryAfter tid, der returneres af server mellem gentagelserne. DocumentDB nu vente på op til 30 sekunder for hver enkelt anmodning, er der begrænset (uanset antal forsøg) og returnerer svaret med fejlkode 429. Denne gang kan også tilsidesættes i egenskaben RetryOptions på ConnectionPolicy objekt.

- DocumentDB returnerer nu x-ms-gas--antal forsøg og x-ms-throttle-retry-wait-time-ms som svar overskrifterne i hver anmodning om at angive begrænsning prøv igen, antal og den cummulative tid anmodningen ventet mellem gentagelserne.

- Fjernet klassen RetryPolicy og den tilsvarende egenskab (retry_policy), der vises på klassen document_client og i stedet introduceret en RetryOptions klasse udsætte egenskaben RetryOptions på ConnectionPolicy klasse, der kan bruges til at tilsidesætte nogle af standardindstillingerne prøv igen.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Tilføjet understøttelse af flere områder database konti.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Tilføjet understøttelse af tid til Live(TTL) funktion for dokumenter.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Fejlrettelser relateret til server side partitionering at tillade specialtegn partitionkey sti.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementeret [partitioneret af websteder](documentdb-partition-data.md) og [brugerdefinerede ydeevne](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Tilføj Hash & område partition oversættelsesfunktionerne til at hjælpe med at sharding programmer på tværs af flere partitioner.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementere Upsert. Nye UpsertXXX metoder tilføjet for at understøtte Upsert funktion.
- Implementere ID baseret Routing. Ingen offentlige API ændringer, interne alle ændringer.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Understøtter geospatiale indeks.
- Validerer id-egenskaben for alle ressourcer. Id'er for de ressourcer, må ikke indeholde ?, /, #, \, tegn eller slutter med et mellemrum.
- Føjer nye sidehoved "indeks status for transformeringen" til ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementerer V2 indeksering politik.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Understøtter proxyforbindelse.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK.

## <a name="release--retirement-dates"></a>Udgave og lukning af datoer
Microsoft giver besked om mindst **12 måneder** før pension en SDK for at udjævne overgangen til en nyere/understøttet version.

Nye funktioner og funktionalitet og optimeringer føjes kun til den aktuelle SDK, som f.eks det er derfor anbefaler vi, at du altid har opgraderet til den nyeste SDK version så tidligt som muligt. 

En anmodning om at DocumentDB ved hjælp af en udgåede SDK afvises af tjenesten.

> [AZURE.WARNING]
Alle versioner af Azure DocumentDB SDK for Python før version **1.0.0** skal udgå på **29 februar 2016**. 

<br/>

| Version | Udgivelsesdato | Lukning af dato 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | September 29 2016 |---
| [1.9.0](#1.9.0) | Juli 07 2016 |---
| [1.8.0](#1.8.0) | Juni 14 2016 |---
| [1.7.0](#1.7.0) | April 26 2016 |---
| [1.6.1](#1.6.1) | April 08 2016 |---
| [1.6.0](#1.6.0) | Marts 29 2016 |---
| [1.5.0](#1.5.0) | Januar 03 2016 |---
| [1.4.2](#1.4.2) | Oktober 06 2015 |---
| [1.4.1](#1.4.1) | Oktober 06 2015 |---
| [1.2.0](#1.2.0) | August 06 2015 |---
| [1.1.0](#1.1.0) | Juli 09 2015 |---
| [1.0.1](#1.0.1) | 25 maj 2015 |---
| [1.0.0](#1.0.0) | 07 april 2015 |---
| 0.9.4-prelease | 14 januar 2015 | Februar 29 2016
| 0.9.3-prelease | 09 december 2014 | Februar 29 2016
| 0.9.2-prelease | 25 november 2014 | Februar 29 2016
| 0.9.1-prelease | 23 september 2014 | Februar 29 2016
| 0.9.0-prelease | 21 august 2014 | Februar 29 2016

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Se også

Hvis du vil vide mere om DocumentDB skal du se [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) service side. 
