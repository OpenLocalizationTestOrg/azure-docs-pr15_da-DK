<properties
    pageTitle="Føje handlingen HTTP i logik apps | Microsoft Azure"
    description="Oversigt over handlingen HTTP med egenskaber"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Komme i gang med handlingen HTTP

Du kan udvide arbejdsprocesser for din organisation og kommunikere den til en hvilken som helst slutpunkt via HTTP med handlingen HTTP.

Du kan:

- Opret logik app arbejdsprocesser, der aktiverer (udløser), når et websted, som du administrerer går ned.
- Kommunikere til en hvilken som helst slutpunkt via HTTP til at udvide dine arbejdsprocesser til andre tjenester.

For at komme i gang ved hjælp af handlingen HTTP i en logik app skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Brug HTTP udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen, der er defineret i en logik app. [Få mere at vide om udløsere](connectors-overview.md).

Her er et eksempel sekvens af, hvordan du konfigurerer HTTP udløser i logik App Designer.

1. Tilføje HTTP udløser i din logik app.
2. Udfyld parametrene for HTTP slutpunktet, du vil afstemning.
3. Ændre gentagelsesinterval på hvor ofte den skal afstemning.
4. Appen logik udløses nu med det indhold, der returneres under hvert afkrydsningsfelt.

![HTTP-udløser](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Hvordan fungerer HTTP udløser

HTTP-udløser foretager et opkald til et HTTP-slutpunkt på en tilbagevendende interval. Som standard kode en HTTP-svar mindre end 300 resultaterne i en logik app køre. Du kan tilføje en betingelse i kodevisning, som skal evaluere efter HTTP opkaldet til at bestemme, hvis appen logik skal aktiveres. Her er et eksempel på en HTTP-udløser, der udløses, når den returnerede statuskode er større end eller lig med `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Få flere detaljer om parametrene HTTP udløser er tilgængelige på [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Bruge handlingen HTTP

En handling er en handling, der udføres af arbejdsprocessen, der er defineret i en logik app. [Lær mere om handlinger](connectors-overview.md).

1. Vælg knappen **Nyt trin** .
2. Vælg **Tilføj en handling**.
3. Skriv **http** for at få vist handlingen HTTP i søgefeltet handling.

    ![Vælg handlingen, HTTP](./media/connectors-native-http/using-action-1.png)

4. Tilføj eventuelle parametre, der kræves til HTTP opkaldet.

    ![Udføre handlingen HTTP](./media/connectors-native-http/using-action-2.png)

5. Klik på det øverste venstre hjørne af værktøjslinjen for at gemme. Din logik app kan både gemme og publicere (aktivere).

## <a name="http-trigger"></a>HTTP-udløser

Her er nogle oplysninger for den udløser, der understøtter denne forbindelse. HTTP-forbindelsen har en udløser.

|Udløser|Beskrivelse|
|---|---|
|HTTP|Gør en HTTP-opkald, og returnerer svar indholdet.|

## <a name="http-action"></a>HTTP-handling

Her er nogle oplysninger til den handling, der understøtter denne forbindelse. HTTP-forbindelsen har en mulig handling.

|Handling|Beskrivelse|
|---|---|
|HTTP|Gør en HTTP-opkald, og returnerer svar indholdet.|

## <a name="http-details"></a>HTTP-detaljer

I følgende tabel beskrives de krævede og valgfrie input felter til handlingen og de tilsvarende output detaljer, som er knyttet til ved hjælp af handlingen.


#### <a name="http-request"></a>HTTP-anmodning
Følgende er input felter for den handling, som gør en udgående HTTP-anmodning.
A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Metode *|metode|HTTP-verbum til brug|
|URI *|URI|URI for HTTP-anmodningen|
|Sidehoveder|sidehoveder|Et JSON objekt HTTP-headere medtage|
|Brødteksten|brødteksten|HTTP-anmodningsteksten|
|Godkendelse|godkendelse|Oplysninger i afsnittet [godkendelse](#authentication)|
<br>

#### <a name="output-details"></a>Output detaljer

Følgende er nogle output oplysninger til HTTP-svaret.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Sidehoveder|objekt|Svar sidehoveder|
|Brødteksten|objekt|Response-objektet|
|Statuskode|heltal|HTTP-statuskode|

## <a name="authentication"></a>Godkendelse

Funktionen logik Apps i Azure App Service kan du bruge forskellige typer godkendelse mod HTTP slutpunkter. Du kan bruge denne godkendelse med **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**og **[HTTP Webhook](./connectors-native-webhook.md)** forbindelser. Følgende typer af godkendelse kan konfigureres:

* [Grundlæggende godkendelse](#basic-authentication)
* [Klientcertifikat](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth-godkendelse](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Grundlæggende godkendelse

Objektet følgende godkendelse er nødvendig for basisgodkendelse.
A * betyder, at det er et obligatorisk felt.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Skriv *|type|Godkendelsestype (skal være `Basic` til basisgodkendelse)|
|Brugernavn *|brugernavn|Brugernavn for at godkende|
|Adgangskode *|adgangskode|Adgangskode til at godkende|

>[AZURE.TIP] Hvis du vil bruge en adgangskode, du ikke kan hentes fra definitionen, Brug en `securestring` parameter og `@parameters()` [arbejdsproces definition funktionen](http://aka.ms/logicappdocs).

Så vil du oprette et objekt som dette i feltet godkendelse:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Klientcertifikat

Objektet følgende godkendelse er nødvendig for klientcertifikat. A * betyder, at det er et obligatorisk felt.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Skriv *|type|Typen godkendelse (skal være `ClientCertificate` for klient-SSL-certifikater)|
|PFX *|PFX|Base64-kodet indholdet af personlige oplysninger Exchange (PFX)-fil|
|Adgangskode *|adgangskode|Feltet adgangskode til at få adgang til PFX-fil|

>[AZURE.TIP] Du kan bruge en `securestring` parameter og `@parameters()` [arbejdsproces definition funktionen](http://aka.ms/logicappdocs) til at bruge en parameter, der ikke kan læses i definitionen når du har gemt logik app.

Eksempel:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth-godkendelse

Objektet følgende godkendelse er nødvendig for Azure AD OAuth-godkendelse. A * betyder, at det er et obligatorisk felt.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Skriv *|type|Typen godkendelse (skal være `ActiveDirectoryOAuth` til Azure AD OAuth)|
|Lejer *|lejer|Lejer id for Azure AD-lejer|
|Målgruppe *|målgruppe|Angiv til`https://management.core.windows.net/`|
|Klient -ID *|clientId|Klient-id for Azure AD-program|
|Hemmeligt *|hemmeligt|Hemmeligheden bag af den klient, der anmoder om tokenet|

>[AZURE.TIP] Du kan bruge en `securestring` parameter og `@parameters()` [arbejdsproces definition funktionen](http://aka.ms/logicappdocs) til at bruge en parameter, der ikke kan læses i definitionen når den er gemt.

Eksempel:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Næste trin

Prøv nu den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Du kan udforske andre tilgængelige forbindelserne i logik Apps ved at kigge på vores [API'er liste](apis-list.md).
