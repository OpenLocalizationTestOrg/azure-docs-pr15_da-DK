<properties
    pageTitle="Tjenesten vigtigste godkendelse til API-Apps i Azure App Service | Microsoft Azure"
    description="Lær, hvordan du beskytter en API-app i Azure App Service for scenarier med-tjenester."
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
    ms.topic="article"
    ms.date="06/30/2016" 
    ms.author="rachelap"/>

# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Tjenesten vigtigste godkendelse til API-Apps i Azure App Service

## <a name="overview"></a>Oversigt

I denne artikel beskrives det, hvordan du bruger App Service godkendelse til *intern* adgang til API apps. En intern scenario er, hvor du har en API-app, der skal være forbrugsvarer kun via en din egen programkode. Den anbefalede måde at implementere dette scenario i App-tjeneste er at bruge Azure AD til at beskytte kaldet API-app. Du ringe til appen beskyttet API til en bærertokenet, som du angiver Azure AD ved at indsende program-id (service vigtigste) legitimationsoplysninger. Alternativer til brugen af Azure AD, i afsnittet **tjeneste til godkendelse** af [Azure App Service godkendelse oversigt](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

I denne artikel kan du lære følgende:

* Sådan bruges Azure Active Directory (Azure AD) til at beskytte en API-app fra ikke-godkendte access.
* Hvordan du kan bruge en beskyttet API-app fra en API app, online eller mobilapp ved hjælp af Azure AD-tjenesten hovedstolen (app identitet) legitimationsoplysninger. Du kan finde oplysninger om, hvordan du optager fra en logik app [ved hjælp af din brugerdefinerede API hostes på App Service med logik apps](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Sådan sikrer du, at den beskyttede API-app ikke kan kaldes fra en browser ved logget på brugere.
* Hvordan for at sikre dig, at den beskyttede API-app kan kun kaldes af en bestemt Azure AD service hovedstolen.

I artiklen indeholder to sektioner:

* [Hvordan du konfigurerer tjenesten vigtigste godkendelse i Azure App Service](#authconfig) afsnit forklares Generelt, hvordan du konfigurerer godkendelse for en API-app, og hvordan du forbruge appen beskyttet API. Dette afsnit gælder for alle strukturer, der understøttes af App Service, herunder .NET, Node.js og Java lige.

* I sektionen [fortsætter .NET Introduktion selvstudier](#tutorialstart) , fører selvstudiet dig gennem konfigurationen af en "intern adgang" scenarie for en .NET Northwind kører i App-tjeneste. 

## <a id="authconfig"></a>Sådan konfigureres tjenesten vigtigste godkendelse i Azure App Service

Dette afsnit indeholder generelle oplysninger, der gælder for alle API-Apps. Vejledning bestemt til at gøre listen .NET eksempelprogrammet, skal du gå til [fortsætter .NET API Apps selvstudium serien](#tutorialstart).

1. [Azure-portalen](https://portal.azure.com/), gå til **Indstillinger** blade af API-app, du vil beskytte, og derefter finde sektionen **Funktioner** og klikke på **godkendelse / godkendelse**.

    ![Godkendelse/godkendelse Azure-portalen](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. I den **godkendelse / godkendelse** blade, klik **på**.

4. Vælg **Log på med Azure Active Directory** i rullelisten **handling skal udføre, når anmodning ikke er godkendt** .

5. Vælg **Azure Active Directory**under **Godkendelsesprovidere**.

    ![Godkendelse/godkendelse blade Azure-portalen](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Konfigurere bladet **Azure Active Directory-indstillinger** for at oprette en ny Azure AD-programmet, eller brug en eksisterende Azure AD-program, hvis du allerede har en, du vil bruge.

    Interne scenarier involverer typisk en API app ringe til en API-app. Du kan bruge separat Azure AD-programmer for hver API-app eller blot en enkelt Azure AD-program.

    Detaljerede oplysninger om denne blade, se, [hvordan du konfigurerer din App-tjenesteprogrammet bruge logon til Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Klik på **OK**, når du er færdig med bladet godkendelse udbyder konfiguration.

7. I den **godkendelse / godkendelse** blade, klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Når det er gjort, kan App Service kun anmodninger fra ringer i den konfigurerede Azure AD-lejer. Ingen godkendelse eller godkendelse kode er påkrævet i den beskyttede API-app. Bærertokenet der overføres til appen API sammen med ofte anvendte krav i HTTP-headere, og du kan læse disse oplysninger i koden for at kontrollere, at anmodninger er fra en bestemt person, som en tjeneste sikkerhedskonto.

Denne funktionalitet godkendelse fungerer på samme måde for alle sprog, der understøtter App service, herunder .NET, Node.js og Java. 

#### <a name="how-to-consume-the-protected-api-app"></a>Hvordan du kan bruge den beskyttede API-app

Hvem skal give en Azure AD-bærertoken API-kald. Hvis du vil have en bærertoken ved hjælp af tjenesten vigtigste legitimationsoplysninger, bruger kalderen Active Directory Authentication Library (ADAL til [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)eller [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Hvis du vil have et token, indeholder den kode, der kalder ADAL til ADAL følgende oplysninger:

* Navnet på din Azure AD-lejer.
* Klient-ID og klienten hemmeligt (app nøgle) af den Azure AD-app, der er knyttet til kalderen.
* Klient-ID'ET for det Azure AD-program, der er knyttet til den beskyttede API-app. (Hvis der bruges kun én Azure AD-programmet, er det i den samme klient-ID som en for kalderen).

Disse værdier er tilgængelige i Azure AD-sider med [Azure klassisk portal](https://manage.windowsazure.com/).

Når Tokenet er blevet hentet, omfatter kalderen det med HTTP-anmodninger i overskriften godkendelse.  App Service validerer tokenet og giver mulighed for anmodninger om at oprette forbindelse til de beskyttede API-app.

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>Hvordan du beskytter API-app fra access af brugere i samme lejer

Bæreren tokens for brugere i samme lejer er gyldige for den beskyttede API-app.  Hvis du vil sikre dig, at kun en tjeneste sikkerhedskonto kan ringe til den beskyttede API-app, kan du tilføje kode i beskyttet API app til at validere følgende krav fra tokenet:

* `appid`klient-ID'ET for det Azure AD-program, der er knyttet til person, skal være. 
* `oid`(`objectidentifier`) skal være kalderen service primære ID. 

App Service også giver den `objectidentifier` gøre krav i overskriften X-MS-klient-HOVEDSTOLEN-ID.

### <a name="how-to-protect-the-api-app-from-browser-access"></a>Hvordan du beskytter API-app fra adgang fra en webbrowser

Hvis du ikke validerer krav i kode i beskyttet API-appen, og hvis du bruger en separat Azure AD-programmet til appen beskyttet API Sørg for, at programmet Azure AD svar URL-adressen ikke er den samme som appen API base URL-adresse. Hvis svar URL-adresse peger direkte til de beskyttede API-app, kan en bruger i det samme Azure AD-lejer gå til appen API, logge på og korrekt kalde API.

## <a id="tutorialstart"></a>Fortsætter .NET API Apps selvstudium serien

Hvis du følger Node.js eller Java selvstudium serie til API-apps, gå til afsnittet [Næste trin](#next-steps) . 

I resten af denne artikel fortsætter .NET API Apps selvstudium serien og antager, at du har fuldført [bruger godkendelse selvstudium](app-service-api-dotnet-user-principal-auth.md) og har eksempelprogrammet på computeren kører i Azure med brugergodkendelse aktiveret.

## <a name="set-up-authentication-in-azure"></a>Konfigurere godkendelse i Azure

I dette afsnit du konfigurere App Service så de kun HTTP-anmodninger det giver mulighed for at oprette forbindelse til data niveau API app er dem, der har gyldig Azure AD bæreren tokens. 

I følgende afsnit, kan du konfigurere midterste niveau API app for at sende legitimationsoplysningerne til Azure AD, komme tilbage en bærertoken og sende bærertokenet til data niveau API app. Denne proces illustreres i diagrammet.

![Tjenesten godkendelse diagram](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Hvis du støder på problemer under følge vejledningen i selvstudium skal du se afsnittet [fejlfinding](#troubleshooting) i slutningen af selvstudiet. 

1. Gå til **Indstillinger** blade af API-app, du har oprettet for ToDoListDataAPI (data lag) API app på [Azure-portalen](https://portal.azure.com/), og klik derefter på **Indstillinger**.

2. Find sektionen **Funktioner** bladet **Indstillinger** , og klik derefter på **godkendelse / godkendelse**.

    ![Godkendelse/godkendelse Azure-portalen](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. I den **godkendelse / godkendelse** blade, klik **på**.

4. Vælg **Log på med Azure Active Directory**i rullelisten **handling skal udføre, når anmodning ikke er godkendt** .

    Dette er den indstilling, som udløser App Service sikre dig, der kun godkendte anmodninger om adgang til API-app. Anmodninger, der har gyldig bæreren tokens, om App Service overfører tokens langs til appen API og udfylder HTTP-headere med ofte anvendte krav til rådighed disse oplysninger nemmere for din kode.

5. Klik på **Azure Active Directory**under **Godkendelsesprovidere**.

    ![Godkendelse/godkendelse blade Azure-portalen](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Klik på **Express**i bladet **Azure Active Directory-indstillinger** .

    Indstillingen Azure kan automatisk oprette et AAD program i din Azure AD- [lejer](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)med **Express** . 

    Du behøver ikke at oprette en lejer, fordi alle Azure-konto er en automatisk.

7. Klik på **Opret ny AD program** under **administrationstilstand**, hvis den ikke allerede er valgt.

    På portalen tilsluttes **Opret program** inputfeltet med en standardværdi. Som standard hedder programmet Azure AD den samme som API-app. Hvis du foretrækker, kan du angive et andet navn.
    
    ![Azure AD-indstillinger](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

    **Bemærk**: som et alternativ kan du bruge en enkelt Azure AD programmet for både appen opkald API og beskyttet API app. Hvis du vælger Alternativet, skal du ikke indstillingen **Opret AD program** her, fordi du allerede har oprettet et Azure AD-program tidligere i selvstudiet bruger godkendelse. I dette selvstudium skal du bruge adskille Azure AD-programmer til appen opkald API og beskyttet API app.

8. Skal du notere den værdi, der er i boksen **Opret program** input. Du kan slå AAD programmet i portalen Azure klassisk senere.

7. Klik på **OK**.

10. I den **godkendelse / godkendelse** blade, klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

    App Service opretter et Azure Active Directory-program med **Sign-on URL-adresse** , og der angives automatisk **Svar URL-adressen** til URL-adressen for din API-app. Denne værdi gør det muligt for brugere i din AAD lejer logge på og få adgang til API-app.

### <a name="verify-that-the-api-app-is-protected"></a>Kontrollér, at der er beskyttet API-app

1. Gå til URL-adressen til API-app i en browser: bladet **API app** i Azure-portalen, klik på linket under **URL-adresse**. 

    Du har åbnet med en logonskærmen, fordi ikke-godkendte anmodninger ikke er tilladt at nå API-app. 

    Hvis din browser går til Brugergrænsefladen Swagger, browseren allerede kan være logget på – i så fald åbne en InPrivate eller Inkognito-vindue, og gå til URL-adressen på Swagger brugergrænseflade.

18. Log på med legitimationsoplysningerne for en bruger i din AAD lejer.

    Når du er logget på, vises siden "blev oprettet" i browseren.

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Konfigurere ToDoListAPI projektet for at få fat på og sende Azure AD-tokenet

I dette afsnit kan du udføre følgende opgaver:

* Tilføje kode i den midterste niveau API-app, Azure AD-program legitimationsoplysningerne bruges til at få fat på et token og sende det med HTTP-anmodninger til data niveau API app.
* Få legitimationsoplysningerne, som du har brug for fra Azure AD.
* Angiv legitimationsoplysningerne til Azure App Service runtime miljøindstillinger i den midterste niveau API app. 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Konfigurere ToDoListAPI projektet for at få fat på og sende Azure AD-tokenet

Foretage følgende ændringer i ToDoListAPI projektet i Visual Studio.

1. Fjern kommentar fra alle koden i filen *ServicePrincipal.cs* .

    Dette er den kode, der bruger ADAL til .NET til at få fat på Azure AD-bærertokenet.  Det bruges flere konfiguration af værdier, der skal konfigureres i Azure runtime-miljø senere. Her er koden: 

        public static class ServicePrincipal
        {
            static string authority = ConfigurationManager.AppSettings["ida:Authority"];
            static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
            static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
            static string resource = ConfigurationManager.AppSettings["ida:Resource"];
        
            public static AuthenticationResult GetS2SAccessTokenForProdMSA()
            {
                return GetS2SAccessToken(authority, resource, clientId, clientSecret);
            }
        
            static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
            {
                var clientCredential = new ClientCredential(clientId, clientSecret);
                AuthenticationContext context = new AuthenticationContext(authority, false);
                AuthenticationResult authenticationResult = context.AcquireToken(
                    resource,
                    clientCredential);
                return authenticationResult;
            }
        }

    **Note:** Denne kode kræver ADAL til .NET NuGet pakke (Microsoft.IdentityModel.Clients.ActiveDirectory), som allerede er installeret i projektet. Hvis du opretter dette projekt fra bunden, har du vil installere denne pakke. Denne pakke installeres ikke automatisk af skabelonen API app nyt projekt.

2. I *Enheder/ToDoListController*, fjern kommentar fra koden i den `NewDataAPIClient` metode, der tilføjer tokenet til HTTP-anmodninger i overskriften godkendelse.

        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Installer ToDoListAPI projektet. (Højreklik på projektet, og klik derefter på **Publicer > Publicer**.)

    Visual Studio installerer projektet og åbner en browser til den online base URL-adresse. Dette viser en 403 fejlside, som er normal for et forsøg på at gå til en grundlæggende Web API-URL fra en browser.

4. Luk browseren.

### <a name="get-azure-ad-configuration-values"></a>Hent Azure AD konfigurationsværdier

11. Gå til **Azure Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com/).

12. Klik på din AAD lejer på fanen **mappe** .

14. Klik på **programmer > programmer firmaet ejer**, og klik derefter på markeringen.

15. Klik på navnet på den miniature, Azure oprettes til dig, når du har aktiveret godkendelse for ToDoListDataAPI (data lag) API-appen på listen over programmer.

16. Klik på **Konfigurer** .

5. Kopiér værdien **Klient-ID** , og Gem det et sted kan du hente den senere. 

8. Gå tilbage til listen over **programmer firmaet ejer**Azure klassisk portalen, og klik på det AAD-program, du har oprettet for midterste niveau ToDoListAPI API app (den, du har oprettet i tidligere selvstudium, ikke den, du har oprettet i dette selvstudium).

16. Klik på **Konfigurer** .

5. Kopiér værdien **Klient-ID** , og Gem det et sted kan du hente den senere.

6. Vælg **1 år** på rullelisten **Vælg varighed** under **taster**.

6. Klik på **Gem**.

    ![Oprette app nøgle](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Kopiér den nøgleværdi, og Gem det et sted kan du hente den senere.

    ![Kopiere ny app nøgle](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>Konfigurer Azure AD-indstillinger i den midterste niveau API app runtime-miljø

1. Gå til [Azure-portalen](https://portal.azure.com/), og gå derefter til bladet **API App** til appen API, der er vært projektets TodoListAPI (midterste niveau).

2. Klik på **Indstillinger > Indstillinger for program**.

3. Tilføj følgende nøgler og værdier i sektionen **Indstillinger** :

  	| **Nøgle** | Ida: nøglecenter |
  	|---|---|
  	| **Værdi** | https://login.microsoftonline.com/ {Azure AD-lejer kaldt} |
  	| **Eksempel** | https://login.microsoftonline.com/Contoso.onmicrosoft.com |

  	| **Nøgle** | Ida: ClientId |
  	|---|---|
  	| **Værdi** | Klient-ID i programmet opkald (midterste niveau - ToDoListAPI) |
  	| **Eksempel** | 960adec2-b74a-484a-960adec2-b74a-484a |

  	| **Nøgle** | Ida: ClientSecret |
  	|---|---|
  	| **Værdi** | App-tasten af programmet opkald (midterste niveau - ToDoListAPI) |
  	| **Eksempel** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

  	| **Nøgle** | Ida: ressource |
  	|---|---|
  	| **Værdi** | Klient-ID i programmet kaldet (niveau data - ToDoListDataAPI) |
  	| **Eksempel** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

    **Bemærk**: For `ida:Resource`, Sørg for, at du bruger programmet kaldet **klient-ID** og ikke dens **App ID URI**.

    `ida:ClientId`og `ida:Resource` er forskellige værdier for dette selvstudium, fordi du bruger adskille Azure AD applicaations for midterste niveau og data niveau. Hvis du brugte en enkelt Azure AD-program til appen opkald API og beskyttet API app, du vil bruge den samme værdi i begge `ida:ClientId` og `ida:Resource`.

    Koden bruger ConfigurationManager til at hente disse værdier, så de kan være gemt i projektets Web.config-filen eller på Azure runtime-miljø. Mens der kører et ASP.NET-program i Azure App Service, tilsidesætter miljøindstillinger automatisk indstillinger fra Web.config. Miljøindstillinger er normalt en [mere sikker måde til at gemme følsomme oplysninger, der er sammenlignet med en Web.config-fil](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>Teste programmet

1. Gå til HTTPS URL-adressen af AngularJS front-end WebApp i en browser.

2. Klik på **Opgavelisten** fanen og log på med legitimationsoplysningerne for en bruger i din Azure AD-lejer. 

4. Tilføj opgaveelementer for at bekræfte, at programmet fungerer.

    ![Opgaveliste for siden](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

    Hvis programmet ikke fungerer som forventet, kan du kontrollere alle de indstillinger, du har angivet i portalen Azure. Hvis alle indstillingerne forekommer at være korrekt, skal du se afsnittet [fejlfinding](#troubleshooting) senere i dette selvstudium.

## <a name="protect-the-api-app-from-browser-access"></a>Beskytte API-app fra adgang fra en webbrowser

I dette selvstudium du har oprettet en separat Azure AD-programmet til ToDoListDataAPI (data lag) API-app. Som du har set, når App Service opretter et AAD program, konfigurerer programmet AAD på en måde, som gør det muligt for en bruger til at gå til appen API URL-adresse i en browser og log på. Det betyder, at det er muligt for en slutbruger i din Azure AD-lejer, ikke kun en tjeneste, der er primære, at få adgang til API. 

Hvis du vil forhindre adgang fra en webbrowser uden at skrive kode i beskyttet API-appen, kan du ændre **Svar URL-adressen** i programmet AAD, så det er forskelligt fra appen API base URL-adresse. 

### <a name="disable-browser-access"></a>Deaktivere adgang fra en webbrowser

1. Ændre værdien i feltet **Svar URL-adresse** i portalen klassisk **Konfigurer** fanen til det AAD program, der er oprettet med henblik på TodoListService, så det er en gyldig URL-adresse, men ikke appen API URL-adresse.
 
2. Klik på **Gem**.

### <a name="verify-browser-access-no-longer-works"></a>Kontrollere adgang fra en webbrowser fungerer ikke længere

Tidligere godkendt, kan du gå til URL-adressen til API-app fra en browser ved at logge på med en individuel bruger legitimationsoplysninger. I dette afsnit, skal kontrollere du, at dette er ikke længere muligt. 

1. Gå til URL-adressen til appen API igen i et nyt browservindue.

2. Log på, når du bliver bedt om at gøre dette.

3. Logon lykkes, men fører til en fejlside.

    Du har konfigureret AAD-app, så brugerne i AAD lejeren ikke kan logge på og få adgang til API i en browser. Du kan stadig få adgang til API-app ved hjælp af en hovedstol tjeneste-token, som du kan kontrollere ved at gå til den online URL-adresse og tilføje flere opgaveelementer.

## <a name="restrict-access-to-a-particular-service-principal"></a>Begrænse adgangen til en bestemt tjeneste sikkerhedskonto  

Højre nu eventuelle opkalds, der kan få et token for en bruger eller tjeneste hovedstolen i din Azure AD-lejer kan ringe til TodoListDataAPI (data lag) API-app. Du måske at sikre dig, at accepterer data niveau API app kun opkald fra TodoListAPI (midterste niveau) API app og kun fra en bestemt tjeneste sikkerhedskonto. 

Du kan tilføje disse begrænsninger ved at tilføje kode til at validere det `appid` og `objectidentifier` krav på indgående opkald.

I dette selvstudium skal du placere den kode, der har valideret app-ID og service primære ID direkte i din controller handlinger.  Alternativer er at bruge en brugerdefineret `Authorize` attribut eller for at gøre dette datavalidering i din Start sequences (fx OWIN et program). Du kan finde et eksempel på disse [dette eksempelprogram](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs). 

Foretage følgende ændringer til TodoListDataAPI projektet.

2. Åbn filen *Controllers/TodoListController.cs* .

3. Fjern kommentar fra de linjer, angive `trustedCallerClientId` og `trustedCallerServicePrincipalId`.

        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Fjern kommentar fra koden i metoden CheckCallerId. Denne metode kaldes i starten af hver handling metode i controlleren. 

        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }

5. Geninstaller ToDoListDataAPI projektet til Azure App-tjenesten.

6. Gå til AngularJS front-end web Apps HTTPS URL-adresse i din browser, og klik på fanen **Opgavelisten** på siden hjem.

    Programmet fungerer ikke, fordi opkald til back-end mislykkes. Den nye kode kontrollerer faktisk sikkerhedskontrol og objectidentifier, men den endnu har ikke de korrekte værdier til at kontrollere dem mod. Browseren udvikler værktøjer Console-rapporter, at serveren er returnerer fejlen HTTP 401.

    ![Fejl i udvikler værktøjer Console](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

    I følgende trin kan du konfigurere de forventede værdier.

8. Ved hjælp af Azure AD PowerShell, få værdien af hovedstolen service for det Azure AD-program, du har oprettet for TodoListWebApp projektet.

    en. Du kan finde oplysninger om, hvordan du installerer Azure PowerShell og oprette forbindelse til dit abonnement, [Ved hjælp af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md).

    b. For at få en liste over principper for tjenesten skal udføre på `Login-AzureRmAccount` kommandoen og derefter på `Get-AzureRmADServicePrincipal` kommandoen.

    c. Finde Objektid for tjenesten hovedstolen af TodoListAPI programmet, og Gem den på en placering, du kan kopiere fra senere.

7. Gå til bladet API app for den API-app, du har installeret ToDoListDataAPI projektet til i Azure-portalen.

9. Klik på **Indstillinger > Indstillinger for program**.

3. Tilføj følgende nøgler og værdier i sektionen **Indstillinger** :

  	| **Nøgle** | TODO:TrustedCallerServicePrincipalId |
  	|---|---|
  	| **Værdi** | Tjenesten primære id for opkald til programmet |
  	| **Eksempel** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

  	| **Nøgle** | TODO:TrustedCallerClientId |
  	|---|---|
  	| **Værdi** | Klient-ID'ET for opkald til program - kopieret fra TodoListAPI Azure AD-program |
  	| **Eksempel** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Klik på **Gem**.

    ![Klik på Gem](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. Gå tilbage til den online URL-adressen i din browser, og klik på fanen **Opgavelisten** på siden hjem.

    Denne gang programmet fungerer som forventet, fordi app, der er tillid til opkalds-ID og service primære ID er de forventede værdier.

    ![Opgaveliste for siden](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>Opbygning af projekter fra bunden

De to Web API-projekter blev oprettet ved hjælp af skabelonen **Azure API App** project og erstatte standard værdier controlleren med en ToDoList controller. Til hentning af Azure AD-tjenesten vigtigste tokens i ToDoListAPI projektet, blev [Active Directory Authentication Library (ADAL) til .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet pakken installeret.
 
Finde oplysninger om, hvordan du opretter et AngularJS enkeltsidet program med Web API back-end som ToDoListAngular [hænder på øvelse: oprette en enkelt side program (SPA) med ASP.NET Web API og Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Du kan finde oplysninger om, hvordan du tilføjer Azure AD-godkendelse kode [Sikring af AngularJS enkelt side-Apps med Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Sørg for, at du ikke forveksle ToDoListAPI (midterste niveau) og ToDoListDataAPI (data lag). For eksempel i dette selvstudium du tilføje godkendelse til appen API niveau **, men tasten app skal kommer fra det Azure AD-program, du har oprettet for midterste niveau API-app**.

## <a name="next-steps"></a>Næste trin

Dette er det sidste selvstudium i serien API Apps. 

Du kan finde flere oplysninger om Azure Active Directory, i følgende ressourcer.

* [Azure AD udviklere vejledning](http://aka.ms/aaddev)
* [Azure AD-scenarier](http://aka.ms/aadscenarios)
* [Azure AD-eksempler](http://aka.ms/aadsamples)

    [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) eksempel minder om det, der vises i dette selvstudium, men uden at bruge App Service godkendelse.

Oplysninger om andre måder at implementere Visual Studio projekter til API apps, ved hjælp af Visual Studio eller ved at [automatisere installation](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) fra en [kilde](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), se, [hvordan du installerer en Azure App Service-app](../app-service-web/web-sites-deploy.md).
