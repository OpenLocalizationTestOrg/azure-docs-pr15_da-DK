<properties
    pageTitle="Azure RemoteApp licensering | Microsoft Azure"
    description="Få mere at vide, hvordan licensering fungerer i Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Hvordan fungerer licensering arbejde i Azure RemoteApp?

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Så du har konfigureret din Azure RemoteApp, oprettes skabelonerne, tjeneste og er klar til at publicere apps til brugerne. Men der er stadig en sidste ting at finde ud af: licenser. Hvordan fungerer kun licensering sammen for RemoteApp og for de apps, du deler via RemoteApp?

RemoteApp kræver ikke en hvilken som helst Windows-licenser eller Remote Desktop CAL'er. Dit abonnement sig tager af i RemoteApp side sig selv. (Se oplysninger om [priser planer](https://azure.microsoft.com/pricing/details/remoteapp).)

Hvis du bruger en af de billeder, der er inkluderet i dit abonnement, kan du dele en af de apps, der er installeret på billedet uden at bruge en separat licens. Hvis du bruger Windows Server 2012 R2 skabelonbillede til at opbygge din samling, kan du dele System Center Endpoint Protection med dine brugere. De eneste undtagelser til denne regel er Office 365 Pro plus, der kræver en separat abonnement, og Office 2013, hvilket ikke kan deles i en fremstilling af websteder.

Hvis du vil bruge Office 365 skabelon billedet, som følger med RemoteApp, skal du have en *eksisterende* Office 365 ProPlus plan. På samme måde gælder for alle Office 365-Apps, som du publicerer ved hjælp af en brugerdefineret skabelon. Du skal aktivere apps med dine egne abonnement. Dette gælder for både prøveversion og betalte abonnementer. Hvis du vil bruge Office 365-skabelonbillede under prøveversionen, *og du ikke allerede har et abonnement*, skal du gå til siden Office 365 til at [tilmelde dig](https://go.microsoft.com/fwlink/p/?LinkID=403802) til et prøveabonnement. Se [hvordan RemoteApp og Office fungerer sammen?](remoteapp-o365.md) kan finde flere oplysninger.

Hvis i prøveperioden, du ikke vil have vist en Office 365-prøveabonnement, skal du bruge Office 2013 Professional Plus skabelon billede, der følger med RemoteApp. Skabelonbillede af denne kan kun bruges i 30 dage og ikke kan konverteres til et betalt af websteder.

For andre apps skal du kontrollere, at du har licens til at dele app.

Der giver mening, højre? Du kan udgive en app, som du er lovligt ret til at dele. Og du har brug at sikre, at du virkelig er berettiget til at dele dine programmer.

Vær opmærksom på, at du ikke kan bruge en CAL eller Volume Licensing aftale i en skyen af websteder. Du *kan* bruge en volumenlicens aftale til at aktivere programmer i hybride af websteder (med undtagelse af Office). Du skal blot installere dem på din skabelonbillede fra Volume Licensing medier. Følg oplysninger fra producenten af programmet til at installere licenser i et Remote Desktop-miljø.
