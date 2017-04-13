<properties
    pageTitle="Dataindsamling Azure-lager i oversigt over Log Analytics | Microsoft Azure"
    description="Azure ressourcer kan skrive logfiler og målepunkter til en Azure-lager-konto, ofte ved hjælp af Azure diagnosticering. Log Analytics kan indeksere disse data og gøre det kan søges i."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Dataindsamling Azure-lager i Log Analytics oversigt

Mange Azure ressourcer kan skrive logfiler og målepunkter til en Azure-lager-konto. Log Analytics kan bruge disse data og gøre det nemmere at overvåge dine Azure ressourcer.

Hvis du vil skrive til Azure-lager kan en ressource bruge Azure diagnosticering eller har sin egen metode til at skrive data. Disse data kan skrives i forskellige formater til en af følgende steder:

+ Azure-tabel
+ Azure blob
+ EventHub

Log Analytics understøtter Azure-tjenester, skriver data ved hjælp af [Azure diagnosticeringslogfiler](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Desuden understøtter Log Analytics andre tjenester, som output logfiler og målepunkter i forskellige formater og placeringer.  

>[AZURE.NOTE] Du betaler normal Azure data satser for lager og transaktioner, når du sender diagnosticering til en lagerplads konto og når Log Analytics læser data fra din lagerplads konto.

![Azure-lager diagram](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Understøttede Azure ressourcer

Log Analytics kan indsamle data til Azure følgende ressourcer:

| Ressourcetype | Logfiler (Diagnosticering kategorier) | Log Analytics løsning |
| --------------------------------------- | -------------------------------- | --------------- |
| Programmet indsigt | Tilgængelighed <br> Brugerdefinerede hændelser <br> Undtagelser <br> Anmodninger om <br> | Programmet indsigt (Preview) |
| Administration af API | | *ingen* (Preview) |
| Automatisering <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (Preview) |
| Vigtige samling <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (Preview) |
| Application Gateway <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (Preview) |
| Netværk sikkerhedsgruppe <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (Preview) |
| Tjenesten struktur                          | ETWEvent <br> Funktionsdygtige begivenhed <br> Pålidelige Agent begivenhed <br> Pålidelig servicebegivenhed| ServiceFabric (Preview) |
| Virtuelle maskiner | Linux Syslog <br> Windows-begivenhed <br> IIS-loggen <br> Windows ETWEvent | *ingen* |
| Web roller <br> Arbejder roller | Linux Syslog <br> Windows-begivenhed <br> IIS-loggen <br> Windows ETWEvent | *ingen* |

>[AZURE.NOTE] Vi anbefaler, at du installerer [Log Analytics VM lokalnummer](log-analytics-azure-vm-extension.md)for overvågning Azure virtuelle maskiner (både Linux og Windows). Agenten giver dig større indsigt på virtuelle maskiner end hvis du bruger diagnosticering skrevet til lager.

Du kan hjælpe os med at prioritere yderligere logfiler for OMS til at analysere ved afstemning på vores [feedback-side](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Se [analysere Azure diagnosticeringslogfiler ved hjælp af Log Analytics](log-analytics-azure-storage-json.md) til at få mere for at vide om, hvordan Log Analytics kan læse loggene fra Azure tjenester, der understøtter [Azure diagnosticeringslogge](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  - Azure vigtige samling
  - Azure-automatisering
  - Application Gateway
  - Netværk sikkerhedsgrupper
- Se [Brug blob-lager til IIS og tabellagring for begivenheder](log-analytics-azure-storage-iis-table.md) mere at vide om hvordan Log Analytics kan læse logfiler til Azure services, Skriv diagnosticering til tabellagring eller IIS-logfiler, der er skrevet til blob storage, herunder:
  - Tjenesten struktur
  - Web roller
  - Arbejder roller
  - Virtuelle maskiner


Program indsigt er i privat udskrift og det bruger fortløbende Eksportér til blob storage. Kontakt din Microsoft Account Manager for at deltage i den private preview, eller refererer til oplysningerne om [feedback websted](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Næste trin

- [Analysere Azure diagnosticeringslogfiler ved hjælp af Log Analytics](log-analytics-azure-storage-json.md) at læse loggene fra Azure tjenester, Skriv diagnosticering til blob-lager i JSON-format.
- [Brug blob-lager til IIS og tabellagring for begivenheder](log-analytics-azure-storage-iis-table.md) til at læse logfiler til Azure tjenester, Skriv diagnosticering til tabellagring eller IIS-logfiler, der er skrevet til blob storage.
- [Aktivere løsninger](log-analytics-add-solutions.md) for at give indblik i dataene.
- [Brug søgeforespørgsler](log-analytics-log-searches.md) til at analysere dataene.
