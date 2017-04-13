<properties
    pageTitle="Komme i gang med blob-lager og Visual Studio forbundne tjenester (WebJob projekter) | Microsoft Azure"
    description="Hvordan du kommer i gang med at bruge Blob-lager i et projekt med WebJob efter oprettelse af forbindelse til en Azure-lager ved hjælp af Visual Studio forbundne tjenester."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Introduktion til Azure Blob-lager og Visual Studio forbundne tjenester (WebJob projekter)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

Denne artikel indeholder C# kodeeksempler, der viser, hvordan du kan udløse en proces, når en Azure blob oprettes eller opdateres. Eksemplerne i brug [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) version 1.x. Når du tilføjer en lagerplads konto til en WebJob projekt ved hjælp af dialogboksen Visual Studio **Tilføje forbundne tjenester** , den relevante Azure-lager NuGet pakke er installeret, skal de relevante .NET referencer er føjet til projektet, og strenge for lagerplads kontoen er blevet opdateret i App.config-filen.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Hvordan du kan udløse en funktion, når der oprettes eller opdateres en blob

Dette afsnit viser, hvordan du bruger attributten **BlobTrigger** .

 **Note:** WebJobs SDK scanner logfiler for at holde øje med nye eller ændrede BLOB. Denne proces er fuldstændig langsom; en funktion kan ikke udløses indtil nogle minutter eller længere, når blob er blevet oprettet.  Hvis programmet skal behandle BLOB med det samme, er den anbefalede metode til at oprette en kø meddelelse, når du opretter blob og bruge attributten [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) i stedet for attributten **BlobTrigger** på den funktion, der behandler blob.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Datatyper, som du kan binde til BLOB

Du kan bruge attributten **BlobTrigger** på følgende typer:

* **streng**
* **TextReader**
* **Værdistrøm**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Andre typer af [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Hvis du vil arbejde direkte med kontoen Azure-lager, kan du også tilføje en **CloudStorageAccount** -parameter til Metodesignaturen.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Få tekst blob indhold ved binding til streng

Hvis tekst BLOB er forventet, kan **BlobTrigger** anvendes på en **streng** parameter. Følgende eksempel-kode binder blob en tekst til en **streng** parameter med navnet **logMessage**. Funktionen bruger denne parameter til at skrive indholdet af blob til WebJobs SDK dashboard.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Få serialiseret blob indhold ved hjælp af ICloudBlobStreamBinder

Følgende eksempel-kode bruger en klasse, der implementerer **ICloudBlobStreamBinder** for at aktivere attributten **BlobTrigger** binde en blob til typen **WebImage** .

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

**WebImage** binding koden er angivet i en **WebImageBinder** klasse, der stammer fra **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Sådan håndteres en BLOB

Når en **BlobTrigger** funktion ikke, i SDK ringer op til det igen, i tilfælde af fejlen skyldes en midlertidig fejl. Hvis fejlen skyldes indholdet af blob, mislykkes funktionen, hver gang den forsøger at behandle blob. Som standard kalder i SDK en funktion op til 5 gange for en given blob. Hvis det femte prøve mislykkes, føjer SDK en meddelelse til en kø med navnet *webjobs-blobtrigger-forfalskning*.

Det maksimale antal forsøg kan konfigureres. Den samme [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) indstilling bruges til håndtering af en blob og håndtering af en kø meddelelser.

Kø meddelelsen til en BLOB er et JSON-objekt, der indeholder følgende egenskaber:

* FunctionId (i formatet *{WebJob name}*. Funktioner. *{Funktionsnavn}*, for eksempel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" eller "PageBlob")
* ContainerName
* BlobName
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

I følgende eksempel-kode har funktionen **CopyBlob** kode, der medfører, at det mislykkes, hver gang den hedder. Når SDK ringer det til det maksimale antal forsøg, en meddelelse er oprettet i en blob køen og meddelelsen behandles af funktionen **LogPoisonBlob** .

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

SDK deserializes automatisk JSON meddelelsen. Her er klassen **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>BLOB afstemning algoritme

WebJobs SDK scanner alle beholdere, der er angivet af **BlobTrigger** attributter i starten af programmet. I en stor storage konto kan denne scanning gå et stykke tid, så det kan være et stykke tid, før der blev fundet nye BLOB og **BlobTrigger** funktioner udføres.

For at registrere nye eller ændrede BLOB efter program start, læser SDK med jævne mellemrum fra loggene blob-lager. Loggene blob i bufferen og kun få fysisk skrevet hver 10 minutter, eller så, så der kan være betydeligt forsinkelse, når en blob oprettes eller opdateres før den tilhørende **BlobTrigger** funktionen udfører.

Der findes en undtagelse for blob, som du opretter ved hjælp af attributten **Blob** . Når WebJobs SDK opretter en ny blob, sender den nye blob med det samme til en tilsvarende **BlobTrigger** funktioner. Derfor Hvis du har en kæde af blob input og output, kan SDK behandle dem effektivt. Men hvis du vil lave ventetid kører din blob processing-funktioner til BLOB, der er oprettet eller opdateret på anden måde, anbefaler vi bruger **QueueTrigger** i stedet for **BlobTrigger**.

### <a name="blob-receipts"></a>BLOB kvitteringer

WebJobs SDK sikrer, at ingen **BlobTrigger** funktionen bliver kaldet mere end én gang til den samme nye eller opdaterede blob. Det gør dette ved at vedligeholde *blob kvitteringer* for at bestemme, om en given blobversion er blevet behandlet.

BLOB modtagerne er gemt i en objektbeholder med navnet *azure-webjobs-værter* i den angivne ved forbindelsesstrengen AzureWebJobsStorage Azure-lager-konto. Kvittering blob har følgende oplysninger:

* Den funktion, der hedder for blob ("*{WebJob name}*. Funktioner. *{Funktionsnavn}*", for eksempel:"WebJob1.Functions.CopyBlob")
* Objektbeholdernavnet
* Typen blob ("BlockBlob" eller "PageBlob")
* Blob-navn
* ETag (et blob version-id, for eksempel: "0x8D1DC6E70A277EF")

Hvis du vil tvinge oparbejdning af en blob, kan du manuelt slette blob kvittering for denne blob fra *azure-webjobs-værter* objektbeholderen.

## <a name="related-topics-covered-by-the-queues-article"></a>Relaterede emner, der er dækket af køer artiklen

For oplysninger om, hvordan du håndterer blob behandling udløses af meddelelsen kø eller for WebJobs SDK se behandling, scenarier, der er ikke specifikke for blob, [hvordan du bruger Azure kø storage med WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Relaterede emner, der er dækket i denne artikel omfatter følgende:

* Asynkron funktioner
* Flere forekomster
* Lukning
* Brug WebJobs SDK attributter i brødteksten i en funktion
* Angive SDK forbindelse strengene i kode.
* Angive værdier for WebJobs SDK parametre parametre i kode
* Konfigurere **MaxDequeueCount** til håndtering af en blob.
* Udløse en funktion manuelt
* Skrive logfiler

## <a name="next-steps"></a>Næste trin

I denne artikel har leveret kodeeksempler, der viser, hvordan du håndterer almindelige scenarier til at arbejde med Azure BLOB. Du kan finde flere oplysninger om, hvordan du bruger Azure WebJobs og WebJobs SDK [Azure WebJobs dokumentationsressourcer](http://go.microsoft.com/fwlink/?linkid=390226).
