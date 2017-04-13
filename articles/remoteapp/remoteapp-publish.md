<properties
    pageTitle="Publicere en app i Azure RemoteApp | Microsoft Azure"
    description="Få mere at vide, hvordan du publicerer programmer og ressourcer i Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Hvordan du publicerer en app i RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Når du opretter din RemoteApp af websteder, skal du publicere apps eller ressourcer, som du vil gøre tilgængelige for dine brugere. Skabelonbillederne leveres med dit abonnement har kun nogle få apps udgivet af standard - for at dele de andre apps, skal du publicere dem.

> [AZURE.NOTE] Har du brug at opdatere en app? Du skal opdatere [billedet](remoteapp-update.md) først.

Klik på **Publicer**under fanen **udgivelse** i portalen. Du kan enten tilføje en app fra billede af designskabelon menuen **Start** eller angive stien til hvor appen er installeret på skabelon billedet. Hvis du vil tilføje fra menuen **Start** , Vælg app til at publicere på listen. Hvis du vælger at angive en sti til app, Angiv et navn til appen og stien til appen. Bruge variabler i path - eksempelvis "% systemdrev %" i stedet for "c:\".

> [AZURE.NOTE] Hvis du vil tilføje din app fra menuen **Start** , du vil have *tilføjet denne app til den * *starte* * menu på dit billede af designskabelon.* Ellers RemoteApp, kun se hvad *er* i menuen **Start** , og du bliver forvirret. 

>For at sikre at din app er i menuen **Start** , skal du placere en genvejsfil - **.lnk** - i mappen %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.

> Hvis du har glemt at tilføje appen til menuen **Start** , når du har oprettet skabelonen, du vil tilføje stien til appen. (Eller genopret skabelon billedet, men det er helt lidt mere arbejde.)


 