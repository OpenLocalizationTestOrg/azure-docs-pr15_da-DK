<properties
    pageTitle="Introduktion til Azure søgning i Java | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Sådan oprettes et hostet skyen søgeprogram på Azure ved hjælp af Java som din programmeringssprog."
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

# <a name="get-started-with-azure-search-in-java"></a>Introduktion til Azure søgning i Java
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Lær, hvordan du opretter et brugerdefineret Java søgeprogram, der bruger Azure Søg efter dens søgeoplevelse. Dette selvstudium bruger [Azure Søg Service REST-API](https://msdn.microsoft.com/library/dn798935.aspx) til at oprette objekter og handlinger, der bruges i denne opgave.

For at køre dette eksempel, skal du have en Azure Search-tjenesten, som du kan tilmelde dig på [Azure-portalen](https://portal.azure.com). Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) til en trinvis vejledning.

Vi bruges følgende software til at opbygge og teste dette eksempel:

- [Eklipse IDE for Java EE udviklere](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Sørg for at hente EE versionen. Et af trinnene bekræftelse kræver en funktion, der findes kun i denne udgave.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Om dataene

Dette eksempelprogram bruger data fra den [USA geologiske tjenester (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtreret på den tilstand af Rhode Island at reducere størrelsen datasæt. Vi bruger disse data til at oprette et søgeprogram, der returnerer landmærker bygninger som hospitaler og skoler samt geologiske funktioner som streams, søer og senere topmøder.

I dette program, programmet **SearchServlet.java** opbygger og indlæser indekset ved hjælp af en [indekseringsprogram](https://msdn.microsoft.com/library/azure/dn798918.aspx) konstruktion, hentning af det filtrerede USGS dataset fra en offentlig Azure SQL-Database. Foruddefinerede legitimationsoplysninger for forbindelsesoplysninger til online-datakilden er angivet i programkoden. Ingen yderligere konfiguration er nødvendig med hensyn til adgang til data.

> [AZURE.NOTE] Vi har anvendt et filter på dette dataset til at holde under 10.000 Dokumentgrænsen af gratis priser niveauet. Denne begrænsning gælder ikke, hvis du bruger det standard niveau, og du kan ændre denne kode for at bruge et større datasæt. Få mere at vide om kapacitet for hvert priser niveau se [grænser og begrænsninger](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Om programfilerne

Følgende liste beskrives de filer, der er relevante for dette eksempel.

- Search.JSP: Giver brugergrænsefladen
- SearchServlet.java: Indeholder metoder (lignende controller i MVC)
- SearchServiceClient.java: Håndtag HTTP anmodninger
- SearchServiceHelper.java: En hjælpeklasse, der indeholder statiske metoder
- Document.Java: Giver datamodellen
- config.Properties: Angiver den URL-adresse til søgning og api-nøgle
- Pom.XML: En Maven afhængighed

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Finde navnet på tjenesten og api-nøgle for din Azure søgetjeneste

Alle REST-API-opkald til Azure Søg kræver, at du angiver URL-adressen og en api-nøgle. 

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **Search-tjenesten** for at få vist alle de Azure Søg tjenester, der er klargjort for dit abonnement på linjen gå.
3. Vælg den tjeneste, du vil bruge.
4. På dashboardet til tjenesten, kan du se fliser til vigtige oplysninger og nøgleikon for at få adgang til tasterne administrator.

    ![][3]

5. Kopiér URL-adressen og en administrator-nøgle. Du skal bruge dem senere, når du føjer dem til filen **config.properties** .

## <a name="download-the-sample-files"></a>Hente eksempelfilerne

1. Gå til [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) på Github.

2. Klik på **Hent ZIP**, Gem .zip-filen til disk, og derefter udtrække alle de filer, den indeholder. Overvej at hente filerne i arbejdsområdet Java at gøre det nemmere at finde projektet senere.

3. Eksempelfilerne, er skrivebeskyttet. Højreklik på Mappeegenskaber, og fjern markeringen attributten skrivebeskyttet tilstand.

Alle efterfølgende fil ændringer og køre sætninger foretages mod filer i denne mappe.  

## <a name="import-project"></a>Importér projekt

1. Vælg **filer**i Eklipse, > **Importér** > **generelle** > **Eksisterende projekter i arbejdsområdet**.

    ![][4]

2. I **Vælg rodmappen**, skal du gå til den mappe, der indeholder eksempelfiler. Vælg den mappe, der indeholder mappen .project. Projektet skal vises på listen **projekter** som et markeret element.

    ![][12]

3. Klik på **Udfør**.

4. Brug **Projektstifinder** til at få vist og redigere filerne. Hvis den ikke allerede er åben, skal du klikke på **vinduet** > **Åbn visning** > **Projektstifinder** eller brug genvejen til at åbne den.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurere URL-adressen og api-nøgle

1. **Projektstifinder**, og dobbeltklik på **config.properties** for at redigere de konfigurationsindstillinger, der indeholder det servernavn og api-nøgle.

2. Se trinnene tidligere i denne artikel, hvor du fundet den URL-adressen og api-nøgle i [Azure Portal](https://portal.azure.com), til at få de værdier, du kan nu angive i **config.properties**.

3. Erstat "Api-nøgle" i **config.properties**med api-nøgle til din tjeneste. Derefter erstatter navnet på tjenesten (den første del af URL-adressen http://servicename.search.windows.net) "service navn" i den samme fil.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurere project, opbygge og runtime-miljøer

1. Højreklik på projektet i Eklipse i Projektstifinder > **Egenskaber** > **Project facetter**.

2. Vælg **dynamisk webmodul**, **Java**og **JavaScript**.

    ![][6]

3. Klik på **Anvend**.

4. **Vindue**til valg af > **Indstillinger** > **Server** > **Runtime miljøer** > **ekstra.**.

5. Udvid Apache og vælg versionen af det Apache Tomcat server, du tidligere har installeret. På vores system installeret vi version 8.

    ![][7]

6. Angiv mappen, Tomcat installation på den næste side. Dette vil sandsynligvis være C:\Program Files\Apache Software Foundation\Tomcat *version*på en Windows-computer.

6. Klik på **Udfør**.

7. **Vindue**til valg af > **Indstillinger** > **Java** > **installeret JREs** > **tilføje**.

8. Vælg **Standard VM**i **Tilføje JRE**.

10. Klik på **Næste**.

11. JRE Definition i JRE hjem, klik på **mappe**.

12. Gå til **Programfiler** > **Java** , og vælg JDK du tidligere har installeret. Det er vigtigt at vælge JDK som JRE.

13. Vælg **JDK**i installeret JREs. Indstillingerne for skal ligne følgende skærmbillede.

    ![][9]

14. Du kan også vælge **vinduet** > **Webbrowser** > **Internet Explorer** for at åbne programmet i en ekstern browservindue. Ved hjælp af en ekstern browser giver dig en bedre oplevelse med Web-program.

    ![][8]

Du har nu fuldført konfigurationsopgaver. Derefter skal du oprette og køre projektet.

## <a name="build-the-project"></a>Opbygge projektet

1. Højreklik på navnet på projektet i Projektstifinder, og vælg **Kør som** > **Maven opbygge...** til at konfigurere projektet.

    ![][10]

8. Skriv "ren installation" i redigere konfiguration i målene, og klik derefter på **Kør**.

Statusmeddelelser er output til console-vinduet. Du bør se OPBYGGE succes, der angiver det projekt, der er oprettet uden fejl.

## <a name="run-the-app"></a>Køre appen

I det sidste trin, vil du køre programmet i en lokal server runtime-miljø.

Hvis du endnu ikke har angivet en server runtime-miljø i Eklipse, skal du gøre det først.

1. Udvid **WebContent**i Projektstifinder.

5. Højreklik på **Search.jsp** > **køre som** > **kører på serveren**. Vælg serveren, Apache Tomcat, og klik derefter på **Kør**.

> [AZURE.TIP] Hvis du har brugt et ikke-standard-arbejdsområde til at gemme dit projekt, skal du ændre **Køre konfiguration** til at pege på feltet projektplacering for at undgå en opstart serverfejl. Højreklik på **Search.jsp**i Projektstifinder > **Kør som** > **Køre konfigurationer**. Vælg Apache Tomcat serveren. Klik på **argumenter**. Klik på **arbejdsområde** eller **Filsystemet** for at angive den mappe, der indeholder projektet.

Når du kører programmet, skal du se et browservindue, give et søgefelt til indtastning af betingelserne.

Vent omkring et minut før du klikker på **Søg** for at give tjenesten tid til at oprette og indlæse indekset. Hvis du får en HTTP 404-fejl, skal du bare vente lidt længere, før du prøver igen.

## <a name="search-on-usgs-data"></a>Søge på USGS data

Datasættet USGS omfatter poster, der er relevante til tilstand Rhode Island. Hvis du klikker på **Søg** på en tom søgefeltet, får du de øverste 50 poster, som er standard.

Angive et søgeord giver søgemaskinen noget at gå på. Prøv at angive et internationale navn. "Roger Williams" blev første regulatoren Rhode Island. Flere parker, bygninger og skoler navngives efter ham.

![][11]

Du kan også prøve et af disse betingelser:

- Pawtucket
- Pembroke
- slaget + cape

## <a name="next-steps"></a>Næste trin

Dette er det første Azure Søg-selvstudium, der er baseret på Java og USGS datasættet. Over tid udvider vi vil dette selvstudium for at vise ekstra søgefunktioner du muligvis vil bruge i dine tilpassede løsninger.

Hvis du allerede har nogle baggrund i Azure Søg, kan du bruge dette eksempel som en springboard for yderligere forsøg måske så [søgeside](search-pagination-page-layout.md)eller implementere [facetteret navigation](search-faceted-navigation.md). Du kan også forbedre på siden med søgeresultater ved at tilføje tæller og samling dokumenter, så brugerne kan bladre gennem resultaterne.

Ny bruger af Azure søgning? Vi anbefaler, at have prøvet andre selvstudier for at udvikle en forståelse af, hvad du kan oprette. Besøg vores [dokumentation side](https://azure.microsoft.com/documentation/services/search/) for at finde yderligere ressourcer. Du kan også se linkene i vores [Video og selvstudium liste](search-video-demo-tutorial-list.md) til at få adgang til flere oplysninger.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
