<properties
    pageTitle="Azure skærm målepunkter - understøttet måleredskaber per ressourcetype | Microsoft Azure"
    description="Liste over målepunkter, der er tilgængelige for hver ressourcetype med Azure skærm."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Understøttet måleredskaber med Azure skærm

Azure skærm er der flere måder til at interagere med statistik, herunder oprettelse af diagrammer dem i portalen, få adgang til dem via REST-API eller forespørgsler dem ved hjælp af PowerShell eller CLI. Er en komplet liste over alle målepunkter tilgængelige med Azure skærm metriske rørledning nedenfor.

> [AZURE.NOTE] Anden metrik kan være tilgængelige i portalen eller bruger ældre API'er. Listen indeholder kun prøveversionen målepunkter tilgængelige ved hjælp af prøveversionen af konsoliderede Azure skærm metriske rørledninger.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|CoreCount|Core Tæl|Tæl|Total|Samlet antal kerner kontoen batchen|
|TotalNodeCount|Node Tæl|Tæl|Total|Antallet af knuder på kontoen batchen|
|CreatingNodeCount|Oprette Node tælling|Tæl|Total|Antallet af knuder ved at blive oprettet|
|StartingNodeCount|Starte Node Tæl|Tæl|Total|Antallet af knuder starter|
|WaitingForStartTaskNodeCount|Venter For Start Node antal opgaver|Tæl|Total|Antallet af knuder venter på Start opgaven er afsluttet|
|StartTaskFailedNodeCount|Start opgave mislykkedes Node Tæl|Tæl|Total|Antallet af knuder, hvor den Start opgave mislykkes|
|IdleNodeCount|Inaktiv Node Tæl|Tæl|Total|Antallet af knuder, inaktiv|
|OfflineNodeCount|Offline Node Tæl|Tæl|Total|Antallet af knuder, offline|
|RebootingNodeCount|At du har genstartet Node Tæl|Tæl|Total|Antallet af at du har genstartet noder|
|ReimagingNodeCount|Reimaging Node Tæl|Tæl|Total|Antallet af knuder, reimaging|
|RunningNodeCount|Løbende Node optælling|Tæl|Total|Antallet af kører noder|
|LeavingPoolNodeCount|At forlade puljen Node Tæl|Tæl|Total|Antallet af knuder at forlade puljen|
|UnusableNodeCount|Ubrugelig Node Tæl|Tæl|Total|Antallet af knuder, ubrugelig|
|TaskStartEvent|Opgave Start begivenheder|Tæl|Total|Samlet antal opgaver, der har startet|
|TaskCompleteEvent|Opgave fuldført begivenheder|Tæl|Total|Samlet antal opgaver, der har fuldført|
|TaskFailEvent|Opgave fejl begivenheder|Tæl|Total|Samlet antal opgaver, der har fuldført i tilstanden mislykkedes|
|PoolCreateEvent|Puljen oprette begivenheder.|Tæl|Total|Samlet antal grupper, der er oprettet|
|PoolResizeStartEvent|Puljen tilpasning af størrelsen Start begivenheder|Tæl|Total|Samlet antal puljen ændrer størrelsen på, der har startet|
|PoolResizeCompleteEvent|Puljen tilpasning af størrelsen fuldført begivenheder|Tæl|Total|Samlet antal puljen ændrer størrelsen på, der har fuldført|
|PoolDeleteStartEvent|Puljen Slet Start begivenheder|Tæl|Total|Samlet antal puljen sletter, der har startet|
|PoolDeleteCompleteEvent|Puljen Slet fuldført begivenheder|Tæl|Total|Samlet antal puljen sletter, der har fuldført|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|connectedclients|Forbundne klienter|Tæl|Maksimum||
|totalcommandsprocessed|Samlet antal handlinger|Tæl|Total||
|cachehits|I cachen|Tæl|Total||
|cachemisses|Udgivelsescachen|Tæl|Total||
|getcommands|Får|Tæl|Total||
|setcommands|Sæt|Tæl|Total||
|evictedkeys|Udskilt taster|Tæl|Total||
|totalkeys|ALT-taster|Tæl|Maksimum||
|expiredkeys|Udløbet taster|Tæl|Total||
|usedmemory|Anvendte hukommelse|Byte|Maksimum||
|usedmemoryRss|Anvendte hukommelse RSS|Byte|Maksimum||
|serverLoad|Serverens belastning|Procent|Maksimum||
|cacheWrite|Skriv cache|BytesPerSecond|Maksimum||
|cacheRead|Cache læst|BytesPerSecond|Maksimum||
|percentProcessorTime|CPU|Procent|Maksimum||
|connectedclients0|Forbundne klienter (Shard 0)|Tæl|Maksimum||
|totalcommandsprocessed0|Samlet antal handlinger (Shard 0)|Tæl|Total||
|cachehits0|I cachen (Shard 0)|Tæl|Total||
|cachemisses0|Udgivelsescachen (Shard 0)|Tæl|Total||
|getcommands0|Får (Shard 0)|Tæl|Total||
|setcommands0|Sæt (Shard 0)|Tæl|Total||
|evictedkeys0|Udskilt taster (Shard 0)|Tæl|Total||
|totalkeys0|Samlet taster (Node 0)|Tæl|Maksimum||
|expiredkeys0|Udløbet taster (Shard 0)|Tæl|Total||
|usedmemory0|Anvendte hukommelse (Shard 0)|Byte|Maksimum||
|usedmemoryRss0|Anvendte hukommelse RSS (Shard 0)|Byte|Maksimum||
|serverLoad0|Serverens belastning (Shard 0)|Procent|Maksimum||
|cacheWrite0|Cache Skriv (Shard 0)|BytesPerSecond|Maksimum||
|cacheRead0|Cache læst (Shard 0)|BytesPerSecond|Maksimum||
|percentProcessorTime0|CPU (Shard 0)|Procent|Maksimum||
|connectedclients1|Forbundne klienter (Shard 1)|Tæl|Maksimum||
|totalcommandsprocessed1|Samlet antal handlinger (Shard 1)|Tæl|Total||
|cachehits1|I cachen (Shard 1)|Tæl|Total||
|cachemisses1|Udgivelsescachen (Shard 1)|Tæl|Total||
|getcommands1|Får (Shard 1)|Tæl|Total||
|setcommands1|Sæt (Shard 1)|Tæl|Total||
|evictedkeys1|Udskilt taster (Shard 1)|Tæl|Total||
|totalkeys1|Samlet taster (Node 1)|Tæl|Maksimum||
|expiredkeys1|Udløbet taster (Shard 1)|Tæl|Total||
|usedmemory1|Anvendte hukommelse (Shard 1)|Byte|Maksimum||
|usedmemoryRss1|Anvendte hukommelse RSS (Shard 1)|Byte|Maksimum||
|serverLoad1|Serverens belastning (Shard 1)|Procent|Maksimum||
|cacheWrite1|Cache Skriv (Shard 1)|BytesPerSecond|Maksimum||
|cacheRead1|Cache læst (Shard 1)|BytesPerSecond|Maksimum||
|percentProcessorTime1|CPU (Shard 1)|Procent|Maksimum||
|connectedclients2|Forbundne klienter (Shard 2)|Tæl|Maksimum||
|totalcommandsprocessed2|Samlet antal handlinger (Shard 2)|Tæl|Total||
|cachehits2|I cachen (Shard 2)|Tæl|Total||
|cachemisses2|Udgivelsescachen (Shard 2)|Tæl|Total||
|getcommands2|Får (Shard 2)|Tæl|Total||
|setcommands2|Sæt (Shard 2)|Tæl|Total||
|evictedkeys2|Udskilt taster (Shard 2)|Tæl|Total||
|totalkeys2|Samlet taster (Node 2)|Tæl|Maksimum||
|expiredkeys2|Udløbet taster (Shard 2)|Tæl|Total||
|usedmemory2|Anvendte hukommelse (Shard 2)|Byte|Maksimum||
|usedmemoryRss2|Anvendte hukommelse RSS (Shard 2)|Byte|Maksimum||
|serverLoad2|Serverens belastning (Shard 2)|Procent|Maksimum||
|cacheWrite2|Cache Skriv (Shard 2)|BytesPerSecond|Maksimum||
|cacheRead2|Cache læst (Shard 2)|BytesPerSecond|Maksimum||
|percentProcessorTime2|CPU (Shard 2)|Procent|Maksimum||
|connectedclients3|Forbundne klienter (Shard 3)|Tæl|Maksimum||
|totalcommandsprocessed3|Samlet antal handlinger (Shard 3)|Tæl|Total||
|cachehits3|I cachen (Shard 3)|Tæl|Total||
|cachemisses3|Udgivelsescachen (Shard 3)|Tæl|Total||
|getcommands3|Får (Shard 3)|Tæl|Total||
|setcommands3|Sæt (Shard 3)|Tæl|Total||
|evictedkeys3|Udskilt taster (Shard 3)|Tæl|Total||
|totalkeys3|Samlet taster (Node 3)|Tæl|Maksimum||
|expiredkeys3|Udløbet taster (Shard 3)|Tæl|Total||
|usedmemory3|Anvendte hukommelse (Shard 3)|Byte|Maksimum||
|usedmemoryRss3|Anvendte hukommelse RSS (Shard 3)|Byte|Maksimum||
|serverLoad3|Serverens belastning (Shard 3)|Procent|Maksimum||
|cacheWrite3|Cache Skriv (Shard 3)|BytesPerSecond|Maksimum||
|cacheRead3|Cache læst (Shard 3)|BytesPerSecond|Maksimum||
|percentProcessorTime3|CPU (Shard 3)|Procent|Maksimum||
|connectedclients4|Forbundne klienter (Shard 4)|Tæl|Maksimum||
|totalcommandsprocessed4|Samlet antal handlinger (Shard 4)|Tæl|Total||
|cachehits4|I cachen (Shard 4)|Tæl|Total||
|cachemisses4|Udgivelsescachen (Shard 4)|Tæl|Total||
|getcommands4|Får (Shard 4)|Tæl|Total||
|setcommands4|Sæt (Shard 4)|Tæl|Total||
|evictedkeys4|Udskilt taster (Shard 4)|Tæl|Total||
|totalkeys4|Samlet taster (Node 4)|Tæl|Maksimum||
|expiredkeys4|Udløbet taster (Shard 4)|Tæl|Total||
|usedmemory4|Anvendte hukommelse (Shard 4)|Byte|Maksimum||
|usedmemoryRss4|Anvendte hukommelse RSS (Shard 4)|Byte|Maksimum||
|serverLoad4|Serverens belastning (Shard 4)|Procent|Maksimum||
|cacheWrite4|Cache Skriv (Shard 4)|BytesPerSecond|Maksimum||
|cacheRead4|Cache læst (Shard 4)|BytesPerSecond|Maksimum||
|percentProcessorTime4|CPU (Shard 4)|Procent|Maksimum||
|connectedclients5|Forbundne klienter (Shard 5)|Tæl|Maksimum||
|totalcommandsprocessed5|Samlet antal handlinger (Shard 5)|Tæl|Total||
|cachehits5|I cachen (Shard 5)|Tæl|Total||
|cachemisses5|Udgivelsescachen (Shard 5)|Tæl|Total||
|getcommands5|Får (Shard 5)|Tæl|Total||
|setcommands5|Sæt (Shard 5)|Tæl|Total||
|evictedkeys5|Udskilt taster (Shard 5)|Tæl|Total||
|totalkeys5|Samlet taster (Node 5)|Tæl|Maksimum||
|expiredkeys5|Udløbet taster (Shard 5)|Tæl|Total||
|usedmemory5|Anvendte hukommelse (Shard 5)|Byte|Maksimum||
|usedmemoryRss5|Anvendte hukommelse RSS (Shard 5)|Byte|Maksimum||
|serverLoad5|Serverens belastning (Shard 5)|Procent|Maksimum||
|cacheWrite5|Cache Skriv (Shard 5)|BytesPerSecond|Maksimum||
|cacheRead5|Cache læst (Shard 5)|BytesPerSecond|Maksimum||
|percentProcessorTime5|CPU (Shard 5)|Procent|Maksimum||
|connectedclients6|Forbundne klienter (Shard 6)|Tæl|Maksimum||
|totalcommandsprocessed6|Samlet antal handlinger (Shard 6)|Tæl|Total||
|cachehits6|I cachen (Shard 6)|Tæl|Total||
|cachemisses6|Udgivelsescachen (Shard 6)|Tæl|Total||
|getcommands6|Får (Shard 6)|Tæl|Total||
|setcommands6|Sæt (Shard 6)|Tæl|Total||
|evictedkeys6|Udskilt taster (Shard 6)|Tæl|Total||
|totalkeys6|Samlet taster (Node 6)|Tæl|Maksimum||
|expiredkeys6|Udløbet taster (Shard 6)|Tæl|Total||
|usedmemory6|Anvendte hukommelse (Shard 6)|Byte|Maksimum||
|usedmemoryRss6|Anvendte hukommelse RSS (Shard 6)|Byte|Maksimum||
|serverLoad6|Serverens belastning (Shard 6)|Procent|Maksimum||
|cacheWrite6|Cache Skriv (Shard 6)|BytesPerSecond|Maksimum||
|cacheRead6|Cache læst (Shard 6)|BytesPerSecond|Maksimum||
|percentProcessorTime6|CPU (Shard 6)|Procent|Maksimum||
|connectedclients7|Forbundne klienter (Shard 7)|Tæl|Maksimum||
|totalcommandsprocessed7|Samlet antal handlinger (Shard 7)|Tæl|Total||
|cachehits7|I cachen (Shard 7)|Tæl|Total||
|cachemisses7|Udgivelsescachen (Shard 7)|Tæl|Total||
|getcommands7|Får (Shard 7)|Tæl|Total||
|setcommands7|Sæt (Shard 7)|Tæl|Total||
|evictedkeys7|Udskilt taster (Shard 7)|Tæl|Total||
|totalkeys7|Samlet taster (Node 7)|Tæl|Maksimum||
|expiredkeys7|Udløbet taster (Shard 7)|Tæl|Total||
|usedmemory7|Anvendte hukommelse (Shard 7)|Byte|Maksimum||
|usedmemoryRss7|Anvendte hukommelse RSS (Shard 7)|Byte|Maksimum||
|serverLoad7|Serverens belastning (Shard 7)|Procent|Maksimum||
|cacheWrite7|Cache Skriv (Shard 7)|BytesPerSecond|Maksimum||
|cacheRead7|Cache læst (Shard 7)|BytesPerSecond|Maksimum||
|percentProcessorTime7|CPU (Shard 7)|Procent|Maksimum||
|connectedclients8|Forbundne klienter (Shard 8)|Tæl|Maksimum||
|totalcommandsprocessed8|Samlet antal handlinger (Shard 8)|Tæl|Total||
|cachehits8|I cachen (Shard 8)|Tæl|Total||
|cachemisses8|Udgivelsescachen (Shard 8)|Tæl|Total||
|getcommands8|Får (Shard 8)|Tæl|Total||
|setcommands8|Sæt (Shard 8)|Tæl|Total||
|evictedkeys8|Udskilt taster (Shard 8)|Tæl|Total||
|totalkeys8|Samlet taster (Node 8)|Tæl|Maksimum||
|expiredkeys8|Udløbet taster (Shard 8)|Tæl|Total||
|usedmemory8|Anvendte hukommelse (Shard 8)|Byte|Maksimum||
|usedmemoryRss8|Anvendte hukommelse RSS (Shard 8)|Byte|Maksimum||
|serverLoad8|Serverens belastning (Shard 8)|Procent|Maksimum||
|cacheWrite8|Cache Skriv (Shard 8)|BytesPerSecond|Maksimum||
|cacheRead8|Cache læst (Shard 8)|BytesPerSecond|Maksimum||
|percentProcessorTime8|CPU (Shard 8)|Procent|Maksimum||
|connectedclients9|Forbundne klienter (Shard 9)|Tæl|Maksimum||
|totalcommandsprocessed9|Samlet antal handlinger (Shard 9)|Tæl|Total||
|cachehits9|I cachen (Shard 9)|Tæl|Total||
|cachemisses9|Udgivelsescachen (Shard 9)|Tæl|Total||
|getcommands9|Får (Shard 9)|Tæl|Total||
|setcommands9|Sæt (Shard 9)|Tæl|Total||
|evictedkeys9|Udskilt taster (Shard 9)|Tæl|Total||
|totalkeys9|Samlet taster (Node 9)|Tæl|Maksimum||
|expiredkeys9|Udløbet taster (Shard 9)|Tæl|Total||
|usedmemory9|Anvendte hukommelse (Shard 9)|Byte|Maksimum||
|usedmemoryRss9|Anvendte hukommelse RSS (Shard 9)|Byte|Maksimum||
|serverLoad9|Serverens belastning (Shard 9)|Procent|Maksimum||
|cacheWrite9|Cache Skriv (Shard 9)|BytesPerSecond|Maksimum||
|cacheRead9|Cache læst (Shard 9)|BytesPerSecond|Maksimum||
|percentProcessorTime9|CPU (Shard 9)|Procent|Maksimum||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|NumberOfCalls|Samlet antal API-kald|Tæl|Total|Samlet antal API opkald.|
|NumberOfFailedCalls|Samlet antal mislykkede API-opkald|Tæl|Total|Samlet antal mislykkede API-opkald.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Gennemsnit|Procentdelen af tildelte Beregn enheder, der er i øjeblikket bruges af Virtual Machine(s)|
|Netværk i|Netværk i|Byte|Total|Antallet byte, der modtages på alle netværksgrænseflader af Virtual Machine(s) (indgående trafik)|
|Netværk ud|Netværk ud|Byte|Total|Antallet af byte ud på alle netværksgrænseflader ved Virtual Machine(s) (udgående trafik)|
|Disken læse byte|Disken læse byte|Byte|Total|Samlet antal byte, der læses fra disk under overvågning periode|
|Disken Skriv byte|Disken Skriv byte|Byte|Total|Samlet antal byte, der skrives til disken under overvågning periode|
|Læs handlinger/Sec disk|Læs handlinger/Sec disk|CountPerSecond|Gennemsnit|Disken læst IOP'ER|
|Skrevne handlinger/sekund|Skrevne handlinger/sekund|CountPerSecond|Gennemsnit|Skrivning IOP'ER|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Gennemsnit|Procentdelen af tildelte Beregn enheder, der er i øjeblikket bruges af Virtual Machine(s)|
|Netværk i|Netværk i|Byte|Total|Antallet byte, der modtages på alle netværksgrænseflader af Virtual Machine(s) (indgående trafik)|
|Netværk ud|Netværk ud|Byte|Total|Antallet af byte ud på alle netværksgrænseflader ved Virtual Machine(s) (udgående trafik)|
|Disken læse byte|Disken læse byte|Byte|Total|Samlet antal byte, der læses fra disk under overvågning periode|
|Disken Skriv byte|Disken Skriv byte|Byte|Total|Samlet antal byte, der skrives til disken under overvågning periode|
|Læs handlinger/Sec disk|Læs handlinger/Sec disk|CountPerSecond|Gennemsnit|Disken læst IOP'ER|
|Skrevne handlinger/sekund|Skrevne handlinger/sekund|CountPerSecond|Gennemsnit|Skrivning IOP'ER|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|Procent CPU|Procent CPU|Procent|Gennemsnit|Procentdelen af tildelte Beregn enheder, der er i øjeblikket bruges af Virtual Machine(s)|
|Netværk i|Netværk i|Byte|Total|Antallet byte, der modtages på alle netværksgrænseflader af Virtual Machine(s) (indgående trafik)|
|Netværk ud|Netværk ud|Byte|Total|Antallet af byte ud på alle netværksgrænseflader ved Virtual Machine(s) (udgående trafik)|
|Disken læse byte|Disken læse byte|Byte|Total|Samlet antal byte, der læses fra disk under overvågning periode|
|Disken Skriv byte|Disken Skriv byte|Byte|Total|Samlet antal byte, der skrives til disken under overvågning periode|
|Læs handlinger/Sec disk|Læs handlinger/Sec disk|CountPerSecond|Gennemsnit|Disken læst IOP'ER|
|Skrevne handlinger/sekund|Skrevne handlinger/sekund|CountPerSecond|Gennemsnit|Skrivning IOP'ER|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Telemetri meddelelse Send forsøg|Tæl|Total|Antallet af enhed til skyen telemetri meddelelser forsøges skal sendes til din IoT-hub|
|d2c.telemetry.Ingress.success|Telemetri meddelelser, som sendes|Tæl|Total|Antallet af enhed til skyen telemetri meddelelser, der er sendt til din IoT-hub|
|c2d.commands.Egress.Complete.success|Kommandoer, der er fuldført|Tæl|Total|Antallet af skyen til enhed kommandoer blev gennemført af enheden|
|c2d.commands.Egress.Abandon.success|Kommandoer efterladt uden vedligeholdelse|Tæl|Total|Antallet af skyen til enhed kommandoer efterladt uden vedligeholdelse af enheden|
|c2d.commands.Egress.Reject.success|Kommandoer, afvist|Tæl|Total|Antallet af skyen til enhed kommandoer afvist af enheden|
|devices.totalDevices|Samlede enheder|Tæl|Total|Antal enheder, der er registreret til IoT-hub|
|devices.connectedDevices.allProtocol|Forbundne enheder|Tæl|Total|Antal enheder, der er forbundet til IoT-hub|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|INREQS|Indgående anmodninger|Tæl|Total|Begivenhed Hub indgående meddelelse overførsel for et navneområde|
|SUCCREQ|Vellykkede forespørgsler|Tæl|Total|Samlet vellykket anmodninger om et navneområde|
|FAILREQ|Mislykkede anmodninger|Tæl|Total|Samlet antal mislykkede anmodninger til et navneområde|
|SVRBSY|Optaget serverfejl|Tæl|Total|Samlet server optaget fejl for et navneområde|
|INTERR|Intern serverfejl|Tæl|Total|Samlet intern serverfejl for et navneområde|
|MISCERR|Andre fejl|Tæl|Total|Samlet antal mislykkede anmodninger til et navneområde|
|INMSGS|Indgående meddelelser|Tæl|Total|Samlet indgående meddelelser til et navneområde|
|OUTMSGS|Udgående meddelelser|Tæl|Total|Total udgående meddelelser til et navneområde|
|EHINMBS|Indgående byte sekundet|BytesPerSecond|Total|Begivenhed Hub indgående meddelelse overførsel for et navneområde|
|EHOUTMBS|Udgående byte sekundet|BytesPerSecond|Total|Total udgående meddelelser til et navneområde|
|EHABL|Arkiv ordrebeholdningen meddelelser|Tæl|Total|Begivenhed Hub arkivering af e-mails i ordrebeholdningen for et navneområde|
|EHAMSGS|Arkivering af e-mails|Tæl|Total|Begivenhed Hub arkiverede meddelelser i et navneområde|
|EHAMBS|Arkiv meddelelse overførselshastighed|BytesPerSecond|Total|Begivenhed Hub arkiveret meddelelse overførselshastighed i et navneområde|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|RunsStarted|Kører i gang|Tæl|Total|Antallet af arbejdsprocessen kører Introduktion.|
|RunsCompleted|Kører fuldført|Tæl|Total|Antallet af arbejdsprocessen kører færdige.|
|RunsSucceeded|Kører lykkedes|Tæl|Total|Antallet af arbejdsprocessen kører er fuldført.|
|RunsFailed|Kører mislykkedes|Tæl|Total|Antallet af arbejdsprocessen kører mislykkedes.|
|RunsCancelled|Kører annulleret|Tæl|Total|Antallet af arbejdsprocessen kører annullerede.|
|RunLatency|Køre ventetid|Sekunder|Gennemsnit|Ventetid på færdige arbejdsprocessen kører.|
|RunSuccessLatency|Køre succes ventetid|Sekunder|Gennemsnit|Ventetid på lykkedes arbejdsprocessen kører.|
|RunThrottledEvents|Køre begrænset hændelser|Tæl|Total|Antallet af arbejdsproceshandlingen eller udløser begrænset begivenheder.|
|RunFailurePercentage|Køre manglende procentdel|Procent|Total|Procentdel af arbejdsprocessen kører mislykkedes.|
|ActionsStarted|Handlinger, der er i gang |Tæl|Total|Antallet af arbejdsproceshandlinger i gang.|
|ActionsCompleted|Handlinger, der er fuldført |Tæl|Total|Antallet af arbejdsproceshandlinger fuldført.|
|ActionsSucceeded|Handlinger lykkedes |Tæl|Total|Antallet af arbejdsproceshandlinger lykkedes.|
|ActionsFailed|Handlinger mislykkedes|Tæl|Total|Antal arbejdsproceshandlinger mislykkedes.|
|ActionsSkipped|Handlinger ignoreret |Tæl|Total|Antallet af arbejdsproceshandlinger ignoreret.|
|ActionLatency|Handling ventetid |Sekunder|Gennemsnit|Forsinkelse af fuldførte arbejdsproceshandlinger.|
|ActionSuccessLatency|Handling succes ventetid |Sekunder|Gennemsnit|Ventetid på lykkedes arbejdsproceshandlinger.|
|ActionThrottledEvents|Handling begrænset begivenheder|Tæl|Total|Antallet af arbejdsproceshandlingen begrænset begivenheder..|
|TriggersStarted|Udløsere, der er i gang |Tæl|Total|Antallet af arbejdsproces udløsere i gang.|
|TriggersCompleted|Udløsere fuldført |Tæl|Total|Antallet af arbejdsproces udløsere fuldført.|
|TriggersSucceeded|Udløsere lykkedes |Tæl|Total|Antallet af arbejdsproces udløsere lykkedes.|
|TriggersFailed|Udløsere mislykkedes |Tæl|Total|Antallet af arbejdsproces udløsere mislykkedes.|
|TriggersSkipped|Udløsere ignoreret|Tæl|Total|Antallet af arbejdsproces udløsere ignoreret.|
|TriggersFired|Udløsere udløses |Tæl|Total|Antallet af arbejdsproces udløsere udløses.|
|TriggerLatency|Udløser ventetid |Sekunder|Gennemsnit|Forsinkelse af færdige arbejdsproces udløsere.|
|TriggerFireLatency|Udløser Fire ventetid |Sekunder|Gennemsnit|Ventetid på udløses arbejdsproces udløsere.|
|TriggerSuccessLatency|Udløser succes ventetid |Sekunder|Gennemsnit|Ventetid på lykkedes arbejdsproces udløsere.|
|TriggerThrottledEvents|Udløser begrænset begivenheder|Tæl|Total|Antallet af arbejdsproces udløser begrænset begivenheder.|
|BillableActionExecutions|Fakturerbar handling udførelser|Tæl|Total|Antallet af arbejdsproces handling udførelser få faktureret.|
|BillableTriggerExecutions|Fakturerbar udløser udførelser|Tæl|Total|Antallet af arbejdsproces udløser udførelser få faktureret.|
|TotalBillableExecutions|Samlet fakturerbar udførelser|Tæl|Total|Antallet af arbejdsproces udførelser få faktureret.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|Overførselshastighed|Overførselshastighed|BytesPerSecond|Gennemsnit||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|SearchLatency|Ventetid for Søg|Sekunder|Gennemsnit|Gennemsnitlig søgning ventetid for søgetjenesten|
|SearchQueriesPerSecond|Søgeforespørgsler sekundet|CountPerSecond|Gennemsnit|Søgeforespørgsler sekundet til søgetjenesten|
|ThrottledSearchQueriesPercentage|Begrænset søgning forespørgsler procentdel|Procent|Gennemsnit|Procentdel af søgeforespørgsler, der er begrænset til søgetjenesten|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|CPUXNS|CPU-brug per navneområde|Procent|Maksimum|Tjenesten bus premium navneområde CPU brugen metrisk|
|WSXNS|Størrelse hukommelsesforbrug per navneområde|Procent|Maksimum|Tjenesten bus premium navneområde hukommelse brugen metrisk|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|cpu_percent|CPU procentdel|Procent|Gennemsnit|CPU procentdel|
|physical_data_read_percent|Data EY procentdel|Procent|Gennemsnit|Data EY procentdel|
|log_write_percent|Log EY procentdel|Procent|Gennemsnit|Log EY procentdel|
|dtu_consumption_percent|DTU procentdel|Procent|Gennemsnit|DTU procentdel|
|lagerplads|Samlede databasestørrelse|Byte|Maksimum|Samlede databasestørrelse|
|connection_successful|Vellykkede forbindelser|Tæl|Total|Vellykkede forbindelser|
|connection_failed|Mislykkede forbindelser|Tæl|Total|Mislykkede forbindelser|
|blocked_by_firewall|Blokeret af Firewall|Tæl|Total|Blokeret af Firewall|
|baglås|Baglåse|Tæl|Total|Baglåse|
|storage_percent|Database størrelse procentdel|Procent|Maksimum|Database størrelse procentdel|
|xtp_storage_percent|I hukommelsen OLTP lagerplads percent(Preview)|Procent|Gennemsnit|I hukommelsen OLTP lagerplads percent(Preview)|
|workers_percent|Medarbejdere procentdel|Procent|Gennemsnit|Medarbejdere procent|
|sessions_percent|Sessioner procent|Procent|Gennemsnit|Sessioner procent|
|dtu_limit|DTU grænse|Tæl|Gennemsnit|DTU grænse|
|dtu_used|DTU bruges|Tæl|Gennemsnit|DTU bruges|
|service_level_objective|Tjenesten niveau formålet med databasen|Tæl|Total|Tjenesten niveau formålet med databasen|
|dwu_limit|dwu grænse|Tæl|Maksimum|dwu grænse|
|dwu_consumption_percent|DWU procentdel|Procent|Gennemsnit|DWU procentdel|
|dwu_used|DWU bruges|Tæl|Gennemsnit|DWU bruges|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|cpu_percent|CPU procentdel|Procent|Gennemsnit|CPU procentdel|
|physical_data_read_percent|Data EY procentdel|Procent|Gennemsnit|Data EY procentdel|
|log_write_percent|Log EY procentdel|Procent|Gennemsnit|Log EY procentdel|
|dtu_consumption_percent|DTU procentdel|Procent|Gennemsnit|DTU procentdel|
|storage_percent|Lagerplads procentdel|Procent|Gennemsnit|Lagerplads procentdel|
|workers_percent|Medarbejdere procent|Procent|Gennemsnit|Medarbejdere procent|
|sessions_percent|Sessioner procent|Procent|Gennemsnit|Sessioner procent|
|eDTU_limit|eDTU grænse|Tæl|Gennemsnit|eDTU grænse|
|storage_limit|Lagergrænse|Byte|Gennemsnit|Lagergrænsen|
|eDTU_used|eDTU bruges|Tæl|Gennemsnit|eDTU bruges|
|storage_used|Anvendt lagerplads|Byte|Gennemsnit|Anvendt lagerplads|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|ResourceUtilization|SØ % udnyttelse|Procent|Maksimum|SØ % udnyttelse|
|InputEvents|Indtast begivenheder|Tæl|Total|Indtast begivenheder|
|InputEventBytes|Indtast begivenhed byte|Byte|Total|Indtast begivenhed byte|
|LateInputEvents|Sen Input begivenheder|Tæl|Total|Sen Input begivenheder|
|OutputEvents|Output begivenheder|Tæl|Total|Output begivenheder|
|ConversionErrors|Datakonverteringsfejl|Tæl|Total|Datakonverteringsfejl|
|Fejl|Kørselsfejl|Tæl|Total|Kørselsfejl|
|DroppedOrAdjustedEvents|Defekt begivenheder|Tæl|Total|Defekt begivenheder|
|AMLCalloutRequests|Funktionen anmodninger|Tæl|Total|Funktionen anmodninger|
|AMLCalloutFailedRequests|Mislykkedes funktionen anmodninger|Tæl|Total|Mislykkedes funktionen anmodninger|
|AMLCalloutInputEvents|Funktionen begivenheder|Tæl|Total|Funktionen begivenheder|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|CpuPercentage|CPU procentdel|Procent|Gennemsnit|CPU procentdel|
|MemoryPercentage|Procent af hukommelsen|Procent|Gennemsnit|Procent af hukommelsen|
|DiskQueueLength|Længde på disk kø|Tæl|Total|Længde på disk kø|
|HttpQueueLength|Længde på http-kø|Tæl|Total|Længde på http-kø|
|BytesReceived|Data i|Byte|Total|Data i|
|BytesSent|Data ud|Byte|Total|Data ud|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Total|CPU-tid|
|Anmodninger om|Anmodninger om|Tæl|Total|Anmodninger om|
|BytesReceived|Data i|Byte|Total|Data i|
|BytesSent|Data ud|Byte|Total|Data ud|
|Http2xx|HTTP-2xx|Tæl|Total|HTTP-2xx|
|Http3xx|HTTP-3xx|Tæl|Total|HTTP-3xx|
|Http401|HTTP 401|Tæl|Total|HTTP 401|
|Http403|HTTP 403|Tæl|Total|HTTP 403|
|Http404|HTTP 404|Tæl|Total|HTTP 404|
|Http406|HTTP 406|Tæl|Total|HTTP 406|
|Http4xx|HTTP-4xx|Tæl|Total|HTTP-4xx|
|Http5xx|HTTP-serverfejl|Tæl|Total|HTTP-serverfejl|
|MemoryWorkingSet|Arbejdssæt til hukommelse|Byte|Total|Arbejdssæt til hukommelse|
|AverageMemoryWorkingSet|Gennemsnitlig hukommelse arbejdssæt|Byte|Gennemsnit|Gennemsnitlig hukommelse arbejdssæt|
|AverageResponseTime|Gennemsnitlig svartid|Sekunder|Gennemsnit|Gennemsnitlig svartid|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrisk|Metriske visningsnavn|Enhed|Diagramtypen|Beskrivelse|
|---|---|---|---|---|
|CpuTime|CPU-tid|Sekunder|Total|CPU-tid|
|Anmodninger om|Anmodninger om|Tæl|Total|Anmodninger om|
|BytesReceived|Data i|Byte|Total|Data i|
|BytesSent|Data ud|Byte|Total|Data ud|
|Http2xx|HTTP-2xx|Tæl|Total|HTTP-2xx|
|Http3xx|HTTP-3xx|Tæl|Total|HTTP-3xx|
|Http401|HTTP 401|Tæl|Total|HTTP 401|
|Http403|HTTP 403|Tæl|Total|HTTP 403|
|Http404|HTTP 404|Tæl|Total|HTTP 404|
|Http406|HTTP 406|Tæl|Total|HTTP 406|
|Http4xx|HTTP-4xx|Tæl|Total|HTTP-4xx|
|Http5xx|HTTP-serverfejl|Tæl|Total|HTTP-serverfejl|
|MemoryWorkingSet|Arbejdssæt til hukommelse|Byte|Total|Arbejdssæt til hukommelse|
|AverageMemoryWorkingSet|Gennemsnitlig hukommelse arbejdssæt|Byte|Gennemsnit|Gennemsnitlig hukommelse arbejdssæt|
|AverageResponseTime|Gennemsnitlig svartid|Sekunder|Gennemsnit|Gennemsnitlig svartid|

## <a name="next-steps"></a>Næste trin

- [Læs om statistik i Azure skærm](./monitoring-overview.md#monitoring-sources)
- [Opret beskeder på målepunkter](./insights-receive-alert-notifications.md)
- [Eksportere målepunkter til lagerplads, begivenhed Hub eller Log Analytics](./monitoring-overview-of-diagnostic-logs.md)
