<properties
    pageTitle="Brug af Outlook i Azure RemoteApp | Microsoft Azure" 
    description="Lær, hvordan du konfigurerer og bruger Outlook i Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Brug af Microsoft Outlook i Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Azure RemoteApp understøtter Microsoft Outlook O365. Læs mere om, hvordan [Office fungerer i Azure RemoteApp](remoteapp-officesubscription.md). Der er et par anbefalede indstillinger for Outlook, når den bruges i Azure RemoteApp.

## <a name="cached-mode"></a>Cachelagret tilstand
Cachelagret tilstand er en anbefalet konfiguration, når du bruger Outlook i Azure RemoteApp. Når du konfigurerer en Outlook 2013-konto for at bruge Cachelagret Exchange-tilstand, fungerer Outlook 2013 fra en lokal kopi af brugerens Microsoft Exchange-postkassen, der er gemt i en offline-datafil (OST-fil) på brugerens computer, sammen med Offline Address Book Konfigurert. Cachelagrede postkasse og OAB opdateres med jævne mellemrum fra tjenesten O365. Få mere at vide om [forskellene mellem cachelagrede og online tilstand](https://technet.microsoft.com/library/jj683103.aspx).

Brugeren kan markere **Cachelagret Exchange-tilstand** eller **Onlinetilstand** under konfiguration af konto eller ved at ændre indstillingerne for kontoen. Du kan også distribuere én tilstand eller den anden ved hjælp af Office Customization Tool (OCT) eller Gruppepolitik.  

Læs [trinvise instruktioner om aktivering af cachelagret tilstand](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Søgning
I Azure RemoteApp har begrænsninger ved hjælp af søgning i Outlook. Azure RemoteApp bruger puljebaseret FOS for at imødekomme brugersessioner. Search-indeksering, afhænger af det computer-ID, som er forskellige for forskellige FOS. Det er muligt, hver gang en bruger logger ind i Azure RemoteApp, er de dirigeret til en ny VM. Der medfører, hvis vi aktivere lokal søgning, indekseringstjenesten kører, hver gang computer-ID ændres (når brugeren er på en anden VM). Afhængigt af størrelsen på den. OST-fil, indekseringstjenesten kan tage lang tid at fuldføre og brug af ressourcer, der kræves for andre apps. Søg ikke vil kun være langsom, men kan ikke give resultater. Ved hjælp af en onlinetilstand-profil vil kan løse dette problem, men vil ud over overordnede ydeevnen på grund af manglende en lokal cache (se linket ovenfor for at finde flere oplysninger om forskellen mellem cachelagrede og online tilstand). Desværre kan ikke deaktiveres indekseret/lokal søgning og onlinesøgning kan ikke aktiveres som standard i Outlook 2013.
