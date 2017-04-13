Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder en sektion med navnet parametre, der indeholder alle parameterværdierne.
Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der forbliver altid det samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installeret. 

Når du definerer parametre, kan du bruge feltet **allowedValues** til at angive, hvilke værdier, der er en bruger kan angive under installationen. Brug feltet **Standardværdi** for at tildele en værdi til parameteren, hvis nogen værdi ikke er angivet under installationen.

Vi beskriver hver parameter i skabelonen.

### <a name="sitename"></a>siteName

Navnet på den web-app, du vil oprette.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

Navnet på App serviceaftale skal bruges som vært WebApp.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU

Det priser niveau for hosting plan.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Skabelonen definerer de værdier, der er tilladt for denne parameter og tildeler en standardværdi (S1), hvis ikke er angivet nogen værdi.

### <a name="workersize"></a>workerSize

Forekomst størrelsen af den hosting plan (lille, mellem eller stor).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
Skabelonen definerer de værdier, der er tilladt for denne parameter (0, 1 eller 2) og tildeler en standardværdi (0), hvis ikke er angivet nogen værdi. Værdierne, der svarer til små, mellemstore og store.
