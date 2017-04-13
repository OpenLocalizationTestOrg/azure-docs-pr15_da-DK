<properties
    pageTitle="Introduktion til Azure søgning i NodeJS | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Gennemgå bygger et søgeprogram på en søgetjeneste hostet skyen på Azure ved hjælp af NodeJS som din programmeringssprog."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-nodejs"></a>Introduktion til Azure søgning i NodeJS
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Lær, hvordan du opretter et brugerdefineret NodeJS søgeprogram, der bruger Azure Søg efter dens søgeoplevelse. Dette selvstudium bruger [Azure Søg Service REST-API](https://msdn.microsoft.com/library/dn798935.aspx) til at oprette objekter og handlinger, der bruges i denne opgave.

Vi bruges [NodeJS](https://nodejs.org) og NPM, [Sublime tekst 3](http://www.sublimetext.com/3)og Windows PowerShell i Windows 8.1 til at udvikle og teste denne kode.

For at køre dette eksempel, skal du have en Azure Search-tjenesten, som du kan tilmelde dig på [Azure-portalen](https://portal.azure.com). Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til en trinvis vejledning.

## <a name="about-the-data"></a>Om dataene

Dette eksempelprogram bruger data fra den [USA geologiske tjenester (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtreret på den tilstand af Rhode Island at reducere størrelsen datasæt. Vi bruger disse data til at oprette et søgeprogram, der returnerer landmærker bygninger som hospitaler og skoler samt geologiske funktioner som streams, søer og senere topmøder.

I dette program, programmet **DataIndexer** opbygger og indlæser indekset ved hjælp af en [indekseringsprogram](https://msdn.microsoft.com/library/azure/dn798918.aspx) konstruktion, hentning af det filtrerede USGS dataset fra en offentlig Azure SQL-Database. Legitimationsoplysninger og forbindelse oplysninger til online-datakilden findes i programkoden. Ingen yderligere konfiguration er nødvendigt.

> [AZURE.NOTE] Vi har anvendt et filter på dette dataset til at holde under 10.000 Dokumentgrænsen af gratis priser niveauet. Hvis du bruger det standard niveau, gælder ikke denne grænse. Få mere at vide om kapacitet for hvert priser niveau se [søgebegrænsninger for tjenesten](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Finde navnet på tjenesten og api-nøgle for din Azure søgetjeneste

Når du opretter tjenesten, kan du vende tilbage til portalen for at få URL-adressen eller `api-key`. Forbindelser til din søgetjenesten kræver, at du har både URL-adressen og en `api-key` til at godkende opkaldet.

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **Search-tjenesten** for at få vist alle de Azure Søg tjenester, der er klargjort for dit abonnement på linjen gå.
3. Vælg den tjeneste, du vil bruge.
4. På dashboardet til tjenesten, kan du se fliser til vigtige oplysninger og nøgleikon for at få adgang til tasterne administrator.

    ![][3]

5. Kopiér URL-adressen, en administrator nøgle, og en forespørgsel. Du skal bruge alle tre senere, når du føjer dem til filen config.js.

## <a name="download-the-sample-files"></a>Hente eksempelfilerne

Brug én af følgende fremgangsmåder til at hente stikprøvernes.

1. Gå til [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Klik på **Hent ZIP**, Gem .zip-filen, og derefter trække alle de filer, den indeholder.

Alle efterfølgende fil ændringer og køre sætninger foretages mod filer i denne mappe.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Opdater config.js. med din søgning URL-adressen og api-nøgle

Brug af URL-adresse og api-nøgle, du kopierede tidligere, angive den URL-adresse, admin-nøgle og forespørgsel-nøgle i konfigurationsfil.

Administrator taster give fuld kontrol over servicehandlinger, herunder oprette eller slette et indeks og indlæsning af dokumenter. Forespørgsel taster kan bruges i modsætning til skrivebeskyttede handlinger, der typisk bruges af klientprogrammer, der har forbindelse til Azure Søg.

I dette eksempel omfatter vi tasten forespørgsel for at styrke den bedste fremgangsmåde ved at bruge tasten forespørgsel i klientprogrammer.

Følgende skærmbillede viser **config.js** åben i en tekst editor, med de relevante poster afgrænsning, så du kan se, hvor du vil opdatere filen med de værdier, der gælder for search-tjenesten.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Være vært for et runtime-miljø til stikprøvernes

Eksemplet kræver en HTTP-server, som du kan installere globalt ved hjælp af npm.

Brug en PowerShell-vinduet for følgende kommandoer.

1. Gå til den mappe, der indeholder fil, **package.json** .
2. Skriv `npm install`.
2. Skriv `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Bygge indekset og køre programmet

1. Skriv `npm run indexDocuments`.
2. Skriv `npm run build`.
3. Skriv `npm run start_server`.
4. Dirigere browseren på`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Søge på USGS data

Datasættet USGS omfatter poster, der er relevante til tilstand Rhode Island. Hvis du klikker på **Søg** på en tom søgefeltet, får du de øverste 50 poster, som er standard.

Angive et søgeord giver søgemaskinen noget at gå på. Prøv at angive et internationale navn. "Roger Williams" blev første regulatoren Rhode Island. Flere parker, bygninger og skoler navngives efter ham.

![][9]

Du kan også prøve et af disse betingelser:

- Pawtucket
- Pembroke
- slaget + cape


## <a name="next-steps"></a>Næste trin

Dette er det første Azure Søg-selvstudium, der er baseret på NodeJS og USGS datasættet. Over tid udvider vi vil dette selvstudium for at vise ekstra søgefunktioner du muligvis vil bruge i dine tilpassede løsninger.

Hvis du allerede har nogle baggrund i Azure Søg, kan du bruge dette eksempel som en springboard for forsøger suggesters (til Autoudfyldning eller Autofuldførelse forespørgsler), filtre og facetteret navigation. Du kan også forbedre på siden med søgeresultater ved at tilføje tæller og samling dokumenter, så brugerne kan bladre gennem resultaterne.

Ny bruger af Azure søgning? Vi anbefaler, at have prøvet andre selvstudier for at udvikle en forståelse af, hvad du kan oprette. Besøg vores [dokumentation side](https://azure.microsoft.com/documentation/services/search/) for at finde yderligere ressourcer. Du kan også se linkene i vores [Video og selvstudium liste](search-video-demo-tutorial-list.md) til at få adgang til flere oplysninger.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
