
<properties
    pageTitle="Sidetilpasning oplysninger for en VNET i Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide om IP-adresse kravene til Azure RemoteApp kører med en VNET"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Størrelseshåndtag oplysninger for en VNET i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Når du bruger Azure RemoteApp med et virtuelt netværk (VNET), bruger RemoteApp IP-adresser i undernettet. Baseret på skalaen på din RemoteApp-tjeneste, vil du sikre, at dit undernet har tilstrækkelig IP-adresser, der er tilgængelige for RemoteApp virtuelle computere. Mens vejledningen størrelseshåndtag ikke perfekte givet hvordan RemoteApp dynamisk drejer op og drejer ned virtuelle maskiner med en samling, hjælper dig med at anslå dit undernet område. Dette er især vigtigt, som når en RemoteApp tjeneste er placeret i en VNET, kan du øge størrelsen undernet uden at fjerne RemoteApp.

For hver RemoteApp af websteder, du vil køre på maksimale kapacitet, bør du have 100 IP-adresser, der er tilgængelige. Hvis du har et RemoteApp-samling i den almindelige plan, og du vil have de maksimale 500 brugere, bør du have 100 IP-adresser for den pågældende samling. På samme måde, skal du 100 IP-adresser for en RemoteApp af websteder i den grundlæggende struktur, som har 800 brugere. Hvis du planlægger at har færre brugere (mindre end maksimalt), kan du reducere de IP-adresser, det er nødvendigt per af websteder. Kravet om mindste undernet størrelse er 30 IP-adresser (/ 27).

Se følgende oplysninger for at kontrollere, at din VNET er konfigureret og fungerer propertly:

- [Overføre fra en personlig VNET til en Azure VNET](remoteapp-migratevnet.md)
- [Validere Azure VNET til brug med Azure RemoteApp](remoteapp-vnet.md)
