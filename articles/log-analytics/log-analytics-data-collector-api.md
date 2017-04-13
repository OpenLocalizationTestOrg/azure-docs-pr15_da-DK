<properties
    pageTitle="Log Analytics HTTP-Data indsamler API | Microsoft Azure"
    description="Du kan bruge Log Analytics HTTP Data indsamler API for at tilføje INDLÆG JSON data fra en klient, der kan ringe til REST-API Log Analytics-lager. I denne artikel beskrives, hvordan du bruger API'EN, og har eksempler på, hvordan du publicerer data ved hjælp af forskellige programmeringssprog."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="bwren"/>


# <a name="log-analytics-http-data-collector-api"></a>Log Analytics HTTP-Data indsamler API

Når du bruger Azure Log Analytics HTTP Data indsamler API, kan du tilføje INDLÆG JavaScript Object Notation (JSON) data til Log Analytics-lager fra en klient, der kan ringe til REST-API. Ved hjælp af denne metode, kan du sende data fra tredjepartsprogrammer eller fra scripts, som fra en runbook i Azure Automation.  

## <a name="create-a-request"></a>Oprette en anmodning om

De næste to tabeller viser de attributter, der kræves for hver enkelt anmodning til Log Analytics HTTP Data indsamler API. Vi beskriver hver attribut mere detaljeret senere i denne artikel.

### <a name="request-uri"></a>Anmode om URI

| Attribut | Egenskaben |
|:--|:--|
| Metode | INDLÆG |
| URI | https://\<kunde-id\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Indholdstype | programmet/json |

### <a name="request-uri-parameters"></a>Anmode om URI parametre
| Parameter | Beskrivelse |
|:--|:--|
| Kunde-id  | Entydigt id til Microsoft Operations Management Suite arbejdsområdet. |
| Ressource    | Ressourcenavn API: / api/logfiler. |
| API-Version | Versionen af API til brug med denne anmodning. Det er i øjeblikket, 2016-04-01. |

### <a name="request-headers"></a>Anmode om sidehoveder
| Sidehoved | Beskrivelse |
|:--|:--|
| Godkendelse | Godkendelse signatur. Senere i denne artikel kan du læse om, hvordan du opretter en HMAC SHA256 sidehoved. |
| Log af typen | Angiv posttypen af de data, der sendes. Typen log understøtter i øjeblikket kun alfanumeriske tegn. Tal eller specialtegn, understøttes ikke. |
| x-ms-dato | Den dato, anmodningen blev behandlet i RFC 1123 format. |
| tid genereres felt | Navnet på et felt i de data, der indeholder tidsstemplet for dataelementet. Hvis du angiver et felt bruges indholdet til **TimeGenerated**. Hvis dette felt ikke er angivet, er standarden for **TimeGenerated** det tidspunkt, hvor meddelelsen er optagelse. Indholdet af meddelelsesfeltet skal følge formatet ISO 8601 åååå-MM-DDThh:mm:ssZ. |


## <a name="authorization"></a>Godkendelse

En anmodning til Log Analytics HTTP Data indsamler API skal indeholde en tilladelse sidehoved. Hvis du vil godkende en anmodning, skal du logge på anmodningen med primært eller sekundære nede for det arbejdsområde, der opretter anmodningen. Overføre derefter signaturen som en del af anmodningen.   

Her er format for overskriften tilladelse:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* er det entydige id for handlinger Management Suite arbejdsområdet. *Signatur* er [Hash-baseret meddelelse godkendelse kode (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) , der er dannet ud fra anmodningen og derefter beregnes ved hjælp af [SHA256 algoritme](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Derefter skal koder du den ved hjælp af Base64 kodning.

Brug dette format til at kode strengen **SharedKey** signatur:

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Her er et eksempel på en signatur streng:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Når du har strengen signatur, kodes ved hjælp af algoritmen HMAC SHA256 på den UTF-8-kodet streng, og derefter kode resultatet som Base64. Brug dette format:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Eksemplerne i næste afsnit har eksempelkode kan hjælpe dig med at oprette en tilladelse sidehoved.

## <a name="request-body"></a>Anmodningsteksten

Brødteksten i meddelelsen skal være i JSON. Det skal indeholde en eller flere poster med egenskaben og værdipar i dette format:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Du kan batch flere poster sammen i en enkelt anmodning ved hjælp af følgende format. Alle poster skal være den samme posttype.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Posttype og egenskaber

Du kan angive en brugerdefineret posttype, når du sender data via Log Analytics HTTP Data indsamler API. I øjeblikket, kan du skrive data til eksisterende posttyper, som er oprettet af andre datatyper og løsninger. Log Analytics læser indgående data og opretter derefter egenskaber, der svarer til datatypen for de værdier, du angiver.

Hver anmodning til Log Analytics API skal indeholde en **Log-typen** sidehoved med navnet på posttypen. Suffiks **_CL** føjes automatisk til navnet, du angiver for at skelne det fra andre logtyper som en brugerdefineret logfil. Hvis du angiver navnet **MyNewRecordType**, opretter Log Analytics for eksempel en post af typen **MyNewRecordType_CL**. Dette er med til at sikre, at der ikke er nogen konflikter mellem brugeroprettede Skriv navne og dem, der er afsendt i igangværende eller kommende Microsoft solutions.

For at identificere en egenskab datatype, føjer Log Analytics et suffiks til navnet. Hvis en egenskab indeholder en null-værdi, er egenskaben ikke medtaget i den pågældende post. I denne tabel vises de egenskaben datatype og tilsvarende suffiks:

| Egenskaben datatype | Suffiks |
|:--|:--|
| Streng    | _s |
| Boolesk værdi   | _b |
| Dobbelt    | _d |
| Dato og klokkeslæt | _t |
| GUID      | _g |


Den datatype, der bruger Log Analytics for hver egenskab, afhænger af, om posttypen for den nye post allerede findes.

- Hvis posttypen ikke findes, opretter Log Analytics en ny. Log Analytics anvender JSON type afledning til at bestemme datatypen for hver egenskab for den nye post.
- Hvis posttypen eksisterer, forsøger Log Analytics at oprette en ny post, der er baseret på eksisterende egenskaber. Hvis datatypen for en egenskab i den nye post ikke stemmer overens med og kan ikke konverteres til den eksisterende type, eller Hvis posten, omfatter en egenskab, der ikke findes, Log Analytics opretter en ny egenskab, der indeholder det relevante suffiks.

For eksempel vil bidrag posten oprette en post med tre egenskaber, **number_d**, **boolean_b**og **string_s**:

![Eksempel post 1](media/log-analytics-data-collector-api/record-01.png)

Hvis du derefter sendt denne næste post med alle værdier, der er formateret som strenge, vil ikke ændre egenskaberne. Disse værdier kan konverteres til eksisterende datatyper:

![Eksempel post 2](media/log-analytics-data-collector-api/record-02.png)

Men hvis du har foretaget denne næste afsendelse, Log Analytics vil oprette nye egenskaber **boolean_d** og **string_d**. Kan ikke konverteres disse værdier:

![Eksempel post 3](media/log-analytics-data-collector-api/record-03.png)

Hvis du derefter sendt den følgende post, før posttypen blev oprettet, vil Log Analytics oprette en post med tre egenskaber, **tal_s**, **boolean_s**og **string_s**. I denne post, er hver af de oprindelige værdier formateret som en streng:

![Eksempel post 4](media/log-analytics-data-collector-api/record-04.png)


## <a name="data-limits"></a>Begrænsninger for data
Der findes nogle begrænsninger rundt om de data, der er sendt til samlingen Log Analytics-Data API.

- Op til 30 MB per indlæg til Log Analytics Data indsamler API. Dette er en grænse for en enkelt meddelelse. Hvis dataene fra en enkelt lægge, der indeholder mere end 30 MB, skal du opdele data op i mindre størrelse, dele og sende dem samtidigt. 
- Maksimalt 32 KB grænsen for feltværdier. Hvis værdien i feltet er større end 32 KB, afkortes dataene. 
- Anbefalede maksimale antal felter for en bestemt type er 50. Dette er en praktisk grænse fra en brugervenligheden og søgning oplevelse perspektiv.  


## <a name="return-codes"></a>For returnering

HTTP-statuskode 202 betyder, at anmodningen er blevet accepteret til behandling, men behandling er endnu ikke færdig. Dette angiver, at handlingen blev fuldført.

I denne tabel vises det komplette sæt af statuskoder, der kan returnere tjenesten:

| Kode | Status | Fejlkode | Beskrivelse |
|:--|:--|:--|:--|
| 202 | Accepteret |  | Anmodningen blev accepteret. |
| 400 | Forkert anmodning | InactiveCustomer | Arbejdsområdet er blevet lukket. |
| 400 | Forkert anmodning | InvalidApiVersion | Den API-version, du har angivet blev ikke genkendt af tjenesten. |
| 400 | Forkert anmodning | InvalidCustomerId | Det angivne arbejdsområde-ID er ugyldig. |
| 400 | Forkert anmodning | InvalidDataFormat | Ugyldige JSON blev sendt. Svar brødteksten kan indeholde flere oplysninger om at løse fejlen. |
| 400 | Forkert anmodning | InvalidLogType | Typen log angivet indeholdte specialtegn eller tal. |
| 400 | Forkert anmodning | MissingApiVersion | Blev ikke angivet API-version. |
| 400 | Forkert anmodning | MissingContentType | Indholdstypen blev ikke angivet. |
| 400 | Forkert anmodning | MissingLogType | Log påkrævet værditype blev ikke angivet. |
| 400 | Forkert anmodning | UnsupportedContentType | Den indholdstype er ikke angivet til **programmet/json**. |
| 403 | Forbudt | InvalidAuthorization | Tjenesten ikke til at godkende anmodningen. Kontrollér, at nøglen arbejdsområde-ID og forbindelse er gyldige. |
| 500 | Intern serverfejl | UnspecifiedError | Der opstod en intern fejl i tjenesten. Prøv anmodningen. |
| 503 | Tjenesten er ikke tilgængelig | ServiceUnavailable | Tjenesten er i øjeblikket ikke tilgængelig til at modtage anmodninger. Prøv din anmodning. |

## <a name="query-data"></a>Forespørgselsdata

Forespørge data, der blev sendt af Log Analytics HTTP Data indsamler API, skal du søge efter poster med **Type** , der er lig med værdien **LogType** , du har angivet, tilføjede med **_CL**. Eksempelvis hvis du har brugt **MyCustomLog**, derefter du vil returnere alle poster med **Type = MyCustomLog_CL**.


## <a name="sample-requests"></a>Eksempel anmodninger

I næste afsnit finder du eksempler på, hvordan du kan sende data til Log Analytics HTTP Data indsamler API ved hjælp af forskellige programmeringssprog.

Udføre disse trin for at angive variablerne for overskriften tilladelse for hver prøve:

1. Vælg feltet **Indstillinger** i portalen handlinger Management-pakken, og derefter vælge fanen **Forbindelse kilder** .
2. Vælg ikonet kopi til højre for **Arbejdsområde-ID**, og derefter indsætte-ID, som værdien af variablen **Kunde-ID** .
3. Vælg ikonet kopi til højre for **Primærnøgle**, og derefter indsætte-ID, som værdien af variablen **Delt nøgle** .

Du kan også ændre variabler for logtype og JSON data.

### <a name="powershell-sample"></a>Eksempel på PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# eksempel

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Python eksempel

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Næste trin

- Bruge [Visningen Designer](log-analytics-view-designer.md) til at oprette brugerdefinerede visninger på de data, du sender.
