<properties 
    pageTitle="Sådan bruges io.js med Azure App Service Web Apps" 
    description="Lær, hvordan du bruger en WebApp i Azure App Service med io.js." 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016"
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Sådan bruges io.js med Azure App Service Web Apps

Populære Node forgreningsovergang [io.js] funktioner forskellige forskelle Joyent's Node.js projektet, herunder en mere åbent styringsmodel, en hurtigere udgivelsescyklus og en hurtigere indføring af nye og forsøg JavaScript-funktioner.

Mens [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps har mange Node.js versioner forudinstalleret, gør det også muligt for en bruger har udleveret Node.js binært tal. I denne artikel beskrives brug af io.js på App-tjenesten Web Apps på to måder: Brug af en udvidet installation script, som konfigurerer automatisk Azure for at bruge den nyeste version af tilgængelige io.js samt manuel overførsel af en binær io.js. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>Brug af en installation Script

Efter installation af en Node.js-app kører App Service Web Apps forskellige small kommandoer for at sikre, at miljøet er konfigureret korrekt. Brug af en installation script, kan denne proces tilpasses for at medtage overførslen og konfiguration af io.js.

[Io.js installation Script](https://github.com/felixrieseberg/iojs-azure) er tilgængelig på GitHub. Hvis du vil aktivere io.js på din online, skal du blot kopiere **.deployment**, **deploy.cmd** og **IISNode.yml** i roden af mappen programmer og anvende til Web Apps.  

Den første fil, **.deployment**, får Web Apps til at køre **deploy.cmd** på installation. Dette script kører alle sædvanlige trinnene til et Node.js program, men også henter den nyeste version af io.js. Til sidst skal konfigurerer **IISNode.yml** Web Apps for at bruge kun de hentede io.js binære i stedet for en forudinstalleret Node.js binært tal.

> [AZURE.NOTE] Opdatere den anvendte io.js binære fil, skal du blot Geninstaller dit program - henter scriptet en ny version af io.js hver enkelt gang, at programmet installeres.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>Brug af manuel Installation

Manuel installation af en brugerdefineret io.js version indeholder kun to trin. Hent først **gevinst-x64** binære direkte fra [io.js fordeling]. Påkrævet er to filer - **iojs.exe** og **iojs.lib**. Gemme begge filer i en mappe i din online, for eksempel i **placering/iojs**.

Oprette en **IISNode.yml** fil i roden af dit program for at konfigurere Web Apps for at bruge **iojs.exe** i stedet for en forudinstalleret Node-version, og Tilføj følgende linje.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Næste trin

I denne artikel, du har lært Sådan bruger du angivet io.js med App-tjenesten Web Apps, ved hjælp af begge installation scripts samt som manuel installation. 

> [AZURE.NOTE] IO.js i tunge udvikling og opdateret oftere end Node.js. Et antal Node.js moduler fungerer muligvis ikke med io.js - skal du se [io.js på GitHub] til fejlfinding.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

[IO.js]: https://iojs.org
[IO.js fordeling]: https://iojs.org/dist/
[IO.js på GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 