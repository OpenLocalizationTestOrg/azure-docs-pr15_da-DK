<properties
    pageTitle="Arbejde med Node.js moduler"
    description="Få mere at vide om at arbejde med Node.js moduler, når du bruger Azure App Service Cloud Services."
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="using-nodejs-modules-with-azure-applications"></a>Brug af Node.js moduler med Azure-programmer

Dette dokument indeholder vejledning til brug af Node.js moduler sammen med hostes på Azure-programmer. Det giver vejledning i at sikre, at dit program bruger en bestemt version af modulet samt ved hjælp af Oprindelig moduler med Azure.

Hvis du allerede kender, er ved hjælp af Node.js moduler, **package.json** og **npm shrinkwrap.json** filer, følgende en hurtig oversigt over hvad er beskrevet i denne artikel:

* Azure App Service forstår **package.json** og **npm shrinkwrap.json** filer og kan installere moduler, der er baseret på værdierne i disse filer.
* Azure-Skytjenester forventer, at alle moduler skal være installeret på udviklingsmiljø, og den **node\_moduler** directory der skal medtages som en del af installationspakken. Det er muligt at aktivere understøttelse af installerer moduler ved hjælp af **package.json** eller **npm shrinkwrap.json** -filer på Cloud Services, men dette kræver tilpasning af de standard scripts, som bruges af skytjeneste projekter. Se et eksempel af hvordan du gør dette, under [Azure Start opgaven til at køre npm Installer for at undgå at implementere node moduler](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Azure virtuelle maskiner er ikke beskrevet i denne artikel, som installation oplevelsen i en VM bliver afhænger af det operativsystem, der er hostet af den virtuelle maskine.

##<a name="nodejs-modules"></a>Node.js moduler

Moduler er indlæses JavaScript-pakker, som giver bestemte funktioner for dit program. Et modul installeres normalt ved hjælp af værktøjet **npm** kommandolinjen, men nogle (såsom HTTP-modulet) er angivet som en del af pakken core Node.js.

Når moduler er installeret, de er gemt i den **node\_moduler** mappe i roden af mappestrukturen programmet på computeren. Hvert modul i den **node\_moduler** directory vedligeholder sin egen **node\_moduler** mappe, der indeholder eventuelle moduler, som det afhænger af, og dette gentager igen for hver modul hele vejen ned afhængighedskæden. Dette giver mulighed for hvert modul, der er installeret for at få sin egen versionskrav til modulerne det afhænger af, men det kan medføre temmelig store mappestrukturen.

Når du installerer den **node\_moduler** bibliotek som en del af dit program, det vil øge størrelsen på den installation, sammenlignet med ved hjælp af en **package.json** eller **npm shrinkwrap.json** fil. det garanterer, at versionen af de moduler, anvendes fremstilling er de samme som dem, der bruges i udvikling.

###<a name="native-modules"></a>Oprindelig moduler

Mens de fleste moduler er blot tekstformat JavaScript-filer, er nogle moduler platform-specifikke binære billeder. Disse moduler er kompileret, når du installerer, som regel ved hjælp af Python og node gyp. Da Azure Cloud Services, der er afhængige af den **node\_moduler** mappe, der installeres som en del af det program, alle oprindelige modul, der er inkluderet som en del af de installerede moduler skal arbejde i en skybaseret tjeneste, så længe den er installeret og kompileret på en Windows udvikling system.

Azure App Service understøtter ikke alle oprindelige moduler og kan mislykkes på kompilering dem med meget bestemte forudsætninger. Mens nogle populære moduler som MongoDB har valgfrie oprindelige afhængigheder og arbejde lige fin uden dem, vist sig vellykket med næsten alle oprindelige moduler, der er tilgængelige i dag to løsninger:

* Køre **npm installere** på en Windows-computer, der har alle de oprindelige modulet forudsætninger, der er installeret. Derefter installere den oprettede **node\_moduler** mappe som en del af programmet til Azure App-tjenesten.
* Azure App Service kan konfigureres for at udføre brugerdefinerede fest eller shellscripts under installationen, hvilket giver dig mulighed for at udføre brugerdefinerede kommandoer og nøjagtigt konfigurere måde **npm installere** kører. Du kan finde en video, der viser, hvordan du gør dette, [Brugerdefineret websted installation Scripts med Kudu].

###<a name="using-a-packagejson-file"></a>Ved hjælp af en package.json-fil

**Package.json** filen er en metode til at angive de øverste niveau afhængigheder programmet kræver så hosting platform kan installere afhængighederne i stedet for at du behøver at medtage de **node\_pakker** mappe som en del af installationen. Kommandoen **npm installere** bruges til at analysere filen **package.json** og installere alle afhængigheder, der vises, når programmet er blevet installeret.

Under udvikling, kan du bruge den **– Gem**, **– Gem-Udviklingscenter**, eller **– Gem valgfrit** parametre, når du installerer moduler for at tilføje en post for modulet til filen **package.json** automatisk. Du kan finde yderligere oplysninger finder [npm-installation](https://docs.npmjs.com/cli/install).

Én potentielle problemer med **package.json** filen er, at det kun angiver versionen for øverste niveau afhængigheder. Hvert modul, der er installeret kan eller ikke kan angiver versionen af moduler det afhænger af, og så det er muligt, at du muligvis ender med en anden afhængighed kæde end den, der bruges i udvikling.

> [AZURE.NOTE]
> Når du installerer på Azure App Service, hvis filen <b>package.json</b> henviser til et oprindelige modul vises der en stil med følgende fejl, når du udgiver programmet ved hjælp af ciffer:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>Ved hjælp af en npm shrinkwrap.json-fil

Filen **npm shrinkwrap.json** er et forsøg på at løse filen **package.json** modul versionsstyring begrænsninger. Mens **package.json** filen indeholder kun versioner for øverste niveau moduler, indeholder filen **npm shrinkwrap.json** versionskravene til fuld modul afhængighedskæden.

Når dit program er klar til fremstilling, kan du låse-ned versionskravene og oprette en **npm shrinkwrap.json** -fil ved hjælp af kommandoen **npm shrinkwrap** . Dette vil bruge de versioner, der aktuelt er installeret i den **node\_moduler** mappe, og registrere dem til filen **npm shrinkwrap.json** . Kommandoen **npm installere** bruges til at analysere filen **npm shrinkwrap.json** og installere alle afhængigheder, der vises, når programmet er blevet installeret på den værtsmiljø. Du kan finde flere oplysninger [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Når du installerer på Azure App Service, hvis filen <b>npm shrinkwrap.json</b> henviser til et oprindelige modul vises der en stil med følgende fejl, når du udgiver programmet ved hjælp af ciffer:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Næste trin

Nu hvor du ved, hvordan du bruger Node.js moduler med Azure, lære, hvordan du [angiver Node.js versionen], [opbygge og anvende en Node.js web-app], og [hvordan du kan bruge kommandolinjen Azure til Mac og Linux].

Du kan finde yderligere oplysninger finder [Node.js Developer Center](/develop/nodejs/).

[Angiv Node.js versionen]: nodejs-specify-node-version-azure-apps.md
[Hvordan du kan bruge kommandolinjen Azure til Mac og Linux]: xplat-cli-install.md
[opbygge og anvende en Node.js WebApp]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Brugerdefineret websted installation Scripts med Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
