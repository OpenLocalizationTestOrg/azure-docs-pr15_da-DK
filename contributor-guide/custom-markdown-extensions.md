<properties
    title="required"
    pageTitle="Brugerdefineret tabsbeløb udvidelser, som bruges i vores tekniske artikler"
    description="Viser de brugerdefinerede tabsbeløb udvidelser, der aktiverer integrerede videoer, noter og tip, der kan genbruges indhold og andre elementer i azure.microsoft.com tekniske artikler."
    services=""
    solutions=""
    documentationCenter=""
    authors="tysonn"
    manager="carolz"
    editor=""/>

<tags
    ms.service="contributor-guide"
    ms.devlang=""
    ms.topic="article"
    ms.tgt_pltfrm=""
    ms.workload=""
    ms.date="01/22/2015"
    ms.author="tysonn"/>

## <a name="markdown-for-azuremicrosoftcom"></a>Tabsbeløb for Azure.microsoft.com

Generelle tabsbeløb tip finder du under [Tabsbeløb grundlæggende](https://help.github.com/articles/markdown-basics/) og vores [tabsbeløb cheatsheet](./media/documents/markdown-cheatsheet.pdf?raw=true). Hvis du vil oprette artikel crosslinks i tabsbeløb skal se [sammenkæde vejledning] (. / create-links-markdown.md#markdown-syntax-for-acom-relative-links.md/).

Azure.Microsoft.com understøtter [indhegnet kode dokumentkomponenter](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks) og [syntaksen fremhævning](https://help.github.com/articles/github-flavored-markdown/#syntax-highlighting). ACOM understøtter dog kun én syntaksen fremhævning farveskema, uanset det sprog, du angiver i en kodeblok.

## <a name="custom-markdown-extensions-used-in-our-technical-articles"></a>Brugerdefineret tabsbeløb udvidelser, som bruges i vores tekniske artikler

Vores artikler Brug GitHub flavored tabsbeløb til de fleste artikel formatering - afsnit, links, lister, overskrifter, osv. Men vi bruge brugerdefinerede tabsbeløb filtypenavne, hvor vi har brug for mere omfattende formatering på siderne gengivne på azure.microsoft.com. Her er de udvidelser, vi bruger i øjeblikket:

+ [Noter og tip]
+ [Omfatter]
+ [Integrerede videoer]
+ [Teknologi og platform vælgere]

## <a name="notes-and-tips"></a>Noter og tip

Du kan vælge mellem 4 typer af noter og tip:

- AZURE. BEMÆRK!
- AZURE. ADVARSEL
- AZURE. TIPss
- AZURE. VIGTIGE

###<a name="usage"></a>Brugen
Brug Generelt, noter og tip moderat i hele din artikler. Når du bruger dem, kan du vælge den ønskede type note eller tip:

- Brug AZURE. Bemærk Hvis du vil fremhæve neutrale eller positive oplysninger, der fremhæver eller supplerer vigtige punkter i hovedteksten. En note leverer oplysninger, der gælder kun i særlige tilfælde.

  ![](./media/custom-markdown-extensions/Notes-note.PNG)

- Brug AZURE. Advarsel om at give besked om brugeren en betingelse, der kan medføre problemer i fremtiden. For eksempel kan at vælge en bestemt indstilling eller foretage et bestemt valg permanent låse du til et bestemt scenarie.

  ![](./media/custom-markdown-extensions/Notes-warning.PNG)

- Brug AZURE. TIP til at hjælpe brugerne med at anvende teknikker og procedurerne i teksten til deres særlige behov. Et tip kan også foreslå alternative metoder, som ikke muligvis indlysende. Tip til, er dog ikke væsentlige til den grundlæggende forståelse af teksten.

  ![](./media/custom-markdown-extensions/Notes-tip.PNG)

- Brug AZURE. VIGTIGE oplysninger, der er vigtige for at færdiggøre en opgave.

  ![](./media/custom-markdown-extensions/Notes-important.PNG)

Mens disse noter og tip, der understøtter kodeblokke, billeder, lister og links, kan du forsøge at holde dine noter og tip enkle og nemt. Hvis du finder selv oprette komplekse bemærkninger med masser af formatering, skal være, logger du bare et andet afsnit i hovedteksten i artiklen. Og for mange noter i en artikel, der kan være distraherende og svær at scanne eller Læs.

###<a name="sample-markdown"></a>Eksempel tabsbeløb

Alle Eksemplerne viser en AZURE. BEMÆRK!. Hvis du vil bruge et TIP, advarsel eller vigtigt, at erstatte "NOTE" i tabsbeløb:

    > [AZURE.TIP]

    > [AZURE.WARNING]

    > [AZURE.IMPORTANT]

Enkelt afsnit:

    > [AZURE.NOTE] For at fuldføre dette selvstudium, skal du have en aktiv Microsoft Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter.

Multiparagraph:

    > [AZURE.NOTE] For at fuldføre dette selvstudium, skal du have en aktiv Microsoft Azure-konto.
    >
    > Hvis du ikke har en konto, kan du [oprette en gratis prøveversion konto](http://www.windowsazure.com/pricing/free-trial/) på blot et par minutter.

## <a name="includes"></a>Omfatter

Der kan genbruges tekst i vores GitHub lager er placeret i filer, som vi kalder "omfatter". Når du har tekst, der skal bruges i flere artikler, kan du medtage en reference til den pågældende fil, der kan genbruges oplysninger. Afkrydsningsfeltet Medtag selve er en simpel tabsbeløb (.md)-fil. Det kan indeholde en hvilken som helst gyldigt tabsbeløb, herunder tekst, links og billeder. Alle medtage tabsbeløb filer skal være i [den / indeholder biblioteket](https://github.com/Azure/azure-content/tree/master/includes) i roden af lageret. Når artiklen udgives, integreret Medtag tekst problemfrit i emnet publiceret.

- Vi bruger en bestemt syntaks til at referere til en Medtag.

- Du placerer i en Medtag mediefiler skal oprettes i en mappe, media specifikke for afkrydsningsfeltet Medtag. Mediemapper, så de omfatter medlem i [mappen azure-/ omfatter/medier med indhold](https://github.com/Azure/azure-content/tree/master/includes/media). Mappen media må ikke indeholde alle billeder i roden. Hvis afkrydsningsfeltet Medtag ikke har billeder, derefter er en tilsvarende media-mappe ikke påkrævet.

###<a name="usage"></a>Brugen

- Brug omfatter det sted, hvor du har brug for den samme tekst, der vises i flere artikler.

- Omfatter er beregnet til at blive brugt for store mængder indhold - et afsnit eller to, en delt procedure eller en delt sektion. Kan ikke bruge dem til noget, der er mindre end en sætning **de er ikke for produktnavne**.

- Kontrollér, at alle teksten i en Medtag skrives i hele sætninger eller udtryk, der ikke afhænger af foregående tekst eller følgende tekst i artiklen, der refererer til afkrydsningsfeltet Medtag. Ignorere denne vejledning opretter en untranslatable streng i artiklen, der opdeler den oversatte oplevelse. 

- Integrer ikke omfatter i andre omfatter. De understøttes ikke af DPS publicering system.

- Ikke dele medier mellem filer. Bruge en separat fil med et entydigt navn for hver Medtag og artikel. Gemme mediefilen i mappen medier, der er knyttet til afkrydsningsfeltet Medtag.

- Brug ikke en Medtag som kun indholdet af en artikel.  Omfatter er beregnet til at være supplerende til indhold i resten af artiklen.

- Fordi alle indeholder skal være i den / indeholder biblioteket, at stien til en Medtag fra en artikel er altid

    .. / indeholder

- Gentag ikke et link eller billede filnavn reference i både i artiklen og afkrydsningsfeltet Medtag. Tilføje "-medtage" til linket reference eller medier filnavn for at undgå gentagne referencen:

 **Sammenkædning**

 Skift: odata.org til: odata.org – Medtag

 **Billede af reference**

 Skift: table.png til: tabel-include.png

###<a name="sample-markdown"></a>Eksempel tabsbeløb
Syntaksen for at tilføje en Medtag til en artikel til dokumentation er:

    [AZURE.INCLUDE [include-short-name](../includes/include-file-name.md)]

Eksempel

    [AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Den første del af afkrydsningsfeltet Medtag er Medtag navnet uden stien og uden filtypenavnet .md. Den anden del er den relative sti til afkrydsningsfeltet Medtag på den / indeholder biblioteket med filtypenavnet .md.

###<a name="rendering"></a>Gengivelse

På siden gengivne GitHub gengive afkrydsningsfeltet Medtag på følgende måde:

 [AZURE. MEDTAGE Sådan-blob-lager]

I gengivne HTML på azure.microsoft.com, HTML-koden fra den indeholder er flettet ind i resten af dokumentets HTML. Men HTML-koden skal indeholde en HTML kommentar med oprindelige omfatter tabsbeløb filnavn og giver GitHub Anvend resultat. Denne kommentar er inkluderet i forbindelse med fejlfinding, så det kilde indhold kan nemt identificeret og findes i GitHub:

  ![](./media/custom-markdown-extensions/include.png)


## <a name="embedded-videos"></a>Integrerede videoer

Vores tekniske artikler understøtter embeddeded videoer i tekniske artikler, så længe videoerne, der er på Microsofts [kanal 9](http://channel9.msdn.com/) websted. Videoer fra kanal 9 skal integreres med [azure.microsoft.com Video Center](http://azure.microsoft.com/documentation/videos/home/). Vi understøtter i øjeblikket ikke integreret YouTube-videoer; Hvis du er en community bidragyder, er du Velkommen til at sammenkæde på YouTube, hvis den video, du vil vise der skrives der. Microsoft bidragydere skal bruge kanal 9 og Video Center.

### <a name="usage"></a>Brugen

- Sørg for, at videoen er på Video Center.

- Kopiere video-ID, fra den fulde URL-adressen til videoen på kanal 9 eller fra Azure Video Center. For eksempel er det video-ID til videoen på [http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/](http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/) **azure-scheduler-usædvanlige-tidsplaner**.

### <a name="syntax"></a>Syntaksen for

    > [AZURE.VIDEO video-id-string]

### <a name="rendering"></a>Gengivelse

Om GitHub: [https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md](https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md)

Udgivet artikel: [http://azure.microsoft.com/documentation/articles/web-sites-backup/](http://azure.microsoft.com/documentation/articles/web-sites-backup/)


## <a name="technology-and-platform-selectors"></a>Teknologier og platforme vælgere

Brug teknologi og platform switchers i tekniske artikler, når du opretter flere typer af samme artiklen til adresse forskelle i implementering på tværs af teknologier eller platforme. Dette er typisk mest relevante til vores mobil platform indhold for udviklere. Der findes to forskellige typer vælgere, [enkle vælgere](#simple-selectors) og [tovejs vælgere](#two-way-selectors).

Da den samme Datavælger tabsbeløb vises i hvert emne i markerede, anbefaler vi at placere Datavælger til dit emne i en Medtag, og klik derefter refererer til Medtag i alle dine emner, der bruger den samme Datavælger.

###<a id="simple-selectors"></a>Enkel vælgere

Enkel (envejs) vælgere gengives som et sæt af alternativknapper lige under titlen. Brug disse knapper, når kunder har brug at vælge mellem emner i en enkelt platform eller teknologi sæt, som .NET, Node.js og Java.  Bruger filtypenavnet brugerdefinerede tabsbeløb til en hvilken som helst vælgere – Brug ikke HTML til vælgere.  

Se [komme i gang med besked om Hubs](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started/) for at se, hvordan forfatteren oprettet 8 versioner af samme artikel, men anvendte vælgere til at aktivere navigation på tværs af dem alle.

![Enkel Datavælger eksempel](./media/custom-markdown-extensions/selectors.PNG)

####<a name="syntax"></a>Syntaksen for

    > [AZURE.SELECTOR]
    - [Sammenkæde #1 etiket](link #1 url)
    - [Link #2 etiket](link #2 url)

Eksempel:

    > [AZURE.SELECTOR]
    - [Universal Windows](../articles/notification-hubs-windows-store-dotnet-get-started/)
    - [Windows Phone](../articles/notification-hubs-windows-phone-get-started/)
    - [iOS](../articles/notification-hubs-ios-get-started/)
    - [Android](../articles/notification-hubs-android-get-started/)
    - [Kindle](../articles/notification-hubs-kindle-get-started/)
    - [Baidu](../articles/notification-hubs-baidu-get-started/)
    - [Xamarin.iOS](../articles/partner-xamarin-notification-hubs-ios-get-started/)
    - [Xamarin.Android](../articles/partner-xamarin-notification-hubs-android-get-started/)

#### <a name="rendering"></a>Gengivelse

Billedet ovenfor viser gengivelsen på azure.microsoft.com. Vælgerne gengives som en opstilling med punkttegn liste over links på siderne gengivne GitHub.

###<a id="two-way-selectors"></a>Tovejs vælgere

Tovejs vælgere kan brugerne med at vælge en emner fra et tovejs-matrix. Det er nødvendigt, når en Azure-teknologi, som Mobile-tjenester, understøtter flere back end-platforme samt flere klienter. Husk på følgende:

- Mens det er designet som `(Platform | Backend)`, dropwdown teksten kan nu tilpasses.
- Du behøver ikke et listeelement for hvert punkt i matrixen, men kun har et element, hvor en emne URL-adresse findes, og ikke er en dublet.
- Linket kan være alle URL-Adresser, selvom det er normalt et andet GitHub emne.

Se [Introduktion til Mobile-tjenester](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/) til at se, hvordan forfatteren oprettet 15 versioner af samme artikel (9 mobilklient platforme og 2 back end-platforme), men anvendte vælgere til at aktivere navigation på tværs af dem alle. Bemærk, at 3 artikler ikke begge back end-versioner.

![Eksempel på tovejs vælgere](./media/custom-markdown-extensions/selector-list.png)

####<a name="syntax"></a>Syntaksen for

    > [AZURE. DATAVÆLGER-liste (Dropdown1 | Dropdown2)]     -  [(Dropdown1Text1 | Dropdown2Text1)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text1 | Dropdown2Text2)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text2 | Dropdown2Text3)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text3 | Dropdown2Text4)](../articles/dropdown1-text1-dropdown2-text1.md)

Eksempel:

    > [AZURE. DATAVÆLGER-liste (Platform | Backend-version)]     -  [(iOS | .NET)](./mobile-services-dotnet-backend-ios-get-started-push.md)
    - [(iOS | JavaScript)](./mobile-services-javascript-backend-ios-get-started-push.md)
    - [(Windows universal C# | .NET)](./mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows universal C# | JavaScript)](./mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows Phone | .NET)](./mobile-services-dotnet-backend-windows-phone-get-started-push.md)
    - [(Windows Phone | JavaScript)](./mobile-services-javascript-backend-windows-phone-get-started-push.md)
    - [(Android | .NET)](./mobile-services-dotnet-backend-android-get-started-push.md)
    - [(Android | JavaScript)](./mobile-services-javascript-backend-android-get-started-push.md)
    - [(Xamarin iOS | JavaScript)](./partner-xamarin-mobile-services-ios-get-started-push.md)
    - [(Xamarin Android | JavaScript)](./partner-xamarin-mobile-services-android-get-started-push.md)

#### <a name="rendering"></a>Gengivelse

Billedet ovenfor viser gengivelsen på azure.microsoft.com. Vælgerne gengives som en opstilling med punkttegn liste over links på siderne gengivne GitHub.

<!--Anchors-->
[Noter og tip]: #notes-and-tips
[Omfatter]: #includes
[Integrerede videoer]: #embedded-videos
[Teknologi og platform vælgere]: #technology-and-platform-selectors

###<a name="contributors-guide-links"></a>Bidragydere vejledning Links

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)
