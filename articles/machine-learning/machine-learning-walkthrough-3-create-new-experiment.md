<properties
    pageTitle="Trin 3: Opret en ny computer Learning forsøg | Microsoft Azure"
    description="Trin 3 af udarbejde en skønnet løsning gennemgang: oprette en ny kursus forsøg i Azure Machine Learning Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Gennemgang trin 3: Opret en ny Azure Machine Learning forsøg

Dette er det tredje trin i denne gennemgang, [udvikle en skønnet analytics-løsning i Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Oprette et Machine Learning arbejdsområde](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Overføre eksisterende data](machine-learning-walkthrough-2-upload-data.md)
3.  **Oprette en ny forsøg**
4.  [Uddannelse og evaluerer modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Installere webtjenesten](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Få adgang til webtjenesten](machine-learning-walkthrough-6-access-web-service.md)

----------

Næste trin i denne gennemgang er at oprette et forsøg i Machine Learning Studio, der bruger vi har overført datasættet.  

1.  I Studio, skal du klikke på **+ Ny** nederst i vinduet.
2.  Vælg **forsøg**, og vælg derefter "Tom forsøg". Vælg forsøg standardnavn øverst på lærredet og Omdøb den til et sigende navn

    > [AZURE.TIP] Det er en god ide at udfylde **Oversigt** og **Beskrivelse** for forsøget i ruden **Egenskaber** . Disse egenskaber giver dig mulighed for at dokumentere forsøget, så alle, der ser ud på den senere vil kunne forstå dine mål og metoder.

3.  Lærredet i paletten modul til venstre for forsøget, udvide **Gemt datasæt**.
4.  Finde de datasæt, du oprettede under **Mine datasæt** , og træk den til lærredet. Du kan også finde datasættet ved at skrive navnet i feltet **Søg** over paletten.  

##<a name="prepare-the-data"></a>Forbered dataene
Du kan se de første 100 rækker af data og nogle statistiske oplysninger for hele datasættet ved at klikke på outputporten af datasættet (den lille cirkel nederst) og vælge **visuelle effekter**.  

Fordi datafilen ikke blev leveret med kolonneoverskrifter, har Studio leveret generiske overskrifter *(Kol1, Kol2 osv.)*. God overskrifterne er ikke afgørende for at oprette en model, men de gør det nemmere at arbejde med data i forsøget. Også, når vi udgiver til sidst i denne model i en webtjeneste, overskrifterne hjælper med at identificere kolonnerne, der bruger til tjenesten.  

Vi kan tilføje kolonneoverskrifter ved hjælp af [Redigere Metadata] [ edit-metadata] modul.
Brug af [Redigere Metadata] [ edit-metadata] modul til at ændre metadata, der er knyttet til et datasæt. I dette tilfælde kan det giver mere fulde navne for kolonneoverskrifter. 

Brug af [Redigere Metadata][edit-metadata], du først angive, hvilke kolonner til at ændre (i dette tilfælde dem alle.) Derefter skal angive du handlingen, der skal udføres på kolonnerne (i dette tilfælde ændre kolonneoverskrifter.)

1.  Skriv "metadata" i feltet **Søg** i paletten modul. Du får vist [Redigere Metadata] [ edit-metadata] vises på listen modul.
2.  Klik og træk [Redigere Metadata] [ edit-metadata] modul over på lærredet, og slip den under datasættet vi tilføjede tidligere.
3.  Oprette forbindelse datasættet til [Redigere Metadata][edit-metadata]: Klik på outputporten af datasættet (den lille cirkel i bunden af datasættet). Herefter skal du trække til den input port [Redigere] metadata[ edit-metadata] (den lille cirkel øverst i modulet) slip derefter museknappen. Modulet og datasæt forbliver forbundne, selvom du flytter enten på lærredet.

    Forsøget bør nu se nogenlunde sådan ud:  

    ![Tilføje Rediger Metadata][2]
    
    Rødt udråbstegn angiver, at vi ikke har indstillet egenskaberne for dette modul endnu. Vi gør nu.
    
    > [AZURE.TIP] Du kan føje en kommentar til et modul ved at dobbeltklikke på modulet og indtastning af tekst. Dette kan hjælpe dig med at få et overblik hvad modulet gør i din forsøg. I dette tilfælde skal du dobbeltklikke på [Redigere Metadata] [ edit-metadata] modul, og skriv kommentar "Tilføj kolonneoverskrifterne". Klik på et andet sted på lærredet for at lukke dialogboksen tekst. Hvis du vil have vist kommentaren, skal du klikke på pil ned i modulet.

4.  Vælg [Rediger Metadata][edit-metadata], klik derefter på **Start kolonne Datavælger**i ruden **Egenskaber** til højre for lærredet.
5.  Markere alle rækker i **Tilgængelige kolonner** i dialogboksen **Vælg kolonner** , og klik på > at flytte dem til **Markerede kolonner**.
Dialogboksen ser sådan ud: ![kolonne Datavælger med alle markerede kolonner][4]
7.  Klik på **OK** markeringen.
8.  Se efter parameteren **nye kolonnenavne** tilbage i ruden **Egenskaber** . Angiv en liste over navne for de 21 kolonner i datasættet, adskilt af kommaer og i rækkefølgen af kolonner i dette felt. Du kan hente navnene kolonner fra datasæt dokumentation på webstedet UCI, eller for at gøre nemt kan du kopiere og indsætte følgende liste:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Ruden Egenskaber ser sådan ud:

    ![Egenskaber for Rediger Metadata][1]

> [AZURE.TIP] Hvis du vil bekræfte kolonneoverskrifterne, skal du køre forsøget (Klik på **Kør** under forsøg lærredet). Når den er kørt færdig (der vises en grøn markering på [Redigere Metadata][edit-metadata]), klik på outputporten [Redigere Metadata] [ edit-metadata] modul, og vælg **visuelle effekter**. Du kan få vist output fra et modul på samme måde at få vist status for dataene via forsøget.

##<a name="create-training-and-test-datasets"></a>Oprette kursus og teste datasæt

Næste trin i forsøget er at oprette separate datasæt, der bruger vi til uddannelse og test af vores model.

For at gøre dette skal vi bruge de [Opdelte Data] [ split] modul.  

1.  Finde de [Opdelte Data] [ split] modul, træk det over på lærredet og forbinde den til den sidste [Redigere Metadata] [ edit-metadata] modul.
2.  Opdel forholdet er 0,5 og parameteren **Randomized opdele** er angivet som standard. Det betyder, at et tilfældigt halvdel af dataene output via en port af de [Opdelte Data] [ split] modul, og den anden halvdel gennem den anden. Du kan justere disse samt parameteren **tilfældig frø** til at ændre opdelt mellem kurser og test af data. I dette eksempel skal vi vil lade dem som-er.
    
    > [AZURE.TIP] Egenskaben **brøkdel af rækker i den første output dataset** bestemmer, hvor meget af dataene er output via den venstre outputport. Eksempelvis hvis du angiver forholdet til 0.7, er 70% af dataene output via den venstre port og 30% via den rigtige port.  
    
3. Dobbeltklik på de [Opdelte Data] [ split] modul og angive kommentaren, "kursus/test data opdele 50%". 

Vi kan bruge output af de [Opdelte Data] [ split] modul men vi kan lide, men lad os vælge at bruge den venstre output, som kursus data og højre outputformater som test data.  

Som nævnt på webstedet UCI, er omkostningerne for misclassifying en høj risiko som lav fem gange større end omkostningerne for misclassifying en lav kreditrisiko som høj. For at tage højde for denne, oprette vi et nyt datasæt, der afspejler denne omkostninger funktion. I det nye datasæt replikeres hvert høj risiko eksempel fem gange, mens hvert lav risiko eksempel ikke replikeres.   

Vi kan gøre denne replikering ved hjælp af R kode:  

1.  Find og træk [Udføre R Script] [ execute-r-script] modul til forsøget lærredet og forbinde den venstre outputport af de [Opdelte Data] [ split] modul til den første indtastning port ("Dataset1") af [Udføre R Script] [ execute-r-script] modul.
2. Dobbeltklik på [Udføre R Script] [ execute-r-script] modul, og Angiv kommentaren, "Sæt omkostninger justering".
2.  Slet standardteksten i **R Script** -parameter i ruden **Egenskaber** , og Angiv dette script:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Vi har brug at gøre denne samme gentagelse handling for hver output af de [Opdelte Data] [ split] modul så kurser og test data har omkostningsjusteringen samme.

1.  Højreklik på [Udføre R Script] [ execute-r-script] modul, og vælg **Kopiér**.
2.  Højreklik på forsøg lærredet, og vælg **Sæt ind**.
3.  Oprette forbindelse til den første indtastning port af dette [Udføre R Script] [ execute-r-script] modul til højre output port af de [Opdelte Data] [ split] modul. 
4.  Klik på **Kør**nederst på lærredet. 

> [AZURE.TIP] Kopien af modulet udføre R Script indeholder det samme script som den oprindelige mappe. Når du kopierer og indsætter et modul på lærredet, bevarer kopien alle egenskaberne for oprindeligt.  

Vores forsøg nu ser nogenlunde således ud:

![Tilføje opdelt modul og R scripts][3]

Du kan finde flere oplysninger om brug af R scripts i din forsøg, [Forlæng din eksperimentere med R](machine-learning-extend-your-experiment-with-r.md).

**Næste: [tog og evaluerer modellerne](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
