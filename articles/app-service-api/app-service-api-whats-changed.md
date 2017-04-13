<properties
    pageTitle="App Service API Apps - Hvad er ændret | Microsoft Azure"
    description="Få mere at vide, hvad der er nyt for API Apps i Azure App Service."
    services="app-service\api"
    documentationCenter=".net"
    authors="mohitsriv"
    manager="wpickett"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps---whats-changed"></a>App Service API Apps - Hvad er ændret

En række forbedringer til Azure App tjenesten blev [offentliggjort](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/)på hændelsen Connect() i November 2015. Disse forbedringer omfatter underliggende ændringer API Apps bedre justere med Mobile og Web Apps, reducere konceptet Tæl og forbedre installation og runtime ydeevnen. Starte 30 November 2015 nye API apps, du opretter ved hjælp af administrationsportalen Azure, eller den seneste værktøj afspejles disse ændringer. I denne artikel beskrives disse ændringer, samt hvordan du kan geninstallere eksisterende apps for at kunne udnytte mulighederne.

## <a name="feature-changes"></a>Funktionsændringer
De vigtigste funktioner i API Apps – godkendelse, CORS og API metadata – har flyttet direkte i App-tjenesten. Med denne ændring på funktioner er tilgængelige på tværs af internettet, Mobile og API Apps. Faktisk dele alle tre den samme **Microsoft.Web/sites** ressourcetype i ressourcestyring. Gatewayen API Apps er ikke længere behov eller tilbydes med API Apps. Dette gør det også nemmere at bruge Azure API Management, da der vil være lige enkelt API Management gatewayen.

![Oversigt over API-Apps](./media/app-service-api-whats-changed/api-apps-overview.png)

En vigtige design princippet med opdateringen API Apps er at giver dig mulighed at få din API, som er dit sprog.  Hvis din API er allerede installeret som en Web App eller Mobile-App, behøver du ikke at geninstallere din app for at drage fordel af de nye funktioner. Hvis du er i øjeblikket i API Apps preview, detaljerede overførsel vejledning under.

### <a name="authentication"></a>Godkendelse
De eksisterende nøglefærdige API Apps, Mobile tjenester/Apps og Web Apps-godkendelsesfunktioner har været unified og findes i en enkelt Azure App Service godkendelse blade i portalen management. Se en introduktion til godkendelsestjenester i App-tjeneste i [udvide App Service godkendelse / godkendelse](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

API scenarier er der et antal relevante nye muligheder:

- **Support til brug af Azure Active Directory direkte**, uden klient kode skulle exchange AAD token for en sessionstoken: kunden kan kun medtage AAD tokens i sidehovedet godkendelse i henhold til bæreren token specifikationen. Det betyder også ingen App tjenestens SDK er påkrævet i klient- eller side. 
- **Tjenester eller "Interne" access**: Hvis du har en daemonproces eller nogle anden klient, der er brug for adgang til API'er uden en brugergrænseflade, kan du anmode om et token ved hjælp af en AAD service hovedstolen og sende dem til App-tjenesten til godkendelse med dit program.
- **Udskudt godkendelse**: mange programmer har forskellige adgangsbegrænsninger til forskellige dele af programmet. Måske vil nogle API'er skal være offentligt tilgængeligt, mens andre kræver logon. Funktionen oprindelige godkendelse/godkendelse blev, med hele webstedet kræve logon. Denne indstilling findes stadig, men du kan også give din programkode til at gengive access beslutninger, når App Service er godkendt brugeren.
 
Du kan finde flere oplysninger om de nye godkendelsesfunktioner, skal du se [godkendelse og API Apps i Azure App Service](app-service-api-authentication.md). Oplysninger om, hvordan du kan overføre eksisterende API-apps fra den tidligere API apps model til et nyt, skal du se [overflytning eksisterende API apps](#migrating-existing-api-apps) senere i denne artikel.
 
### <a name="cors"></a>CORS
I stedet for en kommasepareret **MS_CrossDomainOrigins** app-indstilling er der nu et blade i administrationsportalen Azure til at konfigurere CORS. Det kan også konfigureres ved hjælp af Ressourcestyring værktøjer som Azure PowerShell, CLI eller [Ressource Explorer](https://resources.azure.com/). Angive egenskaben **cors** på ressourcetypen **Microsoft.Web/sites/config** til din ** &lt;navn på websted&gt;/web** ressource. Eksempel:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>API metadata
Bladet API definition er nu tilgængelig på tværs af internettet, Mobile og API Apps. I administrationsportalen, kan du angive en relativ URL-adresse eller en absolut URL-adresse, der peger på et slutpunkt, hosts en Swagger 2.0 repræsentation af din API. Det kan også konfigureres ved hjælp af Ressourcestyring værktøj. Angive egenskaben **apiDefinition** på ressourcetypen **Microsoft.Web/sites/config** til din ** &lt;navn på websted&gt;/web** ressource. Eksempel:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

På nuværende tidspunkt skal metadata slutpunktet være offentligt tilgængelige uden godkendelse for mange underordnede klienter (fx Visual Studio REST-API generering af klienten og PowerApps "Tilføj API" flow) for at bruge den. Dette betyder, at hvis du bruger App Service godkendelse og ønsker at få vist API-definitionen fra din app selve, skal du bruge indstillingen udskudt godkendelse beskrevet tidligere, så ruten til metadata for din Swagger er offentlig.

## <a name="management-portal"></a>Administrationsportalen
Valg af **Ny > Web + Mobile > API App** i portalen opretter API apps, der afspejler de nye funktioner, der er beskrevet i artiklen. **Gennemse > API Apps** viser kun disse nye API-apps. Når du navigerer til en API-app, deler bladet de samme layout og egenskaber, som dem på internettet og Mobile-Apps. Den eneste forskel er hurtig start indhold og sortering af indstillinger.

Eksisterende API-apps (eller Marketplace API-apps, der er oprettet ud fra logik Apps) med forrige Preview funktioner vil stadig være synlige i logik Apps designer, og når du navigerer alle ressourcer i en ressourcegruppe.

## <a name="visual-studio"></a>Visual Studio

De fleste Onlines værktøjer fungerer sammen med nye API-apps, da de dele den samme underliggende **Microsoft.Web/sites** ressourcetype. Azure Visual Studio værktøjer, men skal være opgraderede version 2.8.1 eller senere fordi den indeholder en række funktioner, der er specifikke for API'er. Hente SDK fra [Azure downloads-siden](https://azure.microsoft.com/downloads/).

Med rationalisering af typerne App Service, publicere er også unified under **Publicer > Microsoft Azure App Service**:

![Publicere API Apps](./media/app-service-api-whats-changed/api-apps-publish.png)

Hvis du vil vide mere om SDK 2.8.1, kan du læse om Familieforøgelse [blogindlæg](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Alternativt kan du manuelt importere profilen Publicer fra administrationsportalen til at aktivere Publicer. Men skyen Explorer, oprettelse af kode og API app markering/oprettelse af kræver SDK 2.8.1 eller nyere.

## <a name="migrating-existing-api-apps"></a>Overføre eksisterende API-apps
Hvis din brugerdefinerede API er installeret til den forrige Preview-version af API Apps, anmode vi, at du overfører til den nye model til API-Apps ved 31 December 2015. Da både den gamle og den nye model er baseret på Web API'er hostet i App-tjeneste, kan genbruges for størstedelen af eksisterende kode.

### <a name="hosting-and-redeployment"></a>Vært og geninstallation
Trinnene til geninstallation er den samme som anvender en hvilken som helst eksisterende Web API til App-tjenesten. Trin:

1. Oprette en tom API-app. Dette kan gøres på portalen med ny > API-App i Visual Studio fra Publicer eller fra ressourcestyring værktøj. Hvis bruger ressourcestyring værktøj eller skabeloner, kan du angive den **type** værdi til **api** på ressourcetype **Microsoft.Web/sites** have hurtig start-guider og indstillinger i portalen management rettet mod API scenarier.
2. Oprette forbindelse og anvende dit projekt til den tomme API-app ved hjælp af funktioner til installation understøttes af App Service. Læs [Azure App Service installation dokumentation](../app-service-web/web-sites-deploy.md) for at få mere at vide. 
  
### <a name="authentication"></a>Godkendelse
Godkendelsestjenester App Service understøtter de samme funktioner, der var tilgængelige med den tidligere API Apps model. Hvis du bruger session tokens og kræver SDK'er, kan du bruge de følgende klient- og SDK'er:

- Klient: [Azure mobilklient SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- Server: [Microsoft Azure-Mobilappen .NET godkendelse lokalnummer](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Hvis du brugte i stedet App Service alpha SDK'er, forældet disse nu:

- Klient: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Server: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

Især med Azure Active Directory er men ingen App Service-specifikke det nødvendigt Hvis du bruger AAD tokenet direkte.

### <a name="internal-access"></a>Intern adgang
Den tidligere API Apps model medtages et indbygget interne adgangsniveau. Dette påkrævet brug af SDK til signering anmodninger. Som beskrevet tidligere, med den nye model i API Apps kan principper for AAD service bruges som et alternativ til tjeneste til godkendelse uden en App tjenestens SDK. Lær mere i [Service vigtigste godkendelse til API-Apps i Azure App-tjeneste](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Registrering
Den tidligere API Apps model havde API'er hvor du kan finde andre API-apps på kørselstidspunktet i samme ressourcegruppe bag samme gatewayen. Dette er især nyttig i arkitekturer, der implementerer microservice mønstre. Mens dette direkte ikke understøttes, er en række indstillinger tilgængelige:

1. Bruge Azure ressourcestyring API til registrering.
2. Placere Azure API Management foran din App Service-hostet API'er. Azure API administration fungerer som en facaden og kan give en stabil eksterne modstående URL-adresse, også selvom du interne topologi ændres.
3. Oprette din egen registrering API app og har andre API-apps, der er registreret med registrering app på Start.
4. Udfylde app indstillingerne for alle de API apps (og klienter) fra det øjeblik, med slutpunkterne på de andre API-apps. Dette er interessant i skabelon installationer og da API Apps nu giver dig kontrol over URL-adressen.

## <a name="using-api-apps-with-logic-apps"></a>Brug af API-Apps med logik Apps

Den nye model for API-apps fungerer fint med [Logik Apps skemaversion 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Næste trin

Læs artiklerne i [afsnittet API Apps dokumentation](https://azure.microsoft.com/documentation/services/app-service/api/)for at få mere for at vide. De er blevet opdateret afspejler den nye model til API-Apps. Desuden Henvend i forummerne for yderligere oplysninger eller retningslinjer for overførsel:

- [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [Stakoverløb](http://stackoverflow.com/questions/tagged/azure-api-apps)
