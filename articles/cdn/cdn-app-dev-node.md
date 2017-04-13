<properties
    pageTitle="Introduktion til Azure CDN SDK til Node.js | Microsoft Azure"
    description="Lær, hvordan du kan skrive Node.js programmer til at administrere Azure CDN."
    services="cdn"
    documentationCenter="nodejs"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introduktion til Azure CDN udvikling

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Du kan bruge [Azure CDN SDK for Node.js](https://www.npmjs.com/package/azure-arm-cdn) til at automatisere oprettelse og administration af CDN profiler og slutpunkter.  Dette selvstudium indeholder en gennemgang oprettelse af et enkelt Node.js console-program, der viser flere af de tilgængelige handlinger.  Dette selvstudium er ikke beregnet til at beskrive alle aspekter af Azure CDN SDK for Node.js detaljeret.

For at fuldføre dette selvstudium skal du bør allerede har [Node.js](http://www.nodejs.org) **4.x.x** eller nyere installeret og konfigureret.  Du kan bruge en hvilken som helst tekstredigeringsprogram, du vil oprette dit Node.js program.  Hvis du vil skrive dette selvstudium, jeg har brugt [Visual Studio-kode](https://code.visualstudio.com).  

> [AZURE.TIP] Den [fuldførte projektet fra dette selvstudium](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) kan hentes på MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Oprette projektet og tilføje NPM afhængigheder

Nu hvor vi har oprettet en ressourcegruppe til vores CDN profiler og har vores Azure AD-program tilladelse til at administrere CDN profiler og slutpunkter i den pågældende gruppe, kan vi begynde at oprette vores program.

Oprette en mappe for at gemme dit program.  Angiv din aktuelle placering til den nye mappe fra en konsol med værktøjerne Node.js i din aktuelle sti og initialiseret projektet ved at udføre:
    
    npm init
    
Derefter vises der en række spørgsmål initialiseret projektet.  Dette selvstudium bruger til **indsætningspunkt**, *app.js*.  Du kan se mine andre valgmuligheder i følgende eksempel.

![NPM initialisering output](./media/cdn-app-dev-node/cdn-npm-init.png)

Vores projekt er nu initialiseret med en *packages.json* fil.  Vores project skal bruge nogle Azure biblioteker, der er indeholdt i NPM pakker.  Vi bruger Azure Client Runtime for Node.js (ms-resten-azure) og biblioteket Azure CDN klient til Node.js (azure-arm-cd).  Lad os føje dem til projektet som afhængigheder.
 
    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Når pakkerne er færdig med skal installationen *package.json* fil ligne dette eksempel (version tal kan variere):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Endelig bruger tekstredigeringsprogrammet, opretter en tom tekstfil og gemmer den i roden af vores projektmappe som *app.js*.  Vi er nu klar til at begynde at skrive kode.

## <a name="requires-constants-authentication-and-structure"></a>Kræver, konstanter, godkendelse og struktur

Lad os få grundlæggende strukturen i vores program, der er skrevet med *app.js* åben i vores editor.

1. Tilføje den "kræver" til vores NPM pakker øverst med følgende:

    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```

2. Vi vil definere nogle vores metoder anvender konstanter.  Tilføj følgende.  Sørg for at erstatte de pladsholdere, herunder den ** &lt;vinklede parenteser&gt;**, med dine egne værdier efter behov.

    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";

    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Dernæst skal vi oprette en forekomst af CDN management-klienten og give den vores legitimationsoplysninger.

    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
    
    Hvis du bruger individuelle brugergodkendelse, er disse to linjer ser lidt anderledes.

    >[AZURE.IMPORTANT] Brug kun denne eksempel-kode, hvis du vælger at have individuelle brugergodkendelse i stedet for en tjeneste vigtigste.  Sørg for at beskytte din individuelle brugerlegitimationsoplysninger og holde dem hemmeligt.

    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```

    Sørg for at erstatte elementerne i ** &lt;vinklede parenteser&gt; ** med de korrekte oplysninger.  For `<redirect URI>`, bruge Omdiriger URI, du har angivet, når du har registreret programmet i Azure AD.
    

4.  Vores Node.js console-program, skal tage nogle kommandolinjeparametre.  Lad os validere pågældende mindst én parameter blev overført.

    ```javascript
    //Collect command-line parameters
    var parms = process.argv.slice(2);

    //Do we have parameters?
    if(parms == null || parms.length == 0)
    {
        console.log("Not enough parameters!");
        console.log("Valid commands are list, delete, create, and purge.");
        process.exit(1);
    }
    ```

5. Der viser os til at størstedelen af vores program, hvor vi forgrenes andre funktioner, der er baseret på de blev sendt.

    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;

        case "create":
            cdnCreate();
            break;
        
        case "delete":
            cdnDelete();
            break;

        case "purge":
            cdnPurge();
            break;

        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```

6.  På mange forskellige steder i vores program, får vi brug at sikre, at det rigtige antal parametre blev sendt ind og få vist Hjælp, hvis de ikke ser rigtige.  Lad os oprette funktioner for at gøre dette.

    ```javascript
    function requireParms(parmCount) {
        if(parms.length < parmCount) {
            usageHelp(parms[0].toLowerCase());
            process.exit(1);
        }
    }

    function usageHelp(cmd) {
        console.log("Usage for " + cmd + ":");
        switch(cmd)
        {
            case "list":
                console.log("list profiles");
                console.log("list endpoints <profile name>");
                break;

            case "create":
                console.log("create profile <profile name>");
                console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
                break;
            
            case "delete":
                console.log("delete profile <profile name>");
                console.log("delete endpoint <profile name> <endpoint name>");
                break;

            case "purge":
                console.log("purge <profile name> <endpoint name> <path>");
                break;

            default:
                console.log("Invalid command.");
        }
    }
    ```

7. De funktioner, som vi bruger på CDN management-klienten er til sidst skal asynkron, så de har brug for en metode til at ringe tilbage, når de er færdig.  Lad os oprette et, der kan vise output fra CDN management-klienten (hvis relevant) og afslutte programmet problemfrit.

    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nu, hvor den grundlæggende struktur i vores program er skrevet, skal vi oprette de funktioner, der kaldes baseret på vores parametre.

## <a name="list-cdn-profiles-and-endpoints"></a>Listen CDN profiler og slutpunkter

Lad os starte med kode til vores eksisterende profiler og slutpunkter.  Kommentarerne kode giver den forventede syntaks, så vi kan se, hvor hver parameter går hen.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Oprette CDN profiler og slutpunkter

Derefter skal vi skrive funktioner for at oprette profiler og slutpunkter.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Slette et slutpunkt

Hvis slutpunktet er blevet oprettet, er en almindelig opgave, som vi muligvis vil udføre i vores program til sletning indhold i vores slutpunkt.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Slette CDN profiler og slutpunkter

Funktionen sidste vi indeholder sletter slutpunkter og profiler.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Køre programmet

Vi kan nu udføre vores program til Node.js ved brug af vores foretrukne fejlfinding eller i konsollen.

> [AZURE.TIP] Hvis du bruger Visual Studio-kode som din fejlfindingen, skal du konfigurere dit miljø til at overføre i kommandolinjeparametre.  Visual Studio kode gør dette i filen **lanuch.json** .  Se efter en egenskab med navnet **argumenter** og tilføje en matrix med strengværdier for din parametre, så den ser nogenlunde sådan ud: `"args": ["list", "profiles"]`.

Lad os starte med at vise vores profiler.

![Listen profiler](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vi er kommet tilbage en tom matrix.  Da vi ikke er nogen profiler i vores ressourcegruppe, der forventes.  Lad os oprette en profil nu.

![Oprette profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Nu, Lad os tilføje et slutpunkt.

![Oprette slutpunkt](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Lad os Slet til sidst skal vores profil.

![Slet profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Næste trin

For at se det færdige projekt fra denne gennemgang, [hente stikprøvernes](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Få vist [reference](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)for at se referencen til Azure CDN SDK til Node.js.

Du kan finde yderligere dokumentation på Azure SDK til Node.js, få vist [fuld reference](http://azure.github.io/azure-sdk-for-node/).

Administrere dine CDN ressourcer med [PowerShell](./cdn-manage-powershell.md).

