<properties
    pageTitle="Tilføje en datainput til din Stream Analytics sager | Microsoft Azure"
    description="Lær at logge på en datakilde til Stream Analytics tingene som streaming datainput fra begivenhed hubber eller reference data fra Blog lagerplads."
    keywords="data, som angives, streaming data"
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
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Føje en streaming data input eller reference data til et Stream Analytics-job

Lær at logge på en datakilde til Stream Analytics tingene som streaming datainput fra begivenhed hubber eller reference data fra Blob-lager.

Azure Stream Analytics-job kan have forbindelse til en datainput eller derover, hver især definerer en forbindelse til en eksisterende datakilde. Data er sendt til datakilden, er det consumed ved kørslen Stream analyser og behandles i realtid som streaming data. Stream Analytics har førsteklasses integration med [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/) og [Azure Blob-lager](../storage/storage-dotnet-how-to-use-blobs.md) , både i og uden for det job abonnement.

I denne artikel er et trin i den [Stream Analytics læringssti](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Datainput: Streaming data og referencedata

Der findes to forskellige typer input i Stream Analytics: datastreams og referencedata.

- **Datastreams**: Stream Analytics job skal indeholde mindst én stream datainput skal consumed og transformeret ved jobbet. Azure Blob-lager og Azure begivenhed Hubs understøttes som data stream inputkilder. Azure begivenhed Hubs bruges til at indsamle begivenhed streams fra forbundne enheder, tjenester og programmer. Azure Blob-lager kan bruges som en inputkilde til ingesting flere data som en stream.  
- **Referencedata**: Stream Analytics understøtter en anden type ekstra input kaldet referencedata.  I modsætning til data i bevægelse er disse data statisk eller nedsætte ændring.  Det bruges typisk for at udføre look-ups og korrelationer med datastreams til at oprette et bedre datasæt.  Azure Blob-lager er i øjeblikket kun understøttede inputkilden for referencedata.  

Sådan tilføjer du en input til Stream Analytics tingene:

1. Klik på **input** , og klik derefter på **Tilføj en Input** i Stream Analytics tingene på portalen Azure.

    ![Azure klassisk portal - tilføje input.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Klik på feltet **input** i Stream Analytics tingene på portalen Azure.  

    ![Azure portal - tilføje data i et felt.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Angive typen input: **datastream** eller **referencedata**.

    ![Tilføje de korrekte data input, streames eller reference](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Tilføje de korrekte data input, streames eller reference](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Hvis opretter en datastream input, skal du angive kildetypen for input.  Dette trin kan blive ignoreret under oprettelse af Reference Data som kun Blob storage understøttes på nuværende tidspunkt.

    ![Tilføje data stream datainput](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Tilføje data stream datainput](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Angiv et navn for denne input i feltet Indtast Alias.  Dette navn bruges i dit arbejde forespørgsel senere til at referere til input.

    Udfyld resten af de nødvendige forbindelsesegenskaber til at oprette forbindelse til datakilden. Disse felter varierer afhængigt af input og kilde indholdstype og er defineret i detaljer [her](stream-analytics-create-a-job.md).  

    ![Tilføje begivenhed hub datainput](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Angive indstillinger for inputdataene serialisering:
    - For at sikre at dine forespørgsler fungerer som forventet, Angiv **Begivenhed serialiseringsformatet** for indgående data.  Understøttede serialiseringsformater er JSON, csv- og Avro.
    - Kontrollér **kodning** for dataene.  UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt.

    ![Indstillinger for serialisering af data for de data, der er indtastet](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Indstillinger for serialisering af data for de data, der er indtastet](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. Når du er færdig input oprettelse, kontrollere Stream Analytics, at det kan oprette forbindelse til inputkilden.  Du kan få vist status for handlingen Test forbindelse i hubben meddelelse.

    ![Teste forbindelsen streaming datainput](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Teste forbindelsen streaming datainput](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Få hjælp til streaming data input
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
