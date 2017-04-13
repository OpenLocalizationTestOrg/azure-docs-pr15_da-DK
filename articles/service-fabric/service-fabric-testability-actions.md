<properties
   pageTitle="Mulighed for at teste handling | Microsoft Azure"
   description="I denne artikel omhandler de mulighed for at teste handlinger, der findes i Microsoft Azure Service struktur."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Mulighed for at teste handlinger
For at simulere en upålidelig infrastruktur, indeholder Azure Service strukturen du udvikler med måder at simulere forskellige reale fejl og tilstandsovergange. Disse vises som mulighed for at teste handlinger. Handlingerne er de laveste-niveau-API'er, der kan medføre en bestemt fejl indsættelse, ændring af tilstanden eller validering. Ved at kombinere disse handlinger, kan du skrive omfattende test scenarier for dine tjenester.

Tjenesten strukturen indeholder nogle almindelige scenarier, test bestående af disse handlinger. Vi anbefaler, at du udnytte disse indbyggede scenarier, som er omhyggeligt udvalgt til at teste almindelige tilstandsovergange og manglende sager. Handlinger kan dog bruges til at oprette brugerdefinerede test scenarier, når du vil tilføje dækning for scenarier, der ikke er omfattet af de indbyggede scenarier endnu, eller som brugerdefineret skræddersyet til dit program.

C#-installationer handlingerne, der findes i samlingen System.Fabric.dll. System-strukturen PowerShell-modulet findes i samlingen Microsoft.ServiceFabric.Powershell.dll. ServiceFabric PowerShell-modulet er installeret for at tillade for nemmere at bruge som en del af runtime-installation.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Korrekt kontra pludseligt fejl handlinger
Mulighed for at teste handlinger er klassificeret i to overordnede filsæt:

* Pludseligt fejl: disse fejl simulere fejl som maskine genstarter og Behandl går ned. I så fald antal mislykkede forsøg stopper konteksten for udførelse af processen pludselig. Det betyder, at ingen oprydning af tilstanden kan køre, før programmet startes igen.

* Korrekt fejl: disse fejl simulere korrekt handlinger som replika flytter og tab udløses af belastning. I så fald tjenesten får en meddelelse om afslutning og kan rydde op i tilstanden, før du afslutter.

Køre service og arbejdsrelaterede arbejdsbelastningen til validering af bedre kvalitet, mens afholde forskellige korrekt og pludseligt fejl. Pludseligt fejl øvelse scenarier, hvor tjenesten processen pludselig afsluttet midt nogle arbejdsproces. Dette tester gendannelse stien, når tjenesten replikaen genoprettes ved Service struktur. Det gør det nemmere teste data konsistens og om-tjenestetilstand vedligeholdes korrekt efter mislykkede forsøg. Andet sæt antal mislykkede forsøg (korrekt fejl) test, tjenesten korrekt reagerer på replikaer flyttes rundt om ved Service struktur. Dette tester håndtering af annullering af i metoden RunAsync. Tjenesten skal kontrollere, om annulleringen tokenet der, skal du gemme korrekt tilstanden, og Afslut metoden RunAsync.

## <a name="testability-actions-list"></a>Mulighed for at teste handlingsliste

| Handling | Beskrivelse | Administrerede API | PowerShell-cmdlet | Korrekt/pludseligt fejl |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Fjerner alle test tilstanden fra klynge i tilfælde af en forkert lukning af test-driver. | CleanTestStateAsync | Fjern ServiceFabricTestState | Ikke relevant |
| InvokeDataLoss | Bæltematerialets tab af data i en tjeneste partition. | InvokeDataLossAsync | Kalde ServiceFabricPartitionDataLoss | Korrekt |
| InvokeQuorumLoss | Indsætter en given med høj sikkerhed tjeneste partition til quorum tab. | InvokeQuorumLossAsync | Kalde ServiceFabricQuorumLoss | Korrekt |
| Flytte primær | Flytter den angivne primære kopi af en med høj sikkerhed tjeneste til den angivne klyngenode. | MovePrimaryAsync | Flyt ServiceFabricPrimaryReplica | Korrekt |
| Flyt sekundær | Flytter den aktuelle sekundær kopi af en med høj sikkerhed tjeneste til en anden klyngenode. | MoveSecondaryAsync | Flyt ServiceFabricSecondaryReplica | Korrekt |
| RemoveReplica | Simulerer en replika fejl ved at fjerne en replika fra en klynge. Dette lukkes replikaen og overgang den rolle 'Ingen', fjerne alle dens tilstand fra klyngen. | RemoveReplicaAsync | Fjern ServiceFabricReplica | Korrekt |
| RestartDeployedCodePackage | Simulerer en kode pakken proces fejl ved at genstarte en kode-pakke, der er installeret på en node i en klynge. Dette afbryder kode pakken processen, som genstartes alle de bruger tjenesten Kopier hostet i processen. | RestartDeployedCodePackageAsync | Genstart ServiceFabricDeployedCodePackage | Pludseligt |
| RestartNode | Simulerer en tjeneste-strukturen klynge node fejl ved at genstarte en node. | RestartNodeAsync | Genstart ServiceFabricNode | Pludseligt |
| RestartPartition | Simulerer et datacenter sort til eller fra eller klynge sort til eller fra scenarie ved at genstarte nogle eller alle kopier af en partition. | RestartPartitionAsync | Genstart ServiceFabricPartition | Korrekt |
| RestartReplica | Simulerer en replika fejl ved at genstarte en permanente replika i en klynge, lukke replikaen og derefter åbne den igen. | RestartReplicaAsync | Genstart ServiceFabricReplica | Korrekt |
| StartNode | Starter en node i en klynge, der allerede er stoppet. | StartNodeAsync | Start-ServiceFabricNode | Ikke relevant |
| StopNode | Simulerer en node fejl ved at stoppe en node i en klynge. Noden forbliver ned, indtil StartNode kaldes. | StopNodeAsync | Stop ServiceFabricNode | Pludseligt |
| ValidateApplication | Validerer tilgængelighed og tilstand for alle Service strukturerede services i et program, som regel efter afholde nogle fejl i systemet. | ValidateApplicationAsync | Test-ServiceFabricApplication | Ikke relevant |
| ValidateService | Valideret tilgængelighed og tilstand for en tjeneste-strukturen tjeneste, som regel efter afholde nogle fejl i systemet. | ValidateServiceAsync | Test-ServiceFabricService | Ikke relevant |

## <a name="running-a-testability-action-using-powershell"></a>Køre en mulighed for at teste handling ved hjælp af PowerShell

Dette selvstudium viser, hvordan du kører en mulighed for at teste handling ved hjælp af PowerShell. Du lære, hvordan du kører en mulighed for at teste handling i forhold til en lokal klynge (én boks) eller en Azure klynge. Microsoft.Fabric.Powershell.dll – Service-strukturen PowerShell-modulet – installeres automatisk, når du installerer Microsoft Service-strukturen MSI. Modulet indlæses automatisk, når du åbner en PowerShell-prompt.

Selvstudium segmenter:

- [Køre en handling mod en én boks klynge](#run-an-action-against-a-one-box-cluster)
- [Køre en handling mod en Azure klynge](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Køre en handling mod en én boks klynge

For at køre handlingen mulighed for at teste mod en lokal klynge skal først oprette forbindelse til klyngen og Åbn PowerShell-prompt i administratortilstand. Lad os se på handlingen **Genstart ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Her er handlingen, **Genstart ServiceFabricNode** køres på en node kaldet "Node1". Tilstanden fuldførelse angiver, at det ikke skal kontrollere om handlingen genstart-node faktisk lykkedes. Angiver tilstanden fuldførelse, som det "Godkend" medfører, at den for at kontrollere, om handlingen genstart faktisk lykkedes. I stedet for at angive direkte noden med navnet, kan du angive den via en Partitionsnøgle og typen replika, på følgende måde:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Genstart ServiceFabricNode** skal bruges til at genstarte en tjeneste-strukturen node i en klynge. Dette standser Fabric.exe processen, som genstartes alle de system tjeneste og bruger tjenesten replikaer hostes på denne node. Brug af denne API til at teste din tjeneste hjælper med at afdække fejl langs failover gendannelse stier. Det hjælper med at simulere node fejl i klyngen.

Følgende skærmbillede viser kommandoen **Genstart ServiceFabricNode** mulighed for at teste i aktion.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Output fra den første **Get-ServiceFabricNode** (en cmdlet fra Service-strukturen PowerShell-modulet) viser, at den lokale klynge indeholder fem noder: Node.1 til Node.5. Når handlingen mulighed for at teste (cmdlet) **Genstart ServiceFabricNode** er udført på den node, med navnet Node.4, får vi, at den node oppetid er blevet nulstillet.

### <a name="run-an-action-against-an-azure-cluster"></a>Køre en handling mod en Azure klynge

Køre en mulighed for at teste handling (ved hjælp af PowerShell) mod en Azure klynge ligner kører handlingen mod en lokal klynge. Den eneste forskel er, før du kan køre handlingen, i stedet for at oprette forbindelse til den lokale klynge, skal du oprette forbindelse til Azure klynge først.

## <a name="running-a-testability-action-using-c35"></a>Køre en mulighed for at teste handling, hvis du bruger C & #35

Hvis du vil køre en mulighed for at teste handling ved hjælp af C#, skal først du oprette forbindelse til klyngen ved hjælp af FabricClient. Derefter opnå de parametre, der er behov for at køre handlingen. Forskellige parametre, som kan bruges til at køre den samme handling.
Man kigger på handlingen RestartServiceFabricNode, er en metode til at køre den ved hjælp af node oplysningerne (nodenavn og node forekomst-ID'ET) i klyngen.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parameter forklaring:

- **CompleteMode** angiver, at tilstanden, der ikke skal kontrollere om handlingen genstart faktisk lykkedes. Angiver tilstanden fuldførelse, som det "Godkend" medfører, at den for at kontrollere, om handlingen genstart faktisk lykkedes.  
- **OperationTimeout** angiver mængden af tid for handlingen afsluttes, før en TimeoutException undtagelse er opstået.
- **CancellationToken** gør det muligt for en ventende opkald skal annulleres.

I stedet for at angive direkte noden med navnet, kan du angive den via en Partitionsnøgle og typen replika.

Yderligere oplysninger finder du [PartitionSelector og ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector og ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector er et hjælpeprogram, vises i mulighed for at teste og bruges til at vælge en bestemt partition, som skal udføres mulighed for at teste handlinger. Det kan bruges til at vælge en bestemt partition, hvis partition ID kendes på forhånd. Eller du kan angive tasten partition og handlingen vil løse partition ID internt. Du har også mulighed for at vælge en tilfældigt partition.

Opret PartitionSelector objektet for at bruge dette hjælpeprogram, og vælg partitionen ved hjælp af metoderne Vælg *. Overføre derefter i objektet PartitionSelector til API, der kræver det. Hvis indstillingen ingen er markeret, som standard en tilfældigt partition.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector er et hjælpeprogram, vises i mulighed for at teste og bruges til at vælge en replika, som skal udføres mulighed for at teste handlinger. Det kan bruges til at vælge en bestemt replika, hvis replika-ID, der kendes på forhånd. Desuden har du mulighed for at vælge en primær replika eller et tilfældigt sekundært. ReplicaSelector stammer fra PartitionSelector, så du har brug at markere både replikaen og den partition, hvor du vil udføre handlingen mulighed for at teste.

Hvis du vil bruge dette hjælpeprogram, oprette et ReplicaSelector objekt og angive den måde, du vil markere replikaen og partitionen. Du kan derefter overføre den til API, der kræver det. Hvis indstillingen ingen er markeret, som standard en tilfældigt replika og tilfældige partition.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Næste trin

- [Mulighed for at teste scenarier](service-fabric-testability-scenarios.md)
- Hvordan du kan teste din tjeneste
   - [Simulere fejl under service arbejdsmængder](service-fabric-testability-workload-tests.md)
   - [Mislykkede forsøg på at tjeneste kommunikation](service-fabric-testability-scenarios-service-communication.md)
