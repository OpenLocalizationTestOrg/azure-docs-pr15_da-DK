<properties
    pageTitle="Log ins efter flere fejl"
    description="En rapport, der angiver brugere, der har logget på efter flere på hinanden følgende mislykkedes log i forsøg."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Logon efter flere fejl
Denne rapport angiver brugere, der har logget på efter flere på hinanden følgende mislykkedes log i forsøg. Mulige årsager kan være:

- Brugeren havde glemt adgangskode</li><li>Brugeren er offer for en vellykket adgangskode gætte brute gennemtvinge angreb

Resultater fra denne rapport viser dig antallet på hinanden følgende mislykkede logon forsøg foretaget før den vellykket logon og et tidsstempel, der er knyttet til den første vellykket logon.

**Rapportindstillinger**: Du kan konfigurere det mindste antal på hinanden følgende mislykkede log i forsøg, der skal udføres, før den kan vises i rapporten. Det er vigtigt at bemærke, at ændringerne ikke anvendes på alle eksisterende mislykkedes log in-programmer, der aktuelt vises i din eksisterende rapport, når du ændrer denne indstilling. De vil dog blive anvendt på alle fremtidige log-ins. Ændringer til denne rapport kan kun foretages af licenserede administratorer.


![Log ins efter flere fejl](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
