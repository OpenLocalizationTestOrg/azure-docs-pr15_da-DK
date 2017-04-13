<properties
    pageTitle="Azure funktioner NodeJS Udviklerreference | Microsoft Azure"
    description="Forstå, hvordan du udvikler Azure funktioner ved hjælp af NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure-funktioner, funktioner, behandling af hændelse, webhooks, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Azure funktioner NodeJS Udviklerreference

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [F # script](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

Node/JavaScript-oplevelsen til Azure funktioner gør det nemt at eksportere en funktion, der overføres en `context` objekt til at kommunikere med runtime og for at modtage og sende data via bindinger.

Denne artikel forudsætter, at du allerede har læst [Azure funktioner Udviklerreference](functions-reference.md).

## <a name="exporting-a-function"></a>Eksportere en funktion

Alle JavaScript-funktioner skal eksportere en enkelt `function` via `module.exports` til runtime til at finde funktionen, og kør den. Denne funktion altid skal indeholde en `context` objekt.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Bindinger af `direction === "in"` sendes langs som argumenter til funktionen, hvilket betyder, at du kan bruge [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) til at håndtere dynamisk nye input (for eksempel ved hjælp af `arguments.length` til forbedrer over alle dine input). Denne funktionalitet er praktisk, hvis du kun har en udløser med intet yderligere input, som du kan forudsigeligt få adgang til dataene udløser uden refererer til din `context` objekt.

Argumenterne er altid overføres langs til funktionen i den rækkefølge, de opstår i *function.json*, selvom du ikke angiver dem i din eksport-sætningen. For eksempel, hvis du har `function(context, a, b)` og ændre den til `function(context, a)`, du kan stadig få værdien af `b` i funktionen kode ved at referere til `arguments[3]`.

Alle bindinger, uanset retning, der også overføres langs den `context` objekt (se nedenfor). 

## <a name="context-object"></a>kontekstobjekt

Kørsel af anvender en `context` objekt til at overføre data til og fra din funktionen, og gør det muligt at kommunikere med runtime.

Objektet kontekst er altid den første parameter til en funktion og skal altid medtages fordi den har metoder såsom `context.done` og `context.log` , er påkrævet for at bruge korrekt på kørselstidspunktet. Du kan navngive objektet, hvad du har lyst (det vil sige `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>context.bindings

Den `context.bindings` objekt indsamler alle dine input- og outputområder data. Dataene føjes til den `context.bindings` objekt den `name` egenskab for bindingen. For eksempel fremgår af følgende binding definitionen *function.json*, du kan få adgang til indholdet af kø via `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

Den `context.done` funktionen fortæller til kørsel af, at du er færdig, der kører. Dette er vigtigt at kalde, når du er færdig med funktionen; Hvis du ikke ved kørsel af stadig aldrig, funktionen fuldført. 

Den `context.done` funktion gør det muligt at overføre fejlen brugerdefinerede tilbage til runtime samt en egenskab pose med egenskaber, overskriver egenskaberne på den `context.bindings` objekt.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>context.log(Message)

Den `context.log` metoden gør muligt at output log sætninger, der mellem sammen til logføring formål. Hvis du bruger `console.log`, dine meddelelser vises kun for processen niveau logføring, som ikke er så nyttige.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

Den `context.log` metode understøtter det samme parameter format, der understøtter den Node [util.format metode](https://nodejs.org/api/util.html#util_util_format_format) . Så, for eksempel kode således:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

kan skrives således:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>HTTP-udløsere: context.req og context.res

Hvis det er HTTP udløsere, fordi det er et almindelige mønster bruge `req` og `res` for HTTP-anmodning og svar objekterne, vi besluttet at gør det nemt at få adgang til dem på objektet kontekst, i stedet for at tvinge du kan bruge fuldt `context.bindings.name` mønster.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Node Version & pakke Management

Node versionen er låst i øjeblikket på `5.9.1`. Vi er ved at undersøge tilføje understøttelse af flere versioner og gør det konfigurerbar.

Du kan medtage pakker i din funktion ved at uploade filen *package.json* til din funktionen mappe i appen funktionen filsystemet. Overføre instruktioner til filen, skal du se afsnittet **Sådan opdaterer du funktionen app-filer** i [Azure funktioner udvikler reference emne](functions-reference.md#fileupdate). 

Du kan også bruge `npm install` i appen funktionen SCM (Kudu) linje kommandogrænseflade:

1. Gå til: `https://<function_app_name>.scm.azurewebsites.net`.

2. Klik på **fejlfinding Console > CMD**.

3. Gå til `D:\home\site\wwwroot\<function_name>`.

4. Køre `npm install`.

Når de ønskede pakker er installeret, skal du importere dem til din funktion på sædvanlige måder (det vil sige via `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Miljøvariabler

For at få en miljøvariablen eller en app, angive værdi skal du bruge `process.env`, som vist i følgende kodeeksempel:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Understøttelse af maskine/CoffeeScript

Der er ikke, men eventuelle direkte understøttelse af automatisk-kompilering maskine/CoffeeScript via runtime, så, vil alle skal håndteres uden for kørsel, fra det øjeblik. 

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger finder du se følgende ressourcer:

* [Azure funktioner Udviklerreference](functions-reference.md)
* [Azure funktioner C# Udviklerreference](functions-reference-csharp.md)
* [Azure funktioner F # Udviklerreference](functions-reference-fsharp.md)
* [Azure funktioner udløsere og bindinger](functions-triggers-bindings.md)
