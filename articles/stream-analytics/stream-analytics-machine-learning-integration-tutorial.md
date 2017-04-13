<properties
    pageTitle="Synspunkt analyse ved hjælp af Azure Stream analyser og Azure maskine læring | Microsoft Azure"
    description="Sådan bruges en brugerdefineret funktion og maskine læring i et Stream Analytics-job"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Synspunkt analyse ved hjælp af Azure Stream analyser og Azure maskine læring #

I denne artikel er udviklet til at hjælpe dig med hurtigt at oprette en simpel Azure Stream Analytics-sag, med Azure Machine Learning integration. Vi skal bruge en synspunkt analytics Machine Learning model fra galleriet Cortana Intelligence til at analysere streaming tekstdata, og bestemme synspunkt karakteren i realtid. Oplysninger i denne artikel kan hjælpe dig med at forstå scenarier som realtid synspunkt analytics på streaming Twitter data, analysere optagede kunde Chat med supportpersonale og evaluere bemærkninger til fora, blogs og videoer, ud over mange andre i realtid, skønnet vurdering scenarier.

I denne artikel indeholder en CSV-eksempelfilen med tekst som input i Azure Blob-lager, vises på følgende billede. Jobbet gælder synspunkt analytics modellen som en brugerdefineret funktion (UDF) på tekst eksempeldataene fra blob-lager. Betyder er placeret i det samme blob-lager i en anden csv-fil. 

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Følgende billede viser denne konfiguration. For et mere realistisk scenario, kan du erstatte Blob-lager med streaming Twitter data fra Azure begivenhed Hubs input. Desuden kan du oprette en [Microsoft Power BI](https://powerbi.microsoft.com/) realtid visualisering af det samlede synspunkt.    

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Forudsætninger

Forudsætninger for at udføre de opgaver, der er vist i denne artikel er som følger:

-   Et aktivt Azure abonnement.
-   En CSV-fil med nogle data i den. Du kan hente filen vises i figur 1 fra [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), eller du kan oprette din egen fil. I denne artikel antager vi, at du bruger det foreslåede navn til hentning på GitHub.

På et højt niveau for at fuldføre de opgaver, der er vist i denne artikel kan gøre du følgende:

1.  Overføre input csv-filen til Azure Blob-lager.
2.  Føje en synspunkt analytics model fra galleriet Cortana Intelligence til arbejdsområdet Azure maskine undervisning.
3.  Installere denne model som en webtjeneste i arbejdsområdet Machine Learning.
4.  Oprette et Stream Analytics-job, der kalder webtjenesten som en funktion til at bestemme synspunkt efter den tekst, der er indtastet.
5.  Start kørslen Stream analyser og overhold output.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Overføre input csv-filen til Blob-lager

Du kan bruge en CSV-fil, som den, der allerede er angivet som kan hentes på GitHub for dette trin. Du kan bruge [Azure Storage Explorer](http://storageexplorer.com/) eller Visual Studio for at overføre filen, eller du kan bruge brugerdefineret kode. Vi bruger eksempler, der er baseret på Visual Studio.

1.  I Visual Studio, skal du klikke på **Azure** > **lagerplads** > **Vedhæfte eksterne lagerplads**. Angiv et **kontonavn** og **Kontonøgle**.  

    ![Stream Analytics Machine Learning, Server Explorer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Udvid den lagerplads, du har vedhæftet, i trin 1, skal du klikke på **Opret Blob objektbeholder**, og angiv derefter et logisk navn. Når du opretter objektbeholderen, kan du åbne den for at få vist indholdet. (Det vil være tom på dette tidspunkt).  

    ![Streame Analytics Machine Learning, skal du oprette blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Overføre csv-filen, klikke på **Overfør Blob**, og klik derefter på **filer fra den lokale disk**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Tilføje synspunkt analytics modellen fra galleriet Cortana Intelligence

1.  Du kan hente [skønnet synspunkt analytics model](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) fra galleriet Cortana Intelligence.  
2.  Klik på **Åbn i Studio**i Machine Learning Studio.  

    ![Streame Analytics Machine Learning, Åbn Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Log på at gå til arbejdsområdet. Vælg den placering, der passer bedst til dine egne placering.
4.  Klik på **Kør** nederst på siden.  
5.  Når processen kører korrekt, skal du klikke på **Installere webtjeneste**.
6.  Synspunkt analytics modellen er klar til brug. Hvis du vil validere, klik på knappen **Test** og angive tekstindtastning, f.eks., at "Jeg elsker Microsoft". Testen skal returnere et resultat som følger:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Stream Analytics Machine Learning, af analysedata](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Klik på linket til **Excel 2010 eller en tidligere projektmappe** til at få din API-nøgle og den URL-adresse, du skal bruge senere til at konfigurere Stream Analytics jobbet i kolonnen **Apps** . (Dette trin skal kun bruge en Machine Learning model fra et andet arbejdsområde Azure-konto. Denne artikel antages det er tilfældet, til at løse dette scenario.)  

Bemærk web service URL-adresse og access tasten fra den hentede fil i Excel, som vist nedenfor:  

![Stream Analytics Machine Learning, oversigt over](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Oprette et Stream Analytics-job, der bruger Machine Learning modellen

1.  Gå til [Azure-portalen](https://manage.windowsazure.com).  
2.  Klik på **nyt** > **datatjenester** > **Stream Analytics** > **Hurtig oprettelse**. Angiv et navn til dit arbejde i **Jobbet navn**, Angiv det relevante område jobbet i **område**og derefter vælge kontoen i **Internationale overvågning lagerplads konto**.    
3.  Klik på **Tilføj en Input**, når jobbet er oprettet, under fanen **oplysninger** .  

    ![Streame Analytics Machine Learning, skal du tilføje Machine Learning input](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  Klik på **datastream**på den første side i guiden **Tilføj** , og klik derefter på **Næste**. Klik på **Blob-lager** som input på den næste side, og klik derefter på **Næste**.  
5.  Give lagerplads blob objektbeholder kontonavnet du definerede tidligere, når du har overført dataene på siden **Indstillinger for lagring af Blob** i guiden. Klik på **Næste**. Klik på **CSV**til **Begivenhed serialiseringsformat**. Acceptere standardværdierne for resten af siden **Indstillinger for serialisering** . Klik på **OK**.  
6.  Klik på **Tilføj en Output**under fanen **output** .  

    ![Streame Analytics Machine Learning, skal du tilføje output](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Klik på **Blob-lager**, og derefter angive de samme parametre, med undtagelse af objektbeholderen. Værdien for **Input** blev konfigureret til at læse fra objektbeholderen med navnet "test" hvor **CSV-** filen blev overført. Skriv "testoutput" til **Output**. Objektbeholder navne skal være forskellige. Kontrollér, at der findes i denne beholder.     
8.  Klik på **Næste** for at konfigurere den output **serialisering indstillinger**. Klik på **CSV**som med **Input**, og klik derefter på knappen **OK** .
9.  Klik på **Tilføj en Machine Learning funktion**på fanen **Funktioner** .  

    ![Streame Analytics Machine Learning, skal du tilføje Machine Learning funktionen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Find den maskine Learning arbejdsområde, webtjeneste og standardslutpunkt på siden **Machine Learning Web Tjenesteindstillinger** . Anvend indstillinger manuelt for at få kendskab til konfiguration af en webtjeneste til et arbejdsområde, så længe du kender URL-adressen og har API-nøglen for denne artikel. Angiv slutpunkt **URL-adresse** og **API-nøgle**. Klik på **OK**.    

    ![Stream Analytics Machine Learning, Machine Learning webtjeneste](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. På fanen **forespørgsel** skal du ændre forespørgslen på følgende måde:    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Klik på **Gem** for at gemme forespørgslen.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Start kørslen Stream analyser og overhold output

1.  Klik på **Start** nederst på siden job.
2.  Klik på **Brugerdefineret tidspunkt** **Starte forespørgsel-dialogboksen**, og derefter vælge et tidsrum før, når du har overført CSV til Blob-lager. Klik på **OK**.  

    ![Stream Analytics Machine Learning, brugerdefinerede tid](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Gå til Blob-lager ved hjælp af værktøjet du brugte til at overføre csv-filen, for eksempel Visual Studio.
4.  Et par minutter, efter at jobbet startes, objektbeholderen output oprettes og en CSV-fil er overført til den.  
5.  Åbn filen i CSV-standardprogram til redigering. Noget, der ligner følgende skal vises:  

    ![Stream Analytics Machine Learning, CSV-visning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Konklusion

Denne artikel beskrives, hvordan du opretter et Stream Analytics-job, der læser streaming tekstdata og gælder synspunkt analytics for dataene i realtid. Du kan udføre alt dette over uden at skulle bekymre dig om indgående at kende opbygning af en synspunkt analytics-model. Dette er en af fordelene ved den Cortana Intelligence pakke.

Du kan også få vist Azure Machine Learning funktionen beslægtede målepunkter. For at gøre dette, skal du klikke på fanen **skærm** . Tre funktionen beslægtede målepunkter vises.  

- **Funktionen anmodninger** angiver antallet af anmodninger sendes til en webtjeneste Machine Learning.  
- **Funktionen begivenheder** angiver antallet af hændelser i anmodningen. Hver anmodning til en webtjeneste Machine Learning indeholder som standard, op til 1.000 begivenheder.  
- **Mislykkedes funktionen anmodninger** angiver antallet af mislykkede anmodninger til webtjenesten Machine Learning.  

    ![Stream Analytics Machine Learning, Machine Learning overvåge visning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
