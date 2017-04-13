<properties
   pageTitle="Defragmentering af målepunkter i Azure Service-strukturen | Microsoft Azure"
   description="En oversigt over brug af defragmentering eller følgesedler som en strategi for målepunkter i tjenesten struktur"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentering af målepunkter og Indlæs i tjenesten struktur
Tjenesten strukturen klynge ressourcestyring drejer hovedsageligt sig justering af med hensyn til distribuere afkrydsningsfeltet Indlæs – sørge for, at alle knuderne i klyngen lige anvendes. Dette er som regel den mest sikre og smartest layout med hensyn til overlevende fejl, fordi det gør du sørge for, at undladelse givet ikke udføre ud af nogle store procentdelen af en given arbejdsbyrde. Tjenesten strukturen klynge ressourcestyring understøtter en anden strategi samt, hvilket er defragmentering. Defragmentering betyder generelt, i stedet for forsøger at distribuere anvendelsen af en metrikværdi på tværs af klyngen, vi skal faktisk forsøge at konsolidere den. Dette er en fortunate vending af vores normal strategi – i stedet for at optimere klynge baseret på minimering gennemsnitlige standardafvigelsen for metriske indlæsning af et givet nøgletal, vi begynde at optimere til øges afvigelse. Men Hvorfor skulle du denne strategi?

Godt, hvis du har sprede afkrydsningsfeltet Indlæs jævnt mellem knuderne i klyngen har derefter du spist op i nogle af de ressourcer, der har noderne til at tilbyde. Dette ikke er normalt et problem, men nogle gange nogle arbejdsbelastninger, som opretter tjenester, som er særligt store og forbruge størstedelen af en node – sig 75% til 95% af en node ressourcer ville ender dedikeret til en enkelt service forekomst eller replika. Det ikke er et problem, registrerer ressourcestyring klynge på klokkeslættet for oprettelsen af tjenesten, som det skal omorganisere klynge for at skabe plads til denne store arbejdsbelastningen og angive om gør det sker, men i mellemtiden pågældende arbejdsbelastningen der skal vente planlægges i klyngen.

Da der planlægningen af nye arbejdsbelastninger er som regel mindst lidt ventetid følsomme, hvis vi ikke gøre noget forskelligt kan vi nogle gange blæse højre ved disse SLA Hvis der er mange af tjenester og tilstand for at flytte rundt, især hvis arbejdsmængder i klyngen er generelt større (og dermed tager længere tid at bevæge dig rundt i klyngen). Faktisk, når vi målt oprettelse af gange i simulering, der er baseret på reelle klynge data, vi har set, hvis services var stort nok og klyngen ret blev brugt, at oprettelsen af disse store tjenester vil langsommere, og, kan vi forbedre dette ved at introducere politikken for defragmentering målepunkter.

Ligesom fil kan oprettelses- eller access få langsommere Hvis computerens harddisk blev fragmenteret og kan være sped ved at defragmentere drevet, så der er blevet større sammenhængende blokke, kan du konfigurere defragmentering målepunkter for at få ressourcestyring klynge proaktiv forsøge at sammentrænge indlæsning af tjenesterne til færre noder, så der er (næsten) altid plads til lige store services , så de skal have oprettet hurtigt. De fleste personer behøver ikke, fordi tjenester skal normalt være lille og det er derfor ikke svært at finde plads til dem, men hvis du har store services og har brug for dem oprettet hurtigt (og er villig til at acceptere andre kompromiserne som øget impactfulness antal mislykkede forsøg og nogle ressourcer, der venstre unutilized, mens de venter på arbejdsbelastninger planlægges) og derefter defragmentering strategi er noget for dig.

Diagrammet nedenfor giver en visuel repræsentation af to forskellige klynger, én, som er defragmenteret og én, som ikke er. I ikke-opgjorte tilfældet, bør du overveje de bevægelser, som er nødvendige til sted et af de største serviceobjekter, hvis en ny blev oprettet, sammenlignet med defragmenteret klynge, hvor det kan med det samme placeres på noder 4 og 5.

![Sammenligne afstemmes og defragmenteret klynger][Image1]

## <a name="defragmentation-pros-and-cons"></a>Defragmentering fordele og ulemper
Hvad er så disse grundlæggende kompromiserne? Vi anbefaler indgående måling af din arbejdsbelastninger før aktivering af defragmentering målepunkter. Her er en hurtig liste over ting at huske på:

| Defragmentering medarbejdere  | Defragmentering ulemper |
|----------------------|----------------------|
|Giver mulighed for hurtigere oprettelse af store services | Koncentrater indlæse på færre noder, øge konflikt
|Gør det muligt at sænke flytning af data under oprettelse    | Fejl kan påvirke flere tjenester og medføre flere transportspand
|Giver mulighed for omfattende beskrivelse af krav og videreudnyttelse af mellemrum | Mere komplekse overordnede ressourcestyring konfiguration

Kan du blande defragmenteret og normal målepunkter i den samme klynge og ressourcestyring vil gøre det er bedst at sikre, at du får et layout, der konsoliderer så mange defragmentering målepunkter, som det kan mens du forsøger at sprede resten. De præcise resultater, får du vist, afhænger af antallet justering af målepunkter sammenlignet med antallet af defragmentering målepunkter og tykkelser, aktuelle belastning, osv.

## <a name="configuring-defragmentation-metrics"></a>Konfigurere defragmentering målepunkter
Konfigurere defragmentering målepunkter er en global beslutning i klyngen og individuelle målepunkter kan vælges for defragmentering:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Næste trin
- Ressourcestyring klynge indeholder mange indstillinger til at beskrive klyngen. Hvis du vil vide Læs mere om dem denne artikel på [en beskrivelse af en tjeneste-strukturen klynge](service-fabric-cluster-resource-manager-cluster-description.md)
- Målepunkter er, hvordan Service-strukturen klynge Resource Manager administrerer forbrug og kapacitet i klyngen. Hvis du vil vide Læs mere om dem og hvordan du konfigurerer dem [denne artikel](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
