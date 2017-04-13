<properties
 pageTitle="Scheduler grænser og -standarder"
 description="Scheduler grænser og -standarder"
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

# <a name="scheduler-limits-and-defaults"></a>Scheduler grænser og -standarder

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Scheduler kvoter, begrænsninger, standarder og Throttles

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Overskriften x-ms-anmodning-id

Alle anmodninger mod tjenesten Opgavestyring returnerer et svar sidehoved med navnet**x-ms-anmodning-id**. Denne overskrift indeholder en uigennemsigtig værdi, der entydigt identificerer anmodningen.

Hvis en anmodning om konsekvent går ned, og du har bekræftet, at din anmodning er udarbejdet korrekt, kan du bruge denne værdi til at rapportere fejlen til Microsoft. I rapporten, kan nævnes værdien af x-ms-anmodning-id, det anslåede tidspunkt, der blev foretaget anmodningen, id'et for abonnementet, job af websteder, og/eller job og typen handling, som har forsøgt anmodningen.

## <a name="see-also"></a>Se også


 [Hvad er Scheduler?](scheduler-intro.md)

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell-cmdletter reference](scheduler-powershell-reference.md)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)
