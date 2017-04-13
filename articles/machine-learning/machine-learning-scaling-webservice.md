<properties
   pageTitle="Skalering webtjeneste | Microsoft Azure"
   description="Lær at tilpasse en webtjeneste ved at øge på dokumentsammenfald og tilføje nye slutpunkter."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="vejledning i, web services Azure maskine operationalization, skalering, slutpunkt, på dokumentsammenfald"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Skalering en webtjeneste

>[AZURE.NOTE] Dette emne beskrives teknikker gælder for en klassisk Machine Learning-webtjeneste. 

Som standard hver publicerede webtjeneste er konfigureret til at understøtte 20 samtidige anmodninger og kan være helt op til 200 samtidige anmodninger. Mens Azure klassisk portalen gør det muligt at angive denne værdi, Azure Machine Learning optimerer automatisk indstillingen for at give den bedste ydeevne for webtjenesten og værdien portalen ignoreres. 

Hvis du planlægger at kalde API med en højere belastning end værdien Maks samtidige opkald over 200 understøtter, skal du oprette flere slutpunkter på den samme webtjeneste. Du kan derefter tilfældigt distribuere din Indlæs i dem alle.

## <a name="add-new-endpoints-for-same-web-service"></a>Tilføje nye slutpunkter for samme webtjeneste

Skaleringen af en webtjeneste er en almindelig opgave. Der er nogle mulige årsager til at skalere til understøtter mere end 200 samtidige anmodninger, øge tilgængeligheden gennem flere slutpunkter eller angive separate slutpunkter til webtjenesten. Du kan øge skalaen ved at tilføje yderligere slutpunkter for den samme webtjeneste via [Azure klassisk portal](https://manage.windowsazure.com/) eller portalen [Azure Machine Learning-webtjeneste](https://services.azureml.net/) .

Du kan finde flere oplysninger om tilføjelse af nye slutpunkter, [Oprette slutpunkter](machine-learning-create-endpoint.md).

Husk på, ved hjælp af en høj på dokumentsammenfald tæller kan være skadelige, hvis du ikke ringer API med en tilsvarende høj sats. Du kan se sporadisk timeout og/eller spidser i ventetid, hvis du placerer en relativt lav belastning på en API, der er konfigureret til høj belastning.

De synkrone API'er bruges typisk i situationer, hvor en lav ventetid ønskes. Ventetid her indebærer tid det tager for API til at udføre en anmodning, og ikke-konto til en hvilken som helst netværksforsinkelser. Antag, at du har en API med en 50 ms ventetid. Mulighed for at fuldt forbruge tilgængelige kapacitet med begrænsning niveau høj og Maks samtidige opkald = 20, skal du ringe denne API 20 * 1000 / 50 = 400 times sekundet. Udvide dette yderligere en maks samtidige opkald over 200, kan du ringe API 4000 gange sekundet, hvis du allerede har en 50 ms ventetid.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
