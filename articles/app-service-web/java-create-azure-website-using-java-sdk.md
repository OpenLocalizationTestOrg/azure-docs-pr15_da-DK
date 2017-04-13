<properties 
    pageTitle="Oprette en Web App i Azure App Service ved hjælp af Azure SDK til Java" 
    description="Lær at oprette en Web App på Azure App Service fra et program ved hjælp af Azure SDK til Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Oprette en Web App i Azure App Service ved hjælp af Azure SDK til Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Oversigt

Denne gennemgang viser, hvordan du opretter en Azure SDK til Java-program, der opretter en Web App i [Azure App-tjeneste][], og derefter installere et program til den. Det består af to dele:

- Del 1 viser, hvordan du opretter en Java-program, der opretter en web-app.
- Del 2 viser, hvordan du opretter en simpel JSP "Hej verden" programmet og derefter bruger du et FTP-klienten til at udrulle kode til App-tjenesten.


## <a name="prerequisites"></a>Forudsætninger

### <a name="software-installations"></a>Softwareinstallationer

AzureWebDemo programkode i denne artikel er skrevet ved hjælp af Azure Java SDK 0.7.0, som du kan installere ved hjælp af [Web Platform Installer][] (WebPI). Desuden Sørg for at bruge den nyeste version af [Azure-værktøjskassen til Eklipse][]. Når du har installeret SDK, opdatere afhængigheder i projektet Eklipse ved at køre **Opdater indeks** i **Maven typer lagre**, og derefter igen at føje den nyeste version af hver pakke i vinduet **afhængigheder** . Du kan kontrollere versionen af installeret software i Eklipse ved at klikke på **Hjælp > installationsoplysninger**; Du skal mindst have følgende versioner:

- Pakke til Microsoft Azure-biblioteker til Java 0.7.0.20150309
- Eklipse IDE for Java EE udviklere 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Oprette og konfigurere skyen ressourcer i Azure

Før du begynder denne procedure, skal du har et aktivt Azure abonnement og konfigurere en standard Active Directory (AD) på Azure.


### <a name="create-an-active-directory-ad-in-azure"></a>Oprette et Active Directory (AD) i Azure

Hvis du ikke har en Active Directory (AD) på dit Azure-abonnement, kan du logge på [Azure klassisk portal][] med din Microsoft-konto. Hvis du har flere abonnementer, skal du klikke på **abonnementer** , og vælg standardmappen for det abonnement, du vil bruge til dette projekt. Klik derefter på **Anvend** for at skifte til visningen abonnement.

1. Vælg **Active Directory** i menuen til venstre. **Klik på ny > bibliotek > brugerdefineret oprette**.

2. Vælg **Opret ny mappe**i **Tilføj adresseliste**.

3. Angiv et mappenavn i **navnet**.

4. Angiv et domænenavn i **domæne**. Dette er et grundlæggende domænenavn, der er medtaget som standard sammen med din adresseliste. det har formularen `<domain_name>.onmicrosoft.com`. Du kan navngive den baseret på navnet på den mappe eller et andet domænenavn, du ejer. Senere, kan du tilføje et andet domænenavn, som organisationen allerede bruger.

5. **Land eller område**, Vælg din landestandard.

Se [Hvad er en Azure AD-mappe][]kan finde flere oplysninger om AD?


### <a name="create-a-management-certificate-for-azure"></a>Oprette et Management-certifikat til Azure

Azure SDK til Java bruger management certifikater til at godkende med Azure-abonnementer. Dette er x.509-v3 certifikater du bruger til at godkende en klientprogrammet, der bruger Service Management API til at handle på vegne af ejeren af abonnementet til at administrere abonnementsressourcer.

Koden i denne procedure bruger et selvsigneret certifikat til at godkende med Azure. Denne fremgangsmåde skal du oprette et certifikat, og Overfør den til [Azure klassisk portal][] på forhånd. Dette omfatter følgende trin:

- Oprette en PFX-fil, der repræsenterer dit klientcertifikat og gemme den lokalt.
- Opret et management-certifikat (virksomheden-fil) fra PFX-fil.
- Overføre filen virksomheden til abonnementet Azure.
- Konvertere filen PFX til JKS, fordi Java bruger dette format til at godkende ved hjælp af certifikater.
- Skriv programmets godkendelse kode, som refererer til den lokale JKS-fil.

Når du har fuldført denne procedure, certifikatet virksomheden er placeret i abonnementet Azure og JKS certifikatet er placeret på dit lokale drev. Se [oprette og overføre et Management certifikat til Azure][]kan finde flere oplysninger om administration af certifikater.


#### <a name="create-a-certificate"></a>Oprette et certifikat

Åbn en kommandokonsol på dit operativsystem for at oprette din egen selvsigneret certifikat, og Kør følgende kommandoer.

> **Note:**  Den computer, hvorpå du kører denne kommando skal have JDK installeret. Stien til keytool afhænger også af den placering, hvor du har installeret JDK. Se [nøgle og værktøj til styring af certifikat (keytool)][] i Java onlinedokumenter kan finde flere oplysninger.

Sådan oprettes .pfx-fil:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Sådan oprettes .cer-filen:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

hvor:

- `<java-install-dir>`er stien til den mappe, hvor du har installeret Java.
- `<keystore-id>`er keystore element-id (for eksempel `AzureRemoteAccess`).
- `<cert-store-dir>`er stien til den mappe, hvor du vil gemme certifikater (for eksempel `C:/Certificates`).
- `<cert-file-name>`er navnet på certifikatfilen (for eksempel `AzureWebDemoCert`).
- `<password>`er den adgangskode, du vælger at beskytte certifikatet det skal være mindst 6 tegn. Du kan angive ingen adgangskode, selvom det ikke anbefales.
- `<dname>`er det entydige navn X.500 være knyttet til alias, og der bruges som felterne udsteder og emne i et selvsigneret certifikat.

Se [oprette og overføre et Management certifikat til Azure][]kan finde flere oplysninger.


#### <a name="upload-the-certificate"></a>Upload certifikatet

Gå til siden **Indstillinger** i klassisk portalen for at overføre et selvsigneret certifikat til Azure, og derefter klikke på fanen **Management beviser** . Klik på **Overfør** nederst på siden, og Naviger til placeringen for den virksomheden-fil, du har oprettet.


#### <a name="convert-the-pfx-file-into-jks"></a>Konvertere filen PFX til JKS

I Windows kommandoprompten (kører som administrator), cd til den mappe, der indeholder certifikater og køre følgende kommando, hvor `<java-install-dir>` er den mappe, hvor du har installeret Java på din computer:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Når du bliver bedt om det, Angiv destination keystore adgangskode. Dette er adgangskoden til filen JKS.

2. Når du bliver bedt om det, angive kilde keystore adgangskode. Dette er den adgangskode, du har angivet for PFX-fil.

De to adgangskoder behøver ikke at være den samme. Du kan angive ingen adgangskode, selvom det ikke anbefales.


## <a name="build-a-web-app-creation-application"></a>Oprette en Web App oprettelse af programmet

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Oprette Eklipse arbejdsområde og Maven projekt

I dette afsnit kan du oprette et arbejdsområde og en Maven projekt for web app oprettelse af programmet, med navnet AzureWebDemo.

1. Oprette et nyt Maven projekt. Klik på **fil > Ny > Maven Project**. Vælg **Opret et enkelt projekt** og **Brug standardplaceringen arbejdsområde**i **Maven projekt**.

2. På den anden side i **Maven projekt**skal du angive følgende:

    - Gruppe-ID:`com.<username>.azure.webdemo`
    - Genstand-ID: AzureWebDemo
    - Version: 0.0.1-SNAPSHOT
    - Emballagen: glas
    - Navn: AzureWebDemo

    Klik på **Udfør**.

3. Åbn det nye projekt pom.xml filen i Projektstifinder. Vælg fanen **afhængigheder** . Dette er et nyt projekt, findes ingen pakker endnu.

4. Åbne visningen Maven typer lagre. **Skal du klikke på vinduet > Åbn visning > andre > Maven > Maven typer lagre** og klikke på **OK**. Visningen **Maven typer lagre** vises nederst i IDE.

5. Åbn **Globale typer lagre**, skal du højreklikke på det **centrale** lager, og vælg **Genopbygge indeks**.

    ![][1]
    
    Dette trin kan tage adskillige minutter afhængigt af hastigheden på din forbindelse. Når indekset genopbygges, skal du se Microsoft Azure-pakkerne i det **centrale** Maven-lager.

6. Klik på **Tilføj**i **afhængigheder**. Angiv i **Enter gruppe ID...** `azure-management`. Vælg pakkerne til grundlæggende administration og administration af App-tjenesten Web Apps:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Note:** Hvis du opdaterer afhængighederne efter en ny-versionen, skal du tilføje igen hver af afhængighederne på denne liste.
    > Når du klikker på **Tilføj** og vælge hver afhængighed, vises det med tallet for den nye version på listen **afhængigheder** .

Klik på **OK**. Azure-pakker vises på listen **afhængigheder** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Skrive Java-kode til at oprette en Web-App ved at ringe til Azure SDK

Skriv derefter den kode, der kalder API'er i Azure SDK til Java til at oprette App Service WebApp.

1. Oprette en Java-klasse indeholde koden overordnet punkt. Højreklik på projektnoden i Projektstifinder, og vælg **Ny > klasse**.

2. **Nye Java-klasse**, Angiv et navn i klassen `WebCreator` og Markér afkrydsningsfeltet **offentlige statisk ugyldig hoved** . Valgene skal se ud som følger:

    ![][2]

3. Klik på **Udfør**. WebCreator.java filen vises i Projektstifinder.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Ringe til Azure API for at oprette en App Service WebApp


#### <a name="add-necessary-imports"></a>Tilføje det er nødvendigt import

Tilføj følgende importen i WebCreator.java, Denne import giver adgang til klasser i administration af biblioteker til forbrug Azure API'er:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definere den overordnede opslagsord punkt klasse

Da formålet med programmet AzureWebDemo er at oprette en App-tjenesten Web App, navnet på den primære klasse for dette program `WebAppCreator`. Denne klasse giver den overordnede opslagsord punkt kode, der ringer op til Azure Service Management API for at oprette WebApp.

Tilføj følgende parameterdefinitioner for WebApp og webspace. Du skal angive dine egne Azure abonnement-ID og certifikat oplysninger.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

hvor:

- `<subscription-id>`er det Azure abonnement-ID, hvor du vil oprette ressourcen.
- `<certificate-store-path>`er stien og filnavnet til filen JKS i mappen lokale certifikat store. For eksempel `C:/Certificates/CertificateName.jks` for Linux og `C:\Certificates\CertificateName.jks` til Windows.
- `<certificate-password>`er den adgangskode, du angav, da du oprettede dit JKS certifikat.
- `webAppName`kan være en hvilken som helst navn, du vælger. Denne fremgangsmåde bruger navnet `WebDemoWebApp`. Det fulde domænenavn er det `webAppName` med den `domainName` føjet, så i dette tilfælde fuld domænet er `webdemowebapp.azurewebsites.net`.
- `domainName`skal være angivet som vist ovenfor.
- `webSpaceName`skal være en af de værdier, der er defineret i klassen [WebSpaceNames][] .
- `appServicePlanName`skal være angivet som vist ovenfor.

> **Note:** Hver gang du køre dette program, du vil ændre værdien af `webAppName` og `appServicePlanName` (eller slette online på portalen Azure) før du installerer programmet igen. Ellers mislykkes udførelse af, fordi den samme ressource findes allerede på Azure.


#### <a name="define-the-web-creation-method"></a>Definere metoden web oprettelse

Derefter skal du angive en metode til at oprette WebApp. Denne metode, `createWebApp`, angiver parametrene for WebApp og webspace. Det opretter også og konfigurerer den App-tjenesten Web Apps management-klient, der er defineret af objektet [WebSiteManagementClient][] . Management-klienten er for at oprette Web Apps. Den indeholder RESTful webtjenester, der tillader programmer til at administrere webapps (udføre handlinger, som opretter f.eks, Opdater og Slet) ved at ringe til service management API.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Koden output vil blive sendt HTTP-status for det svar, der angiver lykkes eller mislykkes, og hvis det lykkes, kan udskrives på navnet på den oprettede WebApp.


#### <a name="define-the-main-method"></a>Definere metoden main()

Angiv den main() metode kode, der kalder createWebApp() for at oprette WebApp.

Til sidst kalder `createWebApp` fra `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Køre programmet og bekræfte oprettelse af web app

For at bekræfte, at dit program køres, skal du klikke på **køres > køre**. Når programmet afsluttes køres, skal du se følgende output i konsollen Eklipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Log på portalen Azure klassisk, og klik på **Web Apps**. Den nye WebApp skal vises på listen Web Apps i et par minutter.


## <a name="deploying-an-application-to-the-web-app"></a>Implementere et program til WebApp

Når du har køre AzureWebDemo og oprettet den nye WebApp, log på portalen klassisk, skal du klikke på **Online**og vælge **WebDemoWebApp** på listen **Web Apps** . Klik på **Gennemse** i den online dashboardside (eller klik på URL-adressen, `webdemowebapp.azurewebsites.net`) til at navigere til den. Du får vist en tom pladsholder side, fordi intet indhold der er blevet publiceret til online endnu.

Næste du opretter et "Hej verden" til computeren og installerer det til WebApp.


### <a name="create-a-jsp-hello-world-application"></a>Oprette et JSP Hej verden tjenesteprogram

#### <a name="create-the-application"></a>Oprette programmet

For at vise, hvordan du installerer et program på internettet, viser følgende procedure, hvordan til at oprette et enkelt "Hej verden" Java-program, og Overfør det til App-tjenesten Web App, der har oprettet dit program.

1. Klik på **fil > Ny > dynamisk Web Project**. Kald den `JSPHello`. Du behøver ikke at ændre andre indstillinger i denne dialogboks. Klik på **Udfør**.

    ![][3]

2. Udvid **JSPHello** projektet i Projektstifinder, skal du højreklikke på **WebContent**og derefter skal du klikke på **Ny > JSP fil**. Navngiv den nye fil i dialogboksen Ny JSP fil `index.jsp`. Klik på **Næste**.

3. Vælg **Ny JSP fil (html)** i dialogboksen **Vælg JSP skabelon** , og klik på **Udfør**.

4. Tilføj følgende kode i i index.jsp, den `<head>` og `<body>` mærke sektioner:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Køre programmet Hej verden i localhost

Før du kører dette program, skal du konfigurere et par egenskaber.

1. Højreklik på **JSPHello** projektet, og vælg **Egenskaber**.

2. I dialogboksen **Egenskaber** : Vælg **Java opbygge sti**, vælge fanen **rækkefølge og Eksportér** , kontrollere **JRE systembibliotek**og derefter skal du klikke på **op** til at flytte til toppen af listen.

    ![][4]

3. Også i dialogboksen **Egenskaber** : Vælg **Rettet eksekverbar kode** , og klik på **Ny**.

4. Vælg en server som **Apache Tomcat v7.0** i dialogboksen **Ny Server Runtime-miljø** , og klik på **Næste**. I dialogboksen **Tomcat Server** angivet **navn** til `Apache Tomcat v7.0`, og angiv **Tomcat Installation-mappen** til den mappe, hvor du har installeret versionen af Tomcat server, du vil bruge.

    ![][5]

    Klik på **Udfør**.

5. Du kan derefter tilbage til siden **Rettet eksekverbar kode** i dialogboksen **Egenskaber** . Vælg **Apache Tomcat v7.0**, og klik derefter på **OK**.

    ![][6]

6. Klik på **Kør**i menuen Eklipse **køre** . I dialogboksen **Kør som** , Vælg **Kør på serveren**. Vælg **Tomcat v7.0 Server**i dialogboksen **Kør på serveren** :

    ![][7]

    Klik på **Udfør**.

7. Når programmet kører, skal du se siden **JSPHello** vises i et localhost vindue i Eklipse (`http://localhost:8080/JSPHello/`), vise følgende meddelelse:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Eksportere programmet som en KRIG

Eksportere web project-filer som en web-arkivfilen (KRIG), så du kan installere det i WebApp. De følgende web project-filer, der er placeret i mappen WebContent:

    META-INF
    WEB-INF
    index.jsp

1. Højreklik på mappen WebContent, og vælg **Eksporter**.

2. Klik på i dialogboksen **Eksportér Vælg** **Web > KRIG** filen, og klik derefter på **Næste**.

3. Vælg mappen src i det aktuelle projekt i dialogboksen **KRIG eksportere** , og indeholder navnet på filen KRIG i slutningen. Eksempel:

    `<project-path>/JSPHello/src/JSPHello.war`

Du kan finde flere oplysninger om installation af KRIG filer, [Tilføj en Java-program til Azure App Service Web Apps](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Implementere Hej verden programmet ved hjælp af FTP

Vælg en tredjeparts FTP-klienten til at publicere programmet. Denne procedure beskrives to muligheder: konsollen Kudu indbygget i Azure; og FileZilla, et populære værktøj med en praktisk, grafiske brugergrænseflade.

> **Note:** Azure-værktøjskassen til Eklipse understøtter installation på lagerplads konti og skytjenester, men understøtter i øjeblikket ikke installation til webapps. Du kan installere lagerplads konti og skytjenester ved hjælp af et projekt til installation af Azure, som beskrevet i [oprette en Hej verden programmet til Azure i Eklipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), men ikke på webapps. Bruge andre metoder som FTP- eller GitHub til at overføre filer til din online.

> **Note:** Vi anbefaler ikke ved hjælp af FTP fra kommandoprompten Windows (kommandolinjen FTP.EXE værktøjet, som leveres med Windows). FTP-klienter, der bruger active FTP, såsom FTP.EXE, mislykkes ofte arbejder over firewalls. Aktive FTP angiver en intern LAN-baseret adresse, som en FTP-server mislykkes sandsynligt til at oprette forbindelse.

Under følgende emner kan finde flere oplysninger om installation til en App Service online ved hjælp af FTP:

- [Installere ved hjælp af et FTP-værktøj](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Konfigurere installation legitimationsoplysninger

Kontrollér, at du har kørt programmet **AzureWebDemo** til at oprette en web-app. Du kan overføre filer til denne placering.

1. Log på portalen klassisk, og klik på **Web Apps**. Sørg for, at **WebDemoWebApp** vises på listen over webapps, og Sørg for, at den kører. Klik på **WebDemoWebApp** for at åbne **dets dashboardside** .

2. På siden **Dashboard** under **Hurtig Glance**, klik på **Konfigurer din installation legitimationsoplysninger** (Hvis du allerede har installation legitimationsoplysninger, dette læser **nulstille dine legitimationsoplysninger installation**).

    Installation legitimationsoplysninger er knyttet til en Microsoft-konto. Du skal angive et brugernavn og adgangskode, du kan bruge til at udrulle ved hjælp af ciffer og FTP. Du kan bruge disse legitimationsoplysninger skal installeres til en hvilken som helst web app i alle Azure abonnementer, der er knyttet til din Microsoft-konto. Angive legitimationsoplysninger for ciffer og FTP-installation i dialogboksen, og Registrer brugernavnet og adgangskoden til fremtidig brug.


#### <a name="get-ftp-connection-information"></a>Få oplysninger om FTP-forbindelse

Hvis du vil bruge FTP til at installere programfiler til nyoprettede WebApp, skal du hente oplysninger om forbindelsen. Der er to måder at få oplysninger om forbindelsen. En metode er at gå til **den online dashboardside;** anden måde er for at hente World Wide web Apps publicere profil. Publicer profilen er en XML-fil, der indeholder oplysninger som FTP-host servernavnet og logonoplysningerne legitimationsoplysninger for din online i Azure App Service. Du kan bruge denne brugernavn og din adgangskode for at installere på en hvilken som helst web app i alle abonnementer, der er knyttet til den Azure-konto, ikke kun denne.

Sådan hentes FTP-forbindelsesoplysningerne fra den online blade i [Azure-portalen][]:

1. Finde under **Essentials**, og Kopiér **FTP-hostname**. Dette er en URI svarende til `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Finde under **Essentials**, og Kopiér **FTP/installation brugernavn**. Dette får formular *webappname\deployment brugernavn*; for eksempel `WebDemoWebApp\deployer77`.

Sådan hentes FTP-forbindelsesoplysningerne fra Publicer profil:

1. Klik på **Hent Udgiv profil**i det online blade. Dette vil hente en .publishsettings-fil til dit lokale drev.

2. Åbn filen .publishsettings i en XML-editor eller teksteditor, og find den `<publishProfile>` element, der indeholder `publishMethod="FTP"`. Det skal se ud som følger:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Bemærk, at online `publishProfile` kort, indstillinger for at FileZilla webstedsadministrator indstillingerne på følgende måde:

- `publishUrl`er den samme som **FTP-værtsnavn**, angiver du værdien i **Host**.
- `publishMethod="FTP"`betyder, at du angiver **Protocol** til **FTP - File Transfer Protocol**og **kryptering** til at **bruge almindelig FTP**.
- `userName`og `userPWD` er taster til det faktiske brugernavn og adgangskode værdier, du angav, når du nulstiller installation legitimationsoplysninger. `userName`er den samme som **installation / FTP-bruger**. De tilknyttes **brugernavn** og **adgangskode** i FileZilla.
- `ftpPassiveMode="True"`betyder, at FTP-stedet bruger passive FTP-overførslen. Vælg **Passive** under fanen **Indstillinger for overførsel** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Konfigurere Web-App Hvis du vil placere en Java-program

Før du udgiver programmet, skal du ændre et par konfigurationsindstillinger, så WebApp kan være vært for en Java-program.

1. Gå til **den online dashboardside** i portalen klassisk, og klik på **Konfigurer**. Angiv følgende indstillinger på siden **Konfigurer** .

2. I **Java version** er standard til **fra**. Vælg Java versionen dit program mål for eksempel 1.7.0_51. Når du gør dette, også sørge for, at **Web objektbeholder** er indstillet til en version af Tomcat Server.

3. Tilføj index.jsp i **Standarddokumenter**, og flytte den op til toppen af listen. (Standardfilen for webapps er hostingstart.html).

4. Klik på **Gem**.


#### <a name="publish-your-application-using-kudu"></a>Publicere dit program ved hjælp af Kudu

En metode til at publicere programmet er at bruge Kudu fejlfinding konsollen indbygget i Azure. Kudu kendes skal være stabil og ensartede med App-tjenesten Web Apps og Tomcat Server. Du kan få adgang til konsollen til online ved at gå til en URL-adresse i formularen følgende:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. For denne procedure, og er konsollen Kudu placeret på følgende URL Gå til denne placering:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Vælg menuen øverste **fejlfinding Console > CMD**.

3. I kommandolinjen console skal du gå til `/site/wwwroot` (eller klik på `site`, derefter `wwwroot` i mappevisning øverst på siden):

    `cd /site/wwwroot`

4. Når du angiver **Java version**, skal Tomcat server oprette en WebApp mappe. I kommandolinjen console skal du gå til mappen WebApp:

    `mkdir webapps`

    `cd webapps`

5. Træk JSPHello.war fra `<project-path>/JSPHello/src/` og slippe den i visningen Kudu directory under `/site/wwwroot/webapps`. Ikke trække det til området "Træk her for at overføre og zip", fordi Tomcat vil Udpak filen.

  ![][8]

Første JSPHello.war vises i området directory alene:

  ![][9]

I kort tid (sandsynligvis mindre end 5 minutter) udpakke Tomcat Server KRIG filen i en udpakket JSPHello-mappe. Klik på rodmappen for at se, om index.jsp har pakket og kopieret der. Hvis det er tilfældet, skal du gå tilbage til mappen WebApp at se, om mappen Udpakket JSPHello er blevet oprettet. Hvis du ikke kan se disse elementer, Vent, og Gentag.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Publicere dit program ved hjælp af FileZilla (valgfrit)

Et andet værktøj, du kan bruge til at publicere programmet er FileZilla, en populære fra tredjepart FTP-klient med en praktisk, grafiske brugergrænseflade. Du kan hente og installere FileZilla fra [http://filezilla-project.org/](http://filezilla-project.org/) , hvis du ikke allerede har den. Du kan finde flere oplysninger om brug af klienten, se [FileZilla dokumentation](https://wiki.filezilla-project.org/Documentation) og dette blogindlæg på [FTP-klienter - del 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. Klik på i FileZilla, **fil > webstedsadministrator**.
2. Klik på **Nyt websted**i dialogboksen **Webstedsadministrator** . Et nyt tomt FTP-sted vises i **Vælg posten** beder dig om at angive et navn. For denne procedure, og kald den `AzureWebDemo-FTP`.

    Angiv følgende indstillinger under fanen **Generelt** :
    - **Host:** Angiv **FTP-værtsnavn** , som du kopierede fra dashboardet.
    - **Port:** (Lad dette stå tomt, som dette er en passiv overførsel og serveren bestemmer porten, der skal bruges.)
    - **Protokol:** FTP-File Transfer Protocol
    - **Kryptering:** Bruge almindelig FTP
    - **Logon Type:** Normal
    - **Bruger:** Angiv installationen / FTP-bruger, som du kopierede fra dashboardet. Dette er den fulde FTP-brugernavn, som har formular *webappname\username*.
    - **Adgangskode:** Angiv den adgangskode, du angav, da du angive installation legitimationsoplysninger.

    Vælg **Passive**under fanen **Indstillinger for overførsel** .

3. Klik på **Opret forbindelse**. Hvis lykkes, Filezilla's console vises en `Status: Connected` meddelelse og problem en `LIST` kommando for at få vist indholdet directory.

4. Vælg den kildemappe, hvor filen JSPHello.war er placeret, i panelet **lokale** websted stien bliver ligner følgende:

    `<project-path>/JSPHello/src/`

5. Vælg destinationsmappen i panelet **Remote** websted. Du vil installere KRIG filen til den `webapps` mappe i roden af WebApp. Gå til `/site/wwwroot`, skal du højreklikke på `wwwroot`, og vælg **Opret mappe**. Navngiv mappen `webapps` og angive denne mappe.

6. Overføre JSPHello.war til `/site/wwwroot/webapps`. Vælg JSPHello.war i listen over **lokale** filer, skal du højreklikke på den, og vælg **Overfør**. Du skal se det vises i `/site/wwwroot/webapps`.

7. Når du kopierer JSPHello.war til mappen WebApp, der automatisk Pak Tomcat Server (udpakke) filer i filen KRIG. Selvom Tomcat Server begynder pakke næsten med det samme, kan det tage lang tid (muligvis timer) for filerne, der vises i FTP-klienten.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Køre programmet Hej verden på Web App

1. Når du har overført filen KRIG og bekræftet, at Tomcat server har oprettet en udpakket `JSPHello` directory, Gennemse for at `http://webdemowebapp.azurewebsites.net/JSPHello` køre programmet.

    > **Note:** Hvis du klikker på **Gennemse** fra portalen klassisk, kan du få websiden standard siger "webprogrammet Java baseret er blevet oprettet." Det være nødvendigt at opdatere websiden for at få vist programmet output i stedet for standard websiden.

2. Når programmet kører, skal du se en webside med følgende output:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Rydde op i Azure ressourcer

Denne fremgangsmåde opretter en App Service WebApp. Du vil blive faktureret for ressourcen, så længe den findes. Medmindre du planlægger at fortsætte med at bruge WebApp til test eller udvikling, bør du overveje at stoppe eller slette den. En WebApp, der er blevet standset vil stadig betale en lille gebyr, men du kan genstarte når som helst. Slette en WebApp sletter alle data, du har overført til den.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure-værktøjskassen til Eklipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure klassisk portal]: https://manage.windowsazure.com
[Hvad er en Azure AD-mappe]: http://technet.microsoft.com/library/jj573650.aspx
[Oprette og overføre et Management-certifikat til Azure]: ../cloud-services/cloud-services-certs-create.md
[Nøgle og værktøj til styring af certifikat (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Azure-portalen]: https://portal.azure.com
