<properties
    pageTitle="Streaming Azure diagnosticering data i varmt stien ved hjælp af begivenhed Hubs | Microsoft Azure"
    description="Viser, hvordan du konfigurerer Azure diagnosticering med begivenhed Hubs fra start til slut, herunder vejledning til almindelige scenarier."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Streaming Azure diagnosticering data i varmt stien ved hjælp af begivenhed Hubs

Azure diagnosticering indeholder fleksible måder til at indsamle målepunkter og hændelseslogge fra cloud services virtuelle maskiner (VM'er) og overføre resultater til Azure-lager. Starter i marts 2016 (SDK 2.9) tidsramme, kan du synkronisere diagnosticering til helt brugerdefinerede datakilder og overføre data varmt sti i sekunder ved hjælp af [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/).

Understøttede datatyper omfatter:

- Begivenhed sporing for Windows (ETW) begivenheder
- Tællere i ydeevne
- Windows-hændelseslogge
- Programmet logfiler
- Azure diagnosticering infrastruktur logfiler

I denne artikel beskrives, hvordan du konfigurerer Azure diagnosticering med begivenhed Hubs fra start til slut. Vejledning findes også i følgende almindelige scenarier:

- Hvordan du tilpasser logfiler og mål, som får sinked til begivenhed hubber
- Sådan ændres konfigurationer i de enkelte miljøer
- Sådan får du vist begivenhed Hubs streamdata.
- Sådan foretages fejlfinding af forbindelsen  

## <a name="prerequisites"></a>Forudsætninger

Begivenhed Hubs synker i Azure diagnosticering understøttes i Cloud Services, FOS, virtuelt skala sæt og Service-strukturen starter i Azure SDK 2.9 og de tilsvarende Azure-værktøjer til Visual Studio.

- Azure diagnosticering lokalnummer 1,6 ([Azure SDK til .NET 2.9 eller nyere](https://azure.microsoft.com/downloads/) er beregnet til dette som standard)
- [Visual Studio 2013 eller nyere](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Eksisterende varianter af Azure diagnosticering i et program ved hjælp af en *.wadcfgx* fil og en af følgende fremgangsmåder:
    - Visual Studio: [konfiguration af diagnosticering til Azure Cloud Services og virtuelle maskiner](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell: [aktivere diagnosticering i Azure Cloud Services ved hjælp af PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Begivenhed Hubs navneområde klargjort i artiklen [Introduktion til begivenhed Hubs](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Oprette forbindelse Azure diagnosticering til begivenhed Hubs sink

Azure diagnosticering sinks altid logfiler og statistik, som standard en Azure-lager-konto. Et program kan desuden synkronisere til begivenhed hubber ved at tilføje en ny sektion **dræn** elementet **WadCfg** i afsnittet **PublicConfig** i filen *.wadcfgx* . I Visual Studio *.wadcfgx* filen er gemt i følgende sti: **Skyen tjenesten Project** > **roller** >  **(RoleName)** > **diagnostics.wadcfgx** fil.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

I dette eksempel begivenhed Hub URL-adressen er angivet til begivenhed hubben fuldstændige navneområde: begivenhed Hubs navneområde + "/" + begivenhed Hub navn.  

URL-adressen for begivenhed Hub vises i [Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885) på dashboardet til begivenhed Hubs.  

**Synkronisere** navnet kan angives til en gyldig streng, så længe den samme værdi bruges konsekvent i hele filen config.

> [AZURE.NOTE]  Der kan være yderligere dræn, som *applicationInsights* konfigureret i dette afsnit. Azure diagnosticering gør det muligt for en eller flere dræn til defineres, hvis hver sink angives også i sektionen **PrivateConfig** .  

Begivenhed Hubs sink skal også erklæret og defineret i afsnittet **PrivateConfig** i filen *.wadcfgx* config.

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

Den `SharedAccessKeyName` værdi skal svare til en delt Access signatur (SAS) nøgle og en politik, der er defineret i navneområdet **Begivenhed Hubs** . Gå til begivenhed Hubs dashboard i [Azure-portalen](https://manage.windowsazure.com), klik på fanen **Konfigurer** , og konfigurere en navngivet politik (eksempelvis "SendRule"), der har *sende* tilladelser. **StorageAccount** er også erklæret i **PrivateConfig**. Det er ikke nødvendigt at ændre værdierne her, hvis de arbejder. I dette eksempel skal vi er tomt værdierne, som er en log, et efterfølgende aktiv indstiller værdierne. For eksempel angiver *ServiceConfiguration.Cloud.cscfg* miljø konfigurationsfil miljø relevante navne og taster.  

> [AZURE.WARNING] Tasten begivenhed Hubs SAS er gemt i almindelig tekst i filen *.wadcfgx* . Ofte, denne tast tjekkes ind til versionsstyring eller er tilgængeligt som et aktiv i din build-server, så du bør beskytte efter behov. Vi anbefaler, at du bruger en SAS nøgle her med *Send kun* tilladelser, så en hacker ikke kan skrive til begivenhed-Hub, men lytte til den eller administrere den.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Konfigurere Azure diagnosticering logfiler og målepunkter til at synkronisere med begivenhed Hubs

Som beskrevet tidligere, alle standardindholdstyper og brugerdefinerede diagnosticering data, er målepunkter og hændelseslogge, automatisk sinked til Azure-lager i konfigurerede intervaller. Med begivenhed Hubs og eventuelle ekstra sink, kan du angive en rod eller blad node i hierarkiet for at være sinked med begivenhed Hub. Dette omfatter ETW begivenheder, tællere i ydeevne, Windows-hændelseslogge og logge af programmet.   

Det er vigtigt at overveje, hvor mange datapunkter der faktisk skal overføres til begivenhed hubber. Typisk overføre udviklere lav ventetid hot sti data, der skal være consumed og fortolket hurtigt. Systemer, overvåge beskeder eller Autoskalering regler er eksempler. En udvikler kan også konfigurere en alternativ analyse store eller søge store – for eksempel Azure Stream Analytics, Elasticsearch, et brugerdefineret overvågning system eller et foretrukne overvågning system fra andre.

Følgende er nogle eksempler på konfigurationer.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

I følgende eksempel anvendes sink til den overordnede **PerformanceCounters** node i hierarkiet, hvilket betyder, at alle underordnede **PerformanceCounters** sendes til begivenhed Hubs.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

I det forrige eksempel sink anvendes kun tre tællere: **Anmodninger i kø**, **Anmodninger afvist**og **% Processor klokkeslæt**.  

I følgende eksempel viser, hvordan en udvikler kan begrænse mængden sendte data skal være den kritiske målepunkter, der bruges til denne tjenestetilstand.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

I dette eksempel sink anvendes til logfiler og filtreres kun for fejl niveau sporing.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Installere og opdatere en Cloud Services-program og diagnosticering config

Visual Studio indeholder den nemmeste sti for at installere programmet og begivenhed Hubs sink konfiguration. For at få vist og redigere filen, Åbn filen *.wadcfgx* i Visual Studio, redigere den og gemme den. Stien er **Skyen Service projekt** > **roller** > **(RoleName)** > **diagnostics.wadcfgx**.  

På dette tidspunkt alle installation og installation opdatere handlinger i Visual Studio, Visual Studio Team System, og alle kommandoer eller scripts, som er baseret på MSBuild og bruge den **/t: publicere** target Medtag *.wadcfgx* i processen til emballagen. Desuden installationer og opdateringer implementere filen til Azure ved hjælp af det relevante Azure diagnosticering agent filtypenavn på din FOS.

Når du installerer programmet og Azure diagnosticering konfiguration, vises med det samme aktivitet i dashboardet af begivenhed Hub. Dette angiver, at du er klar til at gå videre til visning af data om hot sti i værktøjet for lytteren klient på computeren eller analyse efter eget valg.  

I følgende figur viser dashboardet begivenhed Hubs sund afsendelse af diagnosticering data til begivenhed hubben starter et tidspunkt efter 11 PM. Det er, når programmet er installeret med en opdateret *.wadcfgx* -fil, og sink blev konfigureret korrekt.

![][0]  

> [AZURE.NOTE] Når du foretager opdateringer til Azure diagnosticering config filen (.wadcfgx), kan det anbefales, at du push opdateringerne til hele programmet samt konfigurationen ved hjælp af Visual Studio publicering eller et Windows PowerShell-script.  

## <a name="view-hot-path-data"></a>Vis hot sti data

Som beskrevet tidligere, er der mange Brug sager for lytte til og begivenhed Hubs databehandlingen.

En enkelt metode er at oprette et lille test console computeren til at lytte til begivenhed hubben og udskrive output strømmen. Du kan placere følgende kode, som er beskrevet mere detaljeret i [Introduktion til begivenhed Hubs](./event-hubs-csharp-ephcs-getstarted.md)), i et console til computeren.  

Bemærk, at programmet console skal omfatte [begivenhed Processor Host Nuget pakke](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Husk at erstatte værdier i vinklede parenteser i funktionen **Main** med værdier for dine ressourcer.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Fejlfinding i forbindelse med begivenhed Hubs sink

- Begivenhed hubben viser ikke indgående eller udgående Arrangement aktivitet som forventet.

    Kontrollér, at din begivenhed Hub blev klargjort. Alle forbindelsesoplysninger i afsnittet **PrivateConfig** i *.wadcfgx* skal svare til værdierne i din ressource, som det fremgår på portalen. Sørg for, at du har en defineret SAS politik ("SendRule" i eksemplet) på portalen og, at *sende* tilladelse gives.  

- Efter en opdatering viser begivenhed hubben ikke længere indgående eller udgående Arrangement aktivitet.

    Først skal du sørge for, at afkrydsningsfeltet begivenhed Hub og konfiguration af oplysninger er korrekte, som beskrevet tidligere. Nogle gange nulstilles **PrivateConfig** i en installation opdatering. Anbefalede fix er at foretage alle ændringer i *.wadcfgx* i projektet og derefter push en opdatering til fuldført programmet. Hvis det ikke er muligt, skal du kontrollere, at opdateringen diagnosticering flytter en komplet **PrivateConfig** , der indeholder SAS nøglen.  

- Jeg har prøvet forslag, og den begivenhed Hub stadig virker ikke.

    Prøve at lede i tabellen Azure-lager, der indeholder logfiler og fejl for Azure diagnosticering selve: **WADDiagnosticInfrastructureLogsTable**. En af mulighederne er at bruge et værktøj som [Azure Storage Explorer](http://www.storageexplorer.com) til at oprette forbindelse til denne konto lagerplads, få vist denne tabel og tilføje en forespørgsel for tidsstempel i de seneste 24 timer. Du kan bruge værktøjet til at eksportere en .csv-fil og åbne den i et program som Microsoft Excel. Excel gør det nemt at søge efter opkald kort strenge, som **EventHubs**, for at se, hvilke fejl rapporteres.  

## <a name="next-steps"></a>Næste trin

• [Se mere om begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Tillæg: Fuldføre eksempel på Azure diagnosticering konfiguration af fil (.wadcfgx)

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Den komplementære *ServiceConfiguration.Cloud.cscfg* i dette eksempel ser ud som følgende.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
