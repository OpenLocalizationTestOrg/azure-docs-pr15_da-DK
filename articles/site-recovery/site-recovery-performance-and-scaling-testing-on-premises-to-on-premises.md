<properties
    pageTitle="Test af ydeevne og skalering resultaterne lokalt til lokale Hyper-V replikering med gendannelse af websteder | Microsoft Azure"
    description="I denne artikel indeholder oplysninger om performance-test for lokalt på lokale replikering ved hjælp af Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Ydeevne teste og skalere resultater for lokalt på lokale Hyper-V replikering med gendannelse af websteder

Du kan bruge Microsoft Azure gendannelse af websteder til dirigerer og administrere gentagelse af virtuelle maskiner og fysiske servere til Azure eller til en sekundær datacenter. Denne artikel indeholder resultatet af test af ydeevne vi gjorde, da replikering Hyper-V virtuelle maskiner mellem to lokale datacentre.



## <a name="overview"></a>Oversigt

Formålet med test kunne undersøge, hvordan udfører Azure gendannelse af websteder under stabil replikering. Stabil gentagelse opstår, når virtuelle maskiner er fuldført indledende gentagelse og synkroniserer delta ændringer. Det er vigtigt at måle ydeevne ved hjælp af stationær tilstand, fordi det er den tilstand, hvor de fleste virtuelle maskiner forbliver medmindre uventede udfald forekomme.


Test installationen bestod af to lokale websteder med en VMM server i hvert område. Denne test installation er typisk af en hoved office/gren office-installation med hovedkontor, som det primære websted og afdelingen som webstedet sekundær eller gendannelse.

### <a name="what-we-did"></a>Vi har

Her er, hvad vi gik i testen igennem:

1. Oprettet virtuelle maskiner ved hjælp af VMM skabeloner.

1. Introduktion virtuelle maskiner og capture oprindelige ydeevne målepunkter over 12 timer.

1. Oprettet skyer på primær og gendannelsesfiler VMM-servere.

1. Konfigureret skyen beskyttelse i Azure gendannelse af websteder, herunder tilknytning af kilde- og gendannelsesfiler skyer.

1. Aktiveret beskyttelse af virtuelle maskiner og give dem tilladelse til fuldført indledende gentagelse.

1. Ventet et par timer for systemet konstante.

1. Hentes ydeevne målepunkter over 12 timer at sikre, at alle virtuelle maskiner forblev i forventede gentagelse tilstand for disse 12 timer.

1. Mål delta mellem den oprindelige ydeevne målepunkter og gentagelse ydeevne målepunkter.

## <a name="test-deployment-results"></a>Teste resultater for installation

### <a name="primary-server-performance"></a>Primære serverens ydeevne

- Hyper-V replika sporer asynkront ændringer på en logfil med mindste lagerplads omkostninger på den primære server.

- Hyper-V replika anvender præsentation bevares hukommelsescache for at minimere IOP'ER indirekte til sporingsformål. Gemmer skriver til VHDX i hukommelsen og tømmer dem i logfilen før det tidspunkt, hvor loggen sendes til webstedet for gendannelse. En disk, der er justeret sker også, hvis skriver ramme en foruddefineret grænse.

- Diagrammet nedenfor viser stabil IOP'ER spild for gentagelse. Vi kan se, IOP'ER spild på grund af replikering er omkring 5%, hvilket er helt lav.

![Primære resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V replika anvender hukommelse på primær serveren for at optimere disk ydeevne. Som vist i følgende graf, er hukommelse spild på alle servere i den primære klynge marginale. Den faste vises hukommelse er procentdelen af hukommelse bruges af replikering sammenlignet med den samlede installerede hukommelse på Hyper-V-serveren.

![Primære resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V replika har minimum CPU-spild. Som vist i diagrammet, er gentagelse omkostninger i området af 2-3%.

![Primære resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Sekundær (gendannelse) serverens ydeevne

Hyper-V replika bruger en lille mængde hukommelse på serveren gendannelse til at optimere antallet af lagerplads handlinger. Grafen opsummerer hukommelsesforbrug på serveren gendannelse. Den faste vises hukommelse er procentdelen af hukommelse bruges af replikering sammenlignet med den samlede installerede hukommelse på Hyper-V-serveren.

![Sekundær resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

Mængden i/o-handlinger på webstedet gendannelse er en funktion af antallet skrivehandlinger på det primære websted. Lad os se på de samlede i/o-handlinger på webstedet gendannelse forhold til de samlede i/o-handlinger og skrive handlinger på det primære websted. Graferne vise, at samlet IOP'ER på webstedet gendannelse

- Omkring 1,5 gange skrivningen IOP'ER på primært.

- Omkring 37% af det samlede IOP'ER på det primære websted.

![Sekundær resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Sekundær resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Effekten af replikering på udnyttelse af netværk

Et gennemsnit af 275 MB sekundet netværksbåndbredde, der blev brugt mellem noderne primær og gendannelse (med komprimering aktiveret) mod en eksisterende båndbredde på 5 GB sekundet.

![Udnyttelse af resultater netværk](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Effekten af replikering på virtuelt ydeevne

En vigtig overvejelse er påvirkningen af replikering på fremstilling arbejdsbelastninger, som kører på de virtuelle computere. Hvis det primære websted er tilstrækkeligt klargjort for gentagelse, ikke bør der være en hvilken som helst virkning på arbejdsmængder. Hyper-V replikas lightweight sporing ordning sikrer, at arbejdsbelastninger, som kører på de virtuelle computere ikke påvirkes under stationær tilstand replikering. Dette er vist i følgende grafer.

Dette diagram viser IOP'ER udføres ved virtuelle maskiner, der kører forskellige arbejdsbelastninger før og efter replikering er aktiveret. Du kan se, at der er ingen forskel mellem to.

![Replika effekt resultater](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

Følgende graf viser overførsel af virtuelle maskiner, der kører forskellige arbejdsbelastninger før og efter replikering er aktiveret. Du kan se, at replikering ikke har nogen betydeligt virkning.

![Resultater replika effekter](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Konklusion

Resultaterne viser klart, at Azure gendannelse af websteder, kombineret med Hyper-V replika skalere fint med minimum arbejdsbyrde for en stor klynge.  Azure gendannelse af websteder indeholder enkel implementering, gentagelse, administration og overvågning. Hyper-V replika indeholder den nødvendige infrastruktur til vellykket gentagelse skalering. Vi anbefaler, at du du hente [Hyper-V replika kapacitet teamplanlægning](https://www.microsoft.com/download/details.aspx?id=39057)til planlægning af en optimal installation.

## <a name="test-environment-details"></a>Test miljø detaljer

### <a name="primary-site"></a>Primære websted

- Det primære websted har en klynge, der indeholder fem Hyper-V-servere, der kører 470 virtuelle computere.

- Virtuelle maskiner Kør forskellige arbejdsbelastninger, og alle har Azure gendannelse af websteder beskyttelse er aktiveret.

- Lagerplads på Klyngenoden leveres af en iSCSI SAN. Model – Hitachi HUS130.

- Hver klyngeserver har fire netværkskort (NIC) på én Gbps.

- To over netværkskort er forbundet til en privat iSCSI-netværk og to har forbindelse til en ekstern virksomhedens netværk. En af de eksterne netværk er reserveret til klynge kommunikation kun.

![Primære hardwarekrav](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Server|RAM|Model|Processor|Antallet af processorer|NIC|Software|
|---|---|---|---|---|---|---|
|Hyper-V-servere i klynge: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 har 256|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz|4|Jeg Gbps x 4|Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen|
|VMM Server|2|||2|1 Gbps|Windows Server-Database 2012 R2 (x 64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Sekundær (gendannelse)-websted

- Sekundær webstedet har en seks-node sekundær klynge.

- Lagerplads på Klyngenoden leveres af en iSCSI SAN. Model – Hitachi HUS130.

![Specifikation af primære hardware](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Server|RAM|Model|Processor|Antallet af processorer|NIC|Software|
|---|---|---|---|---|---|---|
|Hyper-V-servere i klynge: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|Intel(R) Xeon(R) CPU E5-2630 0 @ 2,30 GHz|2|Jeg Gbps x 4|Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz|4||Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz|2||Windows Server Datacenter 2012 R2 (x64) + Hyper-V-rollen|
|VMM Server|2|||2|1 Gbps|Windows Server-Database 2012 R2 (x 64) + VMM 2012 R2|

### <a name="server-workloads"></a>Server arbejdsmængder

- Vi valgte arbejdsbelastninger, som ofte anvendte i enterprise-kundescenarier til testformål.

- Vi bruger [IOMeter](http://www.iometer.org) med den arbejdsbelastningen egenskab, der er opsummeret i tabellen for simulering.

- Alle IOMeter profiler er indstillet til at skrive tilfældige byte til at simulere værst tænkelige skrive mønstre for arbejdsmængder.

|Arbejdsbelastning|I/o-størrelse (KB)|% Access|% Læst|Udestående i/O'er|I/o-mønster|
|---|---|---|---|---|---|
|Filserver|48163264|60% 20 %5 %5% 10%|80% 80% 80% 80% 80%|88888|Alle 100% tilfældigt|
|SQL Server (lydstyrken 1) SQL Server (volume 2)|864|100% 100%|70 %0%|88|100% random100% sekventielle|
|Exchange|32|100%|67%|8|100% tilfældigt|
|Arbejdsstation/VDI|464|66% 34%|95 70%|11|Begge tilfældige 100%|
|Web filserver|4864|33% 34% 33%|95% 95% 95%|888|Alle 75% tilfældigt|

### <a name="virtual-machine-configuration"></a>Virtuelt konfiguration

- 470 virtuelle maskiner på den primære klynge.

- Alle virtuelle maskiner VHDX disk med.

- Virtuelle maskiner, der kører arbejdsbelastninger, som opsummeret i tabellen. Alle blev oprettet med VMM skabeloner.

|Arbejdsbelastning|# FOS|Mindste RAM (GB)|Maksimale RAM (GB)|Logisk diskstørrelse (GB) per VM|Maksimale IOP'ER|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchangeserver|71|1|4|552|10|
|Filserver|50|1|2|552|22|
|VDI|149|.5|1|80|6|
|Webserver|149|.5|1|80|6|
|TOTAL|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Indstillinger for Azure gendannelse af websteder

- Azure gendannelse af websteder er konfigureret til lokalt på lokale beskyttelse

- VMM server har fire skyer, der er konfigureret, der indeholder de Hyper-V klyngeservere og deres virtuelle computere.

|Primære VMM skyen|Beskyttet virtuelle maskiner i skyen|Replikering frekvens|Yderligere betroet punkter|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minutter|Ingen|
|PrimaryCloudRpo30s|47|30 sekunder|Ingen|
|PrimaryCloudRpo30sArp1|47|30 sekunder|1|
|PrimaryCloudRpo5m|235|5 minutter|Ingen|

### <a name="performance-metrics"></a>Ydeevnen målepunkter

I tabel opsummeres ydeevne målepunkter og tællere, der blev målt i installationen.

|Metrisk|Tæller|
|---|---|
|CPU|\Processor(_Total)\% processortid|
|Tilgængelig hukommelse|\Memory\Available MB|
|IOP'ER|\PhysicalDisk (_Total) \Disk overførsler/sec|
|VM læste (IOP'ER) handlinger/sekund|\Hyper-V virtuelle lagerplads enhed (<VHD>) \Read handlinger/Sec|
|VM skrevne (IOP'ER) handlinger/sekund|\Hyper-V virtuelle lagerplads enhed (<VHD>) \Write handlinger/S|
|VM læse overførselshastighed|\Hyper-V virtuelle lagerplads enhed (<VHD>) \Read byte/sekund|
|VM Skriv overførselshastighed|\Hyper-V virtuelle lagerplads enhed (<VHD>) \Write byte/sekund|


## <a name="next-steps"></a>Næste trin

- [Konfigurere beskyttelse mellem to lokale VMM steder](site-recovery-vmm-to-vmm.md)
