<properties
   pageTitle="Justering af din klynge med Azure Service strukturen klynge ressourcestyring | Microsoft Azure"
   description="En introduktion til justering af din klynge med tjenesten strukturen klynge ressourcestyring."
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

# <a name="balancing-your-service-fabric-cluster"></a>Justering af din service-strukturen klynge
Tjenesten strukturen klynge ressourcestyring giver mulighed for rapportering dynamiske belastning, reagerer på ændringer i klyngen, rette overtrædelse af betingelse og skulle genoprette balancen klyngen, hvis det er nødvendigt. Men hvor ofte gør den disse ting, og hvad udløser det? Der findes flere kontrolelementer, der er relateret til dette.

Det første sæt kontrolelementer omkring justering af er et sæt af timerne. Disse timerne styrer, hvor ofte ressourcestyring klynge undersøger tilstanden for klynge i ting, der skal behandles. Der findes tre forskellige kategorier af arbejdet, hver med deres egen tilsvarende timer. De er:

1.  Placering – dette trin vedrører placere en hvilken som helst med høj sikkerhed replikaer eller uden status forekomster, der mangler. Dette omfatter både nye tjenester og håndtering af med høj sikkerhed replikaer eller uden status forekomster, som har mislykkedes og genskabes. Slette og slippe replikaer eller forekomster håndteres også her.
2.  Betingelse kontrollerer – denne fase kontrollerer, om og retter overtrædelse af af forskellige placeringen begrænsninger (regler) i systemet. Eksempler på regler er ting som at sikre, at noder ikke er over kapacitet og, en tjeneste placeringen begrænsninger (flere oplysninger om disse senere) er opfyldt.
3.  Justering af – denne fase kontrollerer, om proaktiv skulle genoprette balancen er nødvendig baseret på det konfigurerede ønskede niveau af saldo for forskellige måleenheder, og hvis det er tilfældet forsøger at finde et arrangement i den-klynge, mere afstemmes.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Konfiguration af klynge ressourcestyring trin og timerne
Hver af disse forskellige typer af Ressourcestyring klynge kan foretage rettelser styres af en anden timer, der styrer dens hyppighed. Så for eksempel, hvis du kun vil håndtere placere ny tjeneste arbejdsmængder i klyngen hver time (til batchen dem op), men vil normalt justering af kontrollerer hvert par sekunder, kan du konfigurere denne funktionsmåde. Når hver timer udløses, opgaven. Som standard ressourcestyring scanner dens tilstand og anvender opdateringer ("samling" alle de ændringer, der er opstået efter den sidste scanning som bemærke, der er en node ned) hver 1/10 af en anden, sæt placeringen og begrænsning Markér flag hver andet, og afstemning markere hvert 5.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

I dag vi kun udføre én af disse handlinger ad gangen, sekventielt (det er grunden vi refererer til disse konfigurationer som "mindste intervaller")). Dette er så, for eksempel vi allerede har svaret på en hvilken som helst ventende anmodninger til at oprette nye replikaer, før vi gå videre til justering af klyngen. Som du kan se efter de standard tidsintervaller, der er angivet, vi kan scanne, og Kontrollér, om noget vi skal gøre meget ofte dvs., sæt ændringer, vi foretager i slutningen af hvert trin er som regel mindre: Vi er ikke at gennemsøge timer over ændringer i klyngen og forsøger at rette dem på én gang, forsøger vi at håndtere ting mere eller mindre, når de foretager, men med nogle samling, når mange ting sker på den samme tid. Dette gør Service-strukturen ressourcen manager meget svarede til ting, der udføres i klyngen.

Mens de fleste af disse opgaver er enkle (Hvis der er begrænsning overtrædelse af fix dem, hvis der er tjenester skal oprettes, oprette dem), ressourcestyring klynge skal også nogle flere oplysninger om at bestemme, om den imbalanced klynge. Som vi har to andre filer konfiguration: *Justering af tærskler* og *Aktivitet tærskler*.

## <a name="balancing-thresholds"></a>Justering af tærskler
En grænseværdi for justering af er det primære kontrolelement for udløsning proaktiv skulle genoprette balancen (Husk, at timeren er lige hvor ofte ressourcestyring klynge skal du se – det betyder ikke noget, der sker). Grænseværdi for justering af definerer, hvordan imbalanced klyngen skal være for en bestemt metrikværdi i rækkefølge for klynge Resource Manager skal du tænke på det imbalanced og justering af udløser.

Justering af belastning tærskler defineres på grundlag per metrisk som en del af klynge definitionen. Yderligere oplysninger om målepunkter Tjek ud [i denne artikel](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Justering af grænsen for en metrikværdi er et forhold. Hvis antallet af belastning på noden mest indlæst divideret med antallet af belastning på noden mindst indlæst overstiger dette nummer, og klik derefter på klynge betragtes som imbalanced og justering af udløses næste gang kontrollerer ressourcestyring klynge (som standard, nogensinde 5 sekunder, som styret ved MinLoadBalancingInterval, vist ovenfor).

![Justering af belastning grænseværdi for eksempel][Image1]

I denne enkle eksempel hver tjeneste forbrug én enhed af nogle metrisk. I eksemplet øverste den største belastning på en node er 5 og minimum er 2. Lad os sige, at den justering af belastning grænseværdi for denne metrikværdi er 3. Derfor i det øverste eksempel på klynge betragtes som afstemmes og ingen justering af skal udløses, når ressourcestyring klynge kontrollerer (da forholdet i klyngen er 5/2 = 2,5 og, som er mindre end den angivne grænseværdi for justering af belastning af 3).

I eksemplet nederst, er den maksimale belastning på en node 10, mens minimale er 2, hvilket resulterer i en forholdet mellem 5. Dette indsætter klyngen over i designet grænseværdi for justering af belastning af 3 for denne metrikværdi. Som et resultat, bliver en global rebalancing Kør planlagte næste gang udløses, justering af belastning timer. Bemærk, at bare fordi en justering af belastning søgning er startet ikke giver mening flyttes – nogle gange klyngen er imbalanced men situationen kan forbedres - men i en situation som denne (mindst som standard) nogle afkrydsningsfeltet Indlæs fordeles næsten helt sikkert til Node3. Bemærk, at da vi ikke bruger en grådige fremgangsmåde nogle Indlæs kan også distribueres til Node2 da, der ville resultere i begrænsning af overordnede forskellene mellem noder, men vi forventer, at størstedelen af afkrydsningsfeltet Indlæs ville flyde til Node3.

![Justering af belastning grænseværdi for eksempel handlinger][Image2]

Bemærk, at få under justering af belastning grænsen målet er ikke en eksplicit – justering af tærskler er bare en *udløser* , der fortæller ressourcestyring klynge, som det skal se ud i klynge for at finde ud af, hvilke forbedringer, det kan gøre, hvis en hvilken som helst.

## <a name="activity-thresholds"></a>Aktivitet tærskler
Selvom noder er relativt imbalanced, er den *samlede* mængde af Indlæs i klyngen nogle gange lav. Det kan være lige på grund af tidspunktet, eller fordi klyngen er nyt og kun få initialiseres fra. I begge tilfælde, kan du ikke vil bruge tid på justering af klyngen, fordi der er faktisk meget lille opnås – du bruger får lige Netværks- og Beregn ressourcer for at flytte ting omkring, uden at foretage en hvilken som helst absolutte forskel. Da vi vil undgå at gøre dette, der er et andet kontrolelement inde i ressourcestyring, kaldet aktivitet tærskler, som gør det muligt at angive nogle absolutte nedre grænse for aktivitet – Hvis ingen node har mindst dette meget Indlæs derefter justering af ikke udløses selvom i grænseværdi for justering af er opfyldt.

Som eksempel Lad os sige, at vi har rapporter med følgende totalerne for forbrug på disse noder. Antag, at vi bevare vores grænseværdi for justering af 3 for denne metrikværdi, men nu vi har også grænseværdi for en aktivitet af 1536 også. I den første tilfælde, mens klyngen er imbalanced per i grænseværdi for justering af opfylder ingen node den mindste grænseværdi for aktivitet, så vi Forlad ting alene. I eksemplet nederst er Node1 måde over grænseværdi aktivitet, så justering af vil blive udført, (da både i grænseværdi for justering af og aktivitet grænsen for en metrikværdi overskrides)

![Aktivitet grænseværdi for eksempel][Image3]

Ligesom justering af tærskler er aktivitet tærskler defineret per-metrik via klynge definitionen:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Bemærk, at justering og aktivitet tærskler begge bundet til metrisk - justering af vil kun udløses, hvis både justering og aktivitet tærskler overskrides for den samme metrikværdi. Dermed, hvis vi overskrider i justering af grænseværdi for hukommelse og aktivitet grænseværdi for CPU'EN, justering af ikke udløser så længe de resterende tærskler (justering af grænseværdi for CPU'EN) og aktivitet grænseværdi for hukommelse ikke overskrides.

## <a name="balancing-services-together"></a>Justering af tjenester sammen
Der er noget, der er interessante at være opmærksom på, om klyngen er imbalanced eller ikke er en klynge hele beslutning, men den vi gøre for at ændre den måde, som flytter individuelle service replikaer og forekomster rundt. Dette giver mening, højre? Hvis hukommelse er stablet på en node, flere replikaer eller forekomster kan bidrage til den, så det kan kræve flytte en med høj sikkerhed replikaer eller uden status forekomster, der bruger den pågældende, imbalanced metriske.

Nogle gange selvom en kunde ringer os op eller en fil har flyttes en brugertilladelse siger, at en tjeneste, der blev ikke imbalanced. Hvordan kan den ske, at en tjeneste får flyttes, selvom alle pågældende tjeneste målepunkter blev afstemmes, skal også perfekt i så fald på tidspunktet for de andre ubalance? Lad os se!

Tage f.eks fire services, onsiteservice1, Service2, Service3 og Service4. Onsiteservice1 rapporter mod måleredskaber Metric1 og Metric2, Service2 mod Metric2 og Mmetric3, Service3 mod Metric3 og Metric4 og Service4 mod nogle metriske Metric99. Sikkert kan du se, hvor vi her. Vi har en kæde! Vi virkelig har ikke fire uafhængige tjenester fra perspektiv af klynge Ressourcestyring, vi har en masse tjenester, der er relateret (onsiteservice1, Service2 og Service3) og et, der er deaktiveret af sig selv.

![Justering af tjenester sammen][Image4]

Så det er muligt, at en ubalance i Metric1 kan forårsage replikaer eller forekomster tilhører Service3 til at flytte rundt. Disse bevægelser er som regel ret begrænset, men kan være større afhængigt af præcis hvordan imbalanced Metric1 har, og hvilke ændringer var nødvendige i klyngen for at rette den. Vi kan også sige med sikkerhed, ubalance i målepunkter 1, 2 eller 3 medfører aldrig bevægelser i Service4 – der ville være ingen punkt siden flytte replikaerne eller forekomster tilhører Service4 omkring kan ikke gør absolut noget for at påvirke saldoen på målepunkter 1, 2 eller 3.

Ressourcestyring klynge tal automatisk ud af, hvilke tjenester der vedrører, da tjenester kan være blevet tilføjet, fjernes, eller havde deres metriske konfigurationsændring – for eksempel, mellem to gentagne justering af Service2 muligvis er blevet ændret for at fjerne Metric2. Dette fjerner kæden mellem onsiteservice1 og Service2. Nu i stedet for to grupper af tjenester har du tre:

![Justering af tjenester sammen][Image5]

## <a name="next-steps"></a>Næste trin
- Målepunkter er, hvordan Service-strukturen klynge Resource Manager administrerer forbrug og kapacitet i klyngen. Hvis du vil vide Læs mere om dem og hvordan du konfigurerer dem [denne artikel](service-fabric-cluster-resource-manager-metrics.md)
- Bevægelse omkostninger er én måde-signal til klynge Ressourcestyring, bestemte tjenester, der er mere omfattende til at flytte end andre. Hvis du vil vide mere om bevægelse omkostninger skal referere til [denne artikel](service-fabric-cluster-resource-manager-movement-cost.md)
- Ressourcestyring klynge har flere throttles, som du kan konfigurere for at sænke hastigheden transportspand i klyngen. De ikke er normalt er nødvendig, men hvis du har brug for dem kan du læse om dem [her](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
