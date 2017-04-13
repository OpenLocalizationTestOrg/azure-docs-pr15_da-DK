Med Azure ressource Manager skal du definere parametre for værdier, du vil angive, hvornår skabelonen, der er installeret. Skabelonen indeholder en sektion med navnet parametre, der indeholder alle parameterværdierne.
Du skal angive en parameter for de værdier, der varierer baseret på det projekt, du anvender eller baseret på det miljø, du installerer til. Ikke definere parametre for værdier, der forbliver altid det samme. Hver parameterværdi bruges i skabelonen til at definere de ressourcer, der er installere. 

Når du definerer parametre, kan du bruge feltet **allowedValues** til at angive, hvilke værdier, der er en bruger kan angive under installationen. Brug feltet **Standardværdi** for at tildele en værdi til parameteren, hvis nogen værdi ikke er angivet under installationen.

Vi beskriver hver parameter i skabelonen.

### <a name="logicappname"></a>logicAppName

Navnet på logik app til at oprette.

    "logicAppName": {
        "type": "string"
    }