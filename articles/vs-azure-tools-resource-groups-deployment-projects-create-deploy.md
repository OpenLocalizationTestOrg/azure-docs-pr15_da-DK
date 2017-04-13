<properties
   pageTitle="Azure ressource gruppe Visual Studio projekter | Microsoft Azure"
   description="Brug Visual Studio til at oprette et gruppeprojekt Azure ressource og installere ressourcerne til Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="tomfitz" />

# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Oprette og implementere Azure ressourcegrupper via Visual Studio

Med Visual Studio og [Azure SDK](https://azure.microsoft.com/downloads/), kan du oprette et projekt, der installerer din infrastruktur og kode på Azure. Du kan for eksempel definere web host, websted og database til din app og implementere denne infrastruktur sammen med koden. Eller du kan definere et virtuelt, virtuelt netværk og lagerplads konto og installere denne infrastruktur sammen med et script, der er udført på Virtual Machine. **Azure ressourcegruppe** installation project gør det muligt at installere alle de nødvendige ressourcer i en enkelt, gentaget handling. Se [Oversigt over Azure ressourcestyring](azure-resource-manager/resource-group-overview.md)kan finde flere oplysninger om installation og administration af dine ressourcer.

Azure ressourcegruppe projekter indeholder Azure ressourcestyring JSON skabeloner, der definerer de ressourcer, som du installerer til Azure. Se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)for at få mere for at vide om elementerne i skabelonen ressourcestyring. Visual Studio gør det muligt at redigere disse skabeloner og indeholder værktøjer, der forenkler arbejde med skabeloner.

I dette emne installerer du en WebApp og SQL-Database. Men trinnene er næsten på samme måde for en hvilken som helst type ressource. Du kan som let at implementere en virtuel maskine og dens relaterede ressourcer. Visual Studio indeholder mange forskellige startskabeloner for implementering af almindelige scenarier.

I denne artikel viser Visual Studio 2015 opdatering 2 og Microsoft Azure SDK til .NET 2.9. Hvis du bruger Visual Studio 2013 med Azure SDK 2.9, er din oplevelse stort set det samme. Du kan bruge versioner af Azure SDK fra 2.6 eller nyere. din oplevelse af brugergrænsefladen kan dog være anderledes end den brugergrænseflade, der er vist i denne artikel. Vi anbefaler, at du installerer den nyeste version af [Azure SDK](https://azure.microsoft.com/downloads/) , før du starter trinnene. 

## <a name="create-azure-resource-group-project"></a>Oprette Azure ressourcegruppe project

I denne procedure opretter du en Azure ressourcegruppe projekt med en **Online + SQL** skabelon.

1. I Visual Studio, skal du vælge **filer**, **Nyt projekt**, Vælg **C#** eller **Visual Basic**. Klik på **skyen**, og vælg derefter **Azure ressourcegruppe** project.

    ![Skyen installation Project](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Vælg den skabelon, du vil installere til Azure ressourcestyring. Bemærk der er mange forskellige indstillinger, der er baseret på typen projekt, du vil installere. Vælg skabelonen **Online + SQL** i dette emne.

    ![Vælg en skabelon](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Den skabelon, du vælger er blot et udgangspunkt; Du kan tilføje og fjerne ressourcer for at opfylde scenariet.

    >[AZURE.NOTE] Visual Studio til at hente en liste over tilgængelige skabeloner online. Listen kan blive ændret.

    Visual Studio opretter en ressource gruppeprojekt installation for WebApp og SQL-database.

1. Udvid noderne i installation projektet for at se, hvad du har oprettet.

    ![vise noder](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Da vi valgte online + SQL skabelon i dette eksempel, kan du se følgende filer: 

  	|Filnavn|Beskrivelse|
  	|---|---|
  	|Installere AzureResourceGroup.ps1|En PowerShell-script, som udløser en PowerShell-kommandoer til at udrulle til Azure ressourcestyring.<br />**Bemærk!** Visual Studio bruger denne PowerShell-script til at installere din skabelon. De ændringer, du foretager til dette script påvirker installation i Visual Studio, så Vær opmærksom på.|
  	|WebSiteSQLDatabase.json|Skabelonen Ressourcestyring, der definerer infrastrukturen du vil anvende til Azure og de parametre, du kan angive under installationen. Den definerer også afhængigheder mellem ressourcerne, så Resource Manager installerer ressourcer i den rigtige rækkefølge.|
  	|WebSiteSQLDatabase.parameters.json|Parameterfilen, der indeholder værdier, der bruges til skabelonen. Du overfører i parameterværdier til at tilpasse de enkelte installationer.|

    Alle ressource gruppe installation projekter indeholder disse grundlæggende filer. Andre projekter kan indeholde flere filer for at understøtte andre funktioner.

## <a name="customize-the-resource-manager-template"></a>Tilpasse skabelonen ressourcestyring

Du kan tilpasse en installation project ved at ændre de JSON-skabeloner, der beskriver de ressourcer, du vil installere. JSON står for JavaScript Object Notation og er et fortløbende dataformat, som er nem at arbejde med. Filerne, JSON bruger et skema, som du henviser til øverst på hver fil. Hvis du vil forstå skemaet, kan du hente og analysere dem. Definerer skemaet, hvilke elementer der er gyldige, filtyper og formater for felter, de mulige værdier for specificeret værdier osv. Se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md)for at få mere for at vide om elementerne i skabelonen ressourcestyring.

Du kan arbejde på skabelonen ved at åbne **WebSiteSQLDatabase.json**.

Redigeringsprogrammet til Visual Studio indeholder værktøjer for at hjælpe dig med at redigere skabelonen ressourcestyring. **JSON** dispositionsvindue gør det nemt at se de elementer, der er defineret i skabelonen.

![vise JSON disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Vælge en af elementerne i dispositionen tager dig til del af skabelonen og fremhæver de tilsvarende JSON.

![navigere JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Du kan føje en ressource ved enten at vælge knappen **Tilføj ressource** øverst i vinduet JSON disposition eller ved at højreklikke **ressourcer** og vælge **Tilføj ny ressource**.

![føje ressource](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Vælg **Lagerplads konto** og give den et navn til dette selvstudium. Angiv et navn, der højst 11 tegn, og kun indeholder tal og små bogstaver.

![tilføje lagerplads](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Bemærk, ikke kun er den ressource, der er tilføjet, men også en parameter for type lagerplads konto og en variabel for navnet på kontoen, lagerplads.

![vise disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Parameteren **storageType** er foruddefinerede med tilladte filtyper og en standardtype. Du kan lade disse værdier eller redigere dem til det pågældende scenarie. Hvis du ikke vil alle til at installere en **Premium_LRS** lagerplads konto via denne skabelon skal du fjerne det fra de tilladte typer. 

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio indeholder også intellisense for at hjælpe dig med at forstå, hvilke egenskaber er tilgængelige, når du redigerer skabelonen. Gå til **HostingPlan** ressourcen for eksempel for at redigere egenskaberne for din App Service-plan, og Tilføj en værdi for **Egenskaber**. Bemærk Denne intellisense viser de tilgængelige værdier og indeholder en beskrivelse af den pågældende værdi.

![vise intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Du kan angive **numberOfWorkers** til 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## <a name="deploy-the-resource-group-project-to-azure"></a>Anvende ressourcegruppe-projektet til Azure

Nu er du klar til at udrulle projektet. Når du installerer et Azure ressourcegruppe projekt, kan du installere det til en Azure ressourcegruppe. Ressourcegruppen er en logisk gruppering af ressourcer, der deler en fælles livscyklus.

1. I genvejsmenuen for noden installation project, Vælg **Implementer** > **Ny installation**.

    ![Installere element i menuen ny installation](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    Dialogboksen **Installer til ressourcegruppe** vises.

    ![Installere ressourcegruppe dialogboksen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Vælge en eksisterende ressourcegruppe i feltet **ressourcegruppe** rullemenu eller oprette en ny. Åbn feltet **Ressourcegruppe** rullemenuen for at oprette en ressourcegruppe, og vælg **Opret ny**.

    ![Installere ressourcegruppe dialogboksen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    Dialogboksen **Opret ressourcegruppe** vises. Give gruppen et navn og en placering, og vælg knappen **Opret** .

    ![Ressourcegruppe dialogboksen Opret](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Rediger parametrene for installationen ved at vælge knappen **Rediger parametre** .

    ![Parametre knappen Rediger](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)

1. Angiv værdier for de tomme parametre, og vælg knappen **Gem** . Tom parametrene er **hostingPlanName**, **administratorLogin**, **administratorLoginPassword**og **Databasenavn**.

    **hostingPlanName** angiver et navn til den [serviceaftale, som App](./app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) til at oprette. 
    
    **administratorLogin** Angiver brugernavnet for SQL Server-administratoren. Brug ikke almindelige administrator navne som **systemadministratorens** eller **administrator**. 
    
    **AdministratorLoginPassword** angiver en adgangskode til SQL Server-administratoren. Indstillingen **Gem adgangskoder som almindelig tekst i parameterfilen** , der ikke er sikker; derfor Viderestil ikke Markér denne indstilling. Da adgangskoden ikke er gemt som almindelig tekst, skal du angive denne adgangskode igen under installationen. 
    
    **Databasenavn** angiver et navn til databasen til at oprette. 

    ![Rediger i dialogboksen parametre](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
1. Vælg knappen **Implementer** skal installeres projektet til Azure. En PowerShell-konsol åbnes uden for forekomsten Visual Studio. Angiv SQL Server-administratoradgangskoden i konsollen PowerShell, når du bliver bedt om. **PowerShell console kan være skjult bag andre elementer eller minimeret på proceslinjen.** Se efter denne konsol og vælge dem for at angive adgangskoden.

    >[AZURE.NOTE] Visual Studio anmode om at installere Azure PowerShell-cmdletterne. Du skal bruge Azure PowerShell-cmdletter for at installere ressourcegrupper. Hvis du bliver bedt om det, kan du installere dem.
    
1. Installationen kan tage et par minutter. I windows **Output** skal du se status for installationen. Når installationen er færdig, angiver en vellykket installation med lignende til den sidste meddelelse:

        ... 
        18:00:58 - Successfully deployed template 'c:\users\user\documents\visual studio 2015\projects\azureresourcegroup1\azureresourcegroup1\templates\websitesqldatabase.json' to resource group 'DemoSiteGroup'.


1. Åbn [Azure portal](https://portal.azure.com/) i en browser og log på din konto. Vælg **grupper** og ressourcegruppen du brug for at se ressourcegruppen.

    ![Vælg gruppe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Du kan se alle de ressourcer, der er installeret. Bemærk, at navnet på kontoen, lagerplads er ikke helt, hvad du angav, når du tilføjer denne ressource. Kontoen lagerplads skal være entydig. Skabelonen tilføjes automatisk en streng med tegn i det navn, du har angivet for at give et entydigt navn. 

    ![Vis ressourcer](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Hvis du foretager ændringer og vil Geninstaller projektet, Vælg den eksisterende ressourcegruppe i genvejsmenuen af Azure ressource gruppeprojekt. Vælg **Implementer**på genvejsmenuen, og vælg derefter den ressourcegruppe, du har installeret.

    ![Azure ressourcegruppe, der er installeret](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Installere kode med infrastrukturen

På dette tidspunkt, du har installeret infrastrukturen for din app, men der er ikke en faktisk kode, der er installeret med projektet. Dette emne viser, hvordan du installerer en WebApp og SQL-databasetabeller under installationen. Hvis du anvender en virtuel maskine i stedet for en web-app, vil du køre kode på computeren som en del af installationen. Processen til implementering af kode til en WebApp eller for at konfigurere en virtuel maskine er næsten på samme måde.

1. Føje et projekt til Visual Studio-løsning. Højreklik på løsningen, og vælg **Tilføj** > **Nyt projekt**.

    ![føje projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Tilføje en **ASP.NET-webprogram**. 

    ![tilføje WebApp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Vælg **MVC** , og fjern markeringen i feltet til **vært i skyen** , fordi gruppeprojekt ressource udfører denne opgave.

    ![Vælg MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Når Visual Studio har oprettet din online, kan du se begge projekter i løsningen.

    ![projekter vises](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Nu skal du kontrollere, at projektet ressource gruppe er bekendt med det nye projekt. Gå tilbage til projektet ressource gruppe (AzureResourceGroup1). Højreklik på **referencer** , og vælg **Tilføj Reference**.

    ![tilføje reference](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Vælg det web app-projekt, du har oprettet.

    ![tilføje reference](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Ved at tilføje en reference, du kædes ressource gruppe project web app projektet og konfigurere automatisk tre vigtige egenskaber. Du kan se disse egenskaber i vinduet **Egenskaber** for referencen.

      ![se reference](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
    
    Egenskaberne, er:

    - **Yderligere egenskaber, der** indeholder den web-installationspakke, arrangere placering, der rykkes til Azure-lager. Bemærk, at mappen (ExampleApp) og filen (package.zip). Du vil angive disse værdier som parametre, når du installerer app. 
    - **Medtag stien til filen** indeholder den sti, hvor pakken er oprettet. **Medtage destinationer** indeholder den kommando, der udfører installationen. 
    - Standardværdien for **opbygge; Pakke** aktiverer installationen til at opbygge og oprette en web-installationspakke (package.zip).  
    
    Du behøver ikke en Publicer profil, som installationen, bliver de nødvendige oplysninger fra filegenskaberne, når du opretter pakken.
      
1. Føje en ressource til skabelonen.

    ![føje ressource](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Denne gang vælge **Web Installer for Web Apps**. 

    ![tilføje web installere](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Geninstaller projektet ressource gruppe til ressourcegruppen. Nuværende tidspunkt er der nogle nye parametre. Du behøver ikke at angive værdier for **_artifactsLocation** eller **_artifactsLocationSasToken** , fordi Visual Studio genererer automatisk disse værdier. Men du skal angive mappe og filnavn til den sti, der indeholder installationspakken (vist som **ExampleAppPackageFolder** og **ExampleAppPackageFileName** på følgende billede). Indeholder de værdier, du fik vist tidligere i egenskaberne reference (**ExampleApp** og **package.zip**).

    ![tilføje web installere](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Vælg det domæne, installeret med denne ressourcegruppe for den **genstand lagerplads konto**.
    
1. Når installationen er færdig, skal du vælge din online i portalen. Vælg URL-adressen til at gå til webstedet.

    ![Gennemse websted](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Bemærk, at du har installeret standard ASP.NET-app.

    ![vise udløst app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Næste trin

- For at få mere for at vide om administration af dine ressourcer via portalen, se [ved hjælp af portalen Azure for at administrere dine Azure ressourcer](./azure-portal/resource-group-portal.md).
- Hvis du vil vide mere om skabeloner, se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
