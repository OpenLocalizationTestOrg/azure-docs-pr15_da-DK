<properties
    pageTitle="Fleksible softwareudvikling med Azure App Service"
    description="Lær at oprette Høj skalerbarhed komplekse programmer med Azure App Service på en måde, der understøtter fleksible softwareudvikling."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>


# <a name="agile-software-development-with-azure-app-service"></a>Fleksible softwareudvikling med Azure App Service #

I dette selvstudium lærer du, hvordan du opretter Høj skalerbarhed komplekse programmer med [Azure App Service](/services/app-service/) på en måde, der understøtter [fleksible softwareudvikling](https://en.wikipedia.org/wiki/Agile_software_development). Det antages, at du allerede ved, hvordan du kan [installere komplekse programmer forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md).

Begrænsninger i tekniske processer kan ofte fremstår forstyrrende vellykket gennemførelse af fleksible metoder. Azure App Service med funktioner som [sammenhængende udgivelse](app-service-continuous-deployment.md), [arrangere miljøer](web-sites-staged-publishing.md) (pladser) og [overvågning](web-sites-monitor.md), når forbundet med omhu til organisering og administration af distributionen i [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md), kan være en del af en god løsning for udviklere, der Udnyt fleksible softwareudvikling.

Den følgende tabel er en kort liste over krav, der er knyttet til fleksible udvikling, og hvordan Azure tjenester aktiverer dem.

| Krav | Hvordan aktiverer Azure |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Opbygge med hver bekræftelse<br>-Opbygge automatisk og hurtig | Når er konfigureret med kontinuert installation, kan Azure App Service fungere som live kører builds baseret på en Udviklingscenter gren. Hver gang kode overføres til grenen, er det automatisk indbyggede og køre direkte i Azure.|
| -Kontrollér opbygger selv test | Indlæse test, test web, osv., kan der installeres med skabelonen Azure ressourcestyring.|
| -Udfør test i en kopi af produktionsmiljø | Azure ressourcestyring skabeloner kan bruges til at oprette kopier af den Azure produktionsmiljø (herunder indstillinger for forbindelse streng skabeloner skalering, og osv.) til test hurtigt og forudsigeligt.|
| – Få vist resultatet af seneste build nemt | Kontinuert installation til Azure fra et lager betyder, at du kan teste ny kode i et direkte til computeren med det samme, når du bekræfter ændringerne. |
| -Bekræfte til den primære gren hver dag<br>-Automatisere installation | Kontinuert integration af et fremstilling program med et lager primære gren installerer automatisk alle Anvend/Flet til den primære gren fremstilling. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Hvad du vil gøre ##

Du kan gennemgå en typisk Udviklingscenter-test-fase-produktiv arbejdsproces for at publicere ændringer til programmet [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) eksempel, der består af to [Onlines](/services/app-service/web/), en blive en front end (FE), og den anden der en Web API back-end (BE), og en [SQL-database](/services/sql-database/). Du kan arbejde med den installationsarkitektur, der er vist nedenfor:

![](./media/app-service-agile-software-development/what-1-architecture.png)

Indsætte billedet i ord:

-   Installation arkitekturen opdeles i tre forskellige miljøer (eller [grupper](../azure-resource-manager/resource-group-overview.md) i Azure), hver med sine egne [App-serviceaftale](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), [Skalering](web-sites-scale.md) indstillinger og SQL-database. 
-   De enkelte miljøer kan administreres separat. De kan også findes i forskellige abonnementer.
-   Test- og implementeres som to pladser af den samme App Service-app. Den overordnede gren er indstillet til fortløbende integration med den midlertidige slot.
-   Når en bekræftelse til master gren er bekræftet på den midlertidige slot (med fremstilling data), skiftet bekræftet midlertidige appen i visningen fremstilling slot [uden nedetid](web-sites-staged-publishing.md).

Fremstilling og midlertidige miljøet er defineret af skabelonen på [ * &lt;repository_root >*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Afsnittet Udviklingscenter og test miljøer er defineret af skabelonen på [ * &lt;repository_root >*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Du kan også bruge den typiske forgrening strategi med kode, der flyttes fra Udviklingscenter gren op til gren test derefter til den overordnede gren (bevæge sig i kvalitet, så til speak).

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>Du skal bruge: ##

-   En Azure-konto
-   En [GitHub](https://github.com/) -konto
-   Ciffer Shell (installeret med [GitHub til Windows](https://windows.github.com/)) - Dette gør det muligt at køre både ciffer og PowerShell-kommandoer i den samme session 
-   Seneste [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) -bit
-   Grundlæggende forståelse af følgende:
    -   Installation af [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) skabelon (også se [Implementer en kompleks programmet forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md))
    -   [Ciffer](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Du skal bruge en Azure-konto til at udføre dette selvstudium:
> + Du kan [åbne en Azure-konto gratis](/pricing/free-trial/) – du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant du holder kontoen, og brug ledig Azure tjenester, som Web Apps.
> + Du kan [aktivere Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/) – dit Visual Studio-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.
>
> Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="set-up-your-production-environment"></a>Konfigurere dit produktionsmiljø ##

>[AZURE.NOTE] Det script, der bruges i dette selvstudium konfigureres automatisk fortløbende udgivelse fra din GitHub lager. Dette kræver, at legitimationsoplysningerne GitHub allerede er gemt i Azure, ellers script installationen mislykkes, når du forsøger at konfigurere indstillinger for versionsstyring for webapps. 
>
>For at gemme dine GitHub legitimationsoplysninger i Azure, du Opret en WebApp i [Azure portalen](https://portal.azure.com/) og [konfigurere GitHub installation](app-service-continuous-deployment.md). Du skal kun gøre det én gang. 

I et typisk DevOps scenarie, du har et program, der kører direkte i Azure, og du vil foretage ændringer i den gennem fortløbende udgivelse. I dette scenarie skal vælge du en skabelon, du udviklet, testet og bruges til at installere produktionsmiljøet. Du vil konfigurere det i dette afsnit.

1.  Oprette din egen forgreningsovergang [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -lager. Du kan finde oplysninger om oprettelse af din forgreningsovergang [forgreningsovergang en Repo](https://help.github.com/articles/fork-a-repo/). Når din forgreningsovergang er oprettet, kan du se den i din browser.
 
    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Åbne en ciffer Shell session. Hvis du endnu ikke har ciffer Shell, [GitHub til Windows](https://windows.github.com/) Installer nu.

3.  Oprette en lokal kopi af din forgreningsovergang ved at udføre følgende kommando:

        git clone https://github.com/<your_fork>/ToDoApp.git 

4.  Når du har din lokale Klon, gå til * &lt;repository_root >*\ARMTemplates, og kør deploy.ps1 scripts på følgende måde:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.  Når du bliver bedt om det, kan du skrive i det ønskede brugernavn og adgangskode til database access.

    Du bør se klargøring status for forskellige Azure ressourcer. Når installationen er fuldført, kan scriptet Start programmet i browseren, og giver dig et brugervenligt bip.

    ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
    >[AZURE.TIP] Se nærmere på * &lt;repository_root >*\ARMTemplates\Deploy.ps1 at se, hvordan der oprettes ressourcer med entydige id'er. Du kan bruge den samme fremgangsmåde til at oprette kopier af den samme installation uden at bekymre dig om modstridende ressourcenavne.
 
6.  Kør tilbage i sessionen ciffer Shell:

        .\swap –Name ToDoApp<unique_string>master

    ![](./media/app-service-agile-software-development/production-4-swap.png)

7.  Når scriptet afsluttes, skal du gå tilbage til at finde frem til den front end-adresse (http://ToDoApp*&lt;unique_string >*master.azurewebsites.net/) til at se det program, der kører i fremstilling.
 
5.  Log på [Azure-portalen](https://portal.azure.com/) , og se nærmere på Hvad er oprettet.

    Du skal kunne se to webapps i samme ressourcegruppe, hvoraf den ene bruger den `Api` suffiks i navnet. Hvis du ser på gruppen ressourcevisning, kan du også se den SQL-Database og server, App-serviceaftale og de midlertidige pladser til webapps. Gennemse de forskellige ressourcer og sammenligne dem med * &lt;repository_root >*\ARMTemplates\ProdAndStage.json at se, hvordan de konfigureres i skabelonen.

    ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Du har nu konfigureret produktionsmiljøet. Derefter skal vil du starte en ny opdatering til programmet.

## <a name="create-dev-and-test-branches"></a>Oprette Udviklingscenter og teste forgreninger ##

Nu hvor du har en kompleks program, der kører i fremstilling i Azure, får du en opdatering til dit program i overensstemmelse med fleksible metode. I dette afsnit, du opretter Udviklingscenter og teste forgreninger, skal du foretage de nødvendige opdateringer.

1.  Oprette testmiljøet først. Kør følgende kommandoer til at oprette miljø til en ny gren kaldet **NewUpdate**i sessionen ciffer Shell. 

        git checkout -b NewUpdate
        git push origin NewUpdate 
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.  Når du bliver bedt om det, kan du skrive i det ønskede brugernavn og adgangskode til database access. 

    Når installationen er fuldført, kan scriptet Start programmet i browseren, og giver dig et brugervenligt bip. Og ligesom den, du har nu en ny gren med sin egen testmiljø. Tage et øjeblik på at gennemgå et par ting om testmiljø:

    -   Du kan oprette den i en hvilken som helst Azure-abonnement. Det betyder, at produktionsmiljøet kan administreres separat fra dit testmiljø.
    -   Dit testmiljø kører direkte i Azure.
    -   Dit testmiljø er identisk med produktionsmiljø, med undtagelse af de midlertidige pladser og indstillingerne for skalering. Du kan se dette, fordi dette er den eneste forskel mellem ProdandStage.json og Dev.json.
    -   Du kan administrere dit testmiljø i sin egen App-serviceaftale, med en anden pris niveau (såsom **gratis**).
    -   Slette denne testmiljø vil være så enkelt som at slette ressourcegruppen. Du kan finde ud af, hvordan du gør dette [senere](#delete).

2.  Fortsæt for at oprette en Udviklingscenter gren ved at køre følgende kommandoer:

        git checkout -b Dev
        git push origin Dev
        .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.  Når du bliver bedt om det, kan du skrive i det ønskede brugernavn og adgangskode til database access. 

    Tage et øjeblik på at gennemgå et par ting om dette Udviklingscenter miljø: 

    -   Udviklingscenter miljøet har en identisk med testmiljøet konfiguration, da den er installeret ved hjælp af den samme skabelon.
    -   De enkelte Udviklingscenter miljøer kan oprettes i developer's egen Azure abonnement, at forlade testmiljø administreres separat.
    -   Udviklingscenter miljøet kører direkte i Azure.
    -   Sletning af Udviklingscenter miljøet er så enkelt som at slette ressourcegruppen. Du kan finde ud af, hvordan du gør dette [senere](#delete).

>[AZURE.NOTE] Når du har flere udviklere arbejder på den nye opdatering, kan hver af dem nemt oprette en gren og dedikeret Udviklingscenter miljø ved at gøre følgende:
>
>1. Oprette deres egne forgreningsovergang lageret i GitHub (se [forgreningsovergang en Repo](https://help.github.com/articles/fork-a-repo/)).
>2. Klone forgreningsovergang på deres lokale computer
>3. Køre de samme kommandoer til at oprette deres egne Udviklingscenter gren og -miljø.

Når du er færdig, skal din GitHub forgreningsovergang har tre grene:

![](./media/app-service-agile-software-development/test-1-github-view.png)

Og du skal have seks Onlines (tre sæt af to) i tre separate ressourcegrupper:

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE] Bemærk, at ProdandStage.json angiver produktionsmiljø for at bruge den **Standard** priser lag, som er relevante for skalerbarhed af fremstilling programmet.

## <a name="build-and-test-every-commit"></a>Opbygge og teste hver bekræftelse ##

Skabelonfiler ProdAndStage.json og Dev.json angiver allerede kilde kontrolelement parametre, der som standard konfigurerer fortløbende udgivelse for WebApp. Derfor udløser hver bekræftelse GitHub gren en automatisk installation til Azure fra grenen. Lad os se, hvordan din konfiguration fungerer nu.

1.  Sørg for, at du er i Udviklingscenter grenen af den lokale lager. For at gøre dette skal du køre følgende kommando i ciffer Shell:

        git checkout Dev

2.  Gøre en enkelt ændring til den app Brugergrænsefladen lag ved at ændre koden for at bruge [Bootstrap](http://getbootstrap.com/components/) lister. Åbn * &lt;repository_root >*\src\MultiChannelToDo.Web\index.cshtml og foretager fremhævede ændringen nedenfor:

    ![](./media/app-service-agile-software-development/commit-1-changes.png)

    >[AZURE.NOTE] Hvis du ikke kan læse billedet ovenfor: 
    >
    >- Ændre linjen 18 `check-list` til `list-group`.
    >- Ændre linjen 19 `class="check-list-item"` til `class="list-group-item"`.

3.  Gemme ændringen. Tilbage i ciffer Shell køre følgende kommandoer:

        cd <repository_root>
        git add .
        git commit -m "changed to bootstrap style"
        git push origin Dev
 
    Kommandoerne ciffer ligner "fejlkontrol i din kode" i en anden kilde control system som TFS. Når du kører `git push`, den nye bekræftelse udløser en automatisk kode opslagsnål til Azure, som gendanner derefter programmet til at ændringen i Udviklingscenter-miljø.

4.  Gå til dit Udviklingscenter miljø web app blade for at bekræfte, at der opstod denne kode opslagsnål Udviklingscenter-miljø, og kig på delen **installation** . Du skal kunne se dine seneste bekræftelse der.

    ![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.  Klik på **Gennemse** for at se den nye ændring i programmet direkte i Azure derfra.

    ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

    Dette er en ret mindre ændring af programmet. Mange gange ændringer i en kompleks webprogram har dog utilsigtede og uønskede side effekter. Der kan nemt teste hver bekræftelse i direkte builds gør det muligt at løse disse problemer, inden dine kunder få dem vist.

På nuværende tidspunkt, skal du være fortrolig med gennemførelse, som en udvikler på **NewUpdate** projektet, du kan nemt oprette et Udviklingscenter miljø til dig selv, og derefter opbygge hver bekræftelse og teste hver build.

## <a name="merge-code-into-test-environment"></a>Flette kode til testmiljø ##

Når du er klar til at overføre din kode fra Udviklingscenter gren op til NewUpdate gren, er det standard ciffer processen:

1.  Flette et nyt anvendelser til NewUpdate til Udviklingscenter gren i GitHub, som er oprettet af andre udviklere anvendelser. En ny bekræftelse på GitHub vil udløse en kode opslagsnål og opbygge i Udviklingscenter-miljø. Du kan derefter sikre, at din kode i Udviklingscenter gren fungerer stadig med de seneste bit fra NewUpdate gren.

2.  Flette alle dine nye anvendelser fra Udviklingscenter gren til NewUpdate gren på GitHub. Denne handling udløser en kode opslagsnål og build i testmiljøet. 

Note igen, fordi fortløbende installation er allerede konfigureret med disse ciffer grene, du ikke behøver at udføre andre handlinger som kører integration opbygger. Du skal bare til at udføre almindelige kilde kontrolelement fremgangsmåder ved hjælp af ciffer, og Azure udfører alle build processer for dig.

Nu, Lad os overføre din kode til **NewUpdate** gren. I ciffer Shell, skal du køre følgende kommandoer:

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

Det var det! 

Gå til bladet web app til dit testmiljø for at se din nye bekræftelse (flettet ind i NewUpdate gren) nu overføres til testmiljøet. Klik derefter på **Gennemse** for at få vist, typografi ændringen kører nu direkte i Azure.

## <a name="deploy-update-to-production"></a>Installere opdatering på fremstilling ##

Trykke på kode til test- og miljøet skal føler ikke anderledes end hvad du har allerede har gjort når du overføres kode til testmiljø. Er det virkelig, som en simpel. 

I ciffer Shell, skal du køre følgende kommandoer:

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

Husk, at baseret på den måde, det midlertidige og fremstilling miljø er konfigureret i ProdandStage.json, din nye kode rykkes til den **midlertidige** plads og kører der. Så hvis du navigerer til den midlertidige slot URL-adresse, får du vist den nye kode, der kører. For at gøre dette skal køre den `Show-AzureWebsite` cmdlet i ciffer Shell.

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Og nu, når du har bekræftet opdateringen i den midlertidige slot, det eneste, der er tilbage, du kan udskifte det til fremstilling. I ciffer Shell kun køre følgende kommandoer:

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

Tillykke! Du har publiceret en ny opdatering til dit fremstilling webprogram. Hvad der er mere er, at du lige har gjort det ved at nemt oprette Udviklingscenter og teste miljøerne, og opbygge og teste hver bekræftelse. Dette er vigtig byggesten for fleksible softwareudvikling.

<a name="delete"></a>
## <a name="delete-dev-and-test-enviroments"></a>Slette Udviklingscenter og teste enviroments ##

Det er meget nemt at slette dem, fordi du har bevidst skabt din Udviklingscenter og test miljøer for at være selvstændig ressourcegrupper. Hvis du vil slette dem, du oprettede i dette selvstudium både GitHub forgreninger og Azure, konstruktioner kun køre følgende kommandoer i ciffer Shell:

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>Oversigt ##

Fleksible softwareudvikling er en nødt til at have for mange virksomheder, som ønsker at indføre Azure som deres programmet platform. I dette selvstudium, har du lært at oprette og går ned nøjagtige replikaer eller i nærheden af kopier af produktionsmiljøet i stykker uden besvær, også til komplekse programmer. Du har også lært at udnytte denne mulighed for at oprette en udviklingsproces, der kan opbygge og teste hver enkelt bekræftelse i Azure. Dette selvstudium har forhåbentlig vist dig, hvordan du kan bedst bruger Azure App Service og Azure ressourcestyring sammen til at oprette en DevOps løsning, der caters til fleksible metoder. Derefter skal kan du oprette i dette scenarie ved at udføre avancerede DevOps teknikker som [test fremstilling](app-service-web-test-in-production-get-start.md). Du kan finde et almindelige test-i-produktionsmiljø, [Flighting installation (test af betaversion) i Azure App-tjeneste](app-service-web-test-in-production-controlled-test-flight.md).

## <a name="more-resources"></a>Flere ressourcer ##

-   [Installere en kompleks program forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md)
-   [Fleksible udvikling i praksis: tip og trick til moderniserede udviklingscyklus](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-   [Avanceret installationsstrategier til Azure Web Apps ved hjælp af Ressourcestyring skabeloner](http://channel9.msdn.com/Events/Build/2015/2-620)
-   [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)
-   [JSONLint - JSON kontrollere](http://jsonlint.com/)
-   [ARMClient – Konfigurer GitHub udgivelse til websted](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-   [Ciffer forgreninger – grundlæggende forgreninger og flette](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [David Ebbo Blog](http://blog.davidebbo.com/)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Azure på tværs af platforme kommandolinjen værktøjer](../xplat-cli-install.md)
-   [Oprette eller redigere brugere i Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-   [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
