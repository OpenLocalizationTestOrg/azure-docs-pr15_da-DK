
<properties
    pageTitle="Beskytte apps og ressourcer i Azure RemoteApp | Microsoft Azure"
    description="Lær, hvordan du låse apps og ressourcer i Azure RemoteApp"
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



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Beskytte apps og ressourcer i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp giver brugere adgang til centralt administrerede Windows apps, hvor du kan bestemme, hvad brugerne kan og ikke kan gøre.  Dette er især nyttig, når brugeren opretter forbindelse fra en ikke-administreret-enhed (som deres personlige Macbook), og du vil kontrollere brugeradgangen eller oplevelse.

Eksempelvis hvis du bruger Active Directory til brugergodkendelse, og du vil forhindre, at brugerne kopierer data ud af en app, kan du konfigurere en Remote Desktop Gruppepolitik til Bloker brugere fra kopiering af data.

Et andet eksempel er, hvis du vil blokere internetadgang til en bestemt app i af websteder. Du kan oprette en regel i Windows Firewall, der blokerer adgang, når du opretter billedet til din samling.

## <a name="implementation-options"></a>Implementeringsindstillinger

  Her er de vigtigste implementeringsindstillinger, som kan bruges enkeltvis eller sammen, efter behov:

1.  Hvis din RemoteApp samling er domæne tilsluttet kan du gennemtvinge en [Gruppepolitik](https://technet.microsoft.com/library/cc725828.aspx) (med undtagelse af den inaktiv og Afbryd forbindelse timeout politikker beskrevet [her](../azure-subscription-service-limits.md)).
2.  Som et alternativ til gruppepolitik (Hvis din samling er ikke tilsluttet sig domæne, eller du ikke har de rette tilladelser i AD), kan du konfigurere [Lokale politikker](https://technet.microsoft.com/library/cc775702.aspx) til din skabelonbillede.  Bemærk, at gruppen politikker trumf lokale politikker, når der opstår en konflikt.
3.  Nogle indstillinger for OS/app kan ikke konfigureres via politik, men det kan være via nøgle i registreringsdatabasen ved hjælp af [værktøjet RegEdit](./remoteapp-hybridtrouble.md) under konfigurationen af din skabelonbillede.
4.  Du kan bruge [Windows Firewall](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) til at styre netværksadgang til og fra computeren, hvor app'en kører. Kun Sørg for, at du ikke blokerer URL-adresser og porte, der er defineret her.
5.  Du kan bruge [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) til at kontrollere, hvilke programmer og filer brugere kan køre. For eksempel kan erfaren brugere finde ud af, hvordan du kører programmer, at du ikke er udgivet, men, der er tilgængelige i det billede, du brugte til at oprette gruppen af websteder – AppLocker kan blokere dette.

## <a name="detailed-information"></a>Detaljerede oplysninger

- Følgende RDS politikker er sandsynligvis er mest nyttige:
    - [Enhed og Ressourceomdirigering](https://technet.microsoft.com/library/ee791794.aspx)
    - [Printeromdirigering](https://technet.microsoft.com/library/ee791784.aspx)
    - [Profiler](https://technet.microsoft.com/library/ee791865.aspx).
- Bemærk, at konfigurere omdirigeringer via RemoteApp PowerShell modul (som vist [her](./remoteapp-redirection.md)) er baseret på klientmaskinen for at gennemtvinge politikken, så hvis sikkerhed er den primære målsætning skal du gennemtvinge politikken via den lokale politik skabelon billede eller via Gruppepolitik.
- [Windows Server 2012 R2 politikker](https://technet.microsoft.com/library/hh831791.aspx).
- [Office 2013 politikker](https://technet.microsoft.com/library/cc178969.aspx) (herunder [hvordan du tilpasser værktøjslinjen Office](https://technet.microsoft.com/library/cc179143.aspx)).
