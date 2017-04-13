<properties
    pageTitle="Nemt at anvende installation og administration i Azure Batch | Microsoft Azure"
    description="Brug funktionen programmet pakker i Azure batchen til nemt at administrere flere programmer og versioner til installation på batchen beregne noder."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Program-installation med Azure batchen programpakker

Funktionen programmet pakker i Azure batchen indeholder nem håndtering af opgave programmer og deres udrulning af computernoderne i din gruppe. Du kan overføre og administrere flere versioner af de programmer, køres opgaverne, herunder deres støttefiler med-programpakker. Du kan derefter automatisk installere en eller flere af disse programmer til noderne Beregn i din gruppe.

I denne artikel lærer du, hvordan du overfører og administrere programpakker i portalen Azure. Du derefter lære, hvordan du installerer dem på en samling Beregn noder med [Batchen .NET] [ api_net] bibliotek.

> [AZURE.NOTE] Funktionen programmet pakker, der er beskrevet her tilsidesætter funktionen "Batchen Apps" tilgængelig i tidligere versioner af tjenesten.

## <a name="application-package-requirements"></a>Krav til pakke

[Kæde en Azure-lager konto](#link-a-storage-account) skal du til kontoen batchen bruge programpakker.

Funktionen program-pakker, der beskrives i denne artikel er kompatible *kun* med batchen grupper, der er oprettet efter 10 marts 2016. Programpakker installeres ikke for at beregne noder i grupper, der er oprettet før denne dato.

Denne funktion blev introduceret i [Batchen REST-API] [ api_rest] version 2015-12-01.2.2 og tilsvarende [Batchen .NET] [ api_net] bibliotek version 3.1.0. Vi anbefaler, at du altid bruger den nyeste version af API, når du arbejder med batchen.

> [AZURE.IMPORTANT] På nuværende tidspunkt understøtter kun *CloudServiceConfiguration* grupper programpakker. Du kan ikke bruge programpakker i grupper, der er oprettet ved hjælp af VirtualMachineConfiguration billeder. I afsnittet [virtuelt konfiguration](batch-linux-nodes.md#virtual-machine-configuration) af [klargøring Linux beregne knuder på Azure batchen grupper](batch-linux-nodes.md) kan finde flere oplysninger om de to forskellige konfigurationer.

## <a name="about-applications-and-application-packages"></a>Om programmer og -programpakker

Et *program* i Azure batchen refererer til et sæt af versionsnummer binære filer, der kan hentes automatisk til noderne Beregn i din gruppe. En *programpakke* refererer til et *bestemt* de binære tal og repræsenterer en bestemt *version* af programmet.

![Overordnet diagram over programmer og -programpakker][1]

### <a name="applications"></a>Programmer

Et program i Batch indeholder en eller flere programmer pakker og angiver konfigurationsindstillinger for programmet. Et program kan for eksempel angive standardversionen for programmet pakke til at installere på Beregn knuder og om dens pakker kan opdateres eller slettes.

### <a name="application-packages"></a>Programpakker

En programpakke er en .zip-fil, der indeholder de binære filer programmet og supplerende filer, der kræves til udførelse af dine opgaver. Hver programpakke repræsenterer en bestemt version af programmet.

Du kan angive programpakker på niveauet for puljen og opgave. Du kan angive en eller flere af disse pakker og (valgfrit) en version, når du opretter en gruppe eller opgave.

* **Puljen programpakker** er installeret på *hver* node i puljen. Programmer er installeret, når en node sammenkæder en gruppe, og når det er genstartet eller reimaged.

    Puljen programpakker er relevant, når alle noder i en gruppe udføre et job opgaver. Når du opretter en gruppe, og du kan tilføje eller opdatere en eksisterende gruppe pakker, kan du angive en eller flere programpakker. Hvis du opdaterer en eksisterende gruppe programpakker, skal du genstarte dens noder for at installere den nye pakke.

* **Opgave-programpakker** installeres kun til en beregningsnode, der er planlagt til at køre en opgave, lige før du kører opgavens kommandolinjen. Hvis det angivne programpakke og version er allerede på noden, det er ikke genaktiveres, og den eksisterende pakke bruges.

    Opgave-programpakker er nyttig i delt puljen miljøer, hvor forskellige job køres på en gruppe og puljen slettes ikke, når en sag er afsluttet. Hvis din sag har mindre opgaver end noder i puljen, kan opgave-programpakker minimere dataoverførsel, da din programmet installeres kun for noderne, der kører opgaver.

    Andre scenarier, der kan få glæde af opgave-programpakker er job, der bruger et særligt stort program, men for et lille antal opgaver. En forbehandling fase eller en Flet opgave, hvor programmet forbehandling eller Flet er kraftigt.

> [AZURE.IMPORTANT] Der er begrænsninger på antallet af programmer og -programpakker inden for en Batch-konto, samt den maksimale programmet pakke størrelse. Se [kvoter og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md) få mere at vide om disse begrænsninger.

### <a name="benefits-of-application-packages"></a>Fordele ved programpakker

Programpakker kan forenkle koden i din batchen løsning og sænke de faste omkostninger til at administrere de programmer, der køres opgaverne.

Din puljen start opgave nødvendigt ikke at angive en lang liste over individuelle ressourcefiler til at installere på noderne. Du behøver at administrere flere versioner af din programfiler i Azure-lager eller på din noder manuelt. Og du behøver ikke at bekymre dig om at generere [SAS URL-adresser](../storage/storage-dotnet-shared-access-signature-part-1.md) til at give adgang til filer i din lagerplads konto. Batchen fungerer i baggrunden med Azure-lager til at gemme-programpakker og installere dem for at beregne noder.

## <a name="upload-and-manage-applications"></a>Overfør og Administrer programmer

Du kan bruge [Azure portal] [ portal] eller biblioteket [Batchen Management .NET](batch-management-dotnet.md) til at administrere programpakker i kontoen batchen. I de næste par afsnit VI først sammenkæde et firma lager og derefter diskuterer, hvordan du tilføjer programmer og -pakker og administration af dem ved hjælp af portal.

### <a name="link-a-storage-account"></a>Sammenkæde et firma lagerplads

Hvis du vil bruge programpakker, skal du først sammenkæde et firma Azure-lager til kontoen batchen. Hvis du endnu ikke har konfigureret en konto lagerplads for kontoen batchen, vises en advarsel, første gang du klikker på feltet **programmer** i bladet **batchen konto** på Azure-portalen.

> [AZURE.IMPORTANT] Batch aktuelt understøtter *kun* kontotypen **generelle formål** lagerplads som beskrevet i trin 5, [Opret en konto med lagerplads](../storage/storage-create-storage-account.md#create-a-storage-account), i [om Azure lagerplads konti](../storage/storage-create-storage-account.md). Når du sammenkæder en Azure-lager konto til din batchen konto, link *kun* en **generelle formål** lagerplads konto.

![Advarsel om ingen lagerplads serverkonto konfigureres i Azure portal][9]

Tjenesten batchen bruger kontoen tilknyttede lager til opbevaring og hentning af programpakker. Når du har sammenkædet de to konti, kan batchen automatisk installere de pakker, der er gemt i den sammenkædede lagerplads konto til din Beregn noder. Klik på **Kontoindstillinger for lagerplads** på bladet **Advarsel** , og klik derefter på **Lagerplads konto** på bladet **Lagerplads konto** til at sammenkæde et firma lagerplads til kontoen batchen.

![Vælg lagerplads konto blade Azure-portalen][10]

Vi anbefaler, at du opretter en lagerplads konto *specifikt* til brug med kontoen batchen, og vælg det her. Få mere at vide om, hvordan du opretter en lagerplads konto, kan du se "Oprette en lagerplads konto" i [om Azure lagerplads konti](../storage/storage-create-storage-account.md). Når du har oprettet en lagerplads-konto, kan du derefter knytte den til kontoen batchen ved hjælp af bladet **Lagerplads konto** .

> [AZURE.WARNING] Da batchen bruger Azure-lager til at gemme dine programpakker, er du [betale som normalt] [ storage_pricing] for Bloker blob-data. Sørg for at overveje størrelsen og antallet af din programpakker, og fjern frarådede pakker for at minimere omkostninger med jævne mellemrum.

### <a name="view-current-applications"></a>Få vist aktuelle programmer

Klik på menupunktet **programmer** i menuen til venstre mens du får vist bladet **batchen konto** for at få vist programmerne på din batchen konto.

![Programmer felt][2]

Dette åbner bladet **programmer** :

![Listen programmer][3]

Bladet **programmer** viser ID for hvert program i din konto og følgende egenskaber:

* **Pakker**– antallet versioner, der er knyttet til dette program.
* **Standardversion**– den version, der skal installeres, hvis du ikke angiver en version, når du indstiller programmet for en gruppe. Denne indstilling er valgfrit.
* **Tillad opdateringer**– den værdi, der angiver, om pakke opdaterer, sletninger og tilføjelser er tilladt. Hvis dette er indstillet til **Nej**, er pakke opdateringer og sletninger deaktiveret for programmet. Kun nye programmet pakke versioner kan tilføjes. Standard er **Ja**.

### <a name="view-application-details"></a>Se detaljer om programmet

Klik på et program i bladet **programmer** til at åbne bladet, der indeholder detaljerne for det pågældende program.

![Oplysninger om programmet][4]

Du kan konfigurere følgende indstillinger for dit program i bladet programmet detaljer.

* **Tillad opdateringer**– Angiv, om dens programpakker kan opdateret eller slettet. Se "Opdatere eller slette en programpakke" senere i denne artikel.
* **Standardversionen**– Angiv en standard-programpakke til at udrulle for at beregne noder.
* **Vist navn**– Angiv en "brugervenlig" navn, din løsning kan bruge, når det viser oplysninger om programmet, f.eks i Brugergrænsefladen til en tjeneste, du angiver, at dine kunder gennem batchen batchen.

### <a name="add-a-new-application"></a>Tilføje et nyt program

Føj en programpakke for at oprette et nyt program, og Angiv et nyt, entydigt program-ID. Den første programpakke, du tilføjer med det nye program-ID kan også oprette det nye program.

Klik på **Tilføj** på bladet **programmer** til at åbne bladet **nyt program** .

![Ny programmet blade Azure-portalen][5]

Bladet **nyt program** indeholder følgende felter for at angive indstillinger for nyt program og programpakke.

**Program-id**

Dette felt angiver ID'ET for dit nye program, som er underlagt de almindelige Azure batchen ID valideringsregler:

* Kan indeholde enhver kombination af alfanumeriske tegn, herunder bindestreger og understregninger.
* Må ikke indeholde mere end 64 tegn.
* Skal være entydig i batchen kontoen.
* Er tilfælde bevarelse og sagsstyring følsomt.

**Version**

Angiver versionen af programpakken, du sender. Versionsstrenge er underlagt følgende valideringsregler:

* Kan indeholde enhver kombination af alfanumeriske tegn, herunder bindestreger, understregningstegn og perioder.
* Må ikke indeholde mere end 64 tegn.
* Skal være entydig i programmet.
* Bogstav bevaring og bogstaver.

**Programpakke**

Dette felt angiver .zip-fil, der indeholder de binære filer programmet og supplerende filer, der kræves til at køre i programmet. Klik på feltet **Vælg en fil** eller mappeikonet for at gå til, og vælg en .zip-fil, der indeholder dine programfiler.

Når du har valgt en fil, skal du klikke på **OK** for at starte overførslen til Azure-lager. Når handlingen overførslen er fuldført, får du besked og bladet lukkes. Denne handling kan tage noget tid, afhængigt af størrelsen på den fil, du sender og hastigheden for din netværksforbindelse.

> [AZURE.WARNING] Luk først bladet **nyt program** , når handlingen overførslen er fuldført. Hvis du gør det stopper overførslen.

### <a name="add-a-new-application-package"></a>Tilføje en ny programpakke

Vælg et program i bladet **programmer** for at tilføje en ny version af programmet-pakken til et eksisterende program skal, skal du klikke på **pakker**og derefter klikke på **Tilføj** for at åbne bladet **Tilføj pakke** .

![Tilføje programmet pakke blade Azure-portalen][8]

Som du kan se felterne svarer på bladet **nyt program** , men feltet **program-id** er deaktiveret. Som du gjorde for det nye program, og angiver **versionen** til din nye pakke, gå til **programpakke** .zip-filen, og klik derefter på **OK** for at overføre pakken.

### <a name="update-or-delete-an-application-package"></a>Opdatere eller slette en programpakke

For at opdatere eller slette en eksisterende programpakke, Åbn bladet detaljer for programmet, klik på **pakker** til at åbne bladet **pakker** , klik på **ellipsen** i rækken for den programpakke, du vil ændre og vælge den handling, du vil udføre.

![Opdatere eller slette pakke Azure-portalen][7]

**Opdater**

Når du klikker på **Opdater**, vises bladet *Update package* . Denne blade ligner bladet *nye programpakke* dog feltlisten pakke er aktiveret, så du kan angive en ny ZIP-fil til at overføre.

![Opdatere pakke blade Azure-portalen][11]

**Slet**

Når du klikker på **Slet**, du bliver bedt om at bekræfte sletningen af versionerne pakke og batchen sletter pakken fra Azure-lager. Hvis du sletter standardversionen af et program, fjernes indstillingen **standardversionen** for programmet.

![Slette tjenesteprogram][12]

## <a name="install-applications-on-compute-nodes"></a>Installere programmer på Beregn noder

Nu, hvor du har set hvordan du administrerer programpakker ved hjælp af Azure portal, kan vi diskuterer, hvordan du kan installere dem for at beregne noder og køre dem med batchopgaver.

### <a name="install-pool-application-packages"></a>Installere puljen programpakker

For at installere en programpakke på alle Beregn noder i en gruppe skal angive et eller flere programmer pakke *referencer* til puljen. De programpakker, du angiver for en gruppe er installeret på hver beregningsnode når noden sammenkæder puljen, og når noden genstartet eller reimaged.

Angiv en eller flere [CloudPool]i batchen .NET[net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] når du opretter en ny gruppe eller til en eksisterende gruppe. [ApplicationPackageReference] [ net_pkgref] klasse angiver en program-ID og version for at installere på en samling beregne noder.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Hvis en programmet pakke installation af en eller anden grund ikke lykkes, tjenesten batchen markerer node [ubrugeligt][net_nodestate], og ingen opgaver planlægges til udførelse af på noden. I dette tilfælde skal du **genstarte** noden til reinitiate pakke installationen. Genstart noden, gør det også muligt, opgaveplanlægningen igen på noden.

### <a name="install-task-application-packages"></a>Installere opgave programpakker

Svarer til en gruppe, du angiver programmet pakke *referencer* for en opgave. Når en opgave er planlagt til at køre på en node, er pakken hentede og udpakkede lige før opgavens kommandolinjen udføres. Hvis en bestemt pakke og version er allerede installeret på noden, pakken er overført ikke, og den eksisterende pakke bruges.

For at installere en programpakke opgave skal du konfigurere opgavens [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] egenskab:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Udføre de installerede programmer

De pakker, som du har angivet for en gruppe eller opgave hentede og udpakkede til en navngivet mappe i den `AZ_BATCH_ROOT_DIR` for noden. Batchen opretter også en miljøvariablen, der indeholder stien til den mappe med navn. Din opgave kommandolinjer bruge miljøvariablen når der henvises til programmet på noden. Variablen er i følgende format:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`og `version` er værdier, der svarer til den program og pakke version, du har specificeret til installation. Eksempelvis hvis du angav versionen 2.7 af programmet *blender* skal være installeret, skal din opgave kommandolinjer bruger miljøvariablen til at få adgang til filerne:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Hvis du angiver en standardversion for et program, kan du udelade version suffikset. Eksempelvis hvis du angiver "2,7" som standardversionen for programmet *blender*, dine opgaver kan referere til miljøvariablen følgende og de skal udføre version 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Følgende kodestykke viser et eksempel opgave kommandolinjen, der åbner standardversionen af programmet *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Se [miljøindstillinger for opgaver](batch-api-basics.md#environment-settings-for-tasks) i [batchen funktionsoversigt](batch-api-basics.md) finde flere oplysninger om Beregn node miljøindstillinger.

## <a name="update-a-pools-application-packages"></a>Opdatere en samling programpakker

Hvis en eksisterende gruppe, som allerede er blevet konfigureret med en programpakke, kan du angive en ny pakke til puljen. Hvis du angiver en ny pakke reference til en gruppe, gælder følgende:

* Alle nye noder, der sammenføjes puljen og en eksisterende node, som er genstartet eller reimaged installeres den nyligt angivne pakke.
* Beregne noder, der allerede findes i puljen, når du opdaterer pakke referencer ikke installerer nye programpakke automatisk. Disse beregne noder skal genstartes eller reimaged for at hente den nye pakke.
* Når en ny pakke er installeret, afspejler de oprettede miljøvariabler nye programmet pakke referencer.

I dette eksempel har eksisterende puljen version 2.7 af programmet *blender* konfigureret som et af dens [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. For at opdatere den puljen noder med version 2.76b, skal du angive en ny [ApplicationPackageReference] [ net_pkgref] med den nye version, og Anvend ændringen.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nu, hvor den nye version er konfigureret, har en *Ny* node, der forbinder puljen version 2.76b anvendes på den. Genstart for at installere 2.76b på noderne er *allerede* i puljen, eller reimage dem. Bemærk, at genstartet noder vil bevare filer fra tidligere installationer af pakke.

## <a name="list-the-applications-in-a-batch-account"></a>Liste over programmerne på en batchen konto

Du kan få vist programmerne og deres pakker i en batchen konto ved hjælp af [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] metode.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Afrunder

Du kan hjælpe dine kunder, Vælg programmerne til deres arbejde, og Angiv den nøjagtige version skal bruges, når behandling af sager med din batchen aktiverede tjeneste med programpakker. Du kan også angive muligheden for, at dine kunder til at overføre og registrere deres egne programmer i din tjeneste.

## <a name="next-steps"></a>Næste trin

* [Batchen REST-API] [ api_rest] understøtter også til at arbejde med programpakker. For eksempel se [applicationPackageReferences] [ rest_add_pool_with_packages] element i [Tilføj en gruppe til en konto] [ rest_add_pool] oplysninger om at angive pakker der skal installeres ved hjælp af REST-API. Se [programmer] [ rest_applications] få mere at vide om, hvordan du får oplysninger om programmet ved hjælp af batchen REST-API.

* Lær, hvordan du kan [administrere Azure batchen konti og kvoter med batchen Management .NET](batch-management-dotnet.md). [Batchen Management .NET] [ api_net_mgmt] bibliotek kan aktivere konto oprettelsen og sletningen funktioner for din batchen program eller en tjeneste.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Programmet pakker overordnet diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Programmer flise i Azure-portalen"
[3]: ./media/batch-application-packages/app_pkg_03.png "Programmer blade Azure-portalen"
[4]: ./media/batch-application-packages/app_pkg_04.png "Programmet detaljer blade Azure-portalen"
[5]: ./media/batch-application-packages/app_pkg_05.png "Ny programmet blade Azure-portalen"
[7]: ./media/batch-application-packages/app_pkg_07.png "Opdatere eller slette pakker rullemenuen Azure-portalen"
[8]: ./media/batch-application-packages/app_pkg_08.png "Ny programmet pakke blade Azure-portalen"
[9]: ./media/batch-application-packages/app_pkg_09.png "Ingen sammenkædede lagerplads konto påmindelser"
[10]: ./media/batch-application-packages/app_pkg_10.png "Vælg lagerplads konto blade Azure-portalen"
[11]: ./media/batch-application-packages/app_pkg_11.png "Opdatere pakke blade Azure-portalen"
[12]: ./media/batch-application-packages/app_pkg_12.png "Slette pakke bekræftelsesdialogboks Azure-portalen"
