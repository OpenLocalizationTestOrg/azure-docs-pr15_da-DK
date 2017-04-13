<properties
    pageTitle="Opbygning af en model med Recommnendations UI | Microsoft Azure"
    description="Azure Machine Learning anbefalinger - opbygning af en model med brugergrænsefladen i anbefalinger."
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
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Opbygning af en model med brugergrænsefladen i anbefalinger.

Dette dokument er en trinvis vejledning. Vores mål er at vejlede dig gennem de trin, der er nødvendige til at undervise en model, ved hjælp af [Anbefalinger brugergrænseflade](https://recommendations-portal.azurewebsites.net/).
Ved at følge opgave, kan du forstår processen med opbygning af en model, før du gør det fra et program. Det også oplægget du med Brugergrænsefladen, som er praktiske, når du begynder at bruge tjenesten.

Denne øvelse tager om 30 minutter.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Trin 1 - log i til anbefalinger brugergrænseflade ##

1. Hvis du allerede ikke har gjort det, skal du [tilmelding](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) til et nyt [Anbefalinger API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) -abonnement. Du kan tilmelde dig tjenesten på **Azure** på [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) og logge på med din Azure-konto. Der findes detaljerede anvisninger på tilmeldingsprocessen i *opgave 1* i [Introduktion til](cognitive-services-recommendations-quick-start.md) 

1. Når du har anskaffet en **nøgle** til dit abonnement, anbefalinger API, gå til [Anbefalinger brugergrænseflade](https://recommendations-portal.azurewebsites.net/). 

1. Log på portalen ved at angive produktnøglen i feltet **Kontonøgle** , og klik derefter på knappen til **logon** .

    ![Anbefalinger brugergrænseflade: Logon dialogboksen.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Trin 2 – Lad os indsamle nogle kursus data ##

Før du kan oprette et build, programmet kræver to dele af oplysninger: et katalogfil og en række brugen filer. 

Katalogfilen indeholder oplysninger om elementerne, du tilbyder til din kunde. En brugen fil indeholder oplysninger om, hvordan disse elementer bruges eller transaktioner fra din virksomhed.

Normalt ville du forespørge databasen store for disse elementer af oplysninger. I dag, har vi angivet nogle eksempeldata for dig, så du kan lære, hvordan du bruger API'EN anbefalinger.

Du kan hente data fra [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Kopiér og Pak filen **Books.Zip** til en anden mappe på din lokale computer. For eksempel **c:\data**.

Detaljerede oplysninger om skemaet for filerne, katalog for og brugen kan findes i artiklen [indsamling Data til at undervise din Model](cognitive-services-recommendations-collecting-data.md) .
 
Vi vil arbejde med en lille fil, så du ikke har meget længe vente kursus til denne opgave. Hvis du vil prøve en mere realistisk fil, har vi også placeret **MsStoreData.zip** , der indeholder eksempel transaktioner fra Microsoft Store i den [samme placering](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Trin 3 – oprette et projekt, og Overfør katalog og brugen af data ##

Når logføring i [Brugergrænsefladen i anbefalinger](https://recommendations-portal.azurewebsites.net/), kan du se siden projekter. Hvis du tidligere har oprettet en hvilken som helst projekter, bør du undersøge dem her.
Et projekt (også kaldet *en model* i referencen API) er en objektbeholder til dine katalog for og brugen data. Du kan oprette flere *opbygger* i projektet. Vi fører dig gennem processen i de næste trin.

1. Hvis du vil oprette et nyt projekt, skal du skrive navnet på tekstfeltet (noget som "MyFirstModel" er den rigtige) og klikke på **Tilføj projekt**.
 
    ![Anbefalinger Brugergrænsefladen: På siden projekter.][reco_projects]

1. Når projektet bliver oprettet, skal du klikke på knappen **Gennemse til filen** i afsnittet, **Tilføj en fil** . Overføre det katalog, du har fået i trin 2. Hvis du gemte den på *c:\data*, skal du gå til den pågældende mappe.

    ![Anbefalinger brugergrænseflade: Tilføje Data i et projekt.][reco_firstmodel]

1. Når kataloget er overført, kan du klikke på knappen **Gennemse til filen** i afsnittet, **Tilføj brugen filer** . Tilføje usage_large.txt filen.

> **Hvad nu, hvis jeg har en stor fil af Brugsdata?**
>
> Kun brugen filer, der er mindre end 200 MB kan overføres. Dog kan systemet indeholde til 2 GB værd transaktionsdata, så du kan overføre mere end én fil, hvis det er nødvendigt.
> Du skal muligvis ikke så mange data til at oprette en god model, det er ikke bare størrelsen på de data, som har betyder, men kvaliteten af dataene. Det er almindeligt at se Brugsdata, hvor de fleste af transaktionerne er lige på en håndfuld populære elementer, og der er "lidt signal" for andre elementer.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Trin 4 – Lad os gøre nogle kursus! ##

Nu, hvor du har overført både kataloget og brugerdata, er vi klar til at undervise programmet, så den kan lære mønstre fra vores data.

1.  Klik på knappen **Ny Generer** .

1.  Vælg **anbefalinger** som typen build. Bemærk, at vi understøtter en rangering opbygge og en FBT (ofte har købt sammen) oprette typer samt.

    ![Anbefalinger brugergrænseflade: Oprette dialogboksen.][reco_build_dialog.png]


    Et FBT build gør det muligt at identificere mønstre for produkter, der er som regel købt/consumed i samme transaktion.
    Et rangering build bruges til at identificere funktioner af interesse. 
    Vi kan ikke gå meget dybe til FBT eller rangering opbygger i denne workshop, men hvis du er interesseret skal du tjekke [opbygge filtyper og model kvalitet dokumentation side](cognitive-services-recommendations-buildtypes.md).

1. Klik på knappen **Generer** . Opret processen tager om fem minutter, hvis du bruger datatypen bøger. Det tager længere tid på større datasæt.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Trin 5 – Lad os finde ud af, hvad maskinen lært! ##

Når din build er fuldført, vil du bemærke et nyt build på listen builds. Dette build kan forespørges for elementet og bruger anbefalinger.

1. Når din build er fuldført, skal du klikke på **resultat**. Dette kan du afspille med modellen og se, hvilke elementer der anbefales.

    ![Anbefalinger brugergrænseflade: Karakteren knappen][reco_score_button]

1. Vælg et element for at se, hvilke elementer der skal returneres som anbefalinger for det pågældende element. Bemærk, at hvis der ikke er nok ledig transaktioner til at forudsige en anbefaling for et bestemt element, ikke returnerer systemet nogen anbefalinger for det pågældende element.  Hvis du har et element, der returnerer nogen anbefalinger eller anden grund, kan du prøve at vundne andre elementer.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Trin 6 - næste trin ##
Tillykke! Du har uddannelse en model og derefter har fået anbefalinger fra modellen.  Anbefalinger Brugergrænsefladen er et nyttigt værktøj, der gør det muligt at se status for dine projekter og opbygger. 

Nu hvor du har en model, kan du lære at gøre alle ovenstående trin fra et program. For at Lær at kalde API fra et program, inviterer vi dig til at kontrollere [Anbefalinger API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) og hente [Anbefalinger Northwind](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
