<properties 
    pageTitle="Use Case - kunde profiler" 
    description="Få mere at vide, hvordan Azure Data Factory bruges til at oprette en databaserede-arbejdsproces (pipeline) for at profil gaming kunder." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>Use Case - kunde profiler

Azure Data Factory er et af mange tjenester, der bruges til at implementere Cortana Intelligence pakke med løsningsacceleratorer.  Du kan finde flere oplysninger om Cortana Intelligence besøge [Cortana Intelligence pakke](http://www.microsoft.com/cortanaanalytics). I dette dokument beskriver vi en enkelt use case for at hjælpe dig med at komme i gang med at forstå, hvordan Azure Data Factory kan løse almindelige problemer med analyser.

Alt, du har brug for at få adgang til, og prøv at bruge denne enkle Brug store og små bogstaver er et [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/).  Du kan installere en stikprøve, der implementerer denne Brug store og små bogstaver ved at følge trinnene beskrevet i artiklen [eksempler](data-factory-samples.md) .

## <a name="scenario"></a>Scenarie

Contoso er en gaming virksomhed, der opretter spil til flere platforme: konsoller, hånd, der afholdes enheder og pc'er (pc'er). Som afspillere spiller, er store mængder data i produceret, der registrerer brugsmønstre, gaming typografien og indstillinger for brugeren.  Når kombineres med demografiske, internationale og produktdata, Contoso kan udføre analyser for at hjælpe dem om, hvordan du kan forbedre dit oplevelse og mål dem til opgraderinger og i game køb. 

Contosos mål er at identificere sælge/kryds-sælge salgsmuligheder på baggrund af gaming oversigten over dens afspillere og føje overbevisende funktioner til drev virksomhedens vækst og give en bedre oplevelse kunder. For denne use case bruger vi en gaming virksomhed som et eksempel på en virksomhed. Virksomheden ønsker at optimere dets spil baseret på dit problem. Disse principper gælder for enhver virksomhed, som ønsker at deltage sine kunders omkring dens varer og tjenesteydelser og forbedre deres kunders oplevelse.

## <a name="challenges"></a>Udfordringer


## <a name="solution-overview"></a>Oversigt over løsning

Dette simple Brug tilfælde kan bruges som et eksempel på, hvordan du kan bruge Azure Data Factory til indtager, forberede, transformere, analysere og udgive data.

![Til slut arbejdsproces](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Denne figur viser, hvordan data rørledninger vises i portalen Azure, når de er blevet installeret.

1.  **PartitionGameLogsPipeline** læser rå game begivenheder fra blob-lager og opretter partitioner, der er baseret på år, måned og dag.
2.  **EnrichGameLogsPipeline** sammenkæder partitioneret game begivenheder med geografisk kode referencedata og beriger dataene ved at knytte IP-adresser til de tilsvarende geografisk-placeringer.
3.  **AnalyzeMarketingCampaignPipeline** pipeline bruger den forbedrede data og behandler den med dataene, reklamer til at oprette det endelige resultat, der indeholder marketing kampagnens effektivitet.

I dette eksempel bruges Data Factory til at dirigerer aktiviteter, kopiere inputdataene, transformation og proces dataene, og Medtag den endelige data til en Azure SQL-Database.  Du kan også visualisere data rørledninger netværket, administrere dem og overvåge deres status fra Brugergrænsefladen.

## <a name="benefits"></a>Fordele

Gaming virksomhed er ved at optimere deres bruger profil analyser og justering med virksomhedsmål, kan du hurtigt indsamle brugsmønstre og analysere effektiviteten af dens marketingkampagner.




