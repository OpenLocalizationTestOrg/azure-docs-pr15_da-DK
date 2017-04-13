<properties 
   pageTitle="Få vist diagnosticeringslogfiler til Azure Data sø Analytics | Microsoft Azure" 
   description="Forstå, hvordan du konfigurerer og få adgang til diagnosticeringslogfiler for Azure Data sø analyser " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Få adgang til diagnosticeringslogfiler for Azure Data sø analyser

Få mere at vide om, hvordan du kan aktivere logføring af diagnostik for kontoen Data sø analyser og hvordan du kan få vist de logfiler, der indsamles for din konto.

Organisationer kan aktivere logføring af diagnostik til deres Azure Data sø Analytics-konto til at indsamle data access revisionsspor. Disse logfiler indeholder oplysninger som f.eks.:

* En liste over brugere, få adgang til dataene.
* Hvor ofte dataene åbnes.
* Hvor meget data er gemt på kontoen.

## <a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivere abonnementet Azure** Data sø Analytics Public Preview. Se [vejledningen](data-lake-analytics-get-started-portal.md#signup).
- **Azure Data sø Analytics-konto**. Følg vejledningen på [Introduktion til Azure Data sø analyser ved hjælp af portalen Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Aktivere logføring

1. Log den nye [Azure-portalen](https://portal.azure.com).

2. Åbn din Data sø Analytics-konto, og dine Data sø Analytics konto blade, klik på **Indstillinger**, og klik derefter på **Indstillinger for diagnosticering**.

3. Foretage følgende ændringer til at konfigurere logføring af diagnostik i bladet **diagnosticering** .

    ![Aktivere logføring af diagnostik] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Aktivere diagnosticeringslogfiler")

    * Angiv **Status** til **på** til at aktivere logføring af diagnostik.
    * Du kan vælge at store/proces dataene på to forskellige måder.
        * Vælg **Eksportér til begivenhed Hub** til at streame logdata til en Azure begivenhed Hub. Brug denne indstilling, hvis du har en efterfølgende behandling pipeline til at analysere indgående logfiler i realtid. Hvis du vælger denne indstilling, skal du angive detaljerne for Azure begivenhed hubben du vil bruge.
        * Vælg **Eksportér til lagerplads konto** til at gemme logfiler til en Azure-lager-konto. Brug denne indstilling, hvis du ønsker at arkivere data. Hvis du vælger denne indstilling, skal du angive en Azure-lager konto for at gemme logge på.
    * Angiv, om du vil have overvågningslogge eller anmodning om logfiler eller begge dele.
    * Angiv antallet dage, hvor dataene skal bevares.
    * Klik på **Gem**.

Når du har aktiveret diagnosticering indstillinger, kan du se logge i fanen **Diagnosticeringslogfiler** .

## <a name="view-logs"></a>Vis logfiler

Der er to måder at få vist dataene i logfilen for din Data sø Analytics-konto.

* Fra Data sø Analytics-kontoindstillinger
* Fra kontoen Azure-lager, hvor data er gemt

### <a name="using-the-data-lake-analytics-settings-view"></a>Bruge visningen sø Analytics dataindstillinger

1. Blade dine Data sø Analytics konto- **Indstillinger** , klik på **Diagnosticeringslogfiler**.

    ![Logføring af diagnostik visning] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Vis diagnosticeringslogfiler") 

2. Du bør se loggene kategoriseret efter **Overvågningslogge** og **Anmode om logfiler**i bladet **Diagnosticeringslogfiler** .
    * Anmodning om logfiler registrere hver API-anmodning, der er foretaget på Data sø Analytics-kontoen.
    * Overvågningslogge ligner anmode om logfiler, men ikke en meget mere detaljeret opdeling af de handlinger, der udføres på Data sø Analytics-kontoen. Et enkelt Overfør API-kald i anmodning om logfiler kan for eksempel medføre flere "Tilføj" handlinger i overvågningslogge.

3. Klik på linket **Download** for en post i logfilen hente loggene.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Fra Azure-lager-konto indeholder, der logdata

1. Åbn bladet Azure-lager-konto, der er knyttet til Data sø Analytics til logføring, og klik derefter på BLOB. Bladet **Blob service** viser to beholdere.

    ![Logføring af diagnostik visning] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Vis diagnosticeringslogfiler")

    * Objektbeholder **indsigt-logfiler-overvågningslog** indeholder overvågningslogge.
    * Objektbeholder **indsigt-logfiler-pakker** indeholder loggene anmodning.

2. I disse beholdere gemmes loggene under følgende struktur.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] Den `##` poster i stien indeholder år, måned, dag og time som loggen blev oprettet. Data sø Analytics opretter én fil hver time, så `m=` indeholder altid en værdi på `00`.

    Den fulde sti til en overvågningslog skyldes eksempelkuben:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    På samme måde, den fulde sti til en anmodning om logfil kan være:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Log struktur

Revisions- og anmodning om logfiler er i en JSON-format. I dette afsnit skal vi se på strukturen i JSON for anmodning og overvågningslogge.

### <a name="request-logs"></a>Anmode om logfiler

Her er et eksempel på en post i JSON-formateret anmodning logfilen. Hver blob har ét rodobjekt kaldet **poster** , der indeholder en matrix med log objekter.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Anmode om log skema

| Navn            | Type   | Beskrivelse                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tid            | Streng | Tidsstemplet (i UTC) af logfilen                                              |
| resourceId      | Streng | ID for den ressource, som handlingen tog Placer på                            |
| kategori        | Streng | Kategorien log. Eksempelvis **anmodninger**.                                   |
| operationName   | Streng | Navnet på den handling, som logføres. For eksempel GetAggregatedJobHistory.              |
| resultType      | Streng | Status for handlingen, f.eks, 200.                                 |
| callerIpAddress | Streng | IP-adressen på den klient, der udfører anmodningen                                |
| correlationId   | Streng | Id for logfilen. Denne værdi kan bruges til at gruppere et sæt af relaterede logposter |
| identitet        | Objekt | Den identitet, som genererede logfilen                                            |
| Egenskaber      | JSON   | Se næste afsnit (anmodning om log egenskaber skema) få mere at vide |

#### <a name="request-log-properties-schema"></a>Anmode om log egenskaber skema

| Navn                 | Type   | Beskrivelse                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Streng | HTTP-metoden bruges til handlingen. For eksempel få. |
| Sti                 | Streng | Stien handlingen blev udført på                   |
| RequestContentLength | heltal    | Indhold længden på HTTP-anmodningen                    |
| ClientRequestId      | Streng | Det Id, der entydigt identificerer denne anmodning              |
| Starttidspunkt            | Streng | Det tidspunkt, hvor serveren har modtaget anmodningen         |
| Sluttidspunkt              | Streng | Det tidspunkt, hvor serveren sendt et svar              |

### <a name="audit-logs"></a>Overvågningslogge

Her er et eksempel på en post i JSON-formateret overvågningsloggen. Hver blob har ét rodobjekt kaldet **poster** , der indeholder en matrix med log objekter

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Overvågningslog log skema

| Navn            | Type   | Beskrivelse                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tid            | Streng | Tidsstemplet (i UTC) af logfilen                                              |
| resourceId      | Streng | ID for den ressource, som handlingen tog Placer på                            |
| kategori        | Streng | Kategorien log. For eksempel, **revision**.                                      |
| operationName   | Streng | Navnet på den handling, som logføres. For eksempel JobSubmitted.              |
| resultType | Streng | En understatus for jobstatus (operationName). |
| resultSignature | Streng | Yderligere oplysninger om jobstatus (operationName). |
| identitet      | Streng | Den bruger, der anmodes om handlingen. For eksempel susan@contoso.com.                                 |
| Egenskaber      | JSON   | Se næste afsnit (overvågningslog log egenskaber skema) få mere at vide |

> [AZURE.NOTE] __resultType__ og __resultSignature__ giver oplysninger om resultatet af en handling, og kun indeholder en værdi, hvis en handling er fuldført. For eksempel indeholder en værdi, når __operationName__ indeholder en værdi af __JobStarted__ eller __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Overvågningslog log egenskaber skema

| Navn       | Type   | Beskrivelse                              |
|------------|--------|------------------------------------------|
| Job-id | Streng | Det ID, der er tildelt til jobbet  |
| Jobnavn | Streng | Det navn, som blev knyttet til jobbet |
| JobRunTime | Streng | Bruges til at behandle jobbet runtime |
| SubmitTime | Streng | Den tid (i UTC), hvor jobbet blev sendt |
| Starttidspunkt | Streng | Den tid, der er startet for jobbet kører efter afsendelse (i UTC). |
| Sluttidspunkt | Streng | Det tidspunkt jobbet afsluttet. |
| Parallelitet | Streng | Antallet af Data sø Analytics-enheder, der anmodes om for dette job under afsendelse. |

> [AZURE.NOTE] __SubmitTime__, __Starttidspunkt__, __Sluttidspunkt__ og __parallelitet__ give oplysninger om en handling, og kun indeholder en værdi, hvis handlingen har startet eller afsluttet. For eksempel indeholder __SubmitTime__ en værdi, når __operationName__ angiver __JobSubmitted__.

## <a name="process-the-log-data"></a>Behandle dataene i logfilen

Azure Data sø Analytics indeholder et eksempel i at behandle og analysere dataene i logfilen. Du kan finde stikprøvernes på [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Næste trin

- [Oversigt over Azure Data sø Analytics](data-lake-analytics-overview.md)


