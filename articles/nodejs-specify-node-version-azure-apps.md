<properties
    pageTitle="Angive en Node.js Version"
    description="Lær, hvordan du angiver versionen af Node.js, der bruges af Azure-websteder og Cloud Services"
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

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Angive en Node.js version i et Azure-program

Når et Node.js program som vært, skal du muligvis vil sikre, at dit program bruger en bestemt version af Node.js. Der er flere måder til at udføre dette for hostes på Azure-programmer.

##<a name="default-versions"></a>Standardversioner

De Node.js versioner, der leveres af Azure opdateres konstant. Medmindre andet er angivet standardversionen, der er angivet i den `WEBSITE_NODE_DEFAULT_VERSION` miljøvariablen der skal bruges. Følg trinnene i nedenstående afsnit i denne artikel for at tilsidesætte denne standardværdi,

> [AZURE.NOTE] Hvis du er vært for dit program i en Azure skybaseret tjeneste (web eller arbejder rolle), og det er første gang, du har installeret programmet, forsøger Azure at bruge den samme version af Node.js, som du har installeret på din udviklingsmiljø, hvis den passer til en af de tilgængelige på Azure standardversioner.

##<a name="versioning-with-packagejson"></a>Versionsstyring med package.json

Du kan angive versionen af Node.js skal bruges ved at tilføje følgende til filen **package.json** :

    "engines":{"node":version}

Hvor er *version* bestemte versionsnummeret til brug. Du kan kan angive mere komplekse betingelser version, f.eks.:

    "engines":{"node": "0.6.22 || 0.8.x"}

Da 0.6.22 ikke er en af de tilgængelige i den værtsmiljø versioner, bruges den højeste version af 0,8 bliver serie, der er tilgængelig i stedet - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Versionsstyring websteder med App-indstillinger
Hvis du er vært for programmet på et websted, kan du indstille miljøvariablen **WEBSITE_NODE_DEFAULT_VERSION** til den ønskede version. 

##<a name="versioning-cloud-services-with-powershell"></a>Versionsstyring Cloud Services med PowerShell

Hvis du er vært for programmet i en skybaseret tjeneste, og installerer programmet ved hjælp af PowerShell Azure, kan du kan tilsidesætte Node.js standardversionen ved hjælp af **Sæt AzureServiceProjectRole** PowerShell-cmdlet. Eksempel:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Bemærk parametrene i ovenstående sætning er store og små bogstaver.  Du kan kontrollere den korrekte version af Node.js er valgt ved at markere egenskaben **programmer** i din rolle **package.json**.

Du kan også bruge **Get-AzureServiceProjectRoleRuntime** til at hente en liste over Node.js versioner tilgængelig for programmer, der er hostet som en skybaseret tjeneste.  Kontrollér altid versionen af Node.js afhænger af dit projekt er på denne liste.

##<a name="using-a-custom-version-with-azure-websites"></a>Ved hjælp af en brugerdefineret version med Azure websteder

Azure giver flere standardversioner af Node.js, kan du bruge en version, som ikke har angivet som standard. Hvis dit program er hostet som en Azure-websted, kan du gøre dette ved hjælp af **iisnode.yml** -filen. Følgende trin gennemgå processen med at bruge en brugerdefineret version af Node.Js med en Azure-websted:

1. Oprette en ny mappe, og derefter oprette en **server.js** fil i mappen. Filen **server.js** skal indeholde følgende:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Derved vises den Node.js version, der bruges, når du gennemser webstedet.

2. Oprette et nyt websted, og notér navnet på webstedet. For eksempel bruger følgende [Azure kommandolinjen værktøjer] til at oprette et nyt Azure-websted med navnet **mywebsite**, og derefter aktivere et ciffer lager for det offentlige websted.

        azure site create mywebsite --git

3. Oprette en ny mappe med navnet **placering** som underordnet til den mappe, der indeholder filen **server.js** .

4. Hent den bestemte version af **node.exe** (Windows-version), du vil bruge med dit program. For eksempel bruger følgende **curl** til at hente version 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Gem filen **node.exe** til mappen **bin** tidligere har oprettet.

5. Oprette en **iisnode.yml** fil i samme mappe som filen **server.js** , og derefter tilføje følgende indhold i filen **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Denne sti er, hvor filen **node.exe** i projektet vil være placeret, når du har udgivet dit program til Azure-webstedet.

6. Publicere dit program. For eksempel da jeg oprettede et nyt websted med parameteren – ciffer tidligere, kan følgende kommandoer tilføje programfiler til min lokale ciffer lager, og distribuere dem til websted lager:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Åbn webstedet i en browser, når programmet er udgivet. Der vises en meddelelse om "Hej fra Azure, der kører node version: v0.8.1".

##<a name="next-steps"></a>Næste trin

Nu hvor du forstå, hvordan du angiver versionen af Node.js, der bruges af dit program, lære, hvordan du [arbejder med moduler], [opbygge og anvende et Node.js websted], og [hvordan du kan bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux].

Du kan finde yderligere oplysninger finder [Node.js Developer Center](/develop/nodejs/).

[Hvordan du kan bruge værktøjerne Azure kommandolinjeparametre til Mac og Linux]: xplat-cli-install.md
[Azure kommandolinjen værktøjer]: xplat-cli-install.md
[arbejde med moduler]: nodejs-use-node-modules-azure-apps.md
[opbygge og anvende et Node.js websted]: web-sites-nodejs-develop-deploy-mac.md
