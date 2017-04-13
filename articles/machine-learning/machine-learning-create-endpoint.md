<properties
    pageTitle="Oprette Web service slutpunkter i Machine Learning | Microsoft Azure"
    description="Oprette Web service slutpunkter i Azure Machine Learning"
    services="machine-learning"
    documentationCenter=""
    authors="hiteshmadan"
    manager="padou"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="himad"/>


# <a name="creating-endpoints"></a>Oprette slutpunkter

>[AZURE.NOTE] Dette emne beskrives teknikker gældende til en klassisk webtjeneste.

Når du opretter webtjenester, som du sælger frem til dine kunder, skal du angive erfaren modeller til hver kunde, der stadig er sammenkædet med forsøget hvorfra webtjenesten blev oprettet. Desuden skal eventuelle opdateringer til forsøget anvendes selektivt til et slutpunkt uden at overskrive tilpasningerne.

For at gøre dette, kan Azure Machine Learning du oprette flere slutpunkter for en udløst webtjeneste. Hvert slutpunkt i webtjenesten er bekræftet af uafhængig adresseret, begrænset og administreres. Hvert slutpunkt er en entydig URL-adresse og godkendelse nøgle, kan du distribuere til dine kunder.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Tilføje slutpunkter til en webtjeneste

Der er tre måder at tilføje et slutpunkt til en webtjeneste.

* Fra et program
* Via Azure Machine Learning Web Services-portalen
* Selvom portalen Azure klassisk

Når slutpunktet er oprettet, kan du bruge den gennem synkron API'er, batchen API'er, og excel-regneark. Foruden at tilføje slutpunkter via Brugergrænsefladen, kan du også bruge slutpunkt Management API'er til automatisk for at føje slutpunkter.

 >[AZURE.NOTE] Hvis du har tilføjet flere slutpunkter til webtjenesten, kan du ikke slette standardslutpunkt.

## <a name="adding-an-endpoint-programmatically"></a>Tilføje et slutpunkt fra et program

Du kan tilføje et slutpunkt til din webtjeneste fra et program ved hjælp af [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) eksempelkode.

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Tilføje et slutpunkt, ved hjælp af portalen Azure Machine Learning Web Services

1. Klik på Web-tjenester på kolonnen venstre navigationsrude i Machine Learning Studio.
2. Klik på **Administrer slutpunkter**nederst i Web-tjenesten dashboard. Portalen Azure Machine Learning Web Services åbnes med siden slutpunkter for webtjenesten.
3. Klik på **Ny**.
4. Skriv et navn og beskrivelse til det nye slutpunkt. Slutpunkt navne skal være 24 tegn eller mindre længde og skal bestå af små alfabeter eller tal/bogstaver. Vælg logføringsniveauet og om Eksempeldata er aktiveret. Se [aktivere logføring til webtjenester Machine Learning](machine-learning-web-services-logging.md)kan finde flere oplysninger om logføring.

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Tilføje et slutpunkt, ved hjælp af portalen Azure klassisk


1. Log på [Azure klassisk portal](http://manage.windowsazure.com)skal du klikke på **Machine Learning** i venstre kolonne. Klik på det arbejdsområde, som indeholder den webtjeneste, hvor du er interesseret.

    ![Gå til arbejdsområdet](./media/machine-learning-create-endpoint/figure-1.png)

2. Klik på **Web-tjenester**.

    ![Gå til webtjenester](./media/machine-learning-create-endpoint/figure-2.png)

3. Klik på den webtjeneste, du er interesseret i at se en liste over tilgængelige slutpunkter.

    ![Gå til første eller sidste ark](./media/machine-learning-create-endpoint/figure-3.png)

4. Nederst på siden skal du klikke på **Tilføj slutpunkt**. Skriv et navn og beskrivelse, sikre, at der ikke er nogen andre slutpunkter med det samme navn i denne webtjeneste. Du kan lade niveauet begrænsning med er standardværdien, medmindre du har særlige krav. Hvis du vil vide mere om (throttling) skal du se [Skalering API slutpunkter](machine-learning-scaling-webservice.md).

    ![Oprette slutpunkt](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Næste trin

Se, [hvordan du kan bruge en udgivet Azure Machine Learning-webtjeneste](machine-learning-consume-web-services.md).
