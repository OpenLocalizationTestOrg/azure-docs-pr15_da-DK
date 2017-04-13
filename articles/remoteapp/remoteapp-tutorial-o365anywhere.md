<properties
   pageTitle="Få den samme grænseflade i Office 365 på en hvilken som helst enhed med Azure RemoteApp | Microsoft Azure"
   description="Lær at dele en Office 365-app med dine brugere ved hjælp af Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Få den samme grænseflade i Office 365 på en hvilken som helst enhed med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

I denne artikel beskrives, hvordan du kan installere Office 365 på en hvilken som helst enhed i din virksomhed. Dine brugere kan få de samme funktioner og Brugergrænsefladen oplevelse på Android, æble- og Windows.

Vi vil udføre dette med Azure RemoteApp ved at placere Office 365 på skala kan virtuelle maskiner i Azure, som brugere kan oprette forbindelse til. Dette sæt af virtuelle maskiner vi kalder en "cloud samling".

## <a name="create-a-cloud-collection"></a>Oprette en skyen af websteder

Først når du har oprettet en Azure-konto, gå til **RemoteApp** ved at klikke på linket i venstre side.
![Viser Azure RemoteApp på Azure-portalen](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Fortsæt derefter ved at klikke på **Ny** på den nederste og "hurtig oprette" en samling. Angiv et navn, området, abonnementet, planen og billedet "Office Proffesional 2013", som vi giver.
![Dialogboksen Opret](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Når du er færdig med formularen skal starte oprettelsen af websteder. Dette kan tage til en time, eller så.

![Venter](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Når processen er færdig, ser den omtrent sådan ud. Hvis vi skal du klikke på **publicering** kan vi se, at de fleste Office-programmer er blevet publiceret for os allerede.
![Oprettet af websteder](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Publicerede apps](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

På dette tidspunkt kan du også tilføje flere brugere, der har adgang til denne samling ved at klikke på **Brugeradgang**.
![Konfigurere brugeradgang](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Nu Lad os prøve at oprette forbindelse til Office 365!

## <a name="connect-to-office-365"></a>Oprette forbindelse til Office 365

Vi hoved [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), Rul ned, og klik på **Hent klienter** for at installere Azure RemoteApp-klienten på den enhed, du er på. Skærmbillederne nedenfor er til Windows.

Når programmet starter bliver du bedt om at logge på med din Microsoft-konto (tidligere kaldet en "Live-ID"), skal du bruge det samme, som din Azure-konto på nuværende tidspunkt. Når du er logget på skal du få vist en meddelelse om ny invitationer, klik på der og skal du se en liste som nedenfor. Acceptere invitationen, der svarer til din mail til ejeren af Azure-konto.

![Ny invitation](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Hvordan det ser ud, når der er nye invitationer.

![Acceptere et program](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Når du acceptere invitationen, skal du se alle Office-apps i Azure RemoteApp-klienten.

![Listen over apps](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Når du klikker på skal et af følgende programmet skal starte på den Azure virtuelle maskine, og du klar! Nyd!

![starte](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
