<properties 
    pageTitle="Sådan bruger du Azure blob-lager med WebJobs SDK" 
    description="Lær at bruge Azure blob-lager med WebJobs SDK. Udløse en proces, når en ny blob vises i en objektbeholder og håndtere 'en BLOB'." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Sådan bruger du Azure blob-lager med WebJobs SDK

## <a name="overview"></a>Oversigt

Denne vejledning indeholder C# kodeeksempler, der viser, hvordan du kan udløse en proces, når en Azure blob oprettes eller opdateres. Eksemplerne i brug [WebJobs SDK](websites-dotnet-webjobs-sdk.md) version 1.x.

For kodeeksempler, der viser, hvordan du opretter blob, se, [hvordan du bruger Azure kø storage med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
Vejledningen antages det, at du ved, [hvordan du opretter et WebJob projekt i Visual Studio med strenge, der peger på kontoen lagerplads](websites-dotnet-webjobs-sdk-get-started.md) eller til [flere lagerplads konti](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Hvordan du kan udløse en funktion, når der oprettes eller opdateres en blob

Dette afsnit viser, hvordan du bruger den `BlobTrigger` attributter. 

> [AZURE.NOTE] WebJobs SDK scanner logfiler for at holde øje med nye eller ændrede BLOB. Denne proces er ikke realtid; en funktion kan ikke udløses indtil nogle minutter eller længere, når blob er blevet oprettet. Desuden [lagerplads logfiler er oprettet på en "bedste anstrengelser"](https://msdn.microsoft.com/library/azure/hh343262.aspx) datotype; der er ingen garanti, der registreres alle begivenheder. Under visse omstændigheder kan være mistede logfiler. Hvis blob udløsere hastighed og pålidelighed begrænsninger ikke er acceptable for dit program, er den anbefalede metode til at oprette en kø meddelelse, når du opretter blob og bruge attributten [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) i stedet for den `BlobTrigger` attribut på den funktion, der behandler blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Enkelt pladsholder for blob navn med filtypenavn  

Følgende eksempel-kode kopierer tekst blob, der vises i objektbeholderen *input* til objektbeholderen *output* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Attributkonstruktøren tager en strengparameter, der angiver objektbeholder navn og en pladsholder for navnet på blob. Hvis en blob med navnet *Blob1.txt* er oprettet i objektbeholderen *input* opretter funktionen i dette eksempel en blob med navnet *Blob1.txt* i beholderen *output* . 

Du kan angive et navn mønster med pladsholderen blob navn, som vist i følgende eksempel-kode:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Denne kode kopieres kun blob, der har navne, der begynder med "oprindelige-". For eksempel kopieres *Oprindelige Blob1.txt* i beholderen *input* til *Kopiér Blob1.txt* i beholderen *output* .

Hvis du vil angive et navn mønster for blob navne, der har klammeparenteser i navnet, skal du dobbeltklikke på klammeparenteser. Eksempelvis hvis du vil finde BLOB i beholderen *billeder* , der har navne således:

        {20140101}-soundfile.mp3

Brug denne til din mønster:

        images/{{20140101}}-{name}

I eksemplet med ville værdien fra *name* pladsholder være *soundfile.mp3*. 

### <a name="separate-blob-name-and-extension-placeholders"></a>Separat blob navn og et lokalnummer pladsholdere

Følgende eksempel-kode ændrer filtypen, som den kopierer blob, der vises i objektbeholderen *input* til objektbeholderen *output* . Koden logger udvidelse af *input* blob og angiver udvidelsen af *output* blob til *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a id="types"></a>Datatyper, som du kan binde til BLOB

Du kan bruge den `BlobTrigger` attribut på følgende typer:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Andre typer af [ICloudBlobStreamBinder](#icbsb) 

Hvis du vil arbejde direkte med Azure-lager-konto, du kan også tilføje en `CloudStorageAccount` -parameter til metode signatur.

Du kan finde eksempler, [blob binding kode i azure-webjobs-sdk-lager på GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Få tekst blob indhold ved binding til streng

Hvis tekst BLOB forventes, `BlobTrigger` kan anvendes på en `string` parameter. Følgende eksempel-kode binder blob en tekst til en `string` parameter med navnet `logMessage`. Funktionen bruger denne parameter til at skrive indholdet af blob til WebJobs SDK dashboard. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Få serialiseret blob indhold ved hjælp af ICloudBlobStreamBinder

Følgende eksempel-kode bruger en klasse, der implementerer `ICloudBlobStreamBinder` til at aktivere den `BlobTrigger` attribut binde en blob til den `WebImage` type.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK", 
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Den `WebImage` binding kode er angivet i en `WebImageBinder` klasse, der stammer fra `ICloudBlobStreamBinder`.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input, 
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Få blob stien til den udløsende blob

Få objektbeholder navn og blob navnet på den blob, som har udløste funktionen, skal du medtage en `blobTrigger` streng parameter i funktionen signaturen.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>Sådan håndteres en BLOB

Når en `BlobTrigger` funktionen opstår SDK kalder den igen, hvis fejlen skyldes en midlertidig fejl. Hvis fejlen skyldes indholdet af blob, mislykkes funktionen, hver gang den forsøger at behandle blob. Som standard kalder i SDK en funktion op til 5 gange for en given blob. Hvis det femte prøve mislykkes, føjer SDK en meddelelse til en kø med navnet *webjobs-blobtrigger-forfalskning*.

Det maksimale antal forsøg kan konfigureres. Den samme [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) indstilling bruges til håndtering af en blob og håndtering af en kø meddelelser. 

Kø meddelelsen til en BLOB er et JSON-objekt, der indeholder følgende egenskaber:

* FunctionId (i formatet *{WebJob name}*. Funktioner. *{Funktionsnavn}*, for eksempel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

I følgende eksempel-kode, den `CopyBlob` funktionen indeholder kode, der medfører, at det mislykkes, hver gang den hedder. Når SDK kalder den til det maksimale antal forsøg, en meddelelse er oprettet i en blob køen og meddelelsen behandles af den `LogPoisonBlob` funktionen. 

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }
        
        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK deserializes automatisk JSON meddelelsen. Her er de `PoisonBlobMessage` klasse: 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>BLOB afstemning algoritme

WebJobs SDK scanner alle beholdere, der er angivet af `BlobTrigger` attributter i starten af programmet. I en stor storage konto denne scanning kan gå et stykke tid, så det kan være et stykke tid, før der blev fundet nye BLOB og `BlobTrigger` funktioner udføres.

For at registrere nye eller ændrede BLOB efter program start, læser SDK med jævne mellemrum fra loggene blob-lager. Loggene blob i bufferen og kun få fysisk skrevet hver 10 minutter eller så, så der kan være betydeligt forsinkelse efter en blob oprettes eller opdateres før den tilhørende `BlobTrigger` funktionen udfører. 

Der er en undtagelse for blob, som du opretter ved hjælp af den `Blob` attributter. Når WebJobs SDK opretter en ny blob, den passerer nye blob med det samme som en hvilken som helst svarer til `BlobTrigger` funktioner. Derfor Hvis du har en kæde af blob input og output, kan SDK behandle dem effektivt. Men hvis du vil lave ventetid kører din blob behandlingsfunktioner til BLOB, der er oprettet eller opdateret på anden måde, det anbefales at bruge `QueueTrigger` i stedet `BlobTrigger`.

### <a id="receipts"></a>BLOB kvitteringer

WebJobs SDK sikrer, at der ingen `BlobTrigger` funktionen bliver kaldet mere end én gang til den samme nye eller opdaterede blob. Det gør dette ved at vedligeholde *blob kvitteringer* for at bestemme, om en given blobversion er blevet behandlet.

BLOB modtagerne er gemt i en objektbeholder med navnet *azure-webjobs-værter* i den angivne ved forbindelsesstrengen AzureWebJobsStorage Azure-lager-konto. Kvittering blob har følgende oplysninger:

* Den funktion, der hedder for blob ("*{WebJob name}*. Funktioner. *{Funktionsnavn}*", for eksempel:"WebJob1.Functions.CopyBlob")
* Objektbeholdernavnet
* Typen blob ("BlockBlob" eller "PageBlob")
* Blob-navn
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

Hvis du vil tvinge oparbejdning af en blob, kan du manuelt slette blob kvittering for denne blob fra *azure-webjobs-værter* objektbeholderen.

## <a id="queues"></a>Relaterede emner, der er dækket af køer artiklen

For oplysninger om, hvordan du håndterer blob behandling udløses af meddelelsen kø eller for WebJobs SDK se behandling, scenarier, der er ikke specifikke for blob, [hvordan du bruger Azure kø storage med WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Relaterede emner, der er dækket i denne artikel omfatter følgende:

* Asynkron funktioner
* Flere forekomster
* Lukning
* Brug WebJobs SDK attributter i brødteksten i en funktion
* Angive SDK forbindelse strengene i kode.
* Angive værdier for WebJobs SDK parametre parametre i kode
* Konfigurere `MaxDequeueCount` for håndtering af en blob.
* Udløse en funktion manuelt
* Skrive logfiler

## <a id="nextsteps"></a>Næste trin

Denne vejledning har leveret kodeeksempler, der viser, hvordan du håndterer almindelige scenarier til at arbejde med Azure BLOB. Du kan finde flere oplysninger om, hvordan du bruger Azure WebJobs og WebJobs SDK [Azure WebJobs anbefalede ressourcer](http://go.microsoft.com/fwlink/?linkid=390226).
 
