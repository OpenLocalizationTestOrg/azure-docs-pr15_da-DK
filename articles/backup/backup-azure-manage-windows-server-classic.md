<properties
    pageTitle="Administrere Azure sikkerhedskopi vaults og servere Azure ved hjælp af den klassiske implementeringsmodel | Microsoft Azure"
    description="Brug dette selvstudium til at se, hvordan du administrere Azure sikkerhedskopi vaults og servere."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jimpark;markgal"/>


# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Administrere Azure sikkerhedskopi vaults og servere, der bruger den klassiske implementeringsmodel

> [AZURE.SELECTOR]
- [Ressourcestyring](backup-azure-manage-windows-server.md)
- [Klassisk](backup-azure-manage-windows-server-classic.md)

I denne artikel finder du en oversigt over de sikkerhedskopiering administrationsopgaver, der er tilgængelige via Azure klassisk portalen og Microsoft Azure Backup agent.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcestyring implementeringsmodel.

## <a name="management-portal-tasks"></a>Portalen administrationsopgaver
1. Log på [administrationsportalen til](https://manage.windowsazure.com).

2. Klik på **Gendannelsestjenester**, og derefter klikke på navnet på sikkerhedskopien samling af legitimationsoplysninger til at få vist siden Hurtig Start.

    ![Gendannelse](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Du kan se de tilgængelige administrationsopgaver ved at vælge indstillinger øverst på siden Hurtig Start.

![Administrere faner](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Dashboard
Vælg **Dashboard** til at få vist brugen oversigten for serveren. **Oversigt over brugen** indeholder:

- Antallet af Windows-servere, der er registreret til skyen
- Antallet af Azure virtuelle maskiner, der er beskyttet i skyen
- Den samlede lagerplads consumed i Azure
- Status for seneste job

Nederst i Dashboard kan du udføre følgende opgaver:

- **Administrer certifikat** – Hvis et certifikat, der blev brugt til at registrere serveren, og klik derefter bruges til at opdatere certifikatet. Hvis du bruger samling legitimationsoplysninger, skal du ikke bruge **Administrer certifikat**.
- **Slet** - sletter den aktuelle sikkerhedskopiering samling af legitimationsoplysninger. Hvis en sikkerhedskopi samling af legitimationsoplysninger er ikke længere bruges, kan du slette den for at frigøre lagerplads. **Slette** aktiveres kun, når alle registrerede servere er blevet slettet fra samling af legitimationsoplysninger.

![Sikkerhedskopiering dashboardopgaver](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Registrerede varer
Vælg **Registered elementer** til at få vist navnene på de servere, der er registreret til denne samling af legitimationsoplysninger.

![Registrerede varer](./media/backup-azure-manage-windows-server-classic/registered-items.png)

**Type** filteret som standard til Azure Virtual Machine. Vælg **Windows server** på rullelisten for at få vist navnene på de servere, der er registreret til denne samling af legitimationsoplysninger.

Her kan du udføre følgende opgaver:

- **Tillad nyt-registrering** – når denne indstilling er markeret for en server kan du bruge **Guiden registrering** i den lokale Microsoft Azure Backup agent til at registrere serveren med den ekstra samling af legitimationsoplysninger endnu en gang. Det være nødvendigt at registrere igen på grund af en fejl i certifikatet, eller hvis en server, der havde genopbygges.
- **Slet** - sletter en server fra sikkerhedskopien samling. Alle de lagrede data, der er knyttet til serveren, der slettes med det samme.

    ![Registrerede varer opgaver](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Beskyttede elementer
Vælg **Beskyttet elementer** til at få vist de elementer, der er sikkerhedskopieret fra serverne.

![Beskyttede elementer](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Konfigurere

Du kan vælge den relevante redundans datalager fra fanen **Konfigurer** . Det bedste tidspunkt at vælge indstillingen lagerplads redundans er rigtigt, når du har oprettet en samling af legitimationsoplysninger og alle computere er registreret til den.

>[AZURE.WARNING] Når et element er registreret til samling af legitimationsoplysninger, redundans datalager er låst og kan ikke ændres.

![Konfigurere](./media/backup-azure-manage-windows-server-classic/configure.png)

Se denne artikel kan finde flere oplysninger om [lagerplads redundans](../storage/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Microsoft Azure Backup agent for opgaver

### <a name="console"></a>Console

Åbn **Microsoft Azure Backup agent** (du kan finde det ved at søge computeren til *Microsoft Azure sikkerhedskopi*).

![Agent for sikkerhedskopiering](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Du kan udføre følgende administrationsopgaver fra **Handlinger** findes på højre for sikkerhedskopiering agent konsollen:

- Registrere Server
- Planlægge sikkerhedskopiering
- Sikkerhedskopiere nu
- Ændre egenskaber

![Agent console handlinger](./media/backup-azure-manage-windows-server-classic/console-actions.png)

>[AZURE.NOTE] **Gendanne Data**, du se under [Gendan filer til en Windows server eller Windows klientmaskinen](backup-azure-restore-windows-server.md).

### <a name="modify-an-existing-backup"></a>Ændre en eksisterende sikkerhedskopi

1. Klik på **Tidsplan sikkerhedskopi**i Microsoft Azure Backup agent.

    ![Planlægge en sikkerhedskopiering af Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)

2. I **Guiden Sikkerhedskopiering tidsplan** Lad indstillingen **Foretag ændringer til sikkerhedskopiering elementer eller klokkeslæt** er markeret, og klik på **Næste**.

    ![Ændre en planlagt sikkerhedskopiering](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)

3. Hvis du vil tilføje eller ændre elementer, skal du klikke på **Tilføj elementer**på skærmbilledet **Vælg elementer til sikkerhedskopi** .

    Du kan også angive **Indstillinger for udelukkelse** fra denne side i guiden. Hvis du vil udelade filer eller filtyper Læs fremgangsmåden for at tilføje [udelukkelse indstillinger](#exclusion-settings).

4. Vælg de filer og mapper, du vil sikkerhedskopiere, og klik på **OK**.

    ![Tilføje elementer](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)

5. Angiv **tidsplanen for sikkerhedskopiering** , og klik på **Næste**.

    Du kan planlægge dagligt (i op til 3 gange om dagen) eller ugentlig sikkerhedskopier.

    ![Angiv din tidsplan for sikkerhedskopiering](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

    >[AZURE.NOTE] Angive tidsplanen for sikkerhedskopiering er beskrevet i detaljer i denne [artikel](backup-azure-backup-cloud-as-tape.md).

6. Vælg den **Opbevaringspolitik** for sikkerhedskopien, og klik på **Næste**.

    ![Vælg din opbevaringspolitik](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)

7. Gennemse oplysningerne på skærmen for **bekræftelse** , og klik på **Udfør**.

8. Når guiden er færdig med at oprette **planen for sikkerhedskopiering**, skal du klikke på **Luk**.

    Efter at have ændret beskyttelse, kan du bekræfte, at sikkerhedskopiering der udløser korrekt ved at gå til fanen **job** og bekræfter, afspejles ændringer i de sikkerhedskopieringsjob.

### <a name="enable-network-throttling"></a>Aktivere netværk (throttling)  
Azure Backup agent indeholder en Throttling fane, hvor du kan styre, hvordan netværksbåndbredde bruges under dataoverførsel. Dette objekt kan være nyttigt, hvis du vil sikkerhedskopiere data under arbejdstimer, men ikke vil sikkerhedskopieringen forstyrrer andre internettrafik. Begrænsning af data gælder filoverførsel for at sikkerhedskopiere og gendanne aktiviteter.  

Sådan aktiveres (throttling):

1. Klik på **Skift egenskaber**i **Backup agent**.

2. Marker afkrydsningsfeltet **Aktiver brug af internetbåndbredde (throttling) for sikkerhedskopiering** .

    ![Netværk (throttling)](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)

3. Når du har aktiveret (throttling), skal du angive den tilladte båndbredde til sikkerhedskopiering dataoverførsel under **arbejdstimer** og **ikke - arbejdstid**.

    Værdierne i de båndbredde begynde på 512 kilobyte per sekund () og kan gå op til 1023 MB sekundet (Mbps). Du kan også angive start og Afslut for **arbejdstimer**, og hvilke dage i ugen betragtes som arbejde dage. Tidspunktet uden for det angivne antal arbejdstimer anses for ikke-arbejdstid.

4. Klik på **OK**.

## <a name="exclusion-settings"></a>Indstillinger for udelukkelse

1. Åbn **Microsoft Azure Backup agent** (du kan finde det ved at søge computeren til *Microsoft Azure sikkerhedskopi*).

    ![Åbn sikkerhedskopiering agent](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

2. Klik på **Tidsplan sikkerhedskopi**i Microsoft Azure Backup agent.

    ![Planlægge en sikkerhedskopiering af Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)

3. I guiden tidsplan sikkerhedskopiering Lad indstillingen **Foretag ændringer til sikkerhedskopiering elementer eller klokkeslæt** er markeret, og klik på **Næste**.

    ![Ændre en tidsplan](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)

4. Klik på **udeladelser indstillinger**.

    ![Vælg elementer til at udelukke](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)

5. Klik på **Tilføj udelukkelse**.

    ![Tilføje udeladelser](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)

6. Vælg placeringen, og klik derefter på **OK**.

    ![Vælg en placering til udelukkelse](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)

7. Tilføje filtypenavnet i feltet **Filtype** .

    ![Udelade efter filtype](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Tilføje et .mp3 filtypenavn

    ![Eksempel på fil af typen](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Hvis du vil tilføje et andet filtypenavn, skal du klikke på **Tilføj udelukkelse** og angive en anden filtypen (tilføje filtypenavnet .jpg).

    ![Eksempel på en anden fil typen](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)

8. Klik på **OK**, når du har tilføjet alle udvidelser.

9. Forsæt med guiden tidsplan sikkerhedskopiering ved at klikke på **Næste** indtil **bekræftelsessiden**og derefter klikke på **Udfør**.

    ![Udelukkelse bekræftelse](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Næste trin
- [Gendanne Windows Server eller Windows-klient fra Azure](backup-azure-restore-windows-server.md)
- Hvis du vil vide mere om Azure sikkerhedskopi, se [Oversigt over sikkerhedskopiering af Azure](backup-introduction-to-azure-backup.md)
- Besøg [Azure sikkerhedskopiering Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933)
