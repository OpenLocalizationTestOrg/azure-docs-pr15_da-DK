<properties 
    pageTitle="Hvad er Azure WebJobs SDK" 
    description="En introduktion til Azure WebJobs SDK. Forklarer, hvad SDK er, typisk scenarier, det er nyttigt til, og kode eksempler." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>Hvad er Azure WebJobs SDK

## <a id="overview"></a>Oversigt

I denne artikel forklarer, hvad WebJobs SDK er, gennemgår nogle almindelige scenarier, det er nyttigt til, og giver et overblik over, hvordan du bruger den i din kode.

[WebJobs](websites-webjobs-resources.md) er en funktion i Azure App-tjeneste, som gør det muligt at køre et program eller script i den samme kontekst som en WebApp, API app eller -mobilappen. Formålet med [WebJobs SDK](websites-webjobs-resources.md) er at forenkle den kode, du skriver til almindelige opgaver, en WebJob kan udføre som billedbehandling, kø behandling, RSS-sammenlægning, vedligeholdelse og sende mails. WebJobs SDK indeholder indbyggede funktioner til at arbejde med Azure-lager og Service Bus til planlægning af opgaver og håndtering af fejl og mange andre almindelige scenarier. Desuden er den udviklet til at være extensible. Den [WebJobs SDK er Åbn kilde](https://github.com/Azure/azure-webjobs-sdk/), og der er en [åbne kilde lager til extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

WebJobs SDK indeholder følgende komponenter:

* **NuGet pakker**. NuGet-pakker, som du føjer til et projekt i Visual Studio Console-programmer giver en ramme, der bruger din kode som op din metoder med WebJobs SDK attributter.
  
* **Dashboard**. Del af WebJobs SDK er inkluderet i Azure App-tjenesten og indeholder omfattende overvågning og diagnosticering til programmer, der bruger NuGet-pakker. Du behøver ikke at skrive programkode for at bruge funktionerne overvågning og diagnosticering.

## <a id="scenarios"></a>Scenarier

Her er nogle typiske scenarier, som du kan håndtere nemmere med Azure WebJobs SDK:

* Billede af kataloger eller andre CPU-intensivt arbejde. Et almindelige funktion af websteder er muligheden for at overføre billeder eller videoer. Ofte vil du ændre indholdet, når det er overført, men du ikke vil foretage brugeren Vent, mens du gør dette.

* Kø behandling. En almindelig måde for en web front end til at kommunikere med en back end-tjeneste er at bruge køer. Når webstedet brug at få arbejdet udført, flytter en meddelelse til en kø. En back end-tjeneste trækker meddelelser fra køen og de fungerer. Du kan bruge køer til billedbehandling: for eksempel, når brugeren overfører et antal filer, anbringe filnavnene i en kø meddelelse, der skal hentes ved back-end for behandling. Eller du kan bruge køer til at forbedre websted reaktionstid. For eksempel i stedet for at skrive direkte til en SQL-database, skrive til en kø fortælle den bruger, du er færdig, og lad den back end-tjeneste håndtaget høj ventetid relationsdatabase arbejde. Du kan finde et eksempel på kø behandling med billede af processen, [WebJobs SDK Introduktion selvstudium](websites-dotnet-webjobs-sdk-get-started.md).

* RSS-sammenlægning. Hvis du har et websted, der fører en liste over RSS-feeds, kan du hente alle artiklerne fra feeds i en baggrundsproces i.

* Fil vedligeholdelse, som sammenlægning eller oprydning af logfiler.  Du skal muligvis logfiler, der er oprettet af flere websteder eller for separat tid tidsperioder, du vil kombinere for at køre analysen job på dem. Eller du kan vælge at planlægge en opgave til at køre ugentlig at rydde op i gamle logfiler.

* Vandindtrængen til Azure tabeller. Du kan have filer, der er gemt og BLOB og vil til at analysere dem og gemme dataene i tabeller. Funktionen vandindtrængen kan skrive mange rækker (millioner i nogle tilfælde), og i WebJobs SDK gør det muligt at implementere denne funktionalitet nemt. SDK indeholder også overvågning i realtid af symboler for fremgang som antallet rækker, der skrives i tabellen.

* Andre længerevarende opgaver, som du vil køre i en baggrundstråd, som [sender mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Alle opgaver, som du vil køre en tidsplan, som udfører handlingen sikkerhedskopiering hver NAT..

I mange af disse situationer kan du vil skalere et WebApp til at køre på flere FOS, hvilket vil køre flere WebJobs samtidigt. I nogle tilfælde kan det resultere i de samme data behandlet flere gange, men det er ikke noget problem, når du bruger den indbyggede kø, blob og Service Bus udløsere af WebJobs SDK. SDK sikrer, din funktioner køres kun én gang til hver meddelelse eller blob.

WebJobs SDK gør det også nemt at håndtere almindelige scenarier til fejlhåndtering. Du kan konfigurere beskeder til at sende beskeder, når en funktion mislykkes, og du kan angive timeout, så en funktion annulleres automatisk, hvis den ikke fuldføres inden for et angivet tidsrum, der er.

## <a id="code"></a>Kodeeksempler

Koden for håndtering af typiske opgaver, der arbejder med Azure-lager er enkelt. I dit Console programs `Main` metode, du opretter en `JobHost` objekt, koordinater opkald til metoder, du skriver. WebJobs SDK framework ved, hvornår du skal ringe til din metoder, og hvad parameterværdier bruge baseret på de WebJobs SDK attributter, du bruger i dem. SDK indeholder *Udløsere* , som angiver, hvad betingelser medføre funktionen, der skal ringes op og *projektmapper* , der angiver, hvordan du får oplysninger til og fra metodeparametre.

For eksempel bevirker attributten [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) , at en funktion til at blive kaldt, når der modtages en meddelelse på en kø, og hvis meddelelsesformatet er JSON for en bytematrix eller en brugerdefineret type, meddelelsen er automatisk deserialiserede. Attributten [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) udløser en proces, når der oprettes en ny blob i en Azure-lager-konto.

Her er et enkelt program, der sender forespørgsler til en kø og opretter en blob for hver kø meddelelse, der er modtaget:

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

Den `JobHost` objekt er en beholder for et sæt af baggrunden funktioner. Den `JobHost` objekt overvåger funktionerne, ure for hændelser, der udløser dem, og udfører funktionerne, når udløser hændelser indtræffer. Du ringe til en `JobHost` metode til at angive, om processen objektbeholder til at køre på den aktuelle tråd eller en baggrundstråd. I eksemplet på `RunAndBlock` metode kører processen tiden på den aktuelle tråd.

Da den `ProcessQueueMessage` metode i dette eksempel har en `QueueTrigger` attribut, udløseren, for at funktionen ikke er modtagelse af en ny meddelelse i køen. Den `JobHost` objektet ser efter nye meddelelser i kø på den angivne kø ("webjobsqueue" i dette eksempel), og når der findes en, kalder `ProcessQueueMessage`. 

Den `QueueTrigger` attribut bindinger på `inputText` -parameter til værdien af kø meddelelsen. Og `Blob` attribut bindinger en `TextWriter` objekt til en blob med navnet "blobname" i en objektbeholder med navnet "containername".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

Derefter bruger funktionen disse parametre til at skrive værdien af kø meddelelsen til blob:

        writer.WriteLine(inputText);

Funktionerne udløser og projektmappe af WebJobs SDK nemmere gør det meget den kode, du skal skrive. Nødvendige at behandle køer, BLOB eller filer, eller at starte planlagte opgaver, koden er gjort for dig ved WebJobs SDK framework. For eksempel rammerne opretter køer, der ikke findes endnu, åbnes køen, læser kø meddelelser, sletter kø meddelelser, når behandling er fuldført, opretter blob beholdere, der ikke findes endnu, skriver til BLOB osv.

Følgende kodeeksempel viser en række over udløsere i én WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, og `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Planlægning

Den `TimerTrigger` attribut giver dig mulighed for at udløse funktioner til at køre en tidsplan. Du kan planlægge en WebJob som helhed via Azure eller planlægge individuelle funktioner af en WebJob ved hjælp af WebJobs SDK `TimerTrigger`. Her er et eksempel på kode.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Se [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) i azure-webjobs-sdk-filtypenavne lager på GitHub.com for mere eksempelkode.

## <a name="extensibility"></a>Udvidelsesmuligheder af

Du ikke er begrænset til indbyggede funktioner – i WebJobs SDK, kan du skrive brugerdefinerede udløsere og projektmapper.  Du kan for eksempel skrive udløsere til cache begivenheder og periodiske tidsplaner. En [åbne kilde lager](https://github.com/Azure/azure-webjobs-sdk-extensions) indeholder en [detaljeret vejledning under WebJobs SDK udvidelsesmuligheder](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) og eksempel kode for at komme i gang med at skrive dine egne udløsere og projektmapper.

## <a id="workerrole"></a>Ved hjælp af WebJobs SDK uden for WebJobs

Et program, der bruger den WebJobs SDK er et standard Console-program og kan køre et vilkårligt sted – det behøver ikke at køre som en WebJob. Du kan teste programmet lokalt på computeren udvikling, og i fremstilling kan du køre den i en skybaseret tjeneste arbejder rolle eller en Windows-tjeneste Hvis du foretrækker en af disse miljøer. 

Dashboardet er kun tilgængelig som et filtypenavn for en Azure App Service WebApp. Hvis du vil køre uden for en WebJob og stadig bruge dashboardet, kan du konfigurere en WebApp for at bruge den samme lagerplads konto, som din WebJobs SDK Dashboard forbindelsesstreng refererer til, og som WebApp WebJobs Dashboard, får du vist data om udførelse af funktionen fra dit program, der kører en anden placering. Du kan få adgang til Dashboard med URL-adressen https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Se [få et dashboard til lokale udvikling med WebJobs SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)kan finde flere oplysninger, men Bemærk, at blogindlægget viser en gamle forbindelsesnavn for streng. 

## <a id="nostorage"></a>Dashboardfunktioner

WebJobs SDK indeholder flere fordele, selvom du ikke bruger WebJobs SDK udløsere eller projektmapper:

* Du kan aktivere funktioner fra dashboardet.
* Du kan afspille funktioner fra dashboardet.
* Du kan få vist logfiler i dashboardet, sammenkædet med bestemt WebJob (programmet logfiler, skrevet ved hjælp af Console.Out, Console.Error, sporing, osv.) eller sammenkædet med aktiveringen bestemt funktion, som genererede dem (logfiler, der er skrevet ved hjælp af en `TextWriter` objekt, i SDK overfører til funktionen som en parameter). 

Du kan finde flere oplysninger, se [Sådan manuelt kalde en funktion](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) , og [hvordan du kan skrive logfiler](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Næste trin

Du kan finde flere oplysninger om WebJobs SDK [Azure WebJobs anbefalede ressourcer](http://go.microsoft.com/fwlink/?linkid=390226).

Du kan finde oplysninger om de seneste forbedringer til WebJobs SDK [Produktbemærkninger](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
