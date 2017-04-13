<properties
    pageTitle="Azure funktioner udløsere og bindinger | Microsoft Azure"
    description="Forstå, hvordan du bruger udløsere og bindinger i Azure funktioner."
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
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure funktioner udløsere og bindinger Udviklerreference


Dette emne indeholder generel reference til udløsere og bindinger. Den indeholder nogle af de avancerede binding funktioner og syntaksen, der understøttes af alle binding typer.  

Hvis du leder efter detaljerede oplysninger om konfiguration og kodningssprog en bestemt type udløser eller binding, kan du klikke på en af den udløser eller bindinger nedenfor i stedet:

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Disse artikler forudsætter, at du har læst [Azure funktioner Udviklerreference](functions-reference.md)og [C#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)eller [Node.js](functions-reference-node.md) udvikler reference-artiklerne.



## <a name="overview"></a>Oversigt

Udløsere er begivenhed svar, der bruges til at udløse din brugerdefinerede kode. De gør det muligt at reagere på hændelser på tværs af Azure-platformen eller lokal. Bindinger repræsenterer den nødvendige metadata, der forbinder din kode til den ønskede udløser eller tilknyttede input eller outputdata.

For at få en bedre ide om de forskellige bindinger, du kan integrere med din Azure funktionen app skal referere til den følgende tabel.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

For bedre at forstå udløser og bindinger Antag Generelt, du vil udføre nogle kode til et nyt element, der er placeret i en Azure-lager kø proces. Azure funktioner indeholder en Azure kø udløser til at understøtte dette. Du har brug for, at overvåge køen følgende oplysninger:
 
- Hvor findes kø kontoen lagerplads.
- Kø navnet.
- Et variabelt navn, din kode skal bruge til at referere til det nye element, som blev afbrudt i køen.  
 
En kø udløser binding indeholder disse oplysninger til en Azure-funktion. Filen *function.json* for hver funktion indeholder alle relaterede bindinger.  Her er et eksempel *function.json* , der indeholder en kø udløse binding. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

Din kode kan sende forskellige typer output afhængigt af hvordan det nye element i kø behandles. For eksempel kan du vil skrive en ny post til en tabel med Azure-lager.  For at gøre dette, kan du konfigurere en outputbinding til en tabel med Azure-lager. Her er et eksempel *function.json* , der indeholder en lagerplads tabel outputbinding, der kan bruges med en kø udløser. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


Den følgende C#-funktion besvarer et nyt element, der placeres i køen og skriver en ny bruger post i en tabel med Azure-lager.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Se flere kodeeksempler og mere specifikke oplysninger om Azure lagerplads datatyper, der understøttes, [Azure funktioner udløsere og bindinger for Azure-lager](functions-bindings-storage.md).


Klik på indstillingen **Avanceret editor** under fanen **integrere** din fordelingsfunktion for at bruge funktionerne til mere avanceret binding i portalen Azure skal. Avanceret editor kan du redigere *function.json* direkte i portalen.

## <a name="dynamic-parameter-binding"></a>Dynamisk parameter binding 

I stedet for en statisk Konfiguration indstilling for output binding egenskaber, kan du konfigurere indstillingerne for skal være dynamisk bundet til data, som er en del af din udløser input binding. Forestil dig en situation, hvor nye ordrer behandles ved hjælp af en Azure-lager kø. Hver ny kø vare er JSON streng, der indeholder mindst følgende egenskaber:

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Du vil sende kunden en SMS-tekstmeddelelse, ved hjælp af din Twilio konto som en opdatering, rækkefølgen blev modtaget.  Du kan konfigurere den `body` og `to` felt i din Twilio output binding skal være dynamisk bundet til den `name` og `mobileNumber` , der er en del af input på følgende måde.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
Din funktionen kode har nu kun initialiseret Outputparameteren på følgende måde. Under kørsel af bindes egenskaberne output til den ønskede indtastede data.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>Tilfældige GUID

Azure funktioner angiver syntaksen til at generere tilfældige GUID med din bindinger. Følgende binding syntaks skrive output til en ny BLOB med et entydigt navn i en objektbeholder til lagring af Azure: 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Returnerer en enkelt output

I tilfælde, hvor funktionskoden returnerer en enkelt output, kan du bruge en outputbinding med navnet `$return` til at bevare en mere naturlig funktionen signatur i din kode. Dette kan kun bruges med sprog, der understøtter en returværdi (C#, Node.js, F #). Bindingen ville være svarende til følgende blob outputbinding, der bruges sammen med en kø udløser.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


Den følgende C#-kode returnerer output mere naturligt uden at bruge en `out` parameter i funktionen signaturen.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Denne samme metode er vist nedenfor med Node.js.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # eksempel angivet nedenfor.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Dette kan også bruges sammen med flere outputparametre ved at angive en enkelt output med `$return`.


## <a name="route-support"></a>Distribuere support

Som standard når du opretter en funktion for en HTTP udløser eller WebHook, er funktionen tilgængelige med en rute i formularen:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Du kan tilpasse dette ved hjælp af det valgfri `route` egenskab på HTTP-udløser input binding. Som eksempel følgende *function.json* fil definerer en `route` egenskab for en HTTP udløser:

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

Med denne konfiguration, er funktionen nu tilgængelige med følgende ruten i stedet for den oprindelige rute.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Dette giver mulighed for funktionen kode til at understøtte to parametre i adressen, `category` og `id`. Du kan bruge en hvilken som helst [Web API distribuere begrænsning](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) med din parametre. Følgende C# funktionen kode gør brug af begge parametre.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Her er Node.js funktionen kode til at bruge de samme distribuere parametre.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Som standard er alle funktionen omdirigerer præfikset *api*. Du kan også tilpasse eller fjerne præfikset ved hjælp af den `http.routePrefix` egenskab i filen *host.json* . I følgende eksempel fjerner *api* distribuere præfikset ved hjælp af en tom streng til præfikset i filen *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Du kan finde detaljerede oplysninger om, hvordan du opdaterer filen *host.json* for funktionen, se, [hvordan du kan opdatere funktionen app filer](functions-reference.md#fileupdate). 

Funktionen er nu tilgængelige med følgende ruten ved at tilføje denne konfiguration:

    http://<yourapp>.azurewebsites.net/products/electronics/357

Du kan finde oplysninger om andre egenskaber, du kan konfigurere i filen *host.json* [host.json reference](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Næste trin

Yderligere oplysninger finder du se følgende ressourcer:

* [Test af en funktion](functions-test-a-function.md)
* [Tilpasse en funktion](functions-scale.md)
