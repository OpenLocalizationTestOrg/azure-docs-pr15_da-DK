<properties
    pageTitle="Log Analytics logge search REST-API | Microsoft Azure"
    description="Denne vejledning indeholder et grundlæggende selvstudium, der beskriver, hvordan du kan bruge Log Analytics søgningen REST-API i handlinger Management pakke (OMS) og den indeholder eksempler, der viser, hvordan du bruge kommandoerne."
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
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>Log Analytics log search REST-API

Denne vejledning indeholder et grundlæggende selvstudium, der beskriver, hvordan du kan bruge Log Analytics Search REST-API i handlinger Management Suite (OMS) og den indeholder eksempler, der viser, hvordan du bruge kommandoerne. Nogle af eksemplerne i denne artikel henviser funktionsdygtige viden, som er navnet på den tidligere version af Log analyser.

## <a name="overview-of-the-log-search-rest-api"></a>Oversigt over Log søgningen REST-API

Log Analytics Search REST-API er RESTful og kan åbnes via Azure ressourcestyring API. I dette dokument kan du finde eksempler hvor API åbnes gennem [ARMClient](https://github.com/projectkudu/ARMClient), en Åbn kilde kommandolinjeværktøj, der forenkler aktivering Azure ressourcestyring API. Brug af ARMClient og PowerShell er et mange muligheder for at få adgang til API'EN Log Analytics søgning. En anden mulighed er at bruge Azure PowerShell-modulet til OperationalInsights, som omfatter cmdletter for at få adgang til søgning. Med disse værktøjer kan du udnytte RESTful Azure ressourcestyring API for at foretage opkald til OMS arbejdsområder og udføre søgning kommandoer grænserne. API output søgeresultater for dig i JSON-formatet, så du kan bruge søgeresultaterne fra et program på mange forskellige måder.

Ressourcestyring Azure kan bruges via et [bibliotek til .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) samt en [REST-API](https://msdn.microsoft.com/library/azure/mt163658.aspx). Gennemse de sammenkædede websider for at få mere at vide.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Grundlæggende Log Analytics Search REST-API-selvstudium

### <a name="to-use-the-arm-client"></a>Bruge ARM klienten

1. Installere [Chocolatey](https://chocolatey.org/), som er en åben kildestyring for pakke til Windows. Åbn et kommandopromptvindue som administrator og derefter køre følgende kommando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Du kan installere ARMClient ved at køre følgende kommando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Til at udføre en enkel søgning ved hjælp af ARMClient

1. Log på din Microsoft- eller OrgID konto:

    ```
    armclient login
    ```

    En vellykket login vises alle de abonnementer, der er knyttet til den angivne konto:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Få handlinger Management Suite arbejdsområderne:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    En vellykket få opkald ville Medtag alle de arbejdsområder, der er knyttet til abonnementet:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Oprette din søgning variabel:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Søge ved hjælp af din nye Søg variabel:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Log Analytics Search REST-API reference eksempler
Følgende eksempler viser, hvordan du kan bruge søgning API.

### <a name="search---actionread"></a>Søgning – handling/læst

**Eksempel URL-adresse:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Anmode om:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
I følgende tabel beskrives de egenskaber, der er tilgængelige.

|**Egenskaben**|**Beskrivelse**|
|---|---|
|toppen|Det maksimale antal resultater til at returnere.|
|fremhæve|Indeholder pre og indlæg parametre, bruges normalt til fremhævning tilsvarende felter|
|før|Præfikser den angivne streng til de tilsvarende felter.|
|indlæg|Føjer den angivne streng til de tilsvarende felter.|
|forespørgsel|Søgestrengen bruges til at indsamle og returnere resultater.|
|Start|Starten af en tidsramme, du vil have resultatet til at blive tilføjet fra.|
|Afslut|Slutningen af en tidsramme, du vil have resultatet til at blive tilføjet fra.|


**Svar:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Søge / {ID} - handling/læst

**Anmode om indholdet af en gemt søgning:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Hvis søgningen returnerer med statussen 'Ventende', kan derefter forespørgsel blandt de opdaterede resultater udføres via denne API. Når du 6 min resultatet af søgningen fjernes fra cachen og HTTP præsteret returneres. Hvis den indledende søgeanmodning returneres statussen 'Vellykket' med det samme, føjes det ikke til cachen medfører denne API til at returnere HTTP præsteret Hvis forespørges. Indholdet af en HTTP 200 resultatet være i samme format som den indledende søgeanmodning lige med opdaterede værdier.

### <a name="saved-searches---rest-only"></a>Gemte søgninger - RESTEN kun

**Anmode om liste over gemte søgninger:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Understøttede metoder: få PLACERE slette

Understøttes af websteder metoder: få

I følgende tabel beskrives de egenskaber, der er tilgængelige.

|Egenskaben|Beskrivelse|
|---|---|
|Id|Entydigt id.|
|ETag|**Kræves til rettelsen**. Opdateret af server på hver skrivning. Værdien skal være lig med den aktuelle lagrede værdi eller ' *' at opdatere. 409 returneres for gamle/ugyldig værdier.|
|Properties.Query|Der er **påkrævet**. Søgeforespørgsel.|
|properties.displayName|Der er **påkrævet**. Brugerens defineret viste navn i forespørgslen. Hvis udformet som en Azure ressource, vil det være et mærke.|
|Properties.category|Der er **påkrævet**. Den brugerdefinerede kategori af forespørgslen. Hvis formateret som en Azure ressource dette ville være et mærke.|

>[AZURE.NOTE] Log Analytics Søg API returnerer aktuelt brugeroprettede gemte søgninger når spurgt om gemte søgninger i et arbejdsområde. API returnerer ikke gemte søgninger, der leveres af løsninger på nuværende tidspunkt. Denne funktionalitet tilføjes på et senere tidspunkt.

### <a name="create-saved-searches"></a>Oprette gemte søgninger

**Anmode om:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Slet gemte søgninger

**Anmode om:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Opdater gemt søgninger

 **Anmode om:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadata - JSON kun

Her er en måde at få vist felter for alle typer af logfilen for de data, der indsamles i arbejdsområdet. Eksempelvis hvis du vil du vide, hvis hændelsestypen et felt med navnet Computer, er derefter dette en metode til at slå op, og Bekræft.

**Anmodning om felter:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Svar:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

I følgende tabel beskrives de egenskaber, der er tilgængelige.

|**Egenskaben**|**Beskrivelse**|
|---|---|
|Navn|Feltnavn.|
|vist navn|Det viste navn i feltet.|
|type|Typen af værdien i feltet.|
|facetable|Kombination af aktuelle 'indekseret', ' gemt ' og 'grene' egenskaber.|
|få vist|Aktuelle 'Vis' egenskab. True, hvis feltet er synligt i Søg.|
|OwnerType er|Begrænset til kun de typer, der hører til onboarded IP.|


## <a name="optional-parameters"></a>Valgfrie parametre
Følgende oplysninger i denne artikel beskrives valgfrie parametre, der er tilgængelige.

### <a name="highlighting"></a>Fremhævning

Parameteren "Fremhævning" er en valgfri parameter, du kan bruge til at anmode om søgning undersystem medtage et sæt af datamærker i dens svar.

Disse mærker angiver start og Afslut fremhævede tekst, der opfylder de betingelser, der er angivet i søgestrengen.
Du kan angive start- og markeringerne, der skal bruges ved søgning ombryde fremhævede ordet.

**Eksempel søgeforespørgsel**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Eksempel resultat:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Bemærk, at resultatet ovenfor indeholder en fejlmeddelelse, der er præfikset og føjet.

## <a name="computer-groups"></a>Computergrupper

Computergrupper er speciel gemte søgninger, der returnerer et sæt af computere.  Du kan bruge en computergruppe i andre forespørgsler til at begrænse resultaterne til computere i gruppen.  En computergruppe er implementeret som en gemt søgning med mærket gruppe med en værdi på computeren.

Følgende er et eksempel svar til en computergruppe.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Hentning af grupper af computere

Bruge metoden Get med gruppe-ID til at hente en computergruppe.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Oprette eller opdatere en computergruppe

Bruge metoden læg med et entydigt gemt søgning-ID til at oprette en ny computergruppe. Hvis du bruger en eksisterende computer-gruppe-ID'ET, vil derefter, at en blive ændret. Når du opretter en computergruppe i konsollen OMS, oprettes ID'ET fra gruppen og navn.

Den forespørgsel, der bruges til gruppedefinition skal returnere et sæt af computere til gruppen kan fungere korrekt.  Det anbefales, at du afslutter din forespørgsel med *| Entydige Computer* at sikre, at de korrekte data returneres.

Definitionen af den gemte søgning skal indeholde et mærke, kaldet gruppe med en værdi på computeren for søgningen skal klassificeres som en computergruppe for.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Slette computergrupper

Brug metoden Delete med gruppe-ID til at slette en computergruppe.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at oprette forespørgsler med brugerdefinerede felter for kriterier.
