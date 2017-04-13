<properties
    pageTitle="Maksimere batchen node bruges sammen med parallelle opgaver | Microsoft Azure"
    description="Øge effektiviteten og reducere omkostningerne ved hjælp af færre Beregn noder og køre samtidige opgaver på hver node i en Azure batchen puljen"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Maksimere Azure batchen Beregn Ressourceforbrug med samtidige node opgaver

Ved at køre mere end én opgave samtidigt på hver beregningsnode i din Azure batchen puljen, kan du maksimere ressourceforbrug på et mindre antal noder i puljen. For nogle arbejdsbelastninger, kan det resultere i kortere jobtider og billigere.

Mens nogle scenarier få glæde af dedikere alle en node ressourcer til en enkelt opgave, mange situationer få glæde af tillade flere opgaver til deling af disse ressourcer:

 - **Minimere dataoverførsel** , når opgaver er muligt at dele data. I dette scenarie skal reducere du dramatisk filoverførsel datagebyrer ved at kopiere delte data til et mindre antal noder og udfører opgaver parallelt på hver node. Dette gælder især hvis dataene, der skal kopieres til hver node skal overføres mellem geografiske områder.

 - Når opgaver kræver meget hukommelse, men kun i korte perioder af tid, og variable tiden under kørsel af **Maximizing hukommelsesforbrug** . Du kan anvende færre, men større, Beregn noder med mere hukommelse til at håndtere effektivt sådanne spidser. Disse noder ville have flere opgaver, der kører parallelt på hver node, men de enkelte opgaver vil drage fordel af de noder stort hukommelse på forskellige tidspunkter.

 - **Mindske node tal begrænsninger** , når mellem noder kommunikation er påkrævet i en gruppe. I øjeblikket, er grupper, der er konfigureret til kommunikation mellem noder begrænset til 50 Beregn noder. Hvis hver node i denne gruppe kan udføre opgaver parallelt, kan et større antal opgaver udføres samtidigt.

 - **Replikering et lokalt beregne klynge**, som når du først flytte et Beregn miljø til Azure. Hvis din aktuelle lokale løsning udfører flere opgaver per beregningsnode, kan du øge det maksimale antal node opgaver til nærmere spejling af konfigurationen.

## <a name="example-scenario"></a>Eksempel-scenario

Som eksempel for at illustrere fordelene ved udførelse af parallel opgave, Antag, at dit opgave-program har CPU og hukommelse krav så [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) noder er tilstrækkelige. Men for at afslutte jobbet inden for den nødvendige tid, der skal bruges 1.000 af disse noder.

I stedet for ved hjælp af Standard\_D1 noder, der har 1 CPU core, kan du bruge [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) noder, der har 16 kerner, og Aktivér udførelse af parallel opgave. Derfor *16 gange færre noder* kan bruges – i stedet for 1.000 noder, kun 63 ville være nødvendige. Desuden Hvis store programfiler eller referencedata, der kræves for hver enkelt node, er jobbet varighed og effektivitet igen forbedret, da data, der kopieres til kun 16 noder.

## <a name="enable-parallel-task-execution"></a>Aktivere udførelse af parallel opgave

Du kan konfigurere Beregn noder til udførelse af parallel opgave på niveauet for gruppen. Angive [CloudPool.MaxTasksPerComputeNode] med biblioteket batchen .NET[ maxtasks_net] egenskab, når du opretter en gruppe. Hvis du bruger batchen REST-API, angive [maxTasksPerNode] [ rest_addpool] element i anmodningsteksten under oprettelse af puljen.

Azure batchen gør det muligt at angive maksimale opgaver per node op til fire gange (4 x) antallet af node kerner. Eksempelvis hvis gruppen er konfigureret med noder størrelse "Store" (fire kerner), derefter `maxTasksPerNode` kan indstilles til 16. Du kan finde oplysninger om antallet kerner for hvert af node størrelserne, [størrelser for Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Se [kvoter og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md)kan finde flere oplysninger om begrænsninger for tjenesten.

> [AZURE.TIP] Sørg for at tage hensyn til de `maxTasksPerNode` værdi, når du opbygger en [Autoskalering formel] [ enable_autoscaling] for din gruppe. For eksempel en formel, der evalueres `$RunningTasks` dramatisk kunne påvirkes af et større opgaver per node. Yderligere oplysninger finder du i [Automatisk skala beregne knuder på en Azure batchen puljen](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Fordelingen af opgaver

Ved Beregn knuderne i en gruppe kan udføre opgaver samtidig, er det vigtigt at angive, hvordan opgaver skal fordeles på tværs af knuderne i puljen.

Ved hjælp af [CloudPool.TaskSchedulingPolicy] [ task_schedule] ejendom, kan du angive, at opgaverne skal tildeles jævnt på tværs af alle noder i puljen ("sprede"). Eller du kan angive, så mange opgaver som muligt skal tildeles til hver node før opgaverne tildeles til en anden node i puljen ("følgesedler").

Overvej puljen af som et eksempel af, hvordan denne funktion er værdifulde [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) noder (i eksemplet ovenfor), der er konfigureret med en [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] værdien af 16. Hvis [CloudPool.TaskSchedulingPolicy] [ task_schedule] er konfigureret med en [ComputeNodeFillType] [ fill_type] af *sprogpakke*, vil det maksimere brugen af alle 16 borekernerne for hver node og tillade en [Autoskalering puljen](batch-automatic-scaling.md) at rydde ubrugte noder fra puljen (noder uden tildelt nogen opgaver). Dette minimerer Ressourceforbrug og sparer penge.

## <a name="batch-net-example"></a>Batchen .NET eksempel

Denne [Batchen .NET] [ api_net] API kodestykke viser en anmodning om at oprette en gruppe, som indeholder fire store noder med højst fire opgaver per node. Det angiver en opgave, planlægning politik, der skal opfylde hver node med opgaver, før du tildele opgaver til en anden node i puljen. Du kan finde flere oplysninger om tilføjelse af grupper ved hjælp af batchen .NET API, se [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batchen RESTEN eksempel

Denne [Batchen RESTEN] [ api_rest] API kodestykke viser en anmodning om at oprette en gruppe, der indeholder to store noder med højst fire opgaver per node. Se [tilføje en gruppe til en konto], du kan finde flere oplysninger om tilføjelse af grupper ved hjælp af REST-API[rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Du kan angive den `maxTasksPerNode` element og [MaxTasksPerComputeNode] [ maxtasks_net] egenskab kun på klokkeslættet for oprettelsen af puljen. De kan ikke ændres, når en gruppe er blevet oprettet.

## <a name="code-sample"></a>Eksempel på kode

[ParallelNodeTasks] [ parallel_tasks_sample] projekt på GitHub illustrerer brugen af [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] egenskab.

Dette C# console program bruger [Batchen .NET] [ api_net] bibliotek til at oprette en gruppe med en eller flere Beregn noder. Det udfører en konfigurerbar antallet af opgaver på disse noder til at simulere variable Indlæs. Output fra programmet angiver, hvilke knuder køres hver opgave. Programmet indeholder også en oversigt over de parametre for jobbet og varighed. Den oversigt del af output fra to forskellige køres af eksempelprogrammet vises nedenfor.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Første kørsel af eksempelprogrammet viser, at med en enkelt node i puljen og standardindstillingen for en opgave per node, jobbet varighed er over 30 minutter.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

Andet køres af eksempel viser en betydeligt nedsat job varighed. Dette skyldes, at puljen blev konfigureret med fire opgaver per node, som giver mulighed for parallel opgave udførelse af til at fuldføre jobbet i næsten et kvartal for klokkeslættet.

> [AZURE.NOTE] Jobbet varigheder i de ovenstående opsummeringer skal ikke medtage klokkeslættet for oprettelsen af puljen. Hvert af de ovenfor job blev sendt til tidligere oprettet grupper, hvis Beregn noder var i tilstanden *inaktiv* på indsendelser tidspunkt.

## <a name="next-steps"></a>Næste trin

### <a name="batch-explorer-heat-map"></a>Batchen Explorer Varmekort

[Azure batchen Explorer][batch_explorer], en af de Azure batchen [eksempelprogrammer][github_samples], indeholder en *Varmekort* funktion, der indeholder visualisering af udførelse af opgave. Når du afholde [ParallelTasks] [ parallel_tasks_sample] Northwind, kan du bruge funktionen Varmekort nemt visualisere udførelse af parallelle opgaver på hver node.

![Batchen Explorer Varmekort][1]

*Batchen Explorer Varmekort, der viser en samling af fire noder med hver node udføres i øjeblikket fire opgaver*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
