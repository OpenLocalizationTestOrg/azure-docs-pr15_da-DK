<properties
   pageTitle="Installere en ny webtjeneste"
   description="Arbejdsprocessen med at udrulle en ARM baseret webtjeneste"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Installere en ny webtjeneste

Microsoft Azure Machine learning nu indeholder webtjenester, der er baseret på [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) tillade for nye faktureringsindstillinger planlægge og implementere din webtjeneste til flere områder.

Generelle arbejdsprocessen til at installere en webtjeneste, ved hjælp af Microsoft Azure Machine Learning webtjenester er:

* Oprette en skønnet forsøg
* installere det.
* konfigurere navnet
* fakturering plan
* Test den
* bruge den.

Følgende grafik viser arbejdsprocessen.

![Web service installation arbejdsproces][1]
 
## <a name="deploy-web-service-from-studio"></a>Installere webtjeneste fra Studio 

Installere et forsøg som en ny webtjeneste. Log på Machine Learning Studio og oprette en ny skønnet webtjeneste. 

**Bemærk**: Hvis du allerede har installeret et forsøg som en klassisk webtjeneste kan du installere det som en ny webtjeneste.
 
Klik på **Kør** i bunden af forsøg lærredet, og klik derefter på **Installere webtjeneste** og **Installere webtjeneste [ny]**. Siden Installation af Machine Learning Web Service manager åbnes.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Machine Learning Web Service Manager installerer forsøg side
Angiv et navn til webtjenesten, på siden installere forsøg.
Vælg en priser plan. Hvis du har en eksisterende priser plan, kan du vælge den, skal ellers du oprette en ny pris plan for tjenesten. 

1.  Rul ned, Vælg en eksisterende plan i **Pris Plan** , eller Vælg indstillingen **Vælg nye plan** .
2.  I **Plannavnet**skal du skrive et navn, der identificerer planen på din faktura.
3.  Vælg en af de **Månedlige planlægge niveauer**. Bemærk, at planlægge niveauer standard til planerne til dine standardområde og din webtjeneste er installeret til dette område.

Klik på **Implementer** og siden Hurtig start for din web service åbnes.

## <a name="quickstart-page"></a>Hurtig start side
Websiden for Hurtig start giver dig adgang og vejledning i de mest almindelige opgaver, du udfører efter oprettelse af en ny webtjeneste. Her kan du let adgang til både **testsiden** og **forbrug** side.

## <a name="testing-your-web-service"></a>Test din webtjeneste

Klik på Test webtjeneste under almindelige opgaver fra siden Hurtig start.   

Teste webtjenesten som en anmodning om svar Service (Ressourceposter):

* Klik på **Test** på menulinjen.
* Klik på **Anmodning om svar**.
* Angiv de relevante værdier for kolonnerne input af din forsøg.
* Klik på Test **Anmodning om svar**.

Du resultaterne vises i højre side af siden.

Hvis du vil afprøve en batchen udførelse af Service (BES)-webtjeneste, vil du bruge en CSV-fil:

* Klik på **Test** på menulinjen.
* Klik på **Batch**.
* Klik på Gennemse under dit input, og gå til datafilen eksempel.
* Klik på **Test**.

Status for din test vises under **Teste batchen sager**.

## <a name="consuming-your-web-service"></a>Forbruger-webtjeneste

Når installeres som en webtjeneste, giver Azure Machine Learning forsøg en REST-API, der kan benyttes af en lang række enheder og platforme. Dette skyldes, at enkelt REST-API accepterer og svares med JSON formaterede meddelelser. Portalen Azure Machine Learning indeholder kode, der kan bruges til at ringe til webtjenesten i R, C# og Python.
 
Du kan finde på siden forbrug:

* API-nøgle og URI'ER for forbrug webtjeneste i apps.
* Excel og web app-skabeloner for at starte start forbrug processen.
* Eksempel på kode i C#, python og R for at komme i gang.

Se, [hvordan du bruge en Azure Machine Learning webtjeneste, der er blevet installeret fra en computer, læ forsøg](machine-learning-consume-web-services.md)kan finde flere oplysninger om forbrug webtjenester.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om forbrug webtjenester, i:

[Hvordan du kan bruge en Azure Machine Learning-webtjeneste, der er blevet installeret fra en computer, læ forsøg](machine-learning-consume-web-services.md)

[Azure Machine Learning-webtjenester: Installation og forbrug](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
