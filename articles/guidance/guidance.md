
<properties
   pageTitle="Azure vejledning | mønstre og fremgangsmåder | Microsoft Azure"
   description="Bedste fremgangsmåder og vejledning til Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="christb"/>

# <a name="azure-guidance"></a>Azure vejledning

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsoft-mønstre og fremgangsmåder teamet er en del af Azure kunde vejledende produktteamet. Vores formålet er at hjælpe udviklere, arkitekter og IT-fagfolk gennemføres på Microsoft Azure-platformen. Vi kan udvikle vejledning, der viser bedste fremgangsmåder til opbygning af skyen løsninger på Azure.

## <a name="checklists"></a>Kontrollister

Disse lister er en hurtig reference til at gennemgå de fundamentale aspekter af tilgængelighed og skalerbarhed. 

- [Tjekliste for tilgængelighed][AvailabilityChecklist] 

    En oversigt over anbefalede fremgangsmåder for at sikre tilgængelighed.

- [Skalerbarhed tjekliste][ScalabilityChecklist]

    En oversigt over anbefalede fremgangsmåder til at designe og implementere SVG tjenester og håndtering af datastyring.

## <a name="best-practices-articles"></a>Bedste fremgangsmåder artikler

Disse artikler indeholder et uddybende oplysninger om vigtige begreber ofte tilknyttet cloud computing. 

- [API Design][APIDesign] 

    En diskussion af designsystemproblemer, du bør overveje, når du designer en web API.

- [API-implementering][APIImplementation] 

    En række anbefalede fremgangsmåder til at implementere og publicere en web API.

- [API sikkerhedsvejledning](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 

    En beskrivelse af godkendelse og autorisation problemstillinger (for eksempel token typer, godkendelse protokoller, godkendelse flyder og truslen afhjælpning).

- [Autoskalering vejledning][AutoscalingGuidance] 

    En oversigt over overvejelser i forbindelse med skalering løsninger uden brug af manuel handling.

- [Baggrund job vejledning][BackgroundJobsGuidance] 

    En beskrivelse af tilgængelige indstillinger og anbefalede fremgangsmåder til at implementere opgaver, der skal udføres i baggrunden, uafhængigt af enhver forgrunds- eller interaktive handlinger.

- [Content Delivery netværk (CDN) vejledning][CDNGuidance] 

    Generelle retningslinjer og anbefalede fremgangsmåde til at bruge CDN til at minimere belastning på dine programmer og maksimere tilgængelighed og ydeevne.

- [Cachelagring vejledning][CachingGuidance] 

    En oversigt over, hvordan du bruger cachelagring til at forbedre den ydeevne og skalerbarhed af et system.

- [Data partitionering vejledning][DataPartitioningGuidance]

    Strategier, som du kan bruge til partition data til at forbedre skalerbarhed, reducere konflikt, og optimering af ydeevne.

- [Overvågnings- og diagnosticering vejledning][MonitoringandDiagnosticsGuidance] 

    Vejledning i sporing, hvordan brugerne anvender dit system, spore ressource anvendelsen og generelt overvåge systemtilstand og systemets ydeevne.

- [Anbefalede navngivningskonventioner][naming-conventions] 

    Anbefalede navngivningskonventioner for Azure ressourcer.

- [Prøv igen generelle retningslinjer][RetryGeneralGuidance] 

    Beskrivelse af de generelle begreber for håndtering af midlertidige fejl.

- [Prøv igen tjenestens vejledning][RetryServiceSpecificGuidance]

    En oversigt over de funktioner, prøv igen for mange af Azure tjenesterne, herunder oplysninger, der kan hjælpe dig med at bruge, tilpasse eller udvide forsøg ordning for den pågældende tjeneste.

## <a name="scenario-guides"></a>Scenarie hjælpelinjer

- [Kører Elasticsearch på Azure][elasticsearch] 
    
    Elasticsearch er en meget SVG open source-søgemaskine og database. Det er egnet til situationer, der kræver hurtig analyse og registrering af oplysningerne i store datasæt. Denne vejledning ser på nogle vigtige aspekter du bør overveje, når du designer en Elasticsearch klynge.

- [Identitet administration til multiprofiler programmer][identity-multitenant] 
    
    Multitenancy er en arkitektur, hvor flere lejere dele et program, men er adskilt fra hinanden. Denne vejledning viser, hvordan du administrere bruger-id'er i en multiprofiler program, ved hjælp af [Azure Active Directory] [ AzureAD] til at håndtere logon og godkendelse.
    
- [Udvikling af dataløsninger stor](https://msdn.microsoft.com/library/dn749874.aspx)

    Denne vejledning udforsker brugen af HDInsight for scenarier som gentagen udforskning, som et datawarehouse for ETL processer og integration i eksisterende BI-systemer. Den indeholder også vejledning i at forstå begreberne big data, planlægning og design stor dataløsninger og implementere disse løsninger.
    
## <a name="patterns"></a>Mønstre

- [Skyen designs: Præskriptive arkitektur vejledning til skyen programmer](https://msdn.microsoft.com/library/dn568099.aspx)

    Skyen designs er et bibliotek med designs og relaterede vejledning emner. Den articulates fordel af at anvende mønstre ved at vise, hvordan hvert enkelt kan passe ind i skyen programmet arkitekturer.
    
- [Optimering af ydeevne til skyen programmer](https://github.com/mspnp/performance-optimization)

    Denne vejledning er en udforskning af almindelige mod mønstre, der forhindre apps fra skalering belastning. Den indeholder eksempler demonstrerer otte mod mønstre og en [ydeevne analyse grundlæggende](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) og en vejledning til [vurdering af ydeevne ved brug af vigtige mål](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Reference arkitekturer

Vores reference arkitekturer er arrangeret efter scenarie.
Hver enkelt arkitektur indeholder anbefalede fremgangsmåder og præskriptive trin og en eksekverbare komponent, der indeholder anbefalingerne.

Det aktuelle bibliotek med reference arkitekturer findes på [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Fleksibilitet vejledning

Disse emner beskriver, hvordan du designer programmer, der er tolerant for fejl i et distribueret cloud-miljø.   

- [Oversigt over fleksibilitet][ResiliencyOvervew]

     Sådan oprettes programmer på Azure-platformen, som kan gendanne fra fejl og fortsætte med at fungere. I denne artikel beskrives en struktureret fremgangsmåde for at opnå fleksibilitet fra design til implementering, installation og handlinger.

- [Fleksibilitet tjekliste][resiliency-checklist]

    En tjekliste over anbefalinger, der hjælper dig med at tage højde for en række fejl funktionsmåder, der kan opstå.

- [Manglende tilstand analyse][resiliency-fma] 

    Manglende tilstand analyse (FMA) er en proces i forbindelse med opbygning fleksibilitet i et system, ved at identificere mulige fejl punkter. Denne artikel indeholder som udgangspunkt for din FMA proces, et katalog over potentielle fejl tilstande og deres afhjælpninger. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md

