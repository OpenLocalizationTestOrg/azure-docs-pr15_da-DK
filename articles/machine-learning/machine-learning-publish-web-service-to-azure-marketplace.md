<properties 
    pageTitle="Publicere machine learning-webtjeneste til Azure Marketplace | Microsoft Azure" 
    description="Hvordan du publicerer dit Azure Machine Learning-webtjeneste til Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Publicere Azure Machine Learning webtjeneste til Azure Marketplace 

Azure Marketplace giver mulighed for at publicere Azure Machine Learning webtjenester, som betales eller ledig tjenester til forbrug ved eksterne kunder. I denne artikel giver et overblik over processen med links til retningslinjer for at komme i gang. Ved hjælp af denne proces, kan du gøre din webtjenester tilgængeligt for andre udviklere til forbrug i deres programmer.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Oversigt over en udgivelsesproces 

Følgende er fremgangsmåden til at publicere en Azure Machine Learning webtjeneste til Azure Marketplace:

1. Oprette og publicere et svar med Machine Learning anmodning om tjenesten (Ressourceposter)
2. Installere tjenesten til fremstilling, og få oplysningerne API-nøgle og OData slutpunkt.
3. Bruge URL-adressen for den publicerede webtjeneste til at udgive til [Azure Marketplace (Datamarkeret)](https://publish.windowsazure.com/workspace/) 
4. Når indsendt, dit tilbud gennemses og skal godkendes før dine kunder kan begynde at købe den. Publiceringsprocessen kan tage et par arbejdsdage. 

## <a name="walk-through"></a>Gennemgå
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Trin 1: Oprette og publicere et svar med Machine Learning anmodning om tjenesten (Ressourceposter)###
 Hvis du allerede ikke har gjort dette, skal du se nærmere på denne [Gennemgå](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Trin 2: Anvende tjenesten til fremstilling og få API-nøgle og OData slutpunkt oplysninger###
1. Vælg indstillingen **MACHINE LEARNING** fra venstre navigationslinje for [Klassisk Azure-portalen](http://manage.windowsazure.com), og vælg dit arbejdsområde. 

2. Klik på fanen **WEB-tjenester** , og vælg den webtjeneste, du vil publicere til marketplace.

    ![Azure Marketplace][workspace]

3. Vælg det slutpunkt, du vil gerne have marketplace forbruge. Hvis du ikke har oprettet en ekstra slutpunkter, kan du vælge **standard** slutpunktet.

4. Når du har klikket på slutpunktet, vil du kunne se **API-nøgle**. Du skal bruge denne oplysning oplysninger senere i trin 3, så vær en kopi af filen.

    ![Azure Marketplace][apikey]

5. Klik på metoden **Anmodning/svar** på dette tidspunkt vi ikke understøtter publicering batchen udførelse af tjenester til marketplace. Derfra føres du til siden API Hjælp for metoden anmodning/svar.

6. Kopiere **OData slutpunktsadresse**, du skal bruge disse oplysninger senere i trin 3.

    ![Azure Marketplace][odata]




Installer tjenesten til fremstilling.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Trin 3: Brug af URL-adressen for den publicerede webtjeneste publicere til Azure Marketplace (DataMarket)###

1.  Gå til [Azure Marketplace (Datamarkeret)](http://datamarket.azure.com/home) 
2.  Klik på linket **Publicer** øverst på siden. Dette kommer du til [Microsoft Azure Udgivelsesportal](https://publish.windowsazure.com)
3.  Klik på sektionen **Udgivere** til at registrere som en udgiver.
4.  Når du opretter et nyt tilbud, markere **Data Services**og derefter klikke på **Opret en ny Data-tjeneste**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  Angiv oplysninger på dit tilbud, herunder en priser plan under **planer** . Bestem, hvis du vil blive tilbudt en gratis eller betalte tjeneste. For at få betalt, give betalingsoplysninger som din bank og skat oplysninger.

6.  Angiv oplysninger om dine tilbud, som titel og beskrivelse til dit tilbud under **Marketing** .

7.  Du kan angive prisen for dine planer for bestemte lande eller lade systemet "autoprice" dit tilbud under **priser** .

8. Klik på **Webtjeneste** som **Datakilde**under fanen **Data Service** .

    ![Azure Marketplace][image2]

9.  Få web service URL-adresse og API-nøgle fra portalen Azure klassisk, som beskrevet i trin 2 ovenfor.

10. Indsætte OData slutpunktsadressen i tekstfeltet **URL-adressen** i dialogboksen Marketplace datatjeneste installation.

11. Vælg **Header** som **Godkendelsesmetode**til **godkendelse**.

    - Skriv "Godkendelse" for **Overskriftsnavnet**.
    - **Sidehoved værdi**skal angive "Bæreren" (uden anførselstegnene), skal du klikke på **på mellemrumstasten** og derefter indsætte API-nøglen.
    - Markér afkrydsningsfeltet **denne tjeneste er OData** .
    - Klik på **Test forbindelse** for at teste forbindelsen.

12. Sørg for **Machine Learning** er valgt under **kategorier**.

13. Når du er færdig med at angive alle metadata om dit tilbud, klik på **Publicer**, og klik derefter **Push til midlertidige**. På dette tidspunkt, får du besked om eventuelle resterende problemer, du har brug for at løse.

14. Når du har kontrolleret fuldførelse af alle de vigtigste problemer, klik på **anmode om godkendelse til at overføre til fremstilling**. Publiceringsprocessen kan tage et par arbejdsdage. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
