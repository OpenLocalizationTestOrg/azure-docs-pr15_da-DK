<properties 
    pageTitle="Biltype telemetri analytics løsning playbook | Microsoft Azure" 
    description="Bruge funktionerne i Cortana Intelligence til at få realtid og skønnet viden på biltype tilstand og bil vaner." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Biltype telemetri analytics løsning playbook

I denne **menu** links til kapitlerne i denne playbook. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Oversigt
Super computere er fjernet af øvelse og parkeret nu i vores garage! Disse avancerede biler indeholder mange forskellige sensorer, give dem mulighed for at spore og overvåge millioner af begivenheder i sekundet. Vi forventer, at ved 2020, de fleste af disse biler vil har er forbindelse til internettet. Forestil dig at trykke på i store mængde data til at levere bedste i klassen sikkerhed, pålideligheden og fører oplevelse! Microsoft har gjort dette elsker virkelighed ved Cortana Intelligence.

Microsofts Cortana Intelligence er en fuldt administreret big data og avancerede analytics-pakke, der gør det muligt at transformere dataene til intelligent handling. Vi vil præsentere dig for Cortana Intelligence biltype Telemetri Analytics løsning skabelon. Denne løsning viser, hvordan bil forhandlere, bil producenter og forsikringsselskaber kan bruge funktionerne i Cortana Intelligence til at få realtid og skønnet viden om biltype tilstand og bil vaner. 

Løsningen er implementeret som et [lambda arkitektur mønster](https://en.wikipedia.org/wiki/Lambda_architecture) , der viser fuldt potentielle af Cortana Intelligence platform til realtid og batchbehandling. Løsning: 

- indeholder en biltype telematik simulator
- bruger begivenhed Hubs for ingesting millioner af simuleret biltype telemetri begivenheder til Azure 
- bruger Stream Analytics til at få realtid indsigt på biltype tilstand
-  fortsætter dataene til længerevarende opbevaring for bedre batchen analyser. 
- udnytter Machine Learning til anomali registrering i realtid og batch processing for at få skønnet indsigt.
- bruger HDInsight for at transformere data skaleres og Data Factory til at håndtere organisering, planlægning, ressourcestyring og overvågning af batchbehandling rørledning 
- giver denne løsning et omfattende dashboard for realtidsdata og skønnet analytics visualiseringer ved hjælp af Power BI

## <a name="architecture"></a>Arkitektur

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figur 1 – biltype Telemetri Analytics løsningsarkitektur*

Denne løsning indeholder følgende **Cortana Intelligence-komponenter** og viser deres start til slut-integration


- **Begivenhed Hubs** for ingesting millioner af biltype telemetri begivenheder til Azure.
- **Stream analyser** for at få realtid indsigt på biltype sundhed og fortsætter disse data til længerevarende opbevaring for bedre batchen analyser.
- **Machine Learning** til anomali registrering i realtid og batchbehandling til at få skønnet indsigt.
- **HDInsight** opgraderede for at transformere data på skala
- **Data Factory** håndterer organisering, planlægning, ressourcestyring og overvågning af batchbehandling rørledning.
- **Power BI** giver denne løsning et omfattende dashboard for realtidsdata og skønnet analytics visualiseringer.

Denne løsning får adgang til to forskellige **datakilder**: 

- **Simuleret biltype signaler og diagnosticering**: biltype telematik simulator udsender diagnostiske oplysninger og signaler, der svarer til tilstanden for biltype og fører mønstret på et bestemt tidspunkt. 
- **Biltype katalog**: et reference datasæt, der indeholder en VIN til model-tilknytning.
