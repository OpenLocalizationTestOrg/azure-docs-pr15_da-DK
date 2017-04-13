<properties
   pageTitle="Hvordan du kan overføre og publicere et webprogram til en Azure skybaseret tjeneste fra Visual Studio | Microsoft Azure"
   description="Lær at overføre og publicere dit webprogram til en Azure skybaseret tjeneste ved hjælp af Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Sådan: overføre og publicere et webprogram til en Azure skybaseret tjeneste fra Visual Studio

Hvis du vil tage fordel af de værtstjenester og skalerbarhed af Azure, kan du vil overføre og publicere dit webprogram til en Azure skybaseret tjeneste. Du kan køre et webprogram i Azure med minimale ændringer til dit eksisterende program.

>[AZURE.NOTE] Dette emne handler om installation af til skytjenester, ikke til websteder. Du kan finde oplysninger om installation af til websteder, [Implementer en WebApp i Azure App Service](./app-service-web/web-sites-deploy.md).

En liste over bestemte skabeloner, der understøttes til både Visual C# og Visual Basic, i afsnittet **Understøttes projektskabeloner** senere i dette emne.

Du skal først aktivere webprogrammet for Azure fra Visual Studio. Følgende illustration viser de vigtigste trin til at publicere din eksisterende webprogram ved at tilføje en Azure projekt skal bruge til installation. Denne proces føjer en Azure projekt med den nødvendige web rolle til din løsning. Baseret på typen webprojekt, som du har, opdateres egenskaberne for projektet til assemblies også hvis servicepakke kræver yderligere assemblies til installation.

![Publicere et webprogram til skyldes Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] Den **konvertere**, kommandoen **Konverter til Azure skyen Service projekt** vises kun for webprojektet i din løsning. For eksempel er kommandoen ikke tilgængelig for et Silverlight projekt i din løsning.
Når du opretter en servicepakke eller publicere dit program til Azure, kan advarsler eller fejl forekomme. Disse advarsler og fejl kan hjælpe dig med at løse problemer, inden du installerer til Azure. Du kan eksempelvis modtage en advarsel om en manglende samling. Du kan finde flere oplysninger om, hvordan du behandle eventuelle advarsler som fejl, skal du se [konfigurere et Azure skyen Service projekt med Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Hvis du opbygge dit program, køre den lokalt med Beregn emulatoren eller publicere det i Azure, kan du får vist følgende fejlmeddelelse i vinduet med **Fejl** : **den angivne sti, filnavn eller begge dele er for lange**. Denne fejl opstår, fordi længden af det fuldstændige Azure projektnavn er for lang. Længden af det projektnavn, herunder den fulde sti, må ikke være mere end 146 tegn. For eksempel dette er den fulde projektnavn, herunder filstien til en Azure projekt, som er oprettet for et Silverlight-program: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Det være nødvendigt at flytte din løsning til en anden mappe, der har en kortere sti til at reducere længden af det fuldstændige projektnavn.

For at overføre og publicere et webprogram til Azure fra Visual Studio skal du følge disse trin.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Aktivere et webprogram til installation til Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Aktivere et webprogram til installation til Azure

1. Åbne genvejsmenuen for et webprojekt i din løsning for at aktivere dit program til installation til Azure, og vælg Tilføj Azure installationsprojekt.

    Følgende handlinger:

    - Et Azure projekt kaldet `<name of the web project>.Azure` føjes til løsning for dit program.

    - En web rolle for webprojektet føjes til Azure projektet.

    - Egenskaben **Lokale kopi** er indstillet til Sand for en hvilken som helst assemblies, der er nødvendige for MVC 2, 3, skal du MVC MVC, 4 og Silverlight Business-programmer. Dette tilføjer disse assemblies til den servicepakke, der bruges til installation.

  >[AZURE.IMPORTANT] Hvis du har andre assemblies eller filer, der kræves til dette webprogram, skal du manuelt angive egenskaber for disse filer. Finde oplysninger om at angive disse egenskaber, i afsnittet **Omfatter filer i den servicepakke** senere i denne artikel.

  >[AZURE.NOTE] Hvis der allerede findes en web rolle for et bestemt webprojekt i et Azure projekt i løsningen skal du **konvertere**vises **Konverter til Azure skyen Service projekt** ikke i genvejsmenuen for webprojektet.

  Hvis du har flere webprojekter i dit webprogram, og du vil oprette web roller for hvert webprojekt, skal du udføre trinnene i denne procedure for hvert webprojekt. Dette opretter separate Azure projekter for hver web rolle. Hver web project kan udgives separat. Alternativt kan kan du manuelt tilføje en anden web rolle til et eksisterende Azure projekt i dit webprogram. Åbne genvejsmenuen for mappen **roller** i projektet Azure for at gøre dette, skal du vælge **Tilføj**, og klik derefter **Web rolle projekt i løsning**, Vælg projektet for at tilføje som en web rolle, og vælg knappen **OK** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Bruge en Azure SQL-Database til dit program

Hvis du har en forbindelsesstreng for dit webprogram, der bruger en SQL Server-database, der er de lokalt, skal du ændre forbindelsesstrengen er at bruge en forekomst af SQL-Database, der er Azure vært i stedet.

>[AZURE.IMPORTANT] Dit abonnement skal gør det muligt at bruge SQL-Database. Hvis du har adgang til dit abonnement fra [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885), kan du bestemme, hvilke tjenester, der indeholder dit abonnement. De følgende instruktioner gælder for den frigivne [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885). Hvis du bruger [Azure-portalen](http://portal.microsoft.com), gå til den næste procedure.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Bruge en forekomst af SQL-Database i din web rolle for din forbindelsesstreng

1. For at oprette en forekomst af SQL-Database i [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885)skal du følge trinnene i følgende artikel: [oprette en SQL-databaseserver](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Når du konfigurerer firewallreglerne for din forekomst af SQL-Database, skal du markere afkrydsningsfeltet **Tillad andre Azure tjenester til at få adgang til denne server** .

1. Hvis du vil oprette en forekomst af SQL-Database skal bruges til din forbindelsesstreng, skal du følge trinnene i næste afsnit i følgende artikel: [oprette en SQL-Database](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Hvis du vil kopiere forbindelsesstrengen ADO.NET skal bruges til din forbindelsesstreng, skal du udføre følgende trin på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Vælg knappen **Database** , og Åbn derefter noden for det abonnement, du brugte til at oprette din forekomst af SQL-Database.

  1. Hvis du vil have vist de tilgængelige forekomster af SQL-Database, skal du vælge noden **SQL-databaser** .

  1. Vælg databasen, for at få vist egenskaberne for databasen. Visningen **Egenskaber** vises.

      >[AZURE.NOTE] Hvis visningen **Egenskaber** ikke vises, skal du muligvis åbne den ved hjælp af skillelinjen.

  1. Vælg knappen ellipsen (...) ud for Vis for at få vist strengene forbindelse.

    Dialogboksen **Forbindelsesstrenge** vises.

  1. Hvis du vil kopiere forbindelsesstrengen ADO.NET, Markér teksten, og vælg tasterne Ctrl + C.

  1. Vælg knappen **Luk** for at lukke dialogboksen.

1. Hvis du vil erstatte forbindelsesstrengen i filen web.config at bruge denne forekomst af SQL-Database, Åbn filen web.config, fremhæve den eksisterende forbindelse streng post og derefter vælge tasterne Ctrl + V. ADO.NET forbindelsesstrengen for forekomsten af SQL-Database, erstatter eksisterende forbindelsesstrengen.

1. Du skal også tilføje parameteren `MultipleActiveResultSets=True` til forbindelsesstrengen. Forbindelsesstrengen skal have følgende format:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Valgfrit) En anden metode til at ændre forbindelsesstrengen direkte i filen web.config er at tilføje en sektion til en af web.config transformation-filerne, afhængigt af den build konfiguration, som du kan bruge til at oprette din servicepakke. Åbn filen Web.Debug.Config eller Web.Release.Config filen. Tilføj følgende afsnit til denne fil:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Gemme den fil, du har ændret og genudgive dit program.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Bruge en forekomst af SQL-Database ved hjælp af portalen Azure klassisk

1. Vælg noden SQL-databaser på [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

  - Hvis forekomsten af SQL-Database, du vil bruge vises, skal du vælge at åbne den.

  - Hvis du ikke har oprettet en hvilken som helst forekomster, Vælg det relevante link, og derefter oprette en forekomst.

1. Når du åbner eller opretter en databaseforekomst af, kan du vælge linket **Strenge** .

1. Nederst på siden skal du vælge linket til at konfigurere firewall-indstillinger og acceptere standardværdierne eller konfigurere de værdier, du har brug for.

1. Kopiér forbindelsesstrengen ADO.NET, sætte det ind i filen web.config over gamle forbindelsesstrengen for den lokale database, og Sørg for at tilføje `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicere et webprogram til Azure

### <a name="to-publish-a-web-application-to-azure"></a>Publicere et webprogram til Azure

1. Teste programmet i den lokale udvikling miljø ved hjælp af Azure beregne emulator, åbne genvejsmenuen for Azure projektet til rollen web og vælge **Angiv som Start projekt**. Klik på **fejlfinding**, **Starte fejlfinding** (tastatur: **F5**).

    Dialogboksen **Start Azure fejlfinding miljøet** åbner og programmet starter i browseren. Du kan finde specifikke oplysninger om, hvordan du starter hver type webprogram i Beregn emulatoren tabellen i dette afsnit.

1. Hvis du vil konfigurere tjenester for dit program publicere til Azure, skal du have en Microsoft-konto og et Azure-abonnement. Følg trinnene i følgende emne for at konfigurere dine tjenester: [forberede til at udgive eller installere et Azure-program fra Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Åbne genvejsmenuen for webprojektet for at publicere webprogrammet til Azure, og vælg **Udgiv til Azure**.

    Åbner dialogboksen **Publicer Azure-program** og Visual Studio starter installationsprocessen. Du kan finde flere oplysninger om, hvordan du publicere programmet, i afsnittet **publicerer et Azure-program fra Visual Studio** i [publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] Du kan også publicere webprogrammet fra Azure projektet. Åbn genvejsmenuen for Azure projektet for at gøre dette, og vælg **Publicer**.

1. For at få vist status for installationen, kan du få vist vinduet **Azure aktivitetslog** . Denne log vises automatisk, når installationsprocessen starter. Du kan udvide elementet linje i aktivitet loggen for at få vist detaljerede oplysninger, som vist i nedenstående illustration:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Valgfrit) Åbn genvejsmenuen for elementet linje aktivitet log for at annullere installationen, og vælg **Annuller og fjerne**. Dette stopper installationsprocessen og sletter eget udviklingsmiljø fra Azure.

    >[AZURE.NOTE] Hvis du vil fjerne denne installation-miljø, når den er blevet installeret, skal du bruge [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Valgfrit) Når din rolle forekomster har startet, viser Visual Studio automatisk installation miljø i noden **Azure beregne** i **Skyen Stifinder** eller **Server Explorer**. Her kan du få vist status for de enkelte rolle forekomster.

    Følgende illustration viser de rolle forekomster i **Server Explorer** , mens de er stadig i tilstanden initialiserer:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Vælg på pilen ud for din installation for at få adgang til dit program efter installation, når statussen **fuldført** vises i **Azure aktivitet log**. Dette viser URL-adressen for dit program i Azure. Se tabellen nedenfor for at få oplysninger om, hvordan du starter en bestemt type webprogram fra Azure.

    I følgende tabel vises de mere at vide om at vælge bestemte webprogrammer Azure eller at køre eller foretage fejlfinding af et webprogram, lokalt ved hjælp af Azure beregne emulatoren:

  	|Web programtype|Kør/fejlfinding lokalt ved hjælp af Beregn emulatoren|Køre i Azure|
  	|---|---|---|
  	|ASP.NET-webprogram|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Vælg linket URL-adresse, der vises under fanen **installation** til **Azure aktivitet log** at indlæse siden i browseren.|
  	|ASP.NET MVC 2 webprogram|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Vælg linket URL-adresse, der vises under fanen **installation** til **Azure aktivitet log** at indlæse siden i browseren.|
  	|ASP.NET MVC 3 webprogram|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Vælg linket URL-adresse, der vises under fanen **installation** til **Azure aktivitet log** at indlæse siden i browseren.|
  	|ASP.NET MVC 4 webprogram|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Vælg linket URL-adresse, der vises under fanen **installation** til **Azure aktivitet log** at indlæse siden i browseren.|
  	|ASP.NET Tøm webprogram|Du skal tilføje en .aspx-side i dit program, du har angivet som startside for webprojektet. På menulinjen, klik på **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Hvis du har en standard .aspx-side i dit program, kan du vælge linket URL-adresse, der vises under fanen **installation** til **Azure aktivitet log** og denne side er indlæst i browseren. Hvis du har en anden .aspx-side, skal du gå til denne bestemt side ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|
  	|Silverlight-program|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du har brug at gå til den pågældende side for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|
  	|Silverlight forretningsprogram|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du har brug at gå til den pågældende side for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|
  	|Silverlight Navigation-program|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du har brug at gå til den pågældende side for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|
  	|WCF-tjenesteprogram|Du skal angive .svc-filen som startside for projektet WCF-tjenesten. På menulinjen, klik på **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du har brug at gå til filen svc for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of service file>.svc`|
  	|WCF arbejdsproces-tjenesteprogram|Du skal angive .svc-filen som startside for projektet WCF-tjenesten. På menulinjen, klik på **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du har brug at gå til filen svc for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of service file>.svc`|
  	|ASP.NET dynamisk enheder|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Du skal opdatere forbindelsesstrengen (se næste afsnit). Du kan også har brug at gå til den pågældende side for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|
  	|ASP.NET Dynamic Data Linq til SQL|På menulinjen skal du vælge **fejlfinding**, **Starte fejlfinding** (tastatur: Vælg **på F5** .).|Skal du følge trinnene i denne fremgangsmåde: bruge en SQL Azure-database til dit program (se tidligere afsnit i dette emne). Du kan også har brug at gå til den pågældende side for dit program ved hjælp af følgende format til din URL-adresse:`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Opdatere en forbindelsesstreng for ASP.NET dynamisk objekter

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Opdatere en forbindelsesstreng for ASP.NET dynamisk objekter

1. For at oprette en SQL Azure-database, der kan bruges til et dynamisk ASP.NET-objekter webprogram, skal du følge trinnene i fremgangsmåden, **bruge en SQL Azure-database for dit program** tidligere i dette emne.

1. Tilføj de tabeller og felter, som du har brug for denne database fra [Azure klassisk portal](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Forbindelsesstrengen for denne type program har følgende format i filen web.config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Opdatere værdien *connectionString* med ADO.NET forbindelsesstrengen for din SQL Azure-database på følgende måde:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. For at gemme filen web.config med de ændringer, du har foretaget i forbindelsesstrengen på menulinjen, skal du vælge **filen**, **gemme web.config**.

## <a name="supported-project-templates"></a>Understøttede Project-skabeloner

Hvis du vil publicere et webprogram til Azure, skal programmet bruge en af project-skabeloner til C# eller Visual Basic, der er angivet i tabellen nedenfor.

|Projektgruppe skabelon|Project-skabelon|
|---|---|
|Web|ASP.NET-webprogram|
|Web|ASP.NET MVC 2 webprogram|
|Web|ASP.NET MVC 3 webprogram|
|Web|ASP.NET MVC4 webprogram|
|Web|ASP.NET Tøm webprogram|
|Web|ASP.NET MVC 2 tom webprogram|
|Web|ASP.NET Dynamic Data objekter-webprogrammet|
|Web|ASP.NET Dynamic Data Linq til SQL-webprogram|
|Silverlight|Silverlight-program|
|Silverlight|Silverlight forretningsprogram|
|Silverlight|Silverlight Navigation-program|
|WCF|WCF-tjenesteprogram|
|WCF|WCF arbejdsproces-tjenesteprogram|
|Arbejdsproces|WCF arbejdsproces-tjenesteprogram|

## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om publicering, [Forbered Udgiv eller Implementer et Azure-program fra Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Se også [Indstilling op med navnet legitimationsoplysninger](vs-azure-tools-setting-up-named-authentication-credentials.md).
