<properties
 pageTitle="Scheduler PowerShell-cmdletter Reference"
 description="Scheduler PowerShell-cmdletter Reference"
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

# <a name="scheduler-powershell-cmdlets-reference"></a>Scheduler PowerShell-cmdletter Reference

Den følgende tabel beskrives og links til siden reference på hver af de vigtigste cmdletter i Azure Scheduler.

Hvis du vil installere Azure PowerShell og knytte den til abonnementet Azure, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). 

Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)kan finde flere oplysninger om [Azure ressourcestyring cmdletter](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx).

|Cmdlet|Cmdlet beskrivelse|
|---|---|
[Deaktiver AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Deaktiverer en sag af websteder. 
[Aktivér AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Gør det muligt for en sag af websteder.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Får Scheduler job.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Henter job af websteder.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Henter job oversigt.
[Ny AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Opretter en HTTP-job.
[Ny AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Opretter en sag af websteder.
[Ny AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Opretter en tjeneste bus køjob.
[Ny AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Opretter et service bus emne job.
[Ny AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Opretter en lagerplads køjob. 
[Fjern AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Fjerner en Scheduler sag.  
[Fjern AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Fjerner en sag af websteder. 
[Angiv AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Ændrer et Scheduler HTTP-job.
[Angiv AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Ændrer en sag af websteder. 
[Angiv AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Ændrer en tjeneste bus køjob.  
[Angiv AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Ændrer job med en tjeneste bus emne. 
[Angiv AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Ændrer en lagerplads køjob.   

Du kan finde mere detaljerede oplysninger, kan du køre en af følgende cmdletter: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Se også


 [Hvad er Scheduler?](scheduler-intro.md)

 [Azure Scheduler begreber, terminologi og enhed hierarki](scheduler-concepts-terms.md)

 [Introduktion til brug af Scheduler i portalen Azure](scheduler-get-started-portal.md)

 [Planer og fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler REST-API-reference](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler høj tilgængelighed og pålidelighed](scheduler-high-availability-reliability.md)

 [Azure Scheduler begrænsninger, standarder og fejlkoder](scheduler-limits-defaults-errors.md)

 [Azure Scheduler udgående godkendelse](scheduler-outbound-authentication.md)
