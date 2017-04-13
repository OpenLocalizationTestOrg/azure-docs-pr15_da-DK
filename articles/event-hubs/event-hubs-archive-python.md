<properties
    pageTitle="Azure begivenhed Hubs arkiv gennemgang | Microsoft Azure"
    description="Eksempel, der bruger Azure Python SDK til at vise, ved hjælp af funktionen begivenhed Hubs arkiv."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Begivenhed Hubs arkiv gennemgang: Python

Begivenhed Hubs arkiv er en ny funktion af begivenhed hubber, der gør det muligt at levere automatisk stream dataene i din begivenhed Hub en Azure Blob-lager konto efter eget valg. Dette gør det nemt at udføre batchbehandling på streaming realtidsdata. I denne artikel beskrives, hvordan du bruger begivenhed Hubs arkiv med Python. Du kan finde flere oplysninger om begivenhed Hubs arkiv, [Oversigt over artikel](event-hubs-archive-overview.md).

Dette eksempel bruger Azure Python SDK til at vise, ved hjælp af funktionen arkiv. Sender.py sender simuleret påvirkning telemetri til begivenhed hubber i JSON-formatet. Begivenhed Hub er konfigureret til at bruge funktionen arkiv til at skrive dataene til at blob storage i batches. Archivereader.py derefter læser disse BLOB og opretter en Tilføj fil per enhed og skriver data i .csv-filer.

Hvad skal gøres

1.  Oprette en Azure Blob-lager-konto og en blob objektbeholder inden for det, ved hjælp af portalen Azure

2.  Oprette en begivenhed Hub navneområde, ved hjælp af portalen Azure

3.  Oprette en begivenhed Hub med funktionen arkiv er aktiveret, ved hjælp af portalen Azure

4.  Sende data til begivenhed hubben med et Python script

5.  Læse filerne fra arkivet og behandle dem med en anden Python script

Forudsætninger

1.  Python 2.7.x

2.  Et Azure-abonnement

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Oprette en Azure-lager-konto

1.  Log på [Azure-portalen][].

2.  Klikke på ny i den venstre navigationsrude i portalen, og derefter klikke på Data + lagerplads, og klik lagerplads konto.

3.  Udfyld felterne i bladet lagerplads konto, og klik på **Opret**.

    ![][1]

4.  Klik på **BLOB**, når du får vist **Installationer lykkedes** meddelelsen, klik på den nye konto lager og bladet **Essentials** . Når bladet **Blob service** åbnes, skal du klikke på **+ objektbeholder** øverst. Navngive objektbeholder **arkiv**, og derefter lukke bladet **Blob tjeneste** .

5.  Klik på **Access-taster** i bladet venstre, og Kopiér navnet på kontoen, lager og værdien af **nøgle1**. Gemme disse værdier i Notesblok eller en anden midlertidig placering.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Oprette et Python script for at sende begivenheder til begivenhed-Hub

1.  Åbn din foretrukne Python editor, som [Visual Studio-kode][].

2.  Oprette et script, kaldet **sender.py**. Dette script sender 200 begivenheder til begivenhed centrum. De er enkel påvirkning målinger, der er sendt i JSON.

3.  Indsæt følgende kode i sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Opdater den foregående kode for at bruge dit navneområde navn og nøgleværdier, som du hentede, da du oprettede navneområdet begivenhed Hubs.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Oprette et Python script for at læse din arkivfiler

1.  Udfyld bladet, og klik på **Opret**.

2.  Oprette et script, kaldet **archivereader.py**. Dette script læser arkivere filer og oprette en fil i enheden, for at skrive dataene kun til den pågældende enhed.

3.  Indsæt følgende kode i archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Sørg for at indsætte de relevante værdier for dit kontonavn til lager og nøgle i opkaldet til `startProcessing`.

## <a name="run-the-scripts"></a>Køre scripts

1.  Åbn en kommandoprompt med Python i dens sti og derefter køre disse kommandoer til at installere Python nødvendige pakker:

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Hvis du har en tidligere version af enten azure-lager eller azure du muligvis bruge den **– opgradere** indstilling

    Du muligvis også at køre følgende (ikke nødvendigt på de fleste systemer):

    ```
    pip install cryptography
    ```

2.  Ændre mappen til uanset hvor du har gemt sender.py og archivereader.py, og Kør denne kommando:

    ```
    start python sender.py
    ```
    
    Dette starter en ny Python proces for at køre afsenderen.

3. Nu Vent et par minutter arkiv til at køre. Skriv følgende kommando i din oprindelige kommandovindue:

    ```
    python archivereader.py
    ```

Denne arkiv processor bruger den lokale mappe til at hente alle BLOB fra konto/objektbeholderen til lagring af. Det behandler alle, der ikke er tomme, og skriv resultaterne som .csv-filer i den lokale mappe.

## <a name="next-steps"></a>Næste trin

Du kan få mere at vide om begivenhed Hubs ved at besøge følgende links:

- [Oversigt over begivenhed Hubs arkivere][]
- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .
- [Oversigt over Hubs begivenhed][]
 

[Azure-portalen]: https://portal.azure.com/
[Oversigt over begivenhed Hubs arkivere]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Visual Studio-kode]: https://code.visualstudio.com/
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
