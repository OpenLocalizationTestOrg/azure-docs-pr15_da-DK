<properties
   pageTitle="Foretage fejlfinding af med system tilstandsrapporter | Microsoft Azure"
   description="I denne artikel beskrives sendes af Azure Service strukturen komponenter og deres brugen til fejlfinding i forbindelse med klynge eller programproblemer tilstandsrapporter."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="use-system-health-reports-to-troubleshoot"></a>Brug system tilstandsrapporter at foretage fejlfinding af

Azure Service-strukturen komponenter rapport af feltet på alle objekter i klyngen. Den [tilstand gemme](service-fabric-health-introduction.md#health-store) opretter og sletter objekter, der er baseret på system-rapporterne. Det også organiserer dem i et hierarki, der registrerer enhed interaktioner.

> [AZURE.NOTE] For at forstå sundhed-relaterede begreber, skal du få mere at vide på [Service-strukturen sundhedstilstandsmodel](service-fabric-health-introduction.md).

System tilstandsrapporter giver indsigt i klynge og programmets funktionalitet og flag problemerne via tilstand. Til programmer og tjenester Kontrollér system tilstandsrapporter, objekter er implementeret og er opfører korrekt fra perspektivet, Service-strukturen. Rapporterne, der indeholder ikke en hvilken som helst Tilstandsovervågning af forretningslogik til tjenesten eller identifikation af hang processer. Brugertjenester kan forbedre sundhed data med specifikke oplysninger om deres logik.

> [AZURE.NOTE] Watchdogs tilstandsrapporter er synlige, kun *efter* systemkomponenterne opretter et objekt. Når en enhed slettes, sletter sundhed store automatisk alle tilstandsrapporter, der er knyttet til den. Den samme er sand, når der oprettes en ny forekomst af objektet (for eksempel en ny forekomst af tjenesten replika der oprettes). Alle rapporter, der er knyttet til den gamle forekomst er slettet og ryddet op fra store.

Systemkomponenten rapporter er identificeret med den kilde, som starter med det "**System.**" præfiks. Watchdogs kan ikke bruge det samme præfiks for deres kilder rapporter med ugyldige parametre er blevet afvist.
Lad os se på nogle Systemrapporter for at forstå, hvad udløser dem, og hvordan du kan løse de mulige problemer, de repræsenterer.

> [AZURE.NOTE] Tjenesten strukturen fortsætter med at tilføje rapporter på betingelser af interesse, der forbedrer indsigt i hvad sker der i klynge og programmet.

## <a name="cluster-system-health-reports"></a>Klynge system tilstandsrapporter
Objektet klynge sundhed oprettes automatisk i store tilstand. Hvis alt fungerer korrekt, kan den ikke har en system-rapport.

### <a name="neighborhood-loss"></a>Computere tab
**System.Federation** rapporterer en fejl, når programmet registrerer et computere tab. Rapporten er fra individuelle noder, og node-ID'ET er medtaget i egenskabsnavn. Hvis en computere går tabt i hele Service-strukturen ring, kan du typisk forvente to hændelser (begge sider af rapporten mellemrum). Hvis flere omgivelser går tabt, er der flere begivenheder.

Rapporten angiver den globale bortlease timeout som tid til live. Rapporten sendes igen hver halvdel af TTL-varighed til, så længe betingelsen er aktiv. Hændelsen fjernes automatisk, når det udløber. Fjern når udløbet funktionsmåde sikrer, at rapporten er ryddet op fra sundhed store korrekt, selvom noden rapportering er nede.

- **SourceId**: System.Federation
- **Egenskaben**: starter med **computere** og indeholder nodeoplysninger
- **Næste trin**: undersøge, hvorfor Bynavn går tabt (for eksempel Kontroller kommunikationen mellem klyngenoder).

## <a name="node-system-health-reports"></a>Node system tilstandsrapporter
**System.FM**, som repræsenterer tjenesten Failover Manager, er det nøglecenter, der administrerer oplysninger om klyngenoder. Hver node skal have en rapport fra System.FM, der viser tilstanden. Node enheder fjernes, når tilstanden node fjernes (se [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Node op/ned
System.FM rapporter som OK, når noden sammenkæder ring (det er oppe at køre). Den rapporterer en fejl, når noden afviger ring (det er nede, enten til opgradering eller blot fordi det mislykkes). Sundhed hierarkiet oprettet af sundhed butikken kræver handling på udløst objekter i korrelations med System.FM node rapporter. Det tager noden en virtuelle overordnede for alle de installerede enheder. De installerede objekter på denne node er fremvises forespørgsler, hvis noden rapporteres som op ved System.FM med den samme forekomst af som den forekomst, der er knyttet til enheder. Når System.FM rapporter, noden er nede eller genstartes (en ny forekomst), rydder sundhed store automatisk op i de installerede enheder, der kan findes kun på noden ned eller til den forrige forekomst af noden.

- **SourceId**: System.FM
- **Egenskaben**: tilstand
- **Næste trin**: Hvis noden ned til en opgradering, den skal komme tilbage når den er blevet opgraderet. I dette tilfælde skal tilstanden skiftes tilbage til OK. Hvis noden ikke kommer tilbage eller det mislykkes, skal problemet yderligere undersøgelse.

I følgende eksempel vises hændelsen System.FM med en tilstanden for OK til node:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Udløb af certifikat
**System.FabricNode** rapporterer en advarsel, når certifikater, der bruges af noden er i nærheden af udløb. Der er tre certifikater per node: **Certificate_cluster**, **Certificate_server**og **Certificate_default_client**. Når udløbet er mindst to uger, er tilstanden rapport OK. Når der er udløbet inden for to uger, er rapporttypen en advarsel. TTL af disse hændelser er uendelig, og de fjernes, når en node forlader klyngen.

- **SourceId**: System.FabricNode
- **Egenskaben**: starter med **certifikatet** og indeholder flere oplysninger om certifikattypen
- **Næste trin**: opdatere certifikater, hvis de er i nærheden af udløb.

### <a name="load-capacity-violation"></a>Indlæse kapacitet overtrædelse
Den Service-strukturen justering af belastning rapporterer en advarsel, hvis der registreres en node kapacitet overtrædelse.

 - **SourceId**: System.PLB
 - **Egenskaben**: starter med **kapacitet**
 - **Næste trin**: kontrollere leveres målepunkter og få vist den aktuelle kapacitet under noden.

## <a name="application-system-health-reports"></a>Programmet system tilstandsrapporter
**System.CM**, som repræsenterer tjenesten klynge Manager, er det nøglecenter, der administrerer oplysninger om et program.

### <a name="state"></a>Stat
System.CM rapporter som OK når programmet har oprettes eller opdateres. Den informerer sundhed store når programmet er blevet slettet, så den kan fjernes fra store.

- **SourceId**: System.CM
- **Egenskaben**: tilstand
- **Næste trin**: Hvis programmet er blevet oprettet, skal den indeholde klynge Manager sundhed rapporten. Ellers kan kontrollere status for programmet ved at udstede en forespørgsel (for eksempel PowerShell-cmdlet'en * *Get-ServiceFabricApplication ApplicationName *applicationName***).

I følgende eksempel viser hændelsen tilstand på den **strukturen: / WordCount** program:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Tjenesten system tilstandsrapporter
**System.FM**, som repræsenterer tjenesten Failover Manager, er det nøglecenter, der administrerer oplysninger om tjenester.

### <a name="state"></a>Stat
System.FM rapporter som OK når tjenesten er blevet oprettet. Den sletter objektet fra sundhed store, når tjenesten er blevet slettet.

- **SourceId**: System.FM
- **Egenskaben**: tilstand

I følgende eksempel viser hændelsen tilstand på tjenesten **strukturen: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Ikke-placerede replikaer overtrædelse
**System.PLB** rapporterer en advarsel, hvis det ikke kan finde en placering for en eller flere kopier af tjenesten. Rapporten fjernes, når det udløber.

- **SourceId**: System.FM
- **Egenskaben**: tilstand
- **Næste trin**: kontrollere service begrænsninger og den aktuelle tilstand for placeringen.

I følgende eksempel viser en overtrædelse for en tjeneste, der er konfigureret med 7 target replikaer i en klynge med 5 noder:

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Partition system tilstandsrapporter
**System.FM**, som repræsenterer tjenesten Failover Manager, er det nøglecenter, der administrerer oplysninger om tjenesten partitioner.

### <a name="state"></a>Stat
System.FM rapporter som OK Når partitionen er blevet oprettet og er i orden. Den sletter objektet fra sundhed store, når partitionen slettes.

Hvis partitionen er under minimum replika antallet, rapporterer en fejl. Hvis partitionen er ikke under mindste replika antallet, men det er under target replika antallet, rapporterer en advarsel. Hvis partitionen er i quorum tab, rapporterer System.FM en fejl.

Andre vigtige begivenheder omfatter en advarsel, når konfigureres igen tager længere tid end forventet, og Opret tager længere tid end forventet. Den forventede tidspunkter for build og konfigureres igen kan konfigureres baseret på tjenesten scenarier. Hvis en tjeneste har en terabyte område, såsom SQL-Database tager Opret eksempelvis længere tid end til en tjeneste med en lille mængde tilstand.

- **SourceId**: System.FM
- **Egenskaben**: tilstand
- **Næste trin**: Hvis tilstanden ikke er OK, det er muligt, at nogle replikaer, der ikke er oprettet, åbnes, eller forfremmet til primær eller sekundær korrekt. I mange tilfælde er den egentlige årsag en tjeneste fejl i implementeringen Åbn eller Skift rolle.

I følgende eksempel viser en sund partition:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

I følgende eksempel viser tilstanden for en partition, der er under target replika Tæl. Det næste trin er at få partition beskrivelsen, som viser, hvordan den er konfigureret: **MinReplicaSetSize** er to og **TargetReplicaSetSize** er syv. Skal du vælge antallet af knuder i klyngen: fem. Så i dette tilfælde to replikaer kan ikke placeres.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Replika begrænsningsfejl
**System.PLB** rapporterer en advarsel, hvis det registrerer en replika begrænsningsfejl og kan ikke placere kopier af partitionen.

- **SourceId**: System.PLB
- **Egenskaben**: starter med **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Replika system tilstandsrapporter
**System.RA**, som repræsenterer komponenten konfigureres igen agent, er nøglecenter til tilstanden replika.

### <a name="state"></a>Stat
**System.RA** rapporter som OK når replikaen er blevet oprettet.

- **SourceId**: System.RA
- **Egenskaben**: tilstand

I følgende eksempel viser en sund replika:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Replika Åbn status
Beskrivelse af denne tilstandsrapport indeholder starttidspunktet (Coordinated Universal Time), når API opkaldet blev aktiveret.

**System.RA** rapporterer en advarsel, hvis den åbne replika tager længere tid end konfigurerede perioden (standard: 30 minutter). Hvis API virkninger tjenesten kører, udstedes rapporten meget hurtigere (en konfigurerbar interval, med en standardværdi på 30 sekunder). Den tid, målt indeholder den tid, det tager for Microsoft Åbn og Åbn tjeneste. Egenskaben ændres til OK, hvis Åbn er fuldført.

- **SourceId**: System.RA
- **Egenskab**: **ReplicaOpenStatus**
- **Næste trin**: Hvis tilstanden ikke er OK, undersøge hvorfor replikaen Åbn tager længere tid end forventet.

### <a name="slow-service-api-call"></a>Langsom service API opkald
**System.RAP** og **System.Replicator** rapport en advarsel, hvis et opkald til tjenestekode bruger tager længere tid end den tid, der er konfigureret. Advarslen ryddes, når opkaldet er fuldført.

- **SourceId**: System.RAP eller System.Replicator
- **Egenskaben**: navnet på langsom API. Beskrivelsen af finde flere oplysninger om den tid, API er blevet godkendt.
- **Næste trin**: undersøge, hvorfor opkaldet tager længere tid end forventet.

I følgende eksempel viser en partition i quorum tab og undersøgelse trinnene udført for at finde ud af, hvorfor. En af replikaerne har en advarsel tilstanden, så du får sin tilstand. Det viser, at servicehandlingen tager længere tid end forventet, en begivenhed, der rapporteres af System.RAP. Når disse oplysninger er modtaget, er næste trin at se på tjenestekode og undersøge der. I dette tilfælde medfører **RunAsync** implementeringen af tjenesten med høj sikkerhed en ikke-afviklet undtagelse. Replikaerne genbrug, så du ikke kan få vist alle replikaer i tilstanden advarsel. Du kan prøve igen få tilstanden og søge efter en hvilken som helst forskelle i replika-ID. I visse tilfælde kan gentagelserne give dig spor.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Når du starter programmet fejlbehæftede under fejlfindingen, Vis vinduerne diagnosticering begivenheder den udløste fra RunAsync undtagelse:

![Visual Studio 2015 diagnosticering hændelser: RunAsync fejl i strukturen: / HelloWorldStatefulApplication.][1]

Visual Studio 2015 diagnosticering hændelser: RunAsync fejl i **strukturen: / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Replikering kø fuld
**System.Replicator** rapporterer en advarsel, hvis gentagelse køen er fuld. På primært sker det som regel, fordi en eller flere sekundære replikaer er længe om at bekræfte handlinger. På sekundært sker det som regel når tjenesten er længe om at anvende handlingerne. Advarslen ryddes, når køen er ikke længere fuld.

- **SourceId**: System.Replicator
- **Egenskab**: **PrimaryReplicationQueueStatus** eller **SecondaryReplicationQueueStatus**, afhængigt af rollen replika

### <a name="slow-naming-operations"></a>Langsom Naming handlinger

**System.NamingService** rapporter tilstand på den primære replika, når handlingen Naming tager længere tid end acceptable. Eksempler på Naming handlinger er [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) eller [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Flere metoder kan findes under FabricClient, for eksempel under [service management metoder](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) eller [egenskab management metoder](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] Tjenesten Naming løser tjenestenavne til en placering i klyngen og brugere mulighed for at administrere tjenestenavne og egenskaber. Det er en tjeneste-strukturen opdelt bevaret tjeneste. En af partitionerne repræsenterer nøglecenter ejer, som indeholder metadata om alle navne Service-strukturen og -tjenester. Tjenesten strukturen navnene er tilknyttet forskellige partitioner, kaldet navn ejer partitioner, så tjenesten kan udvides. Læs mere om [Naming service](service-fabric-architecture.md).

Når handlingen Naming tager længere tid end forventet, er handlingen markeret med en advarsel rapport på den *primære kopi af Naming service partition, fungerer operationen*. Hvis handlingen er fuldført, slettes advarslen. Hvis handlingen fuldført med en fejl, indeholder sundhed rapporten oplysninger om fejlen.

- **SourceId**: System.NamingService
- **Egenskaben**: starter med præfiks **Duration_** og identificerer handlingen langsom og navnet på tjenesten strukturen som handlingen er anvendt. Eksempelvis hvis oprette service på navn strukturen: / MyApp/MyService tager for lang tid, egenskaben er Duration_AOCreateService.fabric:/MyApp/MyService. KV peger på rolle Naming partition til dette navn og en handling.
- **Næste trin**: Kontrollér hvorfor Naming handlingen mislykkes. Hver handling kan have forskellige årsager. For eksempel slette tjenesten kan være fast på en node, fordi værten programmet bevarer ned på en node på grund af en bruger fejl i tjenesten koden.

I følgende eksempel viser handlingen Opret tjeneste. Handlingen tog længere end den konfigurerede varighed. KV nye forsøg og sender arbejde til Nej. IKKE fuldført den seneste handling med Timeout. I dette tilfælde er den samme replika primær for både kV og ingen roller.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication system tilstandsrapporter
**System.Hosting** er nøglecenter på udløst enheder.

### <a name="activation"></a>Aktivering
System.Hosting rapporter som OK når et program er blevet aktiveret på noden. Ellers skal rapporteres der en fejl.

- **SourceId**: System.Hosting
- **Egenskaben**: aktivering, herunder udrulning af versionen
- **Næste trin**: Hvis programmet er beskadiget, kan du undersøge hvorfor aktiveringen mislykkedes.

I følgende eksempel viser aktiveringen:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Download
**System.Hosting** rapporterer en fejl, hvis programmet pakke overførslen mislykkes.

- **SourceId**: System.Hosting
- **Egenskab**: * *hente:*RolloutVersion***
- **Næste trin**: undersøge, hvorfor overførslen mislykkedes på noden.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage system tilstandsrapporter
**System.Hosting** er nøglecenter på udløst enheder.

### <a name="service-package-activation"></a>Tjenesten pakke aktivering
System.Hosting rapporter som OK, hvis tjenesten pakke aktiveringen på noden er gennemført. Ellers skal rapporteres der en fejl.

- **SourceId**: System.Hosting
- **Egenskaben**: aktivering
- **Næste trin**: undersøge, hvorfor aktiveringen mislykkedes.

### <a name="code-package-activation"></a>Kode pakken aktivering
**System.Hosting** rapporter som OK for hver kode pakke Hvis aktiveringen er gået igennem. Hvis aktiveringen mislykkedes, rapporterer en advarsel, som er konfigureret. Hvis **CodePackage** ikke kan aktivere eller afsluttes med en fejl, der er større end det konfigurerede **CodePackageHealthErrorThreshold**, vært rapporterer en fejl. Hvis en servicepakke indeholder flere kode-pakker, oprettes en aktivering rapport for hver enkelt.

- **SourceId**: System.Hosting
- **Egenskaben**: bruger præfikset **CodePackageActivation** og indeholder navnet på den kode pakke og indgangspunkt som * *CodePackageActivation:*CodePackageName*:*SetupEntryPoint/indgangspunkt* ** (for eksempel **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Tjenesten type registrering
**System.Hosting** rapporter som OK, hvis typen er blevet registreret. Den rapporterer en fejl, hvis registreringen ikke blev gjort tidspunkt (som konfigureres ved hjælp af **ServiceTypeRegistrationTimeout**). Hvis typen er ikke-registrerede fra noden, er dette da tiden er blevet lukket. En advarsel vært rapporter.

- **SourceId**: System.Hosting
- **Egenskaben**: bruger præfikset **ServiceTypeRegistration** og indeholder navnet på tjenesten (for eksempel **ServiceTypeRegistration:FileStoreServiceType**)

I følgende eksempel viser en sund udløst servicepakke:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Download
**System.Hosting** rapporterer en fejl, hvis tjenesten pakke overførslen mislykkes.

- **SourceId**: System.Hosting
- **Egenskab**: * *hente:*RolloutVersion***
- **Næste trin**: undersøge, hvorfor overførslen mislykkedes på noden.

### <a name="upgrade-validation"></a>Validering af opgradering
**System.Hosting** rapporterer en fejl, hvis under opgraderingen ikke lykkes, eller Hvis opgraderingen mislykkes i noden.

- **SourceId**: System.Hosting
- **Egenskaben**: bruger præfikset **FabricUpgradeValidation** og indeholder den opgraderede version
- **Beskrivelse**: peger på fejlen

## <a name="next-steps"></a>Næste trin
[Vis Service-strukturen tilstandsrapporter](service-fabric-view-entities-aggregated-health.md)

[Sådan rapport, og Kontrollér tjenestetilstand](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Overvåge og diagnosticere services lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Tjenesten strukturen programmet opgradering](service-fabric-application-upgrade.md)
