<properties 
   pageTitle="Få vist diagnosticeringslogfiler til Azure datalager sø | Microsoft Azure" 
   description="Forstå, hvordan du konfigurerer og få adgang til diagnosticeringslogfiler til Azure sø datalager " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Få adgang til diagnosticeringslogfiler til Azure sø datalager

Få mere at vide om, hvordan du kan aktivere logføring af diagnostik for kontoen sø datalager og hvordan du kan få vist de logfiler, der indsamles for din konto.

Organisationer kan aktivere logføring af diagnostik for deres konto til at indsamle data access revisionsspor Azure sø datalager, der indeholder oplysninger som liste over brugere adgang til dataene, hvor ofte dataene åbnes, hvor meget data er gemt i firma, osv.

## <a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure sø datalager konto**. Følg vejledningen på [Introduktion til Azure Data sø Store ved hjælp af portalen Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Aktivere logføring af diagnostik for kontoen sø datalager

1. Log den nye [Azure-portalen](https://portal.azure.com).

2. Åbn din sø datalager-konto, og din sø datalager konto blade, klik på **Indstillinger**, og klik derefter på **Indstillinger for diagnosticering**.

3. Foretage følgende ændringer til at konfigurere logføring af diagnostik i bladet **diagnosticering** .

    ![Aktivere logføring af diagnostik] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Aktivere diagnosticeringslogfiler")

    * Angiv **Status** til **på** til at aktivere logføring af diagnostik.
    * Du kan vælge at store/proces dataene på to forskellige måder.
        * Vælg indstillingen for at **eksportere til begivenhed Hub** til at streame logdata til en Azure begivenhed Hub. Højst sandsynligt Brug denne indstilling, hvis du har en efterfølgende behandling pipeline til at analysere indgående logfiler i realtid. Hvis du vælger denne indstilling, skal du angive detaljerne for Azure begivenhed hubben du vil bruge.
        * Vælg indstillingen for at **eksportere til lagerplads konto** til at gemme logfiler til en Azure-lager-konto. Brug denne indstilling, hvis du ønsker at arkivere de data, der skal være batchen behandles på et senere tidspunkt. Hvis du vælger denne indstilling, skal du angive en Azure-lager konto for at gemme logge på.
    * Angiv, om du vil have overvågningslogge eller anmodning om logfiler eller begge dele.
    * Angiv antallet dage, hvor dataene skal bevares.
    * Klik på **Gem**.

Når du har aktiveret diagnosticering indstillinger, kan du se logge i fanen **Diagnosticeringslogfiler** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Vis diagnosticeringslogfiler for kontoen sø datalager

Der er to måder at få vist dataene i logfilen for kontoen sø datalager.

* Fra kontoen sø datalager visningen indstillinger for
* Fra kontoen Azure-lager, hvor data er gemt

### <a name="using-the-data-lake-store-settings-view"></a>Bruge visningen sø Store dataindstillinger

1. Blade din sø datalager konto- **Indstillinger** , klik på **Diagnosticeringslogfiler**.

    ![Logføring af diagnostik visning] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Vis diagnosticeringslogfiler") 

2. Du bør se loggene kategoriseret efter **Overvågningslogge** og **Anmode om logfiler**i bladet **Diagnosticeringslogfiler** .
    * Anmodning om logfiler registrere hver API-anmodning, der er foretaget på kontoen sø datalager.
    * Overvågningslogge ligner anmode om logfiler, men ikke en meget mere detaljeret opdeling af de handlinger, der udføres på kontoen sø datalager. Et enkelt Overfør API-kald i anmodning om logfiler kan for eksempel medføre flere "Tilføj" handlinger i overvågningslogge.

3. Klik på linket **Download** mod hver post i logfilen hente loggene.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Fra Azure-lager-konto indeholder, der logdata

1. Åbn bladet Azure-lager-konto, der er knyttet til sø datalager til logføring, og klik derefter på BLOB. Bladet **Blob service** viser to beholdere.

    ![Logføring af diagnostik visning] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Vis diagnosticeringslogfiler")

    * Objektbeholder **indsigt-logfiler-overvågningslog** indeholder overvågningslogge.
    * Objektbeholder **indsigt-logfiler-pakker** indeholder loggene anmodning.

2. I disse beholdere gemmes loggene under følgende struktur.

    ![Logføring af diagnostik visning] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Vis diagnosticeringslogfiler")

    Som et eksempel, der kan være den fulde sti til en overvågningslog`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similary, skyldes den fulde sti til en anmodning om logfil`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Forstå strukturen i dataene i logfilen

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName   | Streng | Navnet på den handling, som logføres. For eksempel getfilestatus.              |
| resultType      | Streng | Status for handlingen, f.eks, 200.                                 |
| callerIpAddress | Streng | IP-adressen på den klient, der udfører anmodningen                                |
| correlationId   | Streng | Id for den logfil, som kan bruges til at gruppere et sæt af relaterede logposter |
| identitet        | Objekt | Den identitet, som genererede logfilen                                            |
| Egenskaber      | JSON   | Se under få mere at vide                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName   | Streng | Navnet på den handling, som logføres. For eksempel getfilestatus.              |
| resultType      | Streng | Status for handlingen, f.eks, 200.                                 |
| correlationId   | Streng | Id for den logfil, som kan bruges til at gruppere et sæt af relaterede logposter |
| identitet        | Objekt | Den identitet, som genererede logfilen                                            |
| Egenskaber      | JSON   | Se under få mere at vide                                                          |

#### <a name="audit-log-properties-schema"></a>Overvågningslog log egenskaber skema

| Navn       | Type   | Beskrivelse                              |
|------------|--------|------------------------------------------|
| StreamName | Streng | Stien handlingen blev udført på  |


## <a name="samples-to-process-the-log-data"></a>Prøver at behandle dataene i logfilen

Azure datalager sø indeholder et eksempel i at behandle og analysere dataene i logfilen. Du kan finde stikprøvernes på [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Se også

- [Oversigt over Azure sø datalager](data-lake-store-overview.md)
- [Sikre data i sø datalager](data-lake-store-secure-data.md)

