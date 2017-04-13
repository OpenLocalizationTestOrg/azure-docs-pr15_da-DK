<properties
    pageTitle="Azure App Service WebApp Avanceret config og filtypenavne"
    description="Bruge XML-dokument Transformation(XDT) erklæringer til at transformere ApplicationHost.config filen i din Azure App Service online og Tilføj privat udvidelser for at aktivere administration af brugerdefinerede handlinger."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure App Service WebApp Avanceret config og filtypenavne

Ved hjælp af [XML-dokument Transformation](http://msdn.microsoft.com/library/dd465326.aspx) (XDT) erklæringer, kan du transformere [filen applicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) i din online i Azure App-tjeneste. Du kan også bruge XDT erklæringer til Tilføj privat udvidelser for at aktivere brugerdefinerede web app administration handlinger. Denne artikel indeholder en stikprøve PHP Manager web app udvidelse, der gør det muligt for administration af PHP indstillinger via en webgrænseflade.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Avanceret konfiguration gennem ApplicationHost.config
App Service platform indeholder fleksibilitet og kontrol til konfiguration af web app. Selvom den standard IIS ApplicationHost.config konfigurationsfil ikke er tilgængelig for direkte redigering i App-tjeneste, understøtter platformen en deklarativ ApplicationHost.config transformation model, der er baseret på XML-dokument Transformation (XDT).

Hvis du vil bruge denne transformation funktionalitet, kan du oprette en ApplicationHost.xdt-fil med XDT indhold og placere under websted roden (d:\home\site) i [Kudu Console](https://github.com/projectkudu/kudu/wiki/Kudu-console). Du skal muligvis genstarte online før ændringerne kan træde i kraft.

Følgende eksempel på applicationHost.xdt viser, hvordan du føjer en ny brugerdefineret miljøvariablen til en WebApp, der bruger PHP 5.4.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


En logfil med transformation status og detaljer er tilgængelig fra FTP-roden under LogFiles\Transform.

Du kan finde flere eksempler [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Bemærk!**<br />
Elementer på listen over moduler under `system.webServer` ikke kan fjernes eller genbestilles, men det er muligt at udvide listen.


##<a id="extend"></a>Udvide din online

###<a id="overview"></a>Oversigt over private web app filtypenavne

App Service understøtter web app udvidelser som en udvidelsesmuligheder af punkt til administrative handlinger. Faktisk implementeres nogle App Service platformfunktioner som forudinstalleret filtypenavne. Mens forudinstalleret platform filtypenavne ikke kan redigeres, kan du oprette og konfigurere privat udvidelser til din egen WebApp. Denne funktionalitet er også afhængig af XDT erklæringer. De vigtigste trin til oprettelse af en privat web app-filtypenavnet er følgende:

1. Web-app lokalnummer **indhold**: oprette et webprogram, der understøttes af App Service
2. Web app lokalnummer **erklæring**: oprette en ApplicationHost.xdt-fil
3. Web app lokalnummer **installation**: placere indhold i mappen SiteExtensions under`root`

Interne links til WebApp skal pege på en sti i forhold til den angivet i filen ApplicationHost.xdt sti. Ændringer til filen ApplicationHost.xdt kræver en web app-Papirkurv.

**Bemærk**: Yderligere oplysninger om disse vigtige elementer er aktiveret i [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Et detaljeret eksempel er inkluderet for at illustrere trinnene til at oprette og aktivere filtypenavnet privat web app. Kildekode for eksemplet PHP Manager, der følger efter kan hentes fra [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Web app lokalnummer eksempel: PHP Manager

PHP Manager er filtypenavnet web app, som web app giver administratorer mulighed for nemt at få vist og konfigurere deres PHP indstillinger ved hjælp af en grænseflade i stedet for at skulle ændre PHP .ini-filer direkte. Fælles konfigurationsfiler til PHP medtage php.ini filen er placeret under programfiler og. user.ini filen er placeret i rodmappen for din online. Da filen php.ini ikke kan redigeres direkte på App Service-platformen, bruger filtypenavnet PHP Manager det. user.ini fil for at anvende ændringerne.

####<a id="PHPwebapp"></a>Webprogrammet PHP Manager

Følgende er startsiden for PHP Manager-installationen:

![TransformSitePHPUI][TransformSitePHPUI]

Som du kan se, er filtypenavnet web app, ligesom en almindelig webprogram, men med en ekstra ApplicationHost.xdt-fil, der er placeret i rodmappen af WebApp (flere oplysninger om filen ApplicationHost.xdt er tilgængelige i næste afsnit i denne artikel).

Filtypenavnet PHP Manager blev oprettet ved hjælp af skabelonen Visual Studio ASP.NET MVC 4-webprogrammet. Den følgende visning fra Solution Explorer viser strukturen i filtypenavnet PHP Manager.

![TransformSiteSolEx][TransformSiteSolEx]

Kun speciel logik, der kræves for fil I/O er ved at angive, hvor mappen Wwwroot af WebApp er placeret. Som kodeeksemplet nedenfor viser, miljø variable "startside" Angiver den online rodsti og wwwroot stien kan være opbygget ved at tilføje "site\wwwroot":

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Når du har stien til den mappe, kan du bruge almindelig fil i/o-handlinger til at læse og skrive til filer.

Et punkt advarsel med web app filtypenavne opfatter håndtering af interne links.  Hvis du har en hvilken som helst links i dine HTML-filer, som giver absolutte stier til interne links på din online, skal du kontrollere disse links er foran med navnet på dit lokalnummer som din rod. Det er nødvendigt, fordi på rodwebstedet for din lokalnummer er nu "/`[your-extension-name]`/" i stedet for at der kun "/", så alle interne kæder skal opdateres i overensstemmelse hermed. Lad os antage, at din kode indeholder et link til følgende:

`"<a href="/Home/Settings">PHP Settings</a>"`

Når linket er en del af filtypenavnet web app, skal være linket i følgende format:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Du kan løse dette krav ved enten ved hjælp af kun relative stier i dit webprogram, eller hvis det ASP.NET-programmer, ved hjælp af den `@Html.ActionLink` metode, der opretter de relevante links for dig.

####<a id="XDT"></a>Filen applicationHost.xdt

Koden for din web app udvidelse går under %HOME%\SiteExtensions\[dine filtypenavn]. Vi vil kalder dette lokalnummer roden.  

Hvis du vil registrere din web app-udvidelse med filen applicationHost.config, skal du placere en fil kaldet ApplicationHost.xdt i roden filtypenavn. Indholdet af filen ApplicationHost.xdt skal være på følgende måde:

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

Det navn, du vælger som dit lokalnummer navn skal have samme navn som dit lokalnummer rodmappen.

Dette er effekten af at tilføje en ny programmet sti til den `system.applicationHost` listen over websteder under webstedet SCM. Webstedet SCM er et websted administration slutpunkt med bestemt adgangslegitimationsoplysninger. Der er URL-adressen `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Web app lokalnummer installation

For at installere web app-filtypenavnet, du kan bruge FTP til at kopiere alle filer for dit webprogram til den `\SiteExtensions\[your-extension-name]` mappe af web app, du vil installere filtypenavnet.  Sørg for at kopiere filen ApplicationHost.xdt til denne placering samt. Genstart din online for at aktivere filtypenavnet.

Du skal kunne se dine web app lokalnummer på:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Bemærk, at URL-adressen ligner URL-adressen for din online, bortset fra at den bruger HTTPS og indeholder ".scm".

Det er muligt at deaktivere alle privat (ikke forudinstalleret) udvidelser til din online under udvikling og efterforskninger ved at tilføje en app-indstillinger med tasten `WEBSITE_PRIVATE_EXTENSIONS` og en værdi på `0`.

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
