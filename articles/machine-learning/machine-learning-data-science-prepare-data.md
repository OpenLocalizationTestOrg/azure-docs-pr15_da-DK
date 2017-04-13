<properties
    pageTitle="Opgaver til at forberede data til forbedring af den machine learning | Microsoft Azure"
    description="Udfyldt procesfarver og Ryd data til at forberede machine learning."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Opgaver til at forberede data til forbedring af den machine learning

Forbehandling af og rydning af unødvendig data er vigtige opgaver, der typisk skal udføres før datasæt kan bruges effektivt til machine learning. Rækkedata er ofte støj og upålidelig og muligvis mangler værdier. Ved hjælp af disse data til modellering kan medføre vildledende resultater. Disse opgaver er en del af Team Data videnskabelige proces (TDSP) og følge typisk en indledende udforskning af et datasæt, der bruges til at finde og planlægge forbehandling påkrævet. Du kan finde mere detaljerede oplysninger om TDSP processen, trinnene beskrevet i [Team Data videnskabelige proces](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Forbehandling og rydning af unødvendig opgaver som at udforske data opgaven kan udføres i en lang række miljøer, såsom SQL eller Hive eller Azure Machine Learning Studio og med forskellige værktøjer og sprog, som R eller Python, afhængigt af hvor dine data er gemt og formateringen. Da TDSP er gentaget karakter, kan disse opgaver finde sted på forskellige trin i arbejdsprocessen over processen.

I denne artikel introducerer forskellige databehandling begreber og opgaver, der kan foretages, enten før eller efter ingesting data til Azure Machine Learning.

Du kan finde et eksempel på data udforske og forbehandling færdig i Azure Machine Learning studio [forbehandling af data i Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videoen.


## <a name="why-pre-process-and-clean-data"></a>Hvorfor allerede behandle og rydde data?

Virkelige data indsamles fra forskellige kilder og processer, og det kan indeholde uregelmæssigheder eller beskadigede data bringe kvaliteten af datasættet. De typiske data kvalitetsproblemer, der opstår er:

* **Ufuldstændig**: Data mangler attributter, eller der indeholder manglende værdier.
* **Noisy**: Data indeholder nogen forkerte poster eller outliers.
* **Inconsistent**: Data indeholder uoverensstemmende poster eller uoverensstemmelser.

Kvalitetsdata er en betingelse for kvalitet skønnet modeller. For at undgå "garbage i garbage ud" og forbedre datakvaliteten på, og derfor model ydeevnen, er det nødvendigt at udføre en data sundhed skærmen for at få øje på problemer med data tidligt og beslutter, at tilsvarende databehandling og markedet trin.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Hvad er nogle typiske data sundhed skærmbilleder, der er ansat?

Vi kan kontrollere generelle kvaliteten af data ved at markere:

* Antallet af **poster**.
* Antallet af **attributter** (eller **Funktioner**).
* Attributten **datatyper** (nominel, ordenstal eller kontinuert).
* Antallet af **manglende værdier**.
* **Hul formedness** data.
    * Hvis dataene er i TSV eller csv-, kan du kontrollere, at kolonnen separatorer og linje separatorer altid korrekt adskille kolonner og linjer.
    * Hvis dataene er i HTML- eller XML-format, skal du kontrollere, om dataene er udformet korrekt baseret på deres respektive standarder.
    * Parsing af kan også være nødvendigt at udtrække strukturerede oplysninger fra semistrukturerede eller ustrukturerede data.
* **Inkonsekvent dataposter**. Markér området af værdier er tilladt. f.eks. Hvis data, der indeholder student GPA, afkrydsningsfelt, hvis GPA er i det angivne område sig 0 ~ 4.

Når du finder problemer med data, **trin til behandling** er nødvendige som omfatter ofte rydning af unødvendig manglende værdier, datanormalisering, discretization, tekst behandling for at fjerne og/eller erstatte integrerede tegn, som kan påvirke datajusteringen, blandede datatyper fælles felter og andre.

**Azure Machine Learning forbruger korrekt udformet tabeldata**.  Hvis dataene allerede er i tabuleret form, kan data forbehandling udføres direkte med Azure Machine Learning i Machine Learning Studio.  Hvis dataene ikke er i tabelformat, sig det er i XML, kan det være nødvendigt at konvertere dataene til tabelform parsing af.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Hvad er nogle af de vigtigste opgaver i data forbehandling?

* **Data rydning af unødvendig**: Udfyld eller manglende værdier, finde og fjerne støj data og outliers.
* **Datatransformation**: normalisere data for at reducere dimensioner og støj.
* **Reduktion af data**: eksempel dataposter eller attributter for nemmere datahåndtering.
* **Data discretization**: konvertere fortløbende attributter til kategoriske attributter for nemmere at bruge med bestemte machine learning metoder.
* **Rydde tekst**: fjerne integrerede tegn, som kan medføre data justeret, f.eks., integrerede faner i en tabulatorsepareret datafil, integrerede nye linjer, som kan muligvis ødelægge poster osv.

I nedenstående afsnit beskrives nogle af disse databehandling trin.

## <a name="how-to-deal-with-missing-values"></a>Hvordan du håndterer manglende værdier?

For at håndtere manglende værdier, er det bedst at først at identificere årsagen til de manglende værdier, der bedre håndtaget problemet. Typisk manglende værdi håndtering af metoder er:

* **Sletning**: fjerner poster med manglende værdier
* **Dukke erstatning**: erstatte manglende værdier med en dummy værdi: f.eks., _Ukendt_ for kategoriske eller 0 for numeriske værdier.
* **Betyder erstatning**: Hvis de manglende data er numeriske, erstatte de manglende værdier med middelværdi.
* **Hyppige erstatning**: Hvis de manglende data er kategoriske, erstatte de manglende værdier med den hyppigste element
* **Regression erstatning**: Brug en regression metode til at erstatte manglende værdier med vendt tilbage til værdier.  

## <a name="how-to-normalize-data"></a>Sådan normalisere data?

Datanormalisering skaleres igen numeriske værdier til et bestemt område. Populære data normalisering metoder omfatter følgende:

* **Min Maks normalisering**: lineært transformere dataene til et område, sig mellem 0 og 1, hvor minimumværdien skaleres til 0 og Maks værdi til 1.
* **Z karakteren normalisering**: tilpasse data, der er baseret på middelværdi og standardafvigelse: dividere forskellen mellem dataene og gennemsnittet af standardafvigelsen.
* **Decimal skalering**: skalere dataene ved at flytte decimaltegnet i attributværdien.  

## <a name="how-to-discretize-data"></a>Sådan discretize data?

Data kan være discretized ved at konvertere fortløbende værdier til nominel attributter eller intervaller. Der er nogle måder at gøre dette:

* **Der er lig med bredde Binning**: opdele området af alle mulige værdier for attributten i N grupper af samme størrelse, og Tildel de værdier, der falder på en placering med intervaltal.
* **Der er lig med-højde Binning**: opdele området af alle mulige værdier for attributten i N grupper, hver med det samme antal forekomster, og derefter tildele de værdier, der falder på en placering med intervaltal.  

## <a name="how-to-reduce-data"></a>Hvordan du kan reducere data?

Der findes forskellige metoder til at reducere størrelsen af dataene til nemmere data håndtering af. Afhængigt af datastørrelse og domænenavn, kan de følgende metoder anvendes:

* **Post udvalg**: eksempel på dataposter og vælge kun repræsentant undersættet dataene.
* **Attributten udvalg**: Vælg kun et undersæt af de vigtigste attributter dataene.  
* **Sammenlægning**: opdele dataene i grupper og gemme tal for hver gruppe. Daglig omsætning numrene på en restaurant kæde de seneste 20 år kan for eksempel være sammenlagt månedlige indtægter at reducere størrelsen af dataene.  

## <a name="how-to-clean-text-data"></a>Hvordan du kan rydde op i tekstdata?

**Tekstfelter i tabeldata** kan omfatte tegn, som påvirker kolonner justering og/eller post grænser. Til f.eks., integreret faner i en tabulatorsepareret fil årsag kolonne uoverensstemmelse, og integreret ødelægge ny linje-tegn post linjer. Forkert tekst kodning håndtering under skrivning/læsning tekst fører til tab af data, utilsigtede Introduktion af kan ikke læses tegn, f.eks., null-værdier og kan også påvirker tekst parsing af. Forsigtig parsing og redigere kan det være nødvendigt for at rydde tekstfelter til stort justering og/eller til at udtrække strukturerede data fra ustrukturerede eller semistrukturerede tekstdata.

**Udforske data** indeholder en tidlig visning ned i dataene. En række problemer med data kan være uafdækkede under dette trin og tilsvarende metoder kan anvendes for at løse disse problemer.  Det er vigtigt at stille spørgsmål som Hvad er kilden til problemet, og hvordan problemet kan være blevet introduceret. Dette også hjælper dig med at beslutte, om databehandling trin, der har brug for at blive ført til at løse dem. Typen indsigt en har til hensigt at udlede fra dataene, kan også bruges til at prioritere databehandling indsats.

## <a name="references"></a>Referencer

>*Datamining: koncepter og teknikker*, tredje udgave, Jensen Kaufmann 2011, Jiawei Han, Micheline Kamber og Jian Pei
