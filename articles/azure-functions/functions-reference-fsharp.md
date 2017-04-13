<properties
    pageTitle="Azure funktioner F # Udviklerreference | Microsoft Azure"
    description="Forstå, hvordan du udvikler Azure funktioner ved hjælp af F #."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure-funktioner, funktioner, begivenhed behandling, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur, F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Azure fungerer F # Udviklerreference

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

F # til Azure-funktioner er en løsning for at køre nemt små kode eller "funktioner" i skyen. Dataflow til funktionen F # via funktionsargumenter. Argumentnavne er angivet i `function.json`, og der er foruddefinerede navne for at få adgang til ting som funktionen logføring og annulleringen tokens.

Denne artikel forudsætter, at du allerede har læst [Azure funktioner Udviklerreference](functions-reference.md).

## <a name="how-fsx-works"></a>Sådan fungerer .fsx

En `.fsx` filen er en F #-script. Det kan betragtes som et F #-projekt, der er indeholdt i en enkelt fil. Filen indeholder begge koden for dit program (i dette tilfælde din Azure-funktionen) og direktiver for at administrere afhængigheder.

Når du bruger en `.fsx` for en Azure-funktion, ofte påkrævet at assemblies medtages automatisk for dig, så du kan fokusere på funktionen i stedet for "standardtekst" koden.

## <a name="binding-to-arguments"></a>Binding til argumenter

Hver binding understøtter nogle sæt af argumenter, som beskrevet i [Azure funktioner udløsere og bindinger Udviklerreference](functions-triggers-bindings.md). For eksempel er en af de argument bindinger en blob udløser understøtter en POCO, som kan udtrykkes ved hjælp af en F #-post. Eksempel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Din F # Azure funktionen tager et eller flere argumenter. Når vi taler om Azure funktioner argumenter, er vi henviser til _input_ argumenter og _output_ argumenter. Et input argument er præcis, hvad det lyder som: input til din F # Azure funktion. Et _output_ argument er af data eller en `byref<>` argument, der fungerer som en metode til at overføre data tilbage _ud_ af dine funktionen.

I eksemplet ovenfor, `blob` er argument input og `output` er argument output. Bemærk, at vi har brugt `byref<>` for `output` (det er ikke nødvendigt at tilføje den `[<Out>]` anmærkning). Ved hjælp af en `byref<>` type gør det muligt for dine funktionen til at ændre, hvilke post eller det objekt, argumentet henviser til.

Når en F #-post, der bruges som en INPUTTYPE, post definitionen skal være mærket med `[<CLIMutable>]` for at tillade Azure funktioner som til at angive felterne korrekt før de sendes i en post til dit funktionen. Under struktur, `[<CLIMutable>]` genererer setters for egenskaberne post. Eksempel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

En F #-klasse kan også bruges til begge ind og ud argumenter. For en klasse skal egenskaber normalt getters og setters. Eksempel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Logføring

Hvis du vil logge output til din [streaming logfiler](../app-service-web/web-sites-streaming-logs-and-console.md) i F #, fører din funktionen et argument af typen `TraceWriter`. For konsistens, anbefaler vi argumentet hedder `log`. Eksempel:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynkron

Den `async` arbejdsproces kan bruges, men resultatet skal returnere en `Task`. Dette kan gøres med `Async.StartAsTask`, f.eks.:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Annullering Token

Hvis funktionen skal håndtere lukning problemfrit, kan du give den et [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument. Dette kan kombineres med `async`, f.eks.:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importere navneområder

Navneområder kan åbnes i den sædvanlige måde:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

De følgende navneområder åbnes automatisk:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Henvisninger til eksterne Assemblies

På samme måde, framework-samling referencer tilføjes med den `#r "AssemblyName"` direktiv.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
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
* `Microsoft.AspNEt.WebHooks.Common`.

Hvis du har brug for til at referere til en privat samling, kan du overføre filen samling i en `bin` mappe i forhold til dine funktionen og reference den ved hjælp af filen navn (f.eks.  `#r "MyAssembly.dll"`). Oplysninger om, hvordan du overfører filer til mappen funktionen, kan du se følgende afsnit på pakke administration.

## <a name="editor-prelude"></a>Editor Prelude

En editor, der understøtter F # compileren tjenester vil ikke være opmærksom på navneområder og samlinger, der automatisk inkluderer Azure funktioner. Som sådanne, kan det være praktisk at medtage en prelude, der hjælper med at finde de enheder, du bruger editoren og udtrykkeligt åbne navneområder. Eksempel:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Når Azure funktioner udfører din kode, det behandler kilden med `COMPILED` defineret, så editor prelude ignoreres.

## <a name="package-management"></a>Pakke administration

Hvis du vil bruge NuGet pakker i en F #-funktionen, skal du tilføje en `project.json` filen til den funktionens mappe i filsystemet appen funktionen. Her er et eksempel `project.json` fil, der tilføjer en NuGet pakke reference til `Microsoft.ProjectOxford.Face` version 1.1.0:

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

Kun .NET Framework 4.6 understøttes, så sørg for, din `project.json` fil angiver `net46` som vist her.

Når du overfører en `project.json` fil, runtime bliver pakkerne og tilføjer automatisk referencer til pakken assemblies. Du behøver ikke at tilføje `#r "AssemblyName"` direktiver. Ved at tilføje de påkrævede `open` sætninger til dit `.fsx` fil.

Du kan eventuelt skal anbringes automatisk referencer assemblies i din editor prelude til at forbedre din editor interaktion med F # samle tjenester.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Sådan føjer du en `project.json` filer til din Azure-funktion

1. Start ved at sikre, at din funktionen app, som du kan gøre ved at åbne din funktionen i portalen Azure, der kører. Dette giver også adgang til loggene streaming hvor pakke installation output vises.

2. Overføre en `project.json` fil ved at bruge en af de metoder, der er beskrevet i [Sådan opdaterer du funktionen app-filer](functions-reference.md#fileupdate). Hvis du bruger [Fortløbende installation til Azure-funktioner](functions-continuous-deployment.md), kan du tilføje en `project.json` filer til din midlertidige gren for at eksperimentere med den, før du føjer den til din installation gren.

3. Når du har den `project.json` fil er tilføjet, kan du se output, der svarer til i følgende eksempel i din funktion streaming log:

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

For at få en miljøvariablen eller en app, angive værdi skal du bruge `System.Environment.GetEnvironmentVariable`, f.eks.:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Genbruge .fsx kode

Du kan bruge kode fra andre `.fsx` filer ved hjælp af en `#load` direktiv. Eksempel:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Stier leverer til den `#load` direktiv er i forhold til placeringen af din `.fsx` fil.

* `#load "logger.fsx"`Indlæser en fil, der er placeret i mappen funktionen.

* `#load "package\logger.fsx"`Indlæser en fil, der er placeret i den `package` mappen med funktionen.

* `#load "..\shared\mylogger.fsx"`Indlæser en fil, der er placeret i den `shared` mappe på det samme niveau som funktionen mappe, det vil sige, direkte under `wwwroot`.

Den `#load` direktiv virker kun med `.fsx` (F # script) filer, og ikke med `.fs` filer.

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger finder du se følgende ressourcer:

* [F # vejledning](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Azure funktioner Udviklerreference](functions-reference.md)
* [Azure funktioner C# Udviklerreference](functions-reference-csharp.md)
* [Azure funktioner NodeJS Udviklerreference](functions-reference-node.md)
* [Azure funktioner udløsere og bindinger](functions-triggers-bindings.md)
* [Azure-test af funktioner](functions-test-a-function.md)
* [Azure funktioner skalering](functions-scale.md)
