<properties
 pageTitle="Størrelser til skytjenester | Microsoft Azure"
 description="Viser de forskellige virtuelt størrelser (og -id'er) til Azure skyen service internettet og arbejder roller."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Størrelser for Cloud Services

Dette emne beskrives de tilgængelige størrelser og indstillinger for skytjeneste rolle forekomster (web roller og arbejder roller). Den indeholder også overvejelser om installation af for at være opmærksom på, når du planlægger at bruge disse ressourcer. Hver størrelse har et ID, som du vil lægge i [tjenesten definitionsfil](cloud-services-model-and-package.md#csdef).

Cloud Services er et af flere typer Beregn ressourcer, som Azure. Klik [her](cloud-services-choose-me.md) for at få mere at vide om Cloud Services.

> [AZURE.NOTE]For at få vist relaterede Azure begrænsninger, se [Azure abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Størrelser til internettet og arbejder rolle forekomster

Der findes flere standardstørrelser til at vælge mellem på Azure. Overvejelser i forbindelse med nogle af disse størrelser, omfatter:

* D-serien FOS er designet til at køre programmer, der har behov for højere Beregn power og midlertidige disk ydeevne. D-serien FOS giver hurtigere processorer, en højere hukommelse-til-core-bredde-forhold og en solid state drev (SSD) for den midlertidige disk. Få mere at vide ved at se meddelelsen på Azure-blog [Nye D-serien virtuelt størrelser](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Dv2-serien, en opfølgning til den oprindelige D-serie, omfatter en mere effektiv CPU. Dv2 serie CPU'EN er om 35% hurtigere end CPU'EN D-serien. Det er baseret på den seneste generering af 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor, og med Intel Turbo Boost teknologi 2.0, kan gå op til 3.1 GHz. Dv2-serien har de samme hukommelse og diskplads konfigurationer som D-serien.

*   G-serien FOS tilbyder mest hukommelse og køre på hosts med Intel Xeon E5 V3 family processorer.

*   A-serien FOS kan installeres på en række forskellige hardwaretyper af og processorer. Størrelsen er begrænset, baseret på hardwaren, til at tilbyde ensartede processor ydeevnen til uanset den hardware, den er installeret på forekomsten, der kører. For at bestemme den fysiske hardware, som denne størrelse er installeret, resultat indeksforespørgslen den virtuelle hardware fra inden for den virtuelle maskine.

*   A0 størrelse er uberettiget abonnerer på på den fysiske hardware. Andre kunde installationer kan påvirke ydeevnen for den, der kørende arbejdsbyrde for denne specifikke størrelse. Den relative ydeevne fremhæves under som de forventede oprindelige underlagt en tilnærmet afvigelse 15%.


Størrelsen af den virtuelle maskine påvirker de priser. Størrelsen påvirker også behandling, hukommelse og lagerplads kapaciteten på den virtuelle maskine. Lagerplads omkostninger beregnes separat ud fra anvendte sider i kontoen lagerplads. Yderligere oplysninger finder du [Virtuelle maskiner priser detaljer](https://azure.microsoft.com/pricing/details/virtual-machines/) og [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage/). 


Overvej følgende kan hjælpe dig med at beslutte en størrelse:


* A8 A11 og H-serien størrelserne er også kendt som *Beregn-intensivt forekomster*. Den hardware, der kører disse størrelser er designet og optimeret til processorkrævende og network-intensivt programmer, herunder high-performance computing (HPC) klynge programmer, modellering og simulering. A8 A11 serien bruger Intel Xeon E5-2670 @ 2,6 GHZ og H-serien bruger Intel Xeon E5-2667 v3 @ 3,2 GHz. Se [om H-serien og Beregn-intensivt A-serien FOS](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)have yderligere oplysninger og overvejelser om brug af disse størrelser. 

* Dv2 serie, D-serien, G-serien, er ideel til programmer, der kræver hurtigere CPU'er, bedre lokale disk ydeevnen eller har højere hukommelse krav.  De tilbyder en effektiv kombination for mange professionel og-programmer.

*   Nogle af de fysiske værter i Azure datacentre understøtter muligvis ikke virtuelt større, såsom A5 – A11. Som et resultat, kan du få fejlmeddelelsen **blev ikke konfigurere virtuelt {computernavn}** eller **kunne ikke oprettes virtuelt {computernavn}** når ændring af størrelsen på en eksisterende virtuelt til en ny størrelse. oprette en ny virtuel maskine i et virtuelt netværk, der er oprettet før 16 April 2013; eller tilføje en ny virtuel maskine til en eksisterende skybaseret tjeneste. Se [fejl: "Kunne ikke konfigureres virtuelt"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) på support-forummet for løsninger for hvert installationsscenario.  

* Dit abonnement kan også begrænse antallet kerner, du kan installere i visse størrelse familier. Du kan øge en kvote ved at kontakte Azure Support.


## <a name="performance-considerations"></a>Overvejelser

Vi har oprettet konceptet af Azure beregne enhed (ACU) til at levere en metode til at sammenligne Beregn (CPU) ydeevne på tværs af Azure lagerenheder. Dette kan du nemt identificere, hvilke SKU er mest sandsynlige at opfylde dine behov for ydeevnen.  ACU er i øjeblikket standardiseret på en lille (Standard_A1) VM der 100 og alle andre SKU'er derefter repræsenterer cirka hvor meget hurtigere, SKU kan køre en standard benchmark. 

>[AZURE.IMPORTANT] ACU er kun en vejledning.  Resultater for din arbejdsbyrde kan variere. 

<br>

|SKU familie |ACU/Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs er markeret med en * bruger Intel® Turbo teknologi til at øge CPU frekvens og give en større ydeevne.  Mængden boost kan variere, afhængigt af VM størrelse, arbejdsbelastningen og andre arbejdsbelastninger, som kører på den samme vært.

## <a name="size-tables"></a>Størrelse tabeller

De følgende tabeller viser størrelsen og kapaciteten de giver.

* Lagerkapacitet vises i enheder af GiB eller 1024 ^ 3 byte. Når sammenligning diske målt i GB (1000 ^ 3 byte) til diske målt i GiB (1024 ^ 3) huske, at kapacitet tal, der er angivet i GiB kan vises mindre. For eksempel 1023 GiB = 1098.4 GB

* Disken overførselshastighed måles i input/output-operationer sekundet (IOP'ER) og MBps hvor MBps = 10 ^ 6 byte/sec.

* Datadisce kan fungere i cachelagrede eller ikke er cachelagrede funktionsmåder. Host cache-tilstand er angivet til **skrivebeskyttet** eller **ReadWrite**for cachelagrede data diskhandling.  Host cache-tilstand er ikke er cachelagrede data diskhandling indstillet til **ingen**.

* Maksimale netværksbåndbredde er den maksimale aggregerede båndbredde allokeres og tildelt per VM type. Den maksimale båndbredde giver vejledning til at vælge den rigtige type VM til at sikre tilstrækkelig netværkskapacitet er tilgængelig. Når du flytter mellem lav, moderat, høj og meget høj, øger overførselshastigheden i overensstemmelse hermed. Faktisk netværksydeevne afhænger af mange faktorer, herunder Netværks- og belastning på programmer og programindstillinger netværk.


## <a name="a-series"></a>En serie

| Størrelse        | CPU-kerner | Hukommelse: GiB | Lokal harddisk: GiB | Maks datadisce | Maks dataoverførsel disk: IOP'ER | Maks netværkskort / netværksbåndbredde |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / lav                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderat              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderat              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / høj                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / høj                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderat              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / høj                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / høj                  |

## <a name="a-series---compute-intensive-instances"></a>A-serie-Beregn-intensivt forekomster

Se [om H-serien og Beregn intensivt A-serie FOS](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)oplysninger og overvejelser om brug af disse størrelser.


| Størrelse         | CPU-kerner | Hukommelse: GiB | Lokal harddisk: GiB | Maks datadisce | Maks dataoverførsel disk: IOP'ER | Maks netværkskort / netværksbåndbredde |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / høj                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / meget høj             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / høj                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / meget høj             |

* RDMA kan

## <a name="d-series"></a>D-serie


| Størrelse         | CPU-kerner | Hukommelse: GiB | Lokale SSD: GiB | Maks datadisce | Maks dataoverførsel disk: IOP'ER | Maks netværkskort / netværksbåndbredde |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderat              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / høj                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / høj                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / høj                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / høj                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / høj                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / høj                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / meget høj             |

## <a name="dv2-series"></a>Dv2 serie

| Størrelse            | CPU-kerner | Hukommelse: GiB | Lokale SSD: GiB | Maks datadisce | Maks dataoverførsel disk: IOP'ER | Maks netværkskort / netværksbåndbredde |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderat              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / høj                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / høj                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / høj                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / meget høj        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / høj                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / høj                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / høj                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / meget høj        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / meget høj        |

## <a name="g-series"></a>G-serie

| Størrelse        | CPU-kerner | Hukommelse: GiB  | Lokale SSD: GiB  | Maks datadisce | Maks disk overførselshastighed: IOP'ER | Maks netværkskort / netværksbåndbredde |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / høj                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / høj                  |
| Standard_G3 | 8         | 112          | 1,536                | 16             | 16 x 500           | 4 / meget høj             |
| Standard_G4 | 16        | 224          | 3,072                | 32             | 32 x 500           | 8 / meget høj        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / meget høj        |


## <a name="h-series"></a>H serie

Azure H serie virtuelle maskiner er det næste høj generering af-performance computing FOS med henblik på aktuelle behov for høj slutningen, som opbygning af molekylemodeller og computational flydende dynamics. Disse 8 og 16 core FOS er bygget på Intel Haswell E5 2667 V3 processorteknologi med DDR4 hukommelse og lokale SSD baseret lager. 

Ud over i væsentlig CPU-potens giver H-serien forskellige muligheder for kort ventetid RDMA netværk ved hjælp af FDR InfiniBand og flere hukommelseskonfigurationer til at understøtte hukommelse intensivt computational krav.


| Størrelse           | CPU-kerner | Hukommelse: GiB | Lokale SSD: GiB | Maks datadisce | Maks disk overførselshastighed: IOP'ER | Maks netværkskort / netværksbåndbredde |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / høj                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / meget høj                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / høj                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / meget høj                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / meget høj                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / meget høj                  |


* RDMA kan

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Bemærkninger: Standard A0 – A4 ved hjælp af CLI og PowerShell 

Nogle VM størrelsen af navnene er en smule anderledes i CLI og PowerShell i modellen Klassisk installation:

* Standard_A0 er ExtraSmall 
* Standard_A1 er små
* Standard_A2 er mellem
* Standard_A3 er stor
* Standard_A4 er ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Konfigurere størrelser til Cloud Services

Du kan angive virtuelt størrelsen på en forekomst af rolle som en del af tjenesten modellen beskrevet ved [service definitionsfil](cloud-services-model-and-package.md#csdef). Størrelsen af rollen bestemmer antallet af CPU-kerner, hukommelseskapaciteten og lokale system filstørrelsen, der er allokeret til en aktiv forekomst. Vælg den rolle størrelse, der er baseret på dit program ressourcekrav.

Her er et eksempel for at indstille den rolle størrelse skal være [Standard_D2](#general-purpose-d) for en forekomst af Web rolle:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [azure abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](../azure-subscription-service-limits.md).
- Lær mere [om H-serien og Beregn-intensivt A-serien FOS](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) for arbejdsmængder som High-performance Computing (HPC).

