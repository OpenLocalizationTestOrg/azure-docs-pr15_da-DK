<properties
   pageTitle="Forårsage kaos i tjenesten strukturen klynger | Microsoft Azure"
   description="Bruge et indsættelse og klynge Analysis Service API'er til at administrere kaos i klyngen."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Forårsage kontrolleret kaos i tjenesten strukturen klynger
Store distribuerede systemer ud til skyen infrastruktur er fuldstændig upålidelig. Azure Service-strukturen gør det muligt for udviklere til at skrive pålidelig services oven på en upålidelig infrastruktur. Hvis du vil skrive robust tjenester, skal udviklere kan forårsage fejl mod sådanne upålidelig infrastruktur til at teste stabilitet deres tjenester.

Fejl indsættelse og klynge Analysis Service (også kendt som et analyse tjenesten) giver udviklere muligheden for at forårsage fejl handlinger til at teste tjenester. Men målrettede simuleret fejl får du kun indtil nu. Hvis du vil tage afprøvning yderligere, kan du bruge kaos.

Kaos simulerer fortløbende, sekvensopdelt fejl (både korrekt og pludseligt) i hele klyngen over længere tid. Når du konfigurerer kaos med rente og typen fejl, kan du starte eller stoppe den gennem C# API'er eller PowerShell til at generere fejl i klyngen og din tjeneste.

Mens der kører kaos, giver forskellige hændelser, der registrere tilstanden for afkrydsningsfeltet Kør i øjeblikket. For eksempel indeholder en ExecutingFaultsEvent alle de fejl, der udføres i pågældende gentagelse. En ValidationFailedEvent indeholder oplysninger om en fejl, der blev fundet under validering. Du kan aktivere API'EN GetChaosReportAsync for at få rapport over kaos kører.

## <a name="faults-induced-in-chaos"></a>Fejl i kaos
Kaos genererer fejl på tværs af hele Service-strukturen-klynge og komprimerer fejl, som kan ses i måneder eller år til et par timer. Kombination af sekvensopdelt fejl med høj fejl rente finder hjørne sager, der er ellers mistes. Denne øvelse kaos fører til at forbedre betydeligt i kode kvaliteten af tjenesten.

Kaos Bæltematerialets fejl fra følgende kategorier:

 - Genstarte en node
 - Genstarte en udløst kode pakke
 - Fjerne en replika
 - Genstarte en replika
 - Flytte en primær replika (Konfigurerbar)
 - Flytte en sekundær replika (Konfigurerbar)

Kaos kører i flere gentagelser. Hver gentagelse består af fejl og klynge validering for den angivne periode. Du kan konfigurere den tid, der for klynge stabiliseres og til validering kan udføres. Hvis der findes en fejl i validering, kaos genererer og fortsætter en ValidationFailedEvent med UTC tidsstemplet og de manglende oplysninger.

For eksempel bør du overveje en forekomst af kaos, der er angivet til at køre for en time med op til tre samtidige fejl. Kaos Bæltematerialets tre fejl, og derefter valideret klynge tilstand. Det gentager listen over de forrige trin, indtil den er eksplicit holdt op med at gennem StopChaosAsync API eller en time overfører. Hvis klyngen bliver beskadiget i en hvilken som helst gentagelse (det vil sige, den ikke stabiliseres i gangen konfigureret), kaos genererer en ValidationFailedEvent. Denne hændelse angiver, at noget er gået galt og muligvis yderligere undersøgelse.

I den aktuelle form Bæltematerialets kaos kun sikre fejl. Det betyder, at i fravær af eksterne fejl, en quorum tab eller tab af data aldrig opstår.

## <a name="important-configuration-options"></a>Vigtige konfigurationsindstillinger
 - **TimeToRun**: samlede tid, der kører kaos, før den er færdig med succes. Du kan stoppe kaos, før den er kørt for perioden TimeToRun gennem StopChaos API.
 - **MaxClusterStabilizationTimeout**: den maksimale mængde af tid at vente klynge for at blive sund før der søges på den igen. Denne Vent er at reducere belastningen på klyngen, mens det gendanne. De Kontroller, der er udført er:
    - Hvis klynge tilstanden er OK
    - Hvis tjenestetilstanden er OK
    - Hvis destinationsreplikaen angiver størrelsen opnås for den tjeneste partition
    - Der findes ingen InBuild replikaer
 - **MaxConcurrentFaults**: det maksimale antal samtidige fejl, der er givet i hver gentagelse. På højere det nummer, jo mere tilstanden kaos er. Dette giver mere komplekse failover og overgang kombinationer. Kaos garanterer, at i fravær af eksterne fejl, der er ingen quorum tab eller tab af data, uanset hvor stor en værdi er i denne konfiguration.
 - **EnableMoveReplicaFaults**: Aktiverer eller deaktiverer de fejl, som medfører de primære eller sekundære replikaer til at flytte. Disse fejl er deaktiveret som standard.
 - **WaitTimeBetweenIterations**: tidsrum, der skal ventes mellem gentagelser, det vil sige, når du har en runde af fejl og tilsvarende validering.
 - **WaitTimeBetweenFaults**: tidsrum, der skal ventes mellem to efterfølgende fejl i en gentagelse.

## <a name="how-to-run-chaos"></a>Sådan køres kaos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
