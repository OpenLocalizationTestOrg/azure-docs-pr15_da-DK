# <a name="azure-technical-documentation-contributor-guide"></a>Azure tekniske dokumentation bidragyder vejledning

Du har fundet det GitHub lager, som indeholder kilden til den tekniske dokumentation, der er publiceret til Azure dokumentation Center på [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Denne lager indeholder også vejledning for at hjælpe dig med at bidrage til vores tekniske dokumentation.  Du kan finde en liste over artiklerne i den bidragydere vejledning [indekset](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Bidrage til Azure dokumentation

Tak for interessen i dokumentationen til Azure!

* [Måder til at bidrage](#ways-to-contribute)
* [Koden for lede](#code-of-conduct)
* [Om dit bidrag til Azure indhold](#about-your-contributions-to-azure-content)
* [Lager organisation](#repository-organization)
* [Brug af GitHub, ciffer og denne lager](#use-github-git-and-this-repository)
* [Sådan bruges tabsbeløb til at formatere dit emne](#how-to-use-markdown-to-format-your-topic)
* [Feedback, kommentarer og support](./contributor-guide/feedback-and-comments.md)
* [Flere ressourcer](#more-resources)
* [Indeks over alle bidragydere vejledning artikler](./contributor-guide/contributor-guide-index.md) (åbner ny side)

## <a name="ways-to-contribute"></a>Måder til at bidrage 

Du kan bidrage til [Azure dokumentation](http://azure.microsoft.com/documentation/) på et par forskellige måder:

* Bidrage til et [forum diskussion](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Send Disqus kommentarer i bunden af artikler.
* Du kan nemt at bidrage til tekniske artikler i brugergrænsefladen GitHub. Finde i artiklen i lageret, eller finde artiklen på [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) og klikke på linket i artiklen, der går til GitHub kilde til artiklen.
* Hvis du foretager væsentlige ændringer i en eksisterende artikel, skal tilføje eller ændre billeder eller bidrager en ny artikel, du deler sig denne lager, installere ciffer fest, tabsbeløb tastatur, og lær nogle ciffer kommandoer.

##<a name="code-of-conduct"></a>Koden for lede

Dette projekt har vedtaget [Microsoft Open Source kode af forestå](https://opensource.microsoft.com/codeofconduct/). Finde flere oplysninger i [Den kode af forestå ofte stillede spørgsmål](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakt [opencode@microsoft.com](mailto:opencode@microsoft.com) med yderligere spørgsmål eller kommentarer.

##<a name="about-your-contributions-to-azure-content"></a>Om dit bidrag til Azure indhold

###<a name="minor-corrections"></a>Mindre rettelser

Mindre rettelser eller beskrivelser af en lang du sender til dokumentation og kode eksemplerne i denne repo er dækket af [Azure websted vilkår for anvendelse (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Større indsendelser

Hvis du sender en anmodning om hente med nye eller væsentlige ændringer til dokumentation og kodeeksempler, sender vi en kommentar i GitHub beder dig om at sende en online bidrag licens aftale (CLA), hvis du er i en af disse grupper:

* Medlemmer af gruppen Microsoft Open-teknologier.
* Bidragydere, der ikke fungerer for Microsoft.

Vi har brug for at fuldføre formularen online, før vi kan acceptere din anmodning om hente.

Detaljerede oplysninger er tilgængelige på [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Lager organisation

Indholdet i azure-indhold lager følger tilrettelæggelse af dokumentation på [Azure.Microsoft.com](http://azure.microsoft.com). Denne lager indeholder to rod mapper:

### <a name="articles"></a>\articles

Mappen *\articles* indeholder dokumentation artiklerne formateret som tabsbeløb filer med filtypenavnet *.md* .

Artikler i rodmappe er udgivet på Azure.Microsoft.com i stien *http://azure.microsoft.com/documentation/articles/ {artikel-navn-uden-md} /*.

* **Artikel filnavne:** Se [vores navngive vejledning-fil](./contributor-guide/file-names-and-locations.md).

Artikler i deres egen service-mappe er udgivet på Azure.Microsoft.com i stien *http://azure.microsoft.com/documentation/articles/service-folder/ {artikel-navn-uden-md} /*

* **Medier undermapper:** Mappen *\articles* indeholder mappen *\media* for rod directory artikel mediefiler, i, som er undermapper med billeder til hver artikel.  Tjenesten mapperne indeholder en separat medier mappe til artikler i hver Tjenestemappe. Artikel billede mapper der navngives identisk med filen artikel minus filtypenavnet *.md* .

### <a name="includes"></a>\includes

Du kan oprette genanvendelige indhold sektioner, der skal medtages i en eller flere artikler. Se [brugerdefineret udvidelser, som bruges i vores technical indhold](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>\markdown skabeloner

Denne mappe indeholder vores standard tabsbeløb skabelon med den grundlæggende tabsbeløb formatering, du har brug for til en artikel.

### <a name="contributor-guide"></a>\contributor-guide

Denne mappe indeholder artikler, der er en del af vores bidragydere vejledning.  

## <a name="use-github-git-and-this-repository"></a>Brug GitHub, ciffer samt denne lager

Finde oplysninger om, hvordan du kan bidrage, hvordan du bruger GitHub UI til at bidrage små ændringer, og hvordan du deler sig og klone lager til vigtigere bidrag [installere og konfigurere værktøjer til redigering i GitHub](./contributor-guide/tools-and-setup.md).

Hvis du installerer GitBash, og du vælger at arbejde lokalt, findes trinnene til at oprette en ny lokal arbejde gren, foretager ændringer og sende ændringerne tilbage til den primære gren i [ciffer kommandoer til at oprette en ny artikel eller opdatere en eksisterende artikel](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Grene

Vi anbefaler, at du opretter lokale arbejde forgreninger, der fokuserer på et bestemt område ændring. Hver gren skal være begrænset til en enkelt konceptet/artikel både til at strømline arbejdsgang og mindske risikoen for Flet konflikter.  De følgende bestræbelser er på det relevante område til en ny gren:

* En ny artikel (og tilknyttede billeder)
* Stavning og grammatik ændringer på en artikel.
* Anvende en enkelt formateringsændringer på tværs af et stort antal artikler (fx ny copyright sidefod).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Sådan bruges tabsbeløb til at formatere dit emne

Alle artikler i denne lager Brug GitHub flavored tabsbeløb.  Her er en liste over ressourcer.

- [Grundlæggende om tabsbeløb](https://help.github.com/articles/markdown-basics/)

- [Der kan udskrives tabsbeløb cheatsheet](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Se vores liste over tabsbeløb editors, [værktøjer og konfiguration af emne](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Artikel metadata

Artikel metadata gør det muligt for bestemte funktioner på webstedet azure.microsoft.com, som forfatter afskrivningen, bidragyder afskrivningen, brødkrummer, artikel beskrivelser og SEO optimeringer samt rapportering Microsoft bruger til at vurdere ydeevnen for indholdet. Så er metadata, der vigtigt! [Her er retningslinjer for at sikre, at metadata for din udføres korrekt](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Etiketter

Automatiseret etiketter er tildelt til at hente anmodninger om at hjælpe os med at administrere hente anmodning om arbejdsprocessen og hjælpe fortæller dig, hvad der foregår med din hente anmodning:

* Bidrag licensaftalen relateret
    * CLA ikke påkrævet: ændringen er relativt mindre og ikke kræver, at du logger en CLA.
    * CLA påkrævet: omfanget af ændringen er relativt stor og kræver, at du logger en CLA.
    * CLA-signerede: bidragyder logget Indstilles, så hente anmodningen kan nu flytte frem til gennemsyn.
* Søjle etiketter: etiketter PnP f.eks., moderne Apps og TDC Hjælp kategoriseret hente anmodninger ved den interne organisation, der skal gennemgå hente anmodningen.
* Ændre sendes til forfatternavn: forfatteren har modtaget meddelelse om ventende hente anmodningen.

## <a name="more-resources"></a>Flere ressourcer

Se alle vores vejledning emner [indeks i vores bidragyder vejledning](./contributor-guide/contributor-guide-index.md) .
