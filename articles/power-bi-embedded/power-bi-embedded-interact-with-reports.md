<properties
   pageTitle="Arbejde med rapporter ved hjælp af JavaScript API | Microsoft Azure"
   description="Power BI integreret, arbejde med rapporter ved hjælp af JavaScript API"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Arbejde med Power BI-rapporter ved hjælp af JavaScript API

Power BI JavaScript API gør det muligt at integrere nemt Power BI-rapporter i dine programmer. Med API, kan dine programmer fra et program arbejde med forskellige rapportelementerne som sider og filtre. Denne interaktivitet gør Power BI-rapporter en mere integreret del af dit program.

Du kan integrere en Power BI-rapport i dit program ved hjælp af en iframe, der er hostet som en del af programmet. Iframe fungerer som en grænse mellem dit program og en rapport, som du kan se på følgende billede. 

![Power BI integreret iframe uden Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

Iframe gør det meget nemmere at integrering processen, men uden JavaScript API rapporten og dit program ikke kan interagere med hinanden. Denne funktion ikke interaktion kan gøre det synes rapporten ikke er virkelig en del af programmet. Rapporten, og programmet virkelig har brug for til at kommunikere frem og tilbage, som i følgende billede.

![Power BI integreret iframe med Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

Power BI JavaScript API gør det muligt at skrive programkode, der kan sikkert passerer gennem iframe-rammen. Dette gør det muligt for dit program til at udføre en handling fra et program i en rapport, og lytte til begivenheder fra handlinger, som brugere foretager i rapporten.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Hvad kan du gøre med Power BI JavaScript API?
Med JavaScript API kan du administrere rapporter, gå til siderne i en rapport, filtrere en rapport og håndtere integrering af begivenheder. I det følgende diagram viser strukturen i API.

![Power BI JavaScript API-diagram](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Administrere rapporter
Javascript API gør det muligt at administrere funktionsmåde på niveauet for rapporten og side:

- Integrere en bestemt Power BI-rapport sikkert i dit program – kan du prøve den [integrere demo program](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Angiv adgangstoken
- Konfigurere rapporten
  - Aktivere og deaktivere filterruden og ruden Sidenavigation - prøve at [opdatere indstillinger for demo program](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Angive standardindstillinger for sider og filtre – kan du prøve det [angive standardindstillinger for demo](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Angiv og afslutte fuldskærmstilstand

[Få flere oplysninger om integrering af en rapport](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Gå til siderne i en rapport
JavaScript API enbales dig med at finde alle siderne i en rapport og angive den aktuelle side. Prøv [navigation demo programmet](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Lær mere om Sidenavigation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrere en rapport
JavaScript API indeholder grundlæggende og avancerede formularer og dataark til integrerede rapporter og rapportsider. Prøv [filtrering demo programmet](http://azure-samples.github.io/powerbi-angular-client/#/scenario4), og gennemgå nogle indledende koden her.  


#### <a name="basic-filters"></a>Grundlæggende filtre
Et grundlæggende filter er placeret på en kolonne eller hierarki niveau og indeholder en liste over værdier for at medtage eller udelade.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Avancerede filtre
Avancerede filtre bruge operatoren logisk og eller eller, og Accepter én eller to betingelser, hver med deres egen operator og værdi. Understøttede betingelser er:

- Ingen
- LessThan
- LessThanOrEqual
- Størreend
- GreaterThanOrEqual
- Indeholder
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Er
- IsNot
- IsBlank
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Få flere oplysninger om filtrering](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Håndtering af begivenheder
Ud over sender oplysninger til iframe, dit program kan også få vist oplysninger om de følgende hændelser, der kommer fra iframe:

- Integrere
  - indlæst
  - fejl
- Rapporter
  - pageChanged
  - dataSelected (kommer snart)

[Få flere oplysninger om håndtering af begivenheder](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om Power BI JavaScript API til udtjekning linkene nedenfor:

- [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [-Objektmodelreference](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Eksempler
  - [Vinklet](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Direkte demo](https://microsoft.github.io/PowerBI-JavaScript/demo/)
