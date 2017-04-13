<properties 
    pageTitle="Azure Søg udvikler-Casestudie: Hvordan WhatToPedia bygget en infomedia portal på Microsoft Azure | Microsoft Azure | Hostet skyen search-tjenesten" 
    description="Lær, hvordan du opretter en oplysninger portal og metakoden søgemaskine ved hjælp af Azure søgning, en skyen hostet søgetjeneste for udviklere." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Azure Søg udvikler-Casestudie

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Hvordan [WhatToPedia.com](http://whattopedia.com/) bygget en infomedia portal på Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">Stor ide</font> 


Vores ide er at opbygge en oplysningsportal, der hjælper med at oprette forbindelse til forhandlere via en meget relevante; fastsat Søg oplevelse, svarende til hvordan rejser portaler match turister op med hoteller, restauranter og underholdning når på uncharted område kunder. 

Portalen vi forestille sig afholde et særdeles høj kvalitet søgeoplevelse på forhandler data i et givet marked, indeholder hjælpe kunder finde butikker baseret på placering og andre faciliteter forhandleren. Vi vil podes søgemaskinen med et indledende datasæt, men vil blive bygget dybere værdi over tid ved hjælp af forhandler abonnenter, der anbringe oplysninger om deres virksomhed. Salgsfremstød, nye varer, populære mærker, interne specielle services-– alle er eksempler på data, der tilføjer værdi til vores websted. Disse data selv rapporteres og integreret i indekserede søgning, når forhandleren tilmelder sig som et abonnement. Reklamer plus abonnementsmodellen, du giver indtægter strømmen til vores nye business.

Søgningen bliver primært bruger interaktion modellen, på en ren skyen platform. For anvendelse af skala og lav omkostninger, vil stort set alt, hvad vi gør fra oplevelser med portalen til versionsstyring, være via en onlinetjeneste. Ved hjælp af en søgemaskine, som indeholder funktioner, som vi har brug for af feltet, kan vi oprette et søgeprogram hurtigt, uden at skulle oprette og administrere en søgning motorens os selv.

## <a name="what-we-built"></a>Hvad vi indbygget

WhatToPedia er et opstart infomedia firma. Vi indbygget [WhatToPedia.com](http://whattopedia.com/) – - i øjeblikket i test-markedet i Nord Europa med 2 februar 2015 opstart datoen. Vores kundebase er primært fysisk fysiske butikker der skal have en økonomisk onlinetilstedeværelse, som er nem at administrere og vedligeholde.

Vores opgave er at tiltrække kunder gennem et godt onlinesøgeoplevelsen, øger resultater, der er baseret på by eller andre computere, mærker, gemme navne eller gemme typer. Tiltrække kunder har en indbyrdes påvirkning motivation forhandlere at abonnere på vores portalwebstedet. Abonnementer er gebyrbaserede, med en økonomisk hastighed.

 ![][7] 

Når du tilmelder dig et abonnement, føres en forhandler over deres eksisterende profil (oprettet først med os fra købte data), ved at opdatere den med andre data om salgsfremstød, udvalgt mærker eller meddelelser. Interne funktioner, som de talte sprog, valutaer accepteres, momsfrit køber, kan være selv anmeldt til bedre tiltrække kunder, der er på udkig efter disse faciliteter.

## <a name="who-we-are"></a>Der vi er

Mit navn findes Thomas Segato (Microsoft Consulting), og jeg har arbejdet med Jesper Boelling, føre udvikler på WhatToPedia til at designe løsningen. 

WhatToPedia er en opstart, test marketing dets nye portalen business i Sverige, hvor de fleste af de 60.000 forhandlere er fysisk fysiske små og mellemstore virksomheder (små og mellemstore virksomheder). Da vi ved, at en person ind i Europa taler flere sprog og udfører flere valutaer, kan vi bygge løsninger, der passer til en flersprogede kunde. Vi det er nødvendigt, og fundet en søgemaskine, der understøtter vores flersprogede krav i Azure Søg.

Azure Search kunne en game skifter for vores projekt. Før du tilgængeligheden af Azure søgning anvendes vi betydelige energi arbejde via kinks oprettelse af vores egen søgemaskine. Har Azure søgning, som en onlinetjeneste fjernet den største tekniske og administrative overskride fra vores løsning, som beregnet hente markedsføre hurtigere og med en mere solid søgeoplevelse.  

## <a name="how-we-did-it"></a>Hvordan vi er

Vores vision var at opbygge en komplet infrastruktur baseret på kun skytjenester. Microsoft blev valgt som strategiske platformen, fordi den var, den udbyder, der tilbydes nødvendige tjenester (for både samarbejde og udvikling), skalere på demand og økonomisk priser.
 
### <a name="high-level-components"></a>Overordnet komponenter

Vi har oprettet en virksomhed, ikke kun et websted. Understøtte den hele indsats kræves et bredt udvalg af værktøjer og programmer. Vi vedtaget Visual Studio og Visual Studio Team Services til udvikling, Team Foundation Service (TFS) Online for versionsstyring og scrum management, Office 365 til kommunikation og samarbejde og naturligvis Microsoft Azure for alle websted-relaterede handlinger og lagerplads. Med Visual Studio angivet IDE-enheden direkte klargøring til Azure, med integration til TFS Online give en ekstra produktivitet boost.

Diagrammet nedenfor illustrerer på højt niveau komponenter, der bruges i WhatToPedia infrastruktur.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Sådan bruger vi Microsoft Azure

Se på de grønne bokse i det forrige diagram, får du vist, WhatToPedia løsningen er bygget på disse tjenester:

- [Azure søgning](https://azure.microsoft.com/services/search/)
- [Azure websteder ved hjælp af MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs for planlagte opgaver](../app-service-web/websites-webjobs-resources.md)
- [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/)
- [Azure BLOB-lager](https://azure.microsoft.com/services/storage/)
- [SendGrid Maillevering](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Meget hjerte løsningens er data, og Søg. Strømmen af data fra provideren forhandler til slutbrugeren illustreres herunder:

  ![][9]

Primære datalagring er forhandler og accounting data i Azure SQL-Database. Dette består af indledende datasæt samt forhandler-specifikke data, der er tilføjet over tid. Vi bruger en Azure WebJob for at sende opdateringer fra SQL-Database til søgning indekserede i Azure Søg.

### <a name="presentation-layer"></a>Præsentation lag

Portalen har et websted, Azure skal implementeret i MVC 4 og [Twitter-Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Vi valgte MVC, da den giver en renere meget metode til HTML end ASP.NET formularbaseret udvikling. Hvis du vil undgå at skulle oprette apps til flere enheder og vedligeholde flere mobile platforme, blev Twitter-Bootstrap valgt til at understøtte alle enheder og platforme.

### <a name="authentication-permissions-and-sensitive-data"></a>Godkendelse, tilladelser og følsomme data

Kunder søge på webstedet anonymt. Som sådanne, der er ingen login krav til kunder og Gem vi consumer data. 

Forhandlere er en anden tekstenhed. Her, gemme vi profiloplysninger offentligt, faktureringsoplysninger og medieindhold, de vil vise på webstedet. Hver forhandler der abonnerer til webstedet, får et brugerlogon, der bruges til at godkende brugeren før foretage opdateringer til den abonnement profil.  Vi gemme sikkert alle abonnementsdata i Azure SQL-Database og Azure BLOB-lager.
Vi valgte en godkendelsesmodel, der er baseret på .NET formularbaseret godkendelse. Vi valgte denne metode til dens enkel vi ikke har brug for rollerne, understøttelse af Brugergrænsefladen og andre overflødige funktioner, der følger med andre muligheder. 

For at sikre, at forhandlere kun se de data, der hører til dem, oprettede vi en forhandler-ID for hver forhandler, der bruges på alle efterfølgende læse og skrive handlinger, der involverer forhandler-specifikke data. Med denne metode fandt vi, at vi ikke behøver at give databasetilladelser til individuelle forhandlere. Alle forhandlere interagerer med systemet under en enkelt databaserolle med forhandler-ID'ET, som vores data isolationsniveauet metode.

Fordi vores business drejer sig om kan de efterfølgende effekter (bil flere business til forhandlere, oprette god grund til at annoncere og abonnere), vi tegne stregen på håndtering af køb via internettet. Så kan ikke du finde en indkøbskurv på det websted, hvilket forenkler vores sikkerhedskravene. 

En anden forenkling vi ansat blev at udlicitere vores kuponudbetalinger handlingerne fakturering og konti. Ved at dirigere kunde betalingsoplysninger direkte til en tredjepart ([SveaWebPay](http://www.sveawebpay.se/)), vi reducere risici at knytte til lagring og beskytte følsomme oplysninger i vores data butikker. 

### <a name="search-engine"></a>Søgemaskine

Kernen i vores løsning er søgemaskinen bygget på Azure Search-tjenesten. Først, vi har oprettet en brugerdefineret søgemaskine, men under denne proces, vi realiseres kompleksitet og indsats var meget høj faktisk, og, som du bliver bedt om os skal du tænke på andre alternativer. 

Grundlæggende funktioner, der er vigtigst for os inkluderet:

- Filtre
- Facetteret navigation
- Øger resultater
- Sideopdeling gennem AJAX

En internetsøgning i den følgende video, som inspireret vi Prøv Azure søgning er gjort os: [Undersøgelse på TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Efter se videoen, er vi klar til at opbygge en model, der er baseret på hvad vi fik vist. Da vi allerede har en datamodel i MVC skal blev oprette prototypen enkle, fordi dataene indeholdt søgbare betingelserne, og vi allerede var udarbejdet kravene til hvordan vi gerne vil sortere, facet, og filtrere dataene. 

Dette er, hvordan vi bygget på prototyper.

**Konfigurere Azure søgetjenesten**

1. Logge på Azure klassisk Portal og føjet søgetjenesten til vores abonnement. Vi har brugt den delte version (gratis med vores abonnement).
2. Oprette et indeks. Til model brugte vi portalen brugergrænseflade til at definere søgefelterne og oprette de vurdering profiler. Vores vurdering profil er baseret på placeringsdata: land | by | adresse (se: tilføje vurdering profiler).
3. Kopiere URL-adressen og administrator api-nøgle til vores konfigurationsfiler. Denne tast er på siden Søg i portalen, og den bruges til at godkende til tjenesten.
    
**Udvikle en søgning indekseringsprogram Job – Windows-konsollen**

1. Læs alle forhandlere fra database.
2. Ringe til Azure Søg Service API for at overføre forhandlere én (se: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Angive en egenskab i database, der er indekseret forhandler til stigende indeksering. Vi har ved at tilføje et felt af 'indekseringsprogram', der gemmer indeks status for hver profil (indekseret eller ej). 

Se tillæg til kodestykket, der opbygger indekseringsprogram jobbet.

**Udvikle en søgning webportal – MVC**

1. Ringe til Azure Search-tjenesten for at få alle dokumenter fra søgningen (se: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Udtrække følge fra search-tjenesten svaret (ved hjælp af json.net http://james.newtonking.com/json)
   - Resultater
   - Facetter
   - Resultatet tæller
   - Udvikle en brugergrænseflade til visning af søgeresultater, facetter og optællinger (vi allerede har dette).

Dette er den kode, vi bruges til at få vist resultaterne fra Azure søgningen:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Øger efter placering**

Vigtigste kravet om at bekræfte i model, medtages sandsynligvis at tilføje et nøgleord til søgning placering til forespørgslen. Det er vigtigt at vores portal, hvis en bruger indtaster et bynavn i feltet Søg forespørge, forhandlere i den angivne by vil rangere højere end forhandlere, der er nøgleordet by i beskrivelsen. For dette krav, vi har brugt en vurdering profil skal rangordnes feltet By, der er højere end andre felter.

**Understøttelse af flere sprog**

Vi er nødvendige for at få vist korrekte søgeresultater i korrekte sprog, og giver mulighed for at finde de samme resultater i forskellige sprog. De to sider på dette problem er: 

- Søge efter ord i flere sprog
- Vis søgeresultater i korrekte sprog

Vi løser delen præsentation ved at tilføje et dokument for hvert sprog med oversatte tekst og en egenskab med sproget. Når en bruger indtaster et søgeord, vi bruger `$filter` udtryk til at filtrere på sproget, brugeren har valgt.

Hver af dokumenterne, der har skjulte egenskaben kaldet "byer" bygget på typen af websteder. Denne egenskab gemmer bynavne på alle sprog, så brugeren til at søge på flere sprog.

###<a name="data-storage"></a>Lagring af data

Alle data (profil, abonnement og regnskab) er gemt i SQL-Database. Alle mediefiler er gemt i Azure BLOB-lager, herunder billeder og videoer, der leveres af forhandleren. Brug af separat BLOB-lager isolerer virkningerne af blive overført filer. filer er aldrig samtidig mingled til webstedet, så vi ikke behøver at genopbygge webstedet, når vi tilføje filer.

En vigtig fordel af vores lagerplads design er, at flere udviklere kan dele en enkelt udvikling lagerplads. En af kravene til WhatToPedia projektet var lov til at oprette et udviklingsmiljø inden for 15 minutter, herunder forhandler data, billeder og videoer. Ved at få de nyeste data fra TFS Online, kører en SQL-script, og kører kørslen Importér, kan et komplet miljø være stod på ingen tid overhovedet. Denne øvelse forbedrer også midlertidige processen.

###<a name="webjobs"></a>WebJobs

Vi bruger Azure WebJobs til at opdatere data til indekset. Delen indeksering blev ved at oprette en søgning indekseringsprogram sag, så du let at integrere i vores løsning. Den eneste kodeændring vi gjort blev til indekseringsprogram jobbet var at tilføje en `Indexed` til vores datamodel for at angive indeks-tilstand. Når en ny profil tilføjes eller opdateres, den `Indexed` felt er indstillet til falsk. På samme måde gælder, hvis forhandleren ændres vedkommendes profildata via portalen.  

Jobbet søger efter alle rækker har `Indexed` indstillet til falsk. Når der bliver fundet rækken, dokumentet er overført til Azure Søg, og derefter på `Indexed` felt er indstillet til sand. Vi havde ikke til at planlægge for at tilføje eller opdatere data, fordi Azure søgetjenesten faktisk tager sig af dette. Hvis du tilføjer et dokument, der allerede findes, vil tjenesten gøre en opdatering automatisk.

Alle web-job er udviklet som console-programmer, der kan overføres til Azure websteder som ZIP-filer, pakket, og derefter planlagt.

Jobbet er planlagt til at køre hvert 5 som en planlagt webopgave. Vi beregnes, tjenesten tager omkring tre minutter til at overføre 3.000 dokumenter, som blev i vores krav. 

> [AZURE.NOTE] Der er en prototyper indekseringsprogram funktion, der for nylig blev introduceret i Azure Søg. Denne funktion fulgte for sent for os til at bruge det i vores first release, men det ser ud til at løse det samme problem brugte vi vores indekseringsprogram job til, hvilket er automatisere Indlæs datahandlinger.


###<a name="backup-strategy"></a>Strategi for sikkerhedskopiering

Vi designet en flere niveauer strategi for sikkerhedskopiering til at gendanne fra en række scenarier fra katastrofal fejl ned til gendannelse af en bestemt transaktion. Aktiver at beskytte indeholder tre typer data (-websted, abonnementsdata og mediefiler). 

Først skal ved at holde kildekode websted i TFS Online, ved vi, at hvis webstedet går ned, kan vi genopbygge den ved at genudgive fra TFS. 

Abonnementsdata i Azure SQL-Database er den mest følsomme aktiv. Vi tilbage dette op ved hjælp af indbyggede funktioner (se [Azure SQL databasesikkerhedskopiering og gendannelse](http://msdn.microsoft.com/library/azure/jj650016.aspx)). Tidsplanen for sikkerhedskopiering er fuld sikkerhedskopi en gang om ugen, ændret databasesikkerhedskopier en gang om dagen og transaktionslogfiler hver 5 minutter.  Givet størrelsen på dataene, er denne løsning mere end nok til vores øjeblikkelig og forventede datamængder.

Vi gemme tredje, billede og videofiler i Azure BLOB-lager. Vi stadig evaluerer ultimate sikkerhedskopiering planen for disse data hensyn Cloudberry Explorer til Azure som en mulig løsning. Nu skal bruge vi en WebJob til at kopiere billeder og videoer til en anden placering.

##<a name="what-we-learned"></a>Vi har lært

Da vi allerede har data, var det nemt at oprette bevis af konceptet. Vi havde en prototyper med facetter inden for timer og tællere, sideopdeling, klassificeres profiler, og søgeresultater. Søgeresultaterne er så præcis, vi besluttede dig for at fjerne nogle af de filtre, der præsenteres til slutbrugeren. 

Den største overraskelse for os blev, hvor hurtigt vi kunne se Azure Søg, og hvor meget vi er kommet tilbage. Direkte, vi etableret bevis af konceptet i et par timer (se nedenstående bemærkning), erstatter 500 kodelinjer med 3 kodelinjer i front end-program (plus en ny WebJob), og at få bedre resultater. 

Vores kode implementeret tidligere, sideopdeling, tæller og andre funktioner, der er standard til at søge. Resultaterne vi kommer tilbage omfatter med Azure søgning, Søg søgeresultater, facetter, sideopdeling data, tæller – de ting, som vi behov og med til at levere os selv. Der desuden øger og indbyggede facetteret navigation, som vi ikke havde i vores oprindelige løsning.

Den største udfordring under implementering blev, det blev en prøveversion og finde oplysninger og delte oplevelser var svær. Når vi har oprettet et par prikker, fandt vi, ved hjælp af Azure Search-tjenesten blev ganske enkelt på grund af dens REST-API og JSON dataformat. Vi kan ringe til en ramme direkte fra mest Åbn kilde-plug-ins som JQuery JSON.Net, og vi kan bruge værktøjer som Fiddler til hurtig forsøg og fejlfinding. 

> [AZURE.NOTE] Ud over at opbevare de data, prepped, hjulpet, forstået på os opbygning af prototyper allerede Sådan teknologi fungerer, gør os mere produktiv og mere appreciative af de indbyggede funktioner en søgning. Hvis du vil sætte produktiviteten i vejret op på søgning forespørgsel byggeri, facetteret navigation, filtre, osv. Du kan forvente prototyper til at tage længere tid. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Styre facetter på siden Søg præsentation

En af vores learnings under bevis af konceptet-var til at planlægge facetter omhyggeligt forhånd. Når du har indlæst en stor mængde data i løsningen, så vi, at alene lydstyrken for facetter var for høj til at præsentere for brugerne. 

Vi løser parameteren grene antal en udskrevet. Parameteren antal medfører en grænse på antallet facetter returneres til brugeren. Du kan finde et link, der indeholder en beskrivelse af parameteren antal [her](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs til planlægning af opgaver

Azure søgning blev ikke kun behageligt overraskelse til os. Vi har opdaget, bruger WebJobs til at automatisere vores data indlæsning, der skal søges i Azure blev hvilket overordnede vores forrige fremgangsmåde, som ved hjælp af en dedikeret VM, der kører Windows Opgavestyring med planlagte opgaver for at opdatere søgeindekset. WebJobs er nemmere at konfigurere og nemmere at fejlfinding og naturligvis meget billigere end skulle betale for en dedikeret VM.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Azure BLOB Storage Explorer for at opdatere billeder

Vi fandt, med [Azure BLOB Storage Explorer](https://azurestorageexplorer.codeplex.com/) (tilgængelig på codeplex) skal være meget nyttigt i administration af billede og video opdateringer til webstedet. Vi bruge den som en udviklingsværktøjet manuelt opdatere billeder og videoer, der er en del af vores hovedwebsted. Vi har fundet det mere fleksibel end implementering af ændringer til portalen og fjerner en komplet test gentagelse, når vi har brug at opdatere et billede. 

##<a name="a-few-final-words"></a>Et par sidste ord

Tak for at gode andre på WhatToPedia for tillader os til at dele deres historie!  

Vi håber, du har fundet denne Casestudie nyttige. Hvis du går videre til brug af søgning Azure, anbefales jeg nogle ressourcer, der kan hjælpe dig med:

- [MSDN-forum dedikeret til Azure søgning](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow har også et mærke](http://stackoverflow.com/questions/tagged/azure-search)
- [Dokumentation side på Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure Søg dokumentation på MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Tillæg: Søg indekseringsprogram WebJob

Følgende kode opbygger den indekseringsprogram, der er nævnt i afsnittet på at opbygge prototypen.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
