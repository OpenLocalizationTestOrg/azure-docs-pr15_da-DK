
<properties
   pageTitle="Elasticsearch på Azure instruktioner | Microsoft Azure"
   description="Elasticsearch på Azure instruktioner."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch på Azure vejledning 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch er en meget SVG open source-søgemaskine og database. Det er praktisk i situationer, der kræver hurtig analyse og registrering af oplysningerne i store datasæt. Denne vejledning ser på nogle vigtige aspekter du bør overveje, når du designer en Elasticsearch klynge, med fokus på måder at optimere og test af konfigurationen.

> [AZURE.NOTE]Denne vejledning antages det, at nogle grundlæggende kendskab til Elasticsearch. Besøg [Elasticsearch websted](https://www.elastic.co/products/elasticsearch)for at få mere detaljerede oplysninger. 

- **[Kører Elasticsearch på Azure][]** giver en kort introduktion til den generelle struktur i Elasticsearch og beskriver, hvordan du kan implementere en Elasticsearch klynge, ved hjælp af Azure. 
- **[Optimering af data indtagelse ydeevnen til Elasticsearch på Azure][]** beskriver installation af en Elasticsearch klynge og giver indgående analyse af de forskellige konfigurationsindstillinger skal overveje, når du forventer en høj hastighed for data indtagelse.
- **[Optimering af datasammenlægning og forespørgselsydelse for Elasticsearch på Azure][]** indeholder en indgående analyse af indstillinger for at overveje, når Bestem, hvordan du kan optimere dit system til forespørgslen, og Søg ydeevne.
- **[Konfiguration af spændstighed og gendannelse på Elasticsearch på Azure][]** beskrives nogle vigtige funktioner i en Elasticsearch klynge, der kan hjælpe med at minimere risikoen for datatab og udvidede data gendannelse klokkeslæt.
- **[Oprette en ydeevne testmiljø til Elasticsearch på Azure][]** beskriver, hvordan du konfigurerer et miljø til test af ydeevne data indtagelse og arbejdsbelastninger, som forespørgsel i en Elasticsearch klynge. 
- **[Implementere en JMeter teste planen for Elasticsearch][]** opsummerer, der kører performance-test, der er implementeret ved hjælp af JMeter testplaner sammen med Java-kode integreres som en JUnit test for at udføre opgaver som overførsel af data i Elasticsearch klynge.
- **[Implementere en JMeter JUnit CVS til test Elasticsearch ydeevne][]** beskriver, hvordan du opretter og bruger en JUnit CVS, som kan generere og overføre data til en Elasticsearch klynge. Dette giver en meget fleksible fremgangsmåde for at indlæse test, der kan generere store mængder testdata uden afhængigt af eksterne data-filer. 
- Sådan køres de fleksibilitet test, der bruges i denne serie opsummerer **[kørsel af automatiseret Elasticsearch fleksibilitet tester][]** . 
- Sådan køres performance-test, der bruges i denne serie opsummerer **[kørsel af automatiseret Elasticsearch ydeevne tester][]** .


[Kører Elasticsearch på Azure]: guidance-elasticsearch-running-on-azure.md
[Justering af Data indtagelse ydeevnen til Elasticsearch på Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Oprette en Performance-test miljø til Elasticsearch på Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementere en JMeter testplan for Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implementere en JMeter JUnit CVS til test Elasticsearch ydeevne]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Justering af datasammenlægning og Forespørgselsydelse for Elasticsearch på Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Konfiguration af spændstighed og gendannelse på Elasticsearch på Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Kørsel af automatiseret Elasticsearch fleksibilitet tester]: guidance-elasticsearch-running-automated-resilience-tests.md
[Køre Automatiseret Elasticsearch Performance-test]: guidance-elasticsearch-running-automated-performance-tests.md
