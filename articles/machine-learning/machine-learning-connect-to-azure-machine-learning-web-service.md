<properties
    pageTitle="Oprette forbindelse til en webtjeneste Machine Learning | Microsoft Azure"
    description="C# eller Python, oprette forbindelse til en Azure Machine Learning webtjeneste ved hjælp af en tilladelse nøgle."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Oprette forbindelse til en webtjeneste Azure Machine Learning

Azure Machine Learning udvikler oplevelsen er en Web service API til at forudsige fra inputdataene i realtid eller i batch-tilstand. Du kan bruge Azure Machine Learning Studio til at oprette prognoser og installere en Azure Machine Learning-webtjeneste.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Til at få mere at vide om, hvordan du opretter og installerer en Machine Learning-webtjeneste, ved hjælp af Machine Learning Studio:

- Se [oprette din første forsøg](machine-learning-create-experiment.md)et selvstudium om, hvordan du opretter et forsøg i Machine Learning Studio.
- Du kan finde oplysninger om, hvordan du kan installere en webtjeneste, [Implementer en Machine Learning-webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).
- Du kan finde flere oplysninger om Machine Learning Generelt, ved at besøge [Machine Learning dokumentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Azure Machine Learning-webtjeneste ##

Med Azure Machine Learning-webtjeneste kommunikerer et eksternt program med en maskine Learning arbejdsproces vurdering model i realtid. En maskine Learning webtjeneste angiver returnerer forudsigelse resultater til et eksternt program. Hvis du vil gøre en Machine Learning-webtjeneste, opkald, skal sende du en API-nøgle, der oprettes, når du installerer en forudsigelse. Machine Learning-webtjeneste er baseret på RESTEN, et populære arkitektur valg for web programming projekter.

Azure Machine Learning har to typer af tjenester:

- Anmodning om svar Service (Ressourceposter) – en kort ventetid, meget SVG-tjeneste, som indeholder en brugergrænseflade til uden status modeller oprettet og implementeret fra Machine Learning Studio.
- Batchen udførelse af Service (BES) – en asynkron service, pointtal, der er en portion for dataposter.

Se [Implementer en Machine Learning-webtjeneste](machine-learning-publish-a-machine-learning-web-service.md)kan finde flere oplysninger om Machine Learning Web-tjenester.

## <a name="get-an-azure-machine-learning-authorization-key"></a>Få en Azure Machine Learning godkendelse nøgle ##

Når du installerer din forsøg, oprettes API nøgler til webtjenesten. Du kan hente de pågældende taster fra flere steder.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Fra Microsoft Azure Machine Learning Web Services-portalen

Log på portalen [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) .

Til at hente API-nøgle til en ny computer Learning-webtjeneste:

1. Klik på **Web Services** den øverste menu i portalen Azure Machine Learning Web Services.
2. Klik på den webtjeneste, du vil hente nøglen.
3. Klik på den øverste menu **forbrug**.
4. Kopier og Gem den **Primærnøgle**.


Til at hente API-nøgle til en klassisk Machine Learning-webtjeneste:

1. Klik på **Klassisk webtjenester** den øverste menu i portalen Azure Machine Learning Web Services.
2. Klik på den webtjeneste, som du arbejder.
3. Klik på det slutpunkt, som du vil hente nøglen.
3. Klik på den øverste menu **forbrug**.
4. Kopier og Gem den **Primærnøgle**.

## <a name="classic-web-service"></a>Klassisk webtjeneste ##

 Du kan også hente en nøgle til en klassisk webtjeneste fra Machine Learning Studio eller Azure portalen.

### <a name="machine-learning-studio"></a>Machine Learning Studio ###

1. Klik på **WEB-tjenester** til venstre i Machine Learning Studio.
2. Klik på en webtjeneste. **API-nøgle** er under fanen **DASHBOARD** .

### <a name="azure-portal"></a>Azure-portalen ###

1. Klik på **Computer LEARNING** i venstre side.
2. Klik på det arbejdsområde, hvor din webtjeneste er placeret.
3. Klik på **WEB-tjenester**.
4. Klik på en webtjeneste.
5. Klik på et slutpunkt. "API-nøgle" er nede på nederst til højre.

## <a id="connect"></a>Oprette forbindelse til en webtjeneste til Machine Learning

Du kan oprette forbindelse til en Machine Learning-webtjeneste, ved hjælp af en hvilken som helst programmeringssprog, der understøtter HTTP og svar. Du kan få vist eksempler i C#, Python og R fra tjenesten Hjælp en Machine Learning webside.

**Machine Learning API Hjælp** Machine Learning API Hjælp oprettes, når du installerer en webtjeneste. Se [Azure Machine Learning gennemgang - installere webtjeneste](machine-learning-walkthrough-5-publish-web-service.md).
Machine Learning API Hjælp indeholder oplysninger om en forudsigelse webtjeneste.

2. Klik på den webtjeneste, som du arbejder.
3. Klik på det slutpunkt, som du vil have vist siden Hjælp til API.
3. Klik på den øverste menu **forbrug**.
3. Klik på **API Hjælp side** under den anmodning om svar eller batchen udførelse af slutpunkter.

**Hjælp til en ny webtjeneste til visningen Machine Learning API**

I Azure maskinen læ Web Services-portalen:

1. Klik på **WEB-tjenester** i den øverste menu.
2. Klik på den webtjeneste, du vil hente nøglen.

Klik på **forbrug** for at få URI'er for anmodning om Reposonse og batchen udførelse af tjenester og eksempler på kode i C#, R og Python.

Klik på **Swagger API** for at få Swagger baseret dokumentation til API'er hedder fra de medfølgende URI'er.

### <a name="c-sample"></a>C# eksempel ###

Hvis du vil oprette forbindelse til en Machine Learning-webtjeneste, kan bruge en **HttpClient** , der passerer ScoreData. ScoreData indeholder en FeatureVector, en n-flerdimensionale vektor af numeriske funktioner, der repræsenterer ScoreData. Du kan godkende til tjenesten Machine Learning med en API-nøgle.

Hvis du vil oprette forbindelse til en Machine Learning-webtjeneste, skal være installeret pakken **Microsoft.AspNet.WebApi.Client** NuGet.

**Installere Microsoft.AspNet.WebApi.Client NuGet i Visual Studio**

1. Publicere Download dataset fra UCI: voksne 2 klasse dataset webtjeneste.
2. Klik på **værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**.
2. Vælg **Installer-pakke Microsoft.AspNet.WebApi.Client**.

**Til at køre kodeeksempel**

1. Publicere "eksempel 1: hente dataset fra UCI: voksen 2 klasse dataset" forsøg, en del af samlingen Machine Learning eksempel.
2. Tildele apiKey med tasten fra en webtjeneste. Se **få en nøgle til godkendelse af Azure Machine Learning** ovenfor.
3. Tildele serviceUri med den anmode om URI.


### <a name="python-sample"></a>Python eksempel ###

Hvis du vil oprette forbindelse til en Machine Learning-webtjeneste, kan bruge biblioteket **urllib2** , der passerer ScoreData. ScoreData indeholder en FeatureVector, en n-flerdimensionale vektor af numeriske funktioner, der repræsenterer ScoreData. Du kan godkende til tjenesten Machine Learning med en API-nøgle.


**Til at køre kodeeksempel**

1. Installere "eksempel 1: hente dataset fra UCI: voksen 2 klasse dataset" forsøg, en del af samlingen Machine Learning eksempel.
2. Tildele apiKey med tasten fra en webtjeneste. I afsnittet **får du en nøgle til Azure Machine Learning godkendelse** i starten af denne artikel.
3. Tildele serviceUri med den anmode om URI.
