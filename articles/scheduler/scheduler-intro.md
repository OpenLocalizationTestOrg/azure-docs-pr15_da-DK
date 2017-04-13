<properties
 pageTitle="Hvad er Azure Scheduler? | Microsoft Azure"
 description="Azure Scheduler kan du med en erklæring beskriver handlinger til at køre i skyen. Det og derefter planlægges og køres automatisk i disse handlinger."
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
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>Hvad er Azure Scheduler?

Azure Scheduler kan du med en erklæring beskriver handlinger til at køre i skyen. Det og derefter planlægges og køres automatisk i disse handlinger.  Scheduler gør dette ved hjælp af [portalen Azure](scheduler-get-started-portal.md), kode, [REST-API](https://msdn.microsoft.com/library/mt629143.aspx)eller Azure PowerShell.

Scheduler opretter, fører og aktiverer planlagte arbejde.  Scheduler ikke vært for en hvilken som helst arbejdsbelastninger eller kører en hvilken som helst kode. IT kun _aktiverer_ kode hostet et andet sted – i Azure, lokalt, eller med en anden udbyder. Det aktiverer via HTTP, HTTPS, en lagerplads kø, en tjeneste bus kø eller en tjeneste bus emne.

Scheduler planlægges [job](scheduler-concepts-terms.md), gemmer en oversigt over job udførelse af resultaterne, en kan gennemse, og deterministically og sikkert planlægges arbejdsbelastninger, som skal køres. Azure WebJobs (en del af funktionen Web Apps i Azure App Service) og andre Azure funktionerne til planlægning kan du bruge Scheduler i baggrunden. [Scheduler REST-API](https://msdn.microsoft.com/library/mt629143.aspx) hjælper med at administrere kommunikation til disse handlinger. Som sådan understøtter Scheduler [komplekse tidsplaner og avancerede gentagelse](scheduler-advanced-complexity.md) nemt.

Der findes flere scenarier, som egner sig til brugen af Scheduler. Eksempel:

+ _Tilbagevendende programmet handlinger:_ Med jævne mellemrum indsamling af data fra Twitter i et feed.
+ _Daglig vedligeholdelse:_ Daglig fjernelse af logfiler, sikkerhedskopiering og andre opgaver til vedligeholdelse. For eksempel kan en administrator vælge at sikkerhedskopiere databasen klokken 1:00 hver dag for de næste ni måneder.

Scheduler kan du oprette, opdatere, slette, få vist og administrere job og [job af websteder](scheduler-concepts-terms.md) fra et program, ved hjælp af scripts og på portalen.

## <a name="see-also"></a>Se også

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Sådan oprettes komplekse tidsplaner og avancerede gentagelse med Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)
