<properties
   pageTitle="Streame data fra Stream Analytics til sø datalager | Azure"
   description="Brug Azure Stream Analytics til at streamdata i Azure sø datalager"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Streamdata fra Azure-lager Blob til sø datalager ved hjælp af Azure Stream Analytics

I denne artikel lærer du, hvordan du bruger Azure datalager sø som output til et Azure Stream Analytics-job. I denne artikel beskrives et simpelt scenarie, der læser data fra en Azure-lager blob (input) og skriver data til datalager sø (output).

>[AZURE.NOTE] På nuværende tidspunkt understøttes oprettelse og konfiguration af sø datalager output til Stream Analytics kun i [Azure klassisk Portal](https://manage.windowsazure.com). Nogle dele af dette selvstudium bruger det vil sige, portalen Azure klassisk.

## <a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Aktivere abonnementet Azure** Data sø Store Public Preview. Se [vejledningen](data-lake-store-get-started-portal.md#signup).

- **Azure-lager-konto**. Du skal bruge en blob objektbeholder fra denne konto til at indtaste data til et Stream Analytics-job. I dette selvstudium antager du opretter en lagerplads konto kaldet **datalakestoreasa** og en beholder i den konto, kaldet **datalakestoreasacontainer**. Når du har oprettet objektbeholderen, kan du overføre et eksempel på datafil til den. Du kan få et eksempel på datafil fra [Azure Data sø ciffer lager](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Du kan bruge forskellige klienter, som [Azure Storage Explorer](http://storageexplorer.com/)til at overføre data til en blob objektbeholder.

    >[AZURE.NOTE] Hvis du opretter kontoen fra Azure-portalen, Sørg for, at du har oprettet det. med **Klassisk** implementeringsmodel. Dette sikrer, at kontoen lagerplads kan åbnes fra portalen Azure klassisk, fordi det er, hvad vi bruger til at oprette et Stream Analytics-job. Du kan finde oplysninger om, hvordan du opretter en lagerplads konto fra Azure-portalen med klassisk installationen, [Opret en konto til Azure-lager](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > Eller du kan oprette en lagerplads konto fra portalen Azure klassisk.

- **Azure sø datalager konto**. Følg vejledningen på [Introduktion til Azure Data sø Store ved hjælp af portalen Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-Job

Du starte med at oprette et Stream Analytics-job, der indeholder en inputkilde og en outputdestination. Dette selvstudium kilden er en Azure blob-objektbeholder og destinationen er sø datalager.

1. Log [Azure klassisk Portal](https://manage.windowsazure.com).

2. For nederst til venstre på skærmen, klik på **Ny**, **Datatjenester**, **Stream Analytics**, **Hurtig oprettelse**. Indeholder værdierne, som vist nedenfor, og klik derefter på **Opret Stream Analytics Job**.

    ![Oprette et Stream Analytics-Job] (./media/data-lake-store-stream-analytics/create.job.png "Opret et Stream Analytics-job")

## <a name="create-a-blob-input-for-the-job"></a>Oprette en Blob input til opgaven

1. Åbn siden for Stream Analytics jobbet, klik på fanen **input** , og klik derefter på **Tilføj en Input** for at starte guiden.

2. Vælg **datastream**på siden **Tilføj et input for dit arbejde** , og klik derefter på pil frem.

    ![Tilføj et input for dit arbejde] (./media/data-lake-store-stream-analytics/create.input.1.png "Tilføj et input for dit arbejde")

3. Vælg **Blob-lager**på siden **Tilføj en datastream for dit arbejde** , og klik derefter på pil frem.

    ![Tilføj en datastream til jobbet] (./media/data-lake-store-stream-analytics/create.input.2.png "Tilføj en datastream til jobbet")

4. På siden **Indstillinger for lagring af Blob** fordel for oplysninger om den blob-lager, du vil bruge som input datakilde.

    ![Angive blob storage indstillinger] (./media/data-lake-store-stream-analytics/create.input.3.png "Angive blob storage indstillinger")

    * **Enter et input alias**. Dette er et entydigt navn, du angiver for sagen input.
    * **Vælg en konto, lagerplads**. Kontrollér, at kontoen lagerplads er i samme område som Stream Analytics jobbet, eller du vil betale yderligere omkostninger for at flytte data mellem områder.
    * **Angiv en objektbeholder til lagring**. Du kan vælge at oprette en ny objektbeholder eller vælge en eksisterende beholder.

    Klik på pilen frem.

5. På siden **Indstillinger for serialisering** angive serialiseringsformatet som **CSV**, afgrænser som **under fanen**kodning som **UTF8**, og klik derefter på markeringen.

    ![Angiver serialisering indstillinger] (./media/data-lake-store-stream-analytics/create.input.4.png "Angiver serialisering indstillinger")

6. Når du er færdig med guiden blob input tilføjes under fanen **input** og kolonnen **diagnose** skal vise **OK**. Du kan også direkte teste forbindelsen til input ved hjælp af knappen **Afprøv forbindelse** nederst.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Oprette et datalager sø output til jobbet

1. Åbn siden for Stream Analytics jobbet, klik på fanen **output** , og klik derefter på **Tilføj en Output** for at starte guiden.

2. Vælg **Sø datalager**på siden **Tilføj en output for dit arbejde** , og klik derefter på pil frem.

    ![Tilføj en output for dit arbejde] (./media/data-lake-store-stream-analytics/create.output.1.png "Tilføj en output for dit arbejde")

3. På siden **godkende forbindelsen** , hvis du allerede har oprettet en sø datalager-konto, skal du klikke på **Godkend nu**. Ellers skal du klikke på **Tilmeld dig nu** for at oprette en ny konto. I dette selvstudium, Lad os antage, at du allerede har en sø datalager-konto, der er oprettet (som nævnt i påkrævede). Du kan automatisk godkendes ved hjælp af de legitimationsoplysninger, som du er logget på portalen Azure klassisk.

    ![Godkend sø datalager] (./media/data-lake-store-stream-analytics/create.output.2.png "Godkend sø datalager")

4. Angiv oplysninger på siden **Indstillinger for Data sø Store** som vist i skærmbilledet nedenfor.

    ![Angiv sø datalager indstillinger] (./media/data-lake-store-stream-analytics/create.output.3.png "Angiv sø datalager indstillinger")

    * **Enter et output alias**. Dette er et entydigt navn, du angiver job output til.
    * **Angiv en sø datalager-konto**. Du bør allerede har oprettet, som nævnt i de nødvendige forudsætninger.
    * **Angiv en sti præfiksmønster**. Dette er påkrævet til identifikation af outputfiler, der er skrevet i sø datalager af Stream Analytics-job. Da titler for afgang skrevet af jobbet er i formatet GUID, hjælper herunder et præfiks med at identificere skrevne output. Hvis du vil medtage en datoen og tidsstemplet som en del af præfikset Sørg for, at du medtage `{date}/{time}` i præfiksmønster. Hvis du medtager dette, felterne **Format for dato **og **Klokkeslæt-formatet** er aktiveret, og du kan vælge formatet af valg.

    Klik på pilen frem.

5. På siden **Indstillinger for serialisering** angive serialiseringsformatet som **CSV**, afgrænser som **under fanen**kodning som **UTF8**, og klik derefter på markeringen.

    ![Angiv outputformatet] (./media/data-lake-store-stream-analytics/create.output.4.png "Angiv outputformatet")

6. Når du er færdig med guiden sø datalager output tilføjes under fanen **output** og kolonnen **diagnose** skal vise **OK**. Du kan også direkte teste forbindelsen til output ved hjælp af knappen **Afprøv forbindelse** nederst.

## <a name="run-the-stream-analytics-job"></a>Køre Stream Analytics-job

Hvis du vil køre et Stream Analytics-job, skal du køre en forespørgsel fra fanen forespørgsel. I dette selvstudium, du kan køre forespørgslen eksempel ved at erstatte pladsholderne med jobbet input og output aliasser, som vist i skærmbilledet nedenfor.

![Kør forespørgsel] (./media/data-lake-store-stream-analytics/run.query.png "Kør forespørgsel")

Klik på **Gem** fra bunden af skærmen, og klik derefter på **Start**. Vælg **Brugerdefineret tidspunkt**fra i dialogboksen, og vælg derefter en dato fra fortiden, som **1/1/2016**. Klik på markeringen i afkrydsningsfeltet for at starte jobbet. Det kan tage op til et par minutter til at starte jobbet.

![Angiv jobtid] (./media/data-lake-store-stream-analytics/run.query.2.png "Angiv jobtid")

Når jobbet er startet, skal du klikke på fanen **skærm** for at se, hvordan dataene blev behandlet.

![Overvåge job] (./media/data-lake-store-stream-analytics/run.query.3.png "Overvåge job")

Til sidst skal kan du bruge [Azure Portal](https://portal.azure.com) til at åbne kontoen sø datalager og kontrollere, om dataene blev skrevet til kontoen.

![Bekræft output] (./media/data-lake-store-stream-analytics/run.query.4.png "Bekræft output")

I ruden Data Explorer meddelelse, der er skrevet output til en mappe som angivet i sø datalager outputindstillinger (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Se også

* [Oprette en HDInsight klynge for at bruge sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
