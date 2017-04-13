<properties 
   pageTitle="StorSimple 8000 serie opdatering 2 produktbemærkninger | Microsoft Azure"
   description="Beskriver de nye funktioner, problemer og løsninger til StorSimple 8000 serie opdatering 2."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 serie opdatering 2 produktbemærkninger  

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger beskriver de nye funktioner og identificere de kritiske åbne problemer for StorSimple 8000 serie opdatering 2. De indeholder også en liste over StorSimple software, -driver og disk firmware-opdateringer i denne version. 

Opdatering 2 kan anvendes på en hvilken som helst StorSimple enhed kører Release (GA) eller Opdater 0,1 gennem opdatering 1.2. Den enhed version, der er knyttet til opdatering 2 er 6.3.9600.17673.

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringen i din StorSimple løsning.

>[AZURE.IMPORTANT]
> 
- Det tager ca. 4-7 timer at installere opdateringen (herunder Windows-opdateringer). 
- Opdatering 2 har software, LSI driver og SSD firmware-opdateringer.
- For nye versioner, kan du ikke kan se opdateringer med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Vent et par dage, og derefter søgningen efter opdateringer igen som disse bliver tilgængelig snart.


## <a name="whats-new-in-update-2"></a>Hvad er nyt i opdatering 2

Opdatering 2 introducerer følgende nye funktioner.

- **Lokalt fastgjort enheder** – blev i tidligere versioner af StorSimple 8000 serien, datablokke lagdelt i skyen, der er baseret på brugen. Der er ikke muligt at garantere, at blokke ville forbliver på et lokalt. I opdatering 2, når du opretter et drev, kan du angive en lydstyrken som lokalt fastgjort og primære data fra den pågældende enhed ikke kan lagdelt til skyen. Snapshot af lokalt fastgjorte enheder skal stadig kopieres til skyen til sikkerhedskopi så skyen kan bruges til data mobilitet og genoprettelse efter genoprettelse formål. Desuden kan du ændre typen beholdning (det vil sige, Konverter lagdelt enheder til lokalt fastgjorte enheder og Konvertér lokalt fastgjort enheder til lagdelt). 

- **Forbedringer af StorSimple virtuel enhed** – tidligere StorSimple 8000 serien placeret den virtuelle enhed som en løsning med nedbrud gendannelse eller udvikling/test. Der opstod kun én model virtuelle enhed (model 1100). Opdatering 2 introducerer to virtuel enhed modeller: 

     - 8010 (tidligere kaldet 1100) – ingen ændring har en kapacitet på 30 TB og bruger Azure standard lagerplads.
     - 8020 – har en kapacitet på 64 TB og bruger Azure Premium lagerplads til forbedret ydeevne.

    Der findes en enkelt Virtuelle for begge virtuel enhed modeller (8010/8020). Når du første gang starter den virtuelle enhed, registrerer parametrene platform og gælder den korrekte modelversion.

- **Forbedringer af netværk** – opdatering 2 indeholder følgende netværk forbedringer:

     - Flere netværkskort kan være aktiveret til skyen, så failover kan opstå, hvis en NIC mislykkes.
     - Routing forbedringer, med fast måleredskaber for skyen aktiveret dokumentkomponenter.
     - Online forsøg mislykkedes ressourcer før en failover.
     - Nye beskeder for fejl på tjeneste.

- **Opdatering af forbedringer** – i opdatering 1.2 og tidligere versioner, StorSimple 8000 serien blev opdateret via to kanaler: Windows Update for klynge, iSCSI, og så videre og Microsoft Update til binære filer og firmware.
    Opdatering 2 bruger Microsoft Update for alle opdatere pakker. Dette bør føre til mindre tid rettelse eller at gøre failover. 

- **Opdateringer** – følgende firmware-opdateringer er inkluderet:
    - LSI: lsi_sas2.sys produktversion 2.00.72.10
    - Kun SSD (ingen harddisk opdateringer): XMGG, XGEG, KZ50, F6C2 og VR08

- **Proaktiv Support** – opdatering 2 hjælper Microsoft med at adskille flere diagnostiske oplysninger fra enheden. Når vores operations-teamet identificerer enheder, der er problemer, er vi bedre udstyret til at indsamle oplysninger fra enheden og diagnosticere problemer. **Ved at acceptere opdatering 2, du har givet os til at levere denne proaktiv support**.    
 

## <a name="issues-fixed-in-update-2"></a>Problemer, der er rettet i opdatering 2

De følgende tabeller indeholder en oversigt over problemer, der er blevet løst i opdateringer 2.    

| Nej. | Funktion | Problem | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Netværksgrænseflader | Efter opgradering til opdatering 1 rapporteret tjenesten StorSimple Manager, fil2 og Data3 portene mislykkedes på den samme controller. Problemet er løst. | Ja | Nej |
| 2 | Opdateringer | Efter opgradering til opdatering 1 opstod lyden alarm beskeder i Azure klassisk portal på flere enheder. Problemet er løst. | Ja | Nej |
| 3 | Openstack godkendelse | Når du bruger Openstack som udbyderen skyen, kan du modtager en fejlmeddelelse, der er for lang tid din skyen godkendelse streng. Problemet er løst. | Ja | Nej |


## <a name="known-issues-in-update-2"></a>Kendte problemer i opdatering 2

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
| 20 |Lokalt fastgjorte enheder | Hvis du forsøger at konvertere en lagdelte lydstyrken (oprettet og klonede med opdatering 1.2 eller tidligere) til et lokalt fastgjorte lydstyrken og enheden løbe tør for plads, eller der er en skybaseret afbrydelse, kan klik derefter på clone(s) være beskadiget.| Dette problem opstår kun med enheder, der blev oprettet og klonede med før opdatering 2 software. Dette skal være en sjælden scenarie.|
| 21 | Konvertering af lydstyrken | Opdater ikke ACRs, der er knyttet til en disk, mens en lydstyrken konvertering er i gang (lagdelt til lokalt fastgjort eller omvendt). Opdatere ACRs, kan det medføre beskadigelse af data. | Hvis det er nødvendigt, Opdater ACRs før lydstyrken konverteringen, og du skal ikke foretage yderligere ACR opdateringer mens konverteringen er i gang. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Controller og firmware opdateringer i opdatering 2

Denne version opdaterer driveren og diskplads firmware på din enhed.
 
- Du kan finde flere oplysninger om LSI firmware opdatere, skal du se Microsoft Knowledge base-artikel 3121900. 
- Du kan finde flere oplysninger om disk firmware opdatere, skal du se Microsoft Knowledge base-artikel 3121899.
 
## <a name="virtual-device-updates-in-update-2"></a>Virtuel enhedsopdateringer i opdatering 2

Denne opdatering kan ikke anvendes til den virtuelle enhed. Nye virtuelle enheder skal oprettes. 

## <a name="next-step"></a>Næste trin

Lær, hvordan du kan [installere opdatering 2](storsimple-install-update-2.md) på enheden StorSimple.
