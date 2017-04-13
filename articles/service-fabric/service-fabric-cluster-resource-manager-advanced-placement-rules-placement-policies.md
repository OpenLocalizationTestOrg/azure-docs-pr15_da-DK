<properties
   pageTitle="Tjenesten strukturen klynge ressourcestyring - placeringen politikker | Microsoft Azure"
   description="Oversigt over ekstra placering politikker og regler for tjenesten strukturerede Services"
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

# <a name="placement-policies-for-service-fabric-services"></a>Placering af politikker for tjenesten strukturerede services
Der er mange forskellige yderligere regler, der kan du komme hvor om, hvis din Service-strukturen klynge samlet på tværs af en geografiske afstand sig flere datacentre eller Azure områder, eller hvis dit miljø strækker sig over flere områder af geopolitiske kontrolelement (eller nogle andre tilfælde, hvor du har juridiske eller politik grænser, der interesserer dig, eller den afstand, der er involveret har faktisk ydeevne/ventetid virkning). De fleste af disse kunne konfigureres via egenskaber for knude og placeringen begrænsninger, men nogle er mere kompliceret. Vi giver dig disse yderligere commmands for at gøre tingene nemmere. Ligesom med andre placeringen begrænsninger, placeringen politikker kan konfigureres på grundlag per navngivne service forekomst.

## <a name="specifying-invalid-domains"></a>Angive ugyldige domæner
Politikken InvalidDomain placering kan du angive, at en bestemt fejl domæne er ugyldige for denne arbejdsbyrde. Denne politik sikrer, at en bestemt tjeneste aldrig kører i et bestemt område, for eksempel af geopolitiske eller virksomhedens politik årsager. Flere ugyldige domæner kan angives via separate politikker.

![Eksempel på ugyldigt domæne][Image1]

Kode:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Angive nødvendige domæner
Påkrævet domæne placeringen politikken kræver, at alle med høj sikkerhed replikaer eller forekomster af uden status tjenesten til tjenesten være til stede i det angivne domæne. Du kan angive flere nødvendige domæner via separate politikker.

![Eksempel på påkrævet domæne][Image2]

Kode:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Angive et foretrukne domæne for de primære kopier
Det foretrukne primære domæne er en interessant kontrolelementet, da det giver mulighed for markering af det et domæne, som primært skal placeres, hvis det er muligt at gøre det. Når alt er sund afsluttes primært på dette domæne. Bør domænenavn eller den primære replika fejl eller lukkes eller anden grund, primært bliver overført til en anden placering. Hvis denne placering ikke er i det foretrukne domæne, vil derefter når det er muligt ressourcestyring klynge flytte det tilbage til det foretrukne domæne. Denne indstilling giver naturligt kun mening for med høj sikkerhed tjenester. Denne politik er mest effektiv i klynger, som er fordelt Azure områder eller flere datacentre. I disse tilfælde du bruger alle placeringer til redundans, men foretrækker, at de primære replikaer skal placeres i en bestemt placering for at give kortere ventetid for handlinger, gå til primært (skriver og også som standard alle læser der leveres af primært).

![Foretrukne primære domæner og Failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Kræve replikaer skal fordeles mellem alle domæner og tillade emballagen
En anden politik, kan du angive er at kræve replikaer skal altid fordeles mellem de tilgængelige fejl domæner. Dette sker som standard i de fleste tilfælde, hvor klyngen er i orden, men der er degenerate tilfælde, hvor replikaer for en given partition kan ender midlertidigt pakket i en enkelt fejl eller opgradere domæne. For eksempel Lad os sige, selvom klyngen har 9 noder i 3 fejl domæner (0, 1 og 2), og din tjeneste har 3 replikaer, noder, der blev brugt for disse kopier i et domæner 1 og 2 gik, og på grund af problemer med kapacitet ingen af de andre noder i disse domæner er gyldig. Hvis tjenesten strukturen opbygge erstatninger for disse kopier, ressourcestyring klynge nødt til at placere dem i et domæne, 0, men, der opretter en situation, hvor et domæne begrænsningen der ignoreres. Den øger risikoen for, hele replikasættet kan gå tabt, (sad FD 0 skal være permananently mistet). (Yderligere oplysninger om begrænsninger og begrænsning prioriteter generelt se [dette emne](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Hvis du nogensinde har set en tilstand advarsel som "belastning har registreret en begrænsningsfejl for denne replika: strukturen: /<some service name> sekundær Partition <some partition ID> der overtræder begrænsningen: FaultDomain" har du klikker på denne betingelse eller noget i retning af den. Normalt disse situationer er forbigående (noderne ikke holde ned langt heltal, eller hvis de gør og vi har brug at opbygge erstatninger der er andre noder i de korrekte fejltolerance domæner, der er gyldige), men der er nogle arbejdsbelastninger, som vil hellere handel tilgængelighed til risikoen for at miste alle deres kopier. Vi kan gøre dette ved at angive politikken "RequireDomainDistribution", som sikrer, at ingen to replikaer fra samme partition nogensinde er tilladt i det samme fejl eller opgradere domæne.

Nogle arbejdsbelastninger hellere vil have target antallet af replikaer (kopier af tilstand) til enhver tid (væddemål mod samlede domæne fejl og viden om, at de kan gendannes lokal tilstand normalt), mens andre hellere vil tage nedetiden tidligere end offentliggjort de korrekte og dataloss problemstillinger. Da de fleste fremstilling arbejdsbelastninger kører med mere end 3 replikaer, er standard ikke kræver domæne fordeling og lade og belastningsjustering håndtere tilfælde normalt, selvom, der betyder, at midlertidigt et domæne har flere replikaer pakket i den.

Kode:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Nu, vil det være muligt at bruge disse konfigurationer for tjenester i en klynge, ikke blev kalibreret geografisk? Du kan sikker! Men der er ikke en god grund for – især de nødvendige, ugyldige og Foretrukne domænekonfigurationer undgås, medmindre du kører faktisk en geografisk samlet klynge – det ikke mening alle at tvinge en given arbejdsbyrde til at køre i et enkelt rack eller foretrækker nogle segmentet i din lokale klynge hen over en anden, medmindre der er forskellige typer hardware eller arbejdsbelastningen segmentering foregår , og disse tilfælde kan løses via normal placeringen begrænsninger.

## <a name="next-steps"></a>Næste trin
- For flere oplysninger om de andre indstillinger, der er tilgængeligt til konfiguration af tjenester udtjekning emnet på de andre klynge ressourcestyring konfigurationer tilgængelige [Få mere at vide om at konfigurere Services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
