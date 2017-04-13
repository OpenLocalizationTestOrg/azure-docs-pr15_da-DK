<properties 
    pageTitle="Overvåge afhængigheder, undtagelser og udførelse af klokkeslæt i Java webapps" 
    description="Udvidet overvågning af webstedet Java med programmet indsigt" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Overvåge afhængigheder, undtagelser og udførelse af klokkeslæt i Java webapps

*Programmet indsigt er i Vis udskrift.*

Hvis du har [udstyret din Java online med programmet indsigt][java], du kan bruge Java Agent til at få større indsigt, uden kodeændringer:


* **Afhængigheder:** Data om opkald, der gør dit program til andre komponenter, herunder:
 * **RESTEN opkald** foretages via HttpClient, OkHttp og RestTemplate (fjeder).
 * **Redis** via klienten Jedis foretagede opkald. Hvis opkaldet tager længere tid end 10 'er, henter agenten også argumenterne opkald.
 * **[JDBC opkald](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB eller Apache Derby DB. "executeBatch" kald understøttes. Hvis opkaldet tager længere tid end 10 'er, rapporterer på agent MySQL og PostgreSQL ved forespørgselsplanen. 
* **Taget undtagelser:** Data om undtagelser, der skal håndteres af din kode.
* **Metode kørselstid:** Data om den tid det tager for at udføre bestemte metoder.

Hvis du vil bruge Java agent, skal installere du det på din server. Din Onlines skal være udstyret med [Programmet indsigt Java SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Installere programmet indsigt agent til Java

1. Køre din Java-server, [hente agenten](https://aka.ms/aijavasdk)på maskinen.
2. Redigere scriptet til programmet server Start, og Tilføj følgende JVM:

    `javaagent:`*hele stien til filen agent glas*

    For eksempel i Tomcat på en Linux maskine:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Genstart din application server.

## <a name="configure-the-agent"></a>Konfigurere tjenesten agent

Oprette en fil med navnet `AI-Agent.xml` og placere det i den samme mappe som agent glas filen.

Angiv indholdet af XML-filen. Redigere i følgende eksempel for at medtage eller udelade funktionerne, du vil. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Du skal aktivere rapporter undtagelse og metode tidsindstilling for individuelle metoder.

Som standard `reportExecutionTime` er sandt og `reportCaughtExceptions` er falsk.

## <a name="view-the-data"></a>Få vist dataene

I programmet indsigt ressourcen, aggregeret remote, afhængighed og metode udførelse af-klokkeslæt vises [under feltet ydeevne][metrics]. 

For at søge efter individuelle forekomster af afhængighed, undtagelse og metode rapporter, skal du åbne [søgning i][diagnostic]. 

[Diagnosticere afhængighed problemer – få mere at vide](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Har du spørgsmål? Problemer med?

* Ingen data? [Angive undtagelser](app-insights-ip-addresses.md)
* [Fejlfinding i forbindelse med Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
