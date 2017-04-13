<properties
    pageTitle="Output til en Azure Redis cachen, ved hjælp af Azure-funktioner fra Azure Stream Analytics | Microsoft Azure"
    description="Lær, hvordan du bruger en Azure-funktion forbundne en tjeneste Bus kø, for at udfylde en Azure Redis Cache fra output fra et Stream Analytics-job."
    keywords="data streame redis cache service bus kø"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Sådan gemmes data fra Azure Stream Analytics i en Azure Redis cachen til brug af Azure

Azure Stream Analytics kan du hurtigt at udvikle og installere lave omkostninger løsninger for at få realtid indsigt fra enheder, sensorer, infrastruktur og programmer eller en hvilken som helst strøm af data. Det giver mulighed for forskellige Brug tilfælde som realtid administration og overvågning, kommandoen og kontrolelementet, bedrageri registrering, forbundne biler og mange flere. I mange disse scenarier, kan du vil gemme data afgangsantal ved Azure Stream Analytics til et fordelt datalager som en Azure Redis cache.

Antag, at du er en del af en telekommunikation virksomhed. Du prøver at registrere SIM svindel, hvor flere opkald, der kommer fra den samme identitet, på samme tid, men i forskellige geografisk placeringer. Du har til opgave lagring af alle mulige falsk opkald i en Azure Redis cache. I denne blog giver vi vejledning om hvordan du nemt kan fuldføre din opgave. 

## <a name="prerequisites"></a>Forudsætninger
Fuldføre [Realtid svindel registrering] [ fraud-detection] gennemgang for ASA

## <a name="architecture-overview"></a>Oversigt over arkitektur
![Skærmbillede af arkitektur](./media/stream-analytics-functions-redis/architecture-overview.png)

Som vist i den foregående figur, kan Stream Analytics streaming indtastede data for at forespørges og sendes til output. Baseret på output, kan Azure funktioner derefter udløse en type af begivenhed. 

I denne blog fokuserer vi på Azure funktioner en del af denne pipeline eller mere specifikt udløser for en begivenhed, der gemmer falske data i cachen.
Når du er færdig [Realtid bedrageri registrering] [ fraud-detection] selvstudium, du har en input (en begivenhed hub), en forespørgsel og output (blob storage) allerede konfigureret og kører. I denne blog, kan vi ændre output for at bruge en tjeneste Bus kø i stedet. Vi opretter forbindelse en Azure-funktion til denne kø herefter. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Oprette og forbinde en Service Bus kø output
Hvis du vil oprette en tjeneste Bus kø, skal du følge trin 1 og 2 i sektionen .NET i [Komme i gang med Bus servicekøer][servicebus-getstarted].
Nu Lad os oprette forbindelse køen til Stream Analytics jobbet, der er oprettet i tidligere svindel registrering gennemgå.



1. Gå til bladet **output** af tingene i portalen Azure, og vælg **Tilføj** øverst på siden.

    ![Tilføje output](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Vælg **Service Bus kø** som **synkronisere** , og følg vejledningen på skærmen. Sørg for at vælge navneområdet for Service Bus køen du oprettede i [Komme i gang med Bus servicekøer][servicebus-getstarted]. Klik på knappen "højre", når du er færdig.
3. Angiv følgende værdier:
    - **Begivenhed serialiseringsfunktion Format**: JSON
    - **Kodning**: UTF8
    - **FORMAT**: linje adskilt

4. Klik på knappen **Opret** for at tilføje denne kilde og for at bekræfte, at Stream Analytics kan oprette forbindelse til kontoen lagerplads.

5. Erstat den aktuelle forespørgsel i fanen **forespørgsel** med følgende. Erstat *[Dit SERVICE BUS navn]* med outputnavn, du oprettede i trin 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Oprette en Azure Redis Cache
Oprette en Azure Redis cache ved at følge afsnittet .NET i [Sådan Brug Azure Redis Cache] [ use-rediscache] indtil sektionen kaldet ***konfigurere cache-klienter***.
Når det er fuldført, har du en ny Redis Cache. Vælg **hurtigtaster** og note ned ***primære forbindelsesstreng***under **alle indstillinger**.

![Skærmbillede af arkitektur](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Oprette en Azure-funktion
Følg [Opret dit første Azure-funktionen] [ functions-getstarted] selvstudium at komme i gang med Azure funktioner. Hvis du allerede har en Azure funktion, du vil bruge, derefter gå videre til [skrivning til Redis Cache](#Writing-to-Redis-Cache)

1. I portalen, Vælg App tjenester fra navigationslinjen til venstre, og klik derefter på dit Azure funktionen appnavn for at få adgang til funktionens app-websted.
    ![Skærmbillede af app services funktionslisten](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Klik på **nye funktion > ServiceBusQueueTrigger – C#**. Følg disse instruktioner for følgende felter:
    - **Kø navn**: det samme navn som det navn, du har angivet, da du oprettede køen i [Komme i gang med Bus servicekøer] [ servicebus-getstarted] (ikke navnet på den tjeneste bus). Kontrollér, at du bruger den kø, der er forbundet med Stream Analytics output.
    - **Forbindelse Service Bus**: Vælg **Tilføj en forbindelsesstreng**. For at finde forbindelsesstrengen, gå til portalen klassisk skal du vælge **Service Bus**, den service bus, du har oprettet og **FORBINDELSESOPLYSNINGERNE** i bunden af skærmen. Kontrollér, at du er på hovedskærmen på denne side. Kopiere og indsætte forbindelsesstrengen. Velkommen til at angive en hvilken som helst forbindelsesnavn.
    
        ![Skærmbillede af tjenesten bus forbindelse](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: Vælg **administrere**


3. Klik på **Opret**

## <a name="writing-to-redis-cache"></a>Skrivning til Redis Cache
Vi har nu oprettet en Azure-funktion, der læses fra en tjeneste Bus kø. Alt, hvad der resterer bruges vores funktion til at skrive disse data til den Redis Cache. 

1. Vælg din nyoprettede **ServiceBusQueueTrigger**, og klik på **Indstillinger for funktionen** i øverste højre hjørne. Vælg **Gå til App Tjenesteindstillinger > Indstillinger > Indstillinger for et webprogram**

2. Oprette et navn i sektionen **Name** i sektionen forbindelse strenge. Indsæt den primære forbindelsesstreng, du fandt i **oprette en Redis Cache** trin i sektionen **værdi** . Vælg **brugerdefineret** , hvor der står **SQL-Database**.

3. Klik på **Gem** øverst.

    ![Skærmbillede af tjenesten bus forbindelse](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Gå tilbage til App-Tjenesteindstillinger og vælg **Værktøjer > App Service Editor (Preview) > på > gå**.

    ![Skærmbillede af tjenesten bus forbindelse](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Opret en JSON-fil med navnet **project.json** med følgende i en editor efter eget valg og gemme den på harddisken.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Overføre denne fil til rodmappen på din funktionen (ikke WWWROOT). Du bør se en fil med navnet **project.lock.json** automatisk vises, skal bekræfte, at Nuget pakker "StackExchange.Redis" og "Newtonsoft.Json" er blevet importeret.

7. Erstat den foruddefinerede genererede kode med følgende kode i filen **run.csx** . Erstat "CONN navn" i funktionen lazyConnection med det navn, du oprettede i trin 2 af **til lagring af data i Redis cache**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Starte Stream Analytics-job

1. Starte programmet, telcodatagen.exe. Brug er som følger:````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. Klik på **Start** øverst på siden fra bladet Stream Analytics tingene på portalen.

    ![Skærmbillede af startjob](./media/stream-analytics-functions-redis/starting-job.png)

3. Vælg **nu** **starte job** bladet, der vises, og klik derefter på knappen **Start** i bunden af skærmen. Jobstatus ændres til start og efter tidsændringer til at køre.
 
    ![Skærmbillede af valg af start job tidszone](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Køre løsning, og se resultaterne
Gå tilbage til siden **ServiceBusQueueTrigger** , bør du nu se log sætninger. Disse logfiler vise, at du har fået noget fra Service Bus køen, sætte det i databasen, og hentet ud ved hjælp af tid som tasten!

Gå til siden Redis cachen i den nye portal (som vist i det foregående trin, [Opret en Azure Redis Cache](#Create-an-Azure-Redis-Cache) ) for at bekræfte, at dine data er i din Redis cache, og vælg Console.

Du kan nu skrive Redis kommandoer til at bekræfte, at dataene er faktisk i cachen.

![Skærmbillede af Redis Console](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Næste trin
Vi glæder om de nye ting Azure funktioner og Stream analytics kan gøre sammen, og vi håber dette låser nye muligheder for dig. Hvis du har din feedback på den ønskede næste, Velkommen til at bruge [Azure UserVoice websted](https://feedback.azure.com/forums/270577-stream-analytics).

Hvis du er ny Microsoft Azure, kan vi inviterer dig til at prøve det ved at tilmelde dig en [gratis Azure-prøveversion konto](https://azure.microsoft.com/pricing/free-trial/). Hvis du er ny bruger af Stream Analytics, derefter inviterer vi dig til at [oprette din første Stream Analytics-job](stream-analytics-create-a-job.md).

Hvis du har brug Hjælp eller har spørgsmål, indlæg på [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) - eller [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) forummerne. 

Du kan også få vist i følgende ressourcer:

- [Azure funktioner Udviklerreference](../azure-functions/functions-reference.md)
- [Azure funktioner C# Udviklerreference](../azure-functions/functions-reference-csharp.md)
- [Azure funktioner F # Udviklerreference](../azure-functions/functions-reference-fsharp.md)
- [Azure funktioner NodeJS Udviklerreference](../azure-functions/functions-reference.md)
- [Azure funktioner udløsere og bindinger](../azure-functions/functions-triggers-bindings.md)
- [Hvordan du kan overvåge Azure Redis Cache](../redis-cache/cache-how-to-monitor.md)

Du kan holde dig opdateret, så alle de seneste nyheder og funktioner, skal du følge [@AzureStreaming](https://twitter.com/AzureStreaming) på Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
