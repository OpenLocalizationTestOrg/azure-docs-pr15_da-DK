<properties
    pageTitle="DocumentDB Node.js API og SDK | Microsoft Azure"
    description="Få mere at vide om Node.js API og SDK, herunder release datoer, lukning af datoer og ændringer mellem hver version af DocumentDB Node.js SDK."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB API'er og SDK'er

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTEN](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>DocumentDB Node.js API og SDK

<table>
<tr><td>**Hente SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**API dokumentation**</td><td>[Node.js API referencedokumentation](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**SDK installationsvejledning**</td><td>[Installationsvejledning](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Bidrage til SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Eksempler**</td><td>[Node.js kodeeksempler](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Hent Introduktion selvstudium**</td><td>[Komme i gang med Node.js SDK](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Web app selvstudium**</td><td>[Opbygge et Node.js webprogram ved hjælp af DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Aktuelle understøttet platform**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Produktbemærkninger

###<a name="1.10.0"/>1.10.0</a>

- Understøttelse af tilføjet på tværs af partition parallelle forespørgsler.
- Understøttelse af TOP/ORDER BY forespørgsler til partitioneret samlinger tilføjet.

###<a name="1.9.0"/>1.9.0</a>

- Tilføjede forsøg politik for understøttelse af begrænset anmodninger. (Begrænset anmodninger modtager en anmodning om rente for stor undtagelse, fejlkode 429). Som standard DocumentDB nye forsøg ni tidspunkter for hver enkelt anmodning når fejlkode 429 er stødt på, honoring retryAfter tid i svarheaderen. Gangen fast forsøg interval kan nu angives som en del af egenskaben RetryOptions for objektet ConnectionPolicy Hvis du vil ignorere den retryAfter tid, der returneres af server mellem gentagelserne. DocumentDB nu vente på op til 30 sekunder for hver enkelt anmodning, er der begrænset (uanset antal forsøg) og returnerer svaret med fejlkode 429. Denne gang kan også tilsidesættes i egenskaben RetryOptions på ConnectionPolicy objekt.

- DocumentDB returnerer nu x-ms-gas--antal forsøg og x-ms-throttle-retry-wait-time-ms som svar overskrifterne i hver anmodning om at angive begrænsning prøv igen, antal og den cummulative tid anmodningen ventet mellem gentagelserne.

- Klassen RetryOptions blev tilføjet, udsætte egenskaben på RetryOptions i klassen ConnectionPolicy, som kan bruges til at tilsidesætte nogle af standardindstillingerne prøv igen.

###<a name="1.8.0"/>1.8.0</a>

 - Tilføjet understøttelse af flere områder database konti.

###<a name="1.7.0"/>1.7.0</a>

- Tilføjet understøttelse af tid til Live(TTL) funktion for dokumenter.

###<a name="1.6.0"/>1.6.0</a>

- Implementeret [partitioneret af websteder](documentdb-partition-data.md) og [brugerdefinerede ydeevne](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Fast RangePartitionResolver.resolveForRead fejl, hvor det ikke returnere links på grund af en forkert Sammenkæd over resultater.

###<a name="1.5.5"/>1.5.5</a>

- Fast hashParitionResolver resolveForRead(): når ingen partition nøglen aktiverende undtagelse i stedet for at returnere en liste over alle registrerede links.

###<a name="1.5.4"/>1.5.4</a>

- Løsninger udstede [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - dedikeret HTTPS Agent: Undlad at ændre den globale agent til DocumentDB formål. Brug en dedikeret agent for alle de bibliotek anmodninger.

###<a name="1.5.3"/>1.5.3</a>

- Løsninger udstede [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - korrekt håndtaget stiplet i medier id'er.

###<a name="1.5.2"/>1.5.2</a>

- Løsninger udstede [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter lytteren trænger advarsel.

###<a name="1.5.1"/>1.5.1</a>

- Løsninger udstede [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - Omdøb mappe Hash til hash til bogstaver systemer.

### <a name="1.5.0"/>1.5.0</a>

- Implementere sharding support ved at tilføje hash & område partition oversættelsesfunktionerne.

### <a name="1.4.0"/>1.4.0</a>

- Implementere Upsert. Nye upsertXXX metoder på documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Ignoreret for at få version tal i overensstemmelse med andre SDK'er.

### <a name="1.2.2"/>1.2.2</a>

- Opdel Q vil medføre slikpapir til nye lager.
- Opdater til pakkefilen for npm registreringsdatabasen.

### <a name="1.2.1"/>1.2.1</a>

- Implementerer ID baseret Routing.
- Løser problemet [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - aktuelle egenskaben er i konflikt med metode current().

### <a name="1.2.0"/>1.2.0</a>

- Understøttelse af geospatiale indeks tilføjet.
- Validerer id-egenskaben for alle ressourcer. Id'er for de ressourcer, må ikke indeholde?, /, #, & #47; & #47; tegn eller slutter med et mellemrum.
- Føjer nye sidehoved "indeks status for transformeringen" til ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementerer V2 indeksering politik.

### <a name="1.0.3"/>1.0.3</a>

- Problem [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implementeret eslint og grunt konfigurationer i core og løftet SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problem- [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - bekræfter slikpapir omfatter ikke sidehoved med fejl.

### <a name="1.0.1"/>1.0.1</a>

- Implementeret mulighed for at forespørgsel for konflikter ved at tilføje readConflicts, readConflictAsync og queryConflicts.
- Opdaterede API dokumentation.
- Udsted [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync fejl.

### <a name="1.0.0"/>1.0.0</a>

- GA SDK.

## <a name="release--retirement-dates"></a>Udgave og lukning af datoer
Microsoft giver besked om mindst **12 måneder** før pension en SDK for at udjævne overgangen til en nyere/understøttet version.

Nye funktioner og funktionalitet og optimeringer føjes kun til den aktuelle SDK, som f.eks det er derfor anbefaler vi, at du altid har opgraderet til den nyeste SDK version så tidligt som muligt.

En anmodning om at DocumentDB ved hjælp af en udgåede SDK afvises af tjenesten.

> [AZURE.WARNING]
Alle versioner af Azure DocumentDB SDK for Node.js før version **1.0.0** skal udgå på **29 februar 2016**.

<br/>

| Version | Udgivelsesdato | Lukning af dato
| ---     | ---          | ---
| [1.10.0](#1.10.0) | Oktober 03 2016 |---
| [1.9.0](#1.9.0) | Juli 07 2016 |---
| [1.8.0](#1.8.0) | Juni 14 2016 |---
| [1.7.0](#1.7.0) | April 26 2016 |---
| [1.6.0](#1.6.0) | Marts 29 2016 |---
| [1.5.6](#1.5.6) | Marts 08 2016 |---
| [1.5.5](#1.5.5) | Februar 02 2016 |---
| [1.5.4](#1.5.4) | Februar 01 2016 |---
| [1.5.2](#1.5.2) | Januar 26 2016 |---
| [1.5.2](#1.5.2) | Januar 22 2016 |---
| [1.5.1](#1.5.1) | Januar 4 2016 |---
| [1.5.0](#1.5.0) | 31 december 2015 |---
| [1.4.0](#1.4.0) | Oktober 06 2015 |---
| [1.3.0](#1.3.0) | Oktober 06 2015 |---
| [1.2.2](#1.2.2) | 10 september 2015 |---
| [1.2.1](#1.2.1) | August 15 2015 |---
| [1.2.0](#1.2.0) | August 05 2015 |---
| [1.1.0](#1.1.0) | Juli 09 2015 |---
| [1.0.3](#1.0.3) | Juni 04 2015 |---
| [1.0.2](#1.0.2) | 23 maj 2015 |---
| [1.0.1](#1.0.1) | 15 maj 2015 |---
| [1.0.0](#1.0.0) | 08 april 2015 |---
| 0.9.4-prerelease | 06 april 2015 | Februar 29 2016
| 0.9.3-prerelease | 14 januar 2015 | Februar 29 2016
| 0.9.2-prerelease | 18 december 2014 | Februar 29 2016
| 0.9.1-prerelease | 22 august 2014 | Februar 29 2016
| 0.9.0-prerelease | 21 august 2014 | Februar 29 2016


## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Se også

Hvis du vil vide mere om DocumentDB skal du se [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) service side.
