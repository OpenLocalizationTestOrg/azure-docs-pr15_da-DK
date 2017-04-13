<properties
    pageTitle="Trin 2: Overføre data til en Machine Learning forsøg | Microsoft Azure"
    description="Trin 2 i udarbejde en skønnet løsning gennemgang: Overfør gemt offentligt tilgængelige data til Azure Machine Learning Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Gennemgang trin 2: Overføre eksisterende data til et Azure Machine Learning forsøg

Dette er det andet trin i denne gennemgang, [udvikle en skønnet analytics-løsning i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Overføre eksisterende data**
3.  [Oprette en ny forsøg](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Uddannelse og evaluerer modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Installere webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Få adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)

----------

Hvis du vil udvikle en skønnet model for kredit risikoen, har vi brug for data, som vi kan bruge til at undervise og derefter teste modellen. I denne gennemgang skal bruger vi "UCI Statlog (tysk kredit Data) datasættet" fra UCI Machine Learning-lager. Du kan finde den her:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ml/DataSets/Statlog+(German+Credit+data)</a>

Vi bruger filen med navnet **german.data**. Hent denne fil på din lokale harddisk.  

Dette dataset indeholder rækker med 20 variabler for 1000 tidligere søgte kreditkontrol. Disse 20 variabler repræsenterer det dataset funktion vektor, som indeholder identificerende egenskaber for hver kredit ansøger. En ekstra kolonne i hver række repræsenterer den ansøger beregnede kreditrisiko med 700 ansøgere identificeret som en lav kreditrisiko og 300 som en høj risiko.

Webstedet UCI indeholder en beskrivelse af egenskaber for funktion vektoren, som omfatter økonomiske oplysninger, kredithistorik, ansættelsesstatus og personlige oplysninger. For hver ansøger en binær bedømmelse er blevet givet angiver, om de er en lav eller høj kredit risikoen.  

Vi bruger disse data til at undervise en skønnet analytics-model. Når vi er færdig, skal vores model kunne acceptere oplysninger for nye personer og forudsige om de er en kreditkontrol lav eller høj risiko.  

Her er et interessant drejning. Beskrivelse af datasættet forklares det, der misclassifying en person, som en lav kreditrisiko, når de er faktisk en høj risiko er 5 gange mere dyrt til den finansielle institution end misclassifying en lav kreditrisiko som høj. En enkelt metode til at tage hensyn i vores forsøg er ved at duplikere (5 gange) disse poster, der repræsenterer en person med en høj risiko. Derefter, hvis modellen misclassifies en høj risiko som lav, det vil gøre misclassification 5 gange én gang for hver Dupliker. Dette vil øge omkostningerne for denne fejl i kursus resultaterne.  

##<a name="convert-the-dataset-format"></a>Konvertere formatet datasæt
Det oprindelige datasæt bruger et tomt adskilt format. Machine Learning Studio fungerer bedre med en fil med kommaseparerede værdier (CSV), så vi kan konvertere datasættet ved at erstatte mellemrum med kommaer.  

Der findes mange måder til at konvertere disse data. En metode er at bruge følgende Windows PowerShell-kommandoen:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

En anden måde er ved hjælp af kommandoen Unix sed:  

    sed 's/ /,/g' german.data > german.csv  

I begge tilfælde, har vi oprettet en fil med semikolonseparerede version af dataene i en fil med navnet **german.csv** , som vi bruger i vores forsøg.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Overføre datasættet til Machine Learning Studio

Når dataene er blevet konverteret til en CSV-fil, har vi brug at overføre den til Machine Learning Studio. Du kan finde flere oplysninger om at komme i gang med Machine Learning Studio, [Microsoft Azure Machine Learning Studio hjem](https://studio.azureml.net/).

1.  Åbn Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). Når du bliver bedt om at logge på, kan du bruge den konto, du har angivet som ejer af arbejdsområdet.
1.  Klik på fanen **Studio** øverst i vinduet.
1.  Klik på **+ Ny** nederst i vinduet.
1.  Vælg **DATASÆT**.
1.  Vælge **fra lokal fil**.
1.  Klik på **Gennemse** i dialogboksen **overføre et nyt datasæt** , og find den **german.csv** fil, du har oprettet.
1.  Angiv et navn til datasættet. I denne gennemgang skal vi kalder det "UCI tysk kreditkort-Data".
1.  Datatype, Vælg **generisk csv-fil uden overskrift (. nh.csv)**.
1.  Tilføje en beskrivelse, hvis du vil have.
1.  Klik på **OK**.  

![Overføre datasættet][1]  


Dette overfører dataene i et datasæt modul, der kan vi bruge i et forsøg.

> [AZURE.TIP] For at administrere datasæt, du har overført til Studio skal du klikke på fanen **DATASÆT** til venstre i vinduet Studio.

Se [importere dataene kursus til Azure Machine Learning Studio](machine-learning-data-science-import-data.md)kan finde flere oplysninger om at importere forskellige typer data i et forsøg.

**Næste: [Opret en ny forsøg](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
