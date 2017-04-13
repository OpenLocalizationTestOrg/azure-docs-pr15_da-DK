<properties
    pageTitle="Integrere en skybaseret tjeneste med Azure CDN | Microsoft Azure"
    description="Et selvstudium, som lærer du at installere en skybaseret tjeneste, der fungerer indhold fra et integreret Azure CDN slutpunkt"
    services="cdn, cloud-services"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor="tysonn"/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="intro"></a>Integrere en skybaseret tjeneste med Azure CDN

En skybaseret tjeneste kan integreres med Azure CDN, fungerer alt indhold fra tjenesten skyen placering. Denne fremgangsmåde giver dig følgende fordele:

- Nemt at installere og opdatere billeder, scripts og typografiark i din skytjeneste project mapper
- Opgrader nemt NuGet pakkerne i din skybaseret tjeneste, såsom jQuery eller Bootstrap versioner
- Administrere dit webprogram og din CDN served indhold alle fra den samme Visual Studio-grænseflade
- Samlet installation arbejdsproces for dit webprogram og dit CDN served indhold
- Integrere ASP.NET samler og minification med Azure CDN

## <a name="what-you-will-learn"></a>Hvad du lærer ##

I dette selvstudium lærer du, hvordan du:

-   [Integrere et Azure CDN slutpunkt med din skytjeneste og levere statisk indhold på dine websider fra Azure CDN](#deploy)
-   [Konfigurere cacheindstillinger til statisk indhold i din skytjeneste](#caching)
-   [Levere indhold fra controller handlinger via Azure CDN](#controller)
-   [Servering samlet og minified indhold via Azure CDN og samtidig bevare den script-fejlfinding oplevelse i Visual Studio](#bundling)
-   [Konfigurere reserve scripts og CSS, når din Azure CDN er offline](#fallback)

## <a name="what-you-will-build"></a>Hvad vil du opbygge ##

Du kan implementere en skybaseret tjeneste Web rolle ved hjælp af standard ASP.NET MVC skabelon, Tilføj kode for at levere indhold fra en integreret Azure CDN som et billede, controller handling resultater og standard JavaScript- og CSS-filerne, og også skrive programkode for at konfigurere fallback mulighed for pakker served, hvis CDN er offline.

## <a name="what-you-will-need"></a>Du skal bruge: ##

Dette selvstudium har følgende forudsætninger:

-   En aktiv [Microsoft Azure-konto](/account/)
-   Visual Studio-2015 med [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [AZURE.NOTE] Du skal bruge en Azure-konto til at udføre dette selvstudium:
> + Du kan [åbne en Azure-konto gratis](/pricing/free-trial/) – du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant op du holder kontoen, og brug ledig Azure tjenester, som websteder.
> + Du kan [aktivere MSDN abonnement fordele](/pricing/member-offers/msdn-benefits-details/) – din MSDN-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.

<a name="deploy"></a>
## <a name="deploy-a-cloud-service"></a>Installere en skybaseret tjeneste ##

I dette afsnit, skal du installere standard ASP.NET MVC programmet skabelon i Visual Studio 2015 til en skybaseret tjeneste Web rolle og integrere den med et nyt CDN slutpunkt. Følg vejledningen nedenfor:

1. I Visual Studio-2015, oprette en ny Azure skybaseret tjeneste fra menulinjen ved at gå til **fil > Ny > projekt > skyen > Azure skybaseret tjeneste**. Give den et navn, og klik på **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Vælg **ASP.NET Web rolle** , og klik på den **>** knappen. Klik på OK.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Vælg **MVC** , og klik på **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Nu, publicere denne Web rolle til en Azure skybaseret tjeneste. Højreklik på projektets skybaseret tjeneste, og vælg **Publicer**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Hvis du endnu ikke har logget på Microsoft Azure, klik på **Tilføj en konto …** rullelisten, og klik på menupunktet **Tilføj en konto** .

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. På siden Log på skal du logge på med den Microsoft-konto, du brugte til at aktivere kontoen Azure.
7. Når du er logget på, skal du klikke på **Næste**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. At antage, at du ikke har oprettet en skybaseret tjeneste eller lagerplads konto, hjælper Visual Studio dig med at oprette begge. Skrive navnet på det ønskede tjenesten, og vælg det ønskede område i dialogboksen **Opret skybaseret tjeneste og konto** . Klik derefter på **Opret**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Verificer konfigurationen Publicer indstillinger på siden, og klik på **Publicer**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

    >[AZURE.NOTE] En udgivelsesproces til skytjenester tager lang tid. Den aktivere Web Installer for alle roller indstilling kan foretage fejlfinding din skybaseret tjeneste, der er meget hurtigere ved at give hurtig (men midlertidige) opdateringer til dine Web roller. Se [publicere en skybaseret tjeneste, ved hjælp af værktøjerne Azure](http://msdn.microsoft.com/library/ff683672.aspx)kan finde flere oplysninger om denne indstilling.

    Når **Microsoft Azure aktivitetslog** viser, at status for udgivelse er **fuldført**, skal oprette du et CDN slutpunkt, der er integreret med denne skybaseret tjeneste.

    >[AZURE.WARNING] Hvis efter udgivelse, viser udløst skytjenesten en fejl skærmbillede, er det sandsynligvis, fordi du har installeret skytjenesten bruger [Gæst OS, der ikke omfatter .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Du kan løse dette problem ved at [installere .NET 4.5.2 som en Start-opgave](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="create-a-new-cdn-profile"></a>Oprette en ny CDN profil

En CDN profil er en samling af CDN slutpunkter.  Hver profil indeholder en eller flere CDN slutpunkter.  Du vil bruge flere profiler til at organisere CDN slutpunkterne af internet-domæne, webprogram eller nogle andre kriterier.

> [AZURE.TIP] Hvis du allerede har en CDN profil, du vil bruge til dette selvstudium, gå til [Opret et nyt CDN slutpunkt](#create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Oprette et nyt CDN slutpunkt

**Oprette et nyt CDN slutpunkt for kontoen lagerplads**

1. Gå til din CDN profil i [Azure Management Portal](https://portal.azure.com).  Du kan have fastgjort den til dashboardet i det forrige trin.  Hvis du ikke, kan du finde det ved at klikke på **Gennemse**og derefter **CDN profiler**og klikke på den profil, du vil tilføje dine slutpunkt til.

    Bladet CDN profil vises.

    ![LEVERANDØR(er) profil][cdn-profile-settings]

2. Klik på knappen **Tilføj slutpunkt** .

    ![Slutpunkt knappen Tilføj][cdn-new-endpoint-button]

    **Tilføj et slutpunkt** blade vises.

    ![Tilføje slutpunkt blade][cdn-add-endpoint]

3. Angiv et **navn** for dette CDN slutpunkt.  Dette navn bruges til at få adgang til dine cachelagrede ressourcer på domænet `<EndpointName>.azureedge.net`.

4. Vælg *skybaseret tjeneste*i rullemenuen **Origin type** .  

5. Vælg din skybaseret tjeneste i rullemenuen **Origin hostname** .

6. Lad standardindstillingerne for **Origin sti**, **Origin host sidehoved**og **protokol/Origin port**.  Du skal angive mindst én protocol (HTTP eller HTTPS).

7. Klik på knappen **Tilføj** for at oprette nyt slutpunkt.

8. Når slutpunktet er oprettet, vises det på en liste med slutpunkter til profilen. Listevisningen viser URL-adressen skal bruges til at få adgang til cachelagret indhold samt origin domænet.

    ![LEVERANDØR(er) slutpunkt][cdn-endpoint-success]

    > [AZURE.NOTE] Slutpunktet bliver med det samme ikke tilgængelig til brug.  Det kan tage op til 90 minutter registrering at overføre via CDN netværket. Brugere, der forsøger at bruge domænenavnet CDN umiddelbart kan modtage statuskode 404, indtil indholdet er tilgængelig via CDN.

## <a name="test-the-cdn-endpoint"></a>Teste CDN slutpunktet

Når status for udgivelse er **fuldført**, Åbn et browservindue, og gå til * *http://<cdnName>*.azureedge.net/Content/bootstrap.css**. I min konfiguration er denne URL-adresse:

    http://camservice.azureedge.net/Content/bootstrap.css

Der svarer til følgende origin URL-adressen på CDN slutpunktet:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Når du navigerer til * *http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, afhængigt af din browser, bliver du bedt om at hente eller åbne den bootstrap.css, der stammer fra din publicerede online.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Du kan på samme måde få adgang til en hvilken som helst offentligt tilgængelige URL-adressen på * *http://*&lt;serviceName >*.cloudapp.net/** direkte fra din CDN slutpunkt. Eksempel:

-   En .js fil fra stien/script
-   En hvilken som helst indholdsfil fra mærket/Content sti
-   Controller/handlinger
-   Hvis forespørgselsstrengen er aktiveret på din CDN slutpunkt, en URL-adresse med forespørgselsstrenge

Faktisk med ovenstående konfigurationen, du kan hoste hele skytjenesten fra * *http://*&lt;cdnName >*.azureedge.net/**. Hvis jeg har navigeret til **http://camservice.azureedge.net/ **, jeg få handling resultatet fra hjem/indeks.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Det betyder ikke, men, at det er altid en god ide (eller generelt en god ide) at kunne levere en hel skybaseret tjeneste via Azure CDN. Nogle af advarsler er:

-   Denne fremgangsmåde kræver, at hele webstedet skal være offentligt tilgængelig, fordi Azure CDN ikke kan fungere privat indhold på nuværende tidspunkt.
-   Hvis CDN slutpunktet går offline for en eller anden grund, om planlagt vedligeholdelse eller brugerfejl, din hele skytjeneste går offline medmindre kunderne kan blive omdirigeret til origin URL-adressen * *http://*&lt;serviceName >*.cloudapp.net/**.
-   Selv med de brugerdefinerede Cache-Control-indstillinger (se [konfigurere indstillinger for cachelagring for statiske filer i skyen-tjeneste](#caching)), et CDN slutpunkt ikke forbedre ydeevnen for meget dynamisk indhold. Hvis du har forsøgt at indlæse siden hjem fra din CDN slutpunkt som vist ovenfor, Bemærk, at det tog mindst 5 sekunder for at indlæse standardstartsiden første gang, som er en ganske enkelt side. Forestil dig, hvad der ville ske til klient-oplevelsen, hvis denne side indeholder dynamisk indhold, der skal opdatere hvert minut. Fungerer dynamisk indhold fra et CDN slutpunkt kræver korte cachen udløb, som omsættes til hyppige udgivelsescachen på CDN slutpunkt. Det gør ondt ydeevnen for din skytjeneste og defeats formålet med et CDN.

Alternativet er at finde ud af, hvilket indhold der skal fungere fra Azure CDN på grundlag af tilfælde i skybaseret tjeneste. Hertil, har du allerede set hvordan du kan få adgang til enkelte indhold filer fra CDN slutpunkt. Jeg viser dig, hvordan at kunne levere en bestemt controller handling gennem CDN slutpunktet i [levere indhold fra controller handlinger via Azure CDN](#controller).

<a name="caching"></a>
## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Konfigurere indstillinger for cachelagring for statiske filer i skyen-tjeneste ##

Med Azure CDN integration i skybaseret tjeneste, kan du angive, hvordan du vil statisk indhold cachelagres i CDN slutpunkt. Åbn *Web.config* fra projektet Web rolle (fx WebRole1) for at gøre dette, og Tilføj en `<staticContent>` element til `<system.webServer>`. XML nedenfor konfigurerer cachen for at udløbe om 3 dage.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Når du gør dette, se alle statiske filer i skyen-tjeneste samme reglen i din CDN cache. Tilføje en *Web.config* -fil til en anden mappe og tilføje dine indstillinger der mere detaljeret kontrol over cacheindstillinger. For eksempel føje en *Web.config* -fil til mappen *\Content* og erstatte indholdet med følgende XML:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Denne indstilling bevirker, at alle statiske filer fra mappen *\Content* cachelagres for 15 dage.

Du kan finde flere oplysninger om, hvordan du konfigurerer den `<clientCache>` element, se [Client Cache &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

I [levere indhold fra controller handlinger via Azure CDN](#controller)viser jeg også dig, hvordan du kan konfigurere cacheindstillinger controller handling resultater i CDN cachen.

<a name="controller"></a>
## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Levere indhold fra controller handlinger via Azure CDN ##

Når du integrerer en skybaseret tjeneste Web rolle med Azure CDN, er det relativt nemt at kunne levere indhold fra controller handlinger via Azure CDN. End fungerer din skybaseret tjeneste direkte via Azure CDN (vist ovenfor), viser [Maarten Balliauw](https://twitter.com/maartenballiauw) , hvordan du gør det med en sjov MemeGenerator controller i [reducere ventetid på internettet med Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Jeg vil blot genskabe det her.

Antag i din skybaseret tjeneste, du vil generere memes baseret på en små chucks Norris-billede (foto ved [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)) sådan ud:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Du har en enkel `Index` handling, der gør det muligt for kunder til at angive superlatives i billedet, genererer derefter meme, når de sende indlæg til handlingen. Eftersom den er chucks Norris, forventer du denne side for at blive meget fra tur populære globalt. Dette er et godt eksempel på fungerer halvgennemsigtig dynamisk indhold med Azure CDN.

Følg trinnene ovenfor for at konfigurere denne controller handling:

1. Oprette en ny .cs fil kaldet *MemeGeneratorController.cs* i mappen *\Controllers* , og Erstat indholdet med følgende kode. Sørg for at erstatte den fremhævede del med navnet på din CDN.  

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

                public ActionResult Index()
                {
                    return View();
                }

                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }

                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }

                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }

                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }

                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }

                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }

                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);

                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }

                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }

2. Højreklik i standard `Index()` handling og vælge **Tilføj visning**.

    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acceptere indstillingerne nedenfor, og klik på **Tilføj**.

    ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Åbn den nye *Views\MemeGenerator\Index.cshtml* og erstatte indholdet med følgende simple HTML til at sende superlatives:

        <h2>Meme Generator</h2>

        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>

5. Publicere skytjenesten igen, og gå til * *http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** i din browser.

Når du sender formularværdier til `/MemeGenerator/Index`, `Index_Post` handling metode returnerer et link til den `Show` handling metode med respektive input-id. Når du klikker på linket, kan du når følgende kode:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Hvis din lokale fejlfindingen er tilsluttet, så får du den almindelige fejlfinding oplevelse med en lokal omdirigering. Hvis den kører i skytjenesten, omdirigerer den til:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Der svarer til følgende origin URL-adressen på dit CDN slutpunkt:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Du kan bruge den `OutputCacheAttribute` attributten i den `Generate` metode til at angive, hvordan resultatet handling skal gemmes i cachen, som vil overholde Azure CDN. Nedenstående kode angive et cachen udløb af 1 time (3.600 sekunder).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

På samme måde, du kan tjene indhold fra noget controller i skybaseret tjeneste via din Azure CDN med den ønskede indstilling for cachelagring.

I næste afsnit viser jeg dig, hvordan at kunne levere pakket og minified scripts og CSS via Azure CDN.

<a name="bundling"></a>
## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrere ASP.NET samler og minification med Azure CDN ##

Scripts og CSS typografiark ændres sjældent og er primære kandidater til Azure CDN cachen. Den nemmeste måde at integrere samler og minification med Azure CDN er fungerer rollen hele internettet via din Azure CDN. Men, som du ikke vil muligvis gøre dette, skal jeg vises, hvordan du kan gøre det, mens bevare den ønskede develper oplevelse af ASP.NET samler og minification, f.eks.:

-   Gode fejlfinding tilstand oplevelse
-   Strømlinet installation
-   Øjeblikkelig opdateringer til klienter til script/CSS version opgraderinger
-   Fallback ordning, når din CDN slutpunkt mislykkes
-   Minimere kode ændring

Åbn *App_Start\BundleConfig.cs* i det **WebRole1** projekt, du oprettede i [integrere et Azure CDN slutpunkt med Azure websteds- og servering statisk indholdet på dine websider fra Azure CDN](#deploy), og se nærmere på de `bundles.Add()` metode opkald.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

Først `bundles.Add()` sætning føjer et script samlet på den virtuelle mappe `~/bundles/jquery`. Åbn derefter *Views\Shared\_Layout.cshtml* at se, hvordan mærket script samlet gengives. Du skal ikke kunne finde følgende linje i Razor kode:

    @Scripts.Render("~/bundles/jquery")

Når denne Razor-kode køres i rollen Azure Web, det gengiver en `<script>` mærke til det script samlet stil med følgende:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Men, når den kører i Visual Studio ved at skrive `F5`, det gengiver hver scriptfil i samlet individuelt (i ovenstående tilfælde kun én script-fil er i samlet):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Dette gør det muligt at foretage fejlfinding JavaScript-kode i dit udviklingsmiljø, mens reducere samtidige klientforbindelser (samler) og forbedre fil Hent ydeevne (minification) i fremstilling. Det er en god funktion til at bevare med Azure CDN integration. Desuden da gengivne samlet indeholder allerede en automatisk oprettede versionsstreng, du vil gentage denne funktionalitet så hver gang du opdaterer din jQuery version gennem NuGet, det kan blive opdateret på klientsiden så tidligt som muligt.

Følg trinnene nedenfor for integration ASP.NET samler og minification med din CDN slutpunkt.

1. Tilbage i *App_Start\BundleConfig.cs*, ændre den `bundles.Add()` metoder til at bruge en anden [Samlet parametre](http://msdn.microsoft.com/library/jj646464.aspx), en, der angiver et CDN adresse. Du kan erstatte den `RegisterBundles` metode definition med følgende kode:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Sørg for at erstatte `<yourCDNName>` med navnet på din Azure CDN.

    Dine egne ord, du skal angive `bundles.UseCdn = true` og føjet en nøje udformet CDN URL-adresse til hver samlet. For eksempel den første parametre i koden:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    er den samme som:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

    Denne konstruktør fortæller ASP.NET samler og minification til at gengive individuelle scriptfiler når foretages fejlfinding lokalt, men bruge den angivne CDN adresse at få adgang til det pågældende script. Bemærk imidlertid to vigtige egenskaber med denne nøje udformet CDN URL-adresse:

    -   Kilde til denne CDN URL-adresse er `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, som er den virtuelle mappe af script samlet i skybaseret tjeneste.
    -   Da du bruger CDN parametre, indeholder mærket CDN script til samlet ikke længere automatisk oprettede versionsstrengen i gengivne URL-adressen. Du skal manuelt oprette en entydig versionsstreng, hver gang script samlet ændres for at tvinge en fundne forekomster på din Azure CDN. På samme tid, skal denne entydige versionsstreng forbliver konstante gennem aktivets levetid installation til at maksimere i cachen på din Azure CDN, når samlet er distribueret.
    -   Forespørgselsstreng v = < W.X.Y.Z > trækker fra *Properties\AssemblyInfo.cs* i projektet Web rolle. Du kan have en arbejdsproces for installation, der indeholder stigende samlingsversion, hver gang du publicerer til Azure. Eller du kan kun redigere *Properties\AssemblyInfo.cs* i dit projekt skal forøges versionsstrengen automatisk, hver gang du opretter, ved hjælp af jokertegnet ' *'. Eksempel:

            [assembly: AssemblyVersion("1.0.0.*")]

        Andre strategi til at strømline Genererer en entydig streng i en installation levetid fungerer her.

3. Genpublicere skybaseret tjeneste, og få adgang til startsiden.

4. Få vist HTML-koden for siden. Du skal kunne se CDN URL-adressen, der gengives med en entydig version hver gang du genpublicere ændringer til din skybaseret tjeneste. Eksempel:  

        ...

        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>

        ...

        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>

        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>

        ...

5. I Visual Studio, fejlfinding af skytjenesten i Visual Studio ved at skrive `F5`.,

6. Få vist HTML-koden for siden. Du ser stadig hver scriptfil gengives enkeltvis, så du kan have en ensartet fejlfinding i Visual Studio-oplevelse.  

        ...

            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>

            <script src="/Scripts/modernizr-2.6.2.js"></script>

        ...

            <script src="/Scripts/jquery-1.10.2.js"></script>

            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>

        ...   

<a name="fallback"></a>
## <a name="fallback-mechanism-for-cdn-urls"></a>Fallback mulighed for CDN URL-adresser ##

Når din Azure CDN slutpunkt af en eller anden grund ikke lykkes, vil du websiden skal være i stand til at få adgang til webserveren origin som indstillingen fallback for indlæsning af JavaScript eller Bootstrap. Det er alvorlige nok at miste billeder på webstedet på grund af CDN utilgængelighed, men meget mere dårlige at miste afgørende page-funktioner, der leveres af scripts og typografiark.

Klassen [Samlet](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) indeholder en egenskab, der kaldes [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) , der gør det muligt at konfigurere fallback mulighed for CDN fejl. Hvis du vil bruge denne egenskab, skal du følge nedenstående trin:

1. Åbne *App_Start\BundleConfig.cs*, hvor du har tilføjet en CDN URL-adresse i hver [Samlet parametre](http://msdn.microsoft.com/library/jj646464.aspx), i projektet Web rolle og foretage de følgende fremhævede ændringer for at føje fallback ordning til standard-pakker:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                "~/Scripts/bootstrap.js",
                                "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Når `CdnFallbackExpression` er ikke null-værdi, script er tilføjet i HTML-koden til at teste, om samlet er indlæst og, hvis ikke, få adgang til samlet direkte fra den oprindelige Web-server. Denne egenskab, skal være indstillet til et JavaScript-udtryk, der tester, om de respektive CDN samlet er indlæst korrekt. Det udtryk, der er behov for at teste hver samlet varierer, afhængigt af indholdet. For ovenstående standard pakker:

    -   `window.jquery`er defineret i jquery-{version} .js
    -   `$.validator`er defineret i jquery.validate.js
    -   `window.Modernizr`er defineret i modernizer-{version} .js
    -   `$.fn.modal`er defineret i bootstrap.js

    Du har måske bemærket, at jeg ikke har angivet CdnFallbackExpression for den `~/Cointent/css` samlet. Dette skyldes, at der er i øjeblikket [fejl i System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) fra serveren en `<script>` mærke for fallback CSS i stedet for den forventede `<link>` mærke.

    Der er dog god [Typografi samlet reserve](https://github.com/EmberConsultingGroup/StyleBundleFallback) tilbydes efter [Ember Consulting gruppe](https://github.com/EmberConsultingGroup).

2. Oprette en ny .cs-fil i Web rolle projektets *App_Start* mappe med navnet *StyleBundleExtensions.cs*for at bruge denne løsning for CSS, og Erstat dens indhold med [kode fra GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Omdøb navneområdet *App_Start\StyleFundleExtensions.cs*, til din Web rolle navn (fx **WebRole1**).

3. Gå tilbage til `App_Start\BundleConfig.cs` og redigere sidst `bundles.Add` sætningen med følgende fremhævede kode:  

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));

    Denne nye lokalnummer metode bruger den samme ide skal tilføjes script i HTML til at kontrollere DOM for den en tilsvarende klassenavnet, regelnavn og regel værdi, der er defineret i CSS samlet og falder tilbage til den oprindelige Web-server, hvis det ikke lykkes at finde samme.

4. Udgive skytjenesten igen, og få adgang til startsiden.

5. Få vist HTML-koden for siden. Du skal finde indkoblet scripts som følger:    

        ...

        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>

        ...

            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>

        ...


    Bemærk, at indplantet script til CSS samlet stadig indeholder den fejlbehæftede rest fra den `CdnFallbackExpression` egenskab i linjen:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Men siden den første del af den || udtrykket returnere altid SAND (i linjen direkte over den), funktionen document.write() kører aldrig.

## <a name="more-information"></a>Få mere at vide ##
- [Oversigt over Azure Content Delivery netværk (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Brug af Azure CDN](cdn-create-new-endpoint.md)
- [ASP.NET samler og Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
