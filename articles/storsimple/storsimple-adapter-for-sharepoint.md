<properties 
   pageTitle="StorSimple Adapter til SharePoint | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du kan installere og konfigurere eller fjerne StorSimple kortet for SharePoint i en SharePoint-serverfarm."
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
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installere og konfigurere StorSimple kortet til SharePoint

## <a name="overview"></a>Oversigt

StorSimple kortet til SharePoint er en komponent, hvor du kan angive Microsoft Azure StorSimple fleksible lager og beskyttelse af data på SharePoint server-farme. Du kan bruge kortet til at flytte binært stort objekt (BLOB) indhold fra SQL Server-indholdsdatabaser til Microsoft Azure StorSimple hybrid cloud storage-enhed.

StorSimple kortet til SharePoint fungerer som en udbyder af Remote BLOB Storage (RBS) og bruger funktionen SQL Server Remote BLOB Storage til at gemme ustrukturerede SharePoint-indhold (i form af BLOB) på en filserver, der understøttes af en StorSimple enhed.

>[AZURE.NOTE] StorSimple-kortet til SharePoint understøtter SharePoint Server 2010 Remote BLOB Storage (RBS). SharePoint Server 2010 eksterne BLOB Storage (EBS) understøttes ikke.

- Hvis du vil hente StorSimple kortet til SharePoint, skal du gå til [StorSimple Adapter til SharePoint] [ 1] i Microsoft Download Center.

- Finde oplysninger om planlægning af RBS og RBS begrænsninger, gå til [Deciding bruge RBS i SharePoint 2013] [ 2] eller [planlægge RBS (SharePoint Server 2010)][3].

Resten af denne oversigt beskriver kort rolle StorSimple kortet til SharePoint og SharePoint kapacitet og ydeevne begrænsningerne, du skal være opmærksom på, før du installerer og konfigurerer kortet. Når du gennemser disse oplysninger, kan du gå til [StorSimple Adapter til SharePoint-installationen](#storsimple-adapter-for-sharepoint-installation) for at konfigurere kortet.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adapter til SharePoint fordele

Indhold gemmes i et SharePoint-websted som ustrukturerede BLOB-data i en eller flere indholdsdatabaser. Som standard er hostet disse databaser på computere, der kører SQL Server og er placeret i SharePoint server-farmen. BLOB kan hurtigt øge i størrelse, forbrug store mængder lokale lager. Derfor vil du finde en anden, billigere storageløsning. SQL Server indeholder en teknologi kaldet Remote Blob Storage (RBS), hvor du kan gemme BLOB indhold i filsystemet uden for SQL Server-databasen. Med RBS, BLOB kan findes i filsystemet på den computer, der kører SQL Server, eller de kan være gemt i filsystemet på en anden servercomputeren.

RBS kræver, at du bruger en udbyder af RBS, som StorSimple kortet til SharePoint, til at aktivere RBS i SharePoint. StorSimple kortet til SharePoint fungerer med RBS, så du kan flytte BLOB til en server, der er sikkerhedskopieret af Microsoft Azure StorSimple systemet. Microsoft Azure StorSimple derefter gemmer BLOB dataene lokalt eller i skyen, baseret på brugen. Der findes lokalt blob, der er meget aktive (normalt kaldes niveau 1 eller varmt data). Mindre aktive data og arkivering data er placeret i skyen. Når du aktiverer RBS på en indholdsdatabase, gemmes det nye BLOB indhold, der er oprettet i SharePoint på StorSimple enheden og ikke i den indholdsdatabase.

Microsoft Azure StorSimple implementeringen af RBS indeholder følgende fordele:

- Ved at flytte BLOB indhold til en anden server, kan du reducere forespørgsel belastning på SQL Server, som kan forbedre SQL Server reaktionstid. 

- Azure StorSimple bruger deduplication og komprimering til at reducere datastørrelsen på.

- Azure StorSimple leverer databeskyttelse i form af lokale og skyen snapshots. Også, hvis du markerer selve databasen på StorSimple enhed, du kan sikkerhedskopiere indholdsdatabase og BLOB sammen i en ned gå ned ensartet måde. (Flytte den indholdsdatabase til enheden understøttes kun til StorSimple 8000 serie enhed. Denne funktion understøttes ikke for serien 5000 eller 7000.)

- Azure StorSimple indeholder funktioner til genoprettelse efter genoprettelse herunder failover, filer og lydstyrken gendannelse (herunder test gendannelse) og hurtig gendannelse af data.

- Du kan bruge data gendannelse software, som Kroll Ontrack PowerControls med StorSimple snapshot af BLOB-data til at udføre på elementniveau gendannelse af SharePoint-indhold. (Denne data gendannelse software er en separat køb).

- StorSimple kortet til SharePoint tilsluttes i Central Administration af SharePoint portal, så du kan administrere hele din SharePoint-løsning fra et centralt sted.

Flytte BLOB indhold til filsystemet kan give andre penge og fordele. For eksempel ved hjælp af RBS kan reducere behovet for dyrt niveau 1-lager, og fordi den formindskes den indholdsdatabase, RBS kan reducere antallet af databaser, der er påkrævet i SharePoint server-farmen. Men andre faktorer, som database begrænsninger og mængden af ikke-RBS indhold kan også have betydning for krav til lagerplads. Du kan finde flere oplysninger om omkostninger og fordele ved at bruge RBS se [planlægge efter RBS (SharePoint Foundation 2010)] [ 4] og [Deciding bruge RBS i SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Begrænsninger for kapacitet og ydeevne

Før du overveje at bruge RBS i din SharePoint-løsning, skal du være opmærksom på tests ydeevne og kapacitetsbegrænsninger for SharePoint Server 2010 og SharePoint Server 2013, og hvordan disse begrænsninger, der er relateret til acceptabel ydeevne. Se [Softwaregrænser og -begrænsninger for SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx)kan finde flere oplysninger.

Gennemgå følgende, før du konfigurerer RBS:

- Sørg for, at den samlede størrelse af indhold (størrelsen på en indholdsdatabase) plus størrelsen på en hvilken som helst tilknyttede externalized BLOB ikke overstiger størrelsesgrænsen RBS understøttes i SharePoint. Denne grænse er 200 GB. 

    **Mål indholdsdatabase og BLOB størrelse**

     1. Køre denne forespørgsel på den centrale Administration WFE. Starte SharePoint Management Shell, og angiv derefter følgende Windows PowerShell-kommando for at hente størrelsen af indholdsdatabaser:

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         Dette trin bliver størrelsen på den indholdsdatabase på disken.

     2. Køre en af de følgende SQL-forespørgsler i SQL Management Studio på boksen SQL server på hver indholdsdatabase, og Tilføj resultatet til tal hentede i trin 1.

        Angiv på SharePoint 2013 indholdsdatabaser:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Angiv på SharePoint 2010 indholdsdatabaser:

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Dette trin bliver størrelsen af de blob, der er blevet externalized.

- Vi anbefaler, at du gemmer alt indhold, BLOB og database lokalt på enheden StorSimple. StorSimple enheden er en klynge med to noder til høj tilgængelighed. Placere indholdsdatabaser og BLOB på StorSimple enheden giver høj tilgængelighed.

    Bruge traditionelle SQL Server overførsel bedste fremgangsmåder til at flytte den indholdsdatabase til StorSimple enheden. Flytte databasen, når alle BLOB indhold fra databasen er blevet flyttet til det pågældende filshare via RBS. Hvis du vælger at flytte den indholdsdatabase til StorSimple enheden, anbefaler vi, at du konfigurerer indholdsdatabase opbevaring på enheden som en primær enhed.

- I Microsoft Azure StorSimple, hvis bruger lagdelte enheder, findes der ingen måde at sikre indholdet gemt lokalt på StorSimple enhed ikke bliver lagdelt til Microsoft Azure skyen lager. Vi anbefaler derfor, bruger StorSimple lokalt fastgjort enheder sammen med SharePoint RBS. Dette sikrer, at alt indhold fra BLOB forbliver lokalt på enheden StorSimple, og er ikke flyttet til Microsoft Azure.

- Hvis du ikke gemmes indholdsdatabaser på enheden StorSimple, bruge traditionelle SQL Server høj tilgængelighed bedste fremgangsmåder, der understøtter RBS. SQL Server klynge understøtter RBS, mens du SQL Server spejling betyder ikke. 

>[AZURE.WARNING] Hvis du ikke har aktiveret RBS, anbefaler vi ikke flytte den indholdsdatabase til StorSimple enhed. Dette er en ikke-testede konfiguration.
 
## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adapter til SharePoint-installation

Før du kan installere StorSimple kortet til SharePoint, skal du konfigurere StorSimple enheden og kontrollere, at SharePoint-serverfarm og SQL Server forskellige opfylder alle forudsætninger. I dette selvstudium beskrives konfigurationskrav samt procedurerne for at installere og opgradere StorSimple kortet til SharePoint. 

## <a name="configure-prerequisites"></a>Konfigurere forudsætninger

Før du kan installere StorSimple kortet til SharePoint, skal du kontrollere, at den StorSimple enhed, SharePoint-serverfarm og SQL Server forskellige opfylder følgende forudsætninger.

### <a name="system-requirements"></a>Systemkrav

StorSimple kortet til SharePoint arbejder med følgende hardware og software:

- Understøttet operativsystem – Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2 

- Understøttede SharePoint-versioner – SharePoint Server 2010 eller SharePoint Server 2013

- Understøttede SQL Server-versioner – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition eller SQL Server 2012 Enterprise Edition

- Understøttede StorSimple enheder – StorSimple 8000 serie, StorSimple 7000 serier eller StorSimple 5000 serie.

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple enhed konfiguration forudsætninger

StorSimple enheden er en blok enhed og kræver en filserver, som dataene kan bruges som f.eks. Vi anbefaler, at du bruger en anden server i stedet for en eksisterende server fra SharePoint-farmen. Denne filserver skal være i samme lokale netværk (LAN) som den SQL Server-computer, der er vært indholdsdatabaser. 

>[AZURE.TIP] 
>
>- Hvis du konfigurerer din SharePoint-farm til høj tilgængelighed, skal du installere filserver for høj tilgængelighed også.
>
>- Hvis du ikke gemmes på indholdsdatabase på StorSimple enheden, kan du bruge traditionelle høj tilgængelighed bedste fremgangsmåder, der understøtter RBS. SQL Server klynge understøtter RBS, mens du SQL Server spejling betyder ikke. 

Skal du kontrollere, at enheden StorSimple er konfigureret korrekt, og at passende mængder til at understøtte SharePoint-installationen er konfigureret og tilgængelig fra SQL Server-computeren. Gå til [Implementer din lokale StorSimple enhed](storsimple-deployment-walkthrough.md) , hvis du endnu ikke har installeret og konfigureret enheden StorSimple. Bemærk IP-adressen på StorSimple enhed. Du skal bruge den under StorSimple Adapter til SharePoint-installation. 

Desuden sørge for, at lydstyrken skal bruges til BLOB externalization opfylder følgende krav:

- Lydstyrken skal være formateret med en 64 KB allokering enhedsstørrelse.

- Dine web front-end (WFE) og application servere skal kunne få adgang til lydstyrken via en sti i Universal Naming Convention (UNC). 

- SharePoint-serverfarm skal konfigureres til at skrive til lydstyrken.

>[AZURE.NOTE] Når du installerer og konfigurerer kortet, alle BLOB externalization skal gennemgå StorSimple-enhed (enheden præsentere enhederne til SQL Server og administrere lagerplads niveauer). Du kan ikke bruge andre mål for BLOB externalization.
 
Hvis du planlægger at bruge StorSimple øjebliksbillede Manager til at tage snapshot af dataene BLOB og database, skal du sørge for at installere StorSimple øjebliksbillede Manager på databaseserveren, så den kan bruge SQL Writer Service til at implementere Windows lydstyrken skygge kopi Service (VSS). 

>[AZURE.IMPORTANT] StorSimple øjebliksbillede Manager understøtter ikke SharePoint VSS Writer og kan ikke gemme programmet ensartet snapshot af SharePoint-data. I et SharePoint-scenarie indeholder StorSimple øjebliksbillede Manager kun nedbrud ensartet sikkerhedskopier. 
 
## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint-farm konfiguration forudsætninger

Sørge for, at din SharePoint-serverfarm er korrekt konfigureret, på følgende måde:

- Bekræft, at din SharePoint-serverfarm er i en sund tilstand, og du kontrollere følgende: 

- Alle SharePoint WFE og programmet servere, der er registreret i farmen kører og kan pinges fra, vil du installere StorSimple kortet til SharePoint-serveren.

- Tjenesten SharePoint Timer (SPTimerV3 eller SPTimerV4), der kører på hver enkelt WFE server og programserver.

- Både tjenesten SharePoint Timer og IIS-programgruppen webstedet Central Administration af SharePoint kører under, du har administratorrettigheder. 

- Sørg for, at Internet Explorer forbedret sikkerhedskontekst (IE ESC) er deaktiveret. Følg disse trin for at deaktivere IE ESC:

    1. Luk alle forekomster af Internet Explorer.

    2. Start af Server Manager.

    3. Klik på **Lokalserver**i venstre rude.

    4. Klik **på**i højre rude ud for **Internet Explorer Enhanced Security Configuration**.

    5. Klik på **fra**under **Administratorer**.

    6. Klik på **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Forudsætninger for Remote BLOB Storage (RBS)

Sørg for, at du bruger en understøttet version af SQL Server. Kun de følgende versioner er understøttet og kan du bruge RBS:

- SQL Server 2008 Enterprise Edition

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

BLOB kan externalized på kun disse enheder, der præsenterer StorSimple enheden til SQL Server. Ingen andre mål for BLOB externalization understøttes.

Når du har fuldført alle nødvendige konfigurationstrinnene, gå til [installeres StorSimple netværkskort til SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installere StorSimple kortet til SharePoint

Brug følgende trin til at installere StorSimple kortet til SharePoint. Hvis du geninstallere softwaren skal du se [opgradere eller geninstallere StorSimple kortet til SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Den tid, der kræves til installation, afhænger af det samlede antal SharePoint databaser i din SharePoint-serverfarm.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## <a name="configure-rbs"></a>Konfigurere RBS

Når du installerer StorSimple kort til SharePoint, kan du konfigurere RBS som beskrevet i følgende procedure.

>[AZURE.TIP] StorSimple kortet til SharePoint tilsluttes siden Central Administration af SharePoint tillade RBS skal være aktiveret eller deaktiveret på hver indholdsdatabase på SharePoint-farmen. Aktivere eller deaktivere RBS på den indholdsdatabase får dog en IIS Nulstil, hvilken, afhængigt af konfigurationen af farm, kan et øjeblik forstyrre tilgængeligheden af SharePoint web front end (WFE). (Faktorer som brug af en front-end belastningsjustering, aktuel server arbejdsmængde og osv., kan begrænse eller fjerne denne afbrydelser). For at beskytte brugere fra en forstyrrelse, anbefaler vi, at du aktiverer eller deaktiverer RBS kun inden for et planlagt vedligeholdelsesvindue.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## <a name="configure-garbage-collection"></a>Konfigurere Spildopsamling

Når objekter er slettet fra et SharePoint-websted, slettes de automatisk ikke fra RBS store lydstyrken. I stedet for en asynkron, baggrund vedligeholdelse slettes tabte BLOB fra store filer. Administratorer kan planlægge denne proces til at køre med jævne mellemrum, eller de kan starte den efter behov.

Dette vedligeholdelsesprogram (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) installeres automatisk for alle SharePoint WFE servere og programmet servere, når du aktiverer RBS. Programmet er installeret på følgende placeringer: *Start drev*: \Programmer\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Oplysninger om konfiguration og brug af vedligeholdelsesprogrammet, men du [Bevare RBS i SharePoint Server 2013][8].

>[AZURE.IMPORTANT] Programmet RBS udføre er ressource intensivt. Du skal planlægge at køre kun i perioder med light aktivitet på SharePoint-farmen.

### <a name="delete-orphaned-blobs-immediately"></a>Slette tabte BLOB lige

Hvis du vil slette tabte BLOB med det samme, kan du bruge følgende instruktioner. Bemærk, at disse instruktioner er et eksempel på, hvordan dette kan gøres i et SharePoint 2013-miljø med følgende komponenter:

- Navnet på indholdsdatabase er WSS_Content.
- SQL-servernavnet er SHRPT13 SQL12\SHRPT13.
- Navnet på web-programmet er SharePoint-80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Opgradere eller geninstallere StorSimple kortet til SharePoint

Brug følgende fremgangsmåde til at opgradere SharePoint server og derefter geninstallere StorSimple Adapter til SharePoint eller blot at opgradere eller geninstallere kortet i en eksisterende SharePoint-serverfarm. 

>[AZURE.IMPORTANT] Gennemse oplysningerne i følgende, før du forsøger at opgradere din SharePoint-software og/eller opgraderingen eller geninstallere StorSimple kortet til SharePoint:
>
>- Alle filer, der tidligere er blevet flyttet til ekstern storage via RBS er ikke tilgængelige, indtil geninstallationen er fuldført, og funktionen RBS er aktiveret igen. For at begrænse bruger virkning, skal du udføre en opgradering eller geninstallation under et planlagt vedligeholdelsesvindue.
>
>- Den tid, der kræves til opgradering/geninstallationen kan variere, afhængigt af det samlede antal databaser i SharePoint i SharePoint server-farmen.
>
>- Når opgraderingen/geninstallationen er fuldført, skal du aktivere RBS indholdsdatabaser. Du kan få flere oplysninger i [Konfigurere RBS](#configure-rbs) .
>
>- Hvis du konfigurerer RBS til en SharePoint-farm, der indeholder et stort antal over databaser (større end 200), kan siden **SharePoint Central Administration** af timeout. Hvis det sker, Opdater siden. Dette påvirker ikke konfigurationsprocessen.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter til sletning af SharePoint

Følgende procedurer beskriver, hvordan du flytter BLOB tilbage til SQL Server-indholdsdatabaser og derefter fjerne StorSimple kortet til SharePoint. 

>[AZURE.IMPORTANT] Du behøver at flytte BLOB tilbage til indholdsdatabaser, før du afinstallerer adapter softwaren. 

### <a name="before-you-begin"></a>Inden du går i gang 

Indsamle følgende oplysninger, før du flytter dataene tilbage til SQL Server-indholdsdatabaser og starte processen til fjernelse af kort:

- Navnene på alle databaser, hvortil RBS er aktiveret
- UNC-sti til det konfigurerede BLOB-lager

### <a name="move-the-blobs-back-to-the-content-databases"></a>Flytte BLOB tilbage til indholdsdatabaser

Før du afinstallerer StorSimple kortet til SharePoint-software, skal du overføre alle blob, der blev externalized tilbage til SQL Server-indholdsdatabaser. Hvis du forsøger at fjerne StorSimple kortet til SharePoint, før du flytter alle BLOB tilbage til indholdsdatabaser, får du vist følgende advarselsmeddelelse.

![Advarselsmeddelelse](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####<a name="to-move-the-blobs-back-to-the-content-databases"></a>Hvis du vil flytte tilbage BLOB til indholdsdatabaser 

1. Hente hver af de externalized objekter.

2. Åbn siden **Central Administration af SharePoint** , og gå til **Systemindstillinger**. 

3. Klik på **Konfigurer StorSimple Adapter**under **Azure StorSimple**.

4. Klik på knappen **Deaktiver** under hver af de indholdsdatabaser, du vil fjerne fra eksterne BLOB-lager på siden **Konfigurer StorSimple Adapter** . 

5. Slet objekterne fra SharePoint, og derefter overføre dem igen.

Du kan også bruge Microsoft` RBS Migrate()` PowerShell-cmdlet, der er inkluderet i SharePoint. Du kan finde yderligere oplysninger finder [overføre indhold ind eller ud af RBS](https://technet.microsoft.com/library/ff628255.aspx).

Når du flytter BLOB tilbage til den indholdsdatabase, kan du gå til næste trin: [Fjern kortet](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Fjerne kortet

Når du flytter BLOB tilbage til SQL Server-indholdsdatabaser, kan du bruge en af følgende indstillinger til at fjerne StorSimple kortet til SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Bruge installationsprogrammet til at fjerne kortet 

1. Brug en konto med administratorrettigheder til at logge på web front end (WFE)-serveren.
2. Dobbeltklik på StorSimple kortet til SharePoint-installationsprogrammet. Konfigurationsguiden starter.

    ![Konfigurationsguiden](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. Klik på **Næste**. Den efterfølgende side vises.

    ![Fjern konfigurationssiden guiden](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. Klik på **Fjern** for at vælge processen til fjernelse af. Den efterfølgende side vises.

    ![Konfiguration af guiden bekræftelsessiden](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. Klik på **Fjern** for at bekræfte fjernelse. Følgende statussiden vises.

    ![Konfiguration af guiden statussiden](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. Når fjernelsen er fuldført, vises afslutningssiden. Klik på **Udfør** for at lukke konfigurationsguiden.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Bruge Kontrolpanel til at fjerne kortet 

1. Åbn Kontrolpanel, og klik derefter på **programmer og funktioner**.

2. Vælg **StorSimple Adapter til SharePoint**, og klik derefter på **Fjern**. 

## <a name="next-steps"></a>Næste trin

[Lær mere om StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx
