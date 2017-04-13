<properties
    pageTitle="Ledningsomrids på dataløsning i Log Analytics | Microsoft Azure"
    description="Tråd data er konsoliderede netværk og ydeevne data fra computere med OMS supportmedarbejdere, herunder Operations Manager og Windows-forbindelse supportmedarbejdere. Netværksdata kombineres med dataene log kan hjælpe dig med at koordinere data."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="wire-data-solution-in-log-analytics"></a>Ledningsomrids på dataløsning i Log Analytics

Tråd data er konsoliderede netværk og ydeevne data fra computere med OMS supportmedarbejdere, herunder Operations Manager og Windows-forbindelse supportmedarbejdere. Netværksdata kombineres med dataene log kan hjælpe dig med at koordinere data. OMS supportmedarbejdere, der er installeret på computere i din IT-infrastruktur skærm netværksdata sendes til og fra disse computere til netværk niveauer 2-3 i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model) , herunder de forskellige protokoller og porte, der bruges.

>[AZURE.NOTE] Løsningen tråd Data er ikke tilgængelig i øjeblikket der skal føjes til arbejdsområder. Kunder, der allerede har aktiveret tråd Data løsningen kan fortsætte med at bruge løsningen tråd Data.

Som standard indsamler OMS logget data for CPU, hukommelse, disk og netværk ydelsesdata fra tællere, der er indbygget i Windows. Netværks- og andre dataindsamling er gjort realtid for hver agent, herunder undernet og programmet niveau protokoller bruges af computeren. Du kan tilføje andre tællere i ydeevne på siden Indstillinger under fanen logføring.

Hvis du har brugt [sFlow](http://www.sflow.org/) eller anden software med [Cisco NetFlow protokol](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), bliver statistik og data, får du vist fra tråd data bekendt for dig.

Nogle af typerne indbyggede Log søgeforespørgsler omfatter:

- Supportmedarbejdere, der leverer data tråd
- IP-adressen på supportmedarbejdere levere tråd data
- Udgående kommunikation efter IP-adresser
- Antallet af byte sendt af programmet protokoller
- Antallet af byte, der er sendt af en programtjenester
- Byte modtaget af forskellige protokoller
- Samlet antal byte, sendes og modtages af IP-adresse
- IP-adresser, der har har kommunikeret med supportmedarbejdere på 10.0.0.0/8 undernet
- Gennemsnitlig forsinkelse for forbindelser, der blev målt pålideligt
- Computer processer, som startede eller modtaget netværkstrafik
- Mængde netværkstrafik for en proces

Når du søger ved hjælp af tråd data, kan du filtrere og gruppere data til at få vist oplysninger om de øverste supportmedarbejdere og øverste protokoller. Eller du kan undersøge hvornår bestemte computere (IP-adresser/MAC-adresser) har kommunikeret med hinanden, hvor lang tid, og hvor meget data blev sendt – grundlæggende, du får vist metadata om netværkstrafik, som er søgebaserede.

Da du får vist metadata, er det dog ikke behøver at være nyttige til videre med fejlfinding. Tråd data i OMS er ikke en fuld registrering netværksdata. Så er det ikke beregnet til fejlfinding i forbindelse med dybe pakke niveau.
Fordelen ved at bruge agent, sammenlignet med andre metoder af websteder, er, at du ikke behøver at installere udstyr, omkonfigurere dit netværk parametre eller udføres komplicerede konfigurationer. Tråd data er blot agent-baserede – du har installeret agenten på en computer, og den vil overvåge sin egen netværkstrafik. En anden fordel er, når du vil overvåge arbejdsbelastninger, som kører i skyen udbydere eller -tjenesteudbyder eller Microsoft Azure, hvor brugeren ikke ejer strukturen lag.

Du behøver derimod fuldført synligheden af hvad der sker på netværket, hvis du ikke installerer supportmedarbejdere på alle computere i din netværksinfrastruktur.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Løsningen tråd Data får data fra computere, der kører Windows Server 2012 R2, Windows 8.1 og nyere operativsystemer.
- Microsoft .NET Framework 4.0 eller nyere er påkrævet på computere, hvor du kan få fat på tråd data fra.
- Føje løsningen tråd Data til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.
- Hvis du vil have vist tråd data til en bestemt løsning, skal du have den løsning, der allerede er føjet til arbejdsområdet OMS.

## <a name="wire-data-data-collection-details"></a>Ledningsomrids Data Datadetaljer af websteder

Tråd data indsamler metadata om netværkstrafik ved hjælp af supportmedarbejdere, som du har aktiveret.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for tråd Data.


| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 eller nyere)|![Ja](./media/log-analytics-wire-data/oms-bullet-green.png)|![Ja](./media/log-analytics-wire-data/oms-bullet-green.png)|![Nej](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![Nej](./media/log-analytics-wire-data/oms-bullet-red.png)|![Nej](./media/log-analytics-wire-data/oms-bullet-red.png)| hver 1 minut|


## <a name="combining-wire-data-with-other-solution-data"></a>Kombinere tråd data med andre løsning data

Data, der returneres fra de indbyggede forespørgsler ovenstående være interessant alene. Dog realiseres mailfunktionen mere nyttig tråd data ved at kombinere med oplysninger fra andre OMS løsninger. For eksempel kan du bruge sikkerhed begivenhed data indsamles af løsningen sikkerhed og overvågning og kombinere med tråd data til at søge efter usædvanlige netværk logonforsøg til navngivne processer.  I dette eksempel vil du bruge i og DISTINCT operatorer til at deltage i datapunkter i søgestrengen.

Krav: Hvis du vil bruge i følgende eksempel, skal du have sikkerhed og Overvåg løsningen installeret. Du kan dog bruge data fra andre løsninger til at kombinere tråd data til at opnå tilsvarende resultater.

### <a name="to-combine-wire-data-with-security-events"></a>Til at kombinere tråd data med sikkerhed begivenheder

1. Klik på feltet **WireData** på siden Oversigt.
2. Klik på **Mængden af netværkstrafik (i byte) af proces** for at få vist listen over returnerede processer på listen over **Almindelige WireData forespørgsler**.
    ![wiredata forespørgsler](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Hvis listen over processer er for lang få et hurtigt overblik, kan du ændre søgeforespørgsel så den ligner:

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Vises i eksemplet nedenfor er en proces med navnet DancingPigs.exe, som kan forekomme mistænkelige.
    ![wiredata søgeresultater](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. Brug af data, der returneres på listen, skal du klikke på en navngivet proces. I dette eksempel, der blev klikket på DancingPigs.exe. De resultater, der er vist nedenfor beskrives typer netværkstrafik som udgående kommunikation over forskellige protokoller.
    ![wiredata resultaterne viser en navngivet proces](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Sikkerhed og Overvåg løsningen er installeret, kan du sende forespørgsler til de sikkerhedshændelser, der har samme procesnavn feltets værdi ved at ændre din søgning ved hjælp af i og DISTINCT Søg forespørgsel operatorerne. Du kan gøre det derefter, når både dataene ledere og andre løsning hændelseslogge har værdier i det samme format. Ændre din søgning, så den ligner:

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata resultater viser kombinerede data](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Du ser, kontooplysninger er vist i ovenstående resultaterne. Nu kan du begrænse søgningen til at finde ud af, hvor ofte den konto, der viser sikkerhed og Overvåg data blev brugt af processen med en forespørgsel, der ligner:        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![wiredata resultaterne viser firmadata](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede tråd dataposter søgning.
- Se dans [ved hjælp af tråd Data i handlinger Management pakke Log Søg blog skrive](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) har yderligere oplysninger om, hvor ofte dataene der indsamles, og hvordan du kan ændre egenskaber for samling til Operations Manager supportmedarbejdere.
