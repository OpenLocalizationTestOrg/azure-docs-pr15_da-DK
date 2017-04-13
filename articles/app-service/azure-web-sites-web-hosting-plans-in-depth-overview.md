<properties
    pageTitle="Azure App Service planer dybden oversigt | Microsoft Azure"
    description="Lær hvordan App Service-planer til Azure App Service arbejde, og hvordan de skaber værdi oplevelsen administration."
    keywords="App, azure app tjeneste, skala, SVG, app-serviceaftale app serviceomkostninger"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Azure App Service planer dybden oversigt#

En App-serviceaftale repræsenterer et sæt af funktioner og kapacitet, som du kan dele på tværs af flere apps. Webapps, Mobile-Apps, funktionen Apps eller API-Apps i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) alle køre i en App-serviceaftale. Disse planer understøtter fem priser niveauer: *ledig*, *delt*, *grundlæggende*, *Standard*og *Premium*. Hvert niveau har sin egen funktioner og kapacitet. Apps i samme abonnement og geografiske placering kan dele en plan. Alle de apps, der deler en plan kan bruge alle funktioner og funktioner, der er defineret af den plan niveau. Alle apps, der er knyttet til en plan køre på de ressourcer, der definerer planen.

Eksempelvis hvis din plan er konfigureret til at bruge to "lille" forekomster i den almindelige webtjenesten, alle apps, der er knyttet til denne plan kører på begge forekomster og har adgang til funktionen standard service niveau. Plan forekomster, som kører apps er fuldt administreret og meget.

I denne artikel gennemgås de vigtigste karakteristika som niveau og skalering af en App Service-plan, og hvordan de kommer ind i Afspil mens du styrer dine apps.

## <a name="apps-and-app-service-plans"></a>Apps og App Service-planer

En app i App Service kan knyttes til kun én App-serviceaftale til enhver tid.

Både apps og planer er indeholdt i en ressourcegruppe. En ressourcegruppe fungerer som livscyklus grænsen for alle de ressourcer, der den. Du kan bruge grupper til at administrere alle elementerne i et program sammen.

Da en enkelt ressourcegruppe kan have flere App Service-planer, kan du tildele forskellige apps til forskellige fysiske ressourcer. Du kan for eksempel adskille ressourcer mellem Udviklingscenter, test- og miljøer. Har du separat miljøer til fremstilling og Udviklingscenter/afprøve gør det muligt at isolere ressourcer. På denne måde Indlæs test mod en ny version af dine apps ikke konkurrere for de samme ressourcer, som din fremstilling apps, som fungerer reelle kunder.

Når du har flere planer i en enkelt ressourcegruppe, kan du også definere et program, der strækker sig over geografiske områder. For eksempel indeholder en meget tilgængelige app kører i to områder mindst to planer, ét for hvert område og én app, der er knyttet til hver enkelt plan. I så fald er alle kopier af appen derefter indeholdt i en enkelt ressourcegruppe. Har du en ressourcegruppe med flere planer og flere apps gør det nemt at administrere, styre og få vist tilstanden af programmet.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Oprette en App-serviceaftale eller bruge eksisterende

Når du opretter en app, skal du overveje at oprette en ressourcegruppe. På den anden side, hvis den app, du er ved at oprette er en komponent til et større program, denne app skal oprettes i ressourcegruppen, der er allokeret for det pågældende større program.

Om den nye app er et helt nyt program eller en del af en større, kan du vælge at bruge en eksisterende App Service-plan til at hoste det eller oprette en ny. Denne beslutning er mere et spørgsmål af kapacitet og forventede belastning.

Hvis denne nye app skal bruge mange ressourcer og har forskellige skalering faktorer fra andre apps hostet i en eksisterende plan, anbefaler vi, at du isolere den i sin egen plan.

Når du opretter en plan, kan du tildele et nyt sæt af ressourcer for din app og opnå større kontrol over ressourceallokering, da hver enkelt plan får sin egen antal forekomster.

Da du kan flytte apps på tværs af planer, kan du ændre den måde, som ressourcer er allokeret på tværs af større programmet.

Endelig, hvis du vil oprette en app i et andet område, og området ikke har en eksisterende plan, Opret en plan i det pågældende område skal kunne være vært for din app der.

## <a name="create-an-app-service-plan"></a>Oprette en App-serviceaftale

>[AZURE.TIP] Hvis du har et App-tjenesten miljø kan du gennemse dokumentationen specifikke for App-tjenesten miljøer her: [Opret en App Service planlægge i en App Service-miljø](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Du kan oprette en tom App-serviceaftale fra App Service plan Gennemse oplevelse eller som en del af app oprettelse.

[Azure-portalen](https://portal.azure.com), klik på **Ny** > **Web + mobile**, og vælg derefter **Web App** eller andre App Service app type.
![Oprette en app på portalen Azure.][createWebApp]

Du kan derefter markere eller oprette App Service planen for den nye app.

 ![Oprette en App-serviceaftale.][createASP]

Hvis du vil oprette en ny App Service-plan, skal du klikke på **[+] Opret ny**skal du skrive navnet på **App-serviceaftale** og derefter vælge en passende **placering**. Klik på **priser niveau**, og vælg derefter en passende priser niveau for tjenesten. Vælg **Vis alle** for at få vist flere indstillinger, priser, såsom **gratis** og **delt**. Når du har valgt det priser niveau, skal du klikke på knappen **Vælg** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Flytte en app til en anden App Service-plan

Du kan flytte en app til en anden app-serviceaftale [Azure-portalen](https://portal.azure.com). Du kan flytte apps mellem planer, som planerne er i samme ressourcegruppe og geografisk område.

Hvis du vil flytte en app til en anden plan, skal du gå til den app, du vil flytte. Se efter **Skift App Service planlægge**på menuen **Indstillinger** .

**Ændre App-serviceaftale** åbnes vælgeren **App-serviceaftale** . På dette tidspunkt du enten vælge et eksisterende plan eller oprette en ny. Kun gyldige planer (i den samme ressourcegruppe og geografiske placering), vises.

![App Service plan Datavælger.][change]

Hver plan indeholder sin egen priser niveau. For eksempel, når du flytter et websted fra et gratis trin til et almindeligt trin din app nu kan bruge alle de funktioner og ressourcer af Standard-niveauet.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Klone en app til en anden App Service-plan
Hvis du vil flytte appen til et andet område, en alternativ er app klone. Klone opretter en kopi af din app i en ny eller eksisterende App Service-plan eller App Service miljø i et område.

 ![Klone en app.][appclone]

Du kan finde **Klon App** i menuen **Funktioner** .

Klone har nogle begrænsninger, som du kan læse om på [Azure App Service App klone Azure portalen](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Skalere et App-serviceaftale

Der er tre måder at tilpasse en plan:

- **Ændre planen priser niveau**. For eksempel en plan inden for det grundlæggende niveau kan konverteres til et Standard- eller Premium trin, og alle apps, der er knyttet til denne plan nu kan bruge de funktioner, der indeholder den nye webtjenesten.
- **Ændre den plan forekomst størrelse**. Som et eksempel, kan en plan inden for de grundlæggende trin, der bruger small forekomster ændres for at bruge store forekomster. Alle apps, der er knyttet til denne plan nu kan bruge den ekstra hukommelse og CPU-ressourcer, der giver større forekomst.
- **Ændre den plan forekomst Tæl**. En almindelig plan, der skaleres til tre forekomster kan for eksempel skaleres til 10 forekomster. En Premium plan kan skaleres ud til 20 forekomster (underlagt tilgængelighed). Alle apps, der er knyttet til denne plan nu kan bruge den ekstra hukommelse og CPU-ressourcer, der giver større forekomst antallet.

Du kan ændre den priser niveau og forekomst størrelse ved at klikke på elementet, der **Skala op** under indstillinger for appen eller App-serviceaftale. Ændringer gælder for App serviceaftale og påvirker alle apps, der er den vært.

 ![Angiv værdier for at skalere op en app.][pricingtier]

## <a name="app-service-plan-cleanup"></a>App-tjenesten planlægge oprydning
**App Service planer** , der har ingen apps, der er knyttet til dem betale stadig gebyrer, da de fortsat reserver Beregn kapaciteten konfigureret i App Service plan skala egenskaberne.
For at undgå uventede gebyrer, når den sidste-app hostet i en App-serviceaftale slettes, slettes den resulterende tomme App serviceaftale også.


## <a name="summary"></a>Oversigt

App tjenesteplanerne repræsenterer et sæt af funktioner og kapacitet, som du kan dele på tværs af dine apps. App Service planer giver dig mulighed for at tildele bestemte apps til et sæt ressourcer og yderligere optimere din Azure ressource anvendelsen. Denne metode, hvis du vil gemme penge på dit testmiljø, kan du dele en plan på tværs af flere apps. Du kan også maksimere overførselshastighed til dit produktionsmiljø ved at skalere det på tværs af flere områder og planer.

## <a name="whats-changed"></a>Hvad er ændret

* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png
