<properties
   pageTitle="Azure Service-strukturen nedbrud | Microsoft Azure"
   description="Azure Service-strukturen indeholder de funktioner, der er nødvendige for at håndtere alle de typer af nedbrud. I denne artikel beskrives typerne af nedbrud, der kan opstå, og hvordan du håndterer dem."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Nedbrud i Azure Service struktur

En vigtig del af fremvisning af en høj tilgængelighed skyen program er at sikre, at det kan klare alle forskellige typer fejl, herunder dem, der er uden for dit kontrolelement. I denne artikel beskrives det fysiske layout af en Azure Service strukturen klynge i forbindelse med potentielle nedbrud og giver vejledning i, hvordan du håndterer sådanne nedbrud at begrænse eller fjerne risikoen for nedetid eller tab af data.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Fysisk layoutet af tjenesten strukturen klynger i Azure

For at forstå risikoen i forbindelse med forskellige typer fejl, er det praktisk at vide, hvordan klynger fysisk placeres i Azure.

Når du opretter en tjeneste-strukturen klynge i Azure, skal du vælge et område, hvor den skal placeres. Azure infrastrukturen klargør derefter ressourcer for denne klynge i området, især antallet af virtuelle maskiner (VM'er) anmodet om. Lad os se nærmere på hvor og hvordan disse FOS er klargjort.

### <a name="fault-domains"></a>Fejl domæner

FOS i klyngen er jævnt udbrede logiske grupper kaldet et domæner (FDs), som segment skal tildeles de computere, der er baseret på potentielle fejl i host hardware som standard. Specifikt, hvis der findes to FOS på to forskellige FDs, kan du være sikker på, at de ikke dele den samme power kilde eller netværket parameter. Som et resultat, påvirker et lokalt netværk eller strømsvigt påvirker én VM ikke det andet, så Service-strukturen at genoprette arbejde indlæsning af svarer maskinen inden for klyngen.

Du kan visualisere layoutet af din klynge på tværs af et domæner ved hjælp af klynge kortet fremgår [Service strukturen Explorer](service-fabric-visualizing-your-cluster.md):

![Noder udbrede domæner fejl i tjenesten strukturen Explorer][sfx-cluster-map]

>[AZURE.NOTE] Anden aksen i klynge kortet viser opgradering domæner, hvilken logisk gruppe noder baseret på planlagt vedligeholdelsesaktiviteter. Tjenesten strukturen klynger i Azure placeres altid på tværs af fem opgradering domæner.

### <a name="geographic-distribution"></a>Geografiske fordeling

Der er i øjeblikket [26 Azure områder i hele verden][azure-regions], med flere mere offentliggjort. Et enkelt område kan indeholde et eller flere fysiske datacentre afhængigt af behov og tilgængeligheden af passende placeringer mellem andre faktorer. Bemærk, at selv i områder, der indeholder flere fysisk datacentre, der er ingen garanti, din klynge FOS skal fordeles jævnt over de fysiske steder. Faktisk er, alle FOS for en given klynge klargjort i et enkelt fysisk websted.

## <a name="dealing-with-failures"></a>Håndtere fejl

Der er flere typer fejl, der kan påvirke din klynge, hver med sin egen afhjælpning. Vi ser på dem i rækkefølge af sandsynligheden for skal udføres.

### <a name="individual-machine-failures"></a>Individuelle maskine fejl

Som nævnt, indebærer individuelle maskine fejl, i hvor VM eller i hardware eller software-hosting inden for et et domæne, ikke risici på egen hånd. Tjenesten strukturen typisk registrerer fejlen i sekunder og svare tilsvarende baseret på tilstanden for klyngen. Eksempelvis hvis noden er vært for de primære replikaer for en partition, er en ny primær hjælpelinjen fra den partition sekundær replikaer. Når Azure bringer mislykkedes maskinen op, den Deltag igen på klynge automatisk og tage igen på sin del af arbejdsbelastningen.

### <a name="multiple-concurrent-machine-failures"></a>Flere samtidige maskine fejl

Mens fejl domæner reducere risikoen for samtidige maskine fejl, er der altid risikoen for flere vilkårlige mislykkede forsøg på at få ned flere computere i en klynge samtidigt.

Generelt, så længe en størstedelen af noderne ikke tilgængelig, fortsat klyngen fungerer, selvom på nederste kapacitet som med høj sikkerhed replikaer få pakket i et mindre antal computere og færre uden status forekomster er tilgængelige til at sprede Indlæs.

#### <a name="quorum-loss"></a>Quorum tab

Hvis en størstedelen af replikaerne for en med høj sikkerhed tjeneste partition går, indsætter denne partition en tilstand, kaldet "quorum tab". På dette tidspunkt stopper tjenesten strukturen tillade skriver til denne partition til at sikre, at tilstanden forbliver ensartet og pålidelig. Vi i kraft, hvis du vælger at acceptere et tidsrum utilgængelighed at sikre, at klienter ikke bliver bedt om, at deres data blev gemt, selvom det var ikke. Bemærk, at hvis du har tilmeldt sig tillade læser fra sekundær replikaer for den pågældende med høj sikkerhed tjeneste, kan du fortsætte til at udføre de læste visninger, mens du er i denne tilstand. En partition forbliver i quorum tab, indtil et tilstrækkeligt antal replikaer kommer tilbage eller klyngeadministratoren tvinger systemet til at flytte på ved hjælp af [Reparer ServiceFabricPartition API][repair-partition-ps].

>[AZURE.WARNING] Udføre handlingen Reparer, mens den primære replika er nede medfører tab af data.

System services kan også lide quorum tab, med den virkning, der specifikt for den pågældende tjeneste. For eksempel påvirker quorum tab i tjenesten naming navneoversættelse, mens quorum tab i tjenesten failover manager blokerer oprettelse af ny tjeneste og failover. Bemærk, at i modsætning til for dine egne tjenester forsøg på at reparere systemets tjenester er *ikke* anbefales. I stedet, er det bedst bare vente, indtil de ned replikaer returnere.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Minimere risikoen for quorum tab

Du kan minimere risikoen for quorum tab ved at øge sæt target replikastørrelsen til din tjeneste. Det er praktisk at betragte antallet af replikaer, du har brug for i form af antallet af tilgængelige knuder, du kan acceptere på, når under resterende tilgængelige for skrivning, holde styr på i har noget imod dette program eller klynge opgraderinger kan gøre noder ikke tilgængelig i øjeblikket, ud over hardware mislykkede forsøg.

Overvej følgende eksempler med, at du har konfigureret dine tjenester for at få en MinReplicaSetSize på tre, det mindste tal, der anbefales til fremstilling tjenester. Med en TargetReplicaSetSize tre (én primær og to secondaries), en hardwarefejl under en opgradering (to replikaer ned) medfører quorum tab og din tjeneste bliver skrivebeskyttet tilstand. Du kan også hvis du har fem replikaer, vil du ikke kunne klare to fejl under opgraderingen (tre replikaer ned), som de resterende to replikaer kan stadig danner en quorum i mindste replikeringssættet.

### <a name="data-center-outages-or-destruction"></a>Data center udfald eller ødelæggelse

I sjældne tilfælde kan fysisk datacentre bliver ikke tilgængelig i øjeblikket på grund af tab af power- eller netværksproblemer connectivity. Dine Service-strukturen klynger og programmer på samme måde udgår i disse tilfælde, men dataene bevares. Til klynger kører i Azure, du kan få vist opdateringer på udfald på [statussiden for Azure][azure-status-dashboard].

I meget sandsynligt begivenheden, et hele fysiske datacenter er ødelagt, går en hvilken som helst Service-strukturen klynger hostet der tabt, sammen med deres tilstand.

For at beskytte mod denne mulighed, der er alvorligt vigtigt for med jævne mellemrum [sikkerhedskopiere din tilstand](service-fabric-reliable-services-backup-restore.md) til en geografisk overflødige store og sikre, at du har bekræftet muligheden for at gendanne den. Hvor ofte du opretter en sikkerhedskopi vil være afhænger af din gendannelse punkt målsætning (frigivne Produktionsordre). Selvom du ikke har fuldt implementeret sikkerhedskopiering og gendannelse endnu, skal du implementere en handler til den `OnDataLoss` begivenhed, så du kan logge på, når den sker som følger:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Software fejl og andre kilder om tab af data

Kode fejl i services, human funktionsdygtige fejl og brud på sikkerheden er mere almindelig end udbredte data center fejl som en årsag til tab af data. Men i alle tilfælde skal gendannelse strategi er den samme: få regelmæssige sikkerhedskopier af alle med høj sikkerhed tjenester og øvelse din mulighed for at gendanne tilstanden.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du simulere forskellige fejl ved hjælp af [mulighed for at teste framework](service-fabric-testability-overview.md)
- Læs andre nedbrud og høj tilgængelighed ressourcer. Microsoft har udgivet en stor mængde vejledning på disse emner. Mens nogle af disse dokumenter henvise til bestemte teknikker til brug i andre produkter, indeholder de mange generelle bedste fremgangsmåder, du kan anvende i konteksten Service-strukturen:
 - [Tjekliste for tilgængelighed](../best-practices-availability-checklist.md)
 - [Udføre en nedbrud gendannelse analysér](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Nedbrud og høj tilgængelighed til Azure-programmer][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
