<properties
   pageTitle="Tjenesten strukturen klynge ressourcestyring: Bevægelse omkostninger | Microsoft Azure"
   description="Oversigt over bevægelse omkostninger for tjenesten strukturerede services"
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

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Tjenesten bevægelse omkostninger for har indflydelse på klynge ressourcestyring valgmuligheder
En vigtig faktor du bør overveje, når du forsøger at finde ud af, hvilke ændringer der skal foretage i en klynge og resultat af en løsning er de samlede omkostninger for at opnå denne løsning.

Flytning af tjenesten forekomster eller replikaer omkostninger CPU tid og netværk båndbredde som minimum. For med høj sikkerhed tjenester, kan det også koster mængden af plads på disk, skal du oprette en kopi af tilstanden, før du lukker den gamle replikaer. Klart vil du minimere omkostninger for en hvilken som helst løsning, der Azure Service strukturen klynge ressourcestyring dukker op med. Men du heller ønsker ikke at ignorere løsninger, der ville forbedre betydeligt tildeling af ressourcer i klyngen.

Klynge ressourcestyring har computing omkostninger og begrænse dem, mens den forsøger at administrere klynge i henhold til de andre mål på to måder. Først er, når du klynge ressourcestyring planlægger et nyt layout for-klyngen, tæller det hver Flyt, det vil gøre. I et simpelt tilfælde, hvis du får to løsninger med om den samme overordnede saldo (resultat) i slutningen, og Tag med de laveste omkostninger (samlet antal flytter).

Denne metode fungerer ret fint. Men som med standard- eller statisk belastning, er det sandsynligvis ikke i en kompleks system, alle flytter er ens. Nogle er sandsynligvis er meget mere dyr.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Ændre en replikas Flyt omkostninger og faktorer, du skal overveje
Som med rapportering indlæsning (en anden funktion af klynge Resource Manager), give du tjenesten en måde at præsentation rapportering hvordan dyrt tjenesten er at flytte på et bestemt tidspunkt.

Kode:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost har fire niveauer: nul, lav, Medium og høj. Dette er i forhold til hinanden, med undtagelse af nul. Nul betyder, at flytte en replika er gratis, og skal ikke tæller med resultatet af løsningen. Angive din Flyt omkostninger til høj er *ikke* en garanti for, at replikaen ikke flytte, lige, at det ikke flyttes, medmindre der er en god grund til.

![Flytte omkostninger som en faktor til at vælge replikaer for bevægelse][Image1]

MoveCost hjælper dig med at finde de løsninger, medfører det mindste forstyrrelse overordnede og er nemmere at opnå mens du stadig ankomme i tilsvarende balance. En tjeneste begrebet omkostninger kan være i forhold til mange ting. De mest almindelige faktorer ved beregning af omkostninger din Flyt er:

- Mængden af stater eller data, der har tjenesten til at flytte.
- Beløb for afbrydelse af klienter. Omkostninger for at flytte en primær replika er som regel højere end omkostningerne for at flytte en sekundær replika.
- Omkostninger for at afbryde handlingen startet. Nogle handlinger på dataene gemme niveau eller handlinger, der udføres som svar på en klientopkald er dyrt. Efter et bestemt sted vil du ikke stoppe dem, hvis du ikke behøver at. Så for varigheden af handlingen, du laver op omkostninger for at reducere sandsynligheden for, at den tjeneste eller forekomst vil flytte. Når handlingen er færdig, skal sætte du den tilbage til normal.

## <a name="next-steps"></a>Næste trin
- Tjenesten strukturen klynge Resource Manager bruger metrik til at administrere forbrug og kapacitet i klyngen. Hvis du vil vide mere om målepunkter og hvordan du konfigurerer dem, se [administrere Ressourceforbrug og Indlæs i tjenesten strukturen med målepunkter](service-fabric-cluster-resource-manager-metrics.md).
- Se [justering af din Service-strukturen klynge](service-fabric-cluster-resource-manager-balancing.md)for at få mere for at vide om, hvordan ressourcestyring klynge administrerer og afbalancerer Indlæs i klyngen.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
