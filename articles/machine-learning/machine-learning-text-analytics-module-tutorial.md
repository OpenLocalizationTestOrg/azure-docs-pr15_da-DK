<properties
    pageTitle="Oprette tekst analytics-modeller i Azure Machine Learning Studio | Microsoft Azure"
    description="Sådan oprettes tekst analytics-modeller i Azure Machine Learning Studio ved hjælp af moduler for tekst forbehandling, N-g eller funktion krypteres"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Oprette tekst analytics-modeller i Azure Machine Learning Studio

Du kan bruge Azure Machine Learning til at opbygge og giver muligheder for tekst analytics-modeller. Disse modeller kan hjælpe dig med at løse, for eksempel dokument klassifikation eller synspunkt analyse problemer.

I en tekst analytics forsøg vil du typisk:

 1. Rydde og forbehandle tekst datasæt
 2. Udtrække numeriske funktion vektorer fra før behandlede tekst
 3. Tog klassifikation eller regression model
 4. Resultat og validere modellen
 5. Udrulle modellen i fremstilling

I dette selvstudium lærer du disse trin som vi gennemgå en synspunkt analyse-model ved hjælp af Amazon adressekartotek anmeldelser datasæt (se dette opslagsdokument "biografier, Bollywood, udligger bokse og Blenders: domæne tilpasning om synspunkt klassifikation" ved John Blitzer, Markér Dredze og Fernando Pereira; Tilknytning af Computational Linguistics (ACL) 2007.) Dette dataset består af Gennemse resultater (1-2 eller 4-5) og en gratis formular tekst. Målet er at forudsige Gennemse karakteren: Lav (1 - 2) eller fra højt (4-5).

Du kan finde forsøg dækket i dette selvstudium i Cortana Intelligence galleri:

[Forudsige adressekartotek anmeldelser] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Forudsige adressekartotek anmeldelser - skønnet forsøg] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Trin 1: Rydde og forbehandle tekst datasæt

Vi starter forsøget ved at opdele Gennemse resultater i kategoriske nedre og øvre filsæt udforme problemet som to klasse klassifikation. Vi bruger [redigere Metadata] (https://msdn.microsoft.com/library/azure/dn905986.aspx) og [gruppe kategoriske Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx) moduler.

![Oprette etiket](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Vi rydde derefter den tekst, ved hjælp af [forbehandle tekst] (https://msdn.microsoft.com/library/azure/mt762915.aspx) modul. Den rydning af unødvendig reducerer støj i datasættet, kan du finde de vigtigste funktioner og forbedre nøjagtigheden af den endelige model. Vi fjerne stopwords - almindelige ord som "" eller "a" - og tal, specialtegn, dublerede tegn, e-mail-adresser og URL-adresser. Vi også konvertere tekst til små bogstaver, lemmatize ord, og registrere sætning grænser, der derefter angives af "|||" symbol i allerede behandlede tekst.

![Forbehandle tekst](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Hvad nu, hvis du vil bruge en brugerdefineret liste over stopwords? Du kan overføre det i som valgfrit input. Du kan også bruge brugerdefinerede C# syntaksen regulære udtryk til at erstatte understrenge og fjerne ord ved ordklasse: navneord, verber eller adjektiver.

Når den forbehandling er fuldført, kan vi opdele dataene i tog og teste sæt.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Trin 2: Udtrække numerisk funktion vektorer fra før behandlede tekst

For at opbygge en model til tekstdata, har du typisk konverterer numerisk funktion vektorer ledig formular tekst. I dette eksempel skal vi bruge [udtrække N-g-funktioner fra tekst] (https://msdn.microsoft.com/library/azure/mt762916.aspx) modul til at transformere tekstdata til sådanne format. Dette modul tager en kolonne med adskilt af mellemrum ord og beregner en ordbog med ord eller N-g ord, der vises i dit datasæt. Den tæller, hvor mange gange hver word eller N-g, vises i hver post og opretter funktion vektorer fra dem, der tæller. I dette selvstudium angivet vi N-g størrelse til 2, så vores funktion vektorer indeholder enkeltord og kombinationer af to efterfølgende ord.

![Udtrække N-g](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Vi anvende TF * IDF (udtryk frekvens Inverse dokument frekvens) vægtning med N-g tæller. Denne metode tilføjer tykkelse ord, der vises ofte i en enkelt post, men er sjældne på tværs af hele datasættet. Andre indstillinger omfatter binær, TF, og afbilde vægt.

Sådan tekstfunktioner har ofte høj dimensionalitet. Eksempelvis hvis din indekserede har 100.000 entydige ord, ville dit funktion space have 100.000 dimensioner eller mere Hvis N-g anvendes. Modulet udtrække N-g funktioner giver dig et sæt af indstillinger for at reducere dimensionalitet. Du kan vælge at udelade ord, som kort eller lang, eller også usædvanlige eller for hyppige have betydeligt skønnet værdi. I dette selvstudium udelade vi N-g, der vises i færre end 5 poster eller i mere end 80% af poster.

Du kan også bruge valg af funktion til at vælge kun disse funktioner, der er den mest forbundne med din forudsigelse mål. Vi bruger valg af chi2-funktion til at vælge 1000 funktioner. Du kan få vist ordliste markerede ord eller N-g ved at klikke på højre output Udpak N-g modulet.

Som en alternativ metode til at bruge udtrække N-g-funktioner, kan du bruge funktionen krypteres modul. Bemærk selvom, [funktion krypteres] (https://msdn.microsoft.com/library/azure/dn906018.aspx) ikke har indbygget funktion markering funktioner eller TF * IDF vejer.

## <a name="step-3-train-classification-or-regression-model"></a>Trin 3: Undervise klassifikation eller regression model

Nu er blevet transformeret teksten til numeriske funktion kolonner. Datasættet stadig indeholder streng kolonner fra forrige faser, så vi bruge Vælg kolonner i Dataset til at udelukke dem.

Vi bruger derefter [to klasse logistisk Regression] (https://msdn.microsoft.com/library/azure/dn905994.aspx) for at forudsige vores mål: Høj eller lav Gennemse resultat. På dette tidspunkt er blevet transformeret tekst analytics problemet til et normalt klassifikation problem. Du kan bruge værktøjerne i Azure Machine Learning til at forbedre modellen. Du kan for eksempel eksperimentere med forskellige klassificeringer til at finde ud af, hvordan præcise resultater, de giver, eller brug hyperparameter tilpasning for at forbedre nøjagtigheden.

![Tog og resultat](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Trin 4: Resultat og validere modellen

Hvordan vil du validerer erfaren modellen? Vi resultat mod test datasættet og evaluerer nøjagtigheden. Dog lært modellen ordliste af N-g og deres vægt fra kursus datasættet. Derfor skal vi bruge pågældende ordliste og disse tykkelser når uddrage funktioner fra testdata, i modsætning til at oprette ordliste etnyt. Vi tilføje udtrække N-g funktioner modul til vurdering gren i forsøget derfor, Tilslut output ordliste fra kursus gren, og du kan angive tilstanden Ordliste til skrivebeskyttet tilstand. Vi også deaktivere filtrering af N-g ud fra hyppighed ved at angive minimum til 1 forekomst og maksimum til 100%, og Deaktiver indstillingen funktion.

Når tekstkolonnen i testdata transformeret til numerisk funktion kolonner, udelukke vi kolonnerne streng fra forrige faser som i kursus gren. Vi bruger derefter karakteren Model modul til at forudsige og evaluere Model modul til at evaluere nøjagtigheden.

## <a name="step-5-deploy-the-model-to-production"></a>Trin 5: Udrulle modellen i fremstilling

Modellen er næsten klar til at være installeret på fremstilling. Når installeret som webtjeneste, det tager ledig formular tekststreng som input og returnere en forudsigelse "Lav" eller "høj". Erfaringer N-g-ordliste bruges til at konvertere teksten til funktioner og erfaren logistisk regressionsmodel for at sikre en forudsigelse fra disse funktioner. 

For at konfigurere skønnet forsøget skal gemme vi først N-g-ordliste som datasæt samt erfaren logistisk regressionsmodellen fra kursus gren af forsøget. Vi Gem forsøget bruger "Gem som" for at oprette en forsøg graf for skønnet forsøg. Vi fjerne modulet opdelte Data og uddannelse gren fra forsøget. Vi derefter oprette forbindelse til tidligere gemt N-g ordliste og model til udtrække N-g-funktioner og karakteren Model moduler, henholdsvis. Vi også fjerne modulet evaluere Model.

Vi Indsæt Vælg kolonner i Dataset modul før forbehandle tekst modul til at fjerne kolonnen navn, og fjern markeringen af indstillingen "Tilføj karakteren kolonne til dataset" i karakteren modul. På denne måde webtjenesten ikke anmoder om det navn, den forsøger at forudsige og bruges ikke ekkoet input funktioner i svaret.

![Skønnet forsøg](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Vi har nu et forsøg, der kan publiceres som en webtjeneste og kaldes ved hjælp af udførelse af anmodning om svar eller batchen API'er.

## <a name="next-steps"></a>Næste trin

Få mere at vide om tekst analytics moduler fra [MSDN dokumentation] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
