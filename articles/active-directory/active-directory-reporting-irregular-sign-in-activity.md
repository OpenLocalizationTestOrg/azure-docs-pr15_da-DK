<properties
    pageTitle="Uregelmæssige logon aktivitet"
    description="En rapport, der indeholder log in-programmer, der er identificeret som uoverensstemmende efter vores læ algoritmer computer."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="irregular-sign-in-activity"></a>Uregelmæssige logon aktivitet

Uregelmæssige logon er dem, der er blevet identificeret af vores machine learning-algoritmer, på grundlag af en "umuligt rejse"-betingelse, kombineres med en uoverensstemmende logon placering og en enhed. Dette kan betyde, at en hacker har logget på ved hjælp af denne konto.
Hvis vi støder på 10 eller mere uoverensstemmende logon begivenheder i et tidsinterval på 30 dage eller derunder, sender vi en besked via mail til den globale administratorer. Sørg for at medtage aad-alerts-noreply@mail.windowsazure.com på listen over sikre afsendere.
