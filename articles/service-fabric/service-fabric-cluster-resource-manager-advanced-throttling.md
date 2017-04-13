<properties
   pageTitle="(Throttling) i Service-strukturen klynge ressourcestyring | Microsoft Azure"
   description="Lær at konfigurere throttles leveres af tjenesten strukturen klynge ressourcestyring."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>(Throttling) funktionsmåden af tjenesten strukturen klynge ressourcestyring
Selvom du har konfigureret ressourcestyring klynge korrekt, kan klyngen få afbrudt. Der kan for eksempel være samtidig node eller et domæne mislykkede forsøg - hvad ville ske, hvis, der opstod en opgradering? Ressourcestyring forsøger er bedst at løse alt, men i gange således vil du måske overveje en backstop, så klyngen selve har mulighed for at stabilisere (de knuder, der går til at komme tilbage gør, netværk betingelser reparere sig selv, rettede bit installeres). For at hjælpe dig med disse mulige situationer, omfatter Service strukturen klynge ressourcestyring flere throttles. Bemærk, at disse throttles er ret forstyrrende og normalt ikke bør benyttes, medmindre der er blevet nogle forsigtig matematisk færdig omkring mængden af parallelle arbejde, som rent faktisk kan udføres i klyngen, såvel som en hyppige skal reagere på disse sorterer af (ahem) ikke-planlagt makroskopiske konfigureres igen begivenheder (AKA: "Meget forkert dage").

Generelt, anbefales det at undgå meget ringe dage gennem andre indstillinger (som normalt kode opdateringer og undgå overscheduling klyngen til at begynde med) i stedet for (throttling) din klynge for at forhindre, at den ved hjælp af ressourcer, når den forsøger at løse selve). Throttles har standardværdier, som vi har fundet via oplevelse skal være standardindstillinger for ok, men du skal sandsynligvis nærmere og finjustere dem til din forventede faktiske belastning. Mens alt ikke for en udskrevet eller indlæsning af klynge er den bedste fremgangsmåde kan du bestemme, at der er tilfælde som, (indtil du kan løse dem) tager hvor du skal have et par throttles på plads, selvom det betyder, at klyngen længere tid at stabilisere.

##<a name="configuring-the-throttles"></a>Konfigurere throttles
Throttles, der er medtaget som standard er:

-   GlobalMovementThrottleThreshold – dette styrer det samlede antal bevægelser i klyngen over stykke tid (defineret som GlobalMovementThrottleCountingInterval, værdi i sekunder)
-   MovementPerPartitionThrottleThreshold – dette styrer det samlede antal bevægelser til en tjeneste partition over stykke tid (MovementPerPartitionThrottleCountingInterval, værdi i sekunder)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Vær opmærksom på, at de fleste tilfælde vi har set kunder, der bruger disse throttles, der er gået fordi de allerede var i en begrænset ressource-miljø (f.eks begrænset netværksbåndbredde til individuelle noder eller diske, som ikke op til krav parallelle replika opbygger, er der står på dem) som mente, at disse handlinger vil ikke kunne lykkes eller ville være langsom alligevel.  I følgende situationer var kunder vant til at kende, som de er blevet potentielt udvide mængden tid, det vil tage klynge for at oprette forbindelse til en stabil tilstand, herunder at kende, som de kan ender kører på nederste overordnet pålidelighed, mens de er blevet begrænset.

## <a name="next-steps"></a>Næste trin
- For at vide om, hvordan ressourcestyring klynge administrerer og afbalancerer Indlæs i klyngen, skal du se artiklen på [justering af belastning](service-fabric-cluster-resource-manager-balancing.md)
- Ressourcestyring klynge indeholder mange indstillinger til at beskrive klyngen. Hvis du vil vide Læs mere om dem denne artikel på [en beskrivelse af en tjeneste-strukturen klynge](service-fabric-cluster-resource-manager-cluster-description.md)
