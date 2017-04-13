<properties 
   pageTitle="Hvad er StorSimple øjebliksbillede Manager? | Microsoft Azure"
   description="I denne artikel beskrives StorSimple øjebliksbillede overordnet, dens arkitektur og de tilgængelige funktioner."
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

# <a name="what-is-storsimple-snapshot-manager"></a>Hvad er StorSimple øjebliksbillede Manager?

## <a name="overview"></a>Oversigt

StorSimple øjebliksbillede Manager er en snap-in til Microsoft Management Console (MMC), der forenkler databeskyttelse og sikkerhedskopiering administration i et Microsoft Azure StorSimple miljø. Med StorSimple øjebliksbillede Manager, kan du administrere Microsoft Azure StorSimple data i et datacenter og i skyen som en enkelt integreret storageløsning, og som derfor forenkle sikkerhedskopiering processer og reducere omkostninger.

Denne oversigt introducerer StorSimple øjebliksbillede Manager, beskriver de tilgængelige funktioner og forklarer dens rolle i Microsoft Azure StorSimple. 

Finde en oversigt over hele systemet i Microsoft Azure StorSimple, herunder StorSimple enheden, StorSimple Manager service, StorSimple af snapshots og StorSimple Adapter til SharePoint, [StorSimple 8000 serie: en hybrid cloud storageløsning](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- Du kan ikke bruge StorSimple øjebliksbillede Manager til at administrere Microsoft Azure StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder).
>
>- Hvis du planlægger at installere StorSimple opdatering 2 på enheden StorSimple, skal du sørge for at hente den nyeste version af StorSimple øjebliksbillede Manager og installere den, **før du installerer StorSimple opdatering 2**. Den seneste version af StorSimple øjebliksbillede Manager er bagudkompatible og arbejder med alle frigivne versioner af Microsoft Azure StorSimple. Hvis du bruger den tidligere version af StorSimple øjebliksbillede Manager, skal du opdatere den (du ikke behøver at fjerne den tidligere version, før du installerer den nye version).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Formålet med StorSimple af snapshots og arkitektur

StorSimple øjebliksbillede Manager indeholder en central administrationskonsol, som du kan bruge til at oprette ensartet, punkt-in-time sikkerhedskopier af lokale og skyen data. Du kan for eksempel bruge konsollen skal:

- Konfigurere, sikkerhedskopiere og slette enheder.
- Konfigurere lydstyrken grupper for at sikre, at det sikkerhedskopierede data er ensartet programmet på computeren.
- Administrere politikker for sikkerhedskopiering, så dataene der sikkerhedskopieres efter et forudbestemt tidsplan.
- Oprette lokale og snapshots, som kan være gemt i skyen og bruges til nedbrud i skyen.

StorSimple øjebliksbillede Manager henter på listen over programmer, der er registreret hos provideren VSS på værten. Derefter for at oprette programmet ensartede sikkerhedskopier, skal den kontrollerer de enheder, der bruges af et program og foreslår lydstyrken grupper til at konfigurere. StorSimple øjebliksbillede Manager bruger disse lydstyrken grupper til at generere sikkerhedskopier, der er ensartet programmet på computeren. (Programmet konsistens findes, når alle de relaterede filer og databaser synkroniseres og angiver en SAND af programmet på et bestemt sted i gang). 

StorSimple øjebliksbillede Manager sikkerhedskopier tage form af trinvise snapshots, som registrere kun ændringerne, siden sidste sikkerhedskopiering. Som et resultat kan sikkerhedskopier kræver mindre lagerplads og oprettes og hurtigt gendannes. StorSimple øjebliksbillede Manager bruger Windows lydstyrken skygge kopi Service (VSS) til at sikre, at snapshots registrere programmet ensartede data. (Du kan finde flere oplysninger, gå til Integration med Windows lydstyrken tjenesten Øjebliksbillede sektion.) Med StorSimple øjebliksbillede Manager, som du kan oprette ekstra tidsplaner eller tage øjeblikkelig sikkerhedskopier efter behov. Hvis du vil gendanne data fra en sikkerhedskopi, StorSimple øjebliksbillede Manager kan vælge du fra et katalog over lokalt eller skybaseret snapshots. Azure StorSimple gendanner kun de data, der skal bruges som det er nødvendigt, hvilket forhindrer forsinkelser i datatilgængelighed under gendannelse.)

![StorSimple øjebliksbillede Manager arkitektur](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple øjebliksbillede Manager arkitektur** 

## <a name="support-for-multiple-volume-types"></a>Understøttelse af flere typer af lydstyrken

Du kan bruge StorSimple øjebliksbillede Manager til at konfigurere og sikkerhedskopiere følgende typer enheder: 

- **Grundlæggende enheder** – en grundlæggende lydstyrken er en enkelt partition på en grundlæggende disk. 

- **Simple enheder** – en simpel lydstyrken er en dynamisk enhed, der indeholder diskplads fra en enkelt dynamisk disk. En simpel lydstyrken består af et enkelt område på en disk eller flere områder, der er kædet sammen på den samme disk. (Du kan oprette simple enheder på dynamiske diske.) Simple enheder er ikke fejlsikrede.

- **Dynamiske enheder** – en dynamisk enhed er en enhed, der er oprettet på en dynamisk disk. Dynamiske diske bruges en database til at spore oplysninger om enheder, der er indeholdt på dynamiske diske på en computer. 

- **Dynamiske enheder med samme** – dynamiske enheder med samme er opbygget på RAID 1 arkitektur. Med RAID 1 skrives identiske data på to eller flere disk producerer et spejlet sæt. En anmodning om kvittering kan derefter håndteres af enhver disk, der indeholder de ønskede data.

- **Klynge-delte enheder** – med klynge-delte enheder (CSVs), flere noder i en sekundær klynge kan samtidigt læse eller skrive til den samme disk. Failover fra én node til en anden node kan opstå hurtigt, uden at kræve en ændring i drev ejerskab eller tilslutte, afbryde og fjerne en enhed. 

>[AZURE.IMPORTANT] Ikke blande CSVs og ikke-CSVs i det samme øjebliksbillede. Blanding af CSVs og ikke-CSVs i et snapshot understøttes ikke. 
 
Du kan bruge StorSimple øjebliksbillede Manager til at gendanne hele lydstyrken grupper eller klone enkelte enheder og gendanne individuelle filer.

- [Enheder og lydstyrken grupper](#volumes-and-volume-groups) 
- [Typer af sikkerhedskopiering og sikkerhedskopiering politikker](#backup-types-and-backup-policies) 

Kan finde flere oplysninger om StorSimple øjebliksbillede Manager funktioner og hvordan du bruger dem, i [brugergrænsefladen StorSimple øjebliksbillede Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Enheder og lydstyrken grupper

Med StorSimple øjebliksbillede Manager, du opretter enheder og derefter konfigurere dem i lydstyrken grupper. 

StorSimple øjebliksbillede Manager bruger lydstyrken grupper til at oprette sikkerhedskopier, der er ensartet programmet. Programmet konsistens findes, når alle de relaterede filer og databaser synkroniseres og angiver en SAND af et program på et bestemt sted i gang. Lydstyrken grupper (som er også kendt som *konsistens grupper*) udgør grundlaget for en sikkerhedskopi eller Gendan-job.

Lydstyrken grupper er ikke den samme som lydstyrken beholdere. En lydstyrken objektbeholder indeholder en eller flere enheder, der deler en cloud storage konto og andre attributter, som kryptering og båndbredde forbrug. En enkelt lydstyrken objektbeholder kan indeholde op til 256 tyndt klargjort StorSimple enheder. Gå til at [administrere din lydstyrken beholdere](storsimple-manage-volume-containers.md)kan finde flere oplysninger om lydstyrken beholdere. Lydstyrken grupper er samlinger af enheder, du konfigurerer for at lette sikkerhedskopiering. Hvis du markerer to enheder, der tilhører forskellige lydstyrken beholdere, placere dem i en enkelt lydstyrken gruppe og derefter oprette en sikkerhedskopi politik for den pågældende gruppe lydstyrken, sikkerhedskopieres hver lydstyrken i beholderen relevante lydstyrken, ved hjælp af kontoen passende lager.

>[AZURE.NOTE] Alle enheder i en lydstyrken gruppe skal komme fra en enkelt skyen udbyder.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integration med Windows lydstyrken tjenesten Øjebliksbillede

StorSimple øjebliksbillede Manager bruger Windows lydstyrken skygge kopi Service (VSS) til at registrere programmet ensartede data. VSS muliggør programmet konsistens ved at kommunikere med VSS-aware programmer til at koordinere oprettelse af trinvise snapshots. VSS sikrer, at programmerne midlertidigt inaktiv eller passiv, når snapshots er taget. 

StorSimple øjebliksbillede Manager implementeringen af VSS fungerer med SQL Server og generisk NTFS-enheder. Processen er som følger: 

1. En person, som er typisk en datastyring og beskyttelsesløsning (såsom StorSimple øjebliksbillede Manager) eller et sikkerhedskopi-program, starter VSS og beder om at indsamle oplysninger fra writer softwaren i destinationsprogrammet.

2. VSS kontakter komponenten writer for at hente en beskrivelse af dataene. Forfatteren returnerer beskrivelsen af de data, der sikkerhedskopieres. 

3. VSS signaler writer Sådan forbereder du dig programmet til sikkerhedskopi. Forfatteren forbereder dataene til sikkerhedskopi ved at udføre Åbn transaktioner opdatere transaktionslog og osv., og derefter giver besked om VSS.

4. VSS får writer midlertidigt stoppe programmets data butikker, og Sørg for, at ingen data skrives til lydstyrken, mens øjebliksbilledet oprettes. Dette trin sikrer, at dataene konsistens og tager ikke mere end 60 sekunder.

5. VSS får hos udbyderen for at oprette øjebliksbilledet. Udbydere, som kan være software - eller hardware-baserede, administrerer de enheder, der er aktive og oprette øjebliksbilleder af dem efter behov. Udbyderen, der opretter øjebliksbilledet og giver besked om VSS, når den er fuldført.

6. VSS kontakter writer til at informere det program, der kan genoptage I/O og også for at bekræfte, at I/O blev midlertidigt afbrudt under oprettelse af øjebliksbillede. 

7. Hvis kopien lykkedes, returnerer VSS den kopi placering til person. 

8. Hvis dataene blev skrevet, mens øjebliksbilledet blev oprettet, vil sikkerhedskopien være inkonsekvent. VSS sletter øjebliksbilledet og giver besked om person. Person kan enten Gentag sikkerhedskopieringen automatisk eller besked administratoren for at prøve det igen på et senere tidspunkt.

Se nedenstående illustration.

![VSS proces](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows lydstyrken tjenesten Øjebliksbillede processen** 

## <a name="backup-types-and-backup-policies"></a>Typer af sikkerhedskopiering og sikkerhedskopiering politikker

Med StorSimple øjebliksbillede Manager, kan du sikkerhedskopiere data og gemme den lokalt og i skyen. Du kan bruge StorSimple øjebliksbillede Manager til at sikkerhedskopiere data med det samme, eller du kan bruge en sikkerhedskopi politik for at oprette en tidsplan for tage sikkerhedskopier automatisk. Sikkerhedskopiering politikker gør det også muligt at angive, hvor mange snapshots bevares. 

### <a name="backup-types"></a>Typer af sikkerhedskopiering

Du kan bruge StorSimple øjebliksbillede Manager til at oprette følgende typer sikkerhedskopier:

- **Lokale snapshots** – lokale snapshots er punkt-in-time kopier af lydstyrken data, der er gemt på enheden StorSimple. Denne type sikkerhedskopiering kan typisk oprettes og hurtigt gendannes. Du kan bruge en lokal snapshot, som du ville gøre en lokal sikkerhedskopi.

- **Skyen snapshots** – skyen snapshots er punkt-in-time kopier af lydstyrken data, der er gemt i skyen. Et snapshot af en sky er svarer til et øjebliksbillede, replikeres til et andet, storage-system. Skyen snapshots er særligt nyttige i genoprettelse efter nedbrud scenarier.

### <a name="on-demand-and-scheduled-backups"></a>Efter behov og planlagte sikkerhedskopier

Med StorSimple øjebliksbillede Manager, kan du starte en enkeltstående sikkerhedskopi skal have oprettet med det samme, eller du kan bruge en politik for sikkerhedskopiering til at planlægge tilbagevendende sikkerhedskopiering operationer.

En ekstra politik er et sæt automatiseret regler, du kan bruge til at planlægge regelmæssige sikkerhedskopier. En politik for sikkerhedskopiering giver dig mulighed at definere frekvens og parametre for tage snapshot af en bestemt lydstyrken gruppe. Du kan bruge politikker til at angive start- og slutdatoer, klokkeslæt, hyppighed og krav til opbevaring, til både lokal og snapshots i skyen. En politikken anvendes umiddelbart efter at du definerer den. 

Du kan bruge StorSimple øjebliksbillede Manager til at konfigurere eller omkonfigurere sikkerhedskopiering politikker, når det er nødvendigt. 

Du konfigurere følgende oplysninger for hver ekstra politik, som du opretter:

- **Navn** – det entydige navn på den valgte sikkerhedskopiering politik.

- **Type** – typen sikkerhedskopiering politik; lokal snapshot eller skyen øjebliksbillede.

- **Lydstyrken gruppe** – gruppen lydstyrke, som er tildelt den valgte sikkerhedskopiering politik.

- **Opbevaring** – antallet af sikkerhedskopier til at bevare. Hvis du markerer feltet **alle** , bevares alle sikkerhedskopier, indtil det maksimale antal sikkerhedskopier per lydstyrken er nået, hvorefter politikken, mislykkes og genererer en fejlmeddelelse. Du kan også angive et antal sikkerhedskopier til at bevare (mellem 1 og 64).

- **Dato** – den dato, hvor politikken sikkerhedskopiering blev oprettet.

Gå til [Brug StorSimple øjebliksbillede Manager til at oprette og administrere sikkerhedskopiering politikker](storsimple-snapshot-manager-manage-backup-policies.md)for oplysninger om konfiguration af politikker for sikkerhedskopiering.

### <a name="backup-job-monitoring-and-management"></a>Sikkerhedskopieringsjob overvågning og administration

Du kan bruge StorSimple øjebliksbillede Manager til at overvåge og administrere kommende, planlagte og fuldførte sikkerhedskopieringsjob. Desuden indeholder StorSimple øjebliksbillede Manager et katalog over op til 64 færdige sikkerhedskopier. Du kan bruge kataloget til at finde og gendanne enheder eller individuelle filer. 

Gå til [Brug StorSimple øjebliksbillede Manager til at få vist og administrere sikkerhedskopieringsjob](storsimple-snapshot-manager-manage-backup-jobs.md)for oplysninger om overvågning af sikkerhedskopieringsjob.


## <a name="next-steps"></a>Næste trin

- Lær mere om at [bruge StorSimple Snapshot Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).

- Hent [StorSimple øjebliksbillede Manager](https://www.microsoft.com/download/details.aspx?id=44220).
