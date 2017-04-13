<properties
   pageTitle="Opdatere din Azure RemoteApp samling | Microsoft Azure"
   description="Lær at opdatere din Azure RemoteApp af websteder"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Opdatere en samling i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Der kommer et tidspunkt, milepælsdatoer, når du har brug for at opdatere apps eller billede i Azure RemoteApp af websteder. Hvis du bruger et af billederne Azure RemoteApp abonnementet, i enten en skyen eller hybrid af websteder er inkluderet, håndteres alle opdateringer af Azure RemoteApp sig selv, så du nemt kan være.

Men hvis du bruger et brugerdefineret billede (enten, at du opbygget fra bunden eller, du har oprettet ved at ændre en af vores billeder), er du er ansvarlig for det billede og apps. Hvis du vil opdatere dit billede eller en af apps indeni, skal du oprette en ny, opdaterede version af billedet, og derefter erstatte det nuværende billede i af websteder med denne nye opdaterede billede.

Så hvordan går om opdatering af dine websteder? Det er ret nemt:

1. Opdater det billede, du har brugt i din samling. Anvende en hvilken som helst rettelser eller opdateringer, som det er nødvendigt, og derefter gemme den under et nyt navn.
2. [Overføre](remoteapp-uploadimage.md) eller [importere](remoteapp-image-on-azurevm.md) , billede for at RemoteApp.
3. Nu skal du klikke på **Opdater**på siden af websteder.
4. Vælg det nye billede på listen **Skabelonbillede** .
4. Her er den svære del – du har brug at bestemme, hvordan du håndterer alle brugere, der bruger en app i gruppen af websteder. Har du følgende valgmuligheder:
    - **Give brugerne 60 minutter efter opdateringen**. Så snart opdateringen er afsluttet, vises en meddelelse til alle aktive brugere fortæller dem til at gemme deres arbejde og logge af og logge på igen, i Azure RemoteApp. Efter 60 minutter, bliver alle aktive brugere, der ikke har logget af automatisk logget af. Brugere kan straks logge på igen.
    - **Log brugere ud med det samme**. Så snart opdateringen er færdig, skal du logge alle brugere automatisk uden advarsel. Hvis du vælger denne indstilling, kan brugerne miste data. Men de kan genoprette forbindelsen til appen med det samme.

1. Klik på afkrydsningsfeltet for at starte opdateringen.
