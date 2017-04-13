<properties
   pageTitle="Kapacitet, planlægning af Service-strukturen apps | Microsoft Azure"
   description="Beskriver, hvordan du identificere antallet af Beregn knuder, der kræves til en-strukturen tjenesteprogram"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Kapacitet, planlægning af Service-strukturen programmer


Dette dokument lærer du at anslå mængden ressourcer (CPU'er, RAM, lagerplads på disken), skal du køre programmerne Azure Service struktur. Det er fælles for din ressourcekrav til ændrer sig med tiden. Du typisk kræver nogle ressourcer, mens du udvikle/test din tjeneste, og klik derefter kræver flere ressourcer, som du gå til fremstilling og dit program vokser i popularitets. Når du designer dit program, gennemgå de langsigtede krav, og Foretag valg, der tillader din tjeneste skalere for at imødekomme høj kundebehov.

 Når du opretter en tjeneste-strukturen klynge, beslutter du typer af virtuelle maskiner (VM'er) udgør klyngen. Hver VM leveres med en begrænset mængde ressourcer i form af CPU'er (kerner og hastighed), netværksbåndbredde, RAM og plads. Som tiden vokser din tjeneste, kan du opgradere til VM'er, der tilbyder større ressourcer og/eller tilføje flere FOS til din klynge. For at gøre disse, skal du udvikle din tjeneste indledningsvis så den kan drage fordel af nye VM'er, der er dynamisk føjet til klyngen.

Nogle af tjenesterne administrere lille ingen data på FOS sig selv. Derfor skal kapacitet, planlægning af disse tjenester fokuserer primært på ydeevne, hvilket betyder, at vælge de relevante CPU'er (kerner og hastighed) af FOS. Desuden bør du overveje netværksbåndbredde, herunder hvor ofte netværk overførsler virkelighed, og hvor meget data overføres. Hvis din tjeneste skal udføre samt service brugen øges, kan du føje flere FOS til saldoen klynge og belastning på netværket, som på tværs af alle FOS.

Kapacitetsplanlægning af skal for tjenester, der administrerer store datamængder på FOS, Fokuser primært på størrelse. Du skal derfor omhyggeligt overveje kapaciteten i den VM RAM og plads. Virtuel hukommelse management system i Windows gør diskplads ligne RAM til programkode. Desuden indeholder Service-strukturen runtime smart sideopdeling at beholde kun varmt data i hukommelsen og flytte kolde dataene til disk. Programmer kan dermed bruge mere hukommelse, end findes fysisk på VM. Ydeevne, øges har du mere RAM blot, da VM holder mere plads i RAM. Du vælger VM skal have en stort nok til at gemme de data, du vil på VM disk. På samme måde, hvor VM har tilstrækkelig RAM til at give dig den ydeevne, du ønsker. Hvis din tjeneste data tiden vokser, kan du tilføje flere FOS klynge og partition data på tværs af alle FOS.

## <a name="determine-how-many-nodes-you-need"></a>Finde ud af, hvor mange noder, du har brug for

Partitionering din tjeneste, kan du tilpasse din tjeneste data ud. Du kan finde flere oplysninger om partitionering, [Partitionering Service struktur](service-fabric-concepts-partitioning.md). Hver partition skal være inden for en enkelt VM, men flere (lille) partitioner kan placeres i en enkelt VM. Så giver har du flere small partitioner dig større fleksibilitet, end du har et par større partitioner. At tage er, at hvis du har mange partitioner øges Service-strukturen omkostninger, og du kan ikke udføre overførte handlinger på tværs af partitioner. Der findes også flere potentielle netværkstrafik, hvis din tjenestekode ofte har brug at få adgang til filer, der bor i forskellige partitioner. Når du designer din tjeneste, skal du omhyggeligt overveje disse fordele og ulemper ankommer på en effektiv strategi for leverandør.

Lad os antage dit program har en enkelt med høj sikkerhed tjeneste, som har en store størrelse, du forventer at vokser til DB_Size GB i året. Du er villig til at tilføje flere programmer (og partitioner) som du oplever forøgelse ud over dette år.  Replikering faktor (RF), som bestemmer antallet af replikaer til din tjeneste påvirker den samlede DB_Size. Den samlede DB_Size på tværs af alle kopier er den replikering faktor multipliceret med DB_Size.  Node_Size repræsenterer disk mellemrum/RAM per node, du vil bruge til din tjeneste. Bedste ydeevne, DB_Size skal være i hukommelsen på tværs af klyngen og en Node_Size, der er omkring RAM VM bør vælges. Ved at tildele en Node_Size, der er større end RAM kapaciteten, tegner du siderækkefølgen leveres under kørsel af tjenesten struktur. Derfor, din ydeevne muligvis ikke er optimal, hvis dataene hele anses for at være varm (siden dataene er gruppe ind/ud). For mange tjenester, hvor kun en brøkdel af dataene er varm, er det dog mere økonomisk.

Antallet af knuder, der kræves til optimal ydeevne kan skal beregnes på følgende måde:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Kontoen for forøgelse

Du vil beregne antallet af knuder, der er baseret på den DB_Size, som du mod forventning din tjeneste til vokser til, ud over den DB_Size, hvor du startede med. Tilpas derefter antallet af knuder, i takt din tjeneste bliver større, så du ikke uberettiget klargøring antallet af knuder. Men antallet af partitioner skal baseres på antallet af knuder, der skal bruges, når du kører din tjeneste på maksimale forøgelse.

Det er godt at have nogle ekstra maskiner, der er tilgængelige når som helst, så du kan håndtere alle uventede spidser eller manglende håndhævelse (f.eks, hvis der et par FOS gå).  Mens den ekstra kapacitet skal bestemmes ved hjælp af din forventede spidser, er et udgangspunkt til reserver et par ekstra FOS (5-10 procent ekstra).

Den foregående går ud fra en enkelt med høj sikkerhed tjeneste. Hvis du har mere end én med høj sikkerhed tjeneste, er det nødvendigt at tilføje den DB_Size, der er knyttet til andre tjenester til ligningen. Alternativt kan du beregne antallet af knuder separat for hver med høj sikkerhed tjeneste.  Din tjeneste muligvis replikaer eller partitioner, der ikke er afstemmes. Husk på, at partitioner også kan have flere data end andre. Flere oplysninger om partitionering, finde [partitionering artikel om bedste fremgangsmåder](service-fabric-concepts-partitioning.md). Foregående ligningen er dog partition og replika agnostic, da tjenesten strukturen sikrer, at replikaerne er sprede mellem knuderne i en optimeret måde.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Bruge et regneark til omkostningsberegning af

Nu Lad os sætte nogle reelt tal i formlen. Et [eksempel regneark](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) viser, hvordan du planlægger kapaciteten for et program, der indeholder tre typer dataobjekter. Vi en omtrentlig dets størrelse, og hvor mange objekter vi forventer har for hvert objekt. Vi kan også vælge, hvor mange replikaer vi vil af hver objekttype. Beregner den samlede mængde af hukommelse til at blive gemt i klyngen i regnearket.

Vi Indtast en VM størrelse og månedlige pris. Baseret på VM størrelse, regnearket kan du se det mindste antal partitioner, du skal bruge til at opdele dataene til at passe fysisk på noderne. Ønsker du muligvis et større antal partitioner, der skal tage højde for dit program bestemte beregning og netværkstrafik skal. Regnearket viser har øges antallet af partitioner, der administrerer user profil-objekter fra en til seks.

Baseret på alle disse oplysninger, regnearket viser nu, kan du få alle dataene med de ønskede partitioner og replikaer fysisk på en klynge 26 noder. Men denne klynge ville være tæt pakket, så du kan eventuelt nogle yderligere noder til node fejl og opgraderinger. Regnearket viser også, at har mere end 57 noder giver ingen yderligere værdi, fordi du ville have tomme noder. Igen, kan du gå over 57 noder alligevel for at imødekomme node fejl og opgraderinger. Du kan tilpasse regnearket, så de passer til dit program særlige behov.   

![Regneark til omkostningsberegning af][Image1]



## <a name="next-steps"></a>Næste trin

Se [partitionering Service strukturerede services] [ 10] mere at vide om partitionering din tjeneste.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
