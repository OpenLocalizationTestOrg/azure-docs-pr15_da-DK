<properties 
    pageTitle="Konfigurere og bruge Machine Learning anbefalinger API | Microsoft Azure" 
    description="Microsoft anbefalinger API oprettet med Azure Machine Learning ofte stillede spørgsmål" 
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

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Konfigurere og bruge Machine Learning anbefalinger API ofte stillede spørgsmål


**Hvad er anbefalinger?**

>[AZURE.NOTE]Du skal begynde at bruge tjenesten anbefalinger API Kognitiv i stedet for denne version. Tjenesten anbefalinger Kognitiv vil erstatte denne tjeneste, og alle de nye funktioner udvikles der. Der er nye funktioner som "samling" support, en bedre API Explorer, en renere API grundflade, mere ensartet tilmelding/fakturering oplevelse, osv.
> Lær mere om [overførsel til den nye Kognitiv tjeneste](http://aka.ms/recomigrate)

ANBEFALINGER i Azure Machine Learning giver organisationer og virksomheder, der er afhænger af anbefalinger til tværs sælge og sælge produkter og tjenester til deres kunder ved en selvbetjening anbefalinger program. Det er en implementering af samarbejde om filtrering, der bruger matrix factorization som dens core algoritme. Programmet udviklere kan få adgang til anbefalinger ved hjælp af REST API'er. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Hvad kan jeg gøre med anbefalinger?**

ANBEFALINGER tager som input et element eller et sæt af elementer og returnerer en liste over relevante anbefalinger. For eksempel: en kunde i en online forhandler klikker på et produkt. Online forhandler sender produktet som input til anbefalinger, får en liste over produkter som svar og bestemmer, hvilke af disse produkter vises til kunden. Kan du bruge anbefalinger til at optimere din onlinebutik eller endda til at informere din indenfor salg afdeling eller call center.

**Er der nogen begrænsninger for brugen?**

Anbefalinger har følgende brugen begrænsninger:
* Maksimale antal modeller per abonnement: 10
* Maksimale antal elementer, der kan indeholde et katalog: 100.000
* Det maksimale antal brugen punkter, gemmes er ~ 5,000,000. Den ældste slettes om nye skal overføres eller rapporteret.
* Maksimumstørrelsen for data, der kan sendes i en mail (for eksempel Importér katalogdata, importere Brugsdata) er 200 MB
* Antallet af transaktioner i sekundet (Kreative) for et anbefalinger model build, der ikke er aktivt er ~ 2 Kreative. Et anbefalinger model build, der er aktive kan indeholde op til 20 Kreative.

##<a name="purchase-and-billing"></a>Køb og fakturering 


**Hvor meget koster anbefalinger i perioden, Start?**

Anbefalinger er en abonnementsbaseret tjeneste. Opladning er baseret på mængde transaktioner månedsbasis. Du kan se [siden] (https://datamarket.azure.com/dataset/amla/recommendations) i Microsoft Azure Marketplace til prisoplysninger.

**Er der de omkostninger, der er knyttet til har anbefalinger spore og lagre brugeraktivitet for mig?**

Ikke på nuværende tidspunkt.

**Har anbefalinger en gratis prøveversion?**

Der er en gratis prøveversion, som er begrænset til 10.000 transaktioner månedsbasis.

**Når jeg faktureres for anbefalinger?**

Et betalt abonnement er et abonnement, hvor der er et månedligt gebyr. Når du køber et betalt abonnement, betaler du straks til den første måned brug. Du betaler det beløb, der er knyttet til tilbud på abonnementssiden (plus gældende moms). Denne månedligt gebyr er gjort hver måned på den samme kalenderdato som dit oprindelige køb, indtil du annullerer abonnementet. 

**Hvordan opgraderer jeg til et højere niveau-tjenesten?**

Du kan købe eller opdatere dit abonnement fra [siden] (https://datamarket.azure.com/dataset/amla/recommendations) side på Microsoft Azure Marketplace.

Når du opgraderer et abonnement:

* Transaktioner, der tilbage på dit gamle abonnement er ikke føjet til dit nye abonnement. 
* Du betaler fulde pris til det nye abonnement, selvom du har ubrugte transaktioner på dit gamle abonnement.

Proces for at opgradere et abonnement:

* Nevigate til [siden] (https://datamarket.azure.com/dataset/amla/recommendations).
* Log på til Marketplace, hvis du ikke allerede er logget på.
* I højre rude vises alle de tilgængelige planer. Klik på alternativknappen for den plan, du vil opgradere til.
* Hvis du vil opgradere, skal du klikke på **OK**. Hvis du ikke vil opgradere, kan du klikke på **Annuller**.

**Vigtige** Læs omhyggeligt dialogboksen, før du opgraderer, fordi der er fakturerings- og brug konsekvenserne.

**Hvor ophører mit abonnement på anbefalinger?**

Dit abonnement afsluttes, når du annullerer den. Hvis du gerne vil annullere dine abonnementer, kan du se de følgende instruktioner.

**Hvordan kan jeg annullere mit abonnement anbefalinger?**

Hvis du vil annullere dit abonnement, skal du følge nedenstående trin. Hvis dit aktuelle abonnement er et betalt abonnement, fortsætter dit abonnement i kraft, indtil slutningen af den aktuelle faktureringsperiode. Hvis du skal annulleringen skal træde i kraft med det samme, skal du kontakte os på [Microsoft Support](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Bemærk!** Ingen refusion gives, hvis du annullerer før slutningen af en faktureringsperiode eller til ubrugte transaktioner i en faktureringsperiode.

* Gå til [siden] (https://datamarket.azure.com/dataset/amla/recommendations).
* Log på til Marketplace, hvis du ikke allerede er logget på.
* Klik på **Annuller** til højre for datasættet navnet og status. Du kan bruge dette abonnement indtil slutningen af den aktuelle faktureringsperiode eller din transaktion er nået (afhængigt af hvad der sker først).

Hvis du gerne vil annullere dit abonnement, straks, så du kan købe et nyt abonnement, kan du arkivere en brugertilladelse hos [Microsoft Support](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Introduktion til anbefalinger

**Er anbefalinger til mig?** 

Anbefalinger i Machine Learning er organisationer og virksomheder, der er afhænger af anbefalinger til tværs sælge og sælge produkter eller tjenester til deres kunder. Hvis du har et kunde-websted, en salgsstyrke, en indvendig salgsstyrke eller et callcenter, og hvis du tilbyder et katalog over mere end et par dozen produkter eller tjenester, nederste linje kan få glæde af ved hjælp af anbefalinger. 

At eksperimentere med anbefalinger er udviklet til at være ganske enkelt. Den aktuelle API-baseret version kræver grundlæggende programmering færdigheder. Hvis du har brug for hjælp, kan du kontakte den leverandør, der udviklet dit websted. Hvis du har en intern IT-afdeling eller et internt udvikler, skal de kunne få anbefalinger til at arbejde for dig. 

**Hvad er forudsætningerne til konfiguration af anbefalinger?**

Anbefalinger kræver, at du har en logfil over brugernes valgmuligheder, som relaterer til dit katalog. Hvis du ikke har en logfil over sådanne og du har et kunde modstående websted, anbefalinger kan indsamle brugeraktivitet for dig. 

Anbefalinger kræver også et katalog over dine produkter eller tjenester. Hvis du ikke har kataloget, anbefalinger kan bruge anvendelsesdata faktisk kunde og Filtrer et katalog. Et implicit katalog skal ikke medtage elementer, der ikke er rapporteret som en del af brugeren transaktioner.

**Hvordan konfigurerer jeg anbefalinger for første gang?**

Efter [abonnement] (https://datamarket.azure.com/dataset/amla/recommendations) til anbefalinger, skal du bruge dokumentationen til API i [Azure Machine Learning anbefalinger Startvejledning](machine-learning-recommendation-api-quick-start-guide.md) til konfiguration af tjenesten.

**Hvor kan jeg finde API dokumentation?** 

I dokumentationen til API er [Azure Machine Learning anbefalinger guiden Hurtig Start](machine-learning-recommendation-api-quick-start-guide.md).

**Hvilke muligheder har jeg overføre katalog og anvendelsesdata til anbefalinger?**

Har du to muligheder for overførsel af dataene katalog for og brugen: Du kan eksportere dataene fra din CRM-systemet eller andre logfiler og overføre den til anbefalinger, eller du kan føje mærker til dit websted, der skal spore brugeraktiviteter. Hvis du bruger denne metode, gemmes data i Azure.

##<a name="maintenance-and-support"></a>Vedligeholdelse og support

**Hvor stor kan min datasæt være?**

Hvert datasæt kan indeholde op til 100.000 katalogelementer og op til 2048 MB Brugsdata.
Desuden kan et abonnement indeholde op til 10 datasæt (modeller).

**Hvor kan jeg få teknisk support til anbefalinger?**

Teknisk support findes på webstedet [Microsoft Azure understøtter](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Hvor kan jeg finde vilkårene for anvendelse?**

[Microsoft Azure Machine Learning anbefalinger API servicebetingelser for](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 
