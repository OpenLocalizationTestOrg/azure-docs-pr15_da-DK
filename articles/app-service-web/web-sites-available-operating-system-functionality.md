<properties 
    pageTitle="Operativsystem funktionalitet på Azure App Service" 
    description="Få mere at vide om OS tilgængelige funktioner for webapps, en back-end-mobilappen og API-apps på Azure App Service" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Operativsystem funktionalitet på Azure App Service #

I denne artikel beskrives almindelige oprindelige operativsystem funktionalitet, der er tilgængelige for alle apps, der kører på [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Disse funktioner omfatter fil, netværk, og registreringsdatabasen adgang, og diagnosticering logfiler og begivenheder. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>App Service plan niveauer

App Service kører kunde apps i et med flere lejer værtsmiljø. Apps, der er implementeret i **gratis** og **delt** niveauer køre i arbejdsprocesser på delte virtuelle maskiner, mens apps, der er implementeret i **Standard** og **Premium** niveauer, der kører på virtual machine(s) dedikeret specifikt til de apps, der er knyttet til en enkelt kunde.

Da App Service understøtter en problemfri skalering oplevelse mellem forskellige niveauer, forbliver den samme sikkerhedskonfigurationen gennemtvinges i forbindelse med App Service apps. Dette sikrer, at apps ikke pludselig fungerer anderledes, ned på uventede måder, når App-serviceaftale skifter fra ét niveau til en anden.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Udvikling strukturer

App Service priser niveauer styre mængden af Beregn ressourcer (CPU, lagerplads på disken, hukommelse og netværk udgangspunkt) tilgængelige til apps. Bredden af framework-funktionalitet, der er tilgængelige for apps forbliver dog den samme uanset skalering niveauer.

App Service understøtter en lang række udvikling strukturer, herunder ASP.NET, klassisk ASP, node.js, PHP og Python - som alle køre som filtypenavne i IIS. For at forenkle og normalisere konfiguration af sikkerhed skal køre App Service apps typisk de forskellige udvikling strukturer med deres standardindstillinger. En metode til at konfigurere apps kunne har været tilpasning af API areal og funktionalitet for hver enkelt development framework. App tjenesten har brugt i stedet for en mere generisk fremgangsmåde ved at aktivere en fælles grundlinje af operativsystem funktionalitet uanset en app development framework.

I følgende afsnit beskrives de almindelige typer af operativsystemfunktioner er tilgængelige til App Service apps.

<a id="FileAccess"></a>
##<a name="file-access"></a>Adgang til filer

Der findes forskellige drev i App-tjeneste, herunder lokale drev og netværksdrev.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Lokale drev

Grundlæggende er App Service en tjeneste, der kører oven på infrastrukturen Azure PaaS (platform som en tjeneste). Som et resultat, er de lokale drev, der er "knyttet" til en virtuel maskine den samme drev-type, der er tilgængelige for alle arbejder rolle, der kører i Azure. Dette omfatter et operativsystemdrev (D:\-drevet), et program drev, der indeholder Azure cspkg pakkefiler bruges udelukkende af App Service (og utilgængelige for kunder) og et "brugere"-drev (C:\ drevet), hvis størrelse varierer afhængigt af størrelsen af VM.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Netværk drev (også kendt UNC deler)

En af de forskellige aspekter af App-tjeneste, som gør appinstallation og vedligeholdelse enkle er, at alt brugerindhold, der er gemt på et sæt af UNC-aktier. Denne model knytter meget fint til almindelige mønstret indhold lagerplads, der bruges af lokale web vært miljøer, der har flere belastningsjusterede servere. 

I App-tjeneste, der er antallet af UNC-aktier, der er oprettet i hver datacenter. En procentdel af bruger indholdet til alle kunder i hver datacenter er allokeret til hver UNC-share. Desuden dele hele filen indhold til en enkelt kundens abonnement placeres altid på samme UNC. 

Bemærk, at på grund af hvordan skyen tjenester fungerer, den bestemte virtuelle maskine ansvarlig for vært for en UNC-share ændres over tid. Det sikres, UNC-aktier være fastgøres med forskellige virtuelle maskiner, som de ankommer op og ned i normal løbet af skyen handlinger. Derfor skal apps aldrig sikre faste antagelser, maskine oplysningerne i en UNC-filsti forbliver stabil over tid. I stedet, de skal bruge den praktisk *faux* absolut sti **D:\home\site** , som indeholder et App Service. Denne faux absolut sti indeholder en bærbar, app-og-bruger-agnostic metode til at referere til egne app. Ved hjælp af **D:\home\site**, kan en overføre delte filer fra app til app uden at konfigurere en ny absolutte sti til hver overførsel.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Typer af adgang til filer, der tildeles en app

Hver kunde abonnement har en reserveret mappestrukturen på et bestemt UNC-share i et datacenter. En kunde muligvis flere apps, der er oprettet i et bestemt datacenter, så alle de mapper, der hører til et enkelt kunde-abonnement er oprettet på samme UNC dele. Del kan omfatte kataloger som dem til indhold, fejl og logge til diagnosticering og tidligere versioner af den app, der er oprettet af versionsstyring. Som forventet, er en kundes app mapper tilgængelige for læse / skrive-adgang på kørselstidspunktet via en den app programkode.

Forbeholder sig en del af plads på harddisken C:\ til app-specifikke midlertidig lokale opbevaring på de lokale drev, der er knyttet til den virtuelle maskine, der kører en app, App Service. Selvom en app har fuld læse-/ skriveadgang til sin egen midlertidigt lokale lager, skal pågældende lagerplads virkelig ikke anvendes direkte af programkoden. I stedet er formålet at tilvejebringe midlertidige fillagring til IIS og web application strukturer. App Service begrænser også mængden midlertidige lokale lager til hver app for at forhindre, at individuelle apps forbrug for store mængder lokale fillager.

To eksempler på, hvordan App Service bruger lokale midlertidige er mappen til midlertidige ASP.NET-filer, og mappen for IIS komprimerede filer. ASP.NET kompilering systemet bruger mappen "Midlertidige ASP.NET filer" som en midlertidig kompilering cacheplacering. IIS anvender den "IIS midlertidige komprimerede filer" mappe til lagring komprimeret svar output. Begge af disse typer filer brugen (samt andre) er tilknyttet i App-tjeneste til-app midlertidigt lokale lager. Denne ændring af tilknytningerne sikrer, at funktionalitet fortsætter som forventet.

Hver app i App-tjeneste kører som en tilfældigt entydige få rettigheder arbejder proces-id'et, der kaldes "programpuljeidentiteten", er en yderligere beskrevet her: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Programkode bruger denne identitet for grundlæggende skrivebeskyttet adgang til operativsystemdrevet (D:\-drevet). Det betyder programkode kan liste over almindelige directory-strukturer og læse almindelige filer på operativsystemdrev. Selvom vises dette kan være noget overordnet for access, de samme mapper og filer er tilgængelige, når du klargør rollen arbejder i en Azure hostet tjenesten og læse drev indholdet. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Adgang til filer på tværs af flere forekomster

Som privat mappen indeholder en app-indhold, og kan skrive programkode til den. Hvis en app kører på flere forekomster, der deles mappen privat af alle forekomster, så alle forekomster ser den samme mappe. Så, f.eks, hvis en app gemmer overførte filer til mappen privat, filerne, som er umiddelbart tilgængelige for alle forekomster. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Netværksadgang
Programkode kan bruge TCP/IP og UDP-baseret protokoller til at foretage udgående netværksforbindelser til Internet handicapvenlige slutpunkter, der viser eksterne tjenester. Apps kan bruge disse samme protokoller til at oprette forbindelse til services Azure & #151, f.eks, ved at oprette HTTPS-forbindelser til SQL-Database.

Der er også en begrænset egenskab for apps til at oprette en lokal tilbagekobling forbindelse, og have en app lytter på den lokale tilbagekobling socket. Denne funktion findes primært for at aktivere apps, der lytter på lokale tilbagekobling sockets som en del af deres funktionalitet. Bemærk, at hver app kan se en "Privat" tilbagekobling forbindelse; App "A" kan ikke lytte til en lokal tilbagekobling socket etableret ved app "B".

Navngivne pipes understøttes også som en metode til kommunikation mellem processer (IPC) mellem forskellige processer, der kører, en app. For eksempel er modulet IIS FastCGI baseret på navngivne pipes til at koordinere individuelle processer, der kører PHP sider.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>Programkode, processer og hukommelse
Som nævnt tidligere køre apps inde i få rettigheder arbejdsprocesser ved hjælp af en tilfældigt programpuljeidentiteten. Programkode har adgang til hukommelsen, der er knyttet til arbejdsprocessen, samt eventuelle underprocesser, der kan være forgrenet ved CGI processer eller andre programmer. Dog én app kan ikke få adgang til hukommelse eller data fra en anden app selvom det er på den samme virtuelle maskine.

Apps kan køre scripts eller sider, der skrives med understøttede web development strukturer. App Service konfigurere ikke indstillinger for en hvilken som helst web framework til mere begrænset funktionsmåder. For eksempel kører ASP.NET-apps, der kører på App-tjenesten i "fuld" tillid i modsætning til en mere begrænset sikkerhed og rettighedsadministration-tilstand. Web strukturer, herunder både klassisk ASP og ASP.NET, kan ringe til igangværende COM-komponenter (men ikke ud af processen COM-komponenter) som ADO (ActiveX Data Objects), der er registreret som standard på Windows-operativsystemet.

Apps kan opsplitte og køre kode. Det er tilladt for en app til at gøre ting som spawn kommandoshell eller afspille en PowerShell-script. Selvom kode og processer kan forgrenet fra en app, vil de eksekverbare programmer og scripts er stadig begrænset til de tilladelser, der er tildelt til den overordnede-programgruppen. For eksempel kan en app opsplitte en eksekverbar fil, der giver en udgående HTTP-opkald, men som samme eksekverbare ikke kan forsøge at Adskil IP-adressen på et virtuelt fra dens NIC. Foretage et videoopkald udgående netværk har tilladelse til at få rettigheder kode, men forsøger at omkonfigurere netværksindstillinger på en virtuel maskine kræver administratorrettigheder.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Logge til diagnosticering og begivenheder
Logoplysninger er en anden række data, som nogle apps forsøger at få adgang til. Typerne oplysninger kan kode, der kører i App-tjeneste indeholder diagnosticering og logoplysninger genereres af en app, der er også nem adgang til appen. 

For eksempel er W3C HTTP logfiler, der genereres af en aktiv app tilgængelige, enten på en log mappe på netværksplacering del oprettet til app'en, eller tilgængelig i blob-lager, hvis en kunde har konfigureret W3C logføring til lager. Denne indstilling aktiverer store mængder logfiler, der skal indsamles uden risikoen for at overskride lagergrænser fil, der er knyttet til et delt netværksdrev.

I en lignende vein realtid diagnosticeringsoplysninger fra .NET apps kan også være logget ved hjælp af .NET sporing og diagnosticering infrastruktur med mulighed for at skrive sporingsoplysningerne til enten den app netværksshare, eller du kan også til en blob storage placering.

Områder af diagnosticering logføring og sporing, som ikke er tilgængelig til apps er Windows ETW begivenheder og almindelige Windows-hændelseslogge (fx System, program og sikkerhed hændelseslogfiler). Da ETW sporingsoplysninger kan potentielt være synlig hele computeren (med højre ACLs), blokeres læse / skrive-adgang til ETW begivenheder. Udviklere oplever måske, at API ringer at læse og skrive ETW begivenheder og almindelige Windows-hændelseslogge ud til at virke, men det er fordi App Service "faking" på opkald, så de vises lykkes. I virkeligheden har programkoden ikke adgang til denne begivenhed data.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Adgang til registreringsdatabasen
Apps har skrivebeskyttet adgang til meget (men ikke alle) af registreringsdatabasen på den virtuelle maskine de kører på. Det betyder registreringsdatabasenøgler, der tillader skrivebeskyttet adgang til den lokale gruppe brugere er tilgængelige for apps i praksis. Et område af registreringsdatabasen, der ikke understøttes i øjeblikket i læse- eller skriveadgang er HKEY\_aktuelle\_bruger hive.

Skriveadgang til registreringsdatabasen blokeres, herunder adgang til registreringsdatabasenøgler hver bruger. Fra den app perspektiv skriveadgang til registreringsdatabasen bør aldrig henvende sig i Azure-miljøet da apps kan (og gøre) overføres på tværs af forskellige virtuelle computere. Kun fast skrivbar opbevaring, der kan være afhængige af ved en app er-app indhold mappestrukturen gemt på App-tjenesten UNC aktier. 

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
