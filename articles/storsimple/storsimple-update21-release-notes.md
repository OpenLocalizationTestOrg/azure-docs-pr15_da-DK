<properties 
   pageTitle="StorSimple 8000 serie opdatering 2.2 produktbemærkninger | Microsoft Azure"
   description="Beskriver de nye funktioner, problemer og løsninger til StorSimple 8000 serie opdatering 2.2."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 serie opdatering 2.2 produktbemærkninger  

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger beskriver de nye funktioner og identificere de kritiske åbne problemer for StorSimple 8000 serie opdatering 2.2. De indeholder også en liste over de StorSimple softwareopdateringer, der er inkluderet i denne udgave. 

Opdater 2.2 kan anvendes på en hvilken som helst StorSimple enhed afprøvet Release (GA) eller Opdater 0,1 opdatering 2.1. Den enhed version, der er knyttet til opdatering 2.2 er 6.3.9600.17708.

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringen i din StorSimple løsning.

>[AZURE.IMPORTANT]
> 
> - Opdatering 2.2 har kun softwareopdateringer. Det tager cirka 1,5-2 timer at installere denne opdatering. 

> - Hvis du kører opdatering 2.1, anbefaler vi, at du anvender Update 2.2 så tidligt som muligt.

> - For nye versioner, kan du ikke kan se opdateringer med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Vent et par dage, og derefter søgningen efter opdateringer igen som disse bliver tilgængelig snart.


## <a name="whats-new-in-update-22"></a>Hvad er nyt i opdatering 2.2

Følgende vigtige forbedringer er foretaget i opdatering 2.2.

 
- **Automatiseret mellemrum videreudnyttelse optimering** – når data slettes på tyndt klargjort enheder, ubrugt lagerplads blokke skal være frigjort. Denne version er forbedret mellemrum videreudnyttelse processen fra skyen medfører ubrugt plads ved at blive tilgængeligt hurtigere i forhold til tidligere versioner.


- **Forbedret øjebliksbillede ydeevne** – Opdater 2.2 er forbedret tid at behandle en skybaseret snapshot i visse scenarier, hvor store mængder bliver brugt, og der er minimale til ingen data transportspand. Et scenarie, der ville få glæde af denne udvidede ville være arkiv enhederne.


- **Indsamling af hardening af Support-pakke** – der har været forbedringer i den måde, Support-pakken er indsamlet og overføres i denne udgave. 


- **Opdatere forbedret pålidelighed** – denne version har fejlrettelser, der medfører en øget pålidelighed opdatering.

  
 

## <a name="issues-fixed-in-update-22"></a>Problemer, der er rettet i opdatering 2.2

De følgende tabeller indeholder en oversigt over problemer, der er blevet løst i opdateringer 2.2 og 2.1.    

| Nej | Funktion                                    | Problem                                                                                                                                                                                                                                                                                        | Gælder for fysiske enhed | Gælder for virtuel enhed |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Host ydeevne                      | I den tidligere udgave, blev problemer med ydeevnen host side observeret under oprettelse af et lokalt fastgjorte lydstyrken og under konvertering af en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken. Disse problemer er løst i denne udgave og medfører en forbedring i ydeevne, host under lydstyrken oprettelsen og konvertering procedurer.                                                                        | Ja                        | Nej                        |
| 2  | Lokalt fastgjorte enheder                     | Systemet ville ned i sjældne tilfælde, når du opretter et lokalt fastgjorte lydstyrken. Denne fejl er blevet løst i denne udgave.                                                                                                                                                               | Ja                        | Nej                        |
| 3  | Overgang switchmiljø                                    | Der var sporadisk går ned, når metadataene for StorSimple skyen anvendelser (8010 og 8020) lagdelt til skyen. Problemet er løst i denne udgave.                                                                                                                              | Nej                         | Ja                       |
| 4  | Oprettelse af øjebliksbillede                          | Der opstod problemer, der er relateret til oprettelse af trinvise snapshots i scenarier med store mængder og minimale til ingen data transportspand. Disse problemer er løst i denne udgave.                                                                                                                 | Ja                        | Ja                       |
| 5  | Openstack godkendelse                   | Når du bruger Openstack som cloud-udbyder, skal brugeren støder på en sjælden fejl, der er relateret til godkendelsen hvor JSON-parseren udløste nedbrud. Denne fejl er rettet i denne udgave.                                                                                                                              | Ja                        | Nej                        |
| 6  | Host side-kopi                             | En sjælden fejl, der er relateret til ODX tidsindstilling blev set i tidligere versioner af softwaren, når du kopierer data fra et drev til en anden enhed. Dette resulterer i en controller failover og systemet potentielt kunne gå i tilstanden nedbrud. Denne fejl er rettet i denne udgave. | Ja                        | Nej       |
| 7  | Windows Management Instrumentation (WMI) | I de tidligere versioner af software, der er flere forekomster af web proxy fejl med undtagelsen "<ManagementException> udbyder indlæsning mislykkedes". Denne fejl blev tilskrevet et hul i hukommelsen WMI og er nu fast.                                                               | Ja                        | Nej                        |
| 8  | Opdater                                     | I visse sjældne tilfælde i tidligere versioner af softwaren, modtaget brugeren en "CisPowershellHcsscripterror", når du forsøger at scanne eller installere opdateringer. Problemet er løst i denne udgave.                                                                                        | Ja                        | Ja                       |
| 9  | Support-pakken                            | I denne udgave, har der været forbedringer af, hvordan pakken Support er indsamlet og overføres.                                                                                                                                                                                                      | Ja                        | Ja                                    |


## <a name="known-issues-in-update-22"></a>Kendte problemer i opdatering 2.2

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.

| Nej. | Funktion | Problem | Kommentarer / løsning | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Disken quorum | I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum, gå så lagerpulje offline. Det forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som peer-controller-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. | Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 3 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata.|  | Ja | Ja |
| 4 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme enhed kilde til anden destinationsenheder understøttes ikke. Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. | | Ja | Nej |
| 5 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt.    | | Ja | Nej |
| 6 | Webtjenesteproxy | Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed. | Sørg for, at URL-adressen til web-proxy har HTTP som den angivne protokol. Få mere at vide ved at gå til [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md). | Ja | Nej |
| 7 | Webtjenesteproxy | Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed. | | Ja | Nej |
| 8 | Høj skyen ventetid og høj i/o-arbejdsbelastning | Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar". | Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt. | Ja | Nej |
| 9 | Azure PowerShell | Når du bruger StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential & #124; Vælg-objekt - først 1 - Vent** for at markere det første objekt, så du kan oprette et nyt **VolumeContainer** -objekt, cmdlet returnerer alle objekter. | Ombryde cmdlet i parenteser på følgende måde: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Vælg-objekt - først 1 - Vent** | Ja | Ja |
| 10| Overførsel | Når flere lydstyrken objektbeholdere er gået til overførsel, er ETA til seneste sikkerhedskopi nøjagtig kun for objektbeholderen første lydstyrken. Desuden starter parallelle overførsel efter de første 4 sikkerhedskopier i objektbeholderen første lydstyrken overføres. | Vi anbefaler, at du overfører én lydstyrken objektbeholder ad gangen. | Ja | Nej |
| 11| Overførsel | Efter indstillingen Gendan er enheder ikke føjet til politikken sikkerhedskopiering eller gruppen virtuelle disk. | Du skal tilføje disse enheder til en politik for sikkerhedskopiering for at oprette sikkerhedskopier. | Ja | Ja |
| 12| Overførsel | Når overførslen er færdig, skal 5000/7000 serie enheden ikke få adgang til de overførte databeholdere. | Vi anbefaler, at du sletter overførte databeholdere, når overførslen er fuldført og anvendt. | Ja | Nej |
| 13| Klon og DR | En StorSimple enhed kører opdatering 1 kan ikke klone eller udføre nedbrud til en enhed kører før opdatering 1 software. | Du skal opdatere den destinationsadresse-enhed til at opdatere 1 for at tillade disse handlinger | Ja | Ja |
| 14 | Overførsel | Sikkerhedskopiering af konfiguration for overførslen kan mislykkes på en enhed med 5000-7000 serie, når der er lydstyrken grupper med nogen tilknyttede enheder. | Slette alle tomme lydstyrken grupper med nogen tilknyttede enheder, og prøv at konfiguration sikkerhedskopieringen.| Ja | Nej |
| 15 | Azure PowerShell-cmdletter og lokalt fastgjorte enheder | Du kan ikke oprette et lokalt fastgjorte lydstyrken via Azure PowerShell-cmdlet'er. (En hvilken som helst enhed, du opretter via Azure PowerShell vil lagdelt.) |Brug altid tjenesten StorSimple Manager til at konfigurere lokalt fastgjorte enheder.| Ja | Nej |
| 16 |Plads til rådighed for lokalt fastgjorte enheder | Hvis du sletter et lokalt fastgjorte lydstyrken, kan plads til nye enheder ikke opdateres med det samme. Tjenesten StorSimple Manager opdaterer lokale pladsen cirka hver time.| Vent på en time, før du forsøger at oprette nye lydstyrken. | Ja | Nej |
| 17 | Lokalt fastgjorte enheder | Gendan tingene Fremviser midlertidige øjebliksbillede sikkerhedskopien i kataloget til sikkerhedskopiering, men kun for varigheden af kørslen Gendan. Det viser desuden en virtuel diskgruppe med præfiks **tmpCollection** på siden **Sikkerhedskopi politikker** , men kun for varigheden af kørslen Gendan. | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 18 | Lokalt fastgjorte enheder | Hvis du annullerer et Gendan job og en controller belastningsjustering forekommer samme bagefter, Gendannelsesjobbet viser **mislykket** i stedet for **annulleret**. Hvis en Gendan mislykkes og controller serverskift samme bagefter, vises kørslen Gendan **annulleret** i stedet for **mislykkedes**. | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 19 |Lokalt fastgjorte enheder | Hvis du annullerer et Gendan job, eller hvis en gendannelse mislykkes, og derefter controller serverskift, vises et ekstra Gendan job på siden **job** . | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 20 |Lokalt fastgjorte enheder | Hvis du forsøger at konvertere en lagdelte lydstyrken (oprettet og klonede med opdatering 1.2 eller tidligere) til et lokalt fastgjorte lydstyrken og enheden løbe tør for plads, eller der er en skybaseret afbrydelse, kan klik derefter på clone(s) være beskadiget.| Dette problem opstår kun med enheder, der blev oprettet og klonede med før opdatering 2.1 software. Dette skal være en sjælden scenarie.|
| 21 | Konvertering af lydstyrken | Opdater ikke ACRs, der er knyttet til en disk, mens en lydstyrken konvertering er i gang (lagdelt til lokalt fastgjort eller omvendt). Opdatere ACRs, kan det medføre beskadigelse af data. | Hvis det er nødvendigt, Opdater ACRs før lydstyrken konverteringen, og du skal ikke foretage yderligere ACR opdateringer mens konverteringen er i gang. |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controller og firmware opdateringer i opdatering 2.2

Denne version indeholder kun software-opdateringer. Men hvis du opdaterer fra en tidligere version end opdatering 2, skal du installere driver, måde, Spaceport, og (i nogle tilfælde) disk firmware-opdateringer på din enhed.
 
Se [installere opdatering 2.2](storsimple-install-update-21.md) på enheden StorSimple kan finde flere oplysninger om hvordan du installerer den driver, måde, Spaceport og disk firmware-opdateringer.

 
## <a name="virtual-device-updates-in-update-22"></a>Virtuel enhedsopdateringer i opdatering 2.2

Denne opdatering kan ikke anvendes til den virtuelle enhed. Nye virtuelle enheder skal oprettes. 

## <a name="next-step"></a>Næste trin

Lær, hvordan du [installere opdateringen 2.2](storsimple-install-update-21.md) på enheden StorSimple.
