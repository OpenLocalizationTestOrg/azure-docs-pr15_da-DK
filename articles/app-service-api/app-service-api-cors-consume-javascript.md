<properties
    pageTitle="CORS understøtter i App-tjeneste | Microsoft Azure"
    description="Lær, hvordan du bruger CORS support i Azure Azure App Service."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/27/2016"
    ms.author="rachelap"/>

# <a name="consume-an-api-app-from-javascript-using-cors"></a>Bruge en API-app fra JavaScript ved hjælp af CORS

App Service indeholder indbygget understøttelse af [Tværs Origin ressource deling (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), som gør det muligt JavaScript klienter mulighed for at foretage opkald i tværs af domæner til API'er, der er hostet i API apps. App Service kan du konfigurere CORS adgang til din API uden at skrive kode i din API.

I denne artikel indeholder to sektioner:

* [Sådan konfigureres CORS](#corsconfig) afsnit forklares generelt Sådan konfigureres CORS til en hvilken som helst API app, online eller -mobilappen. Det gælder lige for alle strukturer, der understøttes af App Service, herunder .NET, Node.js og Java. 

* I sektionen [fortsætter .NET Introduktion selvstudier](#tutorialstart) , er i artiklen et selvstudium, der viser CORS understøtter ved at bygge på den handling i [den første API Apps komme i gang selvstudium](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a>Sådan konfigureres CORS i Azure App Service

Du kan konfigurere CORS i portalen Azure eller ved hjælp af [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) værktøjer.

#### <a name="configure-cors-in-the-azure-portal"></a>Konfigurere CORS i portalen Azure

8. Gå til [Azure-portalen](https://portal.azure.com/)i en browser.

2. Klik på **App-tjenester**, og klik derefter på navnet på din API-app.

    ![Vælg API app-portalen](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Finde sektionen **API** i bladet **Indstillinger** , der åbnes til højre for bladet **API app** , og klik derefter på **CORS**.

    ![Vælg CORS i indstillinger blade](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. I feltet tekst, Skriv URL-adressen eller URL-adresser, du vil tillade JavaScript opkald til at komme fra.


    Hvis du har installeret dit JavaScript-program til en WebApp med navnet todolistangular, Angiv "https://todolistangular.azurewebsites.net". Du kan angive en stjerne (*) for at angive, at alle origin domæner accepteres som et alternativ.


13. Klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Når du klikker på **Gem**, accepterer appen API JavaScript opkald fra de angivne URL-adresser.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Konfigurere CORS ved hjælp af Azure ressourcestyring værktøjer

Du kan også konfigurere CORS for en API-app ved hjælp af [Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md) i kommandolinjen funktioner som [Azure PowerShell](../powershell-install-configure.md) og [Azure CLI](../xplat-cli-install.md). 

Et eksempel på en Azure ressourcestyring skabelon, der angiver egenskaben CORS, åbne [azuredeploy.json fil i lager til dette selvstudium Northwind](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Finde sektionen i den skabelon, der ligner følgende eksempel:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a>Fortsætter .NET Introduktion – selvstudium

Hvis du følger den Node.js eller Java-Introduktion serier til API-apps, har du fuldført få Introduktion serien. Gå til afsnittet [Næste trin](#next-steps) til at finde forslag til yderligere lære om API Apps.

I resten af denne artikel er en fortsættelse af .NET-Introduktion serien og antages det, du er blevet udført [det første selvstudium](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Anvende ToDoListAngular-projektet til en ny WebApp

Du har oprettet en midterste niveau API app og en data niveau API-app i [det første selvstudium](app-service-api-dotnet-get-started.md). I dette selvstudium oprette du en enkelt side program (SPA) WebApp, opkald midterste niveau API-app. For SPA til at arbejde, du skal aktivere CORS i den midterste niveau API-app. 

I [ToDoList Northwind](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list)er ToDoListAngular projektet en simpel AngularJS klient, der ringer op til det midterste niveau ToDoListAPI Web API-projekt. JavaScript-kode i filen *app/scripts/todoListSvc.js* kalder API ved hjælp af AngularJS HTTP-udbyder. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Oprette en WebApp for ToDoListAngular projektet

Fremgangsmåden til at oprette en ny App Service WebApp og implementere et projekt til den ligner du fik vist til [oprettelse og implementere en API-app i det første selvstudium i serien](app-service-api-dotnet-get-started.md#createapiapp). Den eneste forskel er, at app typen er **Web App** i stedet for **API App**.  Skærmbilleder af dialogboksene, se 

1. I **Solution Explorer**, højreklik på ToDoListAngular projektet, og klik derefter på **Udgiv**.

3.  Klik på **Microsoft Azure App Service**under fanen **profil** i guiden **Udgiv websted** .

5. Klik på **Ny**i dialogboksen **App Service** .

3. Angiv et **Web App-navn** , der er entydige i *azurewebsites.net* domæne under fanen **Hosting** i dialogboksen **Opret App Service** . 

5. Vælg det Azure **abonnement** , du vil arbejde med.

6. Vælg den samme ressourcegruppe, du oprettede tidligere i rullelisten **Ressourcegruppe** .

4. Vælg den samme plan, du oprettede tidligere i rullelisten **App-serviceaftale** . 

7. Klik på **Opret**.

    Visual Studio opretter WebApp, opretter en Publicer profil for den og viser **forbindelse** trin i guiden **Udgiv websted** .

    Klik ikke **Publicer** endnu. I følgende afsnit, kan du konfigurere den nye WebApp for at ringe midterste niveau API-app, der kører i App-tjeneste. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Angive midterste niveau URL-adressen i indstillinger for web app

1. Gå til [Azure-portalen](https://portal.azure.com/), og gå derefter til bladet **Web App** til den web-app, du har oprettet for at være vært for projektets TodoListAngular (front-end).

2. Klik på **Indstillinger > Indstillinger for program**.

3. Tilføj følgende nøgle og værdi i sektionen **Indstillinger** :

  	|Nøgle|Værdi|Eksempel
  	|---|---|---|
  	|toDoListAPIURL|https://{Your midterste niveau API app-navn} .azurewebsites .net|https://todolistapi0121.azurewebsites.NET|

4. Klik på **Gem**.

    Når koden kører i Azure, tilsidesætter denne værdi localhost URL-adressen, som findes i filen *Web.config* . 

    Den kode, der henter indstillingsværdien findes i *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    Koden i *todoListSvc.js* anvender indstillingen:

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Anvende ToDoListAngular web-projektet til den nye WebApp

*  Klik på **Publicer**i Visual Studio trinnet **forbindelse** i guiden **Udgiv websted** .

    Visual Studio installerer ToDoListAngular projektet på den nye WebApp og åbner en browser for at URL-adressen til WebApp. 

### <a name="test-the-application-without-cors-enabled"></a>Teste programmet uden CORS aktiveret 

2. Åbn vinduet konsol i browseren udviklerværktøjer.

3. Klik på linket **Opgavelisten** i browservinduet, der viser AngularJS UI.

    JavaScript-kode forsøger at ringe til midterste niveau API-app, men opkaldet mislykkes, fordi front end kører i et andet domæne end back-end. Browserens udvikler værktøjer Console i vinduet vises en fejlmeddelelse i tværs origin.

    ![Fejlmeddelelse i tværs origin](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Konfigurere CORS til midterste niveau API-app

I dette afsnit, skal konfigurere du indstillingen CORS i Azure for det midterste modul ToDoListAPI API app. Denne indstilling, så det midterste modul API app til at modtage JavaScript opkald fra web app, du har oprettet for ToDoListAngular projektet.

8. Gå til [Azure-portalen](https://portal.azure.com/)i en browser.

2. Klik på **App-tjenester**, og klik derefter på ToDoListAPI (midterste niveau) API app.

    ![Vælg API app-portalen](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Finde sektionen **API** i bladet **Indstillinger** , der åbnes til højre for bladet **API app** , og klik derefter på **CORS**.

    ![Vælg CORS i portalen](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. I tekstfeltet skal du angive URL-adressen for online ToDoListAngular (front-end). Eksempelvis hvis du har installeret ToDoListAngular projektet til en WebApp med navnet todolistangular0121, Tillad opkald fra URL-adressen `https://todolistangular0121.azurewebsites.net`.

    Du kan angive en stjerne (*) for at angive, at alle origin domæner accepteres som et alternativ.

13. Klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Når du klikker på **Gem**, accepterer appen API JavaScript opkald fra den angivne URL-adresse. I dette skærmbillede accepterer appen ToDoListAPI0223 API JavaScript-klienten opkald fra ToDoListAngular WebApp.

### <a name="test-the-application-with-cors-enabled"></a>Teste programmet med CORS aktiveret

* Åbn en browser for at HTTPS URL-adressen af WebApp. 

    Denne gang programmet kan du få vist, tilføje, redigere og slette opgaveelementer. 

    ![Opgaveliste side af eksempel app](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>App Service CORS kontra Web API CORS

Du kan installere pakken [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet for at angive i kode hvilke domæner din API accepterer JavaScript ringer op fra i Web API-projekt.
 
Web API CORS understøttelse er mere fleksibel end App Service CORS support. For eksempel i kode kan du angive forskellige accepteret baggrunden for anden handling metoder, mens du angive et sæt af accepteret baggrunden for alle en API-app metoder for App-tjenesten CORS.

> [AZURE.NOTE] Ikke kan du prøve at bruge både Web API CORS og App-tjenesten CORS i én API-app. App Service CORS højere prioritet og Web API CORS har ingen virkning. Eksempelvis hvis du aktiverer et origin domæne i App-tjenesten og aktivere alle origin domæner i Web API-kode, accepterer din Azure API app kun opkald fra det domæne, du har angivet i Azure.

### <a name="how-to-enable-cors-in-web-api-code"></a>Sådan aktiveres CORS i Web API-kode

Følgende trin Opsummer processen for at aktivere Web API CORS support. Du kan finde yderligere oplysninger finder [Så i tværs Origin anmodninger i ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Installere pakken [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet i Web API-projekt.

1. Medtage et `config.EnableCors()` linje af kode i metoden **registrere** i klassen **WebApiConfig** , som i følgende eksempel. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. I din Web API-controller, tilføje en `using` erklæringen for den `System.Web.Http.Cors` navneområde, og Tilføj den `EnableCors` attribut til klassen controller eller til individuelle handling metoder. I eksemplet nedenfor gælder CORS support for hele controller.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## <a name="using-azure-api-management-with-api-apps"></a>Azure API administration af med API Apps

Hvis du bruger Azure API Management med en API-app, skal du konfigurere CORS API administration i stedet for i API-app. Yderligere oplysninger finder du se følgende ressourcer:

* [Oversigt over Azure API (video: CORS starter 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [API administration på tværs af domæne politikker](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

I tilfælde af, at der opstår et problem med ved at følge dette selvstudium, er her nogle fejlfinding ideer.

* Sørg for, at du bruger den nyeste version af [Azure SDK til .NET til Visual Studio-2015](http://go.microsoft.com/fwlink/?linkid=518003).

* Sørg for, at du har angivet `https` i indstillingen CORS, og Sørg for, at du bruger `https` til at køre front end-app.

* Sørg for, at du har angivet indstillingen CORS i den midterste niveau API-app, ikke i front end-app.

* Hvis du er ved at konfigurere CORS i både programkode og Azure App Service, opmærksom på, at indstillingen App Service CORS vil tilsidesætte de ting, du foretager i programkode. 

For at få mere for at vide om Visual Studio-funktioner, som forenkler fejlfinding, se [fejlfinding i forbindelse med Azure App Service apps i Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Næste trin 

I denne artikel så du, hvordan du aktiverer understøttelse af App Service CORS, så klienten JavaScript-kode kan ringe til en API i et andet domæne. Hvis du vil vide mere om API apps, Læs [Introduktion til godkendelse i App-tjeneste](../app-service/app-service-authentication-overview.md), og gå derefter til [brugergodkendelse til API apps](app-service-api-dotnet-user-principal-auth.md) selvstudiet.
