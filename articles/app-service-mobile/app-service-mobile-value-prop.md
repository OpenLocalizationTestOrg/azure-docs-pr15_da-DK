<properties
    pageTitle="Hvad er Mobile-Apps"
    description="Få mere at vide, hvilke fordele App Service overskuelig mobilapps din virksomhed."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Hvad er Mobile-Apps?

Azure App Service er en fuldt administreret [Platform som en tjeneste](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) med tilbud til professionelle udviklere, der knytter et bredt udvalg af funktioner til internettet, mobil og integrationsmuligheder. *Mobile-Apps* i *Azure App Service* tilbyder en meget SVG, globalt tilgængelig mobile program development platform for Enterprise udviklere og systemintegratorer, der knytter et bredt udvalg af funktioner til mobile udviklere.

![Mobilapps](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>Hvorfor Mobilapps?
*Mobile-Apps* i *Azure App Service* tilbyder en meget SVG, globalt tilgængelig mobile program development platform for Enterprise udviklere og systemintegratorer, der knytter et bredt udvalg af funktioner til mobile udviklere. Med Mobile-Apps kan du:

- **Oprette lokale og på tværs af platforme apps** - angiver, om du opbygger oprindelige iOS, Android og Windows-apps eller på tværs af platforme Xamarin eller Cordova (Phonegap) apps, kan du drage fordel af App Service ved hjælp af Oprindelig SDK'er.
- **Opret forbindelse til din virksomhedssystemer** - med Mobile Apps, du kan tilføje virksomhedens Log på i minutter, og oprette forbindelse til din virksomhed lokalt eller skybaseret ressourcer.
- **Opbygge offline klar til apps til synkronisering af data** - gøre dine mobile medarbejdere produktiviteten ved at bygge apps, der fungerer offline og bruge Mobile-Apps til at synkronisere data i baggrunden, når forbindelsen er præsentere med nogen af din virksomhed datakilder eller SaaS APIs.
- **Pushbeskeder for millioner i sekunder** - deltage dine kunder med Chat pushmeddelelser på en hvilken som helst enhed, tilpassede efter behov, sendes, når tid, der er rigtige.

## <a name="mobile-app-features"></a>Mobile App-funktioner
Følgende funktioner er vigtige for skyen aktiverede mobile udvikling:

- **Godkendelse og autorisation** - Vælg et konstant voksende listen over id-udbydere, herunder Azure Active Directory til enterprise-godkendelse plus sociale udbydere som Facebook, Google, Twitter og Microsoft-Account.  Azure Mobile-Apps er en OAuth 2.0 for hver udbyder.  Du kan også integrere SDK for identitetsudbyder til udbyder bestemte funktioner.

  Se mere om vores [godkendelsesfunktioner].

- **Data Access** - Azure Mobile-Apps indeholder en mobile-venlig OData v3 datakilde kædet sammen med SQL Azure eller en lokal SQL Server.  Denne tjeneste kan baseres på enhed Framework, så du kan nemt integrere med andre NoSQL og SQL-dataudbydere, herunder [Azure-Tabellager], MongoDB, [DocumentDB] og SaaS API udbydere som Office 365 og Salesforce.com.
- **Offlinesynkronisering** - Our klient SDK'er gør det nemt for dig at opbygge robust og svarede mobilprogrammer, som fungerer med en offline data opsætning, der kan synkroniseres automatisk med back end-dataene, herunder konflikt opløsning support.

  Se mere om vores [datafunktioner].

- **Pushmeddelelser** - Our klient SDK'ER integrere problemfrit med registrering funktionerne i Azure meddelelse Hubs, så du kan sende pushmeddelelser for millioner af brugere samtidigt.

  Finde flere oplysninger om vores [push besked om funktioner].

- **Klient SDK'er** - vi giver et komplet sæt af klienten SDK'er, der dækker oprindelige udvikling ([iOS], [Android] - og [Windows]), på tværs af platforme udvikling ([Xamarin til iOS og Android], [Xamarin formularer]) og udvikling af hybrid ([Apache Cordova]).  Hver klient SDK er tilgængelige med en MIT-licensen og open source.

## <a name="azure-app-service-features"></a>Azure App Service funktioner.
Følgende funktioner, der platform er normalt nyttige til mobile produktionsanlæg.

- **Automatisk skalering** - App Service kan du hurtigt skalere op eller ud for at håndtere nogen indgående kunde belastning. Vælg antallet af og størrelsen af FOS eller konfigurere automatisk skalering til at skalere dit mobilapp backend-version, baseret på Indlæs eller tidsplan manuelt.

  Finde flere oplysninger om [automatisk skalering].

- **Arrangere miljøer** - App Service kan køre flere versioner af dit websted, så du kan udføre A / B test, test fremstilling som en del af en større DevOps plan og lokalt lagringen af en ny back-end.

  Finde flere oplysninger om [midlertidige miljøer].

- **Kontinuert installation** - App Service kan integreres med almindelige SCM systemer, så du kan installere en ny version af din back-end automatisk ved at trykke på en del af dit SCM system.

  Finde flere oplysninger om [installationsindstillinger].

- **Virtual Networking** - App Service kan oprette forbindelse til lokale ressourcer ved hjælp af virtuelle netværk, ExpressRoute eller hybrid forbindelser.

  Se mere om [hybrid forbindelser], [virtuelle netværk]og [ExpressRoute].

- **Isolerede / dedikeret miljøer** -App Service kan køres i en fuldt isolerede og dedikeret miljøet for sikkert når Azure App Service apps skaleres til høj.  Dette er ideelt til arbejdsbelastninger, der kræver meget høj skala, isolationsniveauet eller sikker netværksadgang.

  Se mere om [App Service miljøer].

## <a name="getting-started"></a>Kom godt i gang ##
For at komme i gang med Mobile Apps skal du følge selvstudiet [Komme i gang] .  Dette dækker de grundlæggende regler for producerer en mobile back end- og klienten efter eget valg, og klik derefter integrering af godkendelse, offlinesynkronisering og pushmeddelelser.  Du kan følge selvstudiet [Introduktion] flere gange - én gang til hver-klientprogrammet.

Yderligere oplysninger om Azure Mobile-Apps, skal du gennemse vores [læ kort].
Du kan finde flere oplysninger om Azure App Service platformen, [Azure App Service].

>[AZURE.NOTE]Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Komme i gang]: app-service-mobile-ios-get-started.md
[Azure-Tabellager]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[af godkendelsesfunktioner]: ./app-service-mobile-auth.md
[funktioner til data]: ./app-service-mobile-offline-data-sync.md
[Push-besked om funktioner]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin til iOS og Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin formularer]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatisk skalering]: ../app-service-web/web-sites-scale.md
[midlertidige miljøer]: ../app-service-web/web-sites-staged-publishing.md
[installationsindstillinger]: ../app-service-web/web-sites-deploy.md
[hybrid forbindelser]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[virtuelt netværk]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[App Service miljøer]: ../app-service-web/app-service-app-service-environment-intro.md
[lære kort]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
