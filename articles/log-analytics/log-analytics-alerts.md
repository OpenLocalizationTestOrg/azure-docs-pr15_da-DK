<properties 
   pageTitle="Beskeder i Log Analytics | Microsoft Azure"
   description="Beskeder i Log Analytics kan identificere vigtige oplysninger i din OMS lager og proaktiv give dig besked om problemer eller kalde handlinger for at forsøge at rette dem.  I denne artikel beskrives, hvordan du opretter en besked om og oplysninger om de forskellige handlinger, som de kan tage."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Beskeder i Log Analytics

Beskeder i Log Analytics identificere vigtige oplysninger i din OMS-lager.  Regler for påmindelser automatisk Kør log søger efter en tidsplan og oprette en besked om post, hvis resultaterne opfylder bestemte kriterier.  Reglen kan derefter køre en eller flere handlinger for at give dig besked om beskeden eller kalde en anden proces proaktiv automatisk.   

![Log Analytics-beskeder](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Oprette en besked om
Hvis du vil oprette en besked om, starter du ved at oprette en logfil søgning efter de poster, der skal aktivere beskeden.  Knappen **besked** vil derefter være tilgængelige, så du kan oprette og konfigurere reglen.

1.  Klik på **Log Søg**fra siden OMS Overview.
2.  Enten Opret en ny log søgeforespørgsel, eller Vælg en gemt logfil søgning. 
3.  Klik på **beskeder om** øverst på siden for at åbne skærmbilledet **Tilføj besked regel** .
4. Referere til tabeller under få at vide om indstillinger for at konfigurere beskeden.
5. Når du angiver en tidsramme for reglen, vises antallet af eksisterende poster, der opfylder søgekriterierne for tidsramme.  Dette kan hjælpe dig med at bestemme den hyppighed, som kan give dig antallet resultater, som du forventer.
4.  Klik på **Gem** for at fuldføre reglen.  Det vil begynde at køre med det samme.


![Tilføje reglen](media/log-analytics-alerts/add-alert-rule.png)

| Egenskaben | Beskrivelse |
|:--|:--|
| **Oplysningerne i påmindelser** | |
| Navn |  Entydigt navn til at identificere reglen. |
| Alvor | Alvorlighed på den besked, der er oprettet af denne regel. |
| Søgeforespørgsel | Vælg **Brug aktuelle forespørgsel** til at bruge den aktuelle forespørgsel eller vælge en eksisterende søgning på listen.  Forespørgselssyntaksen er angivet i tekstfeltet, hvor du kan ændre den Hvis det er nødvendigt.  |
| Tidsramme | Angiver tidsintervallet til forespørgslen.  Forespørgslen returnerer kun de poster, der er oprettet i dette område af det aktuelle klokkeslæt.  Det kan være en hvilken som helst værdi mellem 5 minutter og 24 timer.  Det skal være større end eller lig med den hyppighed af beskeder.  <br><br> Eksempelvis hvis en tidsramme er indstillet til 60 minutter, og forespørgslen køres på 1:15 PM, returneres kun de poster, der er oprettet mellem 12:15 PM og 1:15 PM. |
| **Tidsplan** |     
| Grænseværdi | Kriterier at oprette en besked.  Hvis antallet af poster returneres i forespørgslen stemmer overens med dette kriterium, oprettes der en besked. |
| Hyppighed af beskeder | Angiver, hvor ofte du skal køre forespørgslen.  Kan være en hvilken som helst værdi mellem 5 minutter og 24 timer.  Skal være lig med eller mindre end en tidsramme. |
| Skjul beskeder | Når du slår bekæmpelse for reglen, er handlinger for reglen deaktiveret for en defineret længde af tid, når du har oprettet en ny besked.  Reglen stadig er aktiv og opretter beskeder om poster, hvis kriterierne er opfyldt.  Dette er at tillade, at du har tid til at løse problemet uden at køre identiske handlinger. |
| **Handlinger** | |
| Mailmeddelelse | Angiv **Ja** , hvis du vil have en mail skal sendes, når påmindelsen udløses. |
| Emne    | Emne i mail.  Du kan ikke ændre brødteksten i e-mailen. |
| Modtagere | Adresserne på alle e-mail-modtagere.  Hvis du angiver mere end én adresse, derefter adskil adresserne med et semikolon (;). |
| Webhook | Angiv **Ja** , hvis du vil ringe en webhook, når påmindelsen udløses. |
| Webhook URL-adresse | URL-adressen til webhook. |
| Omfatte brugerdefinerede JSON-data | Vælg denne indstilling, hvis du vil erstatte standard data med en brugerdefineret data. |
| Angiv din brugerdefinerede JSON-data | De brugerdefinerede data til webhook.  Se forrige afsnit få mere at vide. |
| Runbook | Angiv **Ja** , hvis du vil starte en Azure automatisering runbook, når påmindelsen udløses. |
| Vælg en runbook | Vælg runbook til at starte fra runbooks i den automatiske konto konfigureret i din løsning med Automation. |
| Køre på | Vælg **Azure** til at køre runbook i Azure skyen.  Vælg **Hybrid arbejder** til at køre runbook af en [Hybrid Runbook arbejder](..\automation\automation-hybrid-runbook-worker.md) i dit lokale miljø. |


## <a name="manage-alert-rules"></a>Administrere regler for påmindelser
Du kan få en liste over alle regler for påmindelser i menuen **beskeder** i Log Analytics- **Indstillinger**.  

![Administrer beskeder](./media/log-analytics-alerts/configure.png)

1. Vælg feltet **Indstillinger** i konsollen OMS.
2. Vælg **beskeder**.

Du kan udføre flere handlinger i denne visning.

- Deaktivere en regel ved at vælge **fra** ud for den.
- Redigere en besked om ved at klikke på blyantsikonet ud for den.
- Fjerne en besked om ved at klikke på ikonet **X** ud for den. 


## <a name="setting-time-windows"></a>Angive tid windows 

### <a name="event-alerts"></a>Begivenhed beskeder

Hændelser, der omfatter datakilder som Windows-hændelseslogge, Syslog, og brugerdefineret logfiler.  Du vil oprette en besked, når en bestemt fejl begivenhed oprettes, eller når flere fejl hændelser er oprettet i en bestemt tidsramme.

Angive antallet resultater for at minde på en enkelt hændelse, der er større end 0 og både frekvens og tidsramme til fem minutter.  Der kører forespørgslen hver 5 minutter og kontrollere, om forekomsten af en enkelt hændelse, der blev oprettet siden du sidst blev kører forespørgslen.  En længere frekvens kan forsinkes tiden mellem den begivenhed, der indsamles, og den vigtige besked, der oprettes.

Nogle programmer kan logge en lejlighedsvise fejl, der ikke bør nødvendigvis hæve en besked.  Programmet kan for eksempel prøv igen den proces, der oprettede hændelsen fejl og derefter lykkes næste gang.  I dette tilfælde kan du ikke vil oprette beskeden, medmindre flere hændelser er oprettet i en bestemt tidsramme.  

I nogle tilfælde kan du vil oprette en besked i fravær for en begivenhed.  En proces kan for eksempel log regelmæssige arrangementer for at angive, at den fungerer korrekt.  Hvis det ikke logger en af disse hændelser inden for en bestemt tidsramme, skal derefter en påmindelse oprettes.  I dette tilfælde skal du angive grænseværdi for *mindre end*1.

### <a name="performance-alerts"></a>Ydeevnen beskeder

[Ydelsesdata](log-analytics-data-sources-performance-counters.md) er gemt som poster i den samme begivenheder OMS lager.  Værdien i hver post er gennemsnittet målt over de forrige 30 minutter.  Hvis du vil give besked, når en ydeevne tæller overstiger en bestemt grænse, skal grænseværdi skal medtages i forespørgslen.

Hvis du vil have besked, når processoren kører eksempelvis over 90% til 30 minutter, skal du bruge en forespørgsel som *Type = performance ObjectName = Processor CounterName = "% Processortid" CounterValue > 90* og grænsen for reglen skal *større end 0*.  

 Da [ydeevne poster](log-analytics-data-sources-performance-counters.md) samles hver 30 minutter uanset den hyppighed, at du har indsamlet hver tæller, kan en tidsramme, der er mindre end 30 minutter returnere nogen poster.  Indstille en tidsramme til 30 minutter sikrer, at du får en enkelt post for hver forbundne kilde, der repræsenterer gennemsnitlige over periode.

## <a name="alert-actions"></a>Handlinger for beskeder

Ud over at oprette en besked om posten, kan du konfigurere reglen til automatisk at køre en eller flere handlinger.  Handlinger kan proaktiv give dig besked om den vigtige besked eller kalde en proces, der forsøger at løse det problem, der blev registreret.  I følgende afsnit beskrives de handlinger, der er tilgængelige.

### <a name="email-actions"></a>E-mail-handlinger
Mail handlinger sende en e-mail med oplysninger om den vigtige besked til en eller flere modtagere.  Du kan angive emnet for e-mailen, men det er indhold er et standardformat opbygget ved Log analyser.  Den indeholder oversigtsoplysningerne som navnet på beskeden ud over oplysninger om op til ti poster, der returneres af log søgningen.  Den indeholder også et link til en log søgning i Log Analytics, som returnerer på hele sættet af poster fra denne forespørgsel.   Afsenderen af e-mailen er *Microsoft handlinger pakke ledelsen &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Webhook handlinger

Webhook handlinger gør det muligt at kalde en ekstern proces gennem en enkelt HTTP POST-anmodning.  Tjenesten bliver kaldt skal understøtter webhooks og finde ud af, hvordan der skal bruges en hvilken som helst data den modtager.  Du kan også ringe til en REST-API, som ikke specifikt understøtter webhooks, så længe anmodningen er i et format, som API forstår.  Eksempler på brug af en webhook som svar på en besked bruger en tjeneste som [slæk i forhold](http://slack.com) til at sende en meddelelse med oplysninger om den vigtige besked eller oprette en hændelse i en tjeneste som [PagerDuty](http://pagerduty.com/).  

En komplet gennemgang ved at oprette en besked om med en webhook til at ringe til en eksempel-tjeneste er tilgængelig på [Webhooks i Log Analytics-beskeder](log-analytics-alerts-webhooks.md).

Webhooks omfatter en URL-adresse og en formateret med JSON, der er data, der sendes til tjenesten eksterne data.  Som standard indeholder data værdierne i den følgende tabel.  Du kan vælge at erstatte denne data med et brugerdefineret kort af dine egne.  Du kan i så fald bruge variabler i tabellen for hver af parametrene til at medtage deres værdi i din brugerdefinerede data.


| Parameter | Variabel | Beskrivelse |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Navnet på reglen. |
| AlertThresholdOperator | #thresholdoperator | Grænseværdi for operator for reglen.  *Større end* eller *mindre end*. |
| AlertThresholdValue | #thresholdvalue | Grænseværdi for reglen. |
| LinkToSearchResults | #linktosearchresults | Link til Log Analytics log søgning, der returnerer posterne fra den forespørgsel, der oprettet beskeden. |
| ResultCount  | #searchresultcount | Antallet af poster i søgeresultaterne. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Sluttidspunkt for forespørgslen i UTC-format. |
| SearchIntervalInSeconds | #searchinterval | Tidsramme for reglen. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Starttidspunktet for forespørgslen i UTC-format. |
| SearchQuery | #searchquery | Log søgeforespørgsel bruges af reglen. |
| Søgeresultater | Se under | Poster returneres i forespørgslen i JSON-format.  Begrænset til det første 5.000 poster. |
| WorkspaceID | #workspaceid | Arbejdsområdet OMS ID. |


For eksempel kan du angive følgende brugerdefinerede data, der indeholder en enkelt parameter kaldet *tekst*.  Den tjeneste, der ringer op til denne webhook forventede vil denne parameter.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Dette eksempel data vil løse til noget i retning af følgende, når sendes til webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Hvis du vil medtage søgeresultater i en brugerdefineret data, skal du tilføje følgende linje som en egenskab for øverste niveau i json overførslen.  

    "IncludeSearchResults":true

Du kan for eksempel bruge følgende for at oprette en brugerdefineret data, der indeholder lige beskeder om navn og søgeresultaterne. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Du kan gennemgå en komplet eksempel på oprettelse af en besked om med en webhook til at starte en ekstern tjeneste på [Log analyser Giv besked webhook eksempel](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Runbook handlinger

Runbook handlinger starte en runbook i Azure automatisering.  Hvis du vil bruge denne type handling, skal du have [automatisering løsning](log-analytics-add-solutions.md) installeret og konfigureret i arbejdsområdet OMS.  Hvis du ikke har installeret, når du opretter en ny regel for beskeder om den, vises et link til dens Installer.  Du kan vælge mellem runbooks i den automatiske konto, du konfigurerede i løsningen automatisering.

Runbook handlinger start runbook ved hjælp af en [webhook](../automation/automation-webhooks.md).  Når du opretter reglen, vil den automatisk oprette en ny webhook for runbook med navnet på **OMS besked afhjælpning** efterfulgt af et GUID.  

Du kan ikke direkte udfyldes parametre af runbook, men [$WebhookData parameter](../automation/automation-webhooks.md) kan indeholde oplysninger om beskeden, herunder resultaterne af log søgningen, den blev oprettet.  Runbook skal definere **$WebhookData** som en parameter at få adgang til egenskaberne for påmindelsen.  Beskeder om dataene er tilgængelig i json-formatet i en enkelt egenskab kaldet **Søgeresultater** i egenskaben **RequestBody** for **$WebhookData**.  Dette får med egenskaberne i den følgende tabel.


| Node | Beskrivelse |
|:--|:--|
| id         | Stien og GUID af feltet Søg. |
| __metadata | Oplysninger om den advarsel, herunder antallet af poster og status for søgeresultaterne. |
|  værdi     |  Separat post for hver post i søgeresultaterne.  Detaljerne for posten matcher de egenskaber og værdier i posten.   |

For eksempel vil følgende runbook udtrække de poster, der returneres af log søgningen og tildele forskellige egenskaber, der er baseret på typen af hver post.  Bemærk, at runbook starter ved at konvertere **RequestBody** fra json, så den kan arbejde med som et objekt i PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Beskeder om poster

Beskeder om poster, der blev oprettet af regler for påmindelser i Log Analytics har en **Type** **besked** og en **SourceSystem** af **OMS**.  De har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Type          | *Giv besked* |
| SourceSystem  | *OMS* |
| AlertSeverity | Prioritering af påmindelsen. |
| AlertName     | Navnet på beskeden. |
| Forespørgsel         | Teksten i den forespørgsel, der blev kørt.  |
| QueryExecutionEndTime   | Slutningen af tidsintervallet til forespørgslen. |
| QueryExecutionStartTime | Starten af tidsintervallet til forespørgslen.  |
| TimeGenerated | Dato og klokkeslæt for påmindelsen. |

Der findes andre typer af beskeder om poster, der blev oprettet ved [besked Management-løsning](log-analytics-solution-alert-management.md) og [Power BI eksporterer](log-analytics-powerbi.md).  Disse alle skal have en **Type** af **beskeder** , men der skelnes mellem deres **SourceSystem**.




## <a name="next-steps"></a>Næste trin

- Installere [beskeder om Management-løsning](log-analytics-solution-alert-management.md) for at analysere beskeder, der er oprettet i Log Analytics sammen med beskeder, der indsamles fra System Center Operations Manager (SCOM).
- Få mere at vide om [log søgninger](log-analytics-log-searches.md) , kan generere beskeder.
- Udføre en gennemgang for [konfiguration af en webook](log-analytics-alerts-webhooks.md) med en besked om.  
- Lær, hvordan du kan skrive [runbooks i Azure Automation](https://azure.microsoft.com/documentation/services/automation) for at afhjælpning problemer, der er identificeret med beskeder.