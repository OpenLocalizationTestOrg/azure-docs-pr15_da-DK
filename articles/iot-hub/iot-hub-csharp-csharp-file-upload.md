<properties
    pageTitle="Overføre filer fra enheder, der bruger IoT Hub | Microsoft Azure"
    description="Følge dette selvstudium for at lære at overføre filer fra enheder, der bruger Azure IoT Hub med C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Selvstudium: Sådan overfører du filer fra enheder til skyen med IoT Hub

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for pålidelig og sikker tovejs-kommunikation mellem millioner af IoT enheder og et program igen slutningen. Forrige selvstudier ([komme i gang med IoT Hub] og [sende meddelelser i skyen-til-enhed med IoT Hub]) illustrere den grundlæggende enhed i skyen og skyen-til-enhed SMS funktionaliteten i IoT-Hub, og [processen enhed i skyen meddelelser] selvstudiet beskrives en metode til at gemme pålideligt enhed i skyen meddelelser i Azure blob-lager. I visse scenarier, kan du dog nemt knytte dataene sende dine enheder i de lille enhed i skyen-meddelelser, der accepterer IoT Hub. Som eksempler kan nævnes store filer, der indeholder billeder, videoer, vibrationer data prøver til hyppigt, eller som indeholder nogle dataform gennemgået forbehandling. Disse filer er typisk batchen behandles i skyen ved hjælp af funktioner som [Azure Data Factory] eller [Hadoop] stablen. Når filoverførsler fra en enhed er foretrukne til at sende begivenheder, kan du stadig bruge IoT Hub sikkerhed og pålidelighed funktionalitet.

Dette selvstudium bygger på koden i selvstudiet [sende meddelelser i skyen-til-enhed med IoT Hub] til viser, hvordan du bruge funktionerne Overfør fil af IoT Hub. Det viser, hvordan du sikkert giver en enhed med en Azure blob URI for overførsel af en fil, og hvordan du bruger IoT Hub fil Overfør beskeder til at udløse behandling af filen i din app back-end.

I slutningen af dette selvstudium skal du køre to Windows console-programmer:

* **SimulatedDevice**, en ændret version af appen oprettet i det [sende meddelelser i skyen-til-enhed med IoT Hub] selvstudium, som overfører en fil til lager ved hjælp af en SAS URI leveres af din IoT hub.
* **ReadFileUploadNotification**, som modtager meddelelser om overførsel af filer fra din IoT hub.

> [AZURE.NOTE] IoT Hub understøtter mange Enhedsplatforme og sprog (inklusive C, Java og Javascript) gennem Azure IoT enhed SDK'er. Referere til [Azure IoT Developer Center] til trinvise instruktioner til, hvordan du opretter forbindelse til din enhed, til den kode, der vises i dette selvstudium, og generelt til Azure IoT Hub.

For at fuldføre dette selvstudium skal bruge du følgende:

+ Microsoft Visual Studio 2015,

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Knytte en Azure-lager-konto til IoT-Hub

Da simuleret enheden overfører en fil til en blob, skal du have en [Azure-lager] -konto, der er knyttet til IoT-Hub. Når du knytter en Azure-lager konto med en IoT-hub, kan hubben generere en SAS URI, en enhed kan bruge til sikker overføre en fil til en blob objektbeholder. Tjenesten IoT Hub og enhed SDK'er koordinere den proces, der genererer SAS URI og gør det tilgængeligt til en enhed for at bruge til at overføre en fil.

Følg vejledningen i [Konfigurer fil overfører ved hjælp af portalen Azure] [ lnk-configure-upload] knytte en Azure-lager-konto til din IoT-hub.

## <a name="upload-a-file-from-a-simulated-device"></a>Overføre en fil fra en simuleret enhed

I dette afsnit, skal du ændre programmet simuleret enhed du har oprettet i [sende meddelelser i skyen-til-enhed med IoT Hub] til at modtage cloud-til-enhed meddelelser fra IoT-hubben.

1. Højreklik på **SimulatedDevice** projektet i Visual Studio, klik på **Tilføj**, og klik **Eksisterende element**. Gå til en billedfil og medtage dem i dit projekt. Dette selvstudiet antages, at billedet hedder `image.jpg`.

2. Højreklik på billedet, og klik derefter på **Egenskaber**. Sørg for, at **Kopier til mappe, Output** er indstillet til **altid kopi**.

    ![][1]

3. Tilføj følgende udtalelser øverst i filen i filen **Program.cs** :

        using System.IO;

4. Føje følgende metode til klassen **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    Den `UploadToBlobAsync` metode tager i navn og stream kildefilen af filen til overføres og håndterer Overfør til lager. Programmet console viser den tid, det tager for at overføre filen.

5. Tilføj følgende metode i metoden **Main** lige før den `Console.ReadLine()` linje:

        SendToBlobAsync();

> [AZURE.NOTE] Dette selvstudium implementerer ikke nogen forsøg politik for enkels risvin. Fremstilling kode, skal du implementere forsøg politikker (som eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="receive-a-file-upload-notification"></a>Modtage en meddelelse om overførsel af filer

I dette afsnit, skal skrive du en Windows-konsollen app, der modtager fil Overfør beskeder fra IoT Hub.

1. Oprette et nyt projekt i Visual C# Windows ved hjælp af projektskabelon **Console-program** i den aktuelle løsning i Visual Studio. Navngive projektet **ReadFileUploadNotification**.

    ![Nyt projekt i Visual Studio][2]

2. Højreklik på **ReadFileUploadNotification** projektet i Solution Explorer, og klik derefter på **Administrer NuGet pakker**.

    Dette viser vinduet Administrer NuGet pakker.

2. Søge efter `Microsoft.Azure.Devices`, klik på **Installer**, og Accepter vilkår for anvendelse. 

    Dette henter, installerer og tilføjer en reference til [Azure IoT - tjenesten SDK NuGet pakke] i **ReadFileUploadNotification** projektet.

3. Tilføj følgende udtalelser øverst i filen i filen **Program.cs** :

        using Microsoft.Azure.Devices;

4. Tilføj følgende felter til klassen **Program** . Erstatte værdien pladsholder med IoT hub forbindelsesstrengen fra [komme i gang med IoT Hub]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Føje følgende metode til klassen **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Bemærk, at Modtag mønster er den samme, der bruges til at modtage cloud-til-enhed meddelelser fra appen enhed.

6. Til sidst skal du føje følgende linjer til metoden **Main** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1. Højreklik på din løsning i Visual Studio, og vælg **angive start projekter**. Vælg **flere projekter, Start**og derefter vælge handlingen **Start** for **ReadFileUploadNotification** og **SimulatedDevice**.

2. Tryk på **F5**. Begge programmer skal starte. Du bør se overført i én konsol app og Overfør meddelelsen modtages af anden console-app. Du kan bruge [Azure portal] eller Visual Studio Server Explorer for at kontrollere, om tilstedeværelsen af den reviderede fil i kontoen Azure-lager.

  ![][50]


## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært Sådan udnytte fil Overfør funktionerne i IoT Hub til at forenkle filoverførsler fra enheder. Du kan fortsætte med at udforske IoT hub funktioner og scenarier med følgende artikler:

- [Oprette en IoT hub fra et program][lnk-create-hub]
- [Introduktion til C SDK][lnk-c-sdk]
- [IoT Hub SDK'er][lnk-sdks]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Azure-portalen]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Sende meddelelser i skyen-til-enhed med IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Procesfarver enhed i skyen-meddelelser]: iot-hub-csharp-csharp-process-d2c.md
[Komme i gang med IoT-Hub]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-lager]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - tjenesten SDK NuGet pakke]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


