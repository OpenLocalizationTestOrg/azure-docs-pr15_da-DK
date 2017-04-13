<properties
    pageTitle="Indsamling af Data for at træne din Model: Machine Learning anbefalinger API | Microsoft Azure"
    description="Azure maskine læ anbefalinger - indsamling af Data for at træne din Model"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>Indsamling af Data til at undervise din Model #

Anbefalinger API lærer fra din tidligere transaktioner til at finde ud af, hvordan elementer skal være anbefales til en bestemt bruger.

Når du har oprettet en model, skal du angive to oplysning, før du kan gøre en hvilken som helst kursus: et katalog filer og data om brug af.

>   Hvis du allerede ikke har gjort det, opfordrer vi dig til at fuldføre [Startvejledning](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Katalogdata ##

### <a name="catalog-file-format"></a>Katalog-filformat ###

Katalogfilen indeholder oplysninger om elementerne, du tilbyder til din kunde.
Datatypen katalog skal benytte følgende format:

- Uden funktioner-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Med funktioner-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Eksempel rækker i en fil

Uden funktioner:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Med funktioner:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Formatér detaljer

| Navn | Obligatorisk | Type |  Beskrivelse |
|:---|:---|:---|:---|
| Element-Id |Ja | [A-å], [a-z], [0-9], [_] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 50 | Entydigt id for et element. |
| Elementnavn | Ja | Alfanumeriske tegn<br> Maksimal længde: 255 | Elementnavn. |
| Element kategori | Ja | Alfanumeriske tegn <br> Maksimal længde: 255 | Kategori, som dette element tilhører (fx madlavning bøger, Drama...); kan være tom. |
| Beskrivelse | Nej, medmindre funktioner er præsentere (men kan være tomme) | Alfanumeriske tegn <br> Maksimal længde: 4000 | Beskrivelse af elementet. |
| Funktionslisten | Nej | Alfanumeriske tegn <br> Maksimal længde: 4000; Maksimalt antal funktioner: 20 | Kommasepareret liste over funktionsnavn = funktion værdi, der kan bruges til at forbedre model anbefaling.|

#### <a name="uploading-a-catalog-file"></a>Overføre en fil

Se på [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) til overførsel af en katalogfil.  

Bemærk, at indholdet af katalogfilen skal overføres som anmodningsteksten.

Hvis du overføre flere katalogfiler til den samme modellen med flere opkald, indsættes vi kun de nye katalogelementer. Eksisterende elementer, forbliver med de oprindelige værdier. Du kan ikke opdatere katalogdata ved hjælp af denne metode.

>   Bemærk: Den maksimale filstørrelse er 200MB.
>   Det maksimale antal elementer i kataloget understøttes er 100.000 elementer.


## <a name="why-add-features-to-the-catalog"></a>Hvorfor tilføje funktioner i kataloget?

Anbefalinger programmet opretter en statistisk model, der fortæller, hvilke elementer der sandsynligvis synes godt om eller købt af en kunde. Når du har er et nyt produkt, der har aldrig været sammen det ikke muligt at oprette en model på samtidig forekomster alene. Antag, at du starter med et nyt tilbud "børns violin" i din store, da du aldrig har solgt, violin, før du ikke kan se, hvilke andre elementer til anbefaler med pågældende violin.

Hvis programmet kender oplysninger om violin, dog (det vil sige det er et musical instrument, er det for børn alder 7-10, det er ikke en dyr violin, osv.), og klik derefter på program kan læse fra andre produkter med lignende funktioner. Du har solgt violin's tidligere, og som regel personer, der køber violins har tendens til at købe klassisk musik cd'er og ark musik repræsenterer.  Systemet kan finde disse forbindelser mellem funktionerne og give anbefalinger, der er baseret på funktionerne, mens din nye violin har lille brugen.

Funktioner, der er importeret som en del af datatypen katalog, og derefter deres rang (eller vigtigheden af funktionen i modellen) er knyttet når et plads build er færdig. Funktionen plads kan ændre efter anvendelse af Brugsdata og de typer elementer. Men for ensartet brugen/elementer, rangen bør have kun små udsving. Rangen for funktioner, der er en ikke-negative tal. Tallet 0 betyder, at funktionen ikke blev vurderet (sker der, hvis du kalder denne API før afslutningen af det første plads build). Den dato, hvor pladsen blev tilskrevet kaldes karakteren friskhed.


###<a name="features-are-categorical"></a>Funktioner er Categorical

Det betyder, at du skal oprette funktioner, der ligner en kategori. For eksempel pris = 9.34 ikke er en kategoriske funktion. På den anden side som en funktion priceRange = Under5Dollars er en kategoriske funktion. En anden almindelige fejl er at bruge navnet på elementet som en funktion. Det vil betyde, at navnet på et element, der skal være entydige, så den ikke ville beskrive en kategori. Kontrollér, at funktionerne, der repræsenterer kategorier af elementer.


###<a name="how-manywhich-features-should-i-use"></a>Hvor mange/som funktioner skal jeg bruge?


I sidste ende opbygge anbefalinger understøtter opbygning af en model med op til 20 funktioner. Du kan tildele mere end 20 funktioner til elementer i kataloget, men du forventede at gøre et rangering build og vælge kun funktionerne, der rangerer høj. (En funktion med en rang på 2.0 eller flere er en god funktion til at bruge!). 


###<a name="when-are-features-actually-used"></a>Når der funktioner, der faktisk bruges?

Funktioner, der anvendes af modellen, når der ikke er nok transaktionsdata til at give anbefalinger om transaktionsoplysninger alene. Så har funktioner optimal virkning på "kolde elementer" – elementer med par transaktioner. Hvis alle elementer har tilstrækkelige oplysninger du ikke muligvis forbedre modellen med funktioner.


###<a name="using-product-features"></a>Brug af produktfunktioner

For at bruge funktioner som en del af din build, skal du:

1. Kontrollér, at dit katalog indeholder funktioner, når du overfører den.

2. Udløse en rangeringsmodel build. Dette vil gøre analysen af prioritet rangen af funktioner.

3. Udløse en anbefalinger build, indstille følgende opbygge parametre: angive useFeaturesInModel til sand, allowColdItemPlacement til sand, og modelingFeatureList skal du vælge kommasepareret listen over de funktioner, du vil bruge til at forbedre din model. Yderligere oplysninger finder du i [anbefalinger opbygge typeparametre](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Data om brug af ##
En brugen fil indeholder oplysninger om, hvordan disse elementer bruges eller transaktioner fra din virksomhed.

#### <a name="usage-format-details"></a>Detaljer om anvendelse Format
En brugen fil er en fil i CSV (kommasepareret værdi), hvor hver række i filen brugen repræsenterer en interaktion mellem en bruger og et element. Hver række er formateret som følger:<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Navn  | Obligatorisk | Type | Beskrivelse
|-------|------------|------|---------------
|Bruger-Id|         Ja|[A-å], [a-z], [0-9], [_] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 255 |Entydigt id for en bruger.
|Element-Id|Ja|[A-å], [a-z], [0-9], [& #95;] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 50|Entydigt id for et element.
|Tid|Ja|Dato i formatet: åååå/MM/ååååTtt (fx 2013/06/20T10:00:00)|Tidspunkt for data.
|Begivenhed|Nej | Et af følgende:<br>• Klik på<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Køb| Hvilken type transaktion. |

#### <a name="sample-rows-in-a-usage-file"></a>Eksempel rækker i filen brugen

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Overførsel af en brugen fil

Se på [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) til overførsel af brugen filer.
Bemærk, at du skal overføre indholdet af filen brugen som brødteksten i HTTP opkaldet.

>  Bemærk!

>  Maksimumfilstørrelse: 200MB. Du kan overføre flere brugen filer.

>  Du skal overføre en fil, før du går i gang med at tilføje Brugsdata i din model. Kun elementer i katalogfilen bruges i fasen oplæring. Alle andre elementer blive, ignoreret.

## <a name="how-much-data-do-you-need"></a>Hvor meget data har du brug for?

Kvaliteten af din model er meget afhængigt af kvaliteten og mængden af dine data.
Systemet lærer når brugere du køber forskellige elementer (vi kalder denne samtidig forekomster). For FBT builds er det også vigtigt at vide, hvilke elementer der købes i de samme transaktioner. 

Er en god ide at have de fleste elementer i 20 transaktioner eller derover, så hvis du havde 10.000 elementer i kataloget, vil vi anbefaler, at du har mindst 20 gange, antallet af transaktioner eller om 200.000 transaktioner. Endnu engang er dette en tommelfingerregel. Du skal til at eksperimentere med dine data.

Når du har oprettet en model, kan du udføre en [beregning i offline](cognitive-services-recommendations-buildtypes.md) for at se, hvor godt din model sandsynligvis til at udføre.
