<!--author=alkohli last changed: 12/15/15-->

| Grænse-id | Grænse | Kommentarer |
|----------------- | ------|--------- |
| Maksimale antal lagerplads kontolegitimationsoplysninger | 64 | |
| Maksimale antal lydstyrken objektbeholdere | 64 | |
| Maksimale antal enheder | 255 | |
| Maksimale antal tidsplaner per båndbredde skabelon | 168 | En tidsplan for hver time, hver dag i ugen (24 * 7). |
| Maksimumstørrelsen for en lagdelte lydstyrken på fysiske enheder | 64 TB for 8100 og 8600 | 8100 og 8600 er fysiske enheder. |
| Maksimumstørrelsen for en lagdelte lydstyrken på virtuelle enheder i Azure | 30 TB for 8010 <br></br> 64 TB for 8020 | 8010 og 8020 er virtuelle enheder i Azure, der bruger Standard lager og Premium lagerplads henholdsvis. |
| Maksimale størrelse på et lokalt fastgjorte lydstyrken på fysiske enheder | 9 TB for 8100 <br></br> 24 TB for 8600 | 8100 og 8600 er fysiske enheder. |
| Maksimale antal iSCSI-forbindelser | 512 | |
| Maksimale antal iSCSI-forbindelser fra initiatorer | 512 | |
| Maksimale antal access kontrolelement poster per enhed | 64 | |
| Maksimale antal enheder politik for sikkerhedskopiering | 24 | |
| Maksimale antal sikkerhedskopier bevares politik for sikkerhedskopiering | 64 | |
| Maksimale antal tidsplaner politik for sikkerhedskopiering | 10 | |
| Maksimale antal snapshot af enhver type, der kan blive bevaret per lydstyrken | 256 | Dette omfatter lokale snapshots og snapshots i skyen. |
| Maksimale antal snapshots, som kan være til stede i en hvilken som helst enhed | 10.000 | |
| Maksimale antal enheder, der kan behandles parallelt til sikkerhedskopiering og gendannelse eller klone | 16 |<ul><li>Hvis der er mere end 16 enheder, behandles de sekventielt, efterhånden som behandling pladser bliver tilgængelige.</li><li>Kan ikke opstå nye sikkerhedskopier af en klonet eller en gendannede lagdelte lydstyrken, indtil handlingen er færdig. For en lokal enhed, er sikkerhedskopier tilladt, efter at lydstyrken er online.</li></ul>|
| Gendan og Klon gendanne tid for lagdelte enheder | < 2 minutter | <ul><li>Lydstyrken er gjort tilgængelige i løbet af 2 minutter Gendan eller Klon operation, uanset størrelsen.</li><li>Lydstyrken ydeevnen muligvis først langsommere end normalt, som de fleste data og metadata stadig er placeret i skyen. Ydeevnen kan øge som dataflow fra skyen til StorSimple enhed.</li><li>Samlet tid på at hente metadata, afhænger af tildelte enhedens størrelse. Metadata er automatisk hentet ind i enheden i baggrunden til en sats på 5 minutter per TB tildelte lydstyrken data. Denne sats kan være påvirket af internetbåndbredde til skyen.</li><li>Den eller Klon gendannelse er fuldført, når alle metadata, der er på enheden.</li><li>Sikkerhedskopiering handlinger kan ikke udføres indtil gendannelsen eller Klon handlingen er fuldt gennemført.|
| Gendanne Gendan tid for lokalt fastgjorte enheder | < 2 minutter | <ul><li>Lydstyrken er gjort tilgængelige i løbet af 2 minutter gendannelsen, uanset størrelsen.</li><li>Lydstyrken ydeevnen muligvis først langsommere end normalt, som de fleste data og metadata stadig er placeret i skyen. Ydeevnen kan øge som dataflow fra skyen til StorSimple enhed.</li><li>Samlet tid på at hente metadata, afhænger af tildelte enhedens størrelse. Metadata er automatisk hentet ind i enheden i baggrunden til en sats på 5 minutter per TB tildelte lydstyrken data. Denne sats kan være påvirket af internetbåndbredde til skyen.</li><li>I modsætning til lagdelte enheder, hvis det er lokalt fastgjorte enheder, hentes lydstyrken dataene også lokalt på enheden. Gendannelsen er fuldført, når alle lydstyrken dataene er indført på enheden.</li><li>Gendan handlinger kan være lang, og den samlede tid at fuldføre gendannelsen, afhænger af størrelsen på klargjort lokale lydstyrken, din internetbåndbredde og de eksisterende data på enheden. Sikkerhedskopiering handlinger på lokalt fastgjorte lydstyrken er tilladt, mens gendannelsen er i gang.|
| Tynd Gendan tilgængelighed | Sidste failover | |
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra SSD niveauet) * | 920/720 MB/s med en enkelt 10GbE netværket grænseflade | Op til 2 x med MPIO og to netværksgrænseflader. |
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra harddisk niveauet) * | 120/250 MB/s |
| Maksimale klient læse-og skriveadgang overførselshastighed (når served fra skyen niveau) * | 11/41 MB/s | Læs overførselshastighed afhænger af klienter opretter og vedligeholde tilstrækkelige i/o-kø dybde. |

& #42 Maksimale antal gennemløb per i/o-type blev målt med 100 procent læse- og 100 procent Skriv scenarier. Faktisk overførselshastighed kan være lavere og afhænger af I/O blande og netværk betingelser.
