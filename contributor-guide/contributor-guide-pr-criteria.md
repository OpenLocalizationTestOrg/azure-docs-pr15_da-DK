# <a name="quality-criteria-for-pull-request-review"></a>Gennemse kvalitetskriterier for hente anmodning

Disse kriterier er beregnet til forfattere, der opretter og vedligeholder tekniske artikler og hente anmodning korrekturlæsere, der Gennemse indhold hente anmodninger. Hente anmodningen ikke kan [automatisk fletning](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), der skal gennemgås af en human hente anmodning korrekturlæser. Hente anmodning korrekturlæsere Gennemse generelt kun Hvad er nye eller ændrede. Hente anmodning korrekturlæsere evaluering af ændringerne i en anmodning om hente ifølge de blokering og ikke blokerer kvalitet Gennemse elementer, er vist i denne artikel.

## <a name="blocking-content-quality-items"></a>Blokering af indhold kvalitet elementer

Opdateringerne i hente anmodningen skal opfylde følgende kriterier skal flettes. Hente anmodning korrekturlæsere give feedback i hente anmodning om kommentarer til disse elementer, og skriv `#hold-off` i hente Anmod om at vende tilbage til dig (forfatter) med feedback.

| Kategori | Kvalitet gennemgangselement |
|----------|---------------------|
|Forudsætninger| "Klar-til-fletningen" og "Validering lykkedes" etiketterne er tildelt til feltet PR.|
|Forudsætninger| Hente anmodningen kan ikke blokeres ved en Flet konflikt.|
|Repo integritet|    Hente anmodningen indeholder ingen indlysende indhold regressioner.|
|Repo integritet|    Hente anmodning inkluderer ikke en integreret repo eller usædvanlige, overflødige filer.|
|Repo integritet |Hente anmodningen indeholder færre end 100 ændrede filer, medmindre kurs bevidst der opdaterer en release gren fra master. (Virkelig, en kurs skal indeholde længst færre end, men efter 100 ændrede filer GitHub vises ikke på diffs).|
|Navngive |Filnavne for nye filer, skal du følge [retningslinjer for navngivning](file-names-and-locations.md).|
|Navngive |Nye mapper introduceret i repo Følg [mappe naming retningslinjer](file-names-and-locations.md#folder-names-in-the-repo).|
|Indhold    |I artiklen er et teknisk dokument, og derfor i salgskanalen korrekte indhold. Se den [hvad der skal hvorhen vejledning](content-channel-guidance.md).|
|Indhold    |Emne i tekniske dokumentet er relevante for en teknisk artikel. Se den [hvad der skal hvorhen vejledning](content-channel-guidance.md).|
|Indhold    |I artiklen indeholder et indledende afsnit og en beskrivelse af eller grundlæggende meddelelsens indhold. I artiklen skal indeholde tilstrækkelige, fuldført indhold til at skille sig sin egen som en artikel. Den må ikke være et lille fragment af oplysninger. (Undtagelse: et "Begrænsninger" emne, hvis det er i forbindelse med en stor artikel, der viser alle grænserne for en tjeneste.)|
|Indhold| Elementer, der skal være opstillinger nummereres, elementer, der skal være vilkårlig rækkefølge lister med punkttegn. Dette er grundlæggende brugervenligheden.|
|Indhold| Usædvanlige eller nye grafik, informationsarkitektur eller strukturer og tydeligt ikke-standard designs skal være undersøgt med overlappende kurs korrekturlæser. Grupper, der er at eksperimentere med nye ting skal have et problem/løsning lærred eller plan på plads til at vurdere forsøg.|
|Webdesign funktionalitet| Switchers bruges kun til at skifte på tværs af flere versioner af samme artikel.|
|Webdesign funktionalitet| Titler for switchered artikler indeholder oplysninger, der adskiller hvert artikel fra de andre artikler om switchered konfiguration.|
|Webdesign funktionalitet| Manuelt oprettet er indholdsfortegnelser ikke tilladt. I artiklen skal stole på H2s for dens på siden indholdsfortegnelse.|
|Webdesign funktionalitet| Hvis H2 overskrifter er til stede, indeholder i artiklen mindst to H2 overskrifter. Ved hjælp af én H2 overskrift opretter et enkelt element artikel Indholdsfortegnelsen. H2 overskrifter skal bruges før H3 overskrifter til at sikre, at der oprettes en indholdsfortegnelse.|
|Tabsbeløb| HTML: Kilde indhold indeholder ikke HTML på niveauet Bloker – underordnede indbygget HTML er tilladt – som hævet, sænket skrift, specialtegn og andre mindre ting, du ikke kan gøre med tabsbeløb. HTML-tabeller må kun, hvis tabellen indeholder opstilling med punkttegn eller tal / bogstaver, men det er normalt angivelse indholdet skal være forenklet, så kilden kan være kodet i tabsbeløb.|
|Tabsbeløb   |Brugerdefineret tabsbeløb elementer anvendes, hvor det er relevant. Ex: Kodes noter ved hjælp af AZURE. Bemærk filtypenavnet, ikke som almindelig tekst.|
|SEO    |Det "& #124; Microsoft Azure"sted-id er påkrævet|
|SEO    |H1 titlen indeholder tilstrækkelige oplysninger til at beskrive indholdet af denne artikel, for at skelne det fra andre Azure artikler og tilknytte til sandsynligvis kunde nøgleord. "Oversigt" som titel H1 er for eksempel en fejl.|
|Terminologi| Brug af ARM forkortelse, V1 eller V2 referencer til klassiske og ressourcestyring installation modeller er et blokering terminologi element.|
|Billeder |Animeret GIF-filer accepteres ikke i repo.|
|Billeder | Billeder har Ryd opløsning, er gratis forkert stavede ord og indeholder ingen personlige oplysninger | 
|Midlertidige|Artikel eksemplet skal være rene på midlertidige. Det kan ikke indeholde en hvilken som helst indlysende formateringsproblemer: <br><br>-En opstilling med punkttegn eller liste, der vises som et afsnit <br> -Kode i en kodeblok vises delvist i kodeblokken og delvist uden for det <br>-Nummererede trin, der er nummereret forkert på grund af fejlbehæftede indrykning|

## <a name="non-blocking-content-quality-items"></a>Ikke-blokerer indhold kvalitet elementer

For disse elementer indeholder hente anmodning korrekturlæsere feedback og anvisninger til forfatteren til opfølgning med rettelser i en nyere hente anmodning. Denne tilbagemelding blokerer dog ikke beslutningen om at flette. Forfattere skal følge op inden for 3 arbejdsdage med løsninger.

| Kategori | Kvalitet gennemgangselement |
|----------|---------------------|
|Indhold|Artikler skal have en "næste trin" til sidst med 1-3 relevante og overbevisende næste trin. Kort tekst der skal medtages, der hjælper med at forstå, hvorfor de næste trin er relevante kunden. (Nye artikler kun). Eksempel: <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Indhold|Stavning, grammatik og andre problemer med skrivning - hente anmodning korrekturlæsere kan give feedback på nogle mindre problemer som ikke-blokerer feedback. Hvis der er mere end et par redaktionel problemer, log korrekturlæsere en redigeringsanmodning om en efter publikation Rediger artikel.|
|Billeder|Billeder bruger den korrekte billedforklaring typografi og farve, og skærmbilleder Brug den rigtige kant og pladsholder typografi. [Se vejledningen billede](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Billeder|Billeder indeholder alternativ tekst. [Se vejledningen billede](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Webdesign funktionalitet|H2 overskrifter, når de gengives i indholdsfortegnelse på siden skal ideelt ombrydes til højst 2 linjer. Lange overskrifter fremhæver i artiklen Indholdsfortegnelsen sværere at scanne.|
|Typografi konventioner|Alle titler og overskrifter er sætning med stort, per Azure typografi.|
|Proces|Hvis hente anmodningen blev har nemt er konfigureret for at drage fordel af PRmerger automatisering, give feedback til forfatteren om, hvordan du bruger forgreninger, så ændringerne kunne flettes automatisk hente anmodning korrekturlæsere. Artiklen [kurs etikette](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Proces|Når du sletter eller omdøbe en artikel, Sørg for, at du følger processen. Trække anmodning om korrekturlæsere bør tilføje følgende kommentar og link i en kommentar:<br><br>*Kontroller, at du har fulgt proces i den bidragydere vejledning til at slette artikler: <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Relateret

- [Hente anmodning etikette og bedste praksis for Microsoft bidragydere](contributor-guide-pull-request-etiquette.md)

- [Trække anmodning kommentar automatisering](contributor-guide-pull-request-comments.md)
