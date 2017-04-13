<properties 
   pageTitle="StorSimple lokalt fastgjort enheder ofte stillede spørgsmål om | Microsoft Azure"
   description="Indeholder svar på ofte stillede spørgsmål om StorSimple lokalt fastgjort enheder."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokalt fastgjort enheder: ofte stillede spørgsmål (FAQ)

## <a name="overview"></a>Oversigt

Følgende er spørgsmål og svar, som du muligvis, når du opretter et StorSimple fastgjort lokalt drev, en lagdelte konverteres til et lokalt fastgjorte lydstyrken (og omvendt), eller sikkerhedskopiere og gendanne et lokalt fastgjorte lydstyrken.

Spørgsmål og svar er arrangeret i følgende kategorier

- Oprette et lokalt fastgjorte lydstyrken
- Sikkerhedskopiere en lokalt fastgjort
- Konvertere en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken
- Gendanne et lokalt fastgjorte lydstyrken
- Fejlbehæftede på et lokalt fastgjorte drev

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Spørgsmål om at oprette et lokalt fastgjorte lydstyrken

**Q.** Hvad er den maksimale størrelse på et lokalt fastgjorte lydstyrke, som jeg kan oprette på 8000 serie enheder?

**A** kan du klargør lokalt fastgjorte enheder op til 8.5 TB eller lagdelte enheder op til 200 TB på 8100 enheden. På større 8600 enheden, kan du klargør lokalt fastgjorte enheder op til 22,5 TB eller lagdelte enheder op til 500 TB.

**Q.** Jeg for nylig har opgraderet enheden 8100 til opdatering 2, og når jeg forsøger at oprette et lokalt fastgjorte drev, den maksimale tilgængelige størrelse er kun 6 TB og ikke 8.5 TB. Hvorfor kan jeg oprette en 8.5 TB enhed?

**A** kan du klargør lokalt fastgjorte enheder op til 8.5 TB eller lagdelt enheder op til 200 TB på 8100 enheden. Hvis din enhed har allerede lagdelt enheder, vil nu være proportionalt lavere end denne maksimumgrænsen plads til at oprette et lokalt fastgjorte lydstyrken. Eksempelvis hvis 100 TB lagdelte enheder har allerede klargjort på enheden 8100 (som er del af lagdelte kapaciteten), reduceres derefter den maksimale størrelse på en lokal enhed, kan du oprette på 8100 enheden tilsvarende til 4 TB (omkring halvdelen af det maksimale antal lokalt fastgjort lydstyrken kapacitet).

Da nogle lokale plads på enheden, der bruges til at hoste arbejdssæt af lagdelte enheder, reduceres den tilgængelige plads til at oprette et lokalt fastgjorte lydstyrken, hvis enheden er lagdelt enheder. Omvendt, oprette et lokalt fastgjorte lydstyrken proportionalt reducerer den tilgængelige plads til lagdelte enheder. Den følgende tabel opsummerer tilgængelige lagdelte kapaciteten på 8100 og 8600 enheder, når der oprettes lokalt fastgjorte enheder.

|Lokalt fastgjorte enheder klargjort kapacitet|Tilgængelige kapacitet til at blive klargjort for lagdelte enheder - 8100|Tilgængelige kapacitet til at blive klargjort for lagdelte enheder - 8600|
|-----|------|------|
|0 | 200 TB | 500 TB |
|1 TB | 176.5 TB | 477.8 TB|
|4 TB | 105.9 TB | 411.1 TB |
|8.5 TB | 0 TB | 311.1 TB|
|10 TB | IKKE.TILGÆNGELIG | 277.8 TB |
|15 TB | IKKE.TILGÆNGELIG | 166.7 TB |
|22,5 TB | IKKE.TILGÆNGELIG | 0 TB |


**Q.** Hvorfor er lokalt fastgjorte lydstyrken oprettelse af en længerevarende handling? 

**A.** Lokalt fastgjorte enheder er thickly klargjort. For at skabe plads på de lokale niveauer af enheden, kan nogle data fra eksisterende lagdelte enheder installeres til skyen under processen klargøring. Og da dette afhænger af størrelsen på lydstyrken ved at blive klargjort, de eksisterende data på din enhed og den tilgængelige båndbredde i skyen, den tid, det tager at oprette en lokal enhed kan være flere timer.

**Q.** Hvor lang tid tager det for at oprette et lokalt fastgjorte lydstyrke?

**A.** Fordi lokalt fastgjorte enheder er thickly klargjort, kan nogle eksisterende data fra lagdelte enheder under processen klargøring installeres til skyen. Den tid, det tager at oprette et lokalt fastgjorte lydstyrken afhænger derfor af flere faktorer, herunder størrelsen på lydstyrken, dataene på din enhed og den tilgængelige båndbredde. På en frisk installerede enhed, der har ingen enheder er tid til at oprette et lokalt fastgjorte lydstyrken omkring 10 minutter per terabyte data. Oprettelse af lokale drev kan dog tage flere timer, der er baseret på de faktorer, der er beskrevet ovenfor på en enhed, der er i brug.

**Q.** Jeg vil oprette et lokalt fastgjorte lydstyrken. Er der en hvilken som helst bedste fremgangsmåder, jeg skal være opmærksom på?

**A.** Lokalt fastgjorte enheder egner sig til arbejdsbelastninger, som kræver lokale garantier med data til enhver tid og er følsomme til skyen latenstider. Mens du overvejer være brugen af lokale drev på grund af din arbejdsbelastninger, skal du opmærksom på følgende:

- Lokalt fastgjorte enheder thickly er klargjort, og oprette lokale drev påvirker den tilgængelige plads til lagdelte enheder. Vi anbefaler derfor, at du starter med mindre størrelse enheder og skalere op, når din lagerplads kravet om øges.

- Klargøring af lokale drev er en længerevarende handling, der kan omfatte trykke på eksisterende data fra lagdelte enheder til skyen. Som et resultat, kan du opleve reduceret ydeevne på disse enheder.

- Klargøring af lokale drev er en tidskrævende handling. Den faktiske tid, der er involveret afhænger af flere faktorer: størrelsen på lydstyrke ved at blive klargjort, data på din enhed og tilgængelige båndbredde. Hvis du ikke har sikkerhedskopieret dine eksisterende drev i skyen, derefter er oprettelse af lydstyrken langsommere. Vi anbefaler, at du tager skyen øjebliksbillede af dine eksisterende drev, før du klargør et lokalt drev.
 
- Du kan konvertere eksisterende lagdelte enheder til lokalt fastgjorte enheder, og denne konvertering omfatter klargøring af plads på enheden for resulterende lokalt fastgjorte lydstyrken (ud over de overføres ned lagdelte data [eventuelle] fra skyen). Dette er igen, en længerevarende handling, der afhænger af faktorer, der har omtalt ovenfor. Vi anbefaler, at du sikkerhedskopierer dine eksisterende drev før konvertering, som processen bliver endnu langsommere, hvis eksisterende enheder ikke sikkerhedskopieres. Enheden vil også opleve reduceret ydeevne under denne proces.
    
Du kan finde flere oplysninger om, hvordan du [opretter et lokalt fastgjorte lydstyrken](storsimple-manage-volumes-u2.md#add-a-volume)

**Q.** Kan jeg oprette flere lokalt fastgjorte enheder på samme tid?

**A.** Ja, men alle andre lokalt fastgjorte lydstyrken oprettelse og udvidelse job behandles sekventielt.

Lokalt fastgjorte enheder er thickly klargjort og dette kræver, at oprettelsen af lokale plads på enheden (hvilket kan medføre eksisterende data fra lagdelte enheder kan installeres i skyen under processen klargøring). Derfor, hvis en klargøring af job er i gang, andre lokale lydstyrken oprettelse af job sættes i kø før jobbet er afsluttet.

På samme måde, hvis en eksisterende lokale lydstyrken udvides eller en lagdelte lydstyrken konverteres til et lokalt fastgjorte lydstyrken, derefter oprettelse af en ny lokalt fastgjorte lydstyrken er i kø, indtil det forrige job er fuldført. Udvide størrelsen på et lokalt fastgjorte lydstyrken omfatter udvidelse af eksisterende lokale afstanden for det pågældende drev. Konvertering fra en lagdelte til lokalt fastgjort lydstyrken omfatter også oprettelse af lokale mellemrum, til den resulterende lokalt fastgjort lydstyrken. I begge disse handlinger, oprettelse eller udvidelse af lokale mellemrum en lang kører jobbet.

Du kan få vist disse job på siden **sager** i tjenesten Azure StorSimple Manager. Den sag, som er aktivt behandles opdateres kontinuerligt afspejler status for klargøring af mellemrum. De resterende lokalt fastgjorte lydstyrken job er markeret som løbende, men fremdriften stå og de valgte i den rækkefølge, de er i kø.

**Q.** Jeg har slettet en lokalt fastgjorte lydstyrken. Hvorfor kan jeg ikke se regenereret afstanden afspejles i den tilgængelige plads, når jeg forsøger at oprette en ny enhed? 

**A.** Hvis du sletter et lokalt fastgjorte lydstyrken, kan plads til nye enheder ikke opdateres med det samme. Tjenesten StorSimple Manager opdaterer lokale pladsen cirka hver time. Vi anbefaler, at du venter på en time, før du forsøger at oprette nye lydstyrken.

**Q.** Understøttes lokalt fastgjorte enheder på maskinen skyen?

**A.** Lokalt fastgjorte enheder understøttes ikke på skyen maskinen (8010 og 8020 enheder tidligere kaldes den virtuelle enhed StorSimple).

**Q.** Kan jeg bruge Azure PowerShell-cmdlet'er til at oprette og administrere lokalt fastgjorte enheder? 

**A.** Nej, du kan ikke oprette lokalt fastgjorte enheder via Azure PowerShell-cmdlet'er (en hvilken som helst enhed, du opretter via Azure PowerShell er lagdelt). Vi anbefaler også, at du ikke bruge Azure PowerShell-cmdlet'er til at ændre egenskaberne for et lokalt fastgjorte beholdning, som har uønskede effekten af at ændre typen beholdning til lagdelte.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Spørgsmål om sikkerhedskopiering af et lokalt fastgjorte drev

**Q.** Er lokale snapshot af lokalt fastgjorte enheder understøttes?

**A.** Ja, kan du drage lokale øjebliksbillede af dine lokalt fastgjorte drev. Vi anbefaler på det kraftigste, at du regelmæssigt sikkerhedskopiere din lokalt fastgjorte enheder med skyen snapshots til at sikre, at dine data er beskyttet i tilfælde af nedbrud.

**Q.** Er der en hvilken som helst retningslinjer for at administrere lokale snapshots for lokalt fastgjorte enheder?

**A.** Hyppige lokale snapshots sammen med en høj hastighed for data transportspand i lokalt fastgjorte lydstyrken kan medføre lokale plads på enheden for at blive brugt hurtigt og medfører data fra lagdelte enheder, der overføres til skyen. Vi anbefaler derfor du minimere antallet af lokale snapshots.

**Q.** Jeg har modtaget en advarsel om, at min lokale snapshot af lokalt fastgjorte enheder kan være ugyldiggjorte. Hvornår kan dette sker?

**A.** Hyppige lokale snapshots sammen med en høj hastighed for data transportspand i lokalt fastgjorte lydstyrken kan medføre lokale plads på enheden for at blive brugt hurtigt. Hvis de lokale lag på enheden er belastet, kan medføre en udvidet skyen afbrydelse den enhed, bliver fuld og indgående skriver til lydstyrken kan medføre erklæres ugyldig på snapshots (som der findes ingen mellemrum for at opdatere snapshots til at referere til de ældre blokke med data, som er overskrevet). I en sådan situation skriver til lydstyrken fortsætter med at være served, men de lokale snapshots kan være ugyldige. Der er ingen betydning for din eksisterende skyen snapshots.

Beskeder om advarslen er at fortælle dig, at en sådan situation kan opstå og sikre du tale på samme måde i tide ved enten at gennemgå dine lokale snapshots planer at tage mindre hyppige lokale øjebliksbilleder eller slette ældre lokale snapshots, som ikke længere er påkrævet.

Hvis de lokale snapshots er ugyldige, modtager du en oplysninger besked besked om, at de lokale snapshots for bestemte sikkerhedskopiering politikken har ophævet sammen med på listen over tidsstempler af de lokale snapshots, som er ugyldige. Disse snapshots bliver slettet automatisk, og du vil ikke længere kunne se dem i siden **Sikkerhedskopi kataloger** i portalen Azure klassisk.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Spørgsmål om konvertering af en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken

**Q.** Jeg vægt nogle omstændelige på enheden under konvertering af en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken. Hvorfor sker dette? 

**A.** Konverteringsprocessen omfatter to trin: 

  1. Klargøring af plads på enheden for den snart-til--konverteres lokalt fastgjort lydstyrken.
  2. Hente lagdelte data fra skyen for at sikre, at lokale garanterer.

Begge disse trin er lange kører handlinger, som er afhængige af størrelsen på den er konverteret data på den enhed, og tilgængelige båndbredde lydstyrken. Som nogle data fra eksisterende lagdelte enheder kan spilder til skyen som en del af processen klargøring, vil din enhed opleve reduceret ydeevne i dette tidsrum. Desuden konverteringsprocessen kan være langsommere, hvis:

- Eksisterende enheder er ikke blevet sikkerhedskopieret i skyen så vi anbefaler, at sikkerhedskopierer du dine drev, før du starter en konvertering.

- Er der anvendt båndbredde variere den benyttede politikker, som kan begrænse den tilgængelige båndbredde i skyen Vi anbefaler derfor, du har en dedikeret 40 Mbps eller flere forbindelse til skyen.

- Konverteringsprocessen kan tage flere timer på grund af de flere faktorer, der er beskrevet ovenfor; Vi anbefaler derfor, at du udfører denne handling ikke spidser tidspunkter eller på en weekend for at undgå påvirkningen slutningen forbrugere.

Du kan finde flere oplysninger om, hvordan du [lagdelte konverteres til et lokalt fastgjorte lydstyrken](storsimple-manage-volumes-u2.md#change-the-volume-type)

**Q.** Kan jeg annullere handlingen lydstyrken konvertering?

**A.** Nej, du kan ikke på Annuller konverteringen startet én gang. Som beskrevet i forrige spørgsmål, skal du være opmærksom på de potentielle problemer med ydeevnen, du kan støde på under processen, og Følg de bedste fremgangsmåder, der er anført ovenfor, når du planlægger dit konvertering.

**Q.** Hvad sker der med mine lydstyrken, hvis handlingen konvertering af enheder mislykkes?

**A.** Konvertering af lydstyrke kan mislykkes på grund af problemer med forbindelse til skyen. Enheden holder til sidst konverteringsprocessen efter en række mislykkede forsøg på at sænkes lagdelte data fra skyen. I et scenarie, typen beholdning fortsætter med at være lydstyrken kildetype før konvertering, og:

- En vigtig besked op for at give dig besked om lydstyrken konvertering fejlen. Flere oplysninger om [vigtige beskeder, der er relateret til lokalt fastgjorte enheder](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Hvis du konverterer en lagdelte til et lokalt fastgjorte drev, fortsat lydstyrken udvise egenskaberne for en lagdelte lydstyrke, som data kan stadig være placeret i skyen. Vi anbefaler, at du løse problemer med serverforbindelsen og derefter prøve konvertering igen.
 
- På samme måde, når konverteringen fra et lokalt fastgjort til en lagdelte lydstyrken ikke, selvom lydstyrken markeres som et lokalt fastgjorte drev, den fungerer som en lagdelte lydstyrken (fordi data kan være flyttet til skyen). Det vil imidlertid fortsat at optage plads på de lokale niveauer af enheden. Her er ikke tilgængelige for andre lokalt fastgjorte drev. Vi anbefaler, at udføre handlingen for at sikre, at lydstyrken konverteringen er fuldført, og den lokale plads på enheden kan tages fra den igen.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Spørgsmål om gendannelse af en lokalt fastgjorte lydstyrken

**Q.** Er lokalt fastgjorte enheder gendannet med det samme?

**A.** Ja, gendannes lokalt fastgjorte enheder med det samme. Så snart metadataoplysninger for lydstyrken trækkes fra skyen som en del af gendannelsen, lydstyrken er online og kan åbnes af værten. Dog reduceret lokale garantier for lydstyrken data ikke kan findes, indtil alle dataene er blevet hentet fra skyen, og du kan opleve performance på disse enheder for varigheden af indstillingen Gendan.

**Q.** Hvor lang tid tager det for at gendanne et lokalt fastgjorte lydstyrke?

**A.** Lokalt fastgjorte enheder er gendannet med det samme og kommer online, så snart lydstyrken metadataoplysningerne er hentet fra skyen, mens dataene lydstyrken fortsætter skal hentes i baggrunden. Denne sidste del af gendannelsen – få de lokale garantier for datatypen lydstyrken – er en længerevarende handling og det kan tage flere timer for alle dataene, der skal foretages lokale igen. Den tid, det tager at fuldføre den samme afhænger af flere faktorer, som størrelsen på lydstyrken ved at blive gendannet og den tilgængelige båndbredde. Hvis den oprindelige lydstyrke, gendannes er blevet slettet, tages yderligere tid til at oprette den lokale plads på enheden som en del af gendannelsen.

**Q.** Jeg har brug for at gendanne min eksisterende lokalt fastgjorte lydstyrken til en ældre snapshot (udføres, når der blev lagdelt lydstyrken). Lydstyrken gendannes som lagdelt i dette tilfælde?

**A.** Nej, gendannes lydstyrken som et lokalt fastgjorte lydstyrken. Selvom øjebliksbillede datoer til den tid, når lydstyrken blev lagdelt, mens du gendanner eksisterende enheder, StorSimple altid bruger typen lydstyrken på disken som den findes i øjeblikket.

**Q.** Jeg udvidet min lokalt fastgjorte lydstyrken for nylig, men jeg nu vil gendanne dataene til et tidspunkt, hvor lydstyrken var mindre størrelse. Gendan tilpasses aktuelle lydstyrken og har jeg brug for at udvide størrelsen på lydstyrken, når gendannelsen er fuldført?

**A.** Ja, gendannelsen vil ændre størrelsen på lydstyrken, og skal du udvide størrelsen på lydstyrken, når gendannelsen er fuldført.

**Q.** Kan jeg ændre typen af en enhed under gendannelse?

**A.** Nej, du kan ikke ændre typen beholdning under gendannelsen.

- Enheder, der er blevet slettet gendannes den filtype, der er gemt i snapshot.

- Eksisterende enheder gendannes baseret på aktuelle type, uanset hvilken type, der er gemt i snapshot (refererer til de to foregående spørgsmål).
 
**Q.** Jeg brug for at gendanne min lokalt fastgjorte lydstyrken, men jeg har valgt en forkert punkt i tid øjebliksbillede. Kan jeg annullere den aktuelle gendannelse?

**A.** Ja, du kan annullere en igangværende gendannelsen. Tilstanden for lydstyrken samles tilbage til tilstanden i starten af indstillingen Gendan. En hvilken som helst skriver, der er foretaget til lydstyrken, mens gendannelsen var i gang, men gå tabt.

**Q.** Jeg i gang en gendannelse på en af mine lokalt fastgjorte enheder, og nu kan jeg se et øjebliksbillede i min ordrebeholdningen katalog, som jeg ikke recollect oprettelse. Hvad bruges det til?

**A.** Dette er den midlertidige øjebliksbillede, der er oprettet før gendannelsen og bruges til gendannelse, i tilfælde af indstillingen Gendan annulleret eller mislykkes. Slet ikke denne øjebliksbillede det, slettes automatisk Når gendannelsen er fuldført. Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke.

**Q.** Kan jeg klone et lokalt fastgjorte lydstyrke?

**A.** Ja, kan du. Lokalt fastgjorte lydstyrken der dog klones som en lagdelte lydstyrken som standard. Du kan finde flere oplysninger om, hvordan du [Klon et lokalt fastgjorte lydstyrken](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Spørgsmål om fejlbehæftede på et lokalt fastgjorte drev

**Q.** Jeg vil mislykkes på min enhed til en anden fysisk enhed. Bliver mine lokalt fastgjorte enheder ikke gennemført over som lokalt fastgjort eller lagdelt?

**A.** Afhængigt af softwareversion af den destinationsadresse-enhed, bliver ikke gennemført lokalt fastgjorte enheder som:

- Lokalt fastgjort Hvis target enheden kører StorSimple 8000 opdatere serie 2
- Lagdelt Hvis target enheden kører StorSimple opdatere 8000 serie 1.x
- Lagdelt Hvis target enheden er skyen maskinen (softwareopdatering version 2 eller opdatere 1.x)

Flere oplysninger om [failover og DR lokalt fastgjort enheder på tværs af versioner](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**Q.** Lokalt fastgjorte enheder med det samme gendannes under nedbrud (DR)?

**A.** Ja, gendannes lokalt fastgjorte enheder med det samme under failover. Så snart metadataoplysninger for lydstyrken trækkes fra skyen som en del af handlingen failover, lydstyrken er online på målenheden og kan åbnes af værten. I mellemtiden lydstyrken dataene fortsætter med at hente i baggrunden, og du kan opleve nedsat ydeevne på disse enheder for varigheden af sekundære.

**Q.** Kan jeg se failover job fuldført, hvordan kan jeg få vist status for lokalt fastgjorte lydstyrken, gendannes på den destinationsadresse-enhed?

**A.** Under handlingen failover er failover jobbet markeret som fuldføre en gang alle enhederne i dialogboksen Angiv failover er blevet med det samme gendannet og kommer online på målenheden. Dette omfatter alle lokalt fastgjorte enheder, der er muligvis blevet mislykkedes over; dog er lokale garantier data kun tilgængelige, når alle dataene til lydstyrken er blevet hentet. Du kan registrere denne status for opgaver for hver lokalt fastgjorte enhed, der ikke blev oprettet forfra ved at overvåge de tilsvarende gendannelsesjob, der er oprettet som en del af sekundære. Disse individuelle gendannelsesjob oprettes kun for lokalt fastgjorte enheder.

**Q.** Kan jeg ændre typen af en enhed under failover?

**A.** Nej, du kan ikke ændre typen beholdning under en failover. Hvis du ned over til en anden fysisk enhed, der kører StorSimple 8000 opdatere serie 2, enhederne bliver ikke gennemført baseret på den lydstyrke type, der er gemt i snapshot. Når ned til en anden enhed version, kan du referere til spørgsmålet over på typen lydstyrken efter en failover.

**Q.** Kan jeg ikke over en lydstyrken objektbeholder med lokalt fastgjorte enheder til skyen maskinen?

**A.** Ja, kan du. Lokalt fastgjorte enhederne bliver ikke gennemført som lagdelte enheder. Flere oplysninger om [failover og DR lokalt fastgjort enheder på tværs af versioner](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


