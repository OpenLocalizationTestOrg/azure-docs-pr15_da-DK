<properties
    pageTitle="Introduktion til API-Apps og ASP.NET i App-tjeneste | Microsoft Azure"
    description="Lær at oprette, installere og bruge en ASP.NET-API-app i Azure App Service ved hjælp af Visual Studio-2015."
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
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="rachelap"/>

# <a name="get-started-with-api-apps-aspnet-and-swagger-in-azure-app-service"></a>Komme i gang med API Apps, ASP.NET og Swagger i Azure App Service

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Dette er først i en række selvstudier, der viser, hvordan du bruger funktioner til Azure App tjeneste, der er nyttige til at udvikle og vært RESTful API'er.  Dette selvstudium dækker understøttelse af API metadata i Swagger format.

Du kan lære følgende:

* Hvordan du kan oprette og installere [API apps](app-service-api-apps-why-best-platform.md) i Azure App Service ved hjælp af værktøjer, der er indbygget i Visual Studio-2015.
* Sådan automatisere API registrering ved hjælp af pakken Swashbuckle NuGet til at generere dynamisk Swagger API metadata.
* Sådan bruges Swagger API metadata til automatisk oprettelse af klienten kode for en API-app.

## <a name="sample-application-overview"></a>Eksempel programmet oversigt

I dette selvstudium, du arbejder med en enkelt opgave liste Northwind. Programmet har en enkelt side program (SPA) front-end, en ASP.NET Web API midterste niveau og en ASP.NET Web API data niveau.

![API Apps eksempeldiagram-programmet på computeren](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Her er et skærmbillede af [AngularJS](https://angularjs.org/) front end.

![API Apps eksempel programmet opgaveliste](./media/app-service-api-dotnet-get-started/todospa.png)

Visual Studio-løsning inkluderer tre projekter:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** - front end: en AngularJS SPA, der ringer op til det midterste niveau.

* **ToDoListAPI** - midterste niveau: en ASP.NET Web API-projekt, der ringer op til data niveauet for at udføre CRUD handlinger på opgaveelementer.

* **ToDoListDataAPI** - data niveau: et ASP.NET Web API-projekt, som udfører CRUD handlinger på opgaveelementer.

Tredelt arkitekturen er et af mange arkitekturer, som du kan implementere ved hjælp af API Apps og bruges kun til demonstration her. Koden i hvert niveau er så enkelt som muligt at vise API Apps funktioner; for eksempel bruger data niveau serverens hukommelse i stedet for en database som en metode til dens brugerdata.

I dette selvstudium er fuldført, har du de to Web API-projekter op og køre i skyen i App-API'EN apps.

Det næste selvstudium i serien installerer SPA front end til skyen.

## <a name="prerequisites"></a>Forudsætninger

* ASP.NET Web API - selvstudium vejledningen antager, at du har et grundlæggende kendskab til at arbejde med ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) i Visual Studio.

* Azure-konto – du kan [åbne en Azure-konto gratis](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivere Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Der skal du straks kan oprette en forbigående starter app i App-tjeneste – **kræves ingen kreditkort**, og ingen forpligtelser.

* Visual Studio-2015 med [Azure SDK til .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) - feltet SDK installerer Visual Studio 2015, hvis du ikke allerede har den.

    * Klik på Hjælp i Visual Studio -> om Microsoft Visual Studio, og Sørg for, at du har "Azure App Service værktøjer v2.9.1" eller nyere installeret.

    ![Azure-App-funktioner er](./media/app-service-api-dotnet-get-started/apiversion.png)

    >[AZURE.NOTE] Afhængigt af hvor mange af SDK afhængighederne du allerede har på computeren, kan installerer SDK tage lang tid, fra flere minutter at en halv time eller mere.

## <a name="download-the-sample-application"></a>Hente eksempelprogrammet

1. Hent [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) lager.

    Du kan klikke på knappen **Hent ZIP** eller klone lageret på din lokale computer.

2. Åbn ToDoList løsningen i Visual Studio 2015 eller 2013.
   1. Du skal have tillid til hver løsning.
        ![Sikkerhedsadvarsel](./media/app-service-api-dotnet-get-started/securitywarning.png)

3. Oprette løsningen (CTRL + SKIFT + B) til at gendanne NuGet pakkerne.

    Hvis du vil se programmet i handlingen, inden du installerer det, kan du køre den lokalt. Sørg for, at ToDoListDataAPI er startprojektet, og kør løsningen. Du kan forvente at se en HTTP 403 fejl i din browser.

## <a name="use-swagger-api-metadata-and-ui"></a>Brug Swagger API metadata og brugergrænseflade

Understøttelse af [Swagger](http://swagger.io/) 2.0 API metadata er indbygget i Azure App Service. Hver API app kan angive en URL-adressen slutpunkt, der returnerer metadata til API i Swagger JSON-formatet. De metadata, der returneres fra denne slutpunkt kan bruges til at generere klient-kode.

En ASP.NET Web API project kan dynamisk generere Swagger metadata ved hjælp af [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet installationspakken. Pakken Swashbuckle NuGet er allerede installeret i de ToDoListDataAPI og ToDoListAPI projekter, du har hentet.

I dette afsnit af selvstudiet, skal du se på de oprettede Swagger 2.0-metadata, og derefter du prøve en test brugergrænseflade, der er baseret på Swagger metadata.

1. Angive ToDoListDataAPI projektet (**ikke** ToDoListAPI projektet) som projektets start.

    ![Angive ToDoDataAPI som Start projekt](./media/app-service-api-dotnet-get-started/startupproject.png)

2. Tryk på F5 eller klikke på **fejlfinding > Start fejlfinding** til at køre projektet i fejlsikret tilstand.

    Browseren åbnes og viser siden HTTP 403 fejl.

3. Tilføj i browserens adresselinje, `swagger/docs/v1` til slutningen af den linje, og tryk derefter på ENTER. (URL-adressen er `http://localhost:45914/swagger/docs/v1`.)

    Dette er standard URL-adressen, der bruges af Swashbuckle til at returnere Swagger 2.0 JSON metadata for API.

    Hvis du bruger Internet Explorer, browseren beder dig om at hente en *v1.json* fil.

    ![Hente JSON metadata i Internet Explorer](./media/app-service-api-dotnet-get-started/iev1json.png)

    Hvis du bruger Chrome, Firefox eller kant, viser browseren JSON i browservinduet. Forskellige browsere håndtere JSON anderledes, og dit browservindue muligvis ikke at ligne eksemplet.

    ![JSON metadata i Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

    I følgende eksempel viser den første sektion af Swagger metadataene for API med definitionen af Get-metoden. Disse metadata er, hvad drev Swagger Brugergrænsefladen, der bruges i de følgende trin, og du bruge den i en nyere sektion af selvstudiet til automatisk oprettelse af klienten kode.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

4. Luk browseren, og stop Visual Studio fejlfindingen.

5. Åbn filen *App_Start\SwaggerConfig.cs* i ToDoListDataAPI projektet i **Solution Explorer**, og klik derefter Rul ned til linje 174, og fjern kommentar fra følgende kode.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    Filen *SwaggerConfig.cs* oprettes, når du installerer pakken Swashbuckle i et projekt. Filen indeholder en række metoder til at konfigurere Swashbuckle.

    Den kode, du har uncommented gør det muligt for Brugergrænsefladen Swagger, der bruges i de følgende trin. Når du opretter et Web API-projekt ved hjælp af skabelonen API app project, er denne kode kommenterede ud som standard som et mål for sikkerhed.

6. Kør projektet igen.

7. Tilføj i browserens adresselinje, `swagger` til slutningen af den linje, og tryk derefter på ENTER. (URL-adressen er `http://localhost:45914/swagger`.)

8. Når siden Swagger brugergrænseflade vises, skal du klikke på **ToDoList** for at få vist de tilgængelige metoder.

    ![Swagger brugergrænseflade tilgængelige metoder](./media/app-service-api-dotnet-get-started/methods.png)

9. Klik på den første **få** knap på listen.

10. Skriv en stjerne i sektionen **parametre** som værdien af den `owner` parameter, og klik derefter på **Prøv det ud**.

    Når du tilføjer godkendelse i nyere selvstudier, giver det midterste modul faktisk bruger-ID til data niveau. Alle opgaver har nu skal stjerne som deres ejer-ID, mens programmet kører uden godkendelse er aktiveret.

    ![Swagger brugergrænseflade Prøv det!](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    Brugergrænsefladen Swagger opkald ToDoList Hent metode og viser svarkode eller JSON i søgeresultaterne.

    ![Swagger brugergrænseflade Prøv det resultater](./media/app-service-api-dotnet-get-started/gettryitout.png)

11. Klik på **Send**, og klik derefter på feltet under **Modelskemaet**.

    Hvis du klikker på modelskemaet prefills inputfeltet, hvor du kan angive værdien for parameteren for metoden Post. (Hvis det ikke virker i Internet Explorer, bruger en anden browser, eller angive værdien for parameteren manuelt i næste trin).  

    ![Swagger brugergrænseflade Prøv det indlæg](./media/app-service-api-dotnet-get-started/post.png)

12. Ændre JSON i den `todo` parameter indtastet felt, så det ser ud i følgende eksempel eller erstatte din egen Beskrivelsestekst:

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

13. Klik på **Prøv den**.

    ToDoList API returnerer en HTTP 204 svarkode, der angiver succes.

14. Klik på knappen første **få** , og klik derefter på knappen **Prøv det ud** i den valgte sektion på siden.

    Hent metode svaret omfatter nu den nye til at elementet.

15. Valgfrit: Prøv også læg, slette, og få ved ID metoder.

16. Luk browseren, og stop Visual Studio fejlfindingen.

Swashbuckle fungerer med en hvilken som helst ASP.NET Web API-projekt. Hvis du vil tilføje Swagger generering af metadata til et eksisterende projekt, skal du bare installere pakken Swashbuckle.

>[AZURE.NOTE] Swagger metadata omfatter et entydigt ID for hver API handling. Som standard kan Swashbuckle generere dublerede Swagger handlingen id'er til din Web API controller metoder. Dette sker, hvis din controller har overbelastet HTTP-metoder, såsom `Get()` og `Get(id)`. Du kan finde oplysninger om, hvordan du håndterer overloads [tilpasse Swashbuckle oprettede API definitioner](app-service-api-dotnet-swashbuckle-customize.md). Hvis du opretter et Web API-projekt i Visual Studio ved hjælp af skabelonen Azure API App, føjes automatisk kode, der genererer entydige handlingen id'er til filen *SwaggerConfig.cs* .  

## <a id="createapiapp"></a>Oprette en API-app i Azure og installere kode til den

I dette afsnit, skal bruge du Azure-værktøjer, der er integreret i guiden Visual Studio **Udgiv websted** til at oprette en ny API-app i Azure. Derefter skal du anvende ToDoListDataAPI-projektet til den nye API-app og kalde API ved at køre Brugergrænsefladen Swagger.

1. I **Solution Explorer**, højreklik på ToDoListDataAPI projektet, og klik derefter på **Udgiv**.

    ![Klik på Publicer i Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

2.  Klik på **Microsoft Azure App Service**trinnet **profil** i guiden **Udgiv websted** .

    ![Klik på Azure App-tjenesten i publicere Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

3. Log på din Azure-konto, hvis du ikke allerede har gjort det, eller opdatere dine legitimationsoplysninger, hvis de er udløbet.

4. Vælg det Azure **abonnement** , du vil bruge, og klik derefter på **Ny**i dialogboksen App Service.

    ![Klik på ny i dialogboksen App tjeneste](./media/app-service-api-dotnet-get-started/clicknew.png)

    Fanen **Hosting** af dialogboksen **Opret App Service** vises.

    Da du anvender en Web API-projekt, der har Swashbuckle installeret, antages Visual Studio det, at du vil oprette en API-App. Dette er angivet efter overskriften **API App-navn** og ved, at rullelisten **Ændringstype** er indstillet til **API App**.

    ![App typen i dialogboksen App tjeneste](./media/app-service-api-dotnet-get-started/apptype.png)

5. Angiv et **API App-navn** , der er entydige i *azurewebsites.net* domæne. Du kan acceptere standardnavnet, Visual Studio foreslår.

    Hvis du angiver et navn, som en anden person allerede har brugt, vises der et rødt udråbstegn til højre.

    URL-adressen til appen API bliver `{API app name}.azurewebsites.net`.

6. Klik på **Ny**i rullemenuen **Ressourcegruppe** , og derefter indtaste "ToDoListGroup" eller et andet navn, hvis du foretrækker.

    En ressourcegruppe er en samling af Azure ressourcer som API apps, databaser, FOS og så videre. Det er bedst at oprette en ny ressourcegruppe, fordi, der gør det nemt at slette alle de Azure ressourcer, som du opretter i dette selvstudium i ét trin i dette selvstudium.

    Dette felt kan du vælge en eksisterende [ressourcegruppe](../azure-resource-manager/resource-group-overview.md) eller oprette en ny ved at skrive et navn, der er forskellig fra en eksisterende ressourcegruppe i dit abonnement.

7. Klik på knappen **Ny** ud for den **App Service planlægge** ned.

    Skærmbilledet viser eksempelværdier for **API App-navn**, **abonnement**og **Ressourcegruppe** – dine værdier vil være forskellige.

    ![Oprette App Service dialogboksen](./media/app-service-api-dotnet-get-started/createas.png)

    I følgende trin kan du oprette en App-serviceaftale til den nye ressourcegruppe. En App-serviceaftale angiver de Beregn ressourcer, der kører din API-app på. Eksempelvis hvis du vælger det gratis niveau, kører din API-app på delt FOS, mens for nogle betalt niveauer, der køres på dedikeret FOS. Du kan finde oplysninger om App Service planer [App Service planer oversigt](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

8. Angiv "ToDoListPlan" eller et andet navn, hvis du foretrækker, i dialogboksen **Konfigurer App Service planlægge** .

9. Vælg den placering, der er tættest på du i rullelisten **placering** .

    Denne indstilling angiver, hvilke Azure datacenter din app kører i. Vælg en placering tæt du kan minimere [Ventetid](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

10. Klik på **gratis**i rullemenuen **størrelse** .

    Dette selvstudium skal give det gratis priser niveau tilstrækkelige ydeevne.

11. Klik på **OK**i dialogboksen **Konfigurer App Service planlægge** .

    ![Klik på Konfigurer OK i App-serviceaftale](./media/app-service-api-dotnet-get-started/configasp.png)

12. Klik på **Opret**i dialogboksen **Opret App Service** .

    ![Klik på Opret i dialogboksen Opret App-tjeneste](./media/app-service-api-dotnet-get-started/clickcreate.png)

    Visual Studio opretter appen API og en Publicer profil, der indeholder alle de nødvendige indstillinger for API-app. Derefter åbnes guiden **Udgiv websted** , som du skal bruge til at udrulle projektet.

    Guiden **Udgiv websted** åbnes under fanen **forbindelse** (vist nedenfor).

    Indstillingerne for **Server** og **navn på websted** peg på fanen **forbindelse** til din API-app. **Brugernavn** og **adgangskode** er installation legitimationsoplysninger, som Azure opretter for dig. Efter installation åbnes Visual Studio en browser **URL-destinationsadressen** (som er den eneste formål med **Destination URL-adresse**).  

13. Klik på **Næste**.

    ![Klik på næste under fanen forbindelse på Udgiv websted](./media/app-service-api-dotnet-get-started/connnext.png)

    Den næste fane er under fanen **Indstillinger** (vist nedenfor). Her kan du ændre fanen build konfiguration for at installere et fejlfinding build til [ekstern fejlfinding](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). Fanen indeholder også flere **Indstillinger for udgivelse af filen**:

    * Fjerne ekstra filer på destination
    * Precompile under udgivelsen
    * Udelade filer fra mappen App_Data

    I dette selvstudium du ikke har brug for et af følgende. Du kan finde detaljerede beskrivelser af de gør, under [Sådan: installere en Web-projekt ved hjælp af et enkelt klik udgivelse i Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Klik på **Næste**.

    ![Klik på næste under fanen Indstillinger på Udgiv websted](./media/app-service-api-dotnet-get-started/settingsnext.png)

    Næste er fanen **eksempel** (vist nedenfor), som giver mulighed for at se, hvilke filer skal kopieres fra dit projekt til API-app. Når du anvender et projekt til en API-app, du allerede installeret på tidligere, kopieres kun ændrede filer. Hvis du vil se en liste over hvad der skal kopieres, kan du klikke på knappen **Start Preview** .

15. Klik på **Udgiv**.

    ![Klik på Publicer under fanen Vis på Udgiv websted](./media/app-service-api-dotnet-get-started/clickpublish.png)

    Visual Studio installerer ToDoListDataAPI projektet på den nye API-app. Vinduet **Output** logger vellykket installation, og en "oprettet" side vises i et browservindue, der er åbnet til URL-adressen til API-app.

    ![Output vinduet vellykket udrulning](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Ny API app oprettet side](./media/app-service-api-dotnet-get-started/appcreated.png)

16. Føje "swagger" til URL-adressen i browserens adresselinje, og tryk derefter på Enter. (URL-adressen er `http://{apiappname}.azurewebsites.net/swagger`.)

    Browseren viser i samme Swagger brugergrænseflade, du fik vist tidligere, men nu den kører i skyen. Prøv Get-metoden, og du ser, at du er tilbage til standard 2 opgaveemner. De ændringer, du foretog tidligere blev gemt i hukommelsen i den lokale computer.

17. Åbn [Azure-portalen](https://portal.azure.com/).

    Azure portalen har en grænseflade til administration af Azure ressourcer som API apps.

18. Klik på **flere tjenester > App Services**.

    ![Gennemse App tjenester](./media/app-service-api-dotnet-get-started/browseas.png)

19. Finde bladet **App tjenester** , og klik på din nye API-app. (I portalen Azure, windows, som åbnes, til højre kaldes *blade*).

    ![App Services blade](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    To blade Åbn. Én blade har en oversigt over API-app, og én har en lang liste over indstillinger, du kan få vist og redigere.

20. Find sektionen **API** bladet **Indstillinger** , og klik på **API Definition**.

    ![API definitionen i indstillinger blade](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    Bladet **API Definition** kan du angive URL-adressen, der returnerer Swagger 2.0 metadata i JSON-format. Når Visual Studio opretter API-app, angives API definition URL-adressen til standardværdien for Swashbuckle de metadata, du fik vist tidligere, som er API-app'en er grundlæggende URL-adressen plus `/swagger/docs/v1`.

    ![API definition URL-adresse](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Når du vælger en API-app til at generere klient-kode for det, henter Visual Studio metadataene fra denne URL-adresse.

## <a id="codegen"></a>Oprette klient-kode for data niveauet

En af fordelene ved at integrere Swagger i Azure API apps er oprettelse af automatisk kode. Oprettede klient klasser gør det nemmere at skrive programkode, der kalder en API-app.

ToDoListAPI projektet har allerede oprettet Klientkoden, men i de følgende trin skal du slette den og genoprette den for at se, hvordan du gør oprettelsen af kode.

1. Slet mappen *ToDoListDataAPI* i Visual Studio **Solution Explorer**, i project ToDoListAPI. **Advarsel: Slet kun mappen, ikke ToDoListDataAPI projektet.**

    ![Slette genererede klient kode](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Denne mappe blev oprettet ved hjælp af kode generering af processen, som du er ved at gå.

2. Højreklik på ToDoListAPI projektet, og klik derefter på **Tilføj > RESTEN API klient**.

    ![Tilføje REST-API-klienten i Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Klik **Swagger URL-adresse**i dialogboksen **Tilføj RESTEN API klient** , og klik derefter på **Vælg Azure aktiv**.

    ![Vælg Azure aktiv](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

4. Udvid ressourcegruppen, du bruger til dette selvstudium, og vælg din API-app, og klik derefter på **OK**i dialogboksen **App Service** .

    ![Vælg API app til oprettelse af kode](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Bemærk, at når du vender tilbage til dialogboksen **Tilføj RESTEN API klient** , tekstboksen er udfyldt med API definitionen URL-værdi, du fik vist tidligere i portalen.

    ![API Definition URL-adresse](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] En anden måde at få metadata til oprettelse af kode er at angive URL-adressen direkte i stedet for ved at følge dialogboksen gennemse. Eller hvis du vil generere klient kode før du anvender for Azure, skal du kunne køre Web API projektet lokalt, skal du gå til den URL-adresse, der indeholder Swagger JSON-fil, du gemmer filen, og bruge indstillingen **Vælg en eksisterende Swagger metadatafil** .

5. Klik på **OK**i dialogboksen **Tilføj RESTEN API klient** .

    Visual Studio opretter en mappe med navnet efter appen API og danner klient klasser.

    ![Kodefiler for genererede-klienten](./media/app-service-api-dotnet-get-started/codegenfiles.png)

6. Åbn *Controllers\ToDoListController.cs* for at få vist koden på linjen 40, der kalder API ved hjælp af genererede klienten i project ToDoListAPI.

    Følgende kodestykke viser, hvordan koden starter klientobjektet og metoden Get-opkald.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }

        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    Parameteren parametre bliver slutpunkt URL-adressen fra den `toDoListDataAPIURL` app indstilling. I filen Web.config indstilles denne værdi til den lokale IIS Express URL af API projektet, så du kan køre programmet lokalt. Hvis du udelader parameteren parametre, er standardslutpunktet URL-adressen, som du genererede kode fra.

7. Klientklasse genereres med et andet navn, der er baseret på dit API appnavn ændre koden i *Controllers\ToDoListController.cs* , så typenavnet svarer til det blev oprettet i projektet. Hvis du navngivet din API App ToDoListDataAPI071316, vil du ændre denne kode:

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

skal du:

        private static ToDoListDataAPI071316 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## <a name="create-an-api-app-to-host-the-middle-tier"></a>Oprette en API-app Hvis du vil placere det midterste modul

Tidligere du [har oprettet data niveau API app og installeret kode til den](#createapiapp).  Nu kan du følge den samme procedure for midterste niveau API-app.

1. I **Solution Explorer**, skal du højreklikke på det midterste modul ToDoListAPI project (ikke data niveauet ToDoListDataAPI), og klik derefter på **Udgiv**.

    ![Klik på Publicer i Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

2.  Klik på **Microsoft Azure App Service**under fanen **profil** i guiden **Udgiv websted** .

3. Klik på **Ny**i dialogboksen **App Service** .

4. Acceptere standardindstillingen **API App-navn** under fanen **Hosting** i dialogboksen **Opret App Service** , eller skriv et navn, der er entydige i *azurewebsites.net* domænet.

5. Vælg det Azure **abonnement** , du har brugt.

6. Vælg den samme ressourcegruppe, du oprettede tidligere i rullemenuen **Ressourcegruppe** .

7. Vælg den samme plan, du oprettede tidligere i rullemenuen **App-serviceaftale** . Den som standard til den pågældende værdi.

8. Klik på **Opret**.

    Visual Studio opretter API-app, opretter en Publicer profil for den og viser **forbindelse** trin i guiden **Udgiv websted** .

9.  Klik på **Publicer**trinnet **forbindelse** i guiden **Udgiv websted** .

    Visual Studio installerer ToDoListAPI projektet på den nye app API og åbner en browser for at URL-adressen til API-app. Siden "blev oprettet" vises.

## <a name="configure-the-middle-tier-to-call-the-data-tier"></a>Konfigurere det midterste niveau for at ringe data niveau

Hvis du nu kaldet midterste niveau API-app, vil det Prøv at kalde det data niveau ved hjælp af localhost URL-adresse, som stadig findes i filen Web.config. I dette afsnit kan du angive URL-adressen data niveau API app i en indstilling i miljøet i den midterste niveau API-app. Når koden i den midterste niveau API app henter indstillingen data niveau URL-adresse, tilsidesætter miljøindstillingen Nyheder i filen Web.config.

1. Gå til [Azure-portalen](https://portal.azure.com/), og gå derefter til bladet **API App** for den API-app, du har oprettet for at være vært for projektets TodoListAPI (midterste niveau).

2. Klik på **Indstillinger**i Appen API **Indstillinger** blade.

3. Rul ned til sektionen **Indstillinger** i Appen API **Programindstillinger** blade, og Tilføj følgende nøgle og værdi. Værdien skal være URL-adressen til den første API-App du udgivet i dette selvstudium.

  	| **Nøgle** | toDoListDataAPIURL |
  	|---|---|
  	| **Værdi** | https://{your data niveau API appnavnet} .azurewebsites .net |
  	| **Eksempel** | https://todolistdataapi.azurewebsites.NET |

4. Klik på **Gem**.

    ![Klik på Gem for App-indstillinger](./media/app-service-api-dotnet-get-started/asinportal.png)

    Når koden kører i Azure, tilsidesætter denne værdi nu localhost URL-adressen, som findes i filen Web.config.

## <a name="test"></a>Test

1. Gå til URL-adressen til den nye midterste niveau API-app, du lige har oprettet for ToDoListAPI i et browservindue. Du kan gøre det ved at klikke på URL-adressen i appen API primære blade på portalen.

2. Føje "swagger" til URL-adressen i browserens adresselinje, og tryk derefter på Enter. (URL-adressen er `http://{apiappname}.azurewebsites.net/swagger`.)

    Browseren viser den samme Swagger brugergrænseflade, som du fik vist tidligere for ToDoListDataAPI, men nu `owner` er et obligatorisk felt for handlingen Hent ikke, fordi den midterste niveau API app bruges til at sende den pågældende værdi til data niveau API app for dig. (Når du gør godkendelse selvstudier, midterste niveau sender faktisk bruger-id'er den `owner` parameter; for nu det svært-kodning en stjerne.)

3. Prøv Get-metoden og de andre metoder til at validere, midterste niveau API app korrekt ringer data niveau API app.

    ![Swagger brugergrænseflade Get-metoden](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

I tilfælde af, at der opstår et problem, mens du gennemgår dette selvstudium her er nogle ideer, fejlfinding:

* Sørg for, at du bruger den nyeste version af [Azure SDK til .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* To af projektnavnene er lignende (ToDoListAPI, ToDoListDataAPI). Hvis det ikke ser, som beskrevet i instruktionerne, når du arbejder med et projekt, Sørg for, at du har åbnet det korrekte projekt.

* Hvis du er på virksomhedens netværk og forsøger at installere på Azure App Service via en firewall, skal du kontrollere, at port 443 og 8172 er åbne for Web installere. Hvis du ikke kan åbne disse porte, kan du bruge andre installationsmetoder.  Se [installere din app til Azure App-tjenesten](../app-service-web/web-sites-deploy.md).

* "Distribution navne skal være entydige" fejl – du kan få disse Hvis du kommer til at anvende forkerte projektet til en API app og derefter installere det rigtige arbejdsområde til den. For at løse problemet, Vælg genimplementeringen det korrekte projekt til API-app, og under fanen **Indstillinger** i guiden **Udgiv websted** **fjerne ekstra filer på destination**.

Når du har din ASP.NET API-app, der kører i Azure App-tjeneste, kan du mere at vide om Visual Studio-funktioner, der forenkler fejlfinding. Oplysninger om logføring i ekstern fejlfinding og meget mere, [fejlfinding i forbindelse med Azure App Service apps i Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Næste trin

Du har set, hvordan du installerer eksisterende Web API-projekter til API apps, oprette klient-kode til API-apps og forbruge API apps fra .NET-klienter. Det næste selvstudium i denne serie viser, hvordan du [bruger CORS at bruge API apps fra JavaScript-klienter](app-service-api-cors-consume-javascript.md).

Se [Azure/AutoRest](https://github.com/azure/autorest) lager på GitHub.com kan finde flere oplysninger om oprettelse af klienten kode. Åbn et [problem i AutoRest lager](https://github.com/azure/autorest/issues)for at få hjælp med problemer med at bruge den oprettede klient.

Hvis du vil oprette nye API app projekter fra bunden, skal du bruge skabelonen **Azure API App** .

![Skabelon til API App i Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Projektskabelon **Azure API App** er svarer til at vælge **tomt** ASP.NET 4.5.2 skabelon, klikke på afkrydsningsfeltet for at tilføje Web API support og installere pakken Swashbuckle NuGet. Desuden tilføjes skabelonen Swashbuckle konfigurationskode udviklet til at forhindre, at oprettelsen af dublerede Swagger handlingen id'er. Når du har oprettet et API App-projekt, kan du installere det i en API-app på samme måde, du fik vist i dette selvstudium.
