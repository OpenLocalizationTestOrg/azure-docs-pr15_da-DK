<properties
    pageTitle="Azure HTTP-funktioner og webhook bindinger | Microsoft Azure"
    description="Forstå, hvordan du bruger HTTP og webhook udløsere og bindinger i Azure funktioner."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Azure HTTP-funktioner og webhook bindinger

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel beskrives det, hvordan du konfigurerer og kode HTTP og webhook udløsere og bindinger i Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON for HTTP- og webhook bindinger

Filen *function.json* indeholder egenskaber, der vedrører både anmodning og svar.

Egenskaber for HTTP-anmodningen:

- `name`: Variable navn, der bruges i funktionen kode for request-objektet (eller anmodningsteksten for Node.js funktioner).
- `type`: Skal være indstillet til *httpTrigger*.
- `direction`: Skal være indstillet til *i*. 
- `webHookType`: Gyldige værdier er for WebHook udløsere *github*, *slæk*og *genericJson*. Angive denne egenskab for en HTTP-udløser, der ikke er en WebHook, til en tom streng. Du kan finde flere oplysninger om WebHooks, afsnittet nedenfor [WebHook udløsere](#webhook-triggers) .
- `authLevel`: Ikke gælder for WebHook udløsere. Indstillet til "funktionen" til at kræve tasten API, "anonym" Slip API vigtige krav eller "admin" til at kræve den overordnede API-nøgle. Du kan få flere oplysninger i [API taster](#apikeys) nedenfor.

Egenskaber for HTTP-svar:

- `name`: Variable navn, der bruges i funktionen kode for objektet svar.
- `type`: Skal være indstillet til *http*.
- `direction`: Skal være indstillet til *ud*. 
 
Eksempel på *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>WebHook udløsere

En WebHook udløser er en HTTP-udløser, der er udviklet til WebHooks følgende funktioner:

* For bestemte WebHook udbydere (aktuelt GitHub og slæk understøttes) funktioner runtime validerer udbyderens signatur.
* For Node.js funktioner indeholder funktioner runtime anmodningsteksten i stedet for request-objektet. Der er ingen særbehandling for C#-funktioner, da du styre, hvad leveres ved at angive parametertypen. Hvis du angiver `HttpRequestMessage` du får request-objektet. Hvis du angiver typen af POCO, forsøger til kørsel af funktioner til at fortolke en JSON objekt i brødteksten i anmodningen til at udfylde objektegenskaberne.
* Hvis du vil udløse en WebHook skal funktionen HTTP-anmodningen indeholde en API-nøgle. Dette krav er valgfrit for WebHook HTTP udløsere.

Du kan finde oplysninger om, hvordan du konfigurerer en GitHub WebHook [GitHub udvikler - oprettelse af WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL-adressen til udløser funktionen

Hvis du vil udløse en funktion, sender du en HTTP-anmodning til en URL-adresse, der er en kombination af funktionen app URL-adressen og funktionens navn:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>API taster

Som standard skal en API-nøgle bruges sammen med en HTTP-anmodning til at udløse en HTTP eller WebHook funktion. Tasten skal indgå i en forespørgsel strengvariabel med navnet `code`, eller den kan medtages i en `x-functions-key` HTTP sidehoved. For ikke-WebHook funktioner, du kan angive, at en API-nøgle ikke er påkrævet ved at angive den `authLevel` egenskab, der skal "anonym" i filen *function.json* .

Du kan finde API nøgleværdier i mappen *D:\home\data\Functions\secrets* i filsystemet af funktionen app.  Master nøgle og funktionstast er angivet i filen *host.json* , som vist i dette eksempel. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

Funktionstast fra *host.json* kan bruges til at udløse en funktion, men udløse ikke en deaktiveret funktionen. Tasten master kan bruges til at udløse en funktion og vil udløse en funktion, selvom den er deaktiveret. Du kan konfigurere en funktion for at kræve tasten master ved at angive den `authLevel` egenskab til "administrator". 

Hvis mappen *hemmeligheder* indeholder en JSON-fil med samme navn som en funktion, den `key` egenskab i filen kan også bruges til at udløse funktionen, og denne tast fungerer kun med den funktion, det henviser til. For eksempel tasten API til en funktion med navnet `HttpTrigger` er angivet i *HttpTrigger.json* i mappen *hemmeligheder* . Her er et eksempel:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Når du konfigurerer en WebHook udløser, ikke dele tasten master med provideren WebHook. Bruge en nøgle, der kun fungerer med den funktion, der behandler WebHook.  Tasten master kan bruges til at udløse en funktion, selvom deaktiveret funktioner.

## <a name="example-c-code-for-an-http-trigger-function"></a>C#-eksempelkode for en HTTP Udløs funktion 

På eksempelkoden søger efter en `name` parameter i forespørgselsstrengen eller brødteksten i HTTP-anmodningen.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>F #-eksempelkode for en HTTP Udløs funktion

På eksempelkoden søger efter en `name` parameter i forespørgselsstrengen eller brødteksten i HTTP-anmodningen.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Du skal bruge en `project.json` fil, der bruger NuGet refereres til den `FSharp.Interop.Dynamic` og `Dynamitey` assemblies, således:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Dette vil bruge NuGet til at hente dine afhængigheder og henvise dem i dit script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Eksempel Node.js kode for en HTTP Udløs funktion 

Dette eksempelkode søger efter en `name` parameter i forespørgselsstrengen eller brødteksten i HTTP-anmodningen.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>C#-eksempelkode for en GitHub WebHook funktion 

Dette eksempelkode logger GitHub problem kommentarer.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>På F #-eksempelkode for en GitHub WebHook funktion

Dette eksempelkode logger GitHub problem kommentarer.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Eksempel Node.js kode for en GitHub WebHook funktion 

Dette eksempelkode logger GitHub problem kommentarer.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
