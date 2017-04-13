<properties
    pageTitle="Node.js Introduktion til | Microsoft Azure"
    description="Lær at oprette et simpelt Node.js webprogram og installere den på en Azure skybaseret tjeneste."
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Dette selvstudium viser, hvordan du opretter en simpel Node.js program, der kører i en Azure skybaseret tjeneste. Cloud Services er SVG skyen programmer i Azure byggesten. De Tillad adskillelsen og administration af uafhængige og skala ud af front end- og back end-komponenterne i dit program.  Cloud Services giver en robust dedikeret virtuel maskine til pålideligt vært for hver rolle.

Flere oplysninger om Cloud Services, og hvordan de i sammenligning med Azure websteder og virtuelle maskiner, se [Azure websteder, Cloud Services og virtuelle maskiner sammenligning].

>[AZURE.TIP] Vil du oprette et enkelt websted? Hvis din scenario omfatter kun en enkelt websted front end, kan du overveje at [bruge en lette WebApp]. Du kan nemt opgradere til en skybaseret tjeneste, som din online vokser og ændre dine behov.

Ved at følge dette selvstudium, kan du oprette et simpelt webprogram, der er hostet i en web rolle. Du kan bruge Beregn emulatoren til at teste dit program lokalt og derefter installere det ved hjælp af PowerShell kommandolinjen værktøjer.

Programmet som en simpel "Hej verden":

![En webbrowser, der viser websiden Hej verden][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Forudsætninger

> [AZURE.NOTE] Dette selvstudium bruger Azure PowerShell, som kræver Windows.

- Installere og konfigurere [Azure Powershell].
- Hent og Installer [Azure SDK til .NET 2.7]. Vælg i konfigurationen installation:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## <a name="create-an-azure-cloud-service-project"></a>Oprette en Azure skybaseret tjeneste projekt

Du kan udføre følgende opgaver for at oprette et nyt projekt i Azure skybaseret tjeneste sammen med grundlæggende Node.js stilladser:

1. Kør **Windows PowerShell** som Administrator; søge efter **Windows PowerShell**fra **Menuen Start** eller **Startskærmbilledet**.

2. [Oprette forbindelse PowerShell] til dit abonnement.

3. Angiv følgende PowerShell-cmdlet til at oprette for at oprette projektet:

        New-AzureServiceProject helloworld

    ![Resultatet af kommandoen ny AzureService helloworld][The result of the New-AzureService helloworld command]

    **Ny AzureServiceProject** cmdlet genererer en grundlæggende struktur til publicering af et Node.js program til en skybaseret tjeneste. Den indeholder konfigurationsfiler, der er nødvendige for at publicere til Azure. Cmdletten ændres også din arbejdsmappe til mappen til tjenesten.

    Cmdletten opretter følgende filer:

    -   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** og **ServiceDefinition.csdef**: Azure-specifikke filer nødvendige til udgivelse af programmet. Se [Oversigt over oprettelse af en hostet tjeneste til Azure]kan finde flere oplysninger.

    -   **deploymentSettings.json**: gemmer lokale indstillinger, der bruges ved installation Azure PowerShell-cmdletter.

4.  Skriv følgende kommando for at tilføje en ny web rolle:

        Add-AzureNodeWebRole

    ![Output fra kommandoen Tilføj AzureNodeWebRole][The output of the Add-AzureNodeWebRole command]

    **Tilføj AzureNodeWebRole** cmdlet opretter et grundlæggende Node.js til computeren. Den ændrer også **.csfg** og **.csdef** filer for at føje konfigurationsposter til den nye rolle.

    > [AZURE.NOTE] Hvis du ikke angiver en rollenavn, bruges et standardnavn. Du kan angive et navn som den første parameter cmdlet:`Add-AzureNodeWebRole MyRole`

Appen Node.js er defineret i den fil **server.js**, findes i mappen for rollen web (**WebRole1** som standard). Her er koden:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Denne kode er stort set det samme som "Hej verden" eksempel på webstedet for [nodejs.org] , bortset fra den anvender det portnummer, der er tildelt ved cloud-miljø.

## <a name="deploy-the-application-to-azure"></a>Installere programmet til Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

### <a name="download-the-azure-publishing-settings"></a>Hent de Azure udgivelsesindstillinger

Du skal installere programmet til Azure, skal du først hente udgivelsesindstillingerne for abonnementet Azure.

1.  Kør følgende Azure PowerShell-cmdlet:

        Get-AzurePublishSettingsFile

    Dette vil Brug browseren til at gå til overførselssiden Publicer indstillinger. Du muligvis bedt om at logge på med en Microsoft-Account. Hvis det er tilfældet, skal du bruge den konto, der er knyttet til abonnementet Azure.

    Gemme de hentede profil på en filplacering, du nemt kan få adgang til.

2.  Kør følgende cmdlet til at importere den publicering profil, du har hentet:

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] Overvej at slette filen hentede .publishSettings, når du har importeret indstillingerne Udgiv, fordi den indeholder oplysninger, som kan tillade adgang til din konto.

### <a name="publish-the-application"></a>Publicere programmet

Hvis du vil publicere, skal du køre følgende kommandoer:

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** angiver navnet på installationen. Det skal være et entydigt navn, ellers Publicer processen mislykkes. Kommandoen **Get-dato** tegnestifter på en dato/klokkeslæt-streng, der skal gøre navnet entydigt.

- **-Placering** angiver det datacenter, som programmet skal placeres i. Brug cmdlet'en **Get-AzureLocation** for at få vist en liste over tilgængelige datacentre.

- **-Start** åbner et browservindue og navigerer til tjenesten hostet, når installationen er fuldført.

Når publicering lykkes, får du vist et svar, der ligner følgende:

![Output fra kommandoen Publicer AzureService][The output of the Publish-AzureService command]

> [AZURE.NOTE]
> Det kan tage flere minutter, før programmet til at implementere og bliver tilgængelige, når først publiceret.

Når installationen er fuldført, åbnes et browservindue og gå til skytjenesten.

![Et browservindue, der viser Hej verden siden. URL-adressen angiver, at siden er placeret på Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Dit program kører nu på Azure.

**Publicer AzureServiceProject** cmdlet udfører følgende trin:

1.  Opretter en pakke til at installere. Pakken indeholder alle filerne i mappen programmer.

2.  Opretter en ny **lagerplads konto** , hvis der ikke findes. Azure-lager kontoen bruges til at gemme programpakken under installationen. På en sikker måde kan du slette kontoen lagerplads, når installationen er færdig.

3.  Opretter en ny **skybaseret tjeneste** , hvis der ikke allerede findes. En **skybaseret tjeneste** er den beholder, hvori dit program er hostet, når den er installeret til Azure. Se [Oversigt over oprettelse af en hostet tjeneste til Azure]kan finde flere oplysninger.

4.  Udgiver installationspakken til Azure.


## <a name="stopping-and-deleting-your-application"></a>Stoppe og slette dit program

Når du anvender dit program, kan du vil deaktivere den, så du kan undgå ekstra omkostninger. Azure regninger web rolle forekomster i timen server tid consumed. Server tidsforbrug, når dit program er installeret, selvom forekomster ikke kører og er i stoppet tilstand.

1.  I vinduet Windows PowerShell stop tjenesten installationen oprettede i forrige afsnit med følgende cmdlet:

        Stop-AzureService

    Stoppe tjenesten kan tage flere minutter. Når tjenesten er stoppet, modtager du en meddelelse om, at det længere.

    ![Status for kommandoen Stop AzureService][The status of the Stop-AzureService command]

2.  For at slette tjenesten, skal du ringe til følgende cmdlet:

        Remove-AzureService

    Når du bliver bedt om det, kan du angive **Y** for at slette tjenesten.

    Slette tjenesten, kan det tage flere minutter. Du modtager en meddelelse om, at tjenesten er blevet slettet, når tjenesten er blevet slettet.

    ![Status for kommandoen Fjern AzureService][The status of the Remove-AzureService command]

    > [AZURE.NOTE] Sletning af tjenesten sletter ikke lagerplads-konto, der blev oprettet, da tjenesten blev oprindeligt udgivet, og du fortsætter med at blive faktureret for lager, der bruges. Hvis intet andet bruger opbevaring, kan du slette den.

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Node.js Developer Center].

<!-- URL List -->

[Azure websteder, Skytjenester og virtuelle maskiner sammenligning]: ../app-service-web/choose-web-site-cloud-service-vm.md
[Brug af en lette WebApp]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Azure Powershell]: ../powershell-install-configure.md
[Azure SDK til .NET 2.7]: http://www.microsoft.com/en-us/download/details.aspx?id=48178
[Oprette forbindelse PowerShell]: ../powershell-install-configure.md#how-to-connect-to-your-subscription
[nodejs.org]: http://nodejs.org/
[Oversigt over oprettelse af en hostet tjeneste til Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js Developer Center]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
