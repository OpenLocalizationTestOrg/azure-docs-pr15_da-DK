<properties
    pageTitle="Oprette et Azure RemoteApp billede | Microsoft Azure"
    description="Få mere at vide om de tilgængelige indstillinger for oprettelse af billeder til Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="create-an-azure-remoteapp-image"></a>Oprette et Azure RemoteApp-billede

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp bruger billeder til at holde de apps, du deler med dine brugere. (Vi få dit billede og bruge det til at oprette FOS - det er brugere adgang, når de logge på Azure RemoteApp.) For at oprette en Azure RemoteApp af websteder med valget af programmer, uanset om det er skyen eller hybrid, skal starte du med at oprette et billede med disse programmer, der er installeret. Derefter skal du oprette en samling, der bruger billedet, tildele brugere til gruppen af websteder og publicere apps til disse brugere.

Har du forskellige valgmuligheder til at oprette eller ved hjælp af billeder. Grundlæggende [krav](remoteapp-imagereqs.md) om et billede er, at den kører Windows Server 2012 R2 og har rollen Remote Desktop Session Host (RDSH), der er installeret. Hvordan du kommer, der er hvor ting få interessant.

Du har følgende indstillinger, når det drejer sig om billeder:

- Du kan importere og bruge et [billede, der er baseret på en Azure virtuelt](remoteapp-image-on-azurevm.md). Dette er god til line of business-apps, der kræver brugerdefinerede indstillinger. Du kan tilpasse billedet til at arbejde for appen.
- Du kan [oprette og overføre et brugerdefineret billede](remoteapp-create-custom-image.md). Dette er god, hvis du allerede har et billede, du bruger til dit lokale Remote Desktop Services installation.
- Du kan bruge en af [billeder af webstedsskabeloner](remoteapp-images.md) inkluderet i abonnementet RemoteApp. Disse billeder oprettes og vedligeholdes af RemoteApp teamet og indeholder nogle almindelige programmer (som Office-pakken), som du kan gøre tilgængelige for dine brugere. Bemærk, at kun Office 365 Pro Plus billedet kan bruges i et produktionsmiljø.

Uanset hvor du får dit billede, eller hvordan du opretter, får du vil kontrollere, at du forstår [krav til app](remoteapp-appreqs.md) til at sikre, at din app fungerer godt i RemoteApp. Derefter er det næste trin at oprette en [skyen](remoteapp-create-cloud-deployment.md) eller [hybrid](remoteapp-create-hybrid-deployment.md) af websteder.
