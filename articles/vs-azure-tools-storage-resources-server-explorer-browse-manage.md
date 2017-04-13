<properties
   pageTitle="Gennemse og administrere lagerplads ressourcer med Server Explorer | Microsoft Azure"
   description="Gennemse og administrere lagerplads ressourcer med Server Explorer"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>Gennemse og administrere lagerplads ressourcer med Server Explorer

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Oversigt
Hvis du har installeret de Azure-værktøjer til Microsoft Visual Studio, kan du få vist blob, kø og tabeldata fra kontiene lagerplads til Azure. Noden Azure-lager i Server Explorer viser data, som er i din lokale lager emulator konto og andre Azure lagerplads konti.

Vælg **visningen**, **Server Explorer**for at få vist Server Explorer i Visual Studio på menulinjen. Noden lagerplads viser alle lagerplads konti, der findes under hvert Azure abonnement/certifikat, som du har forbindelse til. Hvis kontoen lagerplads ikke vises, kan du tilføje den ved at følge vejledningen til [senere i dette emne](#add-storage-accounts-by-using-server-explorer).

Starter i Azure SDK 2.7, kan du også bruge den nye skyen Explorer til at få vist og administrere dine Azure ressourcer. Du kan få flere oplysninger i [Administrere Azure ressourcer i skyen Stifinder](./vs-azure-tools-resources-managing-with-cloud-explorer.md) .


## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Få vist og administrere lagerplads ressourcer i Visual Studio

Server Explorer viser automatisk en liste over blob, forespørgsler og tabeller i din lagerplads emulator konto. Lagerplads emulator konto er angivet i Server Explorer under noden lagerplads som **udvikling** node.

Udvid noden **udvikling** for at få vist lagerplads emulator firmaets ressourcer. Hvis lagerplads emulatoren ikke er blevet startet, når du udvider noden **udvikling** , starter den automatisk. Det kan tage nogle sekunder. Du kan fortsætte med at arbejde i andre områder af Visual Studio, mens lagerplads emulatoren starter.

Udvid noden for kontoen lagerplads i Server Explorer for at få vist ressourcer med en konto med lagerplads. Følgende underordnede noder vises:

- BLOB

- Køer

- Tabeller

## <a name="work-with-blob-resources"></a>Arbejde med Blob ressourcer

Noden BLOB viser en liste over beholdere for den valgte lagerplads konto. BLOB beholdere indeholder blob filer, og du kan organisere disse BLOB i mapper og undermapper. Se, [hvordan du bruger Blob-lager fra .NET](./storage/storage-dotnet-how-to-use-blobs.md) kan finde flere oplysninger.

### <a name="to-create-a-blob-container"></a>Oprette en blob objektbeholder

1. Åbne genvejsmenuen for noden **BLOB** , og vælg derefter **Opret Blob objektbeholder**.

1. Skriv navnet på den nye objektbeholder i dialogboksen **Opret Blob objektbeholder** , og vælg derefter **Ok**

    >[AZURE.NOTE] Blob objektbeholdernavnet skal begynde med et tal (0-9) eller små bogstaver (a-å).

### <a name="to-delete-a-blob-container"></a>Slette en blob objektbeholder

- Åbn genvejsmenuen for objektbeholderen blob, du vil fjerne, og vælg derefter **Slet**.

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>At få vist en liste over elementerne i en blob objektbeholder

- Åbne genvejsmenuen for et blob objektbeholder navn på listen, og vælg derefter **Vis Blob objektbeholder**.

    Når du får vist indholdet af en blob objektbeholder, vises det på en fane, kaldet visningen blob objektbeholder.

    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Du kan udføre følgende handlinger på BLOB ved hjælp af knapperne i øverste højre hjørne af visningen blob objektbeholder:

    - Skriv et filter og anvende den

    - Opdater listen over BLOB i beholderen

    - Overføre en fil

    - Slette en blob

      >[AZURE.NOTE] Slette en fil fra en blob objektbeholder slette ikke den underliggende fil. det kun fjerne den fra objektbeholderen blob.

    - Åbne en blob

    - Gemme en blob til den lokale computer

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Oprette en mappe eller en undermappe i en blob objektbeholder

1. Vælg objektbeholderen blob i Server Explorer. Vælg knappen **Overfør Blob** i vinduet objektbeholder.

    ![Overføre en fil til en blob-mappe](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Klik på **Gennemse** for at angive den fil, du vil overføre i dialogboksen **Overføre nye filer** , og angiv derefter et navn til mappen i feltet **mappe (valgfrit)** .

    Du kan tilføje undermapper i beholderen mapper ved at følge den samme fremgangsmåde. Hvis du ikke angiver et mappenavn, overføres filen til det øverste niveau i objektbeholderen blob. Filen vises i den angivne mappe i beholderen.

    ![Mappe, der er føjet til en blob objektbeholder](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Dobbeltklik på mappen, eller tryk på ENTER for at se indholdet af mappen. Når du er i den beholder mappe, kan du gå tilbage til i roden af beholderen ved at vælge knappen **Åbn overordnede mappe** (pil op).

### <a name="to-delete-a-container-folder"></a>Sådan slettes en objektbeholder mappe

 - Slette alle filerne i mappen

    >[AZURE.NOTE] Fordi mapper i blob objektbeholdere er virtuelle mapper, du kan ikke oprette en tom mappe, eller du kan slette en mappe for at slette filindholdet. Du skal slette hele indholdet af en mappe for at slette mappen.

### <a name="to-filter-blobs-in-a-container"></a>Filtrere BLOB i en objektbeholder

Du kan filtrere blob, der vises ved at angive et almindelige præfiks.

Hvis du angiver præfikset eksempelvis `hello` i feltet tekst, filter og klik derefter på **Udfør** (**!**) knappen, kun blob, der begynder med "Hej" vises.

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] Filterfeltet er store og små bogstaver og understøtter ikke filtrering med jokertegn. BLOB kan kun filtreres efter præfiks. Præfikset kan omfatte en afgrænser, hvis du bruger en afgrænser til at organisere BLOB i et virtuelt hierarki. For eksempel filtrering på præfikset HelloFabric / returnerer alle BLOB begynder med denne streng.

### <a name="to-download-blob-data"></a>Hente blob-data

- I **Server Explorer**, åbne genvejsmenuen for en eller flere BLOB og derefter vælge **Åbn**, eller Vælg blob navnet og derefter vælge knappen **Åbn** , eller dobbeltklik blob navnet.

    Status for hentning af en blob vises i vinduet **Azure aktivitetslog** .

    Blob åbnes i standardprogram til redigering af denne filtype. Hvis operativsystemet genkender filtypen, åbnes filen i et lokalt installeret program Ellers skal du bliver bedt om at vælge et program, der passer til filtypen blob. Den lokale fil, som oprettes, når du henter en blob er markeret som skrivebeskyttet.

    BLOB-data er cachelagret lokalt og kontrolleres i forhold til de blob tidspunkt for seneste ændring i Blob-tjenesten. Hvis blob er blevet opdateret, da den sidst blev hentet, hentes det igen. Ellers indlæses blob fra den lokale disk. Som standard hentes en blob til en midlertidig mappe. Åbn genvejsmenuen for de valgte blob navne for at hente BLOB til en bestemt mappe, og vælg **Gem som**. Når du gemmer en blob på denne måde, blob-filen åbnes ikke, og den lokale fil er oprettet med læse-og skriveadgang attributter.

### <a name="to-upload-blobs"></a>Overføre BLOB

- Vælg knappen **Overfør Blob** , når beholderen er åbnet til visning i visningen blob objektbeholder.

    Du kan vælge en eller flere filer til at overføre, og du kan overføre filer af enhver type. **Azure aktivitetslog** viser status for overførslen. Du kan finde flere oplysninger om, hvordan du arbejder med blob-data, se, [hvordan du bruger tjenesten Azure Blob Storage i .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>At få vist logfiler, der er overført til BLOB

- Hvis du bruger Azure diagnosticering til at registrere data fra Azure-program, og du har flyttet logfiler til kontoen lagerplads, får du vist beholdere, der blev oprettet af Azure til disse logfiler. Få vist disse logfiler i Server Explorer er en nem måde at identificere problemer med dit program, især hvis den er blevet installeret på Azure. Se [Indsaml logføring Data ved hjælp af Azure diagnosticering](https://msdn.microsoft.com/library/azure/gg433048.aspx)kan finde flere oplysninger om Azure diagnosticering.

### <a name="to-get-the-url-for-a-blob"></a>For at hente URL-adressen til en blob

- Åbn den blob genvejsmenuen, og vælg derefter **Kopiér URL-adresse**.

### <a name="to-edit-a-blob"></a>Redigere en blob

- Vælg blob, og vælg derefter knappen **Åbn Blob** .

    Filen er overført til en midlertidig placering og åbnes på den lokale computer. Du skal overføre blob igen, når du har foretaget ændringer.

## <a name="work-with-queue-resources"></a>Arbejde med kø ressourcer

Lagerplads services køer er hostet på en Azure-lager-konto, og du kan bruge dem til at tillade din skyen service roller til at kommunikere med hinanden og med andre tjenester af en meddelelse, der passerer ordning. Du kan få adgang til køen fra et program gennem en skybaseret tjeneste og over en webtjeneste til eksterne kunder. Du kan også få adgang til køen direkte ved hjælp af Server Explorer i Visual Studio.

Når du udvikler en skybaseret tjeneste, der bruger køer, kan du bruge Visual Studio til at oprette køer og arbejde med dem interaktivt, mens du udvikle og teste din kode.

I Server Explorer, kan du få vist køerne med en konto med lagerplads, oprette og slette køer, Åbn en kø for at få vist dets meddelelser og føje meddelelser til en kø. Når du åbner en kø til visning, du kan få vist de enkelte meddelelser, og du kan udføre følgende handlinger på køen ved hjælp af knapperne i øverste venstre hjørne:

- Opdatere visningen af køen

- Tilføje en meddelelse til køen

- Annullering i kø øverste meddelelsen.

- Ryd hele køen

Følgende billede viser en kø, der indeholder to meddelelser.

![Få vist en kø](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Du kan finde flere oplysninger om lagerplads services køer, under [Sådan: bruge tjenesten kø Storage](http://go.microsoft.com/fwlink/?LinkID=264702). Du kan finde oplysninger om webtjeneste for lagerplads services køer [Kø Service begreber](http://go.microsoft.com/fwlink/?LinkId=264788). Du kan finde oplysninger om, hvordan du sender meddelelser til en lagerplads services kø ved hjælp af Visual Studio [Afsendelse af meddelelser til en lagerplads Services kø](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Lagerplads services køer er anderledes end bus servicekøer. Se Bus servicekøer, emner og abonnementer kan finde flere oplysninger om service bus køer.

## <a name="work-with-table-resources"></a>Arbejde med tabel ressourcer

Tjenesten Azure-tabel storage gemmer store mængder strukturerede data. Tjenesten er en NoSQL datastore der accepterer godkendte opkald fra i og uden for Azure skyen. Azure tabeller er ideel til lagring af strukturerede, ikke-relationelle data.

### <a name="to-create-a-table"></a>Oprette en tabel

1. Vælg noden **tabeller** for lagerplads kontoen i Server Explorer, og derefter vælge **Opret tabel**.

1. Angiv et navn til tabellen i dialogboksen **Opret tabel** .

### <a name="to-view-table-data"></a>Sådan får du vist tabeldata

1. Åbn noden **Azure** i Server Explorer, og Åbn derefter noden **lagerplads** .

1. Åbn noden lagerplads konto, du er interesseret i, og Åbn derefter noden **tabeller** for at se en liste over tabeller for kontoen lagerplads.

1. Åbne genvejsmenuen for en tabel, og vælg derefter **Vis tabel**.

    ![En Azure-tabel i Solution Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

Tabellen er organiseret efter objekter (vist i rækker) og egenskaber (vist i kolonner). Følgende illustration viser eksempelvis enheder, der vises i **Tabeldesigner**:

### <a name="to-edit-table-data"></a>Redigere tabeldata

1. Åbne genvejsmenuen for et objekt (en enkelt række) eller en egenskab (en enkelt celle), og vælg derefter **Rediger**i **Tabeldesigner**.

    ![Tilføje eller redigere en tabel-enhed](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Objekter i en enkelt tabel er ikke nødvendigt at have det samme sæt af egenskaber (kolonner). Husk på følgende begrænsninger på få vist og redigere tabeldata.
    - Du kan ikke se eller redigere binære data (type byte[]), men du kan gemme den i en tabel.

    - Du kan ikke redigere **PartitionKey** eller **RowKey** værdierne, fordi i Azure-tabellager ikke understøtter denne handling.

    - Du kan ikke oprette en egenskab, der kaldes tidsstempel, Azure-lager services skal du bruge en egenskab med dette navn.

    - Hvis du angiver en dato / klokkeslætsværdi, skal du følge et format, der er relevant at de internationale og sproglige indstillinger i din computer (for eksempel MM-DD-ÅÅÅÅ HH:MM:SS [AM | PM] for USA engelsk).

### <a name="to-add-entities"></a>Føje objekter

1. Vælg knappen **Tilføj enhed** , som er tæt på det øverste højre hjørne af tabelvisningen i **Tabeldesigner**.

    ![Tilføje enhed](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Angive værdierne i egenskaberne **PartitionKey** og **RowKey** i dialogboksen **Tilføj enhed** .

    ![Tilføj dialogboks for enhed](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Angiv værdierne omhyggeligt, fordi du ikke kan ændre dem, når du lukker dialogboksen, medmindre du slette enheden, og tilføje den igen.

### <a name="to-filter-entities"></a>Filtrere objekter

Du kan tilpasse sæt af objekter, der vises i en tabel, hvis du bruger forespørgselsgeneratoren.

1. Åbn en tabel til visning for at åbne forespørgselsgeneratoren.

1. Vælg knappen længst til højre på tabelvisningen værktøjslinje.

    Dialogboksen **Forespørgselsgenerator** vises. Følgende illustration viser en forespørgsel, der er der oprettet i forespørgselsgeneratoren.

    ![Forespørgselsgeneratoren](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Når du er færdig opbygning af forespørgslen, lukke dialogboksen. Formularen resulterende tekst i forespørgslen vises i en tekstboks som WCF Data Services filter.

1. Vælg ikonet grøn trekant for at køre forespørgslen.

    Du kan også filtrere enhedsdata, der vises i **Tabeldesign** , hvis du angiver en WCF Data Services filterstreng direkte i filterfeltet. Denne type af streng, der svarer til en SQL WHERE-delsætning, men der sendes til serveren som en HTTP-anmodning. Du kan finde oplysninger om, hvordan du oprette filter strenge [Bygning af Filter strenge for tabeldesign](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    Følgende illustration viser et eksempel på et gyldigt filterstreng:

    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>Opdatere data, som lager

Når Server Explorer opretter forbindelse til eller henter data fra en lagerplads-konto, kan det tage op til et minut for at fuldføre handlingen. Hvis det ikke kan oprette forbindelse, kan handlingen timeout. Mens data er hentet, kan du fortsætte med at arbejde i andre dele af Visual Studio. For at annullere handlingen, hvis det tager for lang tid, skal du vælge knappen **Stop Opdater** på værktøjslinjen Server Explorer.

#### <a name="to-refresh-blob-container-data"></a>Du kan opdatere blob objektbeholder data

- Vælg noden **BLOB** under **lagerplads** , og vælg knappen **Opdater** på værktøjslinjen Server Explorer.

- For at opdatere listen over blob, der vises, skal du vælge knappen **Udfør** .

#### <a name="to-refresh-table-data"></a>Opdatere tabeldata

- Vælg noden **tabeller** under **lagerplads** , og vælg knappen **Opdater** .

- For at opdatere listen over enheder, der vises i **Tabeldesign**skal du vælge knappen **Udfør** på **Tabeldesign**.

#### <a name="to-refresh-queue-data"></a>Sådan opdaterer du kø data

- Vælg noden **køer** , og vælg derefter knappen **Opdater** .

#### <a name="to-refresh-all-items-in-a-storage-account"></a>Opdatere alle elementer på en lagerplads konto

- Vælg kontonavnet, og vælg derefter knappen **Opdater** på værktøjslinjen for Server Explorer.

### <a name="add-storage-accounts-by-using-server-explorer"></a>Tilføje lagerplads konti ved hjælp af Server Explorer

Der er to måder at tilføje lagerplads konti ved hjælp af Server Explorer. Du kan oprette en ny konto lager i abonnementet Azure, eller du kan knytte en eksisterende konto lagerplads.

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>Oprette en ny lagerplads konto ved hjælp af Server Explorer

1. Åbne genvejsmenuen for noden lagerplads i Server Explorer, og derefter vælge Opret lagerplads konto.

    ![Oprette en ny firmapost Azure-lager](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Vælg eller Indtast følgende oplysninger for den nye lagerplads konto i dialogboksen **Opret lagerplads konto** .

    - Azure abonnementet, du vil tilføje kontoen lagerplads.

    - Navnet, du vil bruge til den nye konto lagerplads.

    - Det område eller den forbindelse gruppe (som Vest USA eller Østasien).

    - Hvilken type replikering, du vil bruge til kontoen lagerplads, som geografisk overflødige.

1. Vælg **Opret**.

    Den nye lagerplads konto vises på listen **lagerplads** i Solution Explorer.

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Vedhæfte en eksisterende lagerplads konto ved hjælp af Server Explorer

1. Åbne genvejsmenuen for noden Azure-lager i Server Explorer, og derefter vælge **Vedhæft eksterne lagerplads**.

    ![Tilføje en eksisterende konto lagerplads](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Vælg eller Indtast følgende oplysninger for den nye lagerplads konto i dialogboksen **Opret lagerplads konto** .

    - Navnet på den eksisterende lagerplads-konto, du vil vedhæfte. Du kan angive et navn eller vælge den på listen.

    - Nøgle til den valgte lagerplads konto. Denne værdi leveres typisk for dig, når du vælger en lagerplads konto. Hvis du vil Visual Studio til at huske kontonøgle lagerplads, skal du markere afkrydsningsfeltet for konto Husk.

    - Protokollen, der skal bruges til at oprette forbindelse til kontoen lagerplads, som HTTP, HTTPS eller et brugerdefineret slutpunkt. Se, [hvordan du konfigurerer strenge](https://msdn.microsoft.com/library/azure/ee758697.aspx) kan finde flere oplysninger om brugerdefinerede slutpunkter.

### <a name="to-view-the-secondary-endpoints"></a>Sådan får du vist sekundær slutpunkterne

- Hvis du har oprettet en lagerplads konto ved hjælp af indstillingen **Læseadgang geografisk overflødige** gentagelse, kan du få vist slutpunkterne sekundær. Åbne genvejsmenuen for kontonavnet, og vælg derefter **Egenskaber**.

    ![Sekundær slutpunkter for lager](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Du fjerner en lagerplads konto fra Server Explorer

- Åbne genvejsmenuen for feltet kontonavn i Server Explorer, og vælg derefter **Slet**. Hvis du sletter en lagerplads-konto, fjernes alle gemte vigtige oplysninger for den pågældende konto.

    >[AZURE.NOTE] Hvis du sletter en lagerplads konto fra Server Explorer, påvirker det ikke beregningerne kontoen lagerplads eller data, den indeholder; Referencen fjernes blot fra Server Explorer. Hvis du vil slette permanent en lagerplads-konto, skal du bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

## <a name="next-steps"></a>Næste trin

Du kan få mere at vide om brug af Azure lagerplads services, kan du se [få adgang til Azure lagerplads Services](https://msdn.microsoft.com/library/azure/ee405490.aspx).
