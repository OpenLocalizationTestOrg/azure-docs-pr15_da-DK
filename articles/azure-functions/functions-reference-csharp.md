<properties
    pageTitle="Azure funktioner Udviklerreference | Microsoft Azure"
    description="Forstå, hvordan du udvikler Azure funktioner ved hjælp af C#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Azure funktioner C# Udviklerreference

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
C#-oplevelsen til Azure-funktioner er baseret på Azure WebJobs SDK. Dataflow til din C#-funktionen via metodeargumenter. Argumentnavne er angivet i `function.json`, og der er foruddefinerede navne for at få adgang til ting som funktionen logføring og annulleringen tokens.

Denne artikel forudsætter, at du allerede har læst [Azure funktioner Udviklerreference](functions-reference.md).

## <a name="how-csx-works"></a>Sådan fungerer .csx

Den `.csx` format giver mulighed for at skrive mindre "standardtekst" og fokusere på at skrive bare en C#-funktion. Til Azure-funktioner, du skal blot Medtag samling referencer og navneområder du har brug for op top, som du plejer, og i stedet for at ombryde alt i et navneområde og klasse, du kan kun definere dine `Run` metode. Hvis du vil medtage alle klasser, for eksempel for at definere POCO objekter, du kan medtage en klasse i den samme fil.

## <a name="binding-to-arguments"></a>Binding til argumenter

De forskellige bindinger er bundet til en C#-funktion via den `name` egenskab i sektionen *function.json* konfiguration. Hver binding har sin egen understøttede datatyper, som er beskrevet i binding; for eksempel kan en blob udløser understøtte en streng, en POCO eller flere andre typer. Du kan bruge den type, der passer bedst til dine behov. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Logføring

Hvis du vil logge output streaming logfilerne i C#, kan du medtage en `TraceWriter` skrevet argument. Vi anbefaler, at du navngive den `log`. Vi anbefaler, at du undgår `Console.Write` i Azure funktioner.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Asynkron

For at gøre en funktion asynkron, skal du bruge den `async` nøgleord, og returnerer en `Task` objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Annullering Token

I visse tilfælde skal du muligvis transaktioner, der er følsomme lukkes. Mens altid er det bedst at skrive programkode, der kan håndtere ned, i tilfælde, hvor du vil håndtere lukning anmoder om, du definerer en [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) skrevet argument.  A `CancellationToken` får en host lukning udløses, hvis. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importere navneområder

Hvis du har brug at importere navneområder, kan du gøre så som normalt, med den `using` delsætningen.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

De følgende navneområder importeres automatisk og er derfor valgfri:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Henvisninger til eksterne Assemblies

Framework assemblies, kan du tilføje referencer ved hjælp af den `#r "AssemblyName"` direktiv.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

De følgende assemblies føjes automatisk ved funktionerne Azure hosting-miljø:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Desuden de følgende enheder er speciel familiemedlemmernes og kan refereres til af simplename (fx `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Hvis du har brug for til at referere til en privat samling, kan du overføre filen samling i en `bin` mappe i forhold til dine funktionen og reference den ved hjælp af filen navn (f.eks.  `#r "MyAssembly.dll"`). Oplysninger om, hvordan du overfører filer til mappen funktionen, kan du se følgende afsnit på pakke administration.

## <a name="package-management"></a>Pakke administration

Hvis du vil bruge NuGet pakker i en C#-funktion, overføre en *project.json* fil til den funktionens mappe i appen funktionen filsystemet. Her er et eksempel *project.json* -fil, der tilføjer en reference til Microsoft.ProjectOxford.Face version 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Kun .NET Framework 4.6 understøttes, så skal du kontrollere, at filen *project.json* angiver `net46` som vist her.

Når du overfører en *project.json* -fil, bliver pakkerne til kørsel af, og tilføjer automatisk referencer til pakken assemblies. Du behøver ikke at tilføje `#r "AssemblyName"` direktiver. Ved at tilføje de påkrævede `using` sætninger til filen *run.csx* at bruge de typer, der er defineret i NuGet pakkerne.


### <a name="how-to-upload-a-projectjson-file"></a>Sådan overfører du en project.json-fil

1. Start ved at sikre, at din funktionen app, som du kan gøre ved at åbne din funktionen i portalen Azure, der kører. 

    Dette giver også adgang til loggene streaming hvor pakke installation output vises. 

2. For at overføre filen project.json, skal du bruge en af de metoder, der er beskrevet i afsnittet **Sådan opdaterer du funktionen app-filer** i [Azure funktioner udvikler reference emne](functions-reference.md#fileupdate). 

3. Når filen *project.json* er overført, kan du se output som i følgende eksempel i din funktion streaming log:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Miljøvariabler

For at få en miljøvariablen eller en app, angive værdi skal du bruge `System.Environment.GetEnvironmentVariable`, som vist i følgende kodeeksempel:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Genbruge .csx kode

Du kan bruge klasser og metoder, der er defineret i andre *.csx* filer i filen *run.csx* . Brug til at udføre, `#load` direktiver i *run.csx* filen, som vist i følgende eksempel.

Eksempel på *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Eksempel på *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Du kan bruge en relativ sti med den `#load` direktiv:

* `#load "mylogger.csx"`Indlæser en fil, der er placeret i mappen funktionen.

* `#load "loadedfiles\mylogger.csx"`Indlæser en fil, der er placeret i en mappe i mappen funktionen.

* `#load "..\shared\mylogger.csx"`Indlæser en fil, der er placeret i en mappe på det samme niveau som funktionen mappe, det vil sige, direkte under *wwwroot*.
 
Den `#load` direktiv fungerer kun med *.csx* (C# script) filer, ikke med *.cs* filer. 

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger finder du se følgende ressourcer:

* [Azure funktioner Udviklerreference](functions-reference.md)
* [Azure funktioner F # Udviklerreference](functions-reference-fsharp.md)
* [Azure funktioner NodeJS Udviklerreference](functions-reference-node.md)
* [Azure funktioner udløsere og bindinger](functions-triggers-bindings.md)

