<properties 
    pageTitle="Sådan bruger du vundne profiler i Azure Søg | Microsoft Azure | Hostet skyen search-tjenesten" 
    description="Finjustere søgerelevans gennem pointsystemet profiler i Azure søgning, en hostet skyen søgetjenesten på Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Sådan bruges vurdering profiler i Azure søgning

Vurdering profiler er en funktion i Microsoft Azure-søgning, tilpasses beregningen af pointtal, søgning, har indflydelse på Sådan klassificeres elementer i en liste med søgeresultater. Du kan betragte vundne profiler som en metode til model relevans ved øger elementer, der opfylder foruddefinerede kriterier. Lad os antage, at dit program er et online hotel reservation websted. Ved øger det `location` felt, søgninger, der indeholder et udtryk som Seattle medfører højere pointtal for elementer, der har Seattle i den `location` felt. Bemærk, at du kan have mere end én vurdering profil eller ingen overhovedet, hvis standard vundne er tilstrækkelige til dit program.

For at hjælpe dig med at eksperimentere med vurdering profiler, kan du hente et eksempelprogram, der bruger vurdering profiler til at ændre plads rækkefølgen af søgeresultaterne. Prøven er et console-program – måske ikke meget realistisk for udvikling af reale programmer – men nyttige noget som et learning værktøj. 

Eksempelprogrammet demonstrerer vurdering funktionsmåder ved hjælp af fiktive data, kaldes det `musicstoreindex`. Enkel af eksempel app gør det nemt at ændre vurdering profiler og forespørgsler, og Læs derefter øjeblikkelig virkningen på plads rækkefølge, når programmet udføres.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Forudsætninger

Eksempelprogrammet er skrevet i C# ved hjælp af Visual Studio-2013. Prøv de gratis [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) , hvis du ikke allerede har en kopi af Visual Studio.

Du skal bruge et Azure-abonnement og en Azure søgetjenesten til at fuldføre selvstudiet. Se [oprette en søgetjeneste i portalen](search-create-service-portal.md) for at få hjælp med at konfigurere tjenesten.

[AZURE. MEDTAGE [du har brug for en Azure-konto til at udføre dette selvstudium:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Hente eksempelprogrammet

Gå til [Azure Søg vundne profiler Demo](https://azuresearchscoringprofiles.codeplex.com/) på codeplex hente eksempelprogrammet, der er beskrevet i dette selvstudium.

Klik på **Hent** for at få en zip-fil løsningens under fanen kildekode. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Redigere app.config

1. Når du udtrække filerne, skal du åbne løsningen i Visual Studio til at redigere konfigurationsfilen.
1. I Solution Explorer skal du dobbeltklikke på **app.config**. Denne fil angiver tjenesteslutpunkt og et `api-key` bruges til at godkende anmodningen. Du kan få vist disse værdier fra portalen klassisk.
1. Log på [Azure-portalen](https://portal.azure.com).
1. Gå til tjenesten dashboard til Azure søgning.
1. Klik på feltet **Egenskaber** for at kopiere URL-adressen
1. Klik på feltet **taster** til at kopiere den `api-key`.

Når du er færdig med at tilføje URL-adressen og `api-key` til app.config, programindstillinger ser sådan ud:

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Udforske programmet

Du er næsten klar til at oprette og køre app, men før du gør, se nærmere på de JSON-filer, der bruges til at oprette og udfylde indekset.

**Schema.JSON** definerer det indeks, herunder de vurdering profiler, der er fremhævet i denne demo. Meddelelse om, at skemaet definerer alle de felter, der bruges i indekset, herunder ikke kan søges felter, som `margin`, der kan bruges i en vurdering profil. Vundne profil syntaks er beskrevet i [Tilføj en vurdering profil til en Azure-søgeindekset](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Fil1 3.json** indeholder dataene, 246 album på tværs af en håndfuld genrer. Data, der er en kombination af faktiske album og kunstner oplysninger, med fiktivt felter som `price` og `margin` bruges til at illustrere søgehandlinger. Datafiler svarer til indekset og er overført til din Azure Search-tjenesten. Når dataene er overført og indekseret, kan du stille spørgsmål i forhold til den.

**Program.CS** udfører følgende handlinger:

- Åbner console.

- Opretter forbindelse til Azure søgning ved hjælp af URL-adressen og `api-key`.

- Sletter den `musicstoreindex` Hvis den findes.

- Opretter en ny `musicstoreindex` ved hjælp af schema.json-fil.

- Udfylder indekset ved hjælp af datafilerne.

- Forespørgsler ved hjælp af fire forespørgsler indekset. Bemærk, at de vurdering profiler er angivet som en parameter i forespørgslen. Alle forespørgslerne søge efter det samme ord 'bedst'. Den første forespørgsel demonstrerer standard vundne. De resterende tre forespørgsler bruger en vurdering profil.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Oprette og køre programmet

Hvis du vil udelukke forbindelse eller samling reference problemer, opbygge og køre programmet for at sikre, at der er ingen problemer til at arbejde ud første. Du bør se et console-program, der er åbne i baggrunden. Alle fire forespørgsler udføres i rækkefølge uden at holde pause. Mange systemer afvikles hele programmet på under 15 sekunder. Hvis programmet console omfatter en meddelelse om "færdigt. Tryk på enter for at fortsætte med", det program, der er fuldført. 

Hvis du vil sammenligne forespørgslen køres, du kan markere kopiere indsætte forespørgselsresultaterne fra konsollen og sætter dem ind i en Excel-fil. 

Følgende illustration viser resultater fra de første tre forespørgsler side om side. Alle forespørgslerne bruger den samme søgeord 'bedst', som vises i flere albumtitler.

   ![][10]

Den første forespørgsel bruger standard vundne. Eftersom søgeordet vises kun i albumtitler, og ikke andre kriterier er angivet, returneres elementer, der har 'bedst' i albummets titel i den rækkefølge, hvori søgetjenesten finder dem. 

Den anden forespørgsel bruger en vurdering profil, men Bemærk, at profilen havde ingen effekt. Resultaterne er identisk med den første forespørgsel. Dette skyldes, at profilen vurdering øger et felt (genre), der ikke er germane til forespørgslen. Søgeordet "de bedste" findes ikke i en hvilken som helst 'genre' felt med et dokument. Når en vurdering profil har ingen virkning, er den samme som standard vundne resultaterne.  

Den tredje forespørgsel er det første tegn på vundne profil virkning. Søgeordet er stadig "de bedste", så vi arbejder sammen med det samme sæt album, men fordi profilen vurdering indeholder flere kriterier, der øger 'bedømmelse' og 'sidst opdaterede' nogle elementer er hævet kommunikation højere på listen.

Den næste illustration viser den fjerde og endelige forespørgsel, øget af "margen". Feltet "margen" er ikke kan søges og kan ikke returneres i søgeresultater. Margenværdien "" blev føjet til regneark for at illustrere det punkt, der varer med højere margener vises højere oppe på listen over søgeresultater manuelt. 

   ![][9]

Nu hvor du har experimented med vurdering profiler, prøv at ændre programmet, der skal bruges forskellige forespørgselssyntaksen vundne profiler eller bedre data. Links i næste afsnit indeholder flere oplysninger.

<a id="next-steps"></a>
## <a name="next-steps"></a>Næste trin

Lær mere om vundne profiler. Få mere at vide, skal du se [tilføje en vurdering profil til en Azure-søgeindekset](http://msdn.microsoft.com/library/azure/dn798928.aspx) .

Lær mere om søgeparametre syntaksen og forespørgsel. Du kan finde oplysninger i [Søg i dokumenter (Azure Search REST-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Brug for at gå tilbage og lære mere om oprettelse af indeks? [Se denne video](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) for at forstå de grundlæggende funktioner.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 