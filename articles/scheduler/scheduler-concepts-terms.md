<properties
 pageTitle="Scheduler begreber, vilkår og enheder | Microsoft Azure"
 description="Azure Scheduler begreber, terminologi og enhed hierarki, herunder job og job af websteder.  Viser en omfattende eksempel på et planlagt job."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Scheduler begreber, terminologi + enhed hierarki

## <a name="scheduler-entity-hierarchy"></a>Scheduler enhed hierarki

I følgende tabel beskrives de primære ressourcer, der vises, eller som bruges af Scheduler API:

|Ressource | Beskrivelse |
|---|---|
|**Job af websteder**|En sag samling indeholder en gruppe af job og vedligeholder indstillinger, kvoter og throttles, der deles af sager i gruppen af websteder. En sag af websteder er oprettet af ejeren af et abonnement og grupperer job sammen baseret på brugen eller program grænser. Det er begrænset til ét område. Gør det også håndhævelsen af kvoter for at begrænse brugen af alle job i samlingen. Kvoterne omfatter MaxJobs og MaxRecurrence.|
|**Job**|Et job definerer en enkelt tilbagevendende handling med enkel eller kompleks strategier til udførelse af. Handlinger kan omfatte HTTP, lagerplads kø, service bus kø eller bus emne serviceanmodninger.|
|**En oversigt over**|Oversigt over et repræsenterer detaljer for en udførelse af en sag. Den indeholder succes kontra fejl, samt eventuelle svar detaljer.|

## <a name="scheduler-entity-management"></a>Håndtering af Opgavestyring enhed

Få vist følgende handlinger på ressourcerne, der på et højt niveau planlæggeren og service management API:

|Egenskab|Beskrivelse og URI-adresse|
|---|---|
|**Administration af udskriftsjob af websteder**|FÅ, flytte, og Slet support til oprettelse og redigering af websteder job og job deri. En samling af job er en beholder for job og kort kvoter og delte indstillinger. Eksempler på kvoter, beskrives senere, er maksimale antal sager og mindste gentagelsesinterval. <p>FLYTTE og slette:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>FÅ:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Administration af udskriftsjob**|FÅ, PLACERE, skrive, programrettelse, og Slet support til oprettelse og ændring af sager. Alle job skal være medlem af en sag af websteder, der allerede findes, så der er ingen implicit oprettelse. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Oversigt over administration af udskriftsjob**|FÅ support til hentning af 60 dage efter udførelse af jobs, som jobbet forløbet tid og job udførelse af resultater. Tilføjer forespørgsel streng parameterunderstøttelse af filtrering baseret på og status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Typer af job

Der er flere typer job: http-job (herunder HTTPS-job, der understøtter SSL), lagerplads køjob, service bus køjob og service bus emne job. HTTP-job er ideel, hvis du har et slutpunkt for en eksisterende arbejdsbyrde eller tjeneste. Du kan bruge lagerplads køjob til at sende meddelelser til lagerplads køer, så disse job er velegnet til arbejdsbelastninger, som bruger lagerplads køer. På samme måde, service bus job er velegnet til arbejdsbelastninger, som bruger bus servicekøer og emner.

## <a name="the-job-entity-in-detail"></a>Objektet "job" detaljeret

På et grundlæggende niveau har et planlagt job flere dele:

- Handlingen, der skal udføres, når jobbet timer udløses  

- (Valgfrit) Tid til at køre jobbet  

- (Valgfrit) Hvornår og hvor ofte skal gentage jobbet  

- (Valgfrit) En handling forespørgselsreglen, hvis den primære handling mislykkes  

Et planlagt job indeholder internt, forudsat system data som den næste planlagte kørselstid.

Følgende kode giver en omfattende eksempel på et planlagt job. Detaljer er angivet i de efterfølgende afsnit.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Som det fremgår eksempel planlagte jobbet ovenfor, har en definition af job flere dele:

- Starttidspunktet ("starttidspunkt")  

- Handling ("handling"), som indeholder fejl handling ("errorAction")

- Gentagelse ("gentagelse")  

- Staten ("stater")  

- Status ("status")  

- Prøv igen politik ("retryPolicy")  

Lad os undersøge hver af disse detaljerede oplysninger om:

## <a name="starttime"></a>Starttidspunkt

"Starttidspunkt" er starttidspunktet og giver mulighed for opkalds til at angive en tidszone forskydning på kablet i [ISO-8601-format](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>handling og errorAction

"Handlingen" er den handling, der startes på hver forekomst og beskriver en type af tjenesten aktivering. Handlingen er, hvad vil blive udført på den angivne tidsplan. Scheduler understøtter HTTP, lagerplads kø, service bus emne og tjeneste bus kø handlinger.

Handlingen i det foregående eksempel er en HTTP-handling. Nedenfor vises et eksempel på en lagerplads kø handling:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Nedenfor er et eksempel på en tjeneste bus emne handling.

  "handling": {"type": "serviceBusTopic", "serviceBusTopicMessage": {"topicPath": "t1"  
      "navneområde": "mySBNamespace", "transportType": "netMessaging" / / kan være enten netMessaging eller AMQP "godkendelse": {"sasKeyName": "QPolicy", "type": "sharedAccessKey"}, "besked": "Nogle meddelelsen", "brokeredMessageProperties": {}, "customMessageProperties": {"programnavn": "FromScheduler"}},}

Er et eksempel på en tjeneste bus kø handling under:


  "handling": {"serviceBusQueueMessage": {"queueName": "Kvt1"  
      "navneområde": "mySBNamespace", "transportType": "netMessaging" / / kan være enten netMessaging eller AMQP "godkendelse": {}  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey"}, "besked": "Nogle besked"  
      "brokeredMessageProperties": {}, "customMessageProperties": {"programnavn": "FromScheduler"}}, "type": "serviceBusQueue"}

"errorAction" er valgt som fejl, den handling, der aktiveres, når den primære handling mislykkes. Du kan bruge denne variabel til at ringe fejlhåndtering ark eller sende en meddelelse til brugerne. Dette kan bruges til at kontakte en sekundær slutpunkt i tilfælde, primært ikke er tilgængelig (f.eks., i tilfælde af nedbrud på det slutpunkt websted) eller kan bruges til besked om en fejlhåndtering slutpunkt. Ligesom handlingen primære kan handlingen fejl være simpelt eller sammensat logik, der er baseret på andre handlinger. Se [oprette og bruge en delt Access signatur](https://msdn.microsoft.com/library/azure/jj721951.aspx)for at lære at oprette et SAS token.

## <a name="recurrence"></a>gentagelse

Gentagelse har flere dele:

- Frekvens: En af minut, time, dag, uge, måned, år  

- Interval: Interval på den angivne hyppighed for gentagelse  

- Fastsatte tidsplan: Angiv minutter, timer, ugedage, måneder og monthdays for gentagelsen  

- Antal: Antallet af forekomster  

- Sluttidspunkt: ingen job udføres efter det angivne sluttidspunkt  

Et job er tilbagevendende, hvis der er et tilbagevendende objekt, der er angivet i JSON definitionen. Hvis både Tæl og sluttidspunkt er angivet, accepteret fuldførelse reglen, der forekommer først.

## <a name="state"></a>stat

Tilstanden for jobbet er en af fire værdier: aktiveret, deaktiveret, fuldført eller afbrudt. Du kan ANBRINGE eller programrettelse job med henblik på at opdatere dem til den aktiverede eller deaktiveret tilstand. Hvis et job er blevet fuldført eller afbrudt, er det en sluttilstand, der ikke kan opdateres (men kan stadig slettes jobbet). Et eksempel på egenskaben state er som følger:


        "state": "disabled", // enabled, disabled, completed, or faulted
Fuldførte og fejl job slettes efter 60 dage.

## <a name="status"></a>status

Når et job Scheduler er startet, returneres oplysninger om den aktuelle status for jobbet. Dette objekt kan ikke angives af brugeren – det er angivet i systemet. Det er dog medtages i jobobjektet (i stedet for en separat sammenkædede ressource), så en nemt kan få status for en sag.

Jobstatus for indeholder klokkeslættet for den forrige udførelse af (hvis relevant), den næste planlagte kørselstidspunktet (for igangværende job) og udførelse af antallet af jobbet.

## <a name="retrypolicy"></a>retryPolicy

Hvis et job Scheduler mislykkes, er det muligt at angive en politik for forsøg igen for at afgøre, om og hvordan handlingen skal gentages. Dette bestemmes af objektet **retryType** – det er indstillet til **ingen Hvis der ikke er nogen forsøg politikken, som vist ovenfor** . Angive den til **fast** , hvis der er en politik for prøv igen.

Hvis du vil angive en politik for forsøg, der kan angives to yderligere indstillinger: et interval for gentagelse (**retryInterval**) og antallet af forsøg (**retryCount**).

Prøv igen interval, angivet med objektet **retryInterval** er intervallet mellem forsøg. Er standardværdien er 30 sekunder, dens konfigurerbar minimumværdi er 15 sekunder, og den maksimale værdi er 18 måneder. Job i gratis job af websteder har en konfigurerbar minimumværdi til 1 time.  Det er defineret i ISO 8601-format. På samme måde, værdien af antallet af forsøg er angivet med **retryCount** objektet. det er antallet gange, et nyt forsøg forsøges. Er standardværdien er 4, og den maksimale værdi er 20\. begge **retryInterval** og **retryCount** er valgfrit. De får deres standard, hvis **retryType** er indstillet til **fast** og ingen værdier er angivet eksplicit.

## <a name="see-also"></a>Se også

 [Hvad er Scheduler?](scheduler-intro.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Sådan oprettes komplekse tidsplaner og avancerede gentagelse med Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)
