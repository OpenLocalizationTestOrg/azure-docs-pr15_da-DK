<properties
 pageTitle="Forstå og løse fejl i WebHCat på HDInsight"
 description="Lær, hvordan til om almindelige fejl returneres af WebHCat på HDInsight og hvordan du løser dem."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Forstå og løse fejl, der er modtaget fra WebHCat (Templeton) på HDInsight

Når du bruger WebHCat (tidligere kendt som Templeton,) til at arbejde med HDInsight, modtager du muligvis fejl. Dette dokument giver vejledning i almindelige fejl – hvorfor de opstår, og hvad du kan gøre for at løse problemet.

##<a name="what-is-webhcat"></a>Hvad er WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) er en REST-API til [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), en tabel og lager administration af lag til Hadoop. WebHCat er aktiveret som standard HDInsight klynger og bruges af forskellige værktøjer til at sende job, skal du få jobstatus osv uden at logge på til klyngen.

##<a name="modifying-configuration"></a>Foretage ændringer i konfigurationen

> [AZURE.IMPORTANT] Flere af de fejl, der er angivet i dette dokument opstå, fordi en konfigurerede maksimum er overskredet. Når trinnet opløsning omtaler, du kan ændre en værdi, skal du bruge en af følgende til at udføre ændringen:

* For **Windows** klynger: bruge handlingen script til at konfigurere værdien under oprettelse af klynge. Du kan finde yderligere oplysninger finder [udvikling scripthandlinger](hdinsight-hadoop-script-actions.md).

* Til **Linux** klynger: Brug Ambari (web eller REST-API) for at ændre værdien. Få mere at vide under [administrere HDInsight brug af Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Standardkonfiguration

Følgende er konfiguration standardværdier, der kan påvirke WebHCat ydeevnen eller forårsage fejl, hvis disse værdier er overskredet:

| Indstilling | Hvad gør den | Standardværdi |
| ------- | ------------ | ------------- |
| [yarn.Scheduler.Capacity.Maximum-programmer][maximum-applications] | Det maksimale antal job, der kan være aktiv samtidigt (ventende eller kører) | 10.000 |
| [templeton.Exec.Max-procs][max-procs] | Det maksimale antal anmodninger, der kan være fungeret samtidigt | 20 |
| [mapreduce.jobhistory.Max-alder-ms][max-age-ms] | Antallet af dage, der skal bevares, jobs | 7 dage |

##<a name="too-many-requests"></a>For mange anmodninger

**Http-statuskode**: 429

| Årsag | Opløsning |
| ----- | ---------- |
| Du har overskredet de maksimale samtidige anmodninger served ved WebHCat i minuttet (standard 20) | Reducere arbejdsbelastningen for at sikre, at du ikke sender mere end det maksimale antal samtidige anmodninger eller øge grænsen for samtidige anmodning ved at ændre `templeton.exec.max-procs`. Få flere oplysninger om [konfiguration af ændring](#modifying-configuration) |

##<a name="server-unavailable"></a>Serveren ikke tilgængelig

**Http-statuskode**: 503

| Årsag | Opløsning |
| ---------------- | ------------------- |
| Det sker som regel under failover mellem den primære og sekundære HeadNode for-klyngen | Vent to minutter, og derefter prøve igen |

##<a name="bad-request-content-could-not-find-job"></a>Forkert anmodning indhold: der blev ikke fundet job

**Http-statuskode**: 400

| Årsag | Opløsning |
| ---------------- | ------------------- |
| Oplysninger om job har er ryddet op ved jobs rensekassette | Opbevaringsperiode standard for en oversigt over er 7 dage. Dette kan ændres ved at ændre `mapreduce.jobhistory.max-age-ms`. Få flere oplysninger om [konfiguration af ændring](#modifying-configuration) |
| Jobbet er blevet nedlagt på grund af en failover | Prøv igen jobbet i op til to minutter |
| Blev brugt en ugyldig job-id | Kontrollér, om job-id vises korrekt |

##<a name="bad-gateway"></a>Ugyldig gateway

**Http-statuskode**: 502

| Årsag | Opløsning |
| ---------------- | ------------------- |
| Interne Spildopsamling bliver til virkelighed i processen WebHCat | Vent Spildopsamling færdig eller genstarte tjenesten WebHCat |
| Timeout, venter på svar fra tjenesten ressourcestyring. Dette kan ske, når antallet af aktive programmer går det konfigurerede maksimum (standard 10.000) | Vent på i øjeblikket er aktive job for at fuldføre eller øge grænsen for samtidige job ved at ændre `yarn.scheduler.capacity.maximum-applications`. Få flere oplysninger om [konfiguration af ændring](#modifying-configuration)  |
| Når du forsøger at hente alle job gennem opkaldet [få /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) under `Fields` er indstillet til`*` | Ikke hente oplysninger om *alle* job, skal du i stedet bruge `jobid` til at hente oplysninger om job kun større end visse job-id. Eller brug ikke`Fields` |
| Tjenesten WebHCat er ned under HeadNode failover | Vent, til to minutter og prøve igen |
| Der er mere end 500 ventende job sendt via WebHCat | Vent, indtil aktuelt ventende job har afsluttet før afsendelse af flere job |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
