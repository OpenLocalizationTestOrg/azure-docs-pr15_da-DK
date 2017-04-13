<properties 
    pageTitle="Almindelige handlinger i Machine Learning anbefalinger API | Microsoft Azure" 
    description="Azure ML anbefaling Northwind" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Anbefalinger API eksempel programmet gennemgang

>[AZURE.NOTE]Du skal begynde at bruge tjenesten anbefalinger API Kognitiv i stedet for denne version. Tjenesten anbefalinger Kognitiv vil erstatte denne tjeneste, og alle de nye funktioner udvikles der. Der er nye funktioner som "samling" support, en bedre API Explorer, en renere API grundflade, mere ensartet tilmelding/fakturering oplevelse, osv.
> Lær mere om [overførsel til den nye Kognitiv tjeneste](http://aka.ms/recomigrate)

##<a name="purpose"></a>Formål

Dette dokument viser brugen af Azure Machine Learning anbefalinger API via et [Northwind](https://code.msdn.microsoft.com/Recommendations-144df403).

Dette program er ikke beregnet til at medtage fuld funktionalitet og anvender den alle API'erne. Det viser nogle almindelige handlinger skal udføres, når du først vil lege med anbefalingstjenesten Machine Learning. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Introduktion til Machine Learning anbefalingstjenesten

Anbefalinger via anbefalingstjenesten Machine Learning er aktiveret, når du opretter en anbefaling model, der er baseret på de følgende data:

* Et lager af de elementer, du vil anbefale også kendt som et katalog
* Data, der repræsenterer brugen af elementer på hver bruger eller session (det kan være hentet over tid via dataindsamling ikke som en del af appen eksempel)

Når en anbefaling model er oprettet, kan du bruge det til at forudsige elementer, som en bruger kan være interesseret i, ud fra et sæt af elementer (eller et enkelt element) brugeren markerer.

For at aktivere det forrige scenario skal du gøre følgende i tjenesten Machine Learning anbefaling:

* Oprette en model: Dette er en logisk beholder, der indeholder data (katalog for og brugen) og tekstfuldførelse modellen eller modellerne. Hver model beholder identificeres via et entydigt ID, som er allokeret, når den er oprettet. Dette ID kaldes model-ID'ET, og den bruges af de fleste af API'erne. 
* Overfør til katalog: Når en model objektbeholder er oprettet, kan du knytte til den et katalog.

**Bemærk**: oprette en model og overføre til et katalog udføres normalt én gang for model livscyklussen.

* Overføre brugen: Dette tilføjer anvendelsesdata til objektbeholderen model.
* Oprette en anbefaling model: Når du har tilstrækkelig data, du kan oprette anbefaling modellen. Denne handling bruger de øverste Machine Learning algoritmer til at oprette en anbefaling model. Hver build er knyttet til et entydigt ID. Du vil bevare historikken for dette-ID, fordi det er nødvendigt for funktionaliteten i nogle API'er.
* Overvåge dokumentkomponent processen: et anbefaling model build er en asynkron handling, og det kan tage fra nogle minutter til flere timer, afhængigt af mængden data (katalog for og brugen) og parametrene build. Du skal derfor overvåge Opret. Kun, hvis dens tilknyttede build er fuldført, oprettes der en anbefaling model.
* (Valgfrit) Vælg et aktiv anbefaling model build: dette trin er kun nødvendigt, hvis du har mere end én anbefaling model, der er indbygget i din model objektbeholder. En anmodning om at få anbefalinger uden aktive anbefaling modellen, der angiver omdirigeres automatisk af systemet til det aktive build standard. 

**Bemærk**: en aktive anbefaling model er fremstilling klar og den er oprettet for fremstilling arbejdsbyrde. Dette er forskellig fra en ikke-aktiv anbefaling model, hvilket forbliver i en test-lignende miljø (også kaldet midlertidige).

* Få anbefalinger: Når du har en anbefaling model, du kan udløse anbefalinger til et enkelt element eller en liste over elementer, du vælger. 

Du kan normalt aktiverer få anbefaling for en bestemt tidsperiode. I denne periode, kan du omdirigerer anvendelsesdata til Machine Learning anbefaling systemet, hvilket tilføjer disse data til den angivne model beholder. Når du har tilstrækkelig Brugsdata, kan du oprette en ny anbefaling model, der indeholder ekstra Brugsdata. 

##<a name="prerequisites"></a>Forudsætninger

* Visual Studio 2013
* Adgang til internettet 
* Abonnement på anbefalinger API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Azure Machine Learning eksempel app-løsning

Denne løsning indeholder kildekode, eksempel på anvendelse, katalogfil og direktiver til at hente de pakker, der kræves til målgruppen.

##<a name="the-apis-used"></a>API'er, der bruges

Programmet bruger Machine Learning anbefaling funktionalitet via et undersæt af tilgængelige API'er. Følgende API'er vises i programmet på computeren:

* Oprette model: oprette en logisk beholder for at holde data og anbefaling modeller. En model, der er identificeret med et navn, og du kan ikke oprette mere end én model med samme navn.
* Overfør katalogfil: bruge til at overføre katalogdata.
* Overfør brugen fil: bruge til at overføre data om brug af.
* Udløse build: bruge til at oprette en anbefaling model.
* Overvåge udførelse af build: bruge til at overvåge statussen for et anbefaling model build.
* Vælg en indbygget model til anbefaling: bruge til at angive, hvilke anbefaling der skal bruges som standard til en bestemt model objektbeholder. Dette trin er nødvendigt, kun, hvis du har mere end én anbefaling model, og du vil aktivere et ikke-aktiv build som den aktive anbefaling model.
* Få anbefaling: bruge til at hente anbefalede elementer i overensstemmelse med en given enkelt element eller et sæt af elementer. 

En fuldstændig beskrivelse af API'erne, skal du se i dokumentationen til Microsoft Azure Marketplace. 

**Bemærk**: en model kan have flere builds over tid (ikke samtidigt). Hver build oprettes med den samme eller en opdateret katalog og data om yderligere brug.

## <a name="common-pitfalls"></a>Almindelige faldgruber

* Skal du angive dit brugernavn og din Microsoft Azure Marketplace primære konto for at køre appen eksempel.
* Køre appen eksempel fortløbende mislykkes. Programmet strømmen omfatter oprette, overføre, opbygning af skærmen og få anbefalinger fra en foruddefineret model derfor mislykkes det på fortløbende udførelse af Hvis du ikke ændrer Modelnavnet mellem aktivering.
* Anbefalinger kan returnere uden data. Appen eksempel bruger en meget lille katalog for og brugen fil. Derfor har nogle elementer fra kataloget ingen anbefalede elementer.

## <a name="disclaimer"></a>Ansvarsfraskrivelse
Eksempel-app er ikke beregnet til at blive kørt i et produktionsmiljø. De data, der er angivet i kataloget er meget lille, og får ikke en sigende anbefaling model. Data, der er angivet en demonstration. 
 
