<properties
   pageTitle="Udvikling og test miljøer | Microsoft Azure"
   description="Lær at bruge Azure ressourcestyring skabeloner til at hurtigt og ensartet oprette og slette udvikling og teste miljøer."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Udvikling og test miljøer i Microsoft Azure

Brugerdefinerede programmer, der normalt anvendes til flere udvikling og test miljøer før installation fremstilling. Når der oprettes miljøer lokalt, er dataressourcer enten udtaget eller allokeres til hver miljø til hvert program. Miljøerne indeholder ofte flere fysiske eller virtuelle maskiner med specifikke konfigurationer, der er installeret manuelt eller med komplekse automatiseringsscripts. Installationer ofte tage timer og medføre inkonsekvent konfigurationer på tværs af miljøer.

## <a name="scenario"></a>Scenarie ##

Når du klargør udvikling og testmiljøer i Microsoft Azure, betaler du kun for de ressourcer, du bruger.  I denne artikel forklares det, hvor hurtigt og konsekvent du kan oprette, vedligeholde, og slette udvikling og teste miljøer ved hjælp af Azure ressourcestyring skabeloner og parameter filer, som vist nedenfor.

![Scenarie](./media/solution-dev-test-environments/scenario.png)

Der vises tre udvikling og test miljøer ovenfor.  Hver har webprogram og SQL-databaseressourcer, som er angivet i en skabelonfil.  Navnene på de programmer og -database i hver miljø er forskellige, og er angivet i entydige parameter filer for hver-miljø.  

Hvis du ikke er fortrolig med Azure ressourcestyring begreber, anbefales det, at du læser [Azure ressourcestyring oversigt](azure-resource-manager/resource-group-overview.md) artiklen før læse denne artikel.

Du vil muligvis først gennemgå trinnene i denne artikel, som vist på listen uden at læse en af de der henvises til artikler kan hurtigt få nogle oplevelse ved hjælp af Azure ressourcestyring skabeloner. Når du har været gennem trinnene én gang, du vil kunne få svar på de fleste af de spørgsmål, der blev oprettet din første tid gennem ved at eksperimentere yderligere med trinnene og ved at læse der refereres til artiklerne.

## <a name="plan-azure-resource-use"></a>Planlægge brugen af Azure ressourcer
Når du har et højt niveau design for dit program, kan du definere:

- Hvilke Azure ressourcer omfatter dit program. Du kan opbygge dit program og installere den som en Azure Web App med en Azure SQL-Database.  Du kan oprette dit program i virtuelle maskiner med PHP og MySQL eller IIS og SQL Server eller andre komponenter. [Azure App Service, Cloud Services og virtuelle maskiner sammenligning]( app-service-web/choose-web-site-cloud-service-vm.md) artikel hjælper dig med at beslutte, hvilke Azure ressourcer, kan du vil bruge til dit program.
- Hvad service niveau krav som tilgængelighed, sikkerhed og skalering, der lever op til dit program.

## <a name="download-an-existing-template"></a>Hente en eksisterende skabelon
En skabelon til Azure ressourcestyring definerer alle de Azure ressourcer, der bruger dit program. Allerede findes flere skabeloner, du kan installere direkte i portalen Azure eller hente, redigere og gemme i et kilde kontrolelement system med din programkode.  Udfør nedenstående trin for at hente en eksisterende skabelon.

1. Gennemse eksisterende skabeloner i [Azure Hurtig start skabeloner](https://github.com/Azure/azure-quickstart-templates/) GitHub lager. Du får vist en "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)"-mappe på listen. Da mange brugerdefinerede programmer indeholder et webprogram og SQL-database, bruges denne skabelon som et eksempel i resten af denne artikel kan hjælpe dig med at forstå, hvordan du bruger skabeloner. Det er ikke medtaget i denne artikel for at forklare alt denne skabelon opretter og konfigurerer fuldt ud, men hvis du planlægger at bruge det til at oprette faktiske miljøer i organisationen, vil du forstår fuldt ud ved at læse artiklen [klargøring en WebApp med en SQL-Database](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Bemærk: der er skrevet i denne artikel for December 2015 version af skabelonen [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . På linkene nedenfor, peg på skabelon og parameter filer er denne version af skabelonen.
2. Klik på filen [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) i mappen 201-web-app-sql-database for at få vist indholdet. Dette er Azure ressourcestyring skabelonfilen. 
3. Klik på knappen "[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)" i visningstilstand. 
4. Markere indholdet af denne fil med musen, og gemme dem på computeren som en fil med navnet "TestApp1-Template.json." 
5. Undersøge skabelon indholdet, og Bemærk følgende:
 - I afsnittet **ressourcer** : Dette afsnit definerer type Azure ressourcer, der er oprettet af denne skabelon. Denne skabelon opretter mellem andre ressourcetyper [Azure Web App](app-service-web/app-service-web-overview.md) og [Azure SQL-Database](sql-database/sql-database-technical-overview.md) ressourcer. Hvis du foretrækker at køre og administrere web og SQL Server på virtuelle computere, du kan bruge skabelonen til "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" eller "[lys-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", men vejledningen i denne artikel er baseret på skabelonen [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - **Parametre** sektion: Dette afsnit definerer de parametre, der hver ressource kan konfigureres med. Nogle af de parametre, der er angivet i skabelonen har "standardværdi" egenskaber, mens andre ikke. Når du installerer Azure ressourcer med en skabelon, skal du angive værdier for alle parametre, der ikke har standardværdi egenskaber, der er angivet i skabelonen.  Hvis du ikke angiver værdier for parametre med standardværdi, bruges den angivne værdi for parameteren standardværdi i skabelonen.

En skabelon definerer hvilke Azure ressourcer oprettes og parametrene for hver ressource kan konfigureres med. Du kan få mere at vide om skabeloner og hvordan du kan designe din egen ved at læse artiklen [bedste fremgangsmåder til design af Azure ressourcestyring skabeloner](best-practices-resource-manager-design-templates.md) .

## <a name="download-and-customize-an-existing-parameter-file"></a>Hente og tilpasse en eksisterende parameterfil

Hvis du vil sikkert også den *samme* Azure ressourcer, der er oprettet i hver miljø, skal du sandsynligvis vil konfigurationen af ressourcerne, der kan være *forskellige* fra de enkelte miljøer.  Dette er hvor parameter filer kommer. Oprette parameter filer, der indeholder entydige værdier i de enkelte miljøer ved at udføre trinnene nedenfor.   

1. Få vist indholdet af filen [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) i mappen 201-web-app-sql-database. Dette er parameterfilen for den skabelonfil, du har gemt i den forrige sektion. 
2. Klik på knappen "[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)" i visningstilstand. 
3. Markere indholdet af denne fil med musen, og gemme dem i tre separate filer på din computer med følgende navne:
 - TestApp1-parametre-Development.json
 - TestApp1-parametre-Test.json
 - TestApp1-parametre-Pre-Production.json

3. Brug af tekst eller JSON editor, Rediger filen udvikling miljø parameter du oprettede i trin 3, erstatter de værdier, der er angivet til højre for parameterværdier i filen med de *værdier* , der er angivet til højre for de **parametre** under: 
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Centrale USA*
 - **servernavn**: *testapp1devsrv*
 - **serverLocation**: *Centrale USA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Erstat med din adgangskode*
 - **Databasenavn**: *testapp1devdb*

4. Brug af tekst eller JSON editor, redigere filen Test miljø parameter du oprettede i trin 3, skal du erstatte den de værdier, der er angivet til højre for parameterværdier i filen med *værdier* , der er angivet til højre for de **parametre** nedenfor:
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Centrale USA*
 - **servernavn**: *testapp1testsrv*
 - **serverLocation**: *Centrale USA*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Erstat med din adgangskode*
 - **Databasenavn**: *testapp1testdb*

5. Ved hjælp af tekst eller JSON editor, redigere parameterfilen foreløbig fremstilling, du oprettede i trin 3.  Erstat hele indholdet af filen med nyheder nedenfor:

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

I filen foreløbig fremstilling parametrene ovenfor, parametrene **sku** og **requestedServiceObjectiveName** er blevet *tilføjet*, mens de ikke blev tilføjet i de udvikling og Test parametre filer. Dette skyldes, at der er angivet for disse parametre i skabelonen, standardværdier og i udvikling og Test miljøerne, bruges standardværdierne, men i foreløbig fremstilling miljø ikke-standardværdier for disse parametre bruges.

Det bruges ikke standardværdierne for disse parametre i den foreløbige produktionsmiljø skyldes til at afprøve værdierne for disse parametre, du kan foretrækker til dit produktionsmiljø, så de kan også testes.  Disse alle parametre, der er relateret til Azure [hosting Web App-planer](https://azure.microsoft.com/pricing/details/app-service/), eller **sku** og Azure [SQL-Database](https://azure.microsoft.com/pricing/details/sql-database/)eller **requestedServiceObjectiveName** , der bruges af programmet.  Forskellige lagerenheder og målsætning tjenestenavne har forskellige omkostninger og funktioner og understøtter anden tjeneste niveau målepunkter.

Tabellen nedenfor viser en liste over standardværdierne for disse parametre, der er angivet i skabelonen og de værdier, der bruges i stedet for standardværdierne i parameterfilen foreløbig fremstilling.

| Parameter | Standardværdi | Fil parameterværdi |
|---|---|---|
| **SKU** | Gratis | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Oprette miljøer
Alle Azure ressourcer skal oprettes i en [Azure ressourcegruppe](azure-resource-manager/resource-group-overview.md). Ressourcegrupper gør det muligt at gruppere Azure ressourcer, så de kan administreres samlet.  [Tilladelser](./active-directory/role-based-access-built-in-roles.md) kan tildeles til grupper, så bestemte personer i din organisation kan oprette, redigere, slette eller se dem og ressourcer i e-mails.  Beskeder og faktureringsoplysninger for ressourcer i gruppen ressource kan ses i [Azure-portalen](https://portal.azure.com). Grupper er oprettet i en Azure [område](https://azure.microsoft.com/regions/).  Alle ressourcer, der er oprettet i det centrale USA område i denne artikel. Når du begynder at oprette faktisk miljøer, skal du vælge det område, der bedst opfylder dine behov. 

Oprette grupper for hver-miljø ved hjælp af nedenstående fremgangsmåder.  Alle metoder får det samme resultat.

###<a name="azure-command-line-interface-cli"></a>Azure Command Line Interface (CLI)

Sikre, at du har CLI [installeret](xplat-cli-install.md) på enten en Windows, OS X eller Linux computer, og som du har [tilsluttet](xplat-cli-connect.md) din [Azure AD-konto](./active-directory/active-directory-how-subscriptions-associated-directory.md) (også kaldet arbejds-eller skoleopgaver) til dit Azure-abonnement. Skriv kommandoen nedenfor til at oprette ressourcegruppen for udviklingsmiljøet fra kommandolinjen CLI.

    azure group create "TestApp1-Development" "Central US"

Kommandoen returnerer følgende, hvis det lykkes:

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

For at oprette ressourcegruppen for Test-miljø, skal du skrive kommandoen nedenfor:

    azure group create "TestApp1-Test" "Central US"

For at oprette ressourcegruppen for den gamle produktionsmiljø, skal du skrive kommandoen nedenfor:

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Sørg for, at du har Azure PowerShell 1.01 eller nyere installeret på en Windows-computer og har tilsluttet din [Azure AD-konto](./active-directory/active-directory-how-subscriptions-associated-directory.md) (også kaldet arbejds-eller skoleopgaver) til dit abonnement, som beskrevet i artiklen [Sådan installerer og konfigurerer Azure PowerShell](powershell-install-configure.md) . Skriv kommandoen nedenfor til at oprette ressourcegruppen for udviklingsmiljøet fra en PowerShell-kommandoprompt.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Kommandoen returnerer følgende, hvis det lykkes:

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

For at oprette ressourcegruppen for Test-miljø, skal du skrive kommandoen nedenfor:

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

For at oprette ressourcegruppen for den gamle produktionsmiljø, skal du skrive kommandoen nedenfor:

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Azure-portalen

1. Log på [Azure-portalen](https://portal.azure.com) med en [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (også kaldet en arbejds- eller skole)-konto. Klik på ny--> Management--> ressourcegruppe og Angiv "TestApp1 Development" i feltet ressource-gruppenavn, Vælg dit abonnement, og vælg "Central os" i feltet ressource gruppe placering, som vist i billedet nedenfor.
   ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Klik på knappen Opret for at oprette ressourcegruppen.
3. Klik på Gennemse, Rul ned på listen til ressourcegrupper, og klik på ressourcegrupper, som vist nedenfor.
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Når du klikker på ressourcegrupper kan du se bladet ressource grupper med din nye ressourcegruppe.
   ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Oprette TestApp1-Test og TestApp1-Pre-produktiv ressource grupperer på samme måde, du oprettede ressourcegruppen TestApp1 udvikling ovenfor.

##<a name="deploy-resources-to-environments"></a>Anvende ressourcer til miljøer

Installer Azure ressourcer til ressourcegrupper for hver-miljø ved hjælp af skabelonfilen for løsningen og parameter filer for hver-miljø ved hjælp af en af nedenstående fremgangsmåder.  Begge metoder får det samme resultat.

###<a name="azure-command-line-interface-cli"></a>Azure Command Line Interface (CLI)

Skriv kommandoen nedenfor til at implementere ressourcer til ressourcegruppen du har oprettet for udviklingsmiljø skal erstatte [sti] med stien til de filer, du har gemt i forrige trin fra kommandolinjen CLI.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Når du får vist meddelelsen "Venter på at fuldføre installationen" for et par minutter, returnerer kommandoen følgende, hvis det lykkes:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Hvis kommandoen ikke lykkes, løse eventuelle fejlmeddelelser og prøve igen.  Almindelige problemer bruger parameterværdier, der ikke overholder Azure naming ressourcebegrænsninger. Andre tip til fejlfinding kan findes i artiklen [fejlfinding ressource gruppe installationer i Azure](./resource-manager-troubleshoot-deployments-cli.md) .

Skriv kommandoen nedenfor til at implementere ressourcer til ressourcegruppen du har oprettet for testmiljø, erstatte [sti] med stien til de filer, du har gemt i forrige trin fra kommandolinjen CLI.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Skriv kommandoen nedenfor til at implementere ressourcer til ressourcegruppen du har oprettet for den gamle produktionsmiljø erstatte [sti] med stien til de filer, du har gemt i forrige trin fra kommandolinjen CLI.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

Skriv kommandoen nedenfor til at implementere ressourcer til ressourcegruppen du har oprettet for udviklingsmiljø skal erstatte [sti] med stien til de filer, du har gemt i forrige trin fra en kommandoprompt Azure PowerShell (version 1.01 eller nyere).

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Når du får vist en blinkende markør til et par minutter, returnerer kommandoen følgende, hvis det lykkes:

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Hvis kommandoen ikke lykkes, løse eventuelle fejlmeddelelser og prøve igen.  Almindelige problemer bruger parameterværdier, der ikke overholder Azure naming ressourcebegrænsninger. Andre tip til fejlfinding kan findes i artiklen [fejlfinding ressource gruppe installationer i Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  Fra en PowerShell-kommandoprompt, skrive kommandoen nedenfor til at udrulle ressourcer til ressourcegruppen du har oprettet for testmiljø, erstatte [sti] med stien til de filer, du har gemt i forrige trin.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Fra en PowerShell-kommandoprompt, skrive kommandoen nedenfor til at udrulle ressourcer til ressourcegruppen du har oprettet for den gamle produktionsmiljø erstatte [sti] med stien til de filer, du har gemt i forrige trin.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Skabelonen og parameter filerne kan være versionsnummer og vedligeholdes med din programkode i et system til kontrol af kilde.  Du kan også gemme kommandoerne ovenfor for at scripts filer og gemme dem med din kode samt.

> [AZURE.NOTE] Du kan installere skabelonen til Azure direkte ved at klikke på knappen "Installere til Azure" i artiklen [klargøring en Web App med en SQL-Database](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Du kan finde det nyttigt at få mere at vide om skabeloner, men det ikke gør det muligt at redigere, version, og Gem dine skabelon og parameter værdier med din programkode, så yderligere oplysninger om denne metode ikke er omfattet i denne artikel.

## <a name="maintain-environments"></a>Vedligeholde miljøer
I hele udvikling, kan konfiguration af Azure ressourcer i de forskellige miljøer mappenavnet ændres forsætligt eller ved et uheld.  Dette kan forårsage unødvendige fejlfinding og løsning af problemer under programmet udviklingscyklus.

1. Ændre miljøerne ved at åbne [Azure-portalen](https://portal.azure.com). 
2. Log på den med den samme konto, du brugte til at udføre trinnene ovenfor. 
3. Som vist i billedet nedenfor, klik på Gennemse--> ressourcegrupper (du skal muligvis rulle ned for at se ressourcegrupper).
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Når du klikker på grupper på billedet ovenfor, får du vist bladet ressource grupper og de tre ressourcegrupper, du har oprettet i et tidligere trin, som vist i billedet nedenfor. Klik på ressourcegruppe TestApp1 udvikling, og du får vist bladet, der viser de ressourcer, der er oprettet af skabelonen i den TestApp1 udvikling ressource gruppe-installation, du har udført i et tidligere trin.  Slet den TestApp1DevApp online-ressource ved at klikke på TestApp1DevApp i bladet TestApp1 udvikling ressource gruppe og derefter klikke på Slet i bladet TestApp1DevApp Web app.
   ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Klik på "Ja", når portalen beder dig om som hvorvidt du er sikker på, at du vil slette ressourcen. Lukke bladet TestApp1 udvikling ressource gruppe og åbne den igen nu vises den uden den WebApp, du lige har slettet.  Indholdet af ressourcegruppen er nu anderledes ud end de skal være. Du kan eksperimentere yderligere ved at slette flere ressourcer fra flere grupper eller endda ændre konfigurationsindstillinger for nogle af ressourcerne. I stedet for ved hjælp af portalen Azure til at slette en ressource fra en ressourcegruppe, kan du bruge kommandoen PowerShell [Fjern AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) eller eller kommandoen "azure ressource Slet" fra CLI til at udføre den samme opgave.
6. For at få alle de ressourcer og konfiguration, der skal være i ressourcegrupperne tilbage til den tilstand, de skal vises i igen installere miljøer til ressourcegrupper ved hjælp af de samme kommandoer, du har brugt i afsnittet [Implementer ressourcer for miljøer](#deploy-resources-to-environments) , men erstatte "Deployment1" med "Deployment2."
7.  Som vist i sektionen Oversigt over bladet TestApp1 udvikling i billedet vises i trin 4, kan du se, at den WebApp, du har slettet i portalen i det forrige trin findes igen, som andre ressourcer kan du har valgt at slette. Hvis du har ændret konfigurationen af nogen af ressourcerne, kan du også kontrollere, at de har indstillet tilbage til værdierne i filerne parameter for. En af fordelene ved at implementere dine miljøer med Azure ressourcestyring skabeloner er, at du kan nemt igen installere miljøer tilbage til tilstanden kendte når som helst.
8. Hvis du klikker på tekst under "Sidste installation" i billedet nedenfor, får du vist en blade, der viser installation oversigten for ressourcegruppen.  Da du bruges navnet "Deployment1" til den første installation og "Deployment2" til den anden installation, har du to poster.  At klikke på en installation, vises en blade, der viser resultaterne for de enkelte installationer.
  ![Portal](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Slette miljøer
Når du er færdig med at bruge et miljø, skal du slette den, så du skal ikke afholde Brugsgebyrer for Azure ressourcer, du bruger ikke længere.  Sletning af miljøer er endnu nemmere end at oprette dem.  Individuelle Azure ressourcegrupper er oprettet for hver miljø i tidligere trin, og derefter ressourcer, der blev installeret i ressourcegrupperne. 

Slet de miljøer, ved hjælp af nedenstående fremgangsmåder.  Alle metoder får det samme resultat.

### <a name="azure-cli"></a>Azure CLI

Fra en CLI bliver spurgt, skal du skrive følgende:

    azure group delete "TestApp1-Development"

Når du bliver bedt om det, Angiv y, og derefter trykke på enter for at fjerne udviklingsmiljøet og alle dens ressourcer. Når du har et par minutter returnerer kommandoen følgende:

    info:    group delete command OK

Skriv følgende for at slette de resterende miljøer fra en CLI-prompt:

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

Skriv kommandoen nedenfor til at slette ressourcegruppen og alle dens indhold fra en kommandoprompt Azure PowerShell (version 1.01 eller nyere).    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Når du bliver bedt om det, hvis du er sikker på, at vil du fjerne ressourcegruppen, skal du angive y, efterfulgt af tasten enter.

Skriv følgende for at slette de resterende miljøer:

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Azure-portalen

1. Gå til grupper, som du gjorde på forrige trin i Azure-portalen. 
2. Vælg ressourcegruppe TestApp1 udvikling, og klik derefter på Slet i gruppen bladet TestApp1 udvikling ressource. En ny blade vises. Angiv ressource gruppenavn, og klik på knappen Slet.
![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Slet TestApp1-Test og TestApp1-Pre-produktiv ressource grupperer på samme måde, du har slettet gruppen TestApp1 udvikling ressource.

Uanset hvilken metode du anvender, ressourcegrupperne og alle de ressourcer, de indeholdt derfor ikke længere findes, og du skal ikke længere afholder fakturering udgifter for ressourcerne.  

Hvis du vil minimere Azure job ressource anvendelsen udgifter, der påløber under udvikling af programmer kan du bruge [Azure automatisering](automation/automation-intro.md) til at planlægge, der:

- Stop virtuelle maskiner i slutningen af hver dag, og genstart dem i starten af hver dag.
- Slette hele miljøer i slutningen af hver dag og genoprette dem i starten af hver dag.
 
Nu hvor du har fandt, hvor nemt det er at oprette, vedligeholde, og slette udvikling og teste miljøerne, du kan få mere at vide gjorde om, hvad du kan kun yderligere at eksperimentere med ovenstående trin og læse de referencer, der er indeholdt i denne artikel.

## <a name="next-steps"></a>Næste trin

- [Stedfortræder administrativ kontrol](./active-directory/role-based-access-control-configure.md) til forskellige ressourcer i de enkelte miljøer ved at tildele Microsoft Azure AD grupper eller brugere til bestemte roller, der har mulighed for at udføre et undersæt af handlinger på Azure ressourcer.
- [Tildele mærker](resource-group-using-tags.md) til ressourcegrupper for hvert miljø og/eller de enkelte ressourcer. Du kan føje en "Miljø" mærke til dine ressourcegrupper og angive dens værdi, der svarer til dit miljø navne. Mærker kan være særligt nyttige, når du har brug for til at organisere ressourcer for fakturerings- eller administration.
- Overvåge beskeder og fakturering for ressourcegruppe eller ressource i [Azure-portalen](https://portal.azure.com).
