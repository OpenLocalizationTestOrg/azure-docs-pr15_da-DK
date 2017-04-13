<properties
 pageTitle="Importere eksport af IoT Hub enhed identiteter | Microsoft Azure"
 description="Begreber og .NET kode kodestykker for flere styring af IoT Hub enhed identiteter"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Flere styring af IoT Hub enhed identiteter

Hver IoT hub har en enhed identitet registreringsdatabasen, du kan bruge til at oprette og enheder ressourcer i tjenesten, såsom en kø, der indeholder de meddelelser, startet cloud-til-enhed. Enhed identitet registreringsdatabasen også giver dig mulighed at styre adgangen til enhed mod slutpunkterne. I denne artikel beskrives, hvordan du kan importere og eksportere enhed identiteter ad gangen til og fra en enhed identitet registreringsdatabase.

Importer og Eksporter handlinger finde sted i forbindelse med *job* , der gør det muligt at udføre flere servicehandlinger mod en IoT hub.

Klassen **RegistryManager** omfatter **ExportDevicesAsync** og **ImportDevicesAsync** metoder, der bruger **Job** framework. Disse metoder gør det muligt at eksportere, importere og synkronisere helhed af en IoT hub enhed registreringsdatabasen.

## <a name="what-are-jobs"></a>Hvad er job?

Enhed identitet registreringsdatabasen handlinger bruge **Job** systemet når handlingen:

*  Har udførelse af potentielt lang tid sammenlignet med standard runtime handlinger, eller
*  Returnerer en stor mængde data til brugeren.

I disse tilfælde i stedet for et enkelt API-kald venter eller blokerer på resultatet af handlingen, opretter handlingen asynkront et **Job** til den pågældende IoT hub. Handlingen derefter returnerer med det samme objekt, en **JobProperties** .

Følgende C#-kodestykke viser, hvordan du opretter en Eksportér sag:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Du kan bruge klassen **RegistryManager** forespørge tilstanden for **jobbet** ved hjælp af de returnerede **JobProperties** metadata.

Følgende C#-kodestykke viser, hvordan du afstemning hvert 5 for at få vist Hvis jobbet er blevet udført:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Eksportere enheder

Du kan bruge metoden **ExportDevicesAsync** til at eksportere helhed af en IoT hub enhed registreringsdatabasen til en Azure blob-objektbeholder til [Lagring](https://azure.microsoft.com/documentation/services/storage/) ved hjælp af en [Delt Access signatur](https://msdn.microsoft.com/library/ee395415.aspx).

Denne metode kan du oprette pålidelige sikkerhedskopier af dine enhedsoplysninger i en blob beholder, som du styrer.

Metoden **ExportDevicesAsync** kræver to parametre:

*  En *streng* , der indeholder en URI af en blob objektbeholder. Denne URI skal indeholde et SAS token, som giver skriveadgang til objektbeholderen. Jobbet opretter en blok blob i denne objektbeholder til at gemme fortløbende Eksporter data for enhed. SAS tokenet skal omfatter disse tilladelser:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  En *boolesk* , der angiver, om du vil udelade godkendelse taster i Eksporter data. Hvis **Falsk**, godkendelse nøglerne er medtaget i export output; Ellers eksporteres taster som **null**.

Følgende C#-kodestykke viser, hvordan du kan starte et eksportjob, som indeholder enhed godkendelse taster i de eksporterede data og derefter afstemning for fuldførelse:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Jobbet gemmer outputtet i beholderen medfølgende blob som en blok blob med navnet **devices.txt**. Outputdataene består af JSON serialiseret enhedsdata med en enhed hver linje.

I følgende eksempel viser outputdataene:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Hvis du skal have adgang til disse data i kode, kan du nemt deserialisere disse data ved hjælp af klassen **ExportImportDevice** . Følgende C#-kodestykke viser, hvordan du læse enhedsoplysninger, der tidligere blev eksporteret til en blok blob:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Du kan også bruge metoden **GetDevicesAsync** i klassen **RegistryManager** til at hente en liste over dine enheder. Denne metode har dog en hårde knop 1000 på antallet af enhedsobjekter, der returneres. Forventet use case for metoden **GetDevicesAsync** er for udvikling scenarier som hjælp til fejlfinding og anbefales ikke til fremstilling arbejdsmængder.

## <a name="import-devices"></a>Importere enheder

Metoden **ImportDevicesAsync** i klassen **RegistryManager** gør det muligt at udføre flere import og synkronisering handlinger i en IoT hub enhed registreringsdatabasen. Ligesom metoden **ExportDevicesAsync** bruger metoden **ImportDevicesAsync** **Job** framework.

Tager sig ved hjælp af metoden **ImportDevicesAsync** , fordi ud over klargøring af nye enheder i din enhed identitet registreringsdatabase, kan det også opdatere og slette eksisterende enheder.

> [AZURE.WARNING]  En importhandlingen kan ikke fortrydes. Tag altid en sikkerhedskopi af dine eksisterende data ved hjælp af metoden **ExportDevicesAsync** til en anden blob objektbeholder, før du foretager flere ændringer til din enhed identitet registreringsdatabase.

Metoden **ImportDevicesAsync** kræver to parametre:

*  En *streng* , der indeholder en URI af en [Azure-lager](https://azure.microsoft.com/documentation/services/storage/) blob beholder skal bruges som *input* til jobbet. Denne URI skal indeholde et SAS token, som giver læseadgang til objektbeholderen. Denne beholder skal indeholde en blob med navnet **devices.txt** , der indeholder dataene, fortløbende enhed til at importere til din enhed identitet registreringsdatabase. De importerede data skal indeholde enhedsoplysninger i det samme JSON-format, der **ExportImportDevice** jobbet bruges, når der oprettes en **devices.txt** blob. SAS tokenet skal omfatter disse tilladelser:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  En *streng* , der indeholder en URI af en [Azure-lager](https://azure.microsoft.com/documentation/services/storage/) blob objektbeholder til at bruge som *output* fra jobbet. Jobbet opretter en blok blob i denne objektbeholder til at gemme alle fejloplysninger fra den færdige import **Job**. SAS tokenet skal omfatter disse tilladelser:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  De to parametre kan pege på den samme blob beholder. Parametrene separat aktivere blot mere kontrol over dine data, som objektbeholderen output kræver yderligere tilladelser.

Følgende C#-kodestykke viser, hvordan du starte et job til import:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Importere funktionsmåde

Du kan bruge metoden **ImportDevicesAsync** til at udføre følgende flere handlinger i registreringsdatabasen identitet din enhed:

-   Flere registrering af nye enheder
-   Flere sletninger af eksisterende enheder
-   Flere ændringer af statusserne (aktivere eller deaktivere enheder)
-   Flere tildeling af nye enhed godkendelsesnøgler
-   Flere automatisk-gendannelse af enhed godkendelse taster

Du kan udføre en vilkårlig kombination af de foregående handlinger i et enkelt **ImportDevicesAsync** opkald. Du kan for eksempel registrere nye enheder og slette eller opdatere eksisterende enheder på samme tid. Når den bruges sammen med metoden **ExportDevicesAsync** , kan du helt overføre alle dine enheder fra én IoT hub til en anden.

Bruge egenskaben valgfri **ImportMode %** i serialisering Importér data for hver enhed til at styre Importér proces per-enhed. Egenskaben **ImportMode %** har følgende indstillinger:

| ImportMode % |  Beskrivelse |
| -------- | ----------- |
| **createOrUpdate** | Hvis der ikke findes en enhed med det angivne **id**, er den nyligt registreret. <br/>Hvis enheden, der allerede findes, overskrives eksisterende oplysninger med de medfølgende Indtast data uden hensyn til **ETag** -værdien. |
| **oprette** | Hvis der ikke findes en enhed med det angivne **id**, er den nyligt registreret. <br/>Hvis enheden, der allerede findes, er en fejl skrevet til logfilen. |
| **opdatere** | Hvis der allerede findes en enhed med det angivne **id**, overskrives eksisterende oplysninger med de medfølgende Indtast data uden hensyn til **ETag** -værdien. <br/>Hvis enheden ikke eksisterer, er en fejl skrevet til logfilen. |
| **updateIfMatchETag** | Hvis der allerede findes en enhed med det angivne **id**, overskrives eksisterende oplysninger med de medfølgende inputdataene kun, hvis der er en **ETag** match. <br/>Hvis enheden ikke eksisterer, er en fejl skrevet til logfilen. <br/>Hvis der er en uoverensstemmelse i **ETag** skal skrives fejlen til logfilen. |
| **createOrUpdateIfMatchETag** | Hvis der ikke findes en enhed med det angivne **id**, er den nyligt registreret. <br/>Hvis enheden, der allerede findes, overskrives eksisterende oplysninger med de medfølgende inputdataene kun, hvis der er en **ETag** match. <br/>Hvis der er en uoverensstemmelse i **ETag** skal skrives fejlen til logfilen. |
| **Slet** | Hvis der allerede findes en enhed med det angivne **id**, slettes den uden hensyn til **ETag** -værdien. <br/>Hvis enheden ikke eksisterer, er en fejl skrevet til logfilen. |
| **deleteIfMatchETag** | Hvis der allerede findes en enhed med det angivne **id**, slettes den kun, hvis der er en **ETag** match. Hvis enheden ikke eksisterer, er en fejl skrevet til logfilen. <br/>Hvis der er en uoverensstemmelse i ETag skal skrives fejlen til logfilen. |

> [AZURE.NOTE] Hvis serialiseringsdata ikke eksplicit definerer et **ImportMode %** flag til en enhed, som standard **createOrUpdate** under importen.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importere enheder eksempel – flere klargøring af enheden 

Følgende C#-kodeeksempel viser, hvordan du opretter flere enhed identiteter, som:

- Medtage godkendelse taster.
- Skrive enhed oplysningerne til en blok blob.
- Importere enhederne til enhed identitet registreringsdatabasen.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Importere enheder eksempel – flere sletning

Følgende eksempel-kode viser, hvordan du slette de enheder, du har tilføjet ved hjælp af det forrige eksempel-kode:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Få objektbeholderen SAS URI


Følgende eksempel-kode viser, hvordan du oprette en [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md) med Læs, Skriv og slettetilladelser for en blob objektbeholder:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Næste trin

I denne artikel, du har lært hvordan du kan udføre flere handlinger mod enhed identitet registreringsdatabasen i en IoT hub. Følg disse links for at få flere oplysninger om administration af Azure IoT Hub:

- [Brugen målepunkter][lnk-metrics]
- [Handlinger overvågning][lnk-monitor]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md