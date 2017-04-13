<properties
   pageTitle="Giv besked Management-løsning i handlinger Management pakke (OMS) | Microsoft Azure"
   description="Beskeder om Management-løsning i Log Analytics hjælper dig med at analysere alle beskeder i dit miljø.  Udover at konsolidere påmindelser, der oprettes i OMS, importerer den beskeder fra forbundne System Center Operations Manager (SCOM) administration af grupper i Log analyser."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2016"
   ms.author="bwren" />

# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Beskeder om Management-løsning i handlinger Management pakke (OMS)

![Administration af advarselsikon](media/log-analytics-solution-alert-management/icon.png) Beskeder om Management-løsning hjælper dig med at analysere alle beskeder i dit miljø.  Udover at konsolidere påmindelser, der oprettes i OMS, importerer den beskeder fra forbundne System Center Operations Manager (SCOM) administration af grupper i Log analyser.  I miljøer med flere administration af grupper give besked Management-løsning en samlet visning af beskeder på tværs af alle administration af grupper.

## <a name="prerequisites"></a>Forudsætninger

- For at importere beskeder fra SCOM, kræver denne løsning en forbindelse mellem arbejdsområdet OMS og en SCOM management-gruppe ved hjælp af processen beskrevet i [Forbinde Operations Manager til Log analyser](log-analytics-om-agents.md).  


## <a name="configuration"></a>Konfiguration

Føje beskeder om Management-løsning til arbejdsområdet OMS ved hjælp af processen beskrevet i [Tilføj løsninger](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.

## <a name="management-packs"></a>Management packs

Hvis din SCOM management gruppe er forbundet til arbejdsområdet OMS, skal de følgende management packs installeres i SCOM når du tilføjer denne løsning.  Der er ingen konfiguration eller vedligeholdelse af disse management packs påkrævet.  

- Microsoft System Center Advisor beskeder om Management (Microsoft.IntelligencePacks.AlertManagement)

Du kan finde flere oplysninger om hvordan løsning management packs opdateres, [Forbinde Operations Manager til Log analyser](log-analytics-om-agents.md).

## <a name="data-collection"></a>Dataindsamling

### <a name="agents"></a>Supportmedarbejdere

I følgende tabel beskrives de tilknyttede kilder, der understøttes af denne løsning.

| Forbundne kilde | Support | Beskrivelse |
|:--|:--|:--|
| [Windows supportmedarbejdere](log-analytics-windows-agents.md) | Nej | Direkte Windows supportmedarbejdere genererer ikke SCOM beskeder. |
| [Linux supportmedarbejdere](log-analytics-linux-agents.md) | Nej | Direkte Linux supportmedarbejdere genererer ikke SCOM beskeder. |
| [SCOM administration af gruppe](log-analytics-om-agents.md) | Ja | Beskeder, der er oprettet i SCOM supportmedarbejdere er leveret til administration af gruppe og derefter videresendt til Log analyser.<br><br>En direkte forbindelse fra en agent for SCOM til Log Analytics er ikke påkrævet. Beskeder om data er videresendt fra gruppen management til OMS-lager. |
| [Azure-lager-konto](log-analytics-azure-storage.md) | Nej | SCOM beskeder gemmes ikke i Azure lagerplads konti. |

### <a name="collection-frequency"></a>Frekvens af websteder

Påmindelser, der oprettes i OMS er tilgængelige for løsningen med det samme.  Beskeder om data der sendes fra gruppen SCOM management til Log Analytics hvert 3.  

## <a name="using-the-solution"></a>Ved hjælp af løsningen

Når du tilføjer beskeder om Management-løsning til arbejdsområdet OMS, tilføjes feltet **Beskeder om Management** til dashboardet OMS.  Dette felt viser en Tæl og grafisk repræsentation af antallet af aktive beskeder, der blev oprettet i de seneste 24 timer.  Du kan ikke ændre dette tidsinterval.

![Beskeder om Management-felt](media/log-analytics-solution-alert-management/tile.png)

Klik på feltet **Besked administration** til at åbne **Besked administration** dashboard.  Dashboardet indeholder kolonnerne, i den følgende tabel.  Hver kolonne, som viser de øverste ti påmindelser efter antal, der opfylder kriterierne for denne kolonne til det angivne område og tidsinterval.  Du kan køre en log søgning, der indeholder hele listen ved at klikke på **se alle** nederst i kolonnen eller ved at klikke på kolonneoverskriften.

| Kolonne| Beskrivelse |
|:--|:--|
| Vigtige beskeder | Alle vigtige beskeder med en prioritering på kritisk grupperet efter beskeder om navn.  Klik på navnet på en besked til at køre en log søgning returnere alle poster for den pågældende påmindelse. |
| Advarsel om beskeder | Alle vigtige beskeder med en prioritering på advarsel, der er grupperet efter beskeder om navn.  Klik på navnet på en besked til at køre en log søgning returnere alle poster for den pågældende påmindelse. |
| Aktive SCOM beskeder | Alle SCOM vigtige beskeder med en tilstand dog *lukket* grupperet efter kilde, som genererede beskeden. |
| Alle aktive beskeder | Alle vigtige beskeder med en hvilken som helst alvorlighed, der er grupperet efter beskeder om navn. Kun omfatter SCOM beskeder med en hvilken som helst tilstand end *lukket*.|

Hvis du ruller til højre, kan dashboardet-flere almindelige forespørgsler, som du kan klikke på til at udføre en [log søgning](log-analytics-log-searches.md) efter beskeder om data.

![Beskeder om administrationsdashboard](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>Område- og klokkeslætsintervallet

Som standard er omfanget af de vigtige beskeder analysere beskeder om Management-løsning fra alle forbundne management grupper, der er oprettet i de seneste 7 dage.  

![Beskeder om administration af omfang](media/log-analytics-solution-alert-management/scope.png)

- Hvis du vil ændre grupperne management medtaget i analysen, skal du klikke på **omfang** øverst på dashboardet.  Du kan enten vælge **Global** for alle forbundne management grupper eller **Ved Management Group** for at markere en enkelt management-gruppe.

- Hvis du vil ændre tidsintervallet for vigtige beskeder, Vælg **Data baseret på** øverst i dashboardet.  Du kan vælge påmindelser, der oprettes i de seneste 7 dage, 1 dag eller 6 timer.  Eller du kan vælge **brugerdefineret** og angive et brugerdefineret datointerval.

## <a name="log-analytics-records"></a>Log Analytics poster

Beskeder om Management-løsning analyserer en post med en type af **beskeder om**.  Det kan også importere beskeder fra SCOM og oprette en tilsvarende post for hver med en type **besked** og en SourceSystem af **OpsManager**.  Disse poster har egenskaber i den følgende tabel.  

| Egenskaben | Beskrivelse |
|:--|:--|
| Type | *Giv besked* |
| SourceSystem | *OpsManager* |
| AlertContext | Detaljerne i dataelementet, der forårsagede påmindelsen skal genereres i XML-format. |
| AlertDescription | Detaljeret beskrivelse af beskeden. |
| AlertId | GUID for påmindelsen. |
| AlertName | Navnet på beskeden. |
| AlertPriority | Prioritetsniveauet for påmindelsen. |
| AlertSeverity | Prioritering af påmindelsen. |
| AlertState | Seneste opløsning tilstanden for påmindelsen. |
| LastModifiedBy | Navnet på den bruger, der senest har ændret beskeden. |
| ManagementGroupName | Navnet på gruppen administration, hvor meddelelsen blev genereret. |
| RepeatCount | Antallet af tid, der er den samme meddelelsen blev genereret for samme overvåges objekt siden fortolkes. |
| ResolvedBy | Navnet på den bruger, der løst beskeden. Tømme Hvis påmindelsen er endnu ikke løst. |
| SourceDisplayName | Vist navn for objektet overvågning, som genererede beskeden. |
| SourceFullName | Fulde navn for objektet overvågning, som genererede beskeden. |
| TicketId | Brugertilladelse-ID'ET for den vigtige besked, hvis SCOM miljøet er integreret med en proces til at tildele billetter til beskeder.  Tom af ingen brugertilladelse tildeles-ID. |
| TimeGenerated | Dato og klokkeslæt, der blev oprettet beskeden. |
| TimeLastModified | Dato og klokkeslæt, beskeden sidst blev ændret. |
| TimeRaised | Dato og klokkeslæt, der blev oprettet beskeden. |
| TimeResolved | Dato og klokkeslæt, der blev løst beskeden. Tømme Hvis påmindelsen er endnu ikke løst. |

## <a name="sample-log-searches"></a>Eksempel log søgninger

Den følgende tabel indeholder eksempel log søger efter beskeder om poster, der indsamles via denne løsning.  

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = beskeder om SourceSystem = OpsManager AlertSeverity = fejl TimeRaised > nu - 24 TIMERS | Vigtige beskeder opløftet under de seneste 24 timer |
| Skriv = beskeder om AlertSeverity = advarsel TimeRaised > nu - 24 TIMERS | Advarsel om beskeder opløftet under de seneste 24 timer  |
| Skriv = beskeder om SourceSystem = OpsManager AlertState! = lukket TimeRaised > nu - 24 TIMERS og #124; måle count() som Tæl i SourceDisplayName | Datakilder med aktive beskeder opløftet under de seneste 24 timer |
| Skriv = beskeder om SourceSystem = OpsManager AlertSeverity = fejl TimeRaised > nu - 24 TIMERS AlertState! = lukket | Vigtige beskeder opløftet under de seneste 24 timer, der stadig er aktiv |
| Skriv = beskeder om SourceSystem = OpsManager TimeRaised > nu - 24 TIMERS AlertState = lukket | Beskeder opløftet under de seneste 24 timer, lukkes nu |
| Skriv = beskeder om SourceSystem = OpsManager TimeRaised > nu - 1 dag og #124; måle count() som Tæl i AlertSeverity | Beskeder opløftet under den tidligere 1 dag, der er grupperet efter deres klassifikation |
| Skriv = beskeder om SourceSystem = OpsManager TimeRaised > nu - 1 dag og #124; sortere RepeatCount desc | Beskeder opløftet under den tidligere 1 dag, der er sorteret efter deres antal gentagelser værdi |

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [beskeder i Log Analytics](log-analytics-alerts.md) få at vide om oprettelse af beskeder fra Log Analytics.
