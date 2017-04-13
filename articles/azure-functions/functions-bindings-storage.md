<properties
    pageTitle="Azure funktioner udløsere og bindinger for Azure-lager | Microsoft Azure"
    description="Forstå, hvordan du bruger Azure-lager udløsere og bindinger i Azure funktioner."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fungerer, funktioner, begivenhed behandling, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure funktioner udløsere og bindinger for Azure-lager

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Denne artikel forklares, hvordan du konfigurerer og kode Azure-lager udløsere og bindinger i Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure lagerplads kø udløser

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON for lagerplads kø udløser

Filen *function.json* angiver følgende egenskaber.

- `name`: Variabelnavn, der er brugt i funktionen kode i køen eller kø meddelelsen. 
- `queueName`: Navnet på den kø til afstemning. Reglerne for navngivning af kø, under [navngivning af køer og Metadata](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Navnet på en app-indstilling, der indeholder en lagerplads forbindelsesstreng. Hvis du lader `connection` tomme, udløseren fungerer sammen med standard lagerplads forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsStorage app.
- `type`: Skal være indstillet til *queueTrigger*.
- `direction`: Skal være indstillet til *i*. 

Eksempel på *function.json* for en lagerplads kø udløser:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Kø udløser understøttede datatyper

Kø meddelelsen kan deserialiseres til en af følgende typer:

* Objekt (fra JSON)
* Streng
* Bytematrix 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Kø udløser metadata

Du kan få kø metadata i din funktion ved hjælp af disse variabelnavne:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (en anden måde at hente meddelelsesteksten kø som en streng)

Dette C#-kodeeksempel henter og logge kø metadata:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Håndtering af en kø meddelelser

Meddelelser, der medfører en funktion til at mislykkes, hvis indhold kaldes *uautoriserede meddelelser*. Når funktionen ikke, slettes ikke kø meddelelsen, og til sidst er valgte igen, forårsager cyklussen skal gentages. SDK kan automatisk afbryde cyklussen efter et begrænset antal gentagelser, eller du kan gøre det manuelt.

SDK kalder en funktion op til 5 gange til at behandle en kø meddelelse. Hvis det femte prøve mislykkes, flyttes meddelelsen til en en kø.

En køen hedder *{originalqueuename}*-en. Du kan skrive, der er brug for en funktion til at behandle meddelelser fra en køen ved logføring dem eller sende en meddelelse, der manuel opmærksomhed. 

Hvis du vil håndtere uautoriserede meddelelser manuelt, kan du få antallet gange en meddelelse er blevet valgte til behandling ved at markere `dequeueCount`.

## <a id="storagequeueoutput"></a>Azure lagerplads kø output binding

#### <a name="functionjson-for-storage-queue-output-binding"></a>Function.JSON for lagerplads kø output binding

Filen *function.json* angiver følgende egenskaber.

- `name`: Variabelnavn, der er brugt i funktionen kode i køen eller kø meddelelsen. 
- `queueName`: Navnet på køen. Reglerne for navngivning af kø, under [navngivning af køer og Metadata](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Navnet på en app-indstilling, der indeholder en lagerplads forbindelsesstreng. Hvis du lader `connection` tomme, udløseren fungerer sammen med standard lagerplads forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsStorage app.
- `type`: Skal være indstillet til *kø*.
- `direction`: Skal være indstillet til *ud*. 

Eksempel på *function.json* for en lagerplads kø output indbinding, der bruger en kø udløser og skriver en kø meddelelse:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Kø outputbinding understøttede datatyper

Den `queue` binding kan sekventielt følgende typer til en kø meddelelse:

* Objekt (`out T` i C#, du opretter en meddelelse med en null-objekt, hvis parameteren er null, når funktionen stopper)
* Streng (`out string` i C#, opretter kø meddelelse, hvis parameterværdi er ikke-null, når funktionen stopper)
* Bytematrix (`out byte[]` i C#, fungerer ligesom streng) 
* `out CloudQueueMessage`(C#, fungerer som streng) 

I C# kan du også binde til `ICollector<T>` eller `IAsyncCollector<T>` hvor `T` er et af de understøttede datatyper.

#### <a name="queue-output-binding-code-examples"></a>Kø output binding kodeeksempler

Dette C#-kodeeksempel skriver en enkelt output kø meddelelse til alle de meddelelser, input kø.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Dette C#-kodeeksempel skriver flere meddelelser ved hjælp af `ICollector<T>` (Brug `IAsyncCollector<T>` i en asynkron funktion):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Azure lagerplads blob udløser

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON for lagerplads blob udløser

Filen *function.json* angiver følgende egenskaber.

- `name`: Variabelnavn, der anvendes i funktionen kode til blob. 
- `path`: En sti, som angiver objektbeholder til at overvåge, og du kan vælge et mønster med blob navn.
- `connection`: Navnet på en app-indstilling, der indeholder en lagerplads forbindelsesstreng. Hvis du lader `connection` tomme, udløseren fungerer sammen med standard lagerplads forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsStorage app.
- `type`: Skal være indstillet til *blobTrigger*.
- `direction`: Skal være indstillet til *i*.

Eksempel på *function.json* for en lagerplads blob udløser, der ser for blob, der er føjet til objektbeholderen eksempler workitems:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>BLOB udløser understøttede datatyper

Blob kan deserialiseres til en af følgende typer i Node eller C# funktioner:

* Objekt (fra JSON)
* Streng

I C#-funktioner kan du også bindes til et af følgende typer:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Andre typer af [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>BLOB udløser C#-kodeeksempel

Dette C#-kodeeksempel registrerer indholdet af hver blob, der er føjet til objektbeholderen overvåget.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>BLOB udløser navn mønstre

Du kan angive et mønster til blob navn i den `path` egenskab. Eksempel:

```json
"path": "input/original-{name}",
```

Denne sti vil finde en blob med navnet *Oprindelige Blob1.txt* i objektbeholderen *input* , og værdien af den `name` variabel i funktionen kode ville være `Blob1`.

Et andet eksempel:

```json
"path": "input/{blobname}.{blobextension}",
```

Denne sti vil også finde en blob med navnet *Oprindelige Blob1.txt*og værdien af den `blobname` og `blobextension` variabler i funktionen kode ville være *Oprindelige Blob1* og *txt*.

Du kan begrænse typerne blob, der udløser funktionen ved at angive et mønster med en fast værdi for filtypenavnet. Hvis du angiver den `path` *til/eksempler / {navn} .png*, der kun *.png* BLOB i beholderen *eksempler* udløser funktionen.

Hvis du vil angive et navn mønster for blob navne, der har klammeparenteser i navnet, skal du dobbeltklikke på klammeparenteser. Eksempelvis hvis du vil finde BLOB i beholderen *billeder* , der har navne således:

        {20140101}-soundfile.mp3

Brug dette til den `path` egenskab:

        images/{{20140101}}-{name}

I eksemplet på `name` variable værdi, der ville være *soundfile.mp3*. 

#### <a name="blob-receipts"></a>BLOB kvitteringer

Azure funktioner runtime sikrer, at, ingen blob Udløs funktion bliver kaldet mere end én gang til den samme nye eller opdaterede blob. Det gør dette ved at vedligeholde *blob kvitteringer* for at bestemme, om en given blobversion er blevet behandlet.

BLOB modtagerne er gemt i en objektbeholder med navnet *azure-webjobs-værter* i den angivne ved forbindelsesstrengen AzureWebJobsStorage Azure-lager-konto. Kvittering blob har følgende oplysninger:

* Den funktion, der hedder for blob ("*{funktionsnavn app}*. Funktioner. *{funktionsnavn}*", for eksempel:"functionsf74b96f7. Functions.CopyBlob")
* Objektbeholdernavnet
* Typen blob ("BlockBlob" eller "PageBlob")
* Blob-navn
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

Hvis du vil tvinge oparbejdning af en blob, kan du manuelt slette blob kvittering for denne blob fra *azure-webjobs-værter* objektbeholderen.

#### <a name="handling-poison-blobs"></a>Håndtering af en BLOB

Når en blob udløser funktion ikke, i SDK ringer op til det igen, i tilfælde af fejlen skyldes en midlertidig fejl. Hvis fejlen skyldes indholdet af blob, mislykkes funktionen, hver gang den forsøger at behandle blob. Som standard kalder i SDK en funktion op til 5 gange for en given blob. Hvis det femte prøve mislykkes, føjer SDK en meddelelse til en kø med navnet *webjobs-blobtrigger-forfalskning*.

Kø meddelelsen til en BLOB er et JSON-objekt, der indeholder følgende egenskaber:

* FunctionId (i format *{funktionsnavn app}*. Funktioner. *{funktionsnavn}*)
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

#### <a name="blob-polling-for-large-containers"></a>BLOB forespørges efter store beholdere

Hvis objektbeholderen blob, som udløseren overvåger indeholder mere end 10.000 blob, logfiler funktioner runtime søgninger til at holde øje med nye eller ændrede BLOB. Denne proces er ikke realtid; en funktion kan ikke udløses indtil nogle minutter eller længere, når blob er blevet oprettet. Desuden [lagerplads logfiler er oprettet på en "bedste anstrengelser"](https://msdn.microsoft.com/library/azure/hh343262.aspx) datotype; der er ingen garanti, der registreres alle begivenheder. Under visse omstændigheder kan være mistede logfiler. Hvis hastighed og pålidelighed begrænsningerne i blob udløsere af store objektbeholdere ikke er acceptable for dit program, er den anbefalede metode til at oprette en kø meddelelse, når du opretter blob og bruge en kø udløser i stedet for en blob udløser til at behandle blob.
 
## <a id="storageblobbindings"></a>Azure lagerplads blob input og output bindinger

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON for en blob storage input eller output binding

Filen *function.json* angiver følgende egenskaber.

- `name`: Variabelnavn, der anvendes i funktionen kode til blob. 
- `path`: En sti, som angiver objektbeholderen for at læse blob fra eller skrive blob til, og du kan vælge et mønster med blob navn.
- `connection`: Navnet på en app-indstilling, der indeholder en lagerplads forbindelsesstreng. Hvis du lader `connection` tomme, bindingen, der fungerer sammen med standard lagerplads forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsStorage app.
- `type`: Skal være indstillet til *blob*.
- `direction`: Indstillet til *ind* eller *ud*. 

Eksempel på *function.json* for en lagerplads blob input eller output indbinding, bruge en kø udløser til at kopiere en blob:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>BLOB input- og outputområder understøttede datatyper

Den `blob` binding kan sekventielt eller deserialisere følgende typer i Node.js eller C# funktioner:

* Objekt (`out T` i C# til output blob: opretter en blob som null-objekt, hvis parameterværdi er null, når funktionen stopper)
* Streng (`out string` i C# til output blob: opretter en blob kun, hvis Strengparameteren er ikke-null, når funktionen returnerer)

I C#-funktioner, kan du også binde til følgende typer:

* `TextReader`(kun input)
* `TextWriter`(kun output)
* `Stream`
* `CloudBlobStream`(kun output)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>BLOB output C#-kodeeksempel

Dette C#-kodeeksempel kopierer en blob, hvis navn er modtaget i en kø meddelelse.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Azure lagerplads tabeller input og output bindinger

#### <a name="functionjson-for-storage-tables"></a>Function.JSON for lagerplads tabeller

*Function.json* angiver følgende egenskaber.

- `name`: Det variable navn, der bruges i funktionen kode for tabelbinding. 
- `tableName`: Navnet på tabellen.
- `partitionKey`og `rowKey` : bruges sammen til at læse et enkelt objekt i en C# eller Node funktion eller skrive et enkelt objekt i en Node funktion.
- `take`: Det maksimale antal rækker, der skal læse for tabel, der er indtastet i en Node funktion.
- `filter`: OData-filterudtrykket til tabel, der er indtastet i en Node funktion.
- `connection`: Navnet på en app-indstilling, der indeholder en lagerplads forbindelsesstreng. Hvis du lader `connection` tomme, bindingen, der fungerer sammen med standard lagerplads forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsStorage app.
- `type`: Skal være indstillet til *tabel*.
- `direction`: Indstillet til *ind* eller *ud*. 

I følgende eksempel *function.json* bruger en kø udløser til at læse en enkelt tabelrække. JSON giver en faste partition nøgleværdi og angiver, at tasten række kommer fra kø meddelelsen.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Lagerplads tabeller input og output-understøttede datatyper

Den `table` binding kan sekventielt eller deserialisere objekter i Node.js eller C#-funktioner. Objekterne, der har RowKey og PartitionKey egenskaber. 

I C#-funktioner, kan du også bindes til følgende typer:

* `T`hvor `T` implementerer`ITableEntity`
* `IQueryable<T>`(kun input)
* `ICollector<T>`(kun output)
* `IAsyncCollector<T>`(kun output)

#### <a name="storage-tables-binding-scenarios"></a>Lagerplads tabeller binding scenarier

Tabelbinding understøtter følgende scenarier:

* Læse en enkelt række i en funktion C# eller Node.

    Angive `partitionKey` og `rowKey`. Den `filter` og `take` egenskaber ikke bruges i dette scenarie.

* Læs flere rækker i en C#-funktion.

    Funktioner runtime indeholder en `IQueryable<T>` objekt bundet til tabellen. Skriv `T` skal være afledt af `TableEntity` eller implementere `ITableEntity`. Den `partitionKey`, `rowKey`, `filter`, og `take` egenskaber ikke bruges i dette scenarie Du kan bruge den `IQueryable` objekt til at udføre en hvilken som helst filtrering, der er påkrævet. 

* Læs flere rækker i en Node funktion.

    Angiv den `filter` og `take` egenskaber. Ikke angiver `partitionKey` eller `rowKey`.

* Skriv en eller flere rækker i en C#-funktion.

    Funktioner runtime indeholder en `ICollector<T>` eller `IAsyncCollector<T>` bundet til tabellen, hvor `T` angiver skemaet for de enheder, du vil tilføje. Skriv typisk `T` stammer fra `TableEntity` eller implementerer `ITableEntity`, men ikke være til. Den `partitionKey`, `rowKey`, `filter`, og `take` egenskaber ikke bruges i dette scenarie.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Eksempel på lagerplads tabeller: læse en enkelt tabel enhed i C# eller Node

C#-kodeeksemplet nedenfor fungerer med den foregående *function.json* -fil, der vises tidligere at læse en enkelt tabel-enhed. Kø-meddelelse med den række nøgleværdi, og tabel enheden skal skrives i en type, der er defineret i filen *run.csx* . Typen omfatter `PartitionKey` og `RowKey` egenskaber og er ikke afledt af `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

F #-kodeeksemplet nedenfor fungerer også med filen foregående *function.json* at læse en enkelt tabel-enhed.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

Følgende eksempel på Node kode fungerer også med filen foregående *function.json* at læse en enkelt tabel-enhed.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Eksempel på lagerplads tabeller: Læs flere tabel enheder i C# 

Følgende *function.json* og C# kode eksempel læser enheder til en Partitionsnøgle, der er angivet i kø meddelelsen.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C#-kode tilføjer en reference til Azure-lager SDK, så enhedstypen kan være afledt af `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Eksempel på lagerplads tabeller: oprette tabel objekter i C# 

I følgende eksempel *function.json* og *run.csx* viser, hvordan du kan skrive tabel objekter i C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Eksempel på lagerplads tabeller: oprette tabel objekter i F#

I følgende eksempel *function.json* og *run.fsx* viser, hvordan du kan skrive tabel objekter i F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Eksempel på lagerplads tabeller: oprette en tabel enhed i Node

I følgende eksempel *function.json* og *run.csx* viser, hvordan du kan skrive en tabel enhed i Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
