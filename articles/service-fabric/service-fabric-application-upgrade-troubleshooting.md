<properties
   pageTitle="Fejlfinding i forbindelse med program opgraderinger | Microsoft Azure"
   description="Denne artikel omhandler nogle almindelige problemer rundt om opgradering af en tjeneste-strukturen programmet, og hvordan du løse dem."
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

# <a name="troubleshoot-application-upgrades"></a>Fejlfinding i forbindelse med program opgraderinger

I denne artikel beskrives nogle af de almindelige problemer rundt om opgradering af et program i Azure Service-strukturen og hvordan du løser dem.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Fejlfinding i forbindelse med opgradering af en mislykkedes program

Når en opgradering ikke, indeholder output fra kommandoen **Get-ServiceFabricApplicationUpgrade** yderligere oplysninger om fejlfinding fejlen.  Følgende liste angiver, hvordan de yderligere oplysninger kan bruges:

1. Identificere fejl.
2. Identificere årsagen til fejl.
3. Isolere en eller flere fejlbehæftede komponenter til yderligere undersøgelse.

Disse oplysninger er tilgængelige, når tjenesten strukturen registrerer manglende uanset om **FailureAction** er annullere eller annullere opgraderingen.

### <a name="identify-the-failure-type"></a>Identificere fejl

I outputtet af **Get-ServiceFabricApplicationUpgrade**identificerer **FailureTimestampUtc** tidsstemplet (i UTC), som blev registreret en fejl ved opgradering af Service-strukturen og **FailureAction** blev udløst. **FailureReason** identificerer en af tre overordnet mulige årsager til fejlen:

1. UpgradeDomainTimeout - angiver, at et bestemt opgradering domæne tog lang tid at fuldføre og **UpgradeDomainTimeout** udløbet.
2. OverallUpgradeTimeout - angiver, at den overordnede opgraderingen tog lang tid at fuldføre og **UpgradeTimeout** udløbet.
3. HealthCheck - angiver, når du har opgraderet en update-domæne, programmet forblev beskadiget ifølge de angivne sundhed politikker og **HealthCheckRetryTimeout** udløbet.

Disse poster kun vises i output når opgraderingen mislykkes og starter ruller tilbage. Yderligere oplysninger vises afhængigt af fejlen.

### <a name="investigate-upgrade-timeouts"></a>Undersøge opgradering timeout

Opgradere timeout mislykkede forsøg mest almindeligt skyldes problemer med tjenesten tilgængelighed. Output følge dette afsnit er typisk for opgraderinger, hvor tjenesten replikaer eller forekomster ikke starte i den nye version af kode. Feltet **UpgradeDomainProgressAtFailure** registrerer et øjebliksbillede af en hvilken som helst ventende opgradering arbejdet på tidspunktet for fejl.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

I dette eksempel opgraderingen mislykkedes ved opgradering domæne *MYUD1* og to partitioner (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* og *4b43f4d8-b26b-424e-9307-7a7a62e79750*) er fastlåst. Partitionerne er fastlåst, fordi runtime kunne ikke foretage primære replikaer (*WaitForPrimaryPlacement*) på target noder *Node1* og *Node4*.

Kommandoen **Get-ServiceFabricNode** kan bruges til at kontrollere, at disse to noder i opgradering domæne *MYUD1*. *UpgradePhase* står *PostUpgradeSafetyCheck*, hvilket betyder, at kontrollen safety som opstår, når alle noder i opgradering domænet er færdig med at opgradere. Alle disse oplysninger, der peger på et potentielt problem med den nye version af programmet koden. De mest almindelige problemer er service-fejl i Åbn eller opgradering til primære kodestier.

En *UpgradePhase* af *PreUpgradeSafetyCheck* betyder, at der er problemer med forberede opgradering domænet, før den blev udført. I dette tilfælde er de mest almindelige problemer service-fejl i Luk eller degradering fra primære kodestier.

Den aktuelle **UpgradeState** er *RollingBackCompleted*, så den oprindelige opgraderingen skal være udført med en rollback **FailureAction**, som automatisk rulles tilbage opgraderingen ved fejl. Hvis oprindelige opgraderingen blev udført med en manuel **FailureAction**, vil derefter opgraderingen i stedet en afbrudt midlertidigt at tillade direkte fejlfinding af programmet.

### <a name="investigate-health-check-failures"></a>Undersøge sundhed fejlene

Sundhed fejlene kan udløses af forskellige problemer, der kan forekomme, når alle noder i en opgradering domæne er færdig med opgradering og overføre alle safety Kontroller. Output følge dette afsnit er typisk af en fejl ved opgradering på grund af mislykkede tilstandskontrol. Feltet **UnhealthyEvaluations** registrerer et øjebliksbillede af tilstandskontrol mislykkede på tidspunktet for opgraderingen efter den angivne [tilstandspolitik](service-fabric-health-introduction.md).

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Ved at undersøge sundhed fejlene først kræver en forståelse af tjenesten strukturen sundhed modellen. Men selv uden sådanne en dybtgående forståelse, kan vi se, at to tjenester er beskadiget: *strukturen: / DemoApp/Svc3* og *strukturen: / DemoApp/Svc2*, sammen med tilstandsrapporter fejl ("InjectedFault" i dette tilfælde). I dette eksempel er to ud af fire services beskadiget, som er under standard målet på 0% beskadiget (*MaxPercentUnhealthyServices*).

Opgraderingen blev afbrudt på fejlbehæftede ved at angive en **FailureAction** af manuel, når du starter opgraderingen. Denne tilstand giver os mulighed for at undersøge det aktive system i fejltilstand før du udfører yderligere handling.

### <a name="recover-from-a-suspended-upgrade"></a>Gendanne fra en opgradering af afbrudt

Med en rollback **FailureAction**, der er ingen genoprettelse, det er nødvendigt, da opgraderingen automatisk annullerer på fejlbehæftede. Med en manuel **FailureAction**er der flere gendannelse muligheder:

1. Manuelt udløse en rollback
2. Fortsæt gennem resten af opgraderingen manuelt
3. Genoptage overvåget opgraderingen

Kommandoen **Start ServiceFabricApplicationRollback** kan bruges til enhver tid til at starte annullerer programmet. Når kommandoen returnerer korrekt, rollback anmodningen er registreret i systemet og starter kort tid derefter.

Kommandoen **CV-ServiceFabricApplicationUpgrade** kan bruges til at fortsætte gennem resten af opgraderingen manuelt, ét opgradering domæne ad gangen. I denne tilstand udføres kun safety kontrol af systemet. Det er ikke længere tilstandskontrol udføres. Denne kommando kan kun bruges, når *UpgradeState* viser *RollingForwardPending*, hvilket betyder, at det aktuelle opgradering domæne er færdig med at opgradere, men det er ikke startet (ventende).

Kommandoen **Opdater ServiceFabricApplicationUpgrade** kan bruges til at genoptage overvåget opgraderingen begge sikkerheds- og kontrol, der udføres.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

Opgraderingen fortsætter fra en opgradering domæne, hvor det sidst blev afbrudt, og brug den samme opgradere parametre og sundhed politikker som før. Hvis det er nødvendigt, kan alle opgradering parametre og sundhed politikker, der vises i den foregående output ændres i den samme kommando, når opgraderingen genoptages. I dette eksempel blev opgraderingen genoptages i overvågede tilstand med parametrene og sundhed politikkerne være uændret.

## <a name="further-troubleshooting"></a>Yderligere fejlfinding

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Tjenesten strukturen ikke følger de angivne sundhed politikker

Mulig årsag 1:

Tjenesten oversætter alle procenter til faktiske antal enheder (for eksempel replikaer, partitioner og services) for systemtilstand evaluering og altid runder op til hele enheder. Eksempelvis hvis den maksimale _MaxPercentUnhealthyReplicasPerPartition_ er 21%, og der er fem kopier, derefter tjenesten strukturen kan op til to beskadiget replikaer (pågældende is,'Math.Ceiling (5\*0.21)). Dermed, sundhed politikker skal du vælge i overensstemmelse hermed.

Mulig årsag 2:

Sundhed politikker er angivet i procent for samlede tjenester og ikke specifikke service forekomster. For eksempel før en opgradering, hvis et program har fire service forekomster A, B, C og D, hvor tjenesten D er beskadiget, men med lille betydning for programmet. Vi vil ignorere kendte beskadiget tjenesten D under opgradering og angive parameteren *MaxPercentUnhealthyServices* skal være 25%, hvis du kun A, B og C skal være sund.

Men under opgraderingen, D kan blive sund mens C bliver beskadiget. Opgraderingen vil stadig det lykkes, fordi kun 25% af tjenesterne er beskadiget. Men det kan medføre en uventet fejl på grund af C bliver uventet beskadiget i stedet for D. I så fald D skal være formateret som en anden tjeneste fra A, B og C. Da sundhed politikker er angivet i tjenestetype, kan forskellige beskadiget procentdel tærskler anvendes på forskellige tjenester. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Jeg har angivet en politik for systemtilstand til programmet opgradering, men stadig ikke opgraderingen for nogle timeout, som jeg aldrig har angivet

Når sundhed politikker ikke er til rådighed til anmodning om opgradering, hentes de fra *ApplicationManifest.xml* i den aktuelle version af programmet. Eksempelvis hvis du opgraderer programmet X fra version 1.0 til version 2.0, programmet sundhed politikker, der er angivet for i version 1.0 bruges. Hvis en anden tilstandspolitik skal bruges til opgraderingen, skal politikken skal angives som en del af programmet opgradering API opkaldet. De politikker, der er angivet som en del af API opkaldet gælder kun under opgraderingen. Når opgraderingen er fuldført, anvendes de politikker, der er angivet i *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Forkert timeout er angivet

Du kan have tænkt om, hvad der sker, når timeout angives mappenavnet. Du kan for eksempel har en *UpgradeTimeout* , der er mindre end *UpgradeDomainTimeout*. Svaret er, at der returneres en fejl. Der returneres en fejl, hvis *UpgradeDomainTimeout* er mindre end summen af *HealthCheckWaitDuration* og *HealthCheckRetryTimeout*, eller hvis *UpgradeDomainTimeout* er mindre end summen af *HealthCheckWaitDuration* og *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Min opgraderinger det tager for lang

Tidspunktet for en opgradering til at fuldføre, afhænger af tilstandskontrol og timeout, der er angivet. Tilstandskontrol og timeout afhænger af, hvor lang tid det tager at kopiere, implementere og stabilisere programmet. Der for tilstanden med timeout kan betyde mere mislykkedes opgraderinger, så vi anbefaler, at du starter konservativt med længere timeout.

Her er en hurtig opdatering om hvordan timeout interagere med opgradering klokkeslæt:

Opgraderer til en opgradering domæne ikke kan gennemføre hurtigere end *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Fejl ved opgradering kan forekomme hurtigere end *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Opgradering klokkeslættet for en opgradering domæne er begrænset af *UpgradeDomainTimeout*.  Hvis *HealthCheckRetryTimeout* og *HealthCheckStableDuration* begge er forskellig fra nul og tilstanden for programmet bevarer skifte frem og tilbage, derefter timeout opgraderingen til sidst på *UpgradeDomainTimeout*. *UpgradeDomainTimeout* starter optælling, når opgraderingen for det aktuelle opgradering domæne begynder.

## <a name="next-steps"></a>Næste trin

[Opgradering af dit program ved hjælp af Visual Studio](service-fabric-application-upgrade-tutorial.md) vejleder dig gennem programmet opgraderes ved hjælp af Visual Studio.

[Opgradering af dit program ved hjælp af Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vejleder dig gennem programmet opgraderes ved hjælp af PowerShell.

Styre, hvordan dit program opgraderer ved hjælp af [Opgradere parametre](service-fabric-application-upgrade-parameters.md).

Gøre dit program opgraderinger kompatibel ved at lære, hvordan du bruger [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lær at bruge avancerede funktioner under opgradering af dit program ved at referere til [Avancerede emner](service-fabric-application-upgrade-advanced.md).

Rette almindelige fejl i programmet opgraderinger ved at referere til trinnene i [Foretage fejlfinding af programmet opgraderinger](service-fabric-application-upgrade-troubleshooting.md).
 
