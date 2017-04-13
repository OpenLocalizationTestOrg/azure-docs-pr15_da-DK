<properties
    pageTitle="Azure skærm Autoskalering almindelige målepunkter. | Microsoft Azure"
    description="Få mere at vide, hvilke målepunkter er ofte anvendte i Autoskalering din Cloud Services, virtuelle maskiner og Web Apps."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure skærm Autoskalering almindelige målepunkter

Azure skærm Autoskalering kan du tilpasse antallet af kørende forekomster, op eller ned, baseret på telemetridata (metrisk). I dette dokument beskrives fælles mål, som du vil bruge. I portalen Azure til Skytjenester og serverfarme, kan du vælge den metrikværdi for ressourcen skalere ved. Du kan også vælge en hvilken som helst metrisk, fra en anden ressource skalere ved.

Her får du flere oplysninger om, hvordan du finder og få vist den målepunkter, du vil skalere ved. Følgende gælder for skalering virtuelt skala sæt samt.

## <a name="compute-metrics"></a>Beregne målepunkter
Azure VM v2 leveres med diagnostics lokalnummer, der er konfigureret, og de har følgende målene slået til som standard.

- [Gæst måleredskaber for Windows VM v2](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Gæst måleredskaber for Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Du kan bruge den `Get MetricDefinitions` API/PoSH/CLI for at få vist de tilgængelige for din VMSS ressource målepunkter. 

Hvis du bruger VM skala sæt, og du ikke kan se en bestemt metrikværdi, der er angivet, er det sandsynligvis *deaktiveret* i din diagnosticering filtypenavn.

Hvis en bestemt metrikværdi ikke er prøver eller overført med den hyppighed, du vil, kan du opdatere diagnosticering konfigurationen.

Hvis begge tilfælde ovenfor er sand, gennemse derefter [Bruge PowerShell til at aktivere Azure diagnosticering i en virtuel maskine, der kører Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) om PowerShell til at konfigurere og opdatere din Azure VM diagnosticering udvidelse for at aktivere metrikværdien. Denne artikel indeholder også et eksempel på diagnosticering konfigurationsfil.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Beregne måleredskaber for Windows VM v2 som gæst OS

Når du opretter en ny VM (v2) i Azure, aktiveres diagnosticering ved hjælp af filtypenavnet diagnosticering.

Du kan oprette en liste over målene ved hjælp af følgende kommando i PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan oprette en besked på følgende målepunkter.

|Metriske navn|   Enhed|
|---|---|
|\Processor(_Total)\% processortid    |Procent|
|\Processor(_Total)\% følsomme tid   |Procent|
|\Processor(_Total)\% Brugertid |Procent|
|\Processor oplysninger (_Total) \Processor frekvens |Tæl|
|\System\Processes| Tæl|
|\Process (_Total) \Thread Tæl| Tæl|
|\Process (_Total) \Handle Tæl  |Tæl|
|\Memory\% aktiverede byte i brug   |Procent|
|\Memory\Available byte|   Byte|
|\Memory\Committed byte    |Byte|
|\Memory\Commit grænse|  Byte|
|\Memory\Pool gruppe|  Byte|
|\Memory\Pool ikke|   Byte|
|\PhysicalDisk(_Total)\% disk tid| Procent|
|\PhysicalDisk(_Total)\% disk læse tid|    Procent|
|\PhysicalDisk(_Total)\% disk skrive klokkeslæt|   Procent|
|\PhysicalDisk (_Total) \Disk overførsler/sec   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk læser/sec   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk skriver/sec  |CountPerSecond|
|\PhysicalDisk (_Total) \Disk byte/sekund   |BytesPerSecond|
|\PhysicalDisk (_Total) \Disk Læste byte/sekund| BytesPerSecond|
|\PhysicalDisk (_Total) \Disk skrevne byte/sekund |BytesPerSecond|
|\PhysicalDisk (_Total) \Avg. Længde på disk kø|  Tæl|
|\PhysicalDisk (_Total) \Avg. Længde på disk læst kø| Tæl|
|\PhysicalDisk (_Total) \Avg. Længde på disk skrive kø |Tæl|
|\LogicalDisk(_Total)\% ledig plads| Procent|
|\LogicalDisk (_Total) \Free MB|   Tæl|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Beregne måleredskaber for Linux VM v2 som gæst OS

Når du opretter en ny VM (v2) i Azure, er diagnosticering aktiveret som standard ved hjælp af diagnosticering filtypenavn.

Du kan oprette en liste over målene ved hjælp af følgende kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Du kan oprette en besked på følgende målepunkter.

|Metriske navn                            |Enhed|
|---|---|
|\Memory\AvailableMemory                |Byte|
|\Memory\PercentAvailableMemory         |Procent|
|\Memory\UsedMemory                     |Byte|
|\Memory\PercentUsedMemory              |Procent|
|\Memory\PercentUsedByCache             |Procent|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Byte|
|\Memory\PercentAvailableSwap           |Procent|
|\Memory\UsedSwap                       |Byte|
|\Memory\PercentUsedSwap                |Procent|
|\Processor\PercentIdleTime             |Procent|
|\Processor\PercentUserTime             |Procent|
|\Processor\PercentNiceTime             |Procent|
|\Processor\PercentPrivilegedTime       |Procent|
|\Processor\PercentInterruptTime        |Procent|
|\Processor\PercentDPCTime              |Procent|
|\Processor\PercentProcessorTime        |Procent|
|\Processor\PercentIOWaitTime           |Procent|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Sekunder|
|\PhysicalDisk\AverageWriteTime         |Sekunder|
|\PhysicalDisk\AverageTransferTime      |Sekunder|
|\PhysicalDisk\AverageDiskQueueLength   |Tæl|
|\NetworkInterface\BytesTransmitted     |Byte|
|\NetworkInterface\BytesReceived        |Byte|
|\NetworkInterface\PacketsTransmitted   |Tæl|
|\NetworkInterface\PacketsReceived      |Tæl|
|\NetworkInterface\BytesTotal           |Byte|
|\NetworkInterface\TotalRxErrors        |Tæl|
|\NetworkInterface\TotalTxErrors        |Tæl|
|\NetworkInterface\TotalCollisions      |Tæl|




## <a name="commonly-used-web-server-farm-metrics"></a>Ofte anvendte Web (serverfarm) målepunkter

Du kan også udføre Autoskalering baseret på almindelige web server målepunkter som Http kø længde. Det er metriske navn er **HttpQueueLength**.  Følgende afsnit viser en liste over tilgængelige serverfarm (online) målepunkter.

### <a name="web-apps-metrics"></a>Web Apps målepunkter

Du kan oprette en liste over Web Apps-metrik ved hjælp af følgende kommando i PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Du kan Giv besked om eller skalere ved disse målepunkter.

|Metriske navn        |Enhed|
|---                |---|
|CpuPercentage      |Procent|
|MemoryPercentage   |Procent|
|DiskQueueLength    |Tæl|
|HttpQueueLength    |Tæl|
|BytesReceived      |Byte|
|BytesSent          |Byte|


## <a name="commonly-used-storage-metrics"></a>Ofte anvendte lagringsmål
Du kan tilpasse efter lagerplads kø længde, som er antallet af beskeder i køen lagerplads. Længde på lagerplads kø er en speciel metrikværdi og i grænseværdi for anvendt bliver antallet af beskeder hver forekomst. Det betyder, hvis der er to forekomster, og hvis grænsen er angivet til 100, det skaleres, når det samlede antal meddelelser i køen er 200. For eksempel 100 meddelelser hver forekomst.

Du kan konfigurere dette er i portalen Azure i bladet **Indstillinger** . Du kan opdatere indstillingen Autoskalering i skabelonen ARM for at bruge *metricName* som *ApproximateMessageCount* og overføre lagerplads køen ID som *metricResourceUri*VM skala sæt.

For eksempel med en klassisk lagerplads konto omfatter Autoskalering indstilling metricTrigger:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Ved (ikke-klassisk) lagerplads skal indeholde metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Ofte anvendte Service Bus målepunkter

Du kan tilpasse efter Service Bus kø længde, som er antallet af beskeder i køen Service Bus. Længde på Service Bus kø er en speciel metrikværdi og i grænseværdi for angivet anvendt bliver antallet af beskeder hver forekomst. Det betyder, hvis der er to forekomster, og hvis grænsen er angivet til 100, det skaleres, når det samlede antal meddelelser i køen er 200. For eksempel 100 meddelelser hver forekomst.

Du kan opdatere indstillingen Autoskalering i skabelonen ARM for at bruge *metricName* som *ApproximateMessageCount* og overføre lagerplads køen ID som *metricResourceUri*VM skala sæt.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Ressource gruppe som findes ikke for tjenesten Bus, men Azure ressourcestyring opretter en standard ressourcegruppe per område. Ressourcegruppen er som regel i formatet 'Standard - ServiceBus-[region]'. 'Standard-ServiceBus-EastUS', 'Standard-ServiceBus-WestUS', 'Standard-ServiceBus-AustraliaEast' osv.
