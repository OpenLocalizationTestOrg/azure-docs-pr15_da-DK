<properties 
    pageTitle="Oprette forbindelse til Media Services konto ved hjælp af REST-API | Microsoft Azure" 
    description="Dette emne beskrives, hvordan du opretter forbindelse til Media Services uisng REST-API." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Oprette forbindelse til Media Services konto ved hjælp af Media Services REST-API

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [RESTEN](media-services-rest-connect-programmatically.md)

Dette emne beskrives, hvordan du får en programmeringsmæssig forbindelse til Microsoft Azure Media Services, når du programming med Media Services REST-API.

To ting er påkrævet, når du åbner Microsoft Azure Media Services: et adgangstoken, der leveres af Azure Access Services ACS (Control), og URI af Media Services sig selv. Du kan bruge en hvilken som helst betyder, at du vil, når du opretter forespørgslerne, så længe du angive de korrekte sidehoved værdier og videregive i adgangstoken korrekt når du ringer til Media Services.

I følgende trin beskrives de mest almindelige arbejdsprocessen, når du bruger Media Services REST-API til at oprette forbindelse til Media Services:

1. Få et adgangstoken 
2. Oprette forbindelse til Media Services URI 

    >[AZURE.NOTE] Når du har oprettet forbindelse til https://media.windows.net, får du en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI.
Du kan også få et HTTP/1.1 200 svar, der indeholder beskrivelsen af ODATA-API metadata.

3. Sende dine efterfølgende API-opkald til den nye URL-adresse. 

    Eksempelvis hvis når du forsøger at oprette forbindelse, har du følgende:

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Du skal sende dine efterfølgende API-opkald til https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Adresse til Access-kontrolelement

Media Services access kontrolelement adresse er https://wamsprodglobal001acs.accesscontrol.windows.net, med undtagelse af North Kina område, hvor det er https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>Få et adgangstoken

Hente et adgangstoken fra ACS for at få adgang til Media Services direkte via REST-API, og brug den under hver HTTP-anmodning, du foretager i tjenesten. Dette token ligner andre tokens, der leveres af ACS baseret på access krav, der er angivet på overskriften for en HTTP-anmodning og ved hjælp af OAuth v2-protokollen. Du behøver ikke andre forudsætninger før direkte forbindelse til Media Services.

I følgende eksempel viser HTTP-anmodning om brevhovedet og meddelelsesteksten, der bruges til at hente et token.

**Sidehoved**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Brødtekst**:

Du har brug at vise client_id og client_secret værdierne i brødteksten i denne anmodning client_id og client_secret, der svarer til kontonavn og AccountKey værdierne henholdsvis. Disse værdier leveres til dig af Media Services, når du konfigurerer din konto. 

Bemærk, at AccountKey for kontoen Media Services skal URL-kodet (se [Procent-kodning](http://tools.ietf.org/html/rfc3986#section-2.1) , når du bruger den som værdien client_secret i access-token anmodning.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Eksempel: 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


I følgende eksempel viser HTTP-svaret, der indeholder adgangen token i brødteksten svar.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Det anbefales at cache "access_token" og "expires_in" værdier til en ekstern storage. Datatypen sikkerhedstoken kunne senere hentet fra opbevaring og igen bruges i dine Media Services REST-API-opkald. Dette er særligt nyttigt for scenarier, hvor tokenet kan sikkert deles af flere processer eller computere.

Sørg for at overvåge "expires_in" værdien af adgangstoken og opdatere dine REST-API-opkald med nye tokens, efter behov.

###<a name="connecting-to-the-media-services-uri"></a>Oprette forbindelse til Media Services URI

I roden URI-adressen for Media Services er https://media.windows.net/. Du skal først oprette forbindelse til denne URI, og hvis du får en 301 Omdiriger tilbage i svar, skal du foretage efterfølgende opkald til den nye URI. Desuden skal ikke bruge en hvilken som helst automatisk-Omdiriger/opfølgning-logik i dine anmodninger. HTTP-verber og anmodning om organisationer kan ikke videresendes til den nye URI.

Bemærk, at i roden URI til overførsel og hente filer til aktiv https://yourstorageaccount.blob.core.windows.net/ hvor kontonavn lager er det samme, som du brugte under konfigurationen af Media Services-konto.

I følgende eksempel demonstrerer HTTP-anmodning i Media Services roden URI (https://media.windows.net/). Anmodningen får en 301 Omdiriger tilbage i svaret. Efterfølgende anmodninger bruger den nye URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Http-anmodning**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**HTTP-svar**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Http-anmodning** (ved hjælp af den nye URI):
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Når du får den nye URI, det er den URI, der skal bruges til at kommunikere med Media Services. 


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
