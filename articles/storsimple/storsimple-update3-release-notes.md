<properties 
   pageTitle="StorSimple 8000 serie Update 3 produktbemærkninger | Microsoft Azure"
   description="Beskriver de nye funktioner, problemer og løsninger til StorSimple 8000 serie Update 3."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>StorSimple 8000 serie Update 3 produktbemærkninger  

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger beskriver de nye funktioner og identificere de kritiske åbne problemer for StorSimple 8000 serie Update 3. De indeholder også en liste over de StorSimple softwareopdateringer, der er inkluderet i denne udgave. 

Opdater 3 kan anvendes på alle StorSimple enheder afprøvet Release (GA) eller Opdater 0,1 opdatering 2.2. Den enhed version, der er knyttet til opdatering 3 er 6.3.9600.17759.

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringen i din StorSimple løsning.

>[AZURE.IMPORTANT]
> 
> - Opdatering 3 har enhedssoftware, LSI driver og firmware og måde og Spaceport opdateres. Det tager cirka 1,5-2 timer at installere denne opdatering. 

> - For nye versioner, kan du ikke kan se opdateringer med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Vent et par dage, og derefter søgningen efter opdateringer igen som disse bliver tilgængelig snart.


## <a name="whats-new-in-update-3"></a>Hvad er nyt i Update 3

De følgende vigtige forbedringer og fejlrettelser er foretaget i Update 3.

 
- **Automatiseret mellemrum videreudnyttelse ændringer** – starter Update 3, mellemrum videreudnyttelse algoritmer køre på den standby controller til systemet, hvilket resulterer i hurtigere udførelse. Se [StorSimple netværk krav](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)kan finde flere oplysninger om de porte, der kræves til at arbejde med pladsen.

- **Forbedret ydeevne** – opdatering 3 er forbedret ydeevne i læse-og skriveadgang i skyen.

- **Overførsel-relaterede forbedringer** – var i denne udgave, flere fejlrettelser og forbedringer færdige for funktionen overførsel fra 5000/7000 serie enheder til 8000 serie enheder. Du kan finde flere oplysninger om, hvordan du bruger funktionen migrering, skal du gå til [overførsel fra 5000/7000 serie enhed 8000 serie enheden](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- **Overvågning relaterede løsninger** - er i denne udgave, fejl, der er relateret til overvågning diagrammer, servicedashboard og enhed dashboard rettet.


## <a name="issues-fixed-in-update-3"></a>Problemer, der er rettet i opdatering 3

De følgende tabeller indeholder en oversigt over problemer, der er blevet løst i opdatering 3.    

| Nej | Funktion                                    | Problem                                                                                                                                                                                                                                                                                        | Gælder for fysiske enhed | Gælder for virtuel enhed |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Host side dataoverførsel                      | I den tidligere udgave, StorSimple skyen maskinen gå offline under en host side dataoverførsel. Problemet er løst i denne udgave.                                                | Nej                        | Ja                        |
| 2  | Lokalt fastgjorte enheder                     | I den tidligere udgave, der er problemer i forbindelse med i/o-fejl, lydstyrke Konverteringsfejl og datapath fejl for lokalt fastgjorte enheder. Disse problemer er rod-forårsaget og fast i denne udgave.                | Ja                        | Nej                       |
| 3  | Overvågning                                    | Der er flere problemer, der er relateret til rapportering enheder og overvågning samt enhed dashboard diagrammer, hvor der blev vist forkerte oplysninger for lokalt fastgjorte enheder. Disse problemer er løst i denne udgave. | Ja                         | Nej                       |
| 4  | Tunge skriver I/O                          | Når du bruger StorSimple til arbejdsbelastninger, der involverer tunge skriver, skal brugeren køre i en sjælden fejl hvor blev der lagdelt arbejdssættet til skyen. Denne fejl er rettet i denne udgave. | Ja                        | Ja                       |
| 5  | Sikkerhedskopi                                     | I visse sjældne tilfælde i tidligere versioner af softwaren, når brugeren har taget en sikkerhedskopi af en ekstern Klon, vil de opstår fejl i skyen og handlingen ville fejl ud. Problemet er løst i denne udgave og handlingen er fuldført.             | Ja                        | Ja                       |
| 6  | Politik for sikkerhedskopiering                                     | I visse sjældne tilfælde fra de tidligere versioner af software, der opstod en fejl, der er relateret til sletning af sikkerhedskopiering politik. Problemet er løst i denne udgave.       | Ja                        | Ja                       |



## <a name="known-issues-in-update-3"></a>Kendte problemer i Update 3

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.

| Nej. | Funktion | Problem | Kommentarer / løsning | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Disken quorum | I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum, gå så lagerpulje offline. Den forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som den peer-enheds-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. | Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 3 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata.|  | Ja | Ja |
| 4 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme kilde-enhed til anden destinationsenheder understøttes ikke. Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. | | Ja | Nej |
| 5 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt.    | | Ja | Nej |
| 6 | Webtjenesteproxy | Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed. | Sørg for, at URL-adressen med web proxy har HTTP som den angivne protokol. Få mere at vide ved at gå til [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md). | Ja | Nej |
| 7 | Webtjenesteproxy | Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed. | | Ja | Nej |
| 8 | Høj skyen ventetid og høj i/o-arbejdsbelastning | Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar". | Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt. | Ja | Nej |
| 9 | Azure PowerShell | Når du bruger StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential & #124; Vælg-objekt - først 1 - Vent** for at markere det første objekt, så du kan oprette et nyt **VolumeContainer** -objekt, cmdlet returnerer alle objekter. | Ombryde cmdlet i parenteser på følgende måde: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Vælg-objekt - først 1 - Vent** | Ja | Ja |
| 10| Overførsel | Når flere lydstyrken objektbeholdere er gået til overførsel, er ETA til seneste sikkerhedskopi nøjagtig kun for objektbeholderen første lydstyrken. Desuden starter parallelle overførsel efter de første 4 sikkerhedskopier i objektbeholderen første lydstyrken overføres. | Vi anbefaler, at du overfører én lydstyrken objektbeholder ad gangen. | Ja | Nej |
| 11| Overførsel | Efter indstillingen Gendan er enheder ikke føjet til politikken sikkerhedskopiering eller gruppen virtuelle disk. | Du skal tilføje disse enheder til en politik for sikkerhedskopiering for at oprette sikkerhedskopier. | Ja | Ja |
| 12| Overførsel | Når overførslen er færdig, skal 5000/7000 serie enheden ikke få adgang til de overførte databeholdere. | Vi anbefaler, at du sletter overførte databeholdere, når overførslen er fuldført og anvendt. | Ja | Nej |
| 13| Klon og DR | En StorSimple af enheden kører opdatering 1 kan ikke klone eller udføre nedbrud til en enhed kører 1 før update-software. | Du skal opdatere den destinationsadresse-enhed til at opdatere 1 for at tillade disse handlinger | Ja | Ja |
| 14 | Overførsel | Sikkerhedskopiering af konfiguration for overførslen kan mislykkes på en enhed med 5000-7000 serie, når der er lydstyrken grupper med nogen tilknyttede enheder. | Slette alle tomme lydstyrken grupper med nogen tilknyttede enheder, og prøv at konfiguration sikkerhedskopieringen.| Ja | Nej |
| 15 | Azure PowerShell-cmdletter og lokalt fastgjorte enheder | Du kan ikke oprette et lokalt fastgjorte lydstyrken via Azure PowerShell-cmdlet'er. (Andre enheder, du opretter via Azure PowerShell vil lagdelt.) |Brug altid tjenesten StorSimple Manager til at konfigurere lokalt fastgjorte enheder.| Ja | Nej |
| 16 |Plads til rådighed for lokalt fastgjorte enheder | Hvis du sletter et lokalt fastgjorte lydstyrken, kan plads til nye enheder ikke opdateres med det samme. Tjenesten StorSimple Manager opdaterer lokale pladsen cirka hver time.| Vent på en time, før du forsøger at oprette nye lydstyrken. | Ja | Nej |
| 17 | Lokalt fastgjorte enheder | Gendan tingene Fremviser midlertidige øjebliksbillede sikkerhedskopien i kataloget til sikkerhedskopiering, men kun for varigheden af kørslen Gendan. Det viser desuden en virtuel diskgruppe med præfiks **tmpCollection** på siden **Sikkerhedskopi politikker** , men kun for varigheden af kørslen Gendan. | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 18 | Lokalt fastgjorte enheder | Hvis du annullerer et Gendan job og en controller belastningsjustering forekommer samme bagefter, Gendannelsesjobbet viser **mislykket** i stedet for **annulleret**. Hvis en Gendan mislykkes og controller serverskift samme bagefter, vises kørslen Gendan **annulleret** i stedet for **mislykkedes**. | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 19 |Lokalt fastgjorte enheder | Hvis du annullerer et Gendan job, eller hvis en gendannelse mislykkes, og derefter controller serverskift, vises et ekstra Gendan job på siden **job** . | Dette kan ske, hvis Gendan tingene fastgjort har kun lokalt drev eller en blanding af lokalt fastgjort og lagdelte enheder. Hvis Gendannelsesjobbet omfatter kun lagdelte enheder, derefter opstår denne funktionsmåde ikke. Ingen brugerhandlinger er påkrævet. | Ja | Nej |
| 20 |Lokalt fastgjorte enheder | Hvis du forsøger at konvertere en lagdelte lydstyrken (oprettet og klonede med opdatering 1.2 eller tidligere) til et lokalt fastgjorte lydstyrken og enheden løbe tør for plads, eller der er en skybaseret afbrydelse, kan klik derefter på clone(s) være beskadiget.| Dette problem opstår kun med enheder, der blev oprettet og klonede med før opdatering 2.1 software. Dette skal være en sjælden scenarie.|
| 21 | Konvertering af lydstyrken | Opdater ikke ACRs, der er knyttet til en disk, mens en lydstyrken konvertering er i gang (lagdelt til lokalt fastgjort eller omvendt). Opdatere ACRs, kan det medføre beskadigelse af data. | Hvis det er nødvendigt, Opdater ACRs før lydstyrken konverteringen, og du skal ikke foretage yderligere ACR opdateringer mens konverteringen er i gang. |
| 22 | Opdateringer | Når du anvender Update 3, siden **vedligeholdelse** i portalen Azure klassisk vises følgende meddelelse relateret til opdatering 2 – "StorSimple 8000 serie opdatering 2 indeholder muligheden for, at Microsoft proaktiv indsamle oplysninger fra enheden, når vi registrerer potentielle problemer". Dette vildlede, som angiver, at enheden opdateres til opdatering 2. Når enheden er opdateret til opdatering 3 succeesfully, forsvinder denne meddelelse. | Dette problem løses i en senere version. | Ja | Nej |


## <a name="controller-and-firmware-updates-in-update-3"></a>Controller og firmware opdateringer i Update 3

Denne version har LSI driver og firmware opdateringer. Du kan finde flere oplysninger om, hvordan du installerer LSI driver og firmware-opdateringer, du se [installere opdatering 3](storsimple-install-update-3.md) på enheden StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Virtuel enhedsopdateringer i Update 3

Denne opdatering kan ikke anvendes til StorSimple skyen maskinen (også kendt som den virtuelle enhed). Nye virtuelle enheder skal oprettes. 


## <a name="next-step"></a>Næste trin

Lær, hvordan du [har installeret Update 3](storsimple-install-update-3.md) på enheden StorSimple.
