<properties
    pageTitle="JSON output til Stream Analytics | Microsoft Azure"
    description="Få mere at vide, hvordan Stream Analytics kan målrette Azure DocumentDB for JSON output for arkivering af data og lav ventetid forespørgsler på ustrukturerede JSON-data."
    keywords="JSON output"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Target Azure DocumentDB til JSON output fra Stream Analytics

Stream Analytics kan målrette [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) for JSON output, så dataforespørgsler arkivering og lav ventetid på ustrukturerede JSON-data. Lær, hvordan du bedst implementere denne integration.

Se nærmere på [Documentdbs læringssti](https://azure.microsoft.com/documentation/learning-paths/documentdb/) at komme i gang for dem, der ikke har kendskab til DocumentDB.

## <a name="basics-of-documentdb-as-an-output-target"></a>Grundlæggende om DocumentDB som en output destination
Azure DocumentDB output i Stream Analytics aktiverer skrivning din stream behandling resultater som JSON output til din DocumentDB af websteder. Stream Analytics opretter ikke af websteder i databasen, i stedet for at du behøver at oprette dem forhånd. Dette er, så de fakturering omkostninger ved DocumentDB af websteder er synlige for dig, og så du kan finjustere ydeevnen, konsistens og kapacitet af samlinger direkte ved hjælp af [DocumentDB API'er](https://msdn.microsoft.com/library/azure/dn781481.aspx). Det anbefales at bruge én DocumentDB Database per streaming tingene til at adskille logisk dine samlinger for en streaming sag.

Nogle af indstillingerne for DocumentDB af websteder er beskrevet nedenfor.

## <a name="tune-consistency-availability-and-latency"></a>Finjustere konsistens, tilgængelighed og forsinkelse

Så de passer til dine krav til programmet på computeren, kan DocumentDB du at finjustere databasen og samlinger, og foretag kompromiser mellem konsistens, tilgængelighed og ventetid. Afhængigt af hvilken niveauer af Læs konsistens dine scenarie behov mod læse og skrive ventetid, kan du vælge et konsistens niveau på databasekontoen. Som standard DocumentDB gør også synkron indeksering i hver CRUD-handling til din samling. Dette er en anden nyttig indstilling til at styre, Skriv/læst ydeevne i DocumentDB. Gennemgå artiklen om [ændre din database og forespørgsel konsistens niveauer](../documentdb/documentdb-consistency-levels.md) for yderligere oplysninger om dette emne.

## <a name="choose-a-performance-level"></a>Vælge et ydeevne

DocumentDB af websteder kan oprettes på 3 forskellige ydeevneniveauer (S1, S2 eller S3), som bestemmer overførselshastigheden tilgængelige for CRUDs til samlingen. Desuden der ydeevne påvirkes af indeksering/konsistens niveauer i din af websteder. Se [denne artikel](../documentdb/documentdb-performance-levels.md) for at forstå disse ydeevneniveauer i detaljer.

## <a name="upserts-from-stream-analytics"></a>Upserts fra Stream Analytics

Stream Analytics integration med DocumentDB giver dig mulighed at indsætte eller opdatere poster til din DocumentDB af websteder, der er baseret på et bestemt dokument-ID-kolonne. Dette kaldes også som en *Upsert*.

Stream Analytics anvender en optimistisk Upsert metode, hvor opdateringer er kun færdig, når Indsæt mislykkes på grund af en konflikt med dokument-ID. Denne opdatering er udført af Stream Analytics som en programrettelse, så det giver mulighed for delvis opdateringer til dokumentet, det vil sige tilføjelse af nye egenskaber eller erstatte en eksisterende egenskab udføres trinvist. Bemærk, at ændringer i værdierne i matrixen egenskaber i JSON dokument resultatet i hele matrixen få overskrives, det vil sige matrixen flettes ikke.

## <a name="data-partitioning-in-documentdb"></a>Data partitionering i DocumentDB

DocumentDB samlinger gør det muligt at dele dine data, der er baseret på både forespørgsel mønstre og ydeevne passer til dit program. Hver af websteder kan indeholde op til 10GB data (maksimum), og der findes i øjeblikket ingen måde at skalere op (eller overløb) en samling. For skalering af Stream Analytics giver dig mulighed at skrive til flere af websteder med et bestemt præfiks (se detaljer om anvendelse nedenfor). Stream Analytics bruger den ensartede [Hash Partition Resolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) strategi, der er baseret på den bruger, der er angivet PartitionKey kolonne til at partition sine output poster. Antallet af websteder med det angivne præfiks på kørslen streaming starttidspunkt bruges som output partition Tæl, hvor jobbet skriver til parallelt (DocumentDB samlinger = Output partitioner). For en enkelt S3 indsætter af websteder med fløde indeksering gøre kun, om 0,4 MB/s Skriv overførselshastighed kan forventes. Brug af flere samlinger kan giver dig mulighed at opnå højere overførselshastighed og øget kapacitet.

Hvis du vil øge antallet partition fremover, kan du vil stoppe tingene, opdele dataene fra dine eksisterende samlinger til nye af websteder og derefter genstarte Stream Analytics jobbet. Få mere at vide om brug PartitionResolver og igen partitionering sammen med eksempelkode, der medtages i et opfølgende indlæg. Artikel [partitionering i DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) indeholder også oplysninger om dette.

## <a name="documentdb-settings-for-json-output"></a>DocumentDB indstillinger for JSON output

Oprette DocumentDB som output i Stream Analytics genererer en påmindelse til oplysninger som set nedenfor. Dette afsnit indeholder en forklaring af definitionen af egenskaber.

![documentdb stream analytics skærmbillede](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Output Alias** – et alias henvise dette output i forespørgslen ASA  
-   **Kontonavn** – navnet eller slutpunkt URI DocumentDB-konto.  
-   **Kontonøgle** – tasten delt adgang til kontoen DocumentDB.  
-   **Database** – feltet DocumentDB databasenavn.  
-   **Samling navn mønster** – samling navn mønster for samlinger der skal bruges. Navneformatet af websteder kan opbygget ved hjælp af tokenet valgfrit {partition}, hvor partitioner starter fra 0. Følgende er eksempel gyldige input:  
   1\) MyCollection – én samling med navnet "MyCollection" skal findes.  
   2\) MyCollection {partition} – Sådan af websteder skal findes – "MyCollection0", "MyCollection1", "MyCollection2" osv.  
-   **Partitionsnøgle** – navnet på feltet i output hændelser, der bruges til at angive nøgle for partitionering output på tværs af websteder. Enkelt samling output til en vilkårlig outputkolonnen kan være bruges fx PartitionId.  
-   **Dokument-ID** – valgfrit. Navnet på feltet i output hændelser, der bruges til at angive den primære nøgle, Indsæt eller Opdater handlinger er baseret.  
