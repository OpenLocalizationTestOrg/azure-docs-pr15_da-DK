<properties 
   pageTitle="Begrænsninger for StorSimple system | Microsoft Azure"
   description="I denne artikel beskrives begrænsninger for systemet og anbefalede størrelser for StorSimple 8000 serie komponenter og forbindelser."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="storsimple-system-limits"></a>StorSimple systembegrænsninger

## <a name="overview"></a>Oversigt

StorSimple giver SVG og fleksibel lagerplads til dit datacenter. Der er dog nogle begrænsninger, som du skal huske på, når du planlægge, installere og betjene din StorSimple løsning. Den følgende tabel beskrives disse begrænsninger og indeholder nogle anbefalinger, så du får mest muligt ud af din StorSimple løsning.

| Grænse-id | Grænse | Kommentarer |
|----------------- | ------|--------- |
| Maksimale antal lagerplads kontolegitimationsoplysninger | 64 | |
| Maksimale antal lydstyrken objektbeholdere | 64 | |
| Maksimale antal enheder | 255 | |
| Maksimale antal lokalt fastgjorte enheder | 32 | |
| Maksimale antal tidsplaner per båndbredde skabelon | 168 | En tidsplan for hver time, hver dag i ugen (24 * 7). |
| Maksimumstørrelsen for en lagdelte lydstyrken på fysiske enheder | 64 TB for 8100 og 8600 | 8100 og 8600 er fysiske enheder. |
| Maksimumstørrelsen for en lagdelte lydstyrken på virtuelle enheder i Azure | 30 TB for 8010 <br></br> 64 TB for 8020 | 8010 og 8020 er virtuelle enheder i Azure, der bruger Standard lager og Premium lagerplads henholdsvis. |
| Maksimale størrelse på et lokalt fastgjorte lydstyrken på fysiske enheder | 8.5 TB for 8100 <br></br> 22,5 TB for 8600 | 8100 og 8600 er fysiske enheder. |
| Maksimale antal iSCSI-forbindelser | 512 | |
| Maksimale antal iSCSI-forbindelser fra initiatorer | 512 | |
| Maksimale antal access kontrolelement poster per enhed | 64 | |
| Maksimale antal enheder politik for sikkerhedskopiering | 24 | |
| Maksimale antal sikkerhedskopier opbevares efter tidsplan (i en ekstra politik) | 64 | |
| Maksimale antal tidsplaner politik for sikkerhedskopiering | 10 | |
| Maksimale antal snapshots af enhver type, der kan blive bevaret per lydstyrken | 256 | Dette nummer indeholder lokale snapshots og snapshots i skyen. |
| Maksimale antal snapshots, som kan være til stede i en hvilken som helst enhed | 10.000 | |
| Maksimale antal enheder, der kan behandles parallelt til sikkerhedskopiering og gendannelse eller klone | 16 |<ul><li>Hvis der er mere end 16 enheder, behandles de sekventielt som behandling pladser være tilgængelige.</li><li>Kan ikke opstå nye sikkerhedskopier af en klonet eller en gendannede lagdelte lydstyrken, indtil handlingen er færdig. For en lokal enhed, er sikkerhedskopier tilladt, efter at lydstyrken er online.</li></ul>|
| Gendan og Klon Gendan tid for lagdelte enheder | < 2 minutter | <ul><li>Lydstyrken er gjort tilgængelige i løbet af 2 minutter Gendan eller Klon operation, uanset størrelsen.</li><li>Lydstyrken ydeevnen muligvis først langsommere end normalt, som de fleste data og metadata stadig er placeret i skyen. Ydeevnen kan øge som dataflow fra skyen til StorSimple enhed.</li><li>Samlet tid på at hente metadata, afhænger af tildelte enhedens størrelse. Metadata er automatisk hentet ind i enheden i baggrunden til en sats på 5 minutter per TB tildelte lydstyrken data. Denne sats kan være påvirket af internetbåndbredde til skyen.</li><li>Den eller Klon gendannelse er fuldført, når alle metadata, der er på enheden.</li><li>Sikkerhedskopiering handlinger kan ikke udføres indtil gendannelsen eller Klon handlingen er fuldt gennemført.|
| Gendanne Gendan tid for lokalt fastgjorte enheder | < 2 minutter | <ul><li>Lydstyrken er gjort tilgængelige i løbet af 2 minutter gendannelsen, uanset størrelsen.</li><li>Lydstyrken ydeevnen muligvis først langsommere end normalt, som de fleste data og metadata stadig er placeret i skyen. Ydeevnen kan øge som dataflow fra skyen til StorSimple enhed.</li><li>Samlet tid på at hente metadata, afhænger af tildelte enhedens størrelse. Metadata er automatisk hentet ind i enheden i baggrunden til en sats på 5 minutter per TB tildelte lydstyrken data. Denne sats kan være påvirket af internetbåndbredde til skyen.</li><li>I modsætning til lagdelte enheder for lokalt fastgjorte enheder hentes lydstyrken dataene også lokalt på enheden. Gendannelsen er fuldført, når alle lydstyrken dataene er indført på enheden.</li><li>Handlingerne Gendan muligvis langt. Den samlede tid at fuldføre gendannelsen afhænger af størrelsen på klargjort lokale lydstyrken, din internetbåndbredde og de eksisterende data på enheden. Sikkerhedskopiering handlinger på lokalt fastgjorte lydstyrken er tilladt, mens gendannelsen er i gang.|
|Skyen snapshots behandling DISKONTO| 15 minutter/TB | <ul><li>Mindste tid til at foretage skyen snapshot er du klar til at uploade per TB tildelte lydstyrken data i sikkerhedskopi. </li><li> Samlet skyen øjebliksbillede tid beregnes ved at tilføje denne gang øjebliksbillede Overfør tid, der påvirkes af internetbåndbredde til skyen.
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra SSD niveauet) * | 920/720 MB/s med en enkelt 10 GbE netværksgrænseflade | Op til 2 x med MPIO og to netværksgrænseflader. |
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra harddisk niveauet) * | 120/250 MB/s |
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra skyen niveau)* for opdatering 3 og nyere** | 40/60 MB/s til lagdelt enheder<br><br>60/80 MB/s til lagdelt enheder med arkivering indstillingen er valgt under oprettelse af lydstyrken | Læs overførselshastighed afhænger af klienter opretter og vedligeholde tilstrækkelige i/o-kø dybde. <br><br>Hastighed opnået afhænger af hastigheden for den underliggende lagerplads konto, der bruges. | 

& #42 Maksimale antal gennemløb per i/o-type blev målt med 100 procent læse- og 100 procent Skriv scenarier. Faktisk overførselshastighed kan være lavere og afhænger af I/O blande og netværk betingelser.

& #42 & #42; Ydeevnen tal før Update 3 måske mindre.

## <a name="next-steps"></a>Næste trin

Gennemse [StorSimple systemkrav](storsimple-system-requirements.md). 
