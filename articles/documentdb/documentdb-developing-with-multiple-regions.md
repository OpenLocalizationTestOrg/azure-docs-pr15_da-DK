<properties
   pageTitle="Udvikling af med flere områder i DocumentDB | Microsoft Azure"
   description="Lær at få adgang til dine data i flere områder fra Azure DocumentDB, en komplet administreret NoSQL database tjeneste."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Udvikling af med flere områder DocumentDB konti

> [AZURE.NOTE] Global fordelingen af DocumentDB databaser er alment tilgængelig og automatisk aktiveret for en hvilken som helst nyoprettede DocumentDB konti. Vi arbejder på for at aktivere global fordeling for alle eksisterende konti, men i mellemtiden, hvis du vil globale fordeling aktiveret på din konto, skal du [kontakte support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , og vi skal aktivere det for dig nu.

For at udnytte [globale fordeling](documentdb-distribute-data-globally.md), kan klientprogrammer angive bestilte indstilling listen over områder skal bruges til at udføre handlinger i dokumentet. Dette kan gøres ved at angive politikken forbindelse. Baseret på Kontokonfiguration Azure DocumentDB, aktuelle internationale tilgængelighed og listen indstilling, der er angivet, vælges det mest optimale slutpunkt ved SDK til at udføre skrive og læse handlinger. 

Denne indstilling liste er angivet, når initialisering af en forbindelse ved hjælp af DocumentDB klienten SDK'er. SDK'er acceptere en valgfri parameter "PreferredLocations", som er en sorteret liste over Azure områder.

SDK send automatisk alle skriver til aktuelt skrive område. 

Alle læser sendes til den første tilgængelige område på listen PreferredLocations. Hvis anmodningen mislykkes, bliver klienten mislykkes ned på listen til det næste område og så videre. 

Klienten SDK'er kun forsøger at læse fra områderne angivet i PreferredLocations. Så eksempelvis vil Hvis kontoen Database er tilgængelig i tre områder, men klienten kun angiver to af de ikke-og skriveadgang områder for PreferredLocations, derefter ingen læser være served af Skriv regionen, selv i tilfælde af failover.

Programmet kan bekræfte det aktuelle Skriv slutpunkt og læse slutpunkt, der er valgt ved SDK ved at markere to egenskaber, WriteEndpoint og ReadEndpoint, tilgængelige i SDK version 1,8 og over. 

Hvis egenskaben PreferredLocations ikke er angivet, håndteres alle anmodninger fra det aktuelle Skriv område. 


## <a name="net-sdk"></a>.NET SDK
SDK kan bruges uden kodeændringer. I dette tilfælde SDK automatisk dirigerer begge læser og skriver til det aktuelle Skriv område. 

I version 1,8 og senere af .NET SDK har parameteren ConnectionPolicy for DocumentClient parametre en egenskab, der kaldes Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Denne egenskab er af typen af websteder `<string>` og skal indeholde en liste over områdenavne. Strengværdierne er formateret i kolonnen områdenavn på [Azure områder]  [ regions] side, som ikke indeholder mellemrum før eller efter først og sidste tegn henholdsvis.

De aktuelle skriveadgang og læst slutpunkter, er tilgængelige i DocumentClient.WriteEndpoint og DocumentClient.ReadEndpoint henholdsvis.

> [AZURE.NOTE] URL-adresser til slutpunkterne bør ikke betragtes som langlivet konstanter. Tjenesten kan opdatere disse når som helst. SDK håndterer automatisk denne ændring.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript og Python SDK'er
SDK kan bruges uden kodeændringer. I dette tilfælde bliver SDK automatisk direkte både læser og skriver til aktuelt skrive område. 

I version 1,8 og nyere versioner af hver SDK, parameteren ConnectionPolicy for DocumentClient parametre en ny egenskab hedder DocumentClient.ConnectionPolicy.PreferredLocations. Dette er parameter er en matrix af strenge, der fører en liste over områdenavne. Navnene er formateret i kolonnen områdenavn i [Azure områder]  [ regions] side. Du kan også bruge de foruddefinerede konstanter i objektet nemmere AzureDocuments.Regions

De aktuelle skriveadgang og læst slutpunkter, er tilgængelige i DocumentClient.getWriteEndpoint og DocumentClient.getReadEndpoint henholdsvis.

> [AZURE.NOTE] URL-adresser til slutpunkterne bør ikke betragtes som langlivet konstanter. Tjenesten kan opdatere disse når som helst. SDK håndterer automatisk denne ændring.

Nedenfor vises et eksempel på en kode for NodeJS/Javascript. Python og Java følger det samme mønster.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTEN 
Når en database-konto er gjort tilgængelig i flere områder, kan klienter forespørge dets tilgængelighed ved at udføre en GET-anmodning på følgende URI.

    https://{databaseaccount}.documents.azure.com/

Tjenesten vil returnere en liste over områder og deres tilsvarende DocumentDB-slutpunkt URI'er for på Kopier. Det aktuelle område skriv angives i svaret. Klienten kan derefter vælge den relevante slutpunkt for alle yderligere REST-API anmodninger på følgende måde.

Eksempel svar

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Læg, INDLÆG og Slet anmodninger skal gå til den angivne Skriv URI
-   Alle henter og andre skrivebeskyttet anmodninger (for eksempel forespørgsler) kan du gå til en hvilken som helst slutpunkt for kundens valg

Skriv anmodninger om til skrivebeskyttet tilstand regioner mislykkes med fejlkode HTTP 403 ("forbudt").

Hvis området Skriv ændres efter kundens indledende registrering fase, mislykkes efterfølgende skriver til det forrige Skriv område med fejlkode HTTP 403 ("forbudt"). Klienten skal skal du vælge på listen over områder igen for at få opdaterede Skriv område.

## <a name="next-steps"></a>Næste trin

Få mere at vide mere om de distribuere data globalt med DocumentDB i følgende artikler:

- [Distribuere data globalt med DocumentDB](documentdb-distribute-data-globally.md)
- [Konsistens niveauer](documentdb-consistency-levels.md)
- [Sådan overførselshastighed fungerer sammen med flere områder](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Tilføje områder ved hjælp af portalen Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
