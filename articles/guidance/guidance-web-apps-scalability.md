<properties
   pageTitle="SVG-webprogrammet | Azure referencearkitektur | Microsoft Azure"
   description="Forbedring af skalerbarhed i et webprogram, der kører på Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Forbedring af skalerbarhed i et webprogram 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel viser en anbefalede arkitektur til forbedring af skalerbarhed og ydeevne i et webprogram, der kører på Microsoft Azure. Arkitekturen bygger på [Azure referencearkitektur: grundlæggende webprogram][basic-web-app]. Anbefalinger og overvejelser i forbindelse med denne artikel gælder for denne arkitektur samt.

>[AZURE.NOTE] Azure har to forskellige installation modeller: ressourcestyring og klassisk. I denne artikel bruges Ressourcestyring, som Microsoft anbefaler til nye installationer.

## <a name="architecture-diagram"></a>Arkitektur diagram

![[0]][0]

Arkitekturen har følgende komponenter:

- **Ressourcegruppe**. En [ressourcegruppe] [ resource-group] er en logisk beholder for Azure ressourcer. 

- ** [Online] [ app-service-web-app] ** og ** [API app][app-service-api-app]**. Et typisk moderne program kan indeholde både et websted og en eller flere RESTful web API'er. En web API kan blive brugt, ved at browserklienter via AJAX, oprindelige klientprogrammer eller serverbaserede programmer. Overvejelser om design web API'er, få vist [API designvejledning][api-guidance].    

- **WebJob**. Bruge [Azure WebJobs] [ webjobs] til at køre længerevarende opgaver i baggrunden. WebJobs kan køre på en tidsplan, kontinuert, eller som svar på en udløser, såsom at placere en meddelelse på en kø. En WebJob kører som en baggrunden i forbindelse med en App Service-app. 

- **Kø**. I den arkitektur, der er vist her, programmet køerne baggrundsopgaver ved at placere en meddelelse til en [Azure kø lager] [ queue-storage] kø. Meddelelsen udløser en funktion i WebJob. 

    Du kan også bruge tjenesten Bus køer. For at sammenligne, se [Azure køer og Service Bus køer - sammenlignet og i farvekategorier][queues-compared].

- **Cache**. Gemme halvgennemsigtig statiske data i [Azure Redis Cache][azure-redis].  

- **LEVERANDØR(er)**. Bruge [Azure netværk til levering af indhold] [ azure-cdn] (CDN) til cache offentligt tilgængeligt indhold til nederste forsinkelse og hurtigere levering af indhold.

- **Lagring af data.** Bruge [Azure SQL-Database] [ sql-db] for relationelle data. Ikke-relationelle data, kan du overveje en NoSQL store, såsom Azure-Tabellager eller [DocumentDB][documentdb].

- **Azure Søg**. Brug af [Azure søgning] [ azure-search] tilføje søgefunktionalitet, herunder søgeforslag, uskarpt Søg og sprogspecifikke søgning. Azure søgning bruges typisk sammen med en anden datalager, især hvis den primære datalager kræver nøje konsistens. I denne metode, du vil autoritative dataene i andet datalageret og sætte søgeindekset i Azure søgning. Azure søgning kan også bruges til at konsolidere et enkelt søgeindekset fra flere data butikker.  

- **Mail/SMS**. Hvis dit program skal bruge til at sende mail eller SMS-beskeder, kan du bruge en tredjeparts-tjeneste, såsom SendGrid eller Twilio i stedet for opbygning af denne funktion direkte i programmet.

## <a name="recommendations"></a>Anbefalinger

### <a name="app-service-apps"></a>App Service apps 

Vi anbefaler, at oprette webprogrammet og web API som separat App Service apps. Dette design kan du køre dem i separate App Service-planer, som også kan du tilpasse dem enkeltvis. Hvis du ikke har brug for dette niveau af skalerbarhed på første, kan du installere apps til samme plan og flytte dem ind i separate planer senere, hvis det er nødvendigt. (For de grundlæggende, Standard og Premium planer, du er faktureret efter VM forekomster i planen, ikke efter app. Se [App Service priser][app-service-pricing])

Hvis du vil bruge funktionerne *Nemt tabeller* eller *Nemt API'er* i App-tjenesten Mobile-Apps, kan du oprette en separat App Service-app til dette formål.  Disse funktioner, der er afhængige af en bestemt program ramme, der aktiveres.

### <a name="webjobs"></a>WebJobs

Hvis WebJob er ressource intensivt, kan du overveje at installere den på en tom App Service-app i en separat App Service-plan, skal give dedikeret forekomster til WebJob. Se [baggrund job vejledning][webjobs-guidance].  

### <a name="cache"></a>Cache

Du kan forbedre ydeevne og skalerbarhed ved hjælp af [Azure Redis Cache] [ azure-redis] til cachen nogle data. Overvej at bruge Redis Cache for:

- Halvgennemsigtig statisk transaktionsdata.

- Sessionstilstand.

- HTML-output. Det kan være nyttig i programmer, der gengives som kompleks HTML-output. 

Du kan finde mere detaljerede retningslinjer for design af en strategi for cachelagring, under [cachelagring vejledning][caching-guidance].

### <a name="cdn"></a>LEVERANDØR(ER) 

Bruge [Azure CDN] [ azure-cdn] til cachen til statisk indhold. Den primære fordel ved et CDN er at reducere ventetid for brugere, da indhold er cachelagret på en *edge-server* , der geografisk er tæt på brugeren. LEVERANDØR(er) kan også formindske belastning på programmet, fordi trafikken ikke håndteres af programmet.

- Hvis din app består hovedsageligt af statiske sider, kan du overveje at bruge CDN til cachen hele appen. Se [bruge Azure CDN i Azure App Service][cdn-app-service].

- Ellers skal filer sætte statisk indhold, som billeder, CSS og HTML-, til Azure-lager, og brug CDN til at cachelagre disse filer. Se [integrere en lagerplads konto med CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN kan ikke fungere indhold, der kræver godkendelse.

Du kan finde mere detaljerede vejledning, under [indhold levering netværk (CDN) vejledning][cdn-guidance]. 

### <a name="storage"></a>Lagerplads

Moderne programmer behandle ofte store datamængder. Hvis du vil skalere til skyen, er det vigtigt at vælge den rette storage type. Her er nogle oprindelige anbefalinger.  Finde mere detaljerede vejledning, se [Vurdering af Data Store funktioner for Polyglot brugerdata løsninger][polyglot-storage].

Hvad du vil gemme | Eksempel | Anbefalede lagerplads
--- | --- | ---
Filer | Billeder, dokumenter, PDF-filer | Azure Blob-lager
Nøgle/værdi-par | Brugerdata profil fundet med bruger-ID | Azure-Tabellager
Korte meddelelser, der er beregnet til at udløse videre behandling | Anmodninger om rækkefølge | Azure kø lagerplads, Service Bus kø eller tjeneste Bus emne
Ikke-relationelle data med et fleksible skema, der kræver grundlæggende forespørgsler | Produktkatalog | Dokument-database, som Azure DocumentDB, MongoDB eller Apache CouchDB
Relationelle data, der kræver mere omfattende understøttelse af forespørgsler, skærpet skemaet og/eller stærke konsistens | Produktlageret | Azure SQL-Database

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

### <a name="app-service-app"></a>App Service app

Hvis din løsning omfatter flere App Service apps, kan du overveje at implementere dem for at adskille App Service planer. Denne fremgangsmåde gør det muligt at skalere dem uafhængigt af hinanden, fordi de kører på separate forekomster. Du kan finde flere oplysninger om skalering ud, under [skalerbarhed overvejelser i forbindelse med] [ basic-web-app-scalability] sektion i den [grundlæggende web application arkitektur][basic-web-app].

På samme måde, du overveje at placere en WebJob i sin egen plan, så baggrundsopgaverne ikke køre på de samme forekomster, der håndterer HTTP-anmodninger.  

### <a name="sql-database"></a>SQL-Database

Øge skalerbarhed af en SQL-database med *sharding* databasen &mdash; det vil sige, partitionering databasen vandret. Sharding gør det muligt at skalere databasen vandret, ved hjælp af [elastiske Databaseværktøjer][sql-elastic]. Potentielle fordelene sharding omfatter:

- Bedre transaktion overførselshastighed.

- Forespørgsler kan køre hurtigere over et undersæt af dataene. 

### <a name="azure-search"></a>Azure søgning

Azure Search fjerner spild af udføre komplekse datasøgninger fra den primære datalager, og det kan skaleres til at håndtere Indlæs. Se [skala ressource niveauer for forespørgsel og indeksering arbejdsmængder i Azure Søg][azure-search-scaling].

## <a name="security-considerations"></a>Overvejelser om sikkerheden

### <a name="cross-origin-resource-sharing-cors"></a>Tværs Origin ressourcedeling (CORS)

Hvis du opretter et websted og web API som separat apps, kan ikke webstedet foretage klientsiden AJAX opkald til API, medmindre du aktiverer CORS. 

> [AZURE.NOTE] Browser-sikkerhed forhindrer en webside i at foretage AJAX-anmodninger til et andet domæne. Denne begrænsning kaldes politikken samme origin og forhindrer et ondsindet websted i at læse sentitive data fra et andet websted. CORS er en W3C standard, som tillader en server til slap af den samme origin politik, og tillade nogle tværs origin anmodninger under afvisning af andre.

App Services har indbygget understøttelse af CORS, uden at skulle skrive en hvilken som helst programkode. Se [forbrug en API-app fra JavaScript ved hjælp af CORS][cors]. Føje webstedet til listen over tilladte baggrunden til API. 

### <a name="sql-database-encryption"></a>SQL-Database kryptering

Bruge [Gennemsigtig datakryptering] [ sql-encryption] Hvis du har brug for til at kryptere data på resten i databasen. Denne funktion udfører realtid kryptering og dekryptering af en hel database (herunder sikkerhedskopier og transaktionslogfiler), uden at der skal ændres til programmet. Kryptering tilføjer nogle ventetid, så det er en god ide at adskille de data, der skal være sikker i sin egen database, og Aktivér kryptering kun for den pågældende database.  

## <a name="next-steps"></a>Næste trin

- Større tilgængelighed, installere programmet i mere end ét område og bruge [Azure trafik Manager] [ tm] for failover. Du kan finde flere oplysninger, se [Azure referencearkitektur:-webprogrammet med høj tilgængelighed][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Webprogram i Azure med forbedret skalerbarhed"