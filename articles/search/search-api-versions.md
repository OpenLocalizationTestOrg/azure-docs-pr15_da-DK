<properties
   pageTitle="API version af Azure søgetjenesten | Microsoft Azure | Søge-API"
   description="Version politik for Azure Search REST API'er og biblioteket klient i .NET SDK."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>API versioner i Azure Søg

Azure Søg ruller ud funktionsopdateringer jævnligt. Nogle gange, men ikke altid kræver disse opdateringer os til at publicere en ny version af vores API for at bevare bagudkompatibilitet. Udgive en ny version, kan du kontrollere, hvornår og hvordan du integrerer Søg service-opdateringer i din kode.

Vi forsøger at publicere versioner kun, når det er nødvendigt, da det kan omfatte nogle forsøg på at opgradere din kode for at bruge en ny API-version som en regel. Vi kan kun publicere en ny version, hvis vi har brug at ændre nogle aspekter af API på en måde, der opdeler bagudkompatibilitet. Dette kan ske på grund af løsninger på eksisterende funktioner eller på grund af nye funktioner, der har ændret eksisterende API surface område.

Vi skal du følge den samme regel SDK opdateringer. Azure Søg SDK følger reglerne, [semantisk versionsstyring](http://semver.org/) , hvilket betyder, at versionen består af tre dele: vigtige underordnet og build-nummer (for eksempel 1.1.0). Vi udsender en ny overordnet version af SDK kun i tilfælde af ændringer, bryde bagudkompatibilitet. Vi vil øge den underordnede version hårde funktionsopdateringer, og for fejlrettelser, vi kun øger build-version.

##<a name="snapshot-of-current-versions"></a>Øjebliksbillede af aktuelle versioner 

Nedenfor, et øjebliksbillede af de aktuelle versioner af alle du programming interface til Azure søgning. Vejvisere og andre oplysninger, som kan findes i de efterfølgende afsnit i dette dokument.

Grænseflader|Seneste overordnet version|Status
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Generelt tilgængelig, udgivet februar 2016
[.NET SDK Preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|2.0-eksempel|Eksempel på udgivet August 2016
[Tjenesten REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Alment tilgængelig
[Tjenesten RESTEN API Preview](search-api-2015-02-28-preview.md)|2015-02-28-eksempel|Vis udskrift
[Administration af REST-API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Alment tilgængelig

For REST API'er, herunder den `api-version` på hver opkald er påkrævet. Dette gør det nemt at målrette en bestemt version, som et eksempel API. Følgende eksempel illustrerer, hvordan den `api-version` parameter er angivet:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Selvom hver anmodning har en `api-version`, anbefales det, at du bruger den samme version som for alle API anmodninger. Dette er tilfældet, når nye API versioner introducere attributter eller handlinger, der ikke genkendes i tidligere versioner. Blanding API versioner kan have utilsigtede konsekvenser og undgås.
> 
Tjenesten REST-API og administration af REST-API er versionsnummer uafhængigt af hinanden. En hvilken som helst lighed version tallene er samtidig hændelige skader.

Alment tilgængelig (eller GA) API'er kan bruges i fremstilling og er underlagt Azure serviceaftaler. Vis versioner har forsøg funktioner, der ikke overføres altid til en GA version. **Vi anbefaler mod ved hjælp af Vis API'er i fremstilling programmer.**

##<a name="sdk-version-roadmap"></a>SDK version vejledning

Hver version af .NET SDK er beregnet til en bestemt version af tjenesten REST-API. Funktioner, der er implementeret i REST-API først, og derefter implementeret i SDK.

.NET SDK er nu alment tilgængelig og arbejde er allerede foregår på den næste version. Den følgende tabel ser frem til fremtidige versioner af SDK, så du har en ide om, hvad der kommer op næste.

.NET SDK version|REST-API version|Funktioner|ETA
----------------|----------------|--------|---
1.1|2015-02-28|Lucene forespørgselssyntaksen|Februar 2016
2.0-eksempel|2015-02-28-eksempel|Brugerdefinerede programmer til analyse, Azure Blob og tabel indeks, tilknytninger, ETags|August 2016
2.x|Ny GA API-version|Samme som 2.0-eksempel|Tidlig K4 2016

##<a name="about-preview-and-generally-available-versions"></a>Om Preview og alment tilgængelig versioner

Azure Søg versioner altid udfyldt forsøg funktioner gennem REST-API først, derefter gennem foreløbige versioner af .NET SDK.

Funktionerne Preview er ingen garanti for overføres til en GA version. Mens funktioner i en GA version betragtes som stabil og uændret med undtagelse af små bagudkompatible løsninger og forbedringer, er preview funktioner tilgængelige for at teste og forsøg med formålet med indsamling af feedback om funktion design og implementering. 

Men fordi preview funktioner kan ændres, anbefales det mod skriver fremstilling kode, der tager en afhængighed i preview-versioner. Hvis du bruger en ældre preview-version, anbefaler vi overføre til den alment tilgængelig (GA) version. 

Til .NET SDK: Du kan finde vejledning til overførsel af kode på [opgradere .NET SDK](search-dotnet-sdk-migration.md).

Generelt tilgængelig betyder, at Azure søgning er nu under serviceniveauaftale (SLA). SERVICENIVEAUAFTALEN kan findes på [Azure Søg niveau serviceaftaler](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

