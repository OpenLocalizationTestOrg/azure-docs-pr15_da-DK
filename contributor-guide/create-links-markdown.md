<properties
   pageTitle="Oprette links i tabsbeløb artikler" description="Beskriver, hvordan du kode crosslinks i tabsbeløb." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Sammenkæde vejledning til Azure teknisk indhold

### <a name="links-from-one-acom-article-to-another"></a>Links fra en ACOM artikel til en anden

For at oprette et link af typen indbygget fra en ACOM tekniske artikel til en anden ACOM tekniske artikel skal du bruge følgende link syntaks:  

- En artikel i en tjeneste directory links til en anden artikel i den samme service-mappe:

  `[link text](article-name.md)`

- En artikel indeholder links fra en tjeneste undermappe til en artikel i rodmappen:

  `[link text](../article-name.md)`

- En artikel i roden directory links til en artikel i en tjeneste undermappe: 

  `[link text](./service-directory/article-name.md)`

- En artikel i et tjenestens undermappe links til en artikel i en anden tjeneste undermappe:

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Links til ankre

Du behøver ikke at oprette ankre – de oprettes automatisk ved at publicere tidspunkt for alle H2 overskrifter. Det eneste, du skal gøre, er at oprette links til H2 afsnittene.

- Oprette et link til en overskrift i samme artikel:

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Oprette et link til et anker i en anden artikel i den samme undermappe:

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Oprette et link til et anker i en anden tjeneste undermappe:

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

En metode til at automatisere processen med at oprette links i dine artikler til automatisk oprettede anker links er [MarkdownAnchorLinkGenerator - et værktøj til at generere anker links til ACOM i det korrekte format](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Indeholder links fra

Da medtage filer er placeret i en anden mappe, skal du bruge længere relative stier, som vist nedenfor. Hvis du vil oprette et link til en artikel fra en Medtag datafil, ved at bruge dette format:

    [link text](../articles/service-folder/article-name.md)
    
Lær mere om, hvordan du bruger en Medtag fil i [brugerdefineret tabsbeløb filtypenavne retningslinjer](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Links i vælgere

Hvis du har vælgere, der er integreret i en Medtag, skal du bruge denne type sammenkædning: 

    > [AZURE. DATAVÆLGER-liste (Dropdown1 | Dropdown2)]     -  [(Tekst1 | Eksempel1)](../articles/service-folder/article-name1.md)
    - [(Tekst1 | Eksempel2)](../articles/service-folder/article-name2.md)
    - [(tekst2 | Eksempel3)](../articles/service-folder/article-name3.md)
    - [(tekst2 | Eksempel4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Referencetype links

Du kan bruge reference typografi links til at gøre det nemmere at læse kildeindholdet. Linkene reference typografi Erstat indbygget link syntaksen med forenklet syntaks, som gør det muligt at flytte de lange URL-adresser til slutningen af artiklen. Her er vovemod Fireballs eksempel:

Indbygget tekst:

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Sammenkæde referencer i slutningen af artiklen:

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Kontrollér, at du medtage mellemrummet efter kolon, før du linket. Når du sammenkæder til andre tekniske artikler, hvis du glemmer at medtage afstanden, brydes linket i den publicerede artikel. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Oprette et link til ACOM sider, der ikke er en del af den tekniske dokumentation

Bruge en absolut URL-adresse for at oprette et link til en side på ACOM (som en priser side, SERVICENIVEAUAFTALE side eller andet, der ikke er en artikel til dokumentation), men udelader de internationale indstillinger. Målet her er, der fungerer i links i GitHub og på webstedet med gengivne:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Link til MSDN- eller TechNet

Når du vil oprette et link til MSDN- eller TechNet skal bruge den fulde link til emnet, og fjern da-DK landestandard link. 

### <a name="use-friendly-link-text-for-all-links"></a>Bruge fulde linktekst for alle kæder

De ord, du medtage et link skal være læsevenlige – med andre ord, de skal være normal engelske ord eller titlen på den side, du opretter et link til. Brug ikke "Klik her". Det er ugyldig for SEO og beskriver ikke destinationen en dækkende måde.

**Rette:**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Forkert:**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>FWLinks

Undgå FWLinks (vores omdirigering system) i azure.microsoft.com indhold. De skal bruges kun som endelig, når du har brug at oprette en kæde til en side, hvis du endnu ikke kender URL-adresse. De stort set aldrig faktisk skal bruges. For ACOM definerer du filnavnet, så du kan se, hvad der skal indsættes forvejen. For et biblioteksemne, der ikke er endnu er udgivet, kan du oprette et link, der bruger emnet GUID, så du ikke behøver at bruge en FWLink.

Hvis du skal bruge en FWLink på en webside, omfatte parameteren P for at gøre det en permanent omdirigering:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Når du indsætter URL-adressen for destinationen i værktøjet FWLink, huske at fjerne landestandarden, hvis dit mål link er ACOM, eller MSDN eller TechNet-biblioteket.

## <a name="remember-the-azure-library-chrome"></a>Husk Azure bibliotek chrome!
Hvis du vil oprette et link til et biblioteksemne i Azure, der ligger under [denne node](https://msdn.microsoft.com/library/azure), Husk at angive Azure chrome i linket (/ azure /). Azure chrome deler Navigationsindstillinger ACOM og viser kun Azure indholdet af MSDN-biblioteket. Et korrekt relateret link ser sådan ud:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Siden der ellers kan gengives i standardvisningen for MSDN, med hele MSDN træet vises.

### <a name="contributors-guide-links"></a>Bidragydere vejledning Links

- [Oversigt over artikel](./../README.md)
- [Indeks over vejledning artikler](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
