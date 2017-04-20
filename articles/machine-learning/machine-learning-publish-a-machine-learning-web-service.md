<properties
    pageTitle="Installere en webtjeneste Machine Learning | Microsoft Azure"
    description="Sådan konvertere et eksperiment uddannelse til en forudseende eksperiment, gøre den klar til installation og derefter implementeres som en Azure Machine Learning-webtjeneste."
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
    ms.date="10/04/2016"
    ms.author="garye"/>

# <a name="deploy-an-azure-machine-learning-web-service"></a>Installere en Azure Machine Learning-webtjeneste

Azure Machine Learning gør det muligt at bygge, teste og implementere forudseende analytiske løsninger.

Fra et højt niveau point-of-view sker det i tre trin:

- **[Opret en uddannelse eksperiment]** - Azure Machine Learning Studio er et fælles visuelle udviklingsmiljø, der bruges til at træne og teste en forudseende analytics-model ved hjælp af uddannelsesdata, du angiver.
- **[Konvertere det til en forudseende eksperiment]** - når modellen er uddannet med eksisterende data, og du er klar til at bruge den til at give nye data, forberede og strømline dit eksperiment for forudsigelser.
- **Installere den som en web-service** - du kan implementere din forudseende forsøg som en [Ny] eller [klassiske] Azure-webtjenesten. Brugerne kan sende data til din model og modtage din model forudsigelser.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Oprette et eksperiment med uddannelse

For at træne en forudseende analytics-model, bruger du Azure Machine Learning Studio til at oprette et eksperiment uddannelse hvor du kan medtage forskellige moduler til at indlæse uddannelsesdata, forberede data efter behov, anvende machine learning algoritmer og evaluere resultaterne. Du kan navigere i et eksperiment og prøve forskellige machine learning algoritmer til at sammenligne og evaluere resultaterne.

Processen til oprettelse og administration af uddannelse forsøg dækkes mere udførligt andetsteds. Yderligere oplysninger finder du i disse artikler:

- [Opret et simpelt eksperiment i Azure Machine Learning Studio](machine-learning-create-experiment.md)
- [Udvikle en forudseende løsning med Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importer uddannelsesdata i Azure Machine Learning Studio](machine-learning-data-science-import-data.md)
- [Administrere gentagelser af forsøg i Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertere forsøget uddannelse til en forudseende forsøg

Når du har uddannet modellen, er du klar til at konvertere din uddannelse forsøg til en forudseende forsøg til at give nye data.

Ved at konvertere til en forudseende forsøg, får du din uddannet model klar til at blive installeret som et resultatmønster webtjeneste. Brugere af web-tjenesten kan sende inputdata til modellen, og modellen vil sende tilbage forudsigelse-resultater. Når du konverterer til en forudseende eksperiment, Husk på hvad du forventer, at modellen skal anvendes af andre.

For at konvertere dit eksperiment uddannelse til en forudseende eksperiment, klik på **Kør** i bunden af forsøget lærredet, skal du klikke på **Angiv webtjeneste**, og vælg derefter **Forudseende webtjeneste**.

![Konverter til vundne forsøg](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Yderligere oplysninger om, hvordan du udfører denne konvertering, se [konvertere en Machine Learning uddannelse forsøg til en forudseende eksperiment](machine-learning-convert-training-experiment-to-scoring-experiment.md).

De følgende trin beskriver implementering af et forudseende forsøg som en ny webtjeneste. Du kan også installere forsøget som klassisk webtjeneste.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Installere forudseende forsøget som en ny webtjeneste

Nu, hvor forudseende forsøget er blevet forberedt, kan du installere den som en Azure webtjeneste. Ved hjælp af webtjenesten, brugerne kan sende data til modellen, og modellen returnerer dens forudsigelser.

Klik på **Kør** i bunden af forsøget lærredet for at installere din forudseende eksperiment. Når forsøget er afsluttet, skal du klikke på **Installer webtjeneste** , og vælg **installere webtjeneste [Ny]**.  Siden installationen af Machine Learning Web Service portal åbnes.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Machine Learning Web Service portal installation forsøg siden

Angiv et navn til webtjenesten på siden installere eksperiment.
Vælg en prissætning plan. Hvis du har en eksisterende prissætning plan, kan du vælge den, skal ellers du oprette en ny pris plan for tjenesten.

1.  Rullelisten, vælge en eksisterende plan, eller Vælg indstillingen **Vælg ny plan** i **Plan pris** .
2.  Skriv et navn, der identificerer planen på din regning i **Modelnavn**.
3.  Vælg en af de **Månedlige planlægger metodetrin**. Plan metodetrin standard planer for dit standardområde og web-tjenesten er installeret på dette område.

Klik på **Installer** og **hurtigstart** -siden til din web-tjenesten åbnes.

Websiden for hurtigstart giver dig adgang til og vejledning på de mest almindelige opgaver, du vil udføre, når du har oprettet en webtjeneste. Herfra kan du let adgang til både testside og forbrug siden.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Test din webtjeneste

For at teste den nye webtjeneste, skal du klikke på **Test webtjeneste** under almindelige opgaver. På testsiden, kan du teste din webtjeneste som en anmodning om svar Service (Ressourceposter) eller en batchkørsel (ved siden).

Ressourceposter testsiden viser input, output og de globale parametre, du har defineret for forsøget. For at teste web-tjenesten, skal du manuelt Angiv de relevante værdier for input eller udlevere en fil med semikolonseparerede værdier (CSV) formateret fil der indeholder værdierne for test.

Angiv de relevante værdier for input for at teste bruger Ressourceposter, fra listen visningstilstand, og klik på **Test anmodning-svar**. Forudsige resultaterne vises i outputkolonnen til venstre.

![Installation af webtjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Hvis du vil teste din ved siden, klik på **Batch**. Klik på Gennemse under dit input på testsiden Batch, og vælg en CSV-fil, der indeholder relevante eksempelværdier. Hvis du ikke har en CSV-fil, og du har oprettet din forudseende eksperiment ved hjælp af Machine Learning Studio, kan du hente data for dit eksperiment, forudseende og bruge den.

For at hente data, skal du åbne Machine Learning Studio. Åbn din forudseende eksperiment og højreklikke på input til dit eksperiment. Vælg **datasæt** i genvejsmenuen, og klik derefter på **Hent**.

![Installation af webtjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klik på **Test**. Status for din Batch Execution job, der vises til højre under **Test-kørsler**.

![Installation af webtjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

På siden **konfiguration** kan du ændre beskrivelsen, titel, opdatere nøglen opbevaring konto og aktivere eksempeldata for webtjenesten.

![Konfigurere web-tjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Når du har implementeret webtjenesten, kan du:

- **Adgang til** den via web service-API.
- **Administrer** den via Azure Machine Learning web services portal eller Azure klassiske portalen.
- **Opdater** den, hvis modellen ændres.

### <a name="access-the-web-service"></a>Få adgang til webtjenesten

Når du installerer din webtjeneste fra Machine Learning Studio, kan du sende data til tjenesten og modtager svar ved hjælp af programmering.

Siden **forbrug** indeholder alle de oplysninger, du har brug for at få adgang til webtjenesten. For eksempel findes API nøglen for at give autoriserede adgang til tjenesten.

Yderligere oplysninger om adgang til en webtjeneste Machine Learning, se, [hvordan du optager en distribueret Azure Machine Learning-webtjeneste](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Administrere din nye web-service

Du kan administrere klassiske services Machine Learning webtjenester webportalen. Klik på **Webtjenester**på [portalens hovedside](https://services.azureml-test.net/). Du kan slette eller kopiere en tjeneste på siden web services. Hvis du vil overvåge en bestemt service, klik på tjenesten, og klik derefter på **dashboardet**. For at overvåge batchjob, der er knyttet til webtjenesten, skal du klikke på **Anmod om batchloggen**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implementere forudseende forsøget som en klassisk webtjeneste.

Nu, hvor forudseende forsøget er tilstrækkeligt forberedt, kan du installere den som en Azure webtjeneste. Ved hjælp af webtjenesten, brugerne kan sende data til modellen, og modellen returnerer dens forudsigelser.

For at installere din forudseende eksperiment, klik på **Kør** i bunden af forsøget lærredet, og klik derefter på **Installere Web Service**. Webtjenesten er sat op, og du er placeret i web service dashboardet.

![Installation af webtjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Du kan teste web-tjenesten i Machine Learning Web Services portal eller Machine Learning Studio.

Test webtjenesten Anmod om svar, klikke på knappen **Test** i web service dashboardet. En dialogboks vises til at bede dig om inddata til tjenesten. Disse er de kolonner, der forventes af resultatmønster forsøget. Angiv et sæt data, og klik derefter på **OK**. De resultater, der genereres af webtjenesten vises nederst i dashboardet.

Du kan klikke på linket **Test** udskrift for at teste din service i Azure Machine Learning Web Services portal, som vist tidligere i afsnittet nye Web-service.

Test kørsel af tjenesten, klikke på **Test** eksempel link. Klik på Gennemse under dit input på testsiden Batch, og vælg en CSV-fil, der indeholder relevante eksempelværdier. Hvis du ikke har en CSV-fil, og du har oprettet din forudseende eksperiment ved hjælp af Machine Learning Studio, kan du hente data for dit eksperiment, forudseende og bruge den.

![Test af webtjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Du kan ændre det viste navn på tjenesten og give dem en beskrivelse på siden **konfiguration** . Navnet og beskrivelsen vises i [Azure klassiske portal](http://manage.windowsazure.com/) , hvor du kan administrere dine webtjenester.

Du kan angive en beskrivelse for din inputdata, outputdata og parametre for web-tjenesten ved at indtaste en streng for hver kolonne under **INPUT skema**, **OUTPUTSKEMA**og **WEBTJENESTEPARAMETER**. Disse beskrivelser bruges i prøven kode-dokumentationen for webtjenesten.

Du kan aktivere logføring til at diagnosticere fejl, som du ser, når der opnås adgang til webtjenesten. Yderligere oplysninger finder du under [aktivere logføring af Machine Learning-webtjenester](machine-learning-web-services-logging.md).

![Konfigurere web-tjenesten](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

Du kan også konfigurere slutpunkter for webtjenesten i Azure Machine Learning Web Services portal svarer til den procedure, der er vist tidligere i afsnittet nye Web-service. Indstillingerne er forskellige, kan du tilføje eller ændre servicebeskrivelse, Aktiver logføring og aktiver eksempeldata til test.

### <a name="access-the-web-service"></a>Få adgang til webtjenesten

Når du installerer din webtjeneste fra Machine Learning Studio, kan du sende data til tjenesten og modtager svar ved hjælp af programmering.

Dashboardet indeholder alle de oplysninger, du har brug for at få adgang til webtjenesten. API-nøgle angives til autoriseret adgang til tjenesten, f.eks API Hjælp sider er forudsat at du går i gang skrive din kode.

Yderligere oplysninger om adgang til en webtjeneste Machine Learning, se, [hvordan du optager en distribueret Azure Machine Learning-webtjeneste](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>Administration af webtjenesten

Der er forskellige handlinger, du kan udføre til at overvåge en webtjeneste. Du kan opdatere den, og Slet den. Du kan også tilføje flere slutpunkter til en klassisk webtjeneste ud over det standardslutpunkt, der oprettes, når du installerer den.

Yderligere oplysninger finder du under [Administrer et Azure Machine Learning arbejdsområde](machine-learning-manage-workspace.md) og [Administrer en webtjeneste ved hjælp af webtjenester til Azure Machine Learning-portalen](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Opdatering af webtjenesten

Du kan foretage ændringer til din webtjeneste, f.eks opdatere modellen med yderligere træning data og installere den igen, overskriver den oprindelige webtjeneste.

For at opdatere web-tjenesten, ved at åbne den oprindelige forudseende eksperiment, som du brugte til at installere webtjenesten og tage en redigerbar kopi ved at klikke på **Gem som**. Foretag ændringerne, og klik derefter på **Installer webtjeneste**.

Fordi du har installeret dette eksperiment før, bliver du spurgt, om du vil overskrive (klassisk Web Service), eller Opdater (ny webtjeneste) den eksisterende service. Hvis du klikker på **Ja** , eller **Opdater** holder eksisterende web-tjenesten og installerer den nye forudseende eksperiment er installeret i stedet.

> [AZURE.NOTE] Hvis du har foretaget ændringer i konfigurationen i den oprindelige web-service, for eksempel skal at indtaste et nyt navn eller en beskrivelse, du angive disse værdier igen.

Én mulighed for opdatering af web-tjenesten er at omskole modellen ved hjælp af programmering. Yderligere oplysninger finder du under [omskole Machine Learning modeller ved hjælp af programmering](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Oprette et eksperiment med uddannelse]: #create-a-training-experiment
[Konvertere det til en forudseende forsøg]: #convert-the-training-experiment-to-a-predictive-experiment
[ny]: #deploy-the-predictive-experiment-as-a-new-Web-service
[klassisk]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
