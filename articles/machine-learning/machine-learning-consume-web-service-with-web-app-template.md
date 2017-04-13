<properties
    pageTitle="Bruge en Machine Learning webtjeneste med en web app-skabelon | Microsoft Azure"
    description="Bruge en web app-skabelon i Azure Marketplace til at bruge en skønnet webtjeneste i Azure Machine Learning."
    keywords="webtjeneste, operationalization, REST-API machine learning"
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
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Bruge en Azure Machine Learning webtjeneste med en web app-skabelon

>[AZURE.NOTE] Dette emne beskrives teknikker gældende til en klassisk webtjeneste. 

Når du har udviklet skønnet modellen og installeres som en Azure webtjeneste ved hjælp af Machine Learning Studio, eller ved hjælp af funktioner som R eller Python, du kan få adgang til operationalized modellen ved hjælp af en REST-API.

Der er flere måder at bruge REST-API og få adgang til webtjenesten. For eksempel kan du skrive et program i C#, R eller Python via det eksempel, der genereres for dig, når du har installeret-webtjenesten (tilgængelig på siden API Hjælp i web-tjenesten dashboardet i Machine Learning Studio). Eller du kan bruge Microsoft Excel eksempelprojektmappen oprettes til dig (også tilgængeligt i web-tjenesten dashboardet i Studio).

Men den hurtigste og nemmeste måde at få adgang til din webtjeneste er gennem de Web App-skabeloner, der er tilgængelige i [Azure Web App Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure maskinen læ Web App-skabeloner

De web app tilgængelige skabeloner i Azure Marketplace kan oprette en brugerdefineret WebApp, der kender din webtjeneste inputdataene og forventede resultater. Alt, du skal gøre, er give web app-adgang til dine webtjeneste og data, og skabelonen gør resten.

Der findes to skabeloner:

- [Skabelon til Azure ML anmodning om svar Service Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Skabelon til Azure ML batchen udførelse af tjenesten Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Hver skabelon opretter en stikprøve ASP.NET-program, brug af API URI og nøgle til din webtjeneste, og installerer det som et websted til Azure. Skabelonen anmodning om svar Service (Ressourceposter) opretter en WebApp, hvor du kan sende en enkelt række med data til webtjenesten til at få et enkelt resultat. Skabelonen batchen udførelse af Service (BES) opretter en WebApp, hvor du kan sende mange rækker af data for at få flere resultater.

Ingen kodningssprog er påkrævet for at bruge disse skabeloner. Du skal blot angive API URI og nøgle og skabelonen opbygger programmet for dig.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Sådan bruges skabelonen anmodning om svar Service (Ressourceposter)

Når du har installeret dit webtjeneste, kan du følge nedenstående trin for at bruge skabelonen Ressourceposter web app, som vist i følgende diagram.

![Proces for at bruge Ressourceposter webskabelon][image1]

1. Åbn fanen **Webtjenester** i Machine Learning Studio, og Åbn derefter den webtjeneste, du vil have adgang til. Kopiere den nøgle, der er angivet under **API-nøgle** , og Gem den.

    ![API-nøgle][image3]

2. Åbn siden **Anmodning/svar** API Hjælp. Kopiere værdien **Request URI** øverst på Hjælpsiden under **anmode om**, og Gem den. Denne værdi ser sådan ud:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![Anmode om URI][image4]

3. Gå til [Azure-portalen](https://portal.azure.com), **logon**, klik derefter på **Ny**, søge efter og vælg **Azure ML anmodning om svar Service Web App**, og derefter klikke på **Opret**. 

    - Giv din online et entydigt navn. URL-adressen til WebApp bliver dette navn efterfulgt af `.azurewebsites.net.` f.eks.`http://carprediction.azurewebsites.net.`

    - Vælg de Azure-abonnement og tjenester, som din webtjeneste kører under.

    - Klik på **Opret**.

    ![Oprette WebApp][image5]

4. Når Azure er færdig med at implementere WebApp, klik på **URL-adressen** på indstillingssiden web app i Azure, eller skriv URL-adressen i en webbrowser. For eksempel`http://carprediction.azurewebsites.net.`

5. Når online køres første gang dig det beder om den **API Send URL-adresse** og **API-nøgle**.
Angiv de værdier, du gemte tidligere:
    - **Anmode om URI** fra siden API hjælp til **API Send URL-adresse**
    - **API-nøgle** fra dashboardet web-tjenesten for den **API-nøgle**.

    Klik på **Send**.

    ![Skrive indlæg URI og API-nøgle][image6]

6. WebApp viser konfigurationssiden dens **Web App** med de aktuelle indstillinger for web-tjenesten. Her kan du foretage ændringer af indstillingerne, der bruges af WebApp.

    > [AZURE.NOTE] Ændring af indstillinger her, ændres kun de for denne WebApp. Den ændre ikke standardindstillingerne for din webtjeneste. Hvis du ændrer **Beskrivelse** her ændre ikke den beskrivelsen vises på dashboardet web-tjenesten i Machine Learning Studio.

    Når du er færdig, skal du klikke på **Gem ændringer**, og klik derefter på **Gå til startsiden**.

7. På startsiden, du kan angive værdier for at sende til din webtjeneste, klik på **Send**, og resultatet vises.

Hvis du vil vende tilbage til siden **konfiguration** , skal du gå til den `setting.aspx` side af WebApp. For eksempel: `http://carprediction.azurewebsites.net/setting.aspx.` bliver du bedt om at angive API-nøglen igen – du skal bruge, for at få adgang til siden og opdatere indstillingerne for.

Du kan stoppe, genstart eller slette web app i portalen Azure som en anden WebApp. Så længe den kører kan du gå til privat webadressen og angive nye værdier.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Sådan bruges skabelonen batchen udførelse af Service (BES)

Du kan bruge skabelonen BES web app på samme måde som skabelonen Ressourceposter, bortset fra at den web-app, der er oprettet, kan du sende flere rækker af data og modtage flere resultater.

Resultaterne fra en webtjeneste batchen udførelse af gemmes i en objektbeholder Azure-lager. inputværdierne kan komme fra Azure lagerplads eller en lokal fil.
Tilfældet, skal du bruge en objektbeholder til Azure lagring til at indeholde de resultater, der returneres af WebApp, og du skal gøre din inputdataene klar.

![Proces for at bruge BES webskabelon][image2]

1. Følg den samme fremgangsmåde for at oprette BES online som for skabelonen Ressourceposter, undtagen:
    - Få **Request URI** fra siden Hjælp til **Udførelse af BATCHEN** API til webtjenesten.
    - Gå til [Azure ML batchen udførelse af tjenesten Web App skabelon](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) til at åbne skabelonen BES på Azure Marketplace, og klik på **Opret Web App**.

2. Angive, hvor du vil have resultatet af gemt, skal du angive destination objektbeholder oplysninger på hjemmesiden web app. Også angive, hvor WebApp kan få inputværdierne i en lokal fil eller en objektbeholder til Azure lagring.
Klik på **Send**.

    ![Gemme oplysninger][image7]

Web-appen vises en side med jobstatus.
Når jobbet er færdig, får du placeringen af resultaterne i Azure blob-lager. Du har også mulighed for at hente resultaterne til en lokal fil.

## <a name="for-more-information"></a>Du kan finde flere oplysninger

Til at få mere at vide om...

- opretter en machine learning forsøg med maskine læ Studio, skal du se [oprette din første forsøg i Azure maskine læ Studio](machine-learning-create-experiment.md)

- Sådan installeres maskine hvad du har lært eksperimentere som en webtjeneste, skal du se [Implementer en Azure maskine læ webtjeneste](machine-learning-publish-a-machine-learning-web-service.md)

- andre måder at få adgang til din webtjeneste, se, [hvordan du bruge en Azure Machine Learning-webtjeneste](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
