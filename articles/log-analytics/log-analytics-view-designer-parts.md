<properties
    pageTitle="Log Analytics visning Designer | Microsoft Azure"
    description="Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data i OMS-lager. Denne artikel indeholder en reference af indstillingerne for hver af de tilgængelige til brug i dine brugerdefinerede visninger visualisering dele."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-visualization-part-reference"></a>Log Analytics visning Designer visualisering del referencen
Vis Designer i Log Analytics gør det muligt at oprette brugerdefinerede visninger i konsollen OMS, der indeholder forskellige visualiseringer af data fra OMS-lager. Denne artikel indeholder en reference af indstillingerne for hver af de tilgængelige til brug i dine brugerdefinerede visninger visualisering dele.

Andre artikler tilgængelige til visning Designer er:

- [Vis Designer](log-analytics-view-designer.md) - oversigt over de Designer visninger og procedurerne for at oprette og redigere brugerdefinerede visninger.
- [Feltet reference](log-analytics-view-designer-tiles.md) - referencen til indstillingerne for hver af de felter, der er tilgængelige til brug i dine brugerdefinerede visninger. 

I følgende tabel beskrives de forskellige typer felter, der er tilgængelige i visningen Designer.  I nedenstående afsnit beskrives hver felttype i detaljer og deres egenskaber.

| Visningstype | Beskrivelse |
|:--|:--|
| [Liste over forespørgsler](#list-of-queries-part) | Viser en liste over log søgeforespørgsler.  Brugeren kan klikke på hver forespørgsel for at få vist resultaterne.  |
| [Tal og liste](#number-amp-list-part) | Sidehoved har en enkelt tal viser antallet af poster fra en log søgeforespørgsel.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid. |
| [To tal og liste](#two-numbers-amp-list-part) | Sidehoved har to tal, der viser antallet af poster fra separate log søgeforespørgsler.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid. |
| [Krans og liste](#donut-amp-list-part) | Sidehoved viser et enkelt tal, der er opsummeret fra en værdikolonne i en log forespørgsel.  Krans viser grafisk resultaterne af de øverste tre poster. |
| [To tidslinjer og liste](#two-timelines-amp-list-part) | Sidehoved viser resultaterne af to log forespørgsler over tid som søjlediagrammer med en billedforklaring, der viser en enkelt tal, der er opsummeret fra en værdikolonne i en log forespørgsel.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid. |   
| [Du kan finde oplysninger](#information-part) | Sidehoved vises statisk tekst og et valgfrit link.  Liste viser en eller flere elementer med statisk tekst og titel. |
| [Kurvediagram, billedforklaring og liste](#line-chart-callout-amp-list-part) | Sidehoved viser et kurvediagram med flere serier fra en forespørgsel, log over tid og en billedforklaring med en værdi.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid. |
| [Kurvediagram og liste](#line-chart-amp-list-part) | Sidehoved viser et kurvediagram med flere serier fra en forespørgsel, log over tid.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid. |
| [Stak af linje diagrammer del](#stack-of-line-charts-part) | Viser tre separate kurvediagrammer med flere serier fra en forespørgsel, log over tid. |


## <a name="list-of-queries-part"></a>Liste over forespørgsler del

Viser en liste over log søgeforespørgsler.  Brugeren kan klikke på hver forespørgsel for at få vist resultaterne.  Visningen omfatter en enkelt forespørgsel som standard, og du kan klikke på **+ forespørgsel** for at tilføje flere forespørgsler.

![Liste over forespørgsler visning](media/log-analytics-view-designer/view-list-queries.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Titel | Tekst, der skal vises øverst i visningen. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Valgt på forhånd filtre | Kommasepareret liste over egenskaber, der skal medtages i venstre filterruden, når brugeren markerer en forespørgsel. |
| Gengive tilstand | Indledende visning, der vises, når forespørgslen er markeret.  Brugeren kan markere alle tilgængelige visninger, når du har åbnet forespørgslen. |
| **Forespørgsler** |
| Søgeforespørgsel | Forespørgsel til at køre. |
| Fulde navn | Beskrivende navn til forespørgslen skal vises for brugeren. |


## <a name="number--list-part"></a>Tal & list del

Sidehoved har en enkelt tal viser antallet af poster fra en log søgeforespørgsel.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid.


![Liste over forespørgsler visning](media/log-analytics-view-designer/view-number-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i visningen. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet.
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Titel** |
| FORKLARING | Tekst, der skal vises øverst i sidehovedet. |
| Forespørgsel | Forespørgsel til at køre i sidehovedet.  Antallet af antallet poster returneres i forespørgslen vises. |
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  De første to egenskaber for de første ti poster i resultaterne vises.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Søjler oprettes automatisk baseret på den relative værdi af den numeriske kolonne.<br><br>Brug kommandoen Sortér i forespørgslen til at sortere poster på listen.  Brugeren kan klikke på se alle for at køre forespørgslen og returnere alle poster. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Navn og værdi Separator | Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben i flere værdier.  Du kan finde oplysninger i [Almindelige indstillinger](#name-value-separator) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |


## <a name="two-numbers--list-part"></a>To tal og listen del

Sidehoved har to tal, der viser antallet af poster fra separate log søgeforespørgsler.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid.

![To tal og listevisning](media/log-analytics-view-designer/view-two-numbers-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i visningen. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet.
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Titel** |
| FORKLARING | Tekst, der skal vises øverst i sidehovedet. |
| Forespørgsel | Forespørgsel til at køre i sidehovedet.  Antallet af antallet poster returneres i forespørgslen vises. |
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  De første to egenskaber for de første ti poster i resultaterne vises.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Søjler oprettes automatisk baseret på den relative værdi af den numeriske kolonne.<br><br>Brug kommandoen Sortér i forespørgslen til at sortere poster på listen.  Brugeren kan klikke på se alle for at køre forespørgslen og returnere alle poster. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Handling | Handling, der skal udføres for minidiagrammet.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Navn og værdi Separator | Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben i flere værdier.  Du kan finde oplysninger i [Almindelige indstillinger](#name-value-separator) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |

## <a name="donut--list-part"></a>Krans & list del

Sidehoved viser et enkelt tal, der er opsummeret fra en værdikolonne i en log forespørgsel.  Krans viser grafisk resultaterne af de øverste tre poster.

![Visningen kranse & liste](media/log-analytics-view-designer/view-donut-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet. |
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Sidehoved** |
| Titel | Tekst, der skal vises øverst i sidehovedet.
| Undertitel | Tekst, der vises under overskriften øverst på hovedet.
| **Krans** |
| Forespørgsel | Forespørgslen køres til krans.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi. |
| **Krans** |  **> Center** |
| Tekst | Tekst, der vises under værdien i krans. |
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi.<br><br>-Sum: Addere værdierne af alle poster.<br>-Procentdel: Procentdel af de poster, der returneres af værdierne i **resultatværdier, der bruges i center operation** til samlede poster i forespørgslen. |
| Resultatværdier, der bruges i center operation | Du kan også klikke på plustegnet for at tilføje en eller flere værdier.  Resultaterne af forespørgslen begrænses til poster med de egenskabsværdier, du angiver.  Hvis der er tilføjet nogen værdier, medtages alle poster i forespørgslen. |
| **Flere indstillinger** | **> Farver** |
| Farve 1<br>Farve 2<br>Farve 3 | Vælg farven for hver af de værdier, der vises i krans. |
| **Flere indstillinger** | **> Avancerede farveskema** |
| Feltværdien | Skriv navnet på et felt til at få den vist som en anden farve, hvis den er indeholdt i krans. |
| Farve | Vælg farven i feltet entydigt. |
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  Antallet af antallet poster returneres i forespørgslen vises. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Handling | Handling, der skal udføres for minidiagrammet.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Navn og værdi Separator | Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben i flere værdier.  Du kan finde oplysninger i [Almindelige indstillinger](#name-value-separator) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |

## <a name="two-timelines--list-part"></a>To tidslinjer & list del

Sidehoved viser resultaterne af to log forespørgsler over tid som søjlediagrammer med en billedforklaring, der viser en enkelt tal, der er opsummeret fra en værdikolonne i en log forespørgsel.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid.

![Visningen to tidslinjer og liste](media/log-analytics-view-designer/view-two-timelines-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet. |
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Først diagram<br>andet diagram** |
| FORKLARING | Tekst, der vises under boblen for den første serie. |
| Farve | Farve skal bruges til kolonnerne i serien. |
| Forespørgsel | Forespørgslen køres til den første serie.  Antallet af antallet poster over hvert tidsinterval repræsenteres af kolonnerne diagram. |
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi for billedforklaringen.<br><br>-Sum: Summen af værdien fra alle poster.<br>-Gennemsnit: Gennemsnittet af værdien fra alle poster.<br>-Sidste eksempel: Værdi fra det sidste interval, der er inkluderet i diagrammet.<br>-Første eksempel: Værdi fra den første interval, der er inkluderet i diagrammet.<br>-Antal: Antallet af alle poster returneres i forespørgslen.|
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  Antallet af antallet poster returneres i forespørgslen vises. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Handling | Handling, der skal udføres for minidiagrammet.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |

## <a name="information-part"></a>En del oplysninger

Sidehoved vises statisk tekst og et valgfrit link.  Liste viser en eller flere elementer med statisk tekst og titel.

![Få vist oplysninger](media/log-analytics-view-designer/view-information.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Farve | Baggrundsfarve for sidehovedet. |
| **Sidehoved** |
| Billede af | Den billedfil, der skal vises i sidehovedet. |
| Etiket | Tekst, der skal vises i sidehovedet. |
| **Sidehoved** | **> Link** |
| Etiket | Linktekst. |
| URL-adresse | URL-adressen for et link til. |
| **Dataelementer** |
| Titel | Tekst, der vises for titlen på hvert element. |
| Indhold | Tekst, der skal vises for hvert element. |


## <a name="line-chart-callout--list-part"></a>Kurvediagram, billedforklaring og listen del

Sidehoved viser et kurvediagram med flere serier fra en forespørgsel, log over tid og en billedforklaring med en værdi.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid.

![Kurvediagram, billedforklaring og listevisning](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet. |
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Sidehoved** |
| Titel | Tekst, der skal vises øverst i sidehovedet. |
| Undertitel | Tekst, der vises under overskriften øverst på hovedet. |
| **Kurvediagram** |
| Forespørgsel | Forespørgsel til at køre til kurvediagram.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater.  Hvis forespørgslen bruger nøgleordet **interval** brug x-aksen i diagrammet dette tidsinterval.  Hvis forespørgslen ikke inkluderer nøgleordet **interval** bruges hver time intervaller til x-aksen. |
| **Kurvediagram** | **> Billedforklaring** |
| Billedforklaring til en titel | Tekst, der skal vises over billedforklaring værdi. |
| Serienavn | Egenskabsværdi for serien skal bruges til værdien billedforklaring.  Hvis ingen serie ikke er angivet, bruges alle poster fra forespørgslen. |
| Handling | Handlingen, der skal udføres på egenskaben value til at opsummere til en enkelt værdi for billedforklaringen.<br><br>-Gennemsnit: Gennemsnittet af værdien fra alle poster.<br>-Tælle antallet af alle poster returneres i forespørgslen.<br>-Sidste eksempel: Værdi fra det sidste interval, der er inkluderet i diagrammet.<br>-Maks: Maksimumværdi fra de intervaller, der er inkluderet i diagrammet.<br>-Min: Minimumværdi fra de intervaller, der er inkluderet i diagrammet.<br>-Sum: Summen af værdien fra alle poster. |
| **Kurvediagram** | **> Y-aksen** |
| Brug logaritmisk skala | Vælg for at bruge en logaritmisk skala for y-aksen. |
| Enheder | Angive enhederne for de værdier, der returneres af forespørgslen.  Disse oplysninger bruges til at vise etiketter på det diagram, der angiver værdityperne og eventuelt til konvertering af værdierne.  En enhedstype angiver, hvilken kategori af enheden og definerer de aktuelle enhedstype værdier, der er tilgængelige.  Hvis du vælger en værdi i konvertere til, og derefter de numeriske værdier konverteres fra den aktuelle enhed type på Konvertér til at skrive. |
| Brugerdefineret etiket | Tekst, der vises for Y-aksen ud for etiketten for enhedstypen.  Hvis ingen etiket ikke er angivet, vises kun enhedstype. |
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  Antallet af antallet poster returneres i forespørgslen vises. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Handling | Handling, der skal udføres for minidiagrammet.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Navn og værdi Separator | Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben i flere værdier.  Du kan finde oplysninger i [Almindelige indstillinger](#name-value-separator) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |

## <a name="line-chart--list-part"></a>Linje diagram & list del

Sidehoved viser et kurvediagram med flere serier fra en forespørgsel, log over tid.  Liste viser de øverste ti resultater fra en forespørgsel med en graf, der angiver den relative værdi af en numerisk kolonne eller dens ændring over tid.

![Få vist linje diagram og liste](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet. |
| Brug ikonet | Vælg for at lade visningen af ikonet. |
| **Sidehoved** |
| Titel | Tekst, der skal vises øverst i sidehovedet. |
| Undertitel | Tekst, der vises under overskriften øverst på hovedet. |
| **Kurvediagram** |
| Forespørgsel | Forespørgsel til at køre til kurvediagram.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater.  Hvis forespørgslen bruger nøgleordet **interval** brug x-aksen i diagrammet dette tidsinterval.  Hvis forespørgslen ikke inkluderer nøgleordet **interval** bruges hver time intervaller til x-aksen. |
| **Kurvediagram** | **> Y-aksen** |
| Brug logaritmisk skala | Vælg for at bruge en logaritmisk skala for y-aksen. |
| Enheder | Angive enhederne for de værdier, der returneres af forespørgslen.  Disse oplysninger bruges til at vise etiketter på det diagram, der angiver værdityperne og eventuelt til konvertering af værdierne.  En enhedstype angiver, hvilken kategori af enheden og definerer de aktuelle enhedstype værdier, der er tilgængelige.  Hvis du vælger en værdi i konvertere til, og derefter de numeriske værdier konverteres fra den aktuelle enhed type på Konvertér til at skrive. |
| Brugerdefineret etiket | Tekst, der vises for Y-aksen ud for etiketten for enhedstypen.  Hvis ingen etiket ikke er angivet, vises kun enhedstype. |
| **Liste** |
| Forespørgsel | Forespørgslen køres til listen.  Antallet af antallet poster returneres i forespørgslen vises. |
| Skjule graf | Vælg for at deaktivere diagrammet til højre for den numeriske kolonne. |
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Farve | Farven på søjlerne eller minidiagrammer. |
| Handling | Handling, der skal udføres for minidiagrammet.  Du kan finde oplysninger i [Almindelige indstillinger](#sparklines) . |
| Navn og værdi Separator | Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben i flere værdier.  Du kan finde oplysninger i [Almindelige indstillinger](#name-value-separator) . |
| Navigation forespørgsel | Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Du kan finde oplysninger i [Almindelige indstillinger](#navigation-query) . |
| **Liste** | **> Kolonnetitler** |
| Navn | Tekst, der skal vises øverst i den første kolonne på listen. |
| Værdi | Tekst, der skal vises øverst i den anden kolonne i listen. |
| **Liste** | **> Tærskler** |
| Aktivere tærskler | Vælg for at aktivere tærskler.  Du kan finde oplysninger i [Almindelige indstillinger](#thresholds) . |

## <a name="stack-of-line-charts-part"></a>Stak af linje diagrammer del

Viser tre separate kurvediagrammer med flere serier fra en forespørgsel, log over tid.

![Stak kurvediagrammer](media/log-analytics-view-designer/view-stack-line-charts.png)

| Indstilling | Beskrivelse |
|:--|:--|
| **Generelt** |
| Gruppen titel | Tekst, der skal vises øverst i feltet. |
| Ny gruppe | Vælg for at oprette en ny gruppe i visningen fra og med den aktuelle visning. |
| Ikonet | Den billedfil, der skal vises ud for resultatet i sidehovedet. |
| **Diagram 1<br>diagram 2<br>diagram 3** | **> Sidehoved** |
| Titel | Tekst, der skal vises øverst i diagrammet. |
| Undertitel | Tekst, der vises under overskriften øverst i diagrammet. |
| **Diagram 1<br>diagram 2<br>diagram 3** | **Kurvediagram** |
| Forespørgsel | Forespørgsel til at køre til kurvediagram.  Den første egenskab skal være en tekstværdi og den anden egenskab en numerisk værdi.  Dette er typisk en forespørgsel, der bruger nøgleordet **mål** til at opsummere resultater.  Hvis forespørgslen bruger nøgleordet **interval** brug x-aksen i diagrammet dette tidsinterval.  Hvis forespørgslen ikke inkluderer nøgleordet **interval** bruges hver time intervaller til x-aksen. |
| **Diagram** | **> Y-aksen** |
| Brug logaritmisk skala | Vælg for at bruge en logaritmisk skala for y-aksen. |
| Enheder | Angive enhederne for de værdier, der returneres af forespørgslen.  Disse oplysninger bruges til at vise etiketter på det diagram, der angiver værdityperne og eventuelt til konvertering af værdierne.  En enhedstype angiver, hvilken kategori af enheden og definerer de aktuelle enhedstype værdier, der er tilgængelige.  Hvis du vælger en værdi i konvertere til, og derefter de numeriske værdier konverteres fra den aktuelle enhed type på Konvertér til at skrive. |
| Brugerdefineret etiket | Tekst, der vises for Y-aksen ud for etiketten for enhedstypen.  Hvis ingen etiket ikke er angivet, vises kun enhedstype. |

## <a name="common-settings"></a>Almindelige indstillinger
I følgende afsnit beskrives indstillingerne er ens for flere visualisering dele.

### <a name="name-value-separator">Navn og værdi Separator</a>
Enkelt tegn afgrænser, hvis du vil analysere tekstegenskaben fra en liste over forespørgsel i flere værdier.  Hvis du angiver en afgrænser, kan du angive navne for hvert felt, der er adskilt med det samme afgrænser i feltet navn.

For eksempel bør du overveje en egenskab, der kaldes *placering* , der medtages værdier som *Redmond dokumentkomponent 41* og *Storeby Building12*.  Du kan angive – for navn og værdi Separator og *By dokumentkomponent* for navnet.  Dette vil fortolkes hver værdi i to egenskaber, som kaldes *By* og *dokumentkomponent*. 

### <a name="navigation-query">Navigation forespørgsel</a>
Forespørgsel, der skal køres, når brugeren markerer et element på listen.  Bruge *{valgte element}* til at medtage syntaksen for element, som brugeren har valgt.

Eksempelvis hvis forespørgslen indeholder en kolonne med navnet *Computer* og navigation forespørgslen er *{valgte element}*, en forespørgsel f.eks *Computer = "MyComputer"* skal køres, når brugeren har valgt en computer.  Hvis forespørgslen navigation er *Type = begivenhed {valgte element}* derefter forespørgslen *Type = begivenhed Computer = "MyComputer"* skal køres.

### <a name="sparklines">Minidiagrammer</a>
Et minidiagram er et lille kurvediagram, som illustrerer værdien af en post på listen over tid.  For visualisering webdele med en liste, kan du vælge, om at få vist en vandret linje, som indikerer relative værdien af en numerisk kolonne eller et minidiagram, der angiver dens værdi over tid.

I følgende tabel beskrives indstillingerne for minidiagrammer.

| Indstilling | Beskrivelse |
|:--|:--|
| Aktivere minidiagrammer | Vælg at få vist minidiagram i stedet for vandret linje. |
| Handling | Hvis minidiagrammer er aktiveret, er handlingen, der skal udføres på hver egenskab på listen for at beregne værdier for minidiagrammet.<br><br>-Sidste eksempel: Sidste værdi i rækken over et tidsinterval.<br>-Maks: Maksimumværdi for serien over et tidsinterval.<br>-Min: Minimumværdien for serien over et tidsinterval.<br>-Sum: Summen af værdierne for serien over et tidsinterval.<br>-Oversigt: Bruger kommandoen samme mål som forespørgsel i sidehovedet. |

### <a name="thresholds">Tærskler</a>
Tærskler gør det muligt at vise en farvet ikonet ud for hvert element på en liste, hvorved du får en hurtig visuel indikator for elementer, der overskrider en bestemt værdi eller falder inden for et bestemt område.  Du kan for eksempel viser et grønt ikon for elementer med en gyldig værdi, gul, hvis værdien er i et område, der angiver en advarsel og rød, hvis den overstiger en fejlværdi.

Når du aktiverer tærskler for en webdel, skal du angive en eller flere tærskler.  Hvis værdien af et element er større end en grænseværdi og mindre end den næste tærskelværdi, bruges den ønskede farve.  Hvis elementet er større end derefter højeste grænseværdi, angive den ønskede farve.   

Hvert grænseværdi sæt har en grænseværdi for med en værdi på **standard**.  Dette er farvesæt, hvis ingen andre værdier er overskredet.  Du kan tilføje eller fjerne tærskler ved at klikke på den **+** eller **x** -knappen.

I følgende tabel beskrives indstillingerne for tærskler.

| Indstilling | Beskrivelse |
|:--|:--|
| Aktivere tærskler | Vælg at få vist en Farveikon til venstre for hver værdi, der angiver sin tilstand i forhold til angivne tærskler. |
| Navn | Navn, der identificerer tærskelværdien. |
| Grænseværdi | Værdi for grænsen.  Sundhed farven for hvert element på listen er angivet til farven på den højeste tærskelværdi overskredet efter elementets værdi.  Der er et standard-grænseværdi, der er farven, hvis ingen tærskelværdi overskrides. |
| Farve | Farve til tærskelværdien. |


## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at understøtte forespørgsler i Visualiseringen dele.