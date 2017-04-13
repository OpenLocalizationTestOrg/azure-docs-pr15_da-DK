<properties
    pageTitle="Overvåge og administrere en elastiske database puljen ved hjælp af Azure portal | Microsoft Azure"
    description="Lær, hvordan man bruger portalen Azure og SQL-Database indbyggede intelligence til at administrere, overvåge og højre-størrelse en SVG elastiske database pulje vil optimere databasens ydeevne og administrere omkostninger."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Overvåge og administrere en elastiske database puljen ved hjælp af Azure portal

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Du kan bruge portalen Azure til at overvåge og administrere en elastiske database puljen og databaser i puljen. Fra portalen, kan du overvåge anvendelsen af en elastiske puljen og databaser inden for denne gruppe. Du kan også foretage et sæt af ændringer til din elastiske puljen og sende alle ændringer på samme tid. Disse ændringer omfatter at tilføje eller fjerne databaser, ændre indstillingerne for elastiske puljen eller ændring af databaseindstillinger for din.

I nedenstående illustration vises et eksempel elastiske puljen. Visningen indeholder:

*  Diagrammer til at overvåge ressource brugen af både elastiske puljen og de databaser, der er indeholdt i puljen.
*  Knappen **Konfigurer** puljen til at foretage ændringer til elastiske puljen.
*  Knappen **Opret database** som opretter en ny database og føjer den til den aktuelle elastiske puljen.
*  Elastiske job, som hjælper dig med at administrere stort antal databaser ved at køre Transact-SQL-scripts mod alle databaser på en liste.

![Gruppen Vis][2]

Hvis du vil gennemgå trinnene i denne artikel, skal du bruge en SQL server på Azure med mindst én database og en elastiske puljen. Hvis du ikke har en elastiske puljen, kan du se [oprette en gruppe](sql-database-elastic-pool-create-portal.md); Hvis du ikke har en database, kan du se [SQL-database selvstudium](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Elastiske puljen overvågning

Du kan gå til en bestemt samling at få vist dets ressourceforbrug mere effektivt. Puljen er som standard konfigureret til at vise lager og eDTU forbrug for den seneste time. Diagrammet kan være konfigureret til at vise forskellige måleenheder over forskellige tidsvinduer.

1. Vælg en pulje til at arbejde med.
2. Under **Elastiske puljen overvågning** er et diagram med navnet **Ressourceforbrug mere effektivt**. Klik på diagrammet.

    ![Elastiske puljen overvågning][3]

    Bladet **metrisk** åbnes, der viser en detaljeret visning af den angivne målepunkter over den angivne tidsramme.   

    ![Metriske blade][9]

### <a name="to-customize-the-chart-display"></a>Tilpasse visningen af diagrammet

Du kan redigere diagrammet og bladet metriske for at få vist andre målepunkter som CPU procentdel, data EY procentdel og log EY procentsats, der bruges.

2. Klik på **Rediger**på bladet metriske.

    ![Klik på Rediger][6]

- Vælg et nyt tidsinterval (tidligere time, i dag, eller sidste uge) eller klikke på **brugerdefineret** for at vælge et datointerval i de sidste to uger i bladet **Redigere diagrammet** . Vælg diagramtype (streg eller linje) og derefter vælge ressourcerne til at overvåge.

> Bemærk: Kun målepunkter med den samme enhed kan vises i diagrammet på samme tid. Eksempelvis hvis du vælger "eDTU procentdel" vil derefter du kun kunne vælge andre metrik med procent som måleenhed.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Klik derefter på **OK**.


## <a name="elastic-database-monitoring"></a>Overvågning af elastiske database

Individuelle databaser kan også overvåges for potentielle problemer.

1. Under **Elastiske Database overvågning**er der et diagram, der viser omfanget af fem databaser. Som standard viser diagrammet de øverste 5 databaser i puljen af gennemsnitlige eDTU brugen i den seneste time. Klik på diagrammet.

    ![Elastiske puljen overvågning][4]

2. Bladet **Database ressource anvendelsen** vises. Dette giver en detaljeret visning af database-brugen i puljen. Brug af gitteret i den nederste del af bladet, kan du vælge en hvilken som helst databaser i puljen at få vist brugen i diagrammet (op til 5-databaser). Du kan også tilpasse vinduet målepunkter og det klokkeslæt, der vises i diagrammet ved at klikke på **Rediger diagram**.

    ![Database ressource anvendelsen blade][8]

### <a name="to-customize-the-view"></a>Tilpasse visningen

1. Klik på **Rediger diagram**i bladet **Database ressourceforbrug mere effektivt** .

    ![Klik på Rediger diagram](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Vælg et nyt tidsinterval (tidligere time eller seneste 24 timer) i bladet **redigere** diagrammet, eller klik på **brugerdefineret** for at vælge en anden dag i de seneste to uger skal vises.

    ![Klik på brugerdefineret](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Klik på **Sammenlign databaser ved** rullepilen for at vælge en anden metrikværdi skal bruges, når der sammenlignes databaser.

    ![Redigere diagrammet](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Markere databaser til at overvåge

Du kan finde bestemt databaser på listen database i bladet **Database ressource anvendelsen** ved at se gennem sider på listen eller ved at skrive navnet på en database. Brug afkrydsningsfeltet for at vælge databasen.

![Søge efter databaser til at overvåge][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Tilføje en besked til en ressource, puljen

Du kan tilføje regler til ressourcer, som sender mails til en person eller beskeder om strenge til URL-adressen slutpunkter, når ressourcen rammer en grænseværdi for anvendelsen, du har angivet.

> [AZURE.IMPORTANT]Ressource anvendelsen overvågning for elastiske grupper indeholder en mellemliggende mindst 20 minutter. Konfigurere beskeder på mindre end 30 minutter for elastiske grupper understøttes ikke i øjeblikket. Få påmindelser elastiske grupper med et punktum (parameter ved navn "-WindowSize" i PowerShell API) på mindre end 30 minutter muligvis ikke blive udløst. Sørg for, at alle beskeder, du definerer for elastiske grupper bruger et punktum (WindowSize) af 30 minutter eller mere.

**Sådan tilføjer du en besked til en ressource:**

1. Klik på **ressourcen anvendelsen** diagrammet for at åbne bladet **metrisk** skal du klikke på **Tilføj påmindelse**, og udfyld derefter oplysningerne i bladet **tilføje en besked om** (**ressource** er automatisk konfigureret til at være den gruppe, du arbejder med).
2. Skriv et **navn** og **Beskrivelse** , der identificerer den vigtige besked til du og modtagerne.
3. Vælg en **metrikværdi** , du vil give besked på listen.

    Diagrammet viser dynamisk ressource anvendelsen for denne metrikværdi for at hjælpe dig med at vælge en grænseværdi.

4. Vælg en **betingelse** (større end mindre end, osv.) og en **grænseværdi**.
5. Klik på **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Flytte en database til en elastiske puljen

Du kan tilføje eller fjerne databaser fra en eksisterende gruppe. For databaser kan være i andre grupper. Du kan kun tilføje databaser, der er på den samme logiske server.

1. Klik på **Konfigurer puljen**under **elastiske databaser** bladet til puljen.

    ![Klik på Konfigurer puljen][1]

2. Klik på **Føj til puljen**i bladet **Konfigurer puljen** .

    ![Klik på Føj til gruppe](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Vælg den database eller databaser til at føje til puljen i bladet **Tilføj databaser** . Klik derefter på **Vælg**.

    ![Vælg databaser for at tilføje](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    **Konfigurer puljen** blade viser nu den database, du har valgt for at tilføjes med dens status er indstillet til **Afventer**.

    ![Ventende puljen tilføjelser](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. **Konfigurer puljen blade**, klik på **Gem**.

    ![Klik på Gem](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Flytte en database fra en elastiske puljen

1. Vælg den database eller databaser for at fjerne i bladet **Konfigurer puljen** .

    ![listen over databaser](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Klik på **Fjern fra gruppe**.

    ![listen over databaser](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    **Konfigurer puljen** blade viser nu den database, du har valgt at blive fjernet med dens status er indstillet til **Afventer**.

    ![Preview database addition og fjernelse](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. **Konfigurer puljen blade**, klik på **Gem**.

    ![Klik på Gem](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Ændre indstillinger for ydelse af en samling

Som du overvåger ressource anvendelsen af en gruppe, kan du opdager, at du skal bruge nogle justeringer. Puljen skal måske en ændring i ydeevnen eller lagerplads begrænsningerne. Du vil muligvis ændre indstillingerne i gruppen. Du kan ændre opsætningen af puljen når som helst at få den bedste balance mellem ydeevne og omkostninger. Se [Hvornår skal en elastiske database puljen bruges?](sql-database-elastic-pool-guidance.md) kan finde flere oplysninger.

**Ændre eDTUs eller lagerplads for hver gruppe og eDTUs per database:**

1. Åbn bladet **Konfigurer puljen** .

    Brug enten skyderen til at ændre indstillingerne for puljen under **Indstillinger for elastiske puljen**.

    ![Elastiske puljen ressource anvendelsen](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Når indstillingen ændres, viser visningen de anslåede månedlige omkostninger efter ændringen.

    ![Opdatere puljen og nye månedlige pris](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Oprette og administrere elastiske job

Elastiske job kan du kører Transact-SQL-scripts i forhold til en hvilken som helst antal databaser i puljen. Du kan oprette nye opgaver, eller du kan administrere eksisterende sager ved hjælp af portalen.

![Oprette og administrere elastiske job][5]


Før du bruger job skal du installere elastiske job komponenter og angive dine legitimationsoplysninger. Se [Oversigt over elastiske database](sql-database-elastic-jobs-overview.md)kan finde flere oplysninger.

Se [Skalering ud med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer skala ud, skal du flytte data, forespørgsel, eller Opret transaktioner.



## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database elastiske puljen](sql-database-elastic-pool.md)
- [Oprette en gruppe elastiske database ved hjælp af portal](sql-database-elastic-pool-create-csharp.md)
- [Oprette en elastiske database puljen med PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Oprette en elastiske database puljen med C#](sql-database-elastic-pool-create-csharp.md)
- [Pris og ydeevne overvejelser i forbindelse med elastiske database grupper](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
