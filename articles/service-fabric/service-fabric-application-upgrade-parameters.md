
<properties
   pageTitle="Opgradering af programmet: opgradere parametre | Microsoft Azure"
   description="I denne artikel beskrives parametre, der er relateret til opgradering af en tjeneste-strukturen applikation, inklusive tilstandskontrol til at udføre og politikker for at fortryde automatisk opgraderingen."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# <a name="application-upgrade-parameters"></a>Programmet opgradering parametre

I denne artikel beskrives de forskellige parametre, der gælder under opgraderingen af et Azure Service strukturen program. Parametrene omfatter navn og version af programmet. De er knapperne, der bestemmer timeout og tilstandskontrol, der er anvendt under opgraderingen, og de angiver de politikker, der skal anvendes, når en opgradering ikke.


<br>

| Parameter | Beskrivelse |
| --- | --- |
| ApplicationName | Navnet på det program, der er ved at blive opgraderet. Eksempler: strukturen: / VisualObjects, strukturen: / ClusterMonitor  |
| TargetApplicationTypeVersion | Skriv versionen af programmet, som de opgradering mål. |
| FailureAction | Den handling, der er taget som Service-strukturen, når opgraderingen mislykkes. Programmet kan rulles tilbage til den før opdatering version (rollback), eller opgraderingen kan den standses på det aktuelle opgradering domæne. I så fald ændres også tilstanden opgradering til manuel. Tilladte værdier er Rollback og manuel. |
| HealthCheckWaitDurationSec | Den tid, der skal ventes (i sekunder), når opgraderingen er fuldført på opgradering domænet før Service-strukturen evaluerer tilstanden af programmet. Denne varighed kan også betragtes som den tid, skal du køre et program, før den kan anses sund. Hvis tilstandskontrol overfører, opgraderingsprocessen skrider frem til det næste opgradering domæne.  Hvis sundhed kontrollen mislykkes, venter tjenesten strukturen for et interval (UpgradeHealthCheckInterval) før du prøver igen på tilstandskontrol igen, indtil HealthCheckRetryTimeout er nået. Standard- og anbefalede værdi er 0 sekunder. |
| HealthCheckRetryTimeoutSec | Varighed (i sekunder), Service-strukturen fortsætter med at udføre sundhed evaluering før erklære opgraderingen, som mislykkedes. Standardværdien er 600 sekunder. Denne varighed starter, når HealthCheckWaitDuration er nået. I denne HealthCheckRetryTimeout kan Service-strukturen udføre flere tilstandskontrol af programmet tilstand. Standardværdien er 10 minutter og skal tilpasses korrekt for dit program. |
| HealthCheckStableDurationSec | Varighed (i sekunder) til at kontrollere, at programmet er stabil før du flytter til det næste opgradering domæne eller opgraderingen er fuldført. Denne Vent varighed bruges til at forhindre øget ændringer af sundhed højre efter tilstandskontrol er udført. Standardværdien er 120 sekunder og skal tilpasses korrekt for dit program. |
| UpgradeDomainTimeoutSec | Maksimale tid (i sekunder) til opgradering af et enkelt opgradering domæne. Hvis denne timeout er nået, opgraderingen stopper og skrider frem baseret på indstillingen for UpgradeFailureAction. Standardværdien er aldrig (uendelig) og skal tilpasses korrekt for dit program. |
| UpgradeTimeout | Timeout (i sekunder), der gælder for hele opgraderingen. Hvis denne timeout er nået, udløses opgradering stopper og UpgradeFailureAction. Standardværdien er aldrig (uendelig) og skal tilpasses korrekt for dit program. |
| UpgradeHealthCheckInterval | Den hyppighed, tilstand er markeret. Denne parameter er angivet i sektionen ClusterManager _klynge_ _manifestet_og er ikke angivet som en del af opgradering cmdlet. Standardværdien er 60 sekunder.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Tjenesten sundhed evaluering under opgradering af programmet

<br>
Kriteriet sundhed evaluering er valgfrit. Hvis kriteriet sundhed evaluering ikke er angivet, når en opgradering starter, bruger tjenesten strukturen de programmet sundhed politikker, der er angivet i ApplicationManifest.xml af forekomsten af programmet.


<br>

| Parameter | Beskrivelse |
| --- | --- |
| ConsiderWarningAsError | Standardværdien er falsk. Behandl advarsel sundhed begivenheder til programmet som fejl ved evaluering af tilstanden for programmet under opgraderingen. Som standard evalueres Service-strukturen ikke advarsel sundhed hændelser for at være fejl (fejl), så opgraderingen kan fortsætte, selvom der er advarsel begivenheder.   |
| MaxPercentUnhealthyDeployedApplications | Standard og anbefalede værdi er 0. Angiv det maksimale antal installerede programmer (se [sundhed afsnit](service-fabric-health-introduction.md)), der kan være beskadiget, før programmet betragtes som beskadiget og mislykkes opgraderingen. Denne parameter angiver programmet tilstanden på noden og hjælper med at registrere problemer under opgraderingen. Typisk få kopier af programmet Indlæs afstemmes til den anden node, hvilket betyder, at vises sund, således at opgraderingen kan fortsætte. Ved at angive en faste MaxPercentUnhealthyDeployedApplications-tilstand, kan tjenesten strukturen registrere et problem med programpakken hurtigt og nemmere at oprette en hurtig opgradering fejl Hjælp. |
| MaxPercentUnhealthyServices | Standard og anbefalede værdi er 0. Angiv det maksimale antal tjenester i den forekomst af programmet på computeren, kan være beskadiget, før programmet betragtes som beskadiget og mislykkes opgraderingen. |
| MaxPercentUnhealthyPartitionsPerService | Standard og anbefalede værdi er 0. Angiv det maksimale antal partitioner i en tjeneste, der kan være beskadiget, før tjenesten betragtes som beskadiget. |
| MaxPercentUnhealthyReplicasPerPartition | Standard og anbefalede værdi er 0. Angiv det maksimale antal replikaer i partition, der kan være beskadiget, før partitionen betragtes som beskadiget. |
| UpgradeReplicaSetCheckTimeout | **Uden status service**– inden for et enkelt opgradering domæne Service-strukturen forsøge at sikre, at andre forekomster af tjenesten er tilgængelige. Hvis destinationen forekomst antallet er mere end ét, venter tjenesten strukturen for mere end én forekomst skal være tilgængeligt, op til maksimalt timeoutværdien. Denne timeout angives ved hjælp af egenskaben UpgradeReplicaSetCheckTimeout. Hvis timeout udløber, fortsætter tjenesten strukturen med opgraderingen, uanset antallet af forekomster af tjenesten. Hvis destinationen forekomst antallet er en, Service-strukturen venter ikke, og med det samme fortsætter med opgraderingen. **Med høj sikkerhed service**– inden for et enkelt opgradering domæne Service-strukturen forsøge at sikre, at replikasættet har en quorum. Tjenesten strukturen venter på en quorum skal være tilgængeligt, op til en maksimale timeoutværdien (angivet af egenskaben UpgradeReplicaSetCheckTimeout). Hvis timeout udløber, fortsætter tjenesten strukturen med opgraderingen, uanset quorum. Denne indstilling er indstillet som aldrig (uendelig), når rullende fremad samt 900 sekunder når ruller tilbage. |
| ForceRestart | Hvis du opdaterer en konfiguration eller datapakke uden at opdatere tjenestekode, tjenesten genstartes kun, hvis egenskaben ForceRestart er indstillet til sand. Når opdateringen er fuldført, om tjenesten strukturen tjenesten, en ny konfigurationspakke eller datapakke er tilgængelig. Tjenesten er ansvarlig for at anvende ændringerne. Hvis det er nødvendigt, kan tjenesten genstarte sig selv. |



<br>
<br>
Kriteriet MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService og MaxPercentUnhealthyReplicasPerPartition kan angives per tjenestetype for en forekomst af programmet. Angive disse parametre til hver service giver mulighed for et program til at indeholde forskellige tjenester typer med forskellige evaluering politikker. For eksempel kan en uden status gateway tjenestetype have en MaxPercentUnhealthyPartitionsPerService, der er forskellig fra en med høj sikkerhed program tjenestetype for et bestemt programforekomst.

## <a name="next-steps"></a>Næste trin

[Opgradering af dit program ved hjælp af Visual Studio](service-fabric-application-upgrade-tutorial.md) vejleder dig gennem programmet opgraderes ved hjælp af Visual Studio.

[Opgradering af dit program ved hjælp af Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vejleder dig gennem programmet opgraderes ved hjælp af PowerShell.

Gøre dit program opgraderinger kompatibel ved at lære, hvordan du bruger [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lær at bruge avancerede funktioner under opgradering af dit program ved at referere til [Avancerede emner](service-fabric-application-upgrade-advanced.md).

Rette almindelige fejl i programmet opgraderinger ved at referere til trinnene i [Foretage fejlfinding af programmet opgraderinger](service-fabric-application-upgrade-troubleshooting.md).
 
