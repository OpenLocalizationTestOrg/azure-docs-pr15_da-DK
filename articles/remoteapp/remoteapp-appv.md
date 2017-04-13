<properties
    pageTitle="Brug af App-V-apps med Azure RemoteApp | Microsoft Azure"
    description="Lær at bruge App-V apps i Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Brug af App-V apps i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Du kan bruge App-V-programmer i en Azure RemoteApp hybrid samling, som kræver forbindelse til et domæne.

Før du går i gang, skal du kontrollere at installere App-V 5.1 klienten med de seneste opdateringer. Du skal oprette et [brugerdefineret billede](remoteapp-create-custom-image.md) , der indeholder App-V-klienten.  

Det er nemt at bruge din eksisterende App-V-infrastruktur med Azure RemoteApp. Da en hybrid af websteder er installeret i en Azure VNET, der har adgang til domænecontrolleren, og FOS er domæne tilsluttet, kan du udnytte din eksisterende App-v til infrastruktur og installation metoder til at easyily App-V værtsprogram i Azure RemoteApp. Her er nogle overvejelser, du skal være opmærksom på baseret på typen App-V-installation, du har i øjeblikket:

| Konfigurationsindstillinger |                       | Positive                                                               | Negative                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Leveringsmetode       | Streaming (efter behov) | App er altid senest og frisk                                     | Ventetid for første gang                                                                                    |
|                       | Tilsluttet               | Hurtigste; App findes allerede på VM                              | Filer bliver større - træder billede plads (grænsen 127 GB)                                                           |
| App placering lagerplads  | Delt indhold        | APP'en kører i hukommelsen for Azure RemoteApp forekomst                         | Eats hukommelse og god forbindelse til streaming (filserver), hvor appen er placeret                      |
|                       | Disken (cachelagret)         | Hurtig udførelse. App ikke er afhængig af tilgængeligheden af indholdskilde | Filer bliver større - træder billede plads (grænsen 127 GB)                                                           |
| Målretning af             | Bruger                  | Kræver fuld enkeltstående App-V infrastruktur                          |                                                                                                       |
|                       | Global (computer)      |  Udfyldt publicere eller målrette ved hjælp af udgivelse server                         |  Har du brug at opdatere dit Azure billede, hvis du vil opdatere appen (stort). Tager mere plads på billede. |

 Når du opretter den brugerdefinerede afbildning og din hybrid samling, udgive dit program, tildele brugere og få glæde af dine eksisterende App-V-programmer, der er hostet i Azure RemoteApp leveret til enhver enhed hvor som helst.
