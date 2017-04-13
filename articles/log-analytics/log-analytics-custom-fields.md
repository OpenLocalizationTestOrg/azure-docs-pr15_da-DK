<properties
   pageTitle="Brugerdefinerede felter i Log Analytics | Microsoft Azure"
   description="Funktionen brugerdefinerede felter i Log Analytics kan du oprette dine egne søgbar felter fra OMS data, føjes til egenskaberne for en indsamlede post.  I denne artikel beskrives af processen for at oprette et brugerdefineret felt og giver en detaljeret gennemgang hændelsen eksempel."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-fields-in-log-analytics"></a>Brugerdefinerede felter i Log Analytics

Funktionen **Brugerdefinerede felter** i Log Analytics kan du udvide eksisterende poster i OMS lager ved at tilføje dine egne felter, der kan søges i.  Brugerdefinerede felter udfyldes automatisk fra data, der er hentet fra andre egenskaber i den samme post.

![Oversigt over brugerdefinerede felter](media/log-analytics-custom-fields/overview.png)

Eksempelvis har posten eksempel nyttige data er begravet i beskrivelsen af begivenhed.  Hente disse data i separate egenskaber gør det tilgængeligt tilgængeligt til handlinger som sortering og filtrering.

![Log knappen Søg](media/log-analytics-custom-fields/sample-extract.png)

>[AZURE.NOTE] I eksemplet skal er du begrænset til 100 brugerdefinerede felter i arbejdsområdet.  Denne grænse udvides, når denne funktion generelt tilgængelig.

## <a name="creating-a-custom-field"></a>Oprette et brugerdefineret felt

Når du opretter et brugerdefineret felt, skal Log Analytics forstå, hvilke data der skal bruge til at udfylde dens værdi.  En teknologi fra Microsoft Research kaldet FlashExtract bruges til hurtigt for at identificere disse data.  I stedet for at du behøver at angive eksplicitte instruktioner, lærer Log Analytics om de data, du vil udtrække fra eksempler, du angiver.

De følgende afsnit indeholder fremgangsmåden til at oprette et brugerdefineret felt.  Nederst i denne artikel er en gennemgang af en stikprøve udtrækning af.

> [!NOTE] Det brugerdefinerede felt er udfyldt som poster, der matcher de angivne kriterier er føjet til datalager OMS, så den vises kun på poster, der indsamles, når det brugerdefinerede felt er blevet oprettet.  Det brugerdefinerede felt kan ikke føjes til poster, der allerede findes i lageret med data, når den er oprettet.

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Trin 1 – identificere poster, der har det brugerdefinerede felt
Det første trin er at identificere de poster, der får det brugerdefinerede felt.  Du starter med en [standard log Søg](log-analytics-log-searches.md) og derefter vælge en post, der skal fungere som den model, Log Analytics lærer fra.  Når du angiver, at du går til at udtrække data i et brugerdefineret felt, åbnes **Felt udtrækning af guiden** , hvor du validerer og afgrænse kriterierne.

2. Gå til **Log Søg** , og brug en [forespørgsel til at hente posterne](log-analytics-log-searches.md) , der har det brugerdefinerede felt.
2. Vælg en post, der anvender Log Analytics skal fungere som en model til at udtrække data for at udfylde det brugerdefinerede felt.  Du kan identificere de data, du vil udtrække fra denne post, og Log Analytics bruger disse oplysninger til at bestemme logikken til at udfylde det brugerdefinerede felt for alle tilsvarende poster.
3. Klik på knappen til venstre for enhver tekstegenskab for posten, og vælg **udtrække felter fra**.
4. I **feltet udtrækning af guiden åbnes**, og den post, du har valgt vises i kolonnen **Main eksempel** .  Det brugerdefinerede felt skal være defineret for disse poster med de samme værdier i de egenskaber, der er valgt.  
5. Hvis markeringen er ikke præcis det, du vil, kan du vælge flere felter til at indskrænke kriterierne.  For at ændre feltværdierne for kriterierne, skal du annullere og vælge en anden post, der opfylder de kriterier, du vil.

### <a name="step-2---perform-initial-extract"></a>Trin 2 – udføre indledende Udpak.
Når du har identificeret de poster, der har det brugerdefinerede felt, identificerer du de data, du vil udtrække.  Log Analytics bruge disse oplysninger til at identificere lignende lignende poster.  I trin efter så vil du kunne validere resultaterne og angive yderligere oplysninger om Log Analytics til brug i analysen.

1. Markér teksten i den post, eksempel, du vil udfylde det brugerdefinerede felt.  Derefter vises der en dialogboks til at angive et navn til feltet og til at udføre den indledende Udpak.  Tegnet ** \_CF** føjes automatisk.
2. Klik på **udtrække** for at udføre en analyse af indsamlede poster.  
3. Afsnittene **Oversigt** og **Søgeresultaterne** vises resultaterne af Udpak, så du kan undersøge dens nøjagtigheden.  **Oversigt** viser de kriterier, der bruges til at identificere poster og et antal for hver af de dataværdier, der er identificeret.  **Søgeresultater** indeholder en detaljeret liste over poster opfylder kriterierne.


### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Trin 3 – bekræfte nøjagtigheden af udtrække og oprette brugerdefineret felt

Når du har udført de indledende Udpak, vises resultaterne baseret på data, der er indsamlet Log analyser.  Hvis resultatet ser nøjagtige kan du oprette det brugerdefinerede felt med uden yderligere arbejde.  Hvis ikke, derefter du kan begrænse resultaterne, så Log Analytics kan forbedre dens logik.

2.  Hvis alle værdier i den indledende Udpak ikke er korrekte, skal du klikke på ikonet **Rediger** ud for den forkerte post og vælg derefter **Rediger denne Fremhæv** for at ændre markeringen.
3.  Angivelsen kopieres til sektionen **flere eksempler** under **Main eksempel**.  Du kan justere fremhævningen her for at hjælpe Log Analytics forstå markeringen skal har foretaget.
4.  Klik på **udtrække** for at bruge denne nye oplysninger til at evaluere alle eksisterende poster.  Resultaterne kan ændres for poster bortset fra den del, du lige har ændret baseret på denne nye intelligence.
5.  Fortsætte med at tilføje rettelser, indtil alle poster i Udpak korrekt identificere dataene for at udfylde nyt brugerdefineret felt.
6. Klik på **Gem Udpak** , når du er tilfreds med resultatet.  Det brugerdefinerede felt er nu defineret, men det kan ikke føjes til alle poster endnu.
7.  Vent for nye poster, der opfylder de angivne kriterier for at indsamles og derefter køre log søgningen igen. Nye poster skal have det brugerdefinerede felt.
8.  Brug det brugerdefinerede felt som andre post egenskab.  Du kan bruge det til at samle og gruppere data og endda bruge den til at producere ny indsigt.


## <a name="viewing-custom-fields"></a>Få vist brugerdefinerede felter
Du kan få vist en liste over alle brugerdefinerede felter i din management-gruppe fra feltet **Indstillinger** af dashboardet OMS.  Vælge **Data** og **brugerdefinerede felter** for en liste over alle brugerdefinerede felter i arbejdsområdet.  

![Brugerdefinerede felter](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Fjerne et brugerdefineret felt
Der er to måder at fjerne et brugerdefineret felt.  Først er indstillingen **Fjern** for hvert felt, når du får vist den komplette liste, som beskrevet ovenfor.  Anden metoden er at hente en post, og klik på knappen til venstre for feltet.  Menuen har mulighed for at fjerne det brugerdefinerede felt.

## <a name="sample-walkthrough"></a>Eksempel gennemgang

Følgende afsnit indeholder en gennemgang en komplet eksempel på oprettelse af et brugerdefineret felt.  I dette eksempel henter navnet på tjenesten i Windows-hændelser, der angiver en tjeneste, ændring af tilstand.  Dette er afhængig af hændelser, der er oprettet ved Service Control Manager System Log på Windows-computere.  Hvis du vil følge dette eksempel, skal du være [ved at indsamle oplysninger om begivenheder til loggen System](log-analytics-data-sources-windows-events.md).

Angiv følgende forespørgsel for at returnere alle begivenheder fra Service Control Manager, som har en begivenhed ID'ET for 7036 som er den hændelse, der angiver en tjeneste starter eller stopper.

![Forespørgsel](media/log-analytics-custom-fields/query.png)

Vi derefter vælge en post med begivenhed ID 7036.

![Post i en datakilde](media/log-analytics-custom-fields/source-record.png)

Vi vil navnet på tjenesten, der vises i egenskaben **RenderedDescription** , og vælg knappen ud for denne egenskab.

![Udtrække felter](media/log-analytics-custom-fields/extract-fields.png)

**Feltet udtrækning af guiden** åbnes, og felterne **hændelseslog** og **Begivenhedid** er markeret i kolonnen **Main eksempel** .  Dette angiver, at det brugerdefinerede felt defineres ved begivenheder fra systemlogfilen med 7036 begivenhed ID'ET.  Dette er tilstrækkeligt, så vi ikke behøver at vælge andre felter.

![Primære eksempel](media/log-analytics-custom-fields/main-example.png)

Vi Fremhæv navnet på tjenesten i egenskaben **RenderedDescription** og bruge **tjenesten** til at identificere navnet på tjenesten.  Det brugerdefinerede felt, der kaldes **Service_CF**.

![Feltets overskrift](media/log-analytics-custom-fields/field-title.png)

Vi se, at navnet på tjenesten er identificeret korrekt for nogle poster, men ikke til andre.   **Søgeresultater** viser, at en del af et navn til **WMI-ydeevne kort** ikke blev markeret.  **Oversigt** viser, at fire poster med **DPRMA** service medtages forkert et ekstra ord og to poster identificeret **Installationsprogrammet moduler** i stedet for **Windows moduler Installer**.  

![Søgeresultater](media/log-analytics-custom-fields/search-results-01.png)

Vi starter med **WMI-ydeevne kort** posten.  Vi skal du klikke på dens redigeringsikonet og derefter **Rediger denne fremhævning**.  

![Ændre fremhævning](media/log-analytics-custom-fields/modify-highlight.png)

Vi øge fremhævningen for at medtage ordet **WMI** og kør derefter igen på Udpak.  

![Yderligere eksempel](media/log-analytics-custom-fields/additional-example-01.png)

Vi kan se, at posterne til **WMI-ydeevne kort** er blevet rettet, og Log Analytics også bruges disse oplysninger til at rette posterne til **Windows modul Installer**.  Selvom denne **DPMRA** ikke er stadig blive identificeret korrekt, kan vi se i sektionen **Oversigt** .

![Søgeresultater](media/log-analytics-custom-fields/search-results-02.png)

Vi Rul til en post med tjenesten DPMRA og bruge den samme fremgangsmåde til at rette denne post.

![Yderligere eksempel](media/log-analytics-custom-fields/additional-example-02.png)

 Når vi kører på udtrækning af, kan vi se, at alle vores resultater er nu nøjagtige.

![Søgeresultater](media/log-analytics-custom-fields/search-results-03.png)

Vi kan se, at **Service_CF** er oprettet, men er endnu ikke har tilføjet til poster.

![Indledende antal](media/log-analytics-custom-fields/initial-count.png)

Når er gået lidt tid så nye begivenheder, der indsamles, skal vi kan se, at feltet **Service_CF** nu blive føjet til poster, der passer til vores kriterier.

![Endelige resultater](media/log-analytics-custom-fields/final-results.png)

Vi kan nu bruge det brugerdefinerede felt som en anden post egenskab.  For at illustrere, oprette vi en forespørgsel, der grupperer efter det nye **Service_CF** felt undersøge hvilke tjenester der er de mest aktive.

![Gruppere efter forespørgsel](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at oprette forespørgsler med brugerdefinerede felter for kriterier.
- Overvåge [brugerdefinerede logfiler](log-analytics-data-sources-custom-logs.md) , som du fortolker ved hjælp af brugerdefinerede felter.