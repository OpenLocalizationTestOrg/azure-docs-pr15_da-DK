<properties
 pageTitle="Planer og fakturering i Azure Scheduler"
 description="Planer og fakturering i Azure Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>Planer og fakturering i Azure Scheduler

## <a name="job-collection-plans"></a>Jobstatus samling planer

Jobbet samlinger er objektet fakturerbar i Azure Scheduler. Jobbet samlinger indeholder et antal sager og kommer i tre planer – gratis, Standard og Premium –, der er beskrevet nedenfor.

|**Jobbet samling Plan**|**Maksimale antal sager per Job af websteder**|**Maks gentagelse**|**Maks Job samlinger per abonnement**|**Begrænsninger**|
|:---|:---|:---|:---|:---|
|**Gratis**|5 job per job af websteder|Én gang i timen. Kan ikke udføre opgaver oftere end én gang i timen|Et abonnement er tilladt op til 1 gratis job af websteder|Kan ikke bruge [HTTP udgående godkendelse objekt](scheduler-outbound-authentication.md)
|**Standard**|50 job per job af websteder|Én gang i minuttet. Kan ikke udføre opgaver oftere end én gang i minuttet|Et abonnement er tilladt op til 100 standard job af websteder|Adgang til fuld funktionssæt Scheduler|
|**P10 Premium**|50 job per job af websteder|Én gang i minuttet. Kan ikke udføre opgaver oftere end én gang i minuttet|Et abonnement er tilladt op til 10.000 P10 Premium job af websteder. <a href="mailto:wapteams@microsoft.com">Kontakt os</a> få mere at vide.|Adgang til fuld funktionssæt Scheduler|
|**R20 = Premium**|1000 job per job af websteder|Én gang i minuttet. Kan ikke udføre opgaver oftere end én gang i minuttet|Et abonnement er tilladt op til 10.000 r20 = Premium job af websteder. <a href="mailto:wapteams@microsoft.com">Kontakt os</a> få mere at vide.|Adgang til fuld funktionssæt Scheduler|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Opgraderinger og Nedgradering af jobbet samling planer

Du kan opgradere eller nedgradere en sag samling plan når som helst blandt gratis, Standard og Premium planer. Når nedgradere til en gratis job af websteder, kan nedgraderingsrettigheder mislykkes på en af følgende årsager:

- En samling af gratis job allerede findes i abonnement
- En sag i samlingen job har en højere gentagelse end tilladt for sager i gratis job af websteder. Den maksimale gentagelse, der er tilladt i en gratis job samling er én gang hver time
- Der er mere end 5 job i samlingen job
- En sag i samlingen job har en HTTP eller HTTPS handling, der bruger en [HTTP udgående godkendelse objekt](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Fakturering og Azure-planer

Abonnementer betaler ikke gratis job af websteder. Hvis du har mere end 100 standard job samlinger (10 standard fakturering enheder), er det en bedre handel have alle job af websteder i premium-plan.

Hvis du har en standard job indsamling og én premium job samling, er du fakturerede én standard fakturering enhed _og_ én premium fakturering enhed. Scheduler service fakturaer baseret på antallet af aktive job samlinger, der er angivet til standard eller premium; det er beskrevet yderligere i de næste to afsnit.

## <a name="standard-billable-units"></a>Standard fakturerbar enheder

En fakturerbar standardmåleenhed kan medtage op til 10 standard job af websteder. Da en standard job af websteder kan have op til 50 job per job af websteder, kan en fakturering standardenhed et abonnement med op til 500 sager – op til næsten 22 millioner job udførelser månedsbasis.

Hvis du har mellem 1 og 10 standard job af websteder, faktureres du for 1 standardenhed fakturering. Hvis du har mellem 11 og 20 standard job af websteder, faktureres du for 2 standard fakturering enheder. Hvis du har mellem 21 og 30 standard job af websteder, du faktureres for 3 standard fakturering enheder og så videre.

## <a name="p10-premium-billable-units"></a>P10 Premium fakturerbar enheder

En P10 premium fakturerbar enhed kan medtage op til 10.000 P10 premium job samlinger. Da en P10 premium job af websteder kan have op til 50 job per job af websteder, kan en premium fakturering enhed et abonnement med op til 500.000 sager – op til næsten 22 milliarder job udførelser månedsbasis.

Hvis du har mellem 1 og 10.000 premium job af websteder, faktureres du for 1 P10 premium fakturering enhed. Hvis du har mellem 10,001 og 20.000 premium job af websteder, du faktureres for 2 P10 premium fakturering enheder og så videre.

Dermed, har de samme funktioner som standard job samlinger P10 premium job samlinger, men ikke en pris pause, i tilfælde af programmet kræver en masse job af websteder.

## <a name="p20-premium-billable-units"></a>R20 = Premium fakturerbar enheder

En r20 = premium fakturerbar enhed kan medtage op til 5.000 r20 = premium job samlinger. Da en r20 = premium job af websteder kan have op til 1.000 job per job af websteder, kan en premium fakturering enhed et abonnement med op til 5,000,000 sager – op til næsten 220 milliarder job udførelser månedsbasis.

R20 = premium job samlinger har de samme funktioner som P10 premium job samlinger, men understøtter også et større tal job per job af websteder og en større samlede antal job overordnede end P10 premium, så du kan have flere skalerbarhed.

## <a name="billing-and-active-status"></a>Fakturering og aktive Status

Jobbet samlinger er altid aktive, medmindre abonnementet hele har præsteret i nogle midlertidige deaktiveret tilstand på grund af fakturering problemer. Den eneste måde at sikre, at en sag samling ikke er faktureret angives for enten den til planen, _ledig_ eller slette samlingen job.

Selvom du kan deaktivere alle job inden for en sag af websteder i en enkelt handling, ændrer det ikke fakturering status for samlingen job – samlingen jobbet vil blive _stadig_ faktureret. På samme måde, tomme job samlinger betragtes som aktiv og vil blive faktureret.

## <a name="pricing"></a>Priser

Priser detaljer, får [Scheduler priser](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Se også


 [Hvad er Scheduler?](scheduler-intro.md)

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)
