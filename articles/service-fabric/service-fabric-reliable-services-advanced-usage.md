<properties
   pageTitle="Avancerede brugen af pålidelig tjenester | Microsoft Azure"
   description="Få mere at vide om avancerede brugen af tjenesten strukturerede pålidelig Services for større fleksibilitet i dine tjenester."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Avancerede brugen af pålidelig tjenesterne programming model
Azure Service-strukturen forenkler skrivning og administration af pålidelig uden status og med høj sikkerhed tjenester. Denne vejledning vil tale om Avanceret brug af pålidelig Services til at få mere kontrol og fleksibilitet over dine tjenester. Før du læse denne vejledning, blive fortrolig med [Pålidelig tjenesterne programming model](service-fabric-reliable-services-introduction.md).

Både med høj sikkerhed og uden status tjenester har to primære indgangspunkter for brugerkode:

 - `RunAsync`er en generel indgangspunkt for din tjenestekode.
 - `CreateServiceReplicaListeners`og `CreateServiceInstanceListeners` er for at åbne kommunikation lyttere efter klient-anmodninger.
 
I de fleste tjenester er disse to indgangspunkter tilstrækkelige. I sjældne tilfælde når mere kontrol over en tjeneste livscyklus er påkrævet, yderligere livscyklus hændelser er tilgængelige.

## <a name="stateless-service-instance-lifecycle"></a>Uden status service forekomst livscyklus

En uden status tjeneste livscyklus er meget simpel. En uden status tjeneste kan kun åbnes, lukket eller afbrudt. `RunAsync`i en uden status tjeneste der udføres, når en forekomst af tjenesten er åbnet, og annulleres, når en forekomst af tjenesten lukkes eller afbrudt. 

Selvom `RunAsync` skal være tilstrækkelige i næsten alle tilfælde, Åbn, Luk, og Afbryd begivenheder i en uden status tjeneste er også tilgængelige:

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  OnOpenAsync kaldes, når forekomsten af uden status er ved at blive brugt. Udvidet service initialisering opgaver kan startes på nuværende tidspunkt.

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync kaldes, når forekomsten af uden status vil blive problemfrit lukket. Dette kan ske, når den tjenestekode skal opgraderes, forekomsten af flyttes på grund af belastning eller en midlertidig fejl registreres. OnCloseAsync kan bruges til at sikkert Luk ressourcerne, stoppe en hvilken som helst behandler i baggrunden, at gemme eksterne tilstand eller lukke eksisterende forbindelser.

- `void OnAbort()`
  OnAbort kaldes, når forekomsten af uden status tvungen der lukkes. Dette kaldes Generelt, når der er fundet en permanent fejl på noden, eller når tjenesten strukturen pålideligt ikke kan administrere forekomst af tjenesten livscyklus på grund af interne fejl.

## <a name="stateful-service-replica-lifecycle"></a>Med høj sikkerhed service replika livscyklus

En med høj sikkerhed service replikas livscyklus er meget mere kompleks end en forekomst af uden status tjenesten. Hvis du vil åbne, lukke og afbryde begivenheder, gennemgår en med høj sikkerhed service replika desuden rolle ændringer i dets levetid. Når en med høj sikkerhed service replika ændres rolle, den `OnChangeRoleAsync` hændelse udløses:

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync kaldes, når med høj sikkerhed service replikaen ændres rolle, for eksempel til primær eller sekundær. Primære replikaer får status for skrivning (har tilladelse til at oprette og skrive til pålidelig af websteder). Sekundær replikaer er givet læsestatus (kan kun læse fra eksisterende pålidelig af websteder). De fleste arbejdet i en med høj sikkerhed tjeneste er udført på den primære replika. Sekundær replikaer kan udføre skrivebeskyttet validering, oprettelse af rapporter, datamining eller andre skrivebeskyttet job.

I en med høj sikkerhed tjeneste, kun den primære replika har skriveadgang til tilstand og dermed er normalt når tjenesten udfører faktisk arbejde. Den `RunAsync` metode i en med høj sikkerhed tjeneste køres kun, når med høj sikkerhed service replikaen er primære. Den `RunAsync` metode er annulleret, når en primær replikas rolle ændres væk fra primær og under de Luk og afbrydelse af hændelser. 

Ved hjælp af den `OnChangeRoleAsync` begivenhed gør det muligt at udføre arbejdet afhængigt af replika rolle samt som svar til rolle ændring.

En med høj sikkerhed tjeneste indeholder også de samme fire livscyklus hændelser som en uden status tjeneste, med samme semantik og use cases:

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>Næste trin
Få mere avancerede emner, der er relateret til tjenesten strukturen, i følgende artikler:

- [Konfiguration af med høj sikkerhed pålidelig Services](service-fabric-reliable-services-configuration.md)

- [Tjenesten strukturen sundhed Introduktion](service-fabric-health-introduction.md)

- [Ved hjælp af system tilstandsrapporter til fejlfinding](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Konfiguration af Services med Service strukturen klynge ressourcestyring](service-fabric-cluster-resource-manager-configure-services.md)
