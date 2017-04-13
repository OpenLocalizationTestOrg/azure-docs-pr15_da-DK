<properties
    pageTitle="Klargøre og installere microservices forudsigeligt i Azure"
    description="Lær, hvordan du installerer et program, der består af microservices i Azure App Service som en enkelt enhed og i en mere forudsigelige måde ved hjælp af JSON ressource gruppeskabeloner og PowerShell scripting."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/06/2016"
    ms.author="cephalin"/>


# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Klargøre og installere microservices forudsigeligt i Azure #

Dette selvstudium viser, hvordan du klargør og installere et program, der består af [microservices](https://en.wikipedia.org/wiki/Microservices) i [Azure App Service](/services/app-service/) som en enkelt enhed og på en mere forudsigelige måde ved hjælp af JSON ressource gruppeskabeloner og PowerShell scripting. 

Når klargøring og implementere Høj skalerbarhed programmer, der består af meget frakoblet er microservices, repeterbarhed og forudsigelighed afgørende for succes. [Azure App Service](/services/app-service/) gør det muligt at oprette microservices, der omfatter webapps, mobilapps, API apps og logik apps. [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) gør det muligt at administrere alle microservices som en enhed, sammen med Ressourceafhængigheder som database og indstillinger for visning af fra kilden. Nu kan du installere en sådan program ved hjælp af JSON skabeloner og enkel PowerShell scripting. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-do"></a>Hvad du vil gøre ##

I dette selvstudium skal vil du installere et program, der omfatter:

-   To web apps (det vil sige to microservices)
-   En back-end SQL-Database
-   Indstillinger for forbindelsesstrenge og versionsstyring
-   Programmet viden, beskeder, autoskalering indstillinger

## <a name="tools-you-will-use"></a>Værktøjer, du vil bruge ##

I dette selvstudium skal du bruge følgende værktøjer. Da det ikke er fyldestgørende diskussion på værktøjer, jeg vil til at få det til slut scenario og kun give dig en kort introduktion til hver enkelt, og hvor du kan finde flere oplysninger om den. 

### <a name="azure-resource-manager-templates-json"></a>Azure ressourcestyring skabeloner (JSON) ###
 
Hver gang du opretter en web-app i Azure App Service, for eksempel bruges Azure ressourcestyring en JSON-skabelon til at oprette gruppen hele ressource med komponent ressourcer. En kompleks skabelon fra [Azure Marketplace](/marketplace) såsom [SVG WordPress](/marketplace/partners/wordpress/scalablewordpress/) app kan omfatte MySQL-database, lagerplads firmaer, App-serviceaftale, online sig selv regler for påmindelser, indstillinger, autoskalering indstillinger og flere, og alle disse skabeloner er tilgængelige for dig via PowerShell. Du kan finde oplysninger om, hvordan du kan hente og bruge disse skabeloner, [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md).

Yderligere oplysninger om Azure ressourcestyring skabelonerne, se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 til Visual Studio ###

Den nyeste SDK indeholder forbedringer til Ressourcestyring skabelon support i editoren JSON. Du kan bruge dette til hurtigt at oprette en ressource Gruppeskabelon fra bunden eller åbne en eksisterende JSON-skabelon (som en skabelon med hentede galleriet) til ændring, udfylde parameterfilen og selv installere ressourcegruppe direkte fra en Azure ressourcegruppe løsning.

Du kan finde flere oplysninger [Azure SDK 2.6 til Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 eller nyere ###

Begyndt i version 0.8.0, omfatter Azure PowerShell installationen modulet Azure ressourcestyring ud over modulet Azure. Denne nyt modul gør det muligt at oprette et script installation af grupper.

Du kan finde flere oplysninger i [Bruge PowerShell for Azure med Azure ressourcestyring](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure ressource Explorer ###

Dette [eksempel værktøj](https://resources.azure.com) gør det muligt at udforske JSON definitioner af alle ressourcegrupper i dit abonnement og de enkelte ressourcer. I værktøjet, kan du redigere JSON definitioner af en ressource, slette hele hierarkiet af ressourcer og opretter nye ressourcer.  De oplysninger, der er tilgængelige i dette værktøj er særdeles nyttigt til skabelon redigering, fordi den viser dig, hvilke egenskaber, du vil angive for en bestemt type ressource, de korrekte værdier, osv. Du kan endda oprette din ressourcegruppe i [Azure Portal](https://portal.azure.com/)og derefter undersøge dens JSON definitioner i værktøjet explorer kan hjælpe dig med templatize ressourcegruppen.

### <a name="deploy-to-azure-button"></a>Installere til Azure knap ###

Hvis du bruger GitHub til versionsstyring, kan du indsætte en [Implementer til Azure knap](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) til dine vigtige oplysninger om. MD, som muliggør en slå nøgle installationer brugergrænseflade til Azure. Mens du kan gøre dette for en enkelt WebApp, kan du udvide her for at aktivere installation af en hel ressourcegruppe ved at placere en azuredeploy.json fil på rod-lager. Denne JSON-fil, som indeholder skabelonen, ressource gruppe, der bruges af Implementer til Azure knap til at oprette ressourcegruppen. Du kan finde et eksempel eksemplet [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , som du skal bruge i dette selvstudium.

## <a name="get-the-sample-resource-group-template"></a>Hente skabelonen eksempel ressource gruppe ##

Så nu Lad os komme til den.

1.  Gå til [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service eksempel.

2.   Klik på **Implementer til Azure**i readme.md.
 
3.  Du er taget til webstedet, [installere azure](https://deploy.azure.com) og bedt om at inputparametre installation. Bemærk, at de fleste felter er udfyldt med lager navn og nogle vilkårlige strenge for dig. Du kan ændre alle felterne, hvis du vil, men de eneste ting, du skal angive er den administrative SQL Server-logon og adgangskoden, og klik derefter på **Næste**.
 
    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.  Klik derefter på **Installer** for at starte installationsprocessen. Når processen kører til afslutning, skal du klikke på http://todoapp*XXXX*. azurewebsites.net for at lede udløst programmet. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

    Brugergrænsefladen ville være en smule langsom, når du først har gå til det, fordi der lige er begyndt apps, men overbevise dig selv, at det er et fuldt funktionelt program.

5.  Tilbage på siden Installer skal du klikke på linket **Administrer** for at se det nye program i portalen Azure.

6.  Klik på linket ressource gruppe i rullemenuen **Essentials** . Bemærk også, at online allerede er tilsluttet GitHub lager under **Eksternt projekt**. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.  Bemærk, at der er allerede to webapps og en SQL-Database i ressourcegruppen i bladet ressource gruppe.

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
Alt, som du lige har set i et kort øjeblik er et fuldt udløst to-microservice program med alle komponenter, afhængigheder, indstillinger, database og fortløbende publicering, indstillet af et automatiseret organisering i Azure ressourcestyring. Alle Dette skete af to ting:

-   Installer Azure knap
-   azuredeploy.JSON i roden repo

Du kan installere denne samme program mange, hundredvis eller tusindvis af gange og har nøjagtigt samme konfiguration hver gang. Repeterbarhed og forudsigeligheden af denne fremgangsmåde gør det muligt at implementere Høj skalerbarhed programmer med øget og konfidensinterval.

## <a name="examine-or-edit-azuredeployjson"></a>Undersøge (eller redigere) AZUREDEPLOY. JSON ##

Nu Lad os se på hvordan GitHub lager er konfigureret. Du kan bruge JSON-editor i Azure .NET SDK, så hvis du ikke har allerede installeret [Azure .NET SDK 2.6](/downloads/), gør det nu.

1.  Klone [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) lageret ved hjælp af dine foretrukne ciffer værktøj. I skærmbilledet nedenfor, jeg gør dette i Team Explorer i Visual Studio-2013.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.  Åbn azuredeploy.json i Visual Studio rodwebstedet lager. Hvis du ikke kan se ruden JSON disposition, skal du installere Azure .NET SDK.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Jeg vil ikke beskrive alle oplysninger om JSON-formatet, men [Mere ressourcer](#resources) sektion indeholder links til at blive fortrolig ressource gruppe skabelonsprog. Her, vil jeg lige vise de interessante funktioner, der kan hjælpe dig med at komme i gang med at gøre din egen brugerdefinerede skabelon til appinstallation.

### <a name="parameters"></a>Parametre ###

Se nærmere på afsnittet parametre for at se, at de fleste af disse parametre er, hvad knappen **Implementer til Azure** beder dig om at skrive. Webstedet bag knappen **Implementer til Azure** udfylder input Brugergrænsefladen ved hjælp af parametre, der er defineret i azuredeploy.json. Disse parametre bruges i hele ressourcedefinitioner, som ressourcenavne, egenskabsværdier osv.

### <a name="resources"></a>Ressourcer ###

I noden ressourcer, kan du se, at 4 på øverste niveau ressourcer er defineret, herunder en SQL Server-forekomst, en App-serviceaftale og to webapps. 

#### <a name="app-service-plan"></a>App-serviceaftale ####

Lad os starte med en enkel rodniveau ressource i JSON. Klik på App-serviceaftale med navnet **[hostingPlanName]** til at fremhæve den tilsvarende JSON-kode i dispositionen JSON. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Bemærk, at den `type` element angiver strengen for en App Service plan (den hedder en serverfarm en lang, lang tid siden), og andre elementer og egenskaber er udfyldt med de parametre, der er defineret i JSON-filen, og denne ressource ikke har nogen indlejrede ressourcer.

>[AZURE.NOTE] Bemærk også, at værdien af `apiVersion` fortæller Azure, hvilken version af REST-API til at bruge JSON ressource definition med, og det kan påvirke, hvordan ressourcen, der skal formateres i den `{}`. 

#### <a name="sql-server"></a>SQL Server ####

Klik derefter på SQL Server ressourcen navnet **SQL Server** i JSON dispositionen.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
Vær opmærksom på følgende omkring den fremhævede JSON-kode:

-   Brug af parametre sikrer, at de ressourcer, der oprettede er med navnet og konfigureret på en måde, så de stemmer overens med hinanden.
-   SQL Server ressourcen har to indlejrede ressourcer, hver har en anden værdi for `type`.
-   De ressourcer, der er indlejret i `“resources”: […]`, hvor databasen og firewall-reglerne defineres, har en `dependsOn` element, som angiver ressource-ID på den ressource, rodniveau SQL Server. Dette fortæller Azure ressourcestyring "før du opretter denne ressource, der skal allerede findes en anden ressource; og hvis der er angivet andre ressourcen i skabelonen, oprette den første".

    >[AZURE.NOTE] Du kan finde detaljerede oplysninger om, hvordan du bruger den `resourceId()` fungerer, skal du se [Azure ressourcestyring skabelon funktioner](../resource-group-template-functions.md).

-   Effekten af de `dependsOn` element er, at Azure ressourcestyring kan ved, hvilke ressourcer kan oprettes parallelt og ressourcerne, der skal oprettes sekventielt. 

#### <a name="web-app"></a>WebApp ####

Nu, Lad os gå videre til de faktiske web-apps selv, som er mere kompliceret. Klik på [variables('apiSiteName')] web app i JSON dispositionen til at fremhæve dens JSON-kode. Du vil bemærke, at ting, du får meget mere interessant. Hertil, får jeg tale om funktionerne, én ad gangen:

##### <a name="root-resource"></a>Rod ressource #####

WebApp, afhænger af to forskellige ressourcer. Det betyder, at oprette Azure ressourcestyring WebApp, kun, når både App serviceaftale og SQL Server-forekomsten er oprettet.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>App-indstillinger #####

App-indstillinger er også defineret som en indlejret ressource.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

I den `properties` element til `config/appsettings`, du har to indstillinger i formatet `“<name>” : “<value>”`.

-   `PROJECT`er en [KUDU indstilling](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , der fortæller Azure-installation, hvilket projekt skal bruges i en med flere project Visual Studio-løsning. Jeg kan du se, hvordan senere versionsstyring er konfigureret, men da ToDoApp koden er i en med flere project Visual Studio-løsning, vi skal bruge denne indstilling.
-   `clientUrl`er blot en app, indstilling, der programkoden bruger.

##### <a name="connection-strings"></a>Forbindelsesstrenge #####

Forbindelse strengene defineres også som en indlejret ressource.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

I den `properties` element til `config/connectionstrings`, hver forbindelsesstreng også er defineret som et navn: værdi-par, med det specifikke format af `“<name>” : {“value”: “…”, “type”: “…”}`. For den `type` element, mulige værdier er `MySql`, `SQLServer`, `SQLAzure`, og `Custom`.

>[AZURE.TIP] Kør følgende kommando for en endelig liste over de forskellige strengtyper, i Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### <a name="source-control"></a>Versionsstyring #####

Indstillingerne for versionsstyring er også defineret som en indlejret ressource. Azure ressourcestyring bruger denne ressource til at konfigurere fortløbende udgivelse (se ting på `IsManualIntegration` senere) og også til at starte installationen af programmet kode automatisk under behandling af JSON-fil.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`og `branch` skal være ret intuitiv og skal pege på ciffer lager og navnet på den gren skal publiceres. Igen, er disse defineret af inputparametre. 

Bemærk i den `dependsOn` element, ud over web app ressourcen sig selv `sourcecontrols/web` også afhænger af `config/appsettings` og `config/connectionstrings`. Det skyldes, når `sourcecontrols/web` er konfigureret, Azure installationsprocessen automatisk forsøger at installere, opbygge og starte programkoden. Derfor indsætte denne afhængighed hjælper dig med at Kontrollér, at programmet har adgang til de nødvendige app-indstillinger og strenge før programkoden køres. 

>[AZURE.NOTE] Bemærk også, at `IsManualIntegration` er indstillet til `true`. Denne egenskab er nødvendigt i dette selvstudium, fordi du ikke rent faktisk ejer GitHub lager, og dermed kan faktisk tilladelsen til Azure til at konfigurere fortløbende udgivelse fra [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (dvs. skubbe automatisk lager opdateringer til Azure). Du kan bruge standardværdien `false` for det angivne lagringssted kun, hvis du har konfigureret ejerens GitHub legitimationsoplysninger i [Azure portal](https://portal.azure.com/) før. Det vil sige, hvis du har konfigureret versionsstyring til GitHub eller BitBucket for en app på [Azure Portal](https://portal.azure.com/) tidligere, vil ved hjælp af dine legitimationsoplysninger, derefter Azure huske legitimationsoplysningerne og bruge dem, når du installerer en app fra GitHub eller BitBucket i fremtiden. Men hvis du ikke allerede har gjort dette, installation af skabelonen JSON mislykkes når Azure ressourcestyring forsøger at konfigurere indstillinger for versionsstyring i online, fordi det ikke kan logge på GitHub eller BitBucket med lager ejerens legitimationsoplysninger.

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Sammenligne JSON-skabelon med udløst ressourcegruppe ##

Her kan du gå igennem alle den web Apps blade i [Azure-portalen](https://portal.azure.com/), men der er et andet værktøj, lige som nyttigt, hvis der ikke er mere. Gå til [Azure ressource Explorer](https://resources.azure.com) preview-værktøjet, som giver dig en JSON repræsentation af alle ressourcegrupperne i dine abonnementer, som de rent faktisk findes i den Azure backend-version. Du kan også se, hvordan den ressourcegruppe JSON hierarki i Azure svarer med hierarkiet i den skabelonfil, der bruges til at oprette den.

Når jeg går til værktøjet [Azure ressource Explorer](https://resources.azure.com) og Udvid noderne i Stifinder, kan jeg for eksempel se ressourcegruppen og de rodniveau ressourcer, der er indsamlet i henhold til deres respektive ressourcetyper.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Hvis du større detaljeringsgrad ned til en WebApp, bør du kunne se web app konfigurationsoplysninger ligner den under skærmbillede:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Igen, indlejrede ressourcer skal have et hierarki, der minder meget om dem i din JSON-skabelonfil, og du bør se app-indstillinger, strenge, osv., korrekt afspejles i ruden JSON. Fravær af indstillingerne her kan skyldes et problem med filen JSON og kan hjælpe dig med at foretage fejlfinding af skabelonfilen JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Installere skabelonen ressource gruppe dig selv ##

Knappen **Implementer til Azure** er velegnet, men det gør det muligt at installere skabelonen ressource gruppe i azuredeploy.json kun, hvis du allerede har installeret azuredeploy.json GitHub. Azure .NET SDK indeholder også værktøjer for dig at implementere en hvilken som helst JSON skabelonfil direkte fra din lokale computer. Følg nedenstående trin for at gøre dette:

1.  Klik på **filer**i Visual Studio > **Ny** > **Project**.

2.  Klik på **Visual C#** > **skyen** > **Azure ressourcegruppe**, klik derefter på **OK**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.  Vælg **Tom skabelon** **Vælg Azure skabelon**, og klik på **OK**.

4.  Træk azuredeploy.json til mappen **skabelon** for det nye projekt.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.  Fra Solution Explorer skal du åbne den kopierede azuredeploy.json.

6.  Kun med henblik på påvisning af, Lad os tilføje nogle almindelige programmet indsigt ressourcer til vores JSON-fil, ved at klikke på **Tilføj ressource**. Hvis du er lige interesseret i at implementere JSON-fil, skal du gå videre til Implementer trinnene.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.  Vælge **Program indsigt til Web Apps**, Sørg for en eksisterende App-tjeneste, planlægge og online er markeret, og klik derefter på **Tilføj**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

    Du får nu kunne se flere nye ressourcer, afhængigt af ressourcen, og hvad det betyder, har afhængigheder af serviceaftale, som App eller WebApp. Disse ressourcer er ikke aktiveret som deres eksisterende definition, og du vil ændre dette.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.  Klik på **appInsights Autoskalering** for at markere dens JSON-kode i dispositionen JSON. Dette er den skalering indstilling for din App Service-plan.

9.  I den fremhævede JSON-kode, skal du finde den `location` og `enabled` egenskaber og angive dem, som vist nedenfor.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10. Klik på **CPUHigh appInsights** for at markere dens JSON-kode i dispositionen JSON. Dette er en besked.

11. Find den `location` og `isEnabled` egenskaber og angive dem, som vist nedenfor. Gør det samme for de andre tre beskeder (lilla Pærer).

    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12. Nu er du klar til at installere. Højreklik på projektet, og vælg **Installer** > **Ny installation**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13. Log på din Azure-konto, hvis du ikke allerede har gjort det.

14. Vælg en eksisterende ressourcegruppe i dit abonnement eller oprette en ny, Vælg **azuredeploy.json**, og klik derefter på **Rediger parametre**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

    Du får nu kunne redigere alle de parametre, der er angivet i skabelonfilen i en flot tabel. Parametre, der definerer standardindstillinger for allerede standardværdierne og parametre, der definerer en liste over tilladte værdier vises som rullemenuerne.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15. Udfyld alle de tomme parametre, og brug [GitHub repo adressen til ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) i **repoUrl**. Klik derefter på **Gem**.
 
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

    >[AZURE.NOTE] Autoskalering er en funktion, der tilbydes i **Standard** lag eller nyere, og plan niveau beskeder er funktioner, der tilbydes i **grundlæggende** niveau eller nyere, skal du indstille parameteren **sku** til **Standard** eller **Premium** for at kunne se alle din nye App indsigt ressourcer lys op.
    
16. Klik på **installation**. Hvis du har valgt **gemme adgangskoder**, gemmes adgangskoden i parameter filer **i almindelig tekst**. Ellers skal du bliver bedt om at skrive adgangskoden til databasen i forbindelse med installationsprocessen.

Det var det! Nu, du har brug lige at gå til [Azure-portalen](https://portal.azure.com/) og værktøjet [Azure ressource Explorer](https://resources.azure.com) for at se de nye beskeder og Autoskalering indstillinger, der er føjet til din JSON installeret program.

Din trinene i dette afsnit gør hovedsageligt følgende:

1.  Forberedt skabelonfilen
2.  Oprettet en parameter filer for at gå med skabelonfilen
3.  Installeret skabelonfilen med Parameterfilen

Det sidste trin gøres nemt ved en PowerShell-cmdlet. For at se, hvad Visual Studio har, når den er installeret dit program skal du åbne Scripts\Deploy AzureResourceGroup.ps1. Der er en masse kode der, men jeg lige til at fremhæve den relevante kode, du vil installere skabelonfilen med parameterfilen.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Cmdletten sidste `New-AzureResourceGroup`, er den samme som faktisk udfører handlingen. Alt dette bør viser dig, ved hjælp af værktøj, det er relativt nemt at installere programmet skyen forudsigeligt. Hver gang du kører cmdlet på samme skabelon med den samme parameterfil, vil du opnå det samme resultat.

## <a name="summary"></a>Oversigt ##

I DevOps er repeterbarhed og forudsigeligheden taster til en hvilken som helst vellykket udrulning af en høj skalerbarhed programmet bestående af microservices. I dette selvstudium, har du installeret en to-microservice-programmet til Azure som en enkelt ressourcegruppe ved hjælp af skabelonen Azure ressourcestyring. Forhåbentlig, det har givet dig den viden, du skal bruge til at begynde at konvertere dit program i Azure til en skabelon, og som kan klargøre og installerer det forudsigeligt. 

## <a name="next-steps"></a>Næste trin ##

Finde ud af, hvordan du kan [anvende fleksible metoder og løbende publicere dit microservices program med øget](app-service-agile-software-development.md) og Avanceret installation teknikker som [flighting installation](app-service-web-test-in-production-controlled-test-flight.md) nemt.

<a name="resources"></a>
## <a name="more-resources"></a>Flere ressourcer ##

-   [Azure ressourcestyring skabelon sprog](../resource-group-authoring-templates.md)
-   [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)
-   [Azure ressourcestyring skabelon funktioner](../resource-group-template-functions.md)
-   [Installere et program med Azure ressourcestyring skabelon](../resource-group-template-deploy.md)
-   [Brug af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)
-   [Fejlfinding i forbindelse med ressourcegruppe installationer i Azure](../resource-manager-troubleshoot-deployments-portal.md)




 
