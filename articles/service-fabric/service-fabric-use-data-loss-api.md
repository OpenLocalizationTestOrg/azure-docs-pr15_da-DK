<properties
   pageTitle="Sådan kalde tab af Data på tjenesten strukturerede Services | Microsoft Azure"
   description="Beskriver, hvordan du bruger datatab api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>Sådan kalde tab af Data på tjenester

>[AZURE.WARNING] Dette dokument beskriver, hvordan du kan medføre tab af data i dine tjenester og skal bruges med omtanke.

## <a name="introduction"></a>Introduktion
Du kan aktivere tab af data på en partition af din Service-strukturen tjeneste ved opkald StartPartitionDataLossAsync().  Denne api bruger tjenesten fejl indsættelse og analyse til at udføre arbejdet for at få datatab betingelser.

## <a name="using-the-fault-injection-and-analysis-service"></a>Brug af et indsættelse og Analysis Service

Tjenesten fejl indsættelse og analyse understøtter aktuelt følgende API'er i diagrammet nedenfor.  I højre side af diagrammet viser den tilsvarende PowerShell-cmdlet.  Se dokumentationen til msdn på hver API til flere oplysninger om hver enkelt.

|           C# API                    |         PowerShell-Cmdlet                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Start-ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Start-ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Start-ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Oversigt over køre en kommando

Tjenesten fejl indsættelse og analyse bruger en asynkron model, hvor du starter kommandoen med én API-, kaldes "Start" API i dette dokument og derefter kontrollerer status for denne kommando ved hjælp af en "GetProgress" API, indtil den har nået tilstanden terminal, eller indtil du annullere den.
For at starte en kommando skal ringe til "Start" API til det tilsvarende API.  Denne API returnerer hvornår tjenesten fejl indsættelse og analyse har accepteret anmodningen.  Men det betyder ikke, hvor langt en kommando er kørt, eller også selvom den har startet endnu.  For at kontrollere status for en kommando, skal du ringe til API, der svarer til "Start" API tidligere kaldet "GetProgress".  "GetProgress" API returnerer et objekt, der angiver den aktuelle status for kommandoen i egenskaben tilstand.  En kommando skal køres på ubestemt tid indtil:

1.  Det er fuldført.  Hvis du kalder "GetProgress" på den i dette tilfælde, udføres fremdrift objektets tilstand.
2.  Det registrerer en alvorlige fejl.  Hvis du kalder "GetProgress" på den i dette tilfælde, vil fremdrift objektets tilstand blive afbrudt
3.  Du annullere den gennem [CancelTestCommandAsync]  [ cancel] API eller [Stop ServiceFabricTestCommand]  [ cancelps] PowerShell-cmdlet.  Hvis du kalder "GetProgress" på den i dette tilfælde, bliver objektet fremdrift tilstanden annulleret eller ForceCancelled, afhængigt af et argument til pågældende API.  Se dokumentationen til [CancelTestCommandAsync]  [ cancel] kan finde flere oplysninger.


## <a name="details-of-running-a-command"></a>Oplysninger om at køre en kommando

For at starte en kommando, skal du ringe til API starte med de forventede argumenter.  Alle starte API'er har et Guid argument med navnet operationId.  Du skal holde styr på argumentet operationId, da den bruges til at registrere status for denne kommando.  Dette skal overføres til "GetProgress" API for at registrere status for opgaver på kommandoen.  OperationId skal være entydig.

GetProgress API bør kaldes i en løkke efter korrekt opkald API starte, før den returnerede status objektets egenskab State er afsluttet.  Alle [Fabrictransientexception's]  [ fte] Operationcanceledexceptions bør gentages.
Når kommandoen har nået tilstanden terminal (fuldført, Faulted eller annulleret), har den returnerede opgavefremdrift objektets resultatet egenskab yderligere oplysninger.  Hvis tilstanden er fuldført, skal indeholde Result.SelectedPartition.PartitionId partition-id'et, der blev valgt.  Result.Exception er null.  Hvis tilstanden er afbrudt, Result.Exception, har årsagen fejl indsættelse og Analysis Service afbrudt kommandoen.  Result.SelectedPartition.PartitionId har det partition-id, der blev valgt.  I nogle situationer, kan kommandoen ikke fortsætter langt nok til at vælge en partition.  Det er tilfældet, bliver PartitionId 0.  Hvis tilstanden er aflyst, er Result.Exception null.  Result.SelectedPartition.PartitionId får det partition-id, som blev valgt som Faulted store og små bogstaver, men hvis kommandoen ikke har fortsætter langt nok til at gøre det, vil det være 0.  Også se eksemplet nedenfor.

Nedenstående eksempelkode viser, hvordan du starter og derefter kontrollere status på en kommando til at medføre tab af data på en bestemt partition.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

Eksemplet nedenfor viser, hvordan du bruger PartitionSelector til at vælge en tilfældigt partition til en bestemt tjeneste:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Historik og afkortning af

Når en kommando har nået tilstanden terminal, forbliver metadataene i tjenesten fejl indsættelse og analyse til et bestemt tidspunkt, før den fjernes for at spare plads.  Hvis "GetProgress" kaldes ved hjælp af operationId af en kommando, når den er blevet fjernet, returneres en FabricException med en fejlkode KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
