<properties 
   pageTitle="Hvad er StorSimple? | Microsoft Azure" 
   description="Beskriver StorSimple overgang switchmiljø, den enhed, virtuel enhed, tjenester og Lagerstyring og introducerer vigtige ord, der bruges i StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 serie: en hybrid cloud storage-løsning

## <a name="overview"></a>Oversigt

Velkommen til Microsoft Azure StorSimple, en integreret storageløsning, der administrerer lager opgaver mellem lokale enheder og Microsoft Azure skylagring. StorSimple er en effektiv, økonomiske og nem at håndtere lagerplads område network (SAN)-løsning, der løser mange af de almindelige problemer og udgifter, der er knyttet til enterprise-lager og databeskyttelse. Det bruger beskyttede StorSimple 8000 serie enheden, integreres med skytjenester og indeholder et sæt værktøjer til administration for en problemfri visning af alle enterprise-lager, herunder skylagring. (StorSimple installationsoplysninger publiceret på webstedet Microsoft Azure gælder kun StorSimple 8000 serie enheder. Hvis du bruger en enhed med StorSimple 5000/7000 serie, gå til [Hjælp til StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple bruger [lagerplads overgang switchmiljø](#automatic-storage-tiering) til at administrere lagrede data på tværs af forskellige lagerplads medier. Det aktuelle arbejdssæt er gemt lokalt på massiv tilstand SSD'er (), data, der bruges mindre hyppigt er gemt på harddiske (harddiske) og arkivering data rykkes til skyen. Desuden bruger StorSimple deduplication og komprimering til at reducere mængden lagerplads, der forbruger dataene. Gå til [Deduplication og komprimering](#deduplication-and-compression)kan finde flere oplysninger. Gå til [StorSimple terminologi](#storsimple-terminology) i slutningen af denne artikel for definitioner af andre vigtige ord og begreber, der bruges i dokumentationen til StorSimple 8000 serie.

Du kan identificere passende mængder med StorSimple opdatering 2, som *lokalt fastgjort* til at sikre, at primære data forbliver lokalt på enheden og ikke klasse til skyen. Dette gør det muligt at køre arbejdsbelastninger, som er følsomme til skyen ventetid, såsom SQL og virtuelt arbejdsbelastninger på lokalt fastgjort enheder, mens du fortsætter med at bruge skyen til sikkerhedskopi. Se [Brug af tjenesten StorSimple Manager til at administrere enheder](storsimple-manage-volumes-u2.md)kan finde flere oplysninger om lokalt fastgjorte enheder. 

Opdatering 2 gør det også muligt at oprette StorSimple virtuelle enheder, der kan udnytte lav latenstider og høj ydeevne fra Azure premium lagerplads. Finde flere oplysninger om StorSimple premium virtuelle enheder [Implementer og administrere en StorSimple virtuel enhed i Azure](storsimple-virtual-device-u2.md). Du kan finde flere oplysninger om Azure premium lagerplads, gå til [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).

Ud over Lagerstyring StorSimple data adgangskodebeskyttelse funktioner gør det muligt at oprette efter behov og planlagt sikkerhedskopier, og derefter gemme dem lokalt eller i skyen. Sikkerhedskopier er taget i form af trinvise snapshots, hvilket betyder, at de kan oprettes og hurtigt gendannes. Skyen snapshots kan være alvorligt vigtige i genoprettelse efter nedbrud scenarier, fordi de erstatte sekundær lagerplads systemer (såsom sikkerhedskopi på bånd), og gør det muligt at gendanne data til din datacenter eller til alternative websteder, hvis det er nødvendigt.

![videoikon](./media/storsimple-overview/video_icon.png) Se video for at få en hurtig introduktion til Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Hvorfor bruge StorSimple?

I følgende tabel beskrives nogle af de vigtigste fordele, som indeholder et Microsoft Azure StorSimple.

| Funktion | Fordel |
|---------|---------|
|Gennemsigtige integration | Microsoft Azure StorSimple bruger iSCSI-protokollen usynligt sammenkæde installationer til lagring af data. Dette sikrer, at disse data, der er gemt i skyen, datacenter, eller på eksterne servere ser ud til at være gemt på ét sted.|
|Begrænset lageromkostninger|Microsoft Azure StorSimple allokerer tilstrækkelige lokal eller skylagring at opfylde aktuelle behov og udvider skylagring kun, når det er nødvendigt. Yderligere reduceres krav til lagerplads og omkostninger ved at fjerne overflødige versioner af de samme data (deduplication) og ved hjælp af komprimering.|
|Forenklet Lagerstyring|Microsoft Azure StorSimple indeholder system administrationsværktøjer, som du kan bruge til at konfigurere og administrere data, der er gemt lokalt, på en fjernserver, og i skyen. Du kan desuden administrere Sikkerhedskopiér og Gendan funktioner fra en Microsoft Management Console (MMC) snap-in. StorSimple indeholder en separat, valgfri grænseflade, som du kan bruge til at udvide services af StorSimple administration og data beskyttelse til indhold, der er gemt på SharePoint-servere. |
|Forbedret nedbrud og overholdelse af regler|Microsoft Azure StorSimple kræver ikke udvidet gendannelse tid. I stedet gendanner det data, som det er nødvendigt. Det betyder normal handlinger kan fortsætte med minimale afbrydelser. Desuden kan du konfigurere politikker for at angive tidsplaner for sikkerhedskopiering og dataopbevaring.
|Datamobilitet|Data, der er overført til Microsoft Azure skytjenester kan åbnes fra andre websteder til gendannelse og overførsel formål. Desuden kan du bruge StorSimple til at konfigurere StorSimple virtuelle enheder på virtuelle maskiner (FOS), der kører på Microsoft Azure. FOS kan derefter bruge virtuelle enheder til at få adgang til lagrede data til test eller gendannelse formål.|
|Understøttelse af andre skyen tjenesteudbydere |StorSimple 8000 serien med software Opdater 1 eller nyere understøtter Amazon S3 med Ressourceposter, HP og OpenStack cloud services, samt Microsoft Azure. (Du skal stadig bruge en Microsoft Azure-lager konto henblik device management). Gå til [Hvad er nyt i opdatering 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12)kan finde flere oplysninger.|
|Forretningskontinuitet | Opdatering 1 eller nyere indeholder en ny overførsel funktion, der StorSimple 5000-7000 serie giver brugerne mulighed at overføre deres data til en StorSimple 8000 serie enhed.|
|Tilgængelighed i portalen Azure Government | Der findes i portalen Azure Government StorSimple opdatering 1 eller nyere. Du kan finde yderligere oplysninger finder [Implementer enheden lokale StorSimple i portalen Government](storsimple-deployment-walkthrough-gov.md).|
|Databeskyttelse og tilgængelighed | StorSimple 8000 serien med opdatering 1 eller nyere understøtter Zone overflødige lagerplads (ZRS), ud over lokalt overflødige lagerplads (LRS) og geografisk overflødige lagerplads (GRS). Se [denne artikel Azure-lager redundans indstillinger](https://azure.microsoft.com/documentation/articles/storage-redundancy/) for ZRS detaljer.|
|Understøttelse af kritiske programmer | Du kan identificere passende mængder lokalt fastgjort med StorSimple opdatering 2. Denne egenskab sikrer, at data, der kræves af kritiske programmer ikke er lagdelt til skyen. Lokalt fastgjorte enheder er ikke underlagt skyen latenstider eller problemer med serverforbindelsen. Se [Brug af tjenesten StorSimple Manager til at administrere enheder](storsimple-manage-volumes-u2.md)kan finde flere oplysninger om lokalt fastgjorte enheder.|
|Lav ventetid og høj ydeevne | StorSimple opdatering 2 gør det muligt at oprette virtuelle enheder, der kan udnytte høj ydeevne, lav ventetid funktioner Azure premium-lager. Finde flere oplysninger om StorSimple premium virtuelle enheder [Implementer og administrere en StorSimple virtuel enhed i Azure](storsimple-virtual-device-u2.md).|

![videoikon](./media/storsimple-overview/video_icon.png) se [denne video](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) for at få en oversigt over de StorSimple 8000 serie funktioner og fordele.

## <a name="storsimple-components"></a>StorSimple komponenter

Microsoft Azure StorSimple løsningen indeholder følgende komponenter:

- **Microsoft Azure StorSimple enhed** – en lokal hybride lagerplads matrix, der indeholder SSDs og harddiske, sammen med overflødige enheder og automatisk failoveregenskaber. Enheder Administrer lagerplads overgang switchmiljø, placere aktuelt åbnede (eller genvejstaster) data på lokale lager (i enhed eller lokale servere), mens du flytter mindre ofte anvendte data til skyen.
- **StorSimple virtuel enhed** – også kendt som StorSimple virtuelle produktet dette er en softwareversion af den StorSimple enhed, der kopieres arkitekturen og de fleste funktioner i fysisk hybrid storage-enhed. Den virtuelle StorSimple enhed kører på en enkelt node i en Azure virtuelt. Premium virtuelle enheder, som udnytte Azure premium lagerplads, er tilgængelige i opdatering 2 eller nyere.
- **Tjenesten StorSimple Manager** – en udvidelse af Azure klassisk portalen, hvor du kan administrere en StorSimple enhed eller StorSimple virtuel enhed fra en enkelt grænseflade. Du kan bruge tjenesten StorSimple Manager til at oprette og administrere tjenester, få vist og administrere enheder, se beskeder, administrere enheder, og få vist og administrere politikker for sikkerhedskopiering og kataloget til sikkerhedskopiering.
- **Windows PowerShell til StorSimple** – en kommandolinjen, som du kan bruge til at administrere StorSimple enhed. Windows PowerShell til StorSimple indeholder funktioner, der gør det muligt at registrere enheden StorSimple kan konfigurere netværksgrænsefladen på din enhed, installere visse typer opdateringer, udføre fejlfinding på enheden ved at få adgang til support-session, og Skift enhedens tilstand. Du kan få adgang til Windows PowerShell til StorSimple ved at oprette forbindelse til seriel konsollen eller ved at bruge Windows PowerShell remoting.
- **Azure PowerShell StorSimple cmdletter** – en samling af Windows PowerShell-cmdlet'er, så du kan automatisere service-niveau og overførsel opgaver fra kommandolinjen. Du kan finde flere oplysninger om Azure PowerShell-cmdletter for StorSimple du gå til [reference til cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **StorSimple øjebliksbillede Manager** – en MMC snap-in, der bruger lydstyrken grupper og tjenesten Windows lydstyrken øjebliksbillede til at generere program-ensartede sikkerhedskopier. Desuden kan du bruge StorSimple øjebliksbillede Manager til at oprette ekstra tidsplaner og Klon eller gendanne enheder. 
- **StorSimple Adapter til SharePoint** – et værktøj, der transparent udvider Microsoft Azure StorSimple lager og databeskyttelse på SharePoint Server-farme, mens du foretager StorSimple lagerplads, kan ses og administreres fra Central Administration af SharePoint-portalen.

Diagrammet nedenfor indeholder en overordnet oversigt over Microsoft Azure StorSimple arkitektur og komponenter.

![StorSimple arkitektur](./media/storsimple-overview/overview-big-picture.png)

I følgende afsnit beskrives hver af disse komponenter mere detaljeret og beskriver, hvordan løsningen arrangerer data, tildeler lagerplads og letter Lagerstyring og beskyttelse af data. Det sidste afsnit indeholder definitioner for nogle af de vigtige begreber og begreber, der er relateret til StorSimple komponenter og deres ledelse.

## <a name="storsimple-device"></a>StorSimple enhed

Microsoft Azure StorSimple enheden er en lokal hybride lagerplads matrix, der indeholder primære lager og iSCSI adgang til data på den. Den styrer kommunikation med skylagring og hjælper med at sikre sikkerheden og fortrolighed af alle data, der er gemt på Microsoft Azure StorSimple løsningen.

StorSimple enheden omfatter SSDs og harddiskdrev harddiske samt understøttelse af klynger og automatisk failover. Den indeholder en delt processor, delt storage og to spejlede enheder. Hver controller indeholder følgende:

- Forbindelsen til en host computer
- Op til seks netværksporte til at oprette forbindelse til det lokale netværk (LAN)
- Hardwareovervågning af
- Ikke-permanente RAM (NVRAM), der bevarer oplysninger, selvom strømmen er afbrudt
- Klynge-aware opdatering for at administrere softwareopdateringer på servere i en sekundær klynge, så opdateringerne har minimale eller ingen effekt på tjenesten kører
- Klynge tjeneste, der kan fungere som en back end-klynge, give høj tilgængelighed og minimere negative effekter, der kan opstå, hvis en harddisk eller SSD mislykkes eller er tilgængelige offline

Kun én controller er aktiv når som helst tidspunkt. Hvis den aktive controller mislykkes, skal aktiveres den anden controller automatisk. 

Gå til [StorSimple hardwarekomponenter og status](storsimple-monitor-hardware-status.md)kan finde flere oplysninger.

## <a name="storsimple-virtual-device"></a>StorSimple virtuel enhed

Du kan bruge StorSimple til at oprette en virtuel enhed, der kopieres arkitektur og funktionerne i den fysiske hybrid lagerplads enhed. Den virtuelle StorSimple-enhed (også kaldet StorSimple virtuelle maskinen) kører på en enkelt node i en Azure virtuelt. (En virtuel enhed kan kun oprettes på en Azure virtuel maskine. Du kan ikke oprette en på en StorSimple enhed eller en lokal server.) 

Den virtuelle enhed har følgende funktioner:

- Det fungerer som en fysisk maskinen og kan tilbyde en iSCSI-grænseflade virtuelle maskiner i skyen. 
- Du kan oprette et ubegrænset antal virtuelle enheder i skyen, og du kan ændre dem til og fra efter behov. 
- Det kan hjælpe dig med at simulere lokale miljøer i nedbrud, udvikling og test scenarier og kan hjælpe med på elementniveau hentning fra sikkerhedskopier. 

Med opdatering 2 og nyere StorSimple virtuelle enheden er tilgængelig i to modeller: 8010 enheden (tidligere kendt som modellen, 1100) og 8020 enhed. 8010 enhed har en maksimale kapacitet på 30 TB. 8020 enheden, der benytter Azure premium-lager, har en maksimale kapacitet på 64 TB. (I lokale lag, Azure premium lagerplads gemmer data på SSDs mens standard storage gemmer data på harddiske.) Bemærk, at du skal have en Azure premium lagerplads-konto for at bruge premium-lagerplads. Du kan finde flere oplysninger om premium lagerplads, gå til [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).

Du kan finde flere oplysninger om StorSimple virtuelle enheden, gå til [Implementer og administrere en StorSimple virtuel enhed i Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Tjenesten StorSimple Manager

Microsoft Azure StorSimple giver en webbaseret brugergrænseflade (tjenesten StorSimple Manager), der gør det muligt at administrere datacenter og cloud storage centralt. Du kan bruge tjenesten StorSimple Manager til at udføre følgende opgaver:

- Konfigurere systemindstillinger for StorSimple enheder.
- Konfigurere og administrere sikkerhedsindstillinger for StorSimple enheder.
- Konfigurere skyen legitimationsoplysninger og egenskaber.
- Konfigurere og administrere drev på en server.
- Konfigurere lydstyrken grupper.
- Sikkerhedskopiere og gendanne data.
- Overvågning af ydeevnen.
- Gennemse systemindstillinger og identificere eventuelle problemer.

Du kan bruge tjenesten StorSimple Manager til at udføre alle administrationsopgaver undtagen dem, der kræver system nedetid som indledende installation og installation af opdateringer.

Gå til [Brug tjenesten StorSimple Manager til at administrere enheden StorSimple](storsimple-manager-service-administration.md)kan finde flere oplysninger.

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell til StorSimple

Windows PowerShell til StorSimple har en grænseflade til kommandolinjeparametre, du kan bruge til at oprette og administrere tjenesten Microsoft Azure StorSimple og konfigurere og overvåge StorSimple enheder. Det er en Windows PowerShell-baseret, kommandolinjeparametre brugergrænseflade, der indeholder dedikeret cmdletter til administration af enheden StorSimple. Windows PowerShell til StorSimple har funktioner, der gør det muligt at:

- Registrere en enhed.
- Konfigurere netværksgrænsefladen på en enhed.
- Installere bestemte typer af opdateringer.
- Foretage fejlfinding af din enhed ved at få adgang til support-session.
- Ændre enhedens tilstand.

Du kan få adgang til Windows PowerShell til StorSimple fra en seriel konsol (på en host computer, der er sluttet direkte til enheden) eller fra en fjernplacering ved hjælp af Windows PowerShell remoting. Bemærk, at nogle Windows PowerShell til StorSimple opgaver såsom at indledende enhed registrering kan kun udføres af seriel konsollen. 

Få mere at vide ved at gå til [Brug af Windows PowerShell til StorSimple til at administrere din enhed](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdletter

Azure PowerShell StorSimple cmdletter er en samling af Windows PowerShell-cmdlet'er, så du kan automatisere service-niveau og overførsel opgaver fra kommandolinjen. Du kan finde flere oplysninger om Azure PowerShell-cmdletter for StorSimple du gå til [reference til cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>StorSimple øjebliksbillede Manager

StorSimple øjebliksbillede Manager er en snap-in til Microsoft Management Console (MMC), som du kan bruge til at oprette ensartet, punkt-in-time sikkerhedskopier af lokale og skyen data. Snap-in kører på en Windows Server-baserede vært. Du kan bruge StorSimple øjebliksbillede Manager til at:

- Konfigurere, sikkerhedskopiere og slette enheder.
- Konfigurere lydstyrken grupper for at sikre, at det sikkerhedskopierede data er ensartet programmet på computeren.
- Administrere politikker for sikkerhedskopiering, så data sikkerhedskopieres efter et forudbestemt tidsplan og gemmes i en dedikeret placering (lokalt eller i skyen).
- Gendanne enheder og enkelte filer.

Sikkerhedskopier registreres som snapshots, som registrere kun ændringerne, efter det sidste øjebliksbillede blev gjort tilgængelig og kræver langt mindre lagerplads end fuld sikkerhedskopier. Du kan oprette ekstra tidsplaner eller tage øjeblikkelig sikkerhedskopier efter behov. Desuden kan du bruge StorSimple øjebliksbillede Manager til at oprette opbevaringspolitikker, der styrer, hvor mange snapshots bliver gemt. Hvis du senere vil gendanne data fra en sikkerhedskopi, StorSimple øjebliksbillede Manager kan vælge du mellem katalog over lokalt eller skybaseret snapshots. 

Hvis der opstår nedbrud, eller hvis du vil gendanne data til en anden mulig grund, gendanner StorSimple øjebliksbillede Manager den trinvist, som det er nødvendigt. Datagendannelse af kræver ikke, at du lukker hele systemet, mens du gendanner en fil, erstatte udstyr eller flytte operationer til et andet websted.

Gå til for at få mere at vide [Hvad er StorSimple øjebliksbillede Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter til SharePoint

Microsoft Azure StorSimple omfatter StorSimple kortet til SharePoint, en valgfri komponent, der udvider transparent StorSimple lager og data adgangskodebeskyttelse funktioner på SharePoint server-farme. Kortet fungerer sammen med en udbyder af Remote Blob Storage (RBS) og funktionen SQL Server RBS, så du kan flytte BLOB til en server, der er sikkerhedskopieret af Microsoft Azure StorSimple systemet. Microsoft Azure StorSimple derefter gemmer BLOB dataene lokalt eller i skyen, baseret på brugen.

StorSimple kortet til SharePoint administreres fra i Central Administration af SharePoint portal. Derfor SharePoint Administration forbliver centralt, og alle lagerplads ser ud til at være i SharePoint-farmen.

Gå til [StorSimple Adapter til SharePoint](storsimple-adapter-for-sharepoint.md), kan finde flere oplysninger. 

## <a name="storage-management-technologies"></a>Teknologier til styring af lager
 
Ud over dedikeret StorSimple enheden, virtuel enhed og andre komponenter bruger Microsoft Azure StorSimple følgende softwareteknologier til at give hurtig adgang til data og at reducere lagerplads forbrug:

- [Automatisk lagring overgang switchmiljø](#automatic-storage-tiering) 
- [Tynd klargøring](#thin-provisioning) 
- [Deduplication og komprimering](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatisk lagring overgang switchmiljø

Microsoft Azure StorSimple arrangerer automatisk data i logiske lag, der er baseret på aktuelle brugen, alder og relation til andre data. Data, der er mest aktive er gemt lokalt, mens mindre aktive og inaktive data automatisk overført til skyen. I følgende diagram vises denne lagerplads metode.
 
![StorSimple lagerplads niveauer](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Hvis du vil aktivere hurtig adgang, gemmer StorSimple meget aktive data (varmt data) på SSDs i StorSimple enhed. Gemmer det data, der bruges nogle gange (varm data) på harddiske i enheden eller på servere på datacenteret. Inaktiv data, sikkerhedskopiering af data, kan det flyttes og data bevares arkivering eller overholdelse formål til skyen. 

>[AZURE.NOTE] I opdatering 2 eller nyere, kan du angive et lokalt fastgjort lydstyrken, i hvilket tilfælde dataene stadig på den lokale enhed og er ikke lagdelt til skyen. 

StorSimple justerer og omarrangerer data og ændre lagerplads tildelinger som brugsmønstre. For eksempel blive nogle oplysninger om mindre aktiv over en periode. Når det bliver gradvist mindre aktive, er de overført fra SSDs til harddiske og derefter til skyen. Hvis de samme data aktiveres igen, er det blevet overført tilbage til storage-enhed.

Processen lagerplads tiering foregår på følgende måde:

1. En systemadministrator har konfigureret en Microsoft Azure cloud storage-konto.
2. Administratoren bruger konsollen seriel og StorSimple Manager-tjenesten (kører i portalen Azure klassisk) til at konfigurere serveren enhed og filer, oprette politikker til beskyttelse af enheder og data. Lokale computere (såsom filservere) Brug den iSCSI Internet Small Computer System Interface () til at få adgang til StorSimple enheden.
3. I første omgang gemmer StorSimple data på hurtig SSD niveau på enheden.
4. Når SSD niveauet nærmer sig kapacitet, StorSimple deduplicates og komprimerer ældste datablokke og flytter dem til harddisk niveauet.
5. Som harddisk niveau fremgangsmåder kapacitet, StorSimple krypterer ældste datablokke og sender dem sikkert til Microsoft Azure-lager konto via HTTPS.
6. Microsoft Azure opretter flere kopier af dataene i dets datacenter og i en ekstern datacenter, at sikre, at dataene kan gendannes, hvis nedbrud opstår. 
7. Når filserveren anmoder om data, der er gemt i skyen, StorSimple returnerer det problemfrit og gemmes en kopi på SSD niveau af StorSimple enhed.

### <a name="thin-provisioning"></a>Tynd klargøring

Tynd klargøring er en virtualization teknologi, hvor tilgængelige lagerplads vises overskrider fysisk ressourcer. I stedet for at reservere tilstrækkelig lagerplads på forhånd bruger StorSimple tynd klargøring til at tildele lige nok plads til at opfylde aktuelle krav. Hvilke elastiske skylagring letter denne metode, fordi StorSimple kan øge eller mindske skylagring at opfylde skiftende behov. 

>[AZURE.NOTE] Lokalt fastgjorte enheder er ikke klargjort tyndt. Lagerplads, der er allokeret til en lokal kun lydstyrken er klargjort i sin helhed, når lydstyrken oprettes.

### <a name="deduplication-and-compression"></a>Deduplication og komprimering

Microsoft Azure StorSimple bruger deduplication og data komprimering til at reducere krav til lagerplads.

Deduplication reducerer den samlede mængde data, der er gemt ved at fjerne redundans i det gemte datasæt. Som oplysningerne ændres, StorSimple ignorerer uændret dataene og registrerer kun ændringer. Desuden reducerer StorSimple mængden af lagrede data ved at identificere og fjerne unødvendige oplysninger. 

>[AZURE.NOTE] Data på lokalt fastgjorte enheder er ikke deduplicated eller komprimeres. Sikkerhedskopier af lokalt fastgjorte enheder er dog deduplicated og komprimeres.

## <a name="storsimple-workload-summary"></a>StorSimple arbejdsbelastningen oversigt

En oversigt over de understøttede StorSimple arbejdsmængder tabelform nedenfor.

| Scenarie                  | Arbejdsbelastning                | Understøttes |  Begrænsninger                                  | Version              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Samarbejde             | Deling af filer            | Ja       |                                                | Alle versioner         |
| Samarbejde             | Fordelt fildeling| Ja       |                                                | Alle versioner         |
| Samarbejde             | SharePoint              | Ja *      |Understøttes kun med lokalt fastgjorte enheder      | Opdatering 2 og nyere versioner   |
| Arkivering                  | Enkel filarkivering   | Ja       |                                                | Alle versioner         |
| Virtualization            | Virtuelle maskiner        | Ja *      |Understøttes kun med lokalt fastgjorte enheder      | Opdatering 2 og nyere versioner   |
| Database                  | SQL                     | Ja *      |Understøttes kun med lokalt fastgjorte enheder      | Opdatering 2 og nyere versioner   |
| Video overvågning        | Video overvågning      | Ja *       |Understøttet, når StorSimple enhed er dedikeret kun for denne arbejdsbelastning| Opdatering 2 og nyere versioner   |
| Sikkerhedskopi                    | Primære mål sikkerhedskopi | Ja *      |Understøttet, når StorSimple enhed er dedikeret kun for denne arbejdsbelastning| Opdater 3 eller nyere |
| Sikkerhedskopi                    | Sekundær target sikkerhedskopi | Ja *      |Understøttet, når StorSimple enhed er dedikeret kun for denne arbejdsbelastning| Opdater 3 eller nyere |

*Ja & #42 -Løsning retningslinjer og begrænsninger, der skal anvendes.*

De følgende arbejdsmængder der ikke understøttes af StorSimple 8000 serie enheder. Hvis installeret på StorSimple, vil disse arbejdsbelastninger resultere i en ikke-understøttede konfiguration.

-  Medicinsk afbildning
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Stor data
-  Distribution af indhold
-  Start fra SCSI

Følgende er en liste over understøttede StorSimple infrastrukturkomponenter. 

| Scenarie | Arbejdsbelastning      | Understøttes |  Begrænsninger                                 | Version      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Generelt  | Express rute | Ja       |                                               |Alle versioner |
| Generelt  | DataCore FC   | Ja *       |Understøttet med DataCore SANsymphony            | Alle versioner |
| Generelt  | DFSR          | Ja *      |Understøttes kun med lokalt fastgjorte enheder     | Alle versioner |
| Generelt  | Indeksering      | Ja *       |Lagdelte enheder kan kun metadata indeksering er understøttede (ingen data).<br>Fuldført indeksering understøttes for lokalt fastgjorte enheder.| Alle versioner |
| Generelt  | Anti-virus    | Ja *       |For lagdelte enheder understøttes kun scanning ved åbning og Luk.<br> Fuld scanning understøttes for lokalt fastgjorte enheder.| Alle versioner |

*Ja & #42 -Løsning retningslinjer og begrænsninger, der skal anvendes.*

## <a name="storsimple-terminology"></a>StorSimple terminologi 

Før du anvender din Microsoft Azure StorSimple løsning, anbefaler vi, at du gennemgå følgende begreber og definitioner.

### <a name="key-terms-and-definitions"></a>Vigtige begreber og definitioner

| Ord (forkortelse eller forkortelse) | Beskrivelse |
| ------------------------------ | ---------------- |
| Access kontrolelement post (ACR)    | En post, der er knyttet til en lydstyrken på enheden Microsoft Azure StorSimple, der bestemmer, hvilke værter kan oprette forbindelse til den. Bestemmelse er baseret på iSCSI kvalificerede navn (IQN) på de værter (findes i ACR), der opretter forbindelse til din StorSimple enhed.|
| AES 256                        | 256-bit avanceret kryptering AES (Standard) algoritme til kryptering af data, som det flyttes til og fra skyen. |
| allokering enhedsstørrelse (AUS)     | Den mindste mængde diskplads, der kan allokeres til at holde en fil i dine Windows fil systemer. Hvis en filstørrelse ikke er et lige multiplum af klyngestørrelse, ekstra plads skal bruges til at holde filen (op til det næste multiplum af klyngestørrelse) medfører tabt plads og findelt harddisken. <br>Anbefalede AUS for Azure StorSimple enheder er 64 KB, fordi det fungerer fint med deduplication algoritmer.|
| Automatiseret lagerplads overgang switchmiljø      | Flytte automatisk mindre aktive data fra SSDs til harddiske og derefter til et trin i skyen, og derefter aktivere management alle lagerplads fra en central brugergrænseflade.|
| kataloget sikkerhedskopiering | En samling af sikkerhedskopier, normalt forbindelse ved den programtype, der blev brugt. Denne samling vises på siden sikkerhedskopi katalog i tjenesten StorSimple Manager brugergrænseflade.|
| kataloget sikkerhedskopiering fil             | En fil, der indeholder en liste over tilgængelige snapshots, der aktuelt er gemt i sikkerhedskopien af databasen af StorSimple øjebliksbillede Manager. |
| politik for sikkerhedskopiering                   | Et udvalg af enheder, type sikkerhedskopi og en tidsplan, hvor du kan oprette sikkerhedskopier på en foruddefineret tidsplan.|
| binær store objekter (BLOB)    | En samling af binære data, der er gemt som et enkelt objekt i en database management system. BLOB er typisk billeder, lyd eller andre multimedieobjekter, selvom nogle gange binære programkode gemmes som en BLOB.|
| Udfordring Password Authentication Protocol (CHAP) | En protokol, der bruges til at godkende peer for en forbindelse, baseret på peer Deling af en adgangskode eller hemmeligt. CHAP kan være envejs eller fælles. Med envejs CHAP godkender destinationen en afsender. Fælles CHAP kræver, at destinationen godkende afsenderen og, at afsenderen godkender destinationen. | 
| Klon                          | En kopi af en volumenlicens. |
|Skyen som et trin (CaaT)          | Cloud storage integreret som et trin i arkitekturen lagerplads, så alle lagerplads ser ud til at være en del af en enterprise lagerplads netværk.|
| cloud-udbyder (CSP)   | En udbyder af cloud computing tjenester.|
| skyen øjebliksbillede                 | En punkt-in-time kopi af lydstyrken data, der er gemt i skyen. Et snapshot af en sky er svarer til et øjebliksbillede, replikeres til et andet, storage-system. Skyen snapshots er særligt nyttige i genoprettelse efter nedbrud scenarier.|
| cloud storage krypteringsnøglen   | En adgangskode eller en nøgle, der bruges af enheden StorSimple til at få adgang til de krypterede data, der er sendt af din enhed til skyen.|
| opdatering af klynge-aware         | Administrere softwareopdateringer på servere i en sekundær klynge, så opdateringerne har minimale eller ingen effekt på tjenesten kører.|
| DataPath                       | En samling af enheder med særlige funktioner, der udfører indbyrdes forbundne databehandling handlinger.|
| deaktivere                     | En permanent handling, der afbryder forbindelsen mellem StorSimple enheden og den tilknyttede skybaseret tjeneste. Skyen snapshot af enheden kan forbliver efter denne proces og klonet eller bruges til nedbrud.|
| diskspejling                 | Replikering af logisk diskenheder på separate hårde drev i realtid at sikre uafbrudt tilgængelighed.|
| dynamisk diskspejling         | Replikering af logisk diskenheder på dynamiske diske.|
| dynamiske diske                  | En disk lydstyrken format, der bruger den logiske Disk Manager (LDM) til at lagre og administrere data på tværs af flere fysiske diske. Dynamiske diske kan forstørres for at give mere plads.|
| Udvidede gruppe af diske (EBOD) omslutning | En sekundær omslutning af enheden Microsoft Azure StorSimple, der indeholder ekstra harddisk diske for ekstra lagerplads.|
| FAT klargøring               | En traditionelt lagerplads klargøring i hvilken storage mellemrum er allokeret baseret på forventet behov (og er som regel ud over brug af aktuelle). Se også *tynd klargøring*.|
| harddisk (harddiske)          | Et drev, der bruger roterende plader til at gemme data.|
| hybrid skylagring           | En lagerplads arkitektur, der bruger lokale og ressourcer, herunder skylagring.|
| Internet Small Computer System Interface (iSCSI) | En Internetprotokol – baseret lagerplads netværk standard til at sammenkæde data lagerplads udstyr eller faciliteter.|
| iSCSI afsenderen                 | En softwarekomponent, der gør det muligt for en host computer med Windows til at oprette forbindelse til en ekstern iSCSI-baseret storagenetværk.|
| iSCSI kvalificerede navn (IQN)      | Et entydigt navn, der identificerer et iSCSI-destinationen eller afsenderen.|
| iSCSI-mål                    | En softwarekomponent, der indeholder centraliserede iSCSI Diskundersystemer i lager-netværk.|
| Live arkivering                  | En lagerplads fremgangsmåde, hvori arkivering data er tilgængelig hele tiden (det ikke er gemt et andet sted på båndet, for eksempel). Microsoft Azure StorSimple bruger direkte arkivering.|
|lokalt fastgjorte lydstyrken | en enhed, der findes på enheden og er aldrig lagdelt til skyen. |
| lokal snapshot                  | En punkt-in-time kopi af lydstyrken data, der er gemt på Microsoft Azure StorSimple enheden.|
| Microsoft Azure StorSimple      | En effektiv løsning bestående af et datacenter lagerplads maskinen og software, der gør det muligt for IT-organisationer til at udnytte skylagring, som hvis det var datacenter lagerplads. StorSimple forenkler databeskyttelse og datastyring samtidig reducere omkostninger. Løsningen konsoliderer primære lagerplads, arkiv, sikkerhedskopiering og genoprettelse efter genoprettelse (DR) igennem nem integration med skyen. Ved at kombinere SAN lager og skyen datastyring på en professionel platform, aktivere StorSimple enheder hastighed, enkle og pålidelighed til alle lagerplads beslægtede behov.|
| Power og køling modul (PCM)  | Hardwarekomponenterne i enheden StorSimple bestående af power forsyninger og køling Vifte, derfor navnet Power og køling modul. Den primære omslutning på enheden har to 764W PCMs, mens EBOD omslutning har to 580W ved PCMs.|
| primære omslutning               | Primære omslutning af enheden StorSimple, der indeholder programmet platform enheder.|
| gendannelse tid mål (RTO)   | Den maksimale mængde tid, der skal anvendes før en forretningsproces eller system er helt gendannet efter nedbrud.| 
|serielt vedhæftet SCSI (SAS)       | En type harddisk (harddiske).|
| Tjenesten data krypteringsnøglen     | En nøgle, der gøres tilgængelige for alle nye StorSimple enheder, der registrerer med tjenesten StorSimple Manager. Af konfigurationsdataene overføres mellem tjenesten StorSimple Manager og enheden er krypteret med en offentlig nøgle og kan derefter dekrypteres kun på den enhed, ved hjælp af en privat nøgle. Tjenesten data krypteringsnøgle kan tjenesten til at få denne privat nøgle til dekryptering.|
| Tjenesten registreringsnøgle        | En nøgle, der kan registrere StorSimple enheden med StorSimple Manager-tjenesten, så den vises i Azure klassisk portal til yderligere management handlinger.|
| Small Computer System Interface (SCSI) | Et sæt af standarder for fysisk forbindelse computere og overføre data mellem dem.|
| massiv state-drev (SSD)         | En disk, der indeholder ingen bevægelige dele for eksempel, et flashdrev.|
| lagerplads konto                 | Et sæt af access-legitimationsoplysninger, der er knyttet til kontoen lagerplads for en given skyen udbyder.| 
| StorSimple Adapter til SharePoint| En Microsoft Azure StorSimple komponent, transparent udvider StorSimple lager og databeskyttelse på SharePoint server-farme.|
| Tjenesten StorSimple Manager      | En udvidelse af Azure klassisk portalen, hvor du kan administrere dine lokale Azure StorSimple og virtuelle enheder.|
| StorSimple øjebliksbillede Manager     | En Microsoft Management Console (MMC) snap-in til administration af sikkerhedskopiering og gendannelse handlinger i Microsoft Azure StorSimple.|
| tage sikkerhedskopiering                     | En funktion, der gør det muligt at tage en interaktiv sikkerhedskopiering af en volumenlicens. Det er en anden måde at tage en sikkerhedskopi af en lydstyrke i modsætning til at tage et automatiseret backup via en defineret politik manuelt.|
| tynd klargøring               | En metode til optimering af effektiviteten, som den tilgængelige lagerplads bruges i lagerplads systemer. I tynd klargøring fordeles opbevaring for flere brugere, der er baseret på den mindste plads af hver enkelt bruger på et givet tidspunkt. Se også *fat klargøring*.|
| overgang switchmiljø | Arrangere data i logiske grupper, der er baseret på aktuelle brugen, alder og relation til andre data. StorSimple arrangerer automatisk data i niveauer.   |
| lydstyrken                          | Logisk lagerområder præsenteres i form af drev. StorSimple enheder svarer til de enheder, der er tilsluttet af værten, herunder dem, der har opdaget ved hjælp af iSCSI og en StorSimple enhed.|
 | lydstyrken objektbeholder                | En gruppering af enheder og de indstillinger, der gælder for dem. Alle enheder i enheden StorSimple er inddelt i lydstyrken beholdere. Indstillinger for lydstyrke objektbeholder omfatter lagerplads konti, krypteringsindstillinger for data, der sendes til skyen med tilknyttede krypteringsnøgler og båndbredde til handlinger, der involverer i skyen.|
| lydstyrken gruppe                    | I StorSimple øjebliksbillede Manager en lydstyrken gruppe er en samling af enheder, der er konfigureret til at lette sikkerhedskopiering behandling.|
| Lydstyrken tjenesten Øjebliksbillede (VSS)| En tjeneste Windows Server operativsystem, som muliggør programmet konsistens ved at kommunikere med VSS-aware programmer til at koordinere oprettelse af trinvise snapshots. VSS sikrer, at programmerne, der er midlertidigt inaktiv, når snapshots er taget.|
| Windows PowerShell til StorSimple | En Windows PowerShell-baserede kommandolinjeparametre brugergrænseflade bruges til at betjene og administrere din StorSimple enhed. Samtidig bevare nogle af de grundlæggende funktioner i Windows PowerShell, har denne grænseflade yderligere dedikeret cmdletter, der henvender sig til administration af en StorSimple enhed.|


## <a name="next-steps"></a>Næste trin

Få mere at vide om [StorSimple sikkerhed](storsimple-security.md).




 

 
