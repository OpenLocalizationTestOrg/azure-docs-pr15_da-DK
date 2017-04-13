
<properties
    pageTitle="Få anbefalinger i batches: Machine learning anbefalinger API | Microsoft Azure"
    description="Azure machine learning-anbefalinger – få anbefalinger i batches"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Få anbefalinger i batches

>[AZURE.NOTE] Få anbefalinger i batches er mere kompliceret end få anbefalinger én ad gangen. Kontrollere API'erne oplysninger om, hvordan du kommer anbefalinger til en enkelt anmodning:

> [Emne til emne anbefalinger](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Bruger-element anbefalinger](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Batchen vundne fungerer kun for builds, der er oprettet efter juli 21 2016.


Der er visse situationer, hvor du skal have fat anbefalinger til mere end ét element ad gangen. Du kan for eksempel være interesseret i at oprette en anbefalinger cache eller endda analyse af typerne anbefalinger, som du får.

Batchen vurdering handlinger, er som vi kalder dem, asynkrone handlinger. Du skal sende anmodningen, vent til handlingen er udført, og derefter indsamle dine resultater.  

Hvis du vil have mere nøjagtig, disse er trinnene for at følge:

1.  Oprette en objektbeholder til lagring af Azure, hvis du ikke allerede har et.
2.  Overføre en input-fil, der beskriver hver af dine anbefaling anmodninger til Azure Blob-lager.
3.  Kick-Start kørslen resultat.
4.  Vent på asynkron handlingen er udført.
5.  Når handlingen er færdig, kan du indsamle resultaterne fra Blob-lager.

Lad os gennemgå hver af disse trin.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Oprette en objektbeholder til lagring, hvis du ikke allerede har et

Gå til [Azure-portalen](https://portal.azure.com) , og oprette en ny lagerplads-konto, hvis du ikke allerede har et. For at gøre dette skal du gå til **Ny** > **Data** + **lagerplads** > **Lagerplads konto**.

Når du har en konto med lagerplads, skal du oprette de blob beholdere, hvor du vil gemme input og output fra batchen udførelsen.

Overfør en input-fil, der beskriver hver af dine anbefaling anmoder om til Blob-lager – Lad os ringe fil input.json her.
Når du har en objektbeholder, skal du overføre en fil, der beskriver hver af de anmodninger, du skal udføre fra tjenesten anbefalinger.

En gruppe kan udføre kun én type anmodning fra et bestemt build. Vi forklares, hvordan du definerer disse oplysninger i næste afsnit. Nu skal Lad os antage, at vi skal udføre element anbefalinger af en bestemt build. Indtast filen indeholder derefter input oplysninger (i dette tilfælde frø elementer) for hvert af anmodningerne.

Dette er et eksempel på ser input.json filen ud:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Filen er en JSON-fil, hvor hver af anmodningerne har de oplysninger, der er nødvendige for at sende en indkaldelse til anbefalinger, som du kan se. Oprette en lignende JSON-fil til de anmodninger, du skal udføre, og kopiere den til objektbeholderen, du lige har oprettet i Blob-lager.

## <a name="kick-start-the-batch-job"></a>Kick-Start kørslen

Næste trin er at sende en ny kørsel. Du kan finde yderligere oplysninger finder [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

Anmodningsteksten af API'EN skal definere de placeringer, hvor det input, output og fejlfiler skal gemmes. Det skal også definerer de legitimationsoplysninger, som er nødvendige for at få adgang til disse placeringer. Desuden skal du angive nogle parametre, der gælder for hele batchen (typen af anbefalinger til at anmode om model/Opret at bruge antallet af resultater i opkaldet, og så videre.)

Dette er et eksempel på, hvad anmodningsteksten skal se ud:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Her et par vigtige ting at være opmærksom på:

-   I øjeblikket **authenticationType** altid skal du vælge **PublicOrSas**.

-   Du har brug at få et delt Access signatur (SAS) token tillade anbefalinger API til at læse og skrive fra/til kontoen Blob-lager. Du kan finde flere oplysninger om, hvordan du kan generere SAS tokens kan findes på [siden anbefalinger API](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   Den eneste **apiName** , der understøttes i øjeblikket er **ItemRecommend**, som bruges til emne til emne anbefalinger. Samling, understøtter ikke i øjeblikket bruger til emne anbefalinger.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Vent på asynkron handlingen er udført

Når du starter batchhandlingen, returnerer svaret overskriften handling-placering, der giver dig de oplysninger, der er nødvendige for at spore handlingen.
Du kan spore handlingen ved hjælp af [Hente handlingen Status API]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), ligesom du gør til sporing af driften af en handling af build.

## <a name="get-the-results"></a>Få vist resultaterne

Når handlingen er udført, at antage, at der ikke er nogen fejl, kan du indsamle resultaterne fra din output Blob storage.

I eksemplet nedenfor viser, hvordan output kan se ud. I dette eksempel viser vi resultater for en gruppe med kun to anmodninger (af pladshensyn).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>Få mere at vide om begrænsninger

-   Kun én kørslen kan kaldes per abonnement ad gangen.
-   En sag input batchfil må ikke være mere end 2 MB.
