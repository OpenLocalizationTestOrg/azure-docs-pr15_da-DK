<properties
   pageTitle="Introduktion til Microsoft Azure log integration | Microsoft Azure"
   description="Få mere at vide om Azure log integration, dens vigtige funktioner, og hvordan det fungerer."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introduktion til Microsoft Azure log integration (Preview)

Få mere at vide om Azure log integration, dens vigtige funktioner, og hvordan det fungerer.

## <a name="overview"></a>Oversigt

Platform som en tjeneste (PaaS) og Infrastructure som en tjeneste (IaaS), der er hostet i Azure generere en stor mængde data i sikkerhedslogfiler. Disse logfiler indeholder vigtige oplysninger, der kan giver intelligence og effektive indsigt i politikker, interne og eksterne risici, problemer med overholdelse af lovgivningen og afvigelser i netværk, host og brugeraktivitet.

Azure log integration gør det muligt at integrere rå logfiler fra dine Azure ressourcer i til din lokale sikkerhedsoplysninger og begivenhed Management (SIEM). Azure log integration indsamler Azure diagnosticering fra din Windows *(WAD)* virtuelle maskiner, samt diagnosticering fra partnerløsninger som en Web Application Firewall (WAF). Denne integration giver et samlet dashboard for alle dine aktiver, lokalt eller i skyen, så du kan samle, koordinere, analysere og Giv besked om sikkerhed begivenheder.

![Azure log integration][1]

## <a name="what-logs-can-i-integrate"></a>Hvilke logfiler kan integrere?

Azure giver omfattende logføring for hver Azure-tjenesten. Disse logfiler er kategoriseret i to primære typer:

- **Kontrolelementet/management logfiler**, som giver indblik i handlingerne Azure ressourcestyring oprette, Opdater og Slet. Azure overvågningslogge er et eksempel af log af denne type.
- **Data plan logfiler**, som giver indblik i de hændelser, der er opløftet som en del af brugen af en Azure ressource. Eksempler på denne type log er Windows begivenheden System, sikkerhed og logge på programmet i en virtuel maskine.

Azure log integration understøtter i øjeblikket integration af Azure overvågningslogge, virtuelt logfiler og Azure Sikkerhedscenter beskeder.

Hvis du har spørgsmål om Azure Log Integration, bedes du sende en mail til [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Næste trin

I dette dokument, blev du introduceret til Azure log integration. Hvis du vil vide mere om Azure log integration og typerne logfiler, der understøttes, se følgende:

- [Microsoft Azure Log Integration for Azure loggene (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center for oplysninger om systemkrav og installationsanvisningerne på Azure log integration.
- [Introduktion til Azure log integration](security-azure-log-integration-get-started.md) - selvstudiet vejleder dig gennem installationen af Azure log integration og integration af logfiler fra Azure-lager, Azure overvågningslogge og Sikkerhedscenter beskeder.
- [Trinnene til konfiguration af partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – dette blogindlæg viser, hvordan du konfigurerer Azure log integration til at arbejde med partnerløsninger Splunk, HP ArcSight og IBM QRadar.
- [Azure log Integration ofte stillede spørgsmål (FAQ)](security-azure-log-integration-faq.md) - denne ofte stillede spørgsmål finder du svar på spørgsmål om Azure log integration.
- [Integration Sikkerhedscenter beskeder med Azure logge Integration](../security-center/security-center-integrating-alerts-with-log-integration.md) – dette dokument viser, hvordan du synkronisere Sikkerhedscenter påmindelser, sammen med virtuelt sikkerhedshændelser, der indsamles via Azure diagnosticerings- og Azure overvågningslogge med din log analytics eller SIEM løsning.
- [Nye funktioner til Azure diagnosticering og Azure overvågningslogge](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – dette blogindlæg introducerer dig til Azure overvågningslogge og andre funktioner, der hjælper dig med at få indsigt i handlinger for ressourcerne Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
