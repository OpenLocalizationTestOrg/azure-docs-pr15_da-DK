<properties
   pageTitle="Hvordan du bruger Power BI integreret med RESTEN | Microsoft Azure"
   description="Lær at bruge Power BI integreret med RESTEN "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Hvordan du bruger Power BI integreret med RESTEN


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI integrerede: Den, og hvad det er til
En oversigt over Power BI integrerede er beskrevet i det officielle [Power BI integrerede websted](https://azure.microsoft.com/services/power-bi-embedded/), men lad os kaste et hurtigt blik, før vi få vist flere detaljer om at bruge det med RESTEN.

Det er helt enkelt virkelig. En ISV ønsker ofte at bruge dynamiske datavisualiseringer af [Power BI](https://powerbi.microsoft.com) i deres egen program som Brugergrænsefladen dokumentkomponenter.

Men, du kender, integrere Power BI-rapporter eller felter til din webside er allerede muligt uden tjenesten Power BI integrerede Azure, ved hjælp af **Power BI API**. Når du vil dele dine rapporter i organisationen samme, kan du integrere rapporterne med Azure AD-godkendelse. Den bruger, der får vist rapporterne, der skal logon, der arbejder med deres egen Azure AD-konto. Når du vil dele dine rapporter for alle brugere (herunder eksterne brugere), kan du blot integrere med anonym adgang.

Men du kan se, denne enkle integrere løsning ikke helt imødekommer behovet hos en ISV-applikation.
De fleste ISV-programmer skal levere dataene til deres egne kunder, ikke nødvendigvis brugere i deres egen organisation. Eksempelvis hvis du levering af nogle service for både firma A og virksomhed B, skal i firma A ses af brugere for deres egen virksomhed A. Det vil sige, er flere arkitekturer nødvendig for leveringen.

Programmet ISV muligvis også tilbyder sin egen godkendelsesmetoder som formularer auth, grundlæggende auth osv … Derefter skal integrering løsningen samarbejde med denne eksisterende godkendelsesmetoder sikkert. Det er også nødvendigt for brugerne at kunne bruge disse ISV-programmer uden ekstra køb eller licensering på et Power BI-abonnement.

 **Power BI integrerede** er udviklet til nøjagtigt følgende typer af ISV scenarier. Så nu hvor vi har den hurtig introduktion væk, Lad os få nogle flere detaljer

Du kan bruge .NET \(C#) eller Node.js SDK, til nemt at opbygge dit program med Power BI integreret. Men i denne artikel gennemgår vi om HTTP flow \(herunder AuthN) af Power BI uden SDK'er. Om dette flow, du kan oprette dit program **med en hvilken som helst programmeringssprog**, og du kan forstå dybt essensen ved Power BI integreret.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Oprette Power BI arbejdsområde indsamling og få hurtigtast \(artikel Provisioning)
Power BI integrerede er et af de Azure tjenester. Kun som bruger Azure Portal ISV betale for Brugsgebyrer \(hver time brugersession), og den bruger, som får rapporten ikke er faktureret eller endda kræver abonnement til Azure.
Før du starter vores udvikling af programmer, skal vi oprette **Power BI arbejdsområde af websteder** ved hjælp af Azure-portalen.

De enkelte arbejdsområder af Power BI integrerede er arbejdsområde for hver kunde (lejer), og vi kan tilføje mange arbejdsområder i hver arbejdsområde af websteder. Den samme adgangsnøgle bruges i hver arbejdsområde af websteder. I kraft, samlingen arbejdsområde er grænsen for Power BI integreret sikkerhed.

![](media\power-bi-embedded-iframe\create-workspace.png)

Når vi afslutte oprettelsen af samlingen arbejdsområde, kan du kopiere hurtigtast fra Azure-portalen.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] Vi kan også Klargør samlingen arbejdsområde og få hurtigtast via REST-API. Se [Power BI ressource udbyder API'er](https://msdn.microsoft.com/library/azure/mt712306.aspx)for at få mere for at vide.

## <a name="create-pbix-file-with-power-bi-desktop"></a>Oprette .pbix fil med Power BI Desktop
Dernæst skal oprette vi dataforbindelse og rapporter der skal integreres.
Til denne opgave er der ingen programmering eller kode. Vi bruge blot Power BI Desktop.
I denne artikel vil vi gennemgå oplysninger om, hvordan du bruger Power BI Desktop. Hvis du har brug for hjælp her, skal du se [Introduktion til Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). I dette eksempel skal bruger vi blot [Detail analyse](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Oprette et Power BI-arbejdsområde

Nu hvor klargøringen sker alt sammen, Lad os komme i gang oprette en kundes arbejdsområde i samlingen arbejdsområde via REST API'er. Følgende HTTP INDLÆG anmode om (RESTEN) oprettelse af det nye arbejdsområde i vores eksisterende arbejdsområde af websteder. I eksemplet er navnet på arbejdsområdet samling **mypbiapp**.
Vi angive blot tasten adgang, som vi tidligere har kopieret, som **AppKey**. Det er meget simpel godkendelse!

**HTTP-anmodning**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-svar**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Den returnerede **workspaceId** bruges til følgende efterfølgende API-opkald. Vores program skal bevare denne værdi.

## <a name="import-pbix-file-into-the-workspace"></a>Importere .pbix-filen til arbejdsområdet
De enkelte arbejdsområder kan være vært for en enkelt Power BI Desktop-fil med et datasæt \(herunder indstillinger for datakilde) og rapporter. Vi kan importere vores .pbix-fil til arbejdsområdet som vist i nedenstående kode. Som du kan se, kan vi overføre binær af .pbix fil ved hjælp af MIME-meddelelse med flere dele i http.

Uri-fragment **32960a09-6366-4208-a8bb-9e0678cdbb9d** er workspaceId, og forespørgsel parameter **datasetDisplayName** er navnet på datasættet for at oprette. Oprettet datasættet indeholder alle data relaterede elementer i .pbix filen, sådan som importerede data, markøren til datakilden, osv.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Denne import opgave kører muligvis et stykke tid. Når du er færdig, kan vores program stille opgavestatus ved hjælp af import-id. I vores eksempel er Importér id'et **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Følgende beder ved hjælp af denne import-id:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Hvis opgaven ikke fuldført, skyldes HTTP-svaret således:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Hvis opgaven er afsluttet, skyldes HTTP-svaret flere således:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Datakilde connectivity \(og flere arkitekturer data)
Mens næsten alle elementer i .pbix fil er importeret til vores arbejdsområde, er legitimationsoplysninger for datakilder ikke. Som et resultat, når du bruger **DirectQuery-tilstand**, kan integrerede rapporten ikke vises korrekt. Men hvis du bruger **Importér tilstand**, kan vi se rapporten ved hjælp af de eksisterende importerede data. I så fald skal vi angive de legitimationsoplysninger, der er at benytte følgende fremgangsmåde via RESTEN opkald.

Først skal vi have gateway datakilden. Vi ved datasæt **id** er det tidligere er blevet returneret-id.

**HTTP-anmodning**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-svar**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Med den returnerede gateway-id og datakilde-id \(se tidligere **gatewayId** og **id** i det returnerede resultat), kan vi ændre legitimationsoplysninger for denne datakildetype, på følgende måde:

**HTTP-anmodning**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP-svar**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Fremstilling, kan vi også angive forskellige forbindelsesstrengen for de enkelte arbejdsområder ved hjælp af REST-API. \(Windows XP, vi kan adskille databasen for hver kunder.)

Følgende ændringer forbindelsesstrengen datakildetype via RESTEN.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Eller, kan vi bruge sikkerhed på brugerniveau række i Power BI integreret, og vi kan adskille data for alle brugere i en rapport. As a Result, vi kan klargøre hver kunde rapport med samme .pbix \(brugergrænseflade, osv.) og forskellige datakilder.

> [AZURE.NOTE] Hvis du bruger **Importér tilstand** i stedet for **DirectQuery-tilstand**, findes der ingen måde at opdatere modeller via API. Og lokale datakilder via Power BI gateway understøttes ikke endnu i Power BI integreret. Men du får virkelig vil holde øje med [Power BI-bloggen](https://powerbi.microsoft.com/blog/) for nyheder, og hvad der kommer i fremtidige versioner.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Godkendelse og hvor (integrering) rapporter er placeret i vores webside

I den forrige REST-API, kan vi bruge hurtigtast **AppKey** sig selv som overskriften godkendelse. Fordi disse opkald kan løses på serversiden backend-version, er det er sikkert.

Men, når vi integrerer rapporten i vores webside, denne slags sikkerhedsoplysninger skal løses ved hjælp af JavaScript \(front end). Derefter skal godkendelse sidehoved værdien være fastgjort. Hvis vores hurtigtast finder ud af en hacker eller skadelig kode, kan de ringe til en hvilken som helst ved hjælp af denne tast handlinger.

Når vi integrere rapporten i vores webside, skal vi bruge beregnede tokenet i stedet for hurtigtast **AppKey**. Vores programmet skal oprette OAuth Json Web Token \(JWT) som består af krav og beregnede digital signatur. Som vist nedenfor, er dette OAuth JWT prik tabulatorsepareret kodet streng tokens.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Først skal vi forberede inputværdi, som er underskrevet senere. Denne værdi er base64 URL-kodet (rfc4648) strengen af de følgende json, og disse er adskilt af prikken \(.) tegn. Vi gennemgår senere, Sådan får du rapport-id'et.

> [AZURE.NOTE] Hvis vi vil bruge række niveau sikkerhed sikkerhed med Power BI integreret, skal vi også angive **brugernavn** og **roller** i krav.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Dernæst skal vi skal oprette base64-kodet streng med HMAC \(signaturen) med SHA256 algoritme. Denne signeret inputværdi er den forrige streng.

Sidste, vi skal kombineres input værdi og signatur strengen ved hjælp af periode \(.) tegn. Den færdige streng er app token for integrering af rapporten. Selvom app tokenet finder ud af en skadelig bruger, får de kan ikke den oprindelige hurtigtast. Denne app token udløber hurtigt.

Her er en PHP eksempel til disse trin:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Til sidst skal integrere rapporten til websiden

Integrering af vores rapport, skal vi få Integrer URL-adressen og rapportere **id** med følgende REST-API.

**HTTP-anmodning**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP-svar**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Vi kan integrere rapporten i vores online ved hjælp af den foregående app-token.
Hvis vi ser på den næste eksempelkode, er den tidligere del den samme som i forrige eksempel. I dette eksempel vises i den sidste del **embedUrl** \(får vist den forrige resultat) i iframe, og opslå app tokenet til iframe.

> [AZURE.NOTE] Du skal ændre værdien rapport-id til et af dine egne. Du kan også på grund af en fejl i vores Indholdsstyring system er mærket iframe i stikprøven kode læse direkte. Fjerne tekst i capped fra mærket, hvis du kopierer og indsætter denne eksempelkode.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Og her er vores resultat:

![](media\power-bi-embedded-iframe\view-report.png)

På nuværende tidspunkt viser Power BI integrerede kun rapporten i iframe. Men holde øje med [Power BI-Blog](). Fremtidige forbedringer kan bruge nye klientsiden API'er, der bliver Lad os sende oplysninger til iframe samt få oplysninger. Spændende ting!


## <a name="see-also"></a>Se også
- [Kontrollere og godkende i Power BI integreret](power-bi-embedded-app-token-flow.md)
