<properties 
    pageTitle="Introduktion til levering af indhold efter behov ved hjælp af REST | Microsoft Azure" 
    description="Dette selvstudium vejleder dig gennem trinnene til at implementere en on demand-program, levering af indhold med Azure Media Services ved hjælp af REST-API." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introduktion til levering af indhold efter behov ved hjælp af REST 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> For at fuldføre dette selvstudium skal have du en Azure konto. Yderligere oplysninger finder du [Azure gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F). 


Denne Hurtig start vejleder dig gennem trinnene til implementering af et Video-on-Demand (VoD) levering af indhold-program ved hjælp af Azure Media Services (AMS) REST API'er. 

Selvstudiet introducerer grundlæggende Media Services arbejdsprocessen og de mest almindelige programmering objekter og opgaver, der kræves til udvikling af Media Services. Ved afslutningen af selvstudiet, vil du kunne streame eller gradvist hente en eksempelfil medier, som du har overført, kodet og hentet.  

## <a name="prerequisites"></a>Forudsætninger
De følgende forudsætninger er nødvendige for at starte udvikling med Media Services med REST API'er.

- Overblik over, hvordan du udvikler med Media Services REST-API. Se [media-tjenester-resten-oversigt](http://msdn.microsoft.com/library/azure/hh973616.aspx)kan finde flere oplysninger.
- Et program efter eget valg, der kan sende HTTP-anmodninger om og svar. Dette selvstudium bruger [Fiddler](http://www.telerik.com/download/fiddler). 

Følgende opgaver er vist i denne Hurtig start.

1.  Oprette en Media Services-konto (ved hjælp af portalen Azure).
2.  Konfigurer streaming slutpunkt (ved hjælp af portalen Azure).
1.  Oprette forbindelse til kontoen Media Services med REST-API.
1.  Oprette et nyt aktiv, og Overfør en videofil med REST-API.
1.  Konfigurere streaming enheder med REST-API.
2.  Kod kildefilen til et sæt af tilpassede bithastighed MP4-filer med REST-API.
1.  Udgive aktiv og få streaming og løbende overførsel URL-adresser med REST-API. 
1.  Afspil dit indhold. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Oprette en Azure Media Services-konto ved hjælp af portalen Azure

Trinnene i dette afsnit viser, hvordan du opretter en AMS-konto.

1. Log på på [Azure-portalen](https://portal.azure.com/).
2. Klik på **+ Ny** > **Media + CDN** > **Media Services**.

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Angiv obligatoriske værdier i **Opret MEDIA SERVICES konto** .

    ![Oprette Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Skriv navnet på den nye AMS-konto i **Kontonavn**. Et kontonavn Media Services er alle små bogstaver tal eller bogstaver, som ikke indeholder mellemrum, og er 3 til 24 tegn.
    2. Vælg mellem de forskellige Azure abonnementer, du har adgang til-abonnement.
    
    2. Vælg den nye eller eksisterende ressource i **Ressourcegruppe**.  En ressourcegruppe er en samling af ressourcer, der deler livscyklus, tilladelser og politikker. Få mere at vide [her](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. På den **placering**bruges Vælg geografisk område til at gemme de medie og metadata poster til kontoen Media Services. Dette område bruges til at behandle og streame medier. Kun de tilgængelige Media Services områder vises på rullelisten. 
    
    3. I **Lagerplads konto**, skal du vælge en lagerplads konto til at levere blob-lager af medieindhold fra kontoen Media Services. Du kan vælge en eksisterende lagerplads konto i det samme geografiske område som kontoen Media Services, eller du kan oprette en lagerplads konto. En ny lagerplads konto er oprettet i samme region. Regler for lagerplads kontonavne er den samme som for Media Services konti.

        Lær mere om lagerplads [her](storage-introduction.md).

    4. Vælg **Fastgør til dashboard** for at se status for konto-installationen.
    
7. Klik på **Opret** nederst i formularen.

    Når kontoen er blevet oprettet, ændres status til at **køre**. 

    ![Indstillinger for Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Til at administrere kontoen AMS (for eksempel overføre videoer, kode aktiver, overvåge statussen job) bruge vinduet **Indstillinger** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Konfigurere streaming slutpunkter ved hjælp af portalen Azure

Når du arbejder med Azure Media Services en af de mest almindelige scenarier levere video via tilpasset bithastighed streaming på dine kunder. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere).

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4-kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) just-in-time, uden at du gemme allerede pakkede versioner af hver af disse streaming formater.

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- Kod filen mezzanine (kilde) i et sæt tilpasset bithastighed MP4-filer (kodning trinnene vises senere i dette selvstudium).  
- Oprette mindst én streaming enhed til den *streaming slutpunkt* , som du planlægger at levering af dit indhold. Nedenstående trin viser, hvordan du kan ændre antallet af streaming enheder.

Med dynamisk emballagen, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient.

For at oprette og ændre antallet af streaming reserveret enheder skal du gøre følgende:


1. Klik på **Streaming slutpunkter**i vinduet **Indstillinger** . 

2. Klik på standard streaming slutpunkt. 

    Vinduet **Standard STREAMING SLUTPUNKT oplysninger** vises.

3. Skubbe skyderen **Streaming enheder** for at angive antallet streaming enheder.

    ![Streaming enheder](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Klik på knappen **Gem** for at gemme ændringerne.

    >[AZURE.NOTE]Tildeling af en hvilken som helst nye enheder kan tage op til 20 minutter at gennemføre.

## <a id="connect"></a>Oprette forbindelse til kontoen Media Services med REST API'ER

To ting er påkrævet, når du åbner Azure Media Services: et adgangstoken, der leveres af Azure Access Services ACS (Control), og URI af Media Services sig selv. Du kan bruge en hvilken som helst betyder, at du vil, når du opretter forespørgslerne, så længe du angive de korrekte sidehoved værdier og videregive i adgangstoken korrekt når du ringer til Media Services.

I følgende trin beskrives de mest almindelige arbejdsprocessen, når du bruger Media Services REST-API til at oprette forbindelse til Media Services:

1. Få et adgangstoken. 
2. Oprette forbindelse til Media Services URI.  

    Husk, at når du har oprettet forbindelse til https://media.windows.net, får du vist en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI. Du kan også få et HTTP/1.1 200 svar, der indeholder beskrivelsen af ODATA-API metadata.
3. Bogføring dine efterfølgende API-opkald til den nye URL-adresse. 
    
    Eksempelvis hvis når du forsøger at oprette forbindelse, har du følgende:
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Du skal sende dine efterfølgende API-opkald til https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Få et adgangstoken

Hente et adgangstoken fra ACS for at få adgang til Media Services direkte via REST-API, og brug den under hver HTTP-anmodning, du foretager i tjenesten. Du behøver ikke andre forudsætninger før direkte forbindelse til Media Services.

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

Du skal sig client_id og client_secret værdierne i brødteksten i denne anmodning client_id og client_secret, der svarer til kontonavn og AccountKey værdierne henholdsvis. Disse værdier leveres til dig af Media Services, når du konfigurerer din konto. 

AccountKey for kontoen Media Services skal være URL-kodet, når du bruger den som værdien client_secret i access-token anmodning.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Det anbefales at cache "access_token" og "expires_in" værdier til en ekstern storage. Datatypen sikkerhedstoken kunne senere hentet fra opbevaring og igen bruges i dine Media Services REST-API-opkald. Dette er særligt nyttigt for scenarier, hvor tokenet kan sikkert deles af flere processer eller computere.

Sørg for at overvåge "expires_in" værdien af adgangstoken og opdatere dine REST-API-opkald med nye tokens, efter behov.

###<a name="connecting-to-the-media-services-uri"></a>Oprette forbindelse til Media Services URI

I roden URI-adressen for Media Services er https://media.windows.net/. Du skal først oprette forbindelse til denne URI, og hvis du får en 301 Omdiriger tilbage i svar, skal du foretage efterfølgende opkald til den nye URI. Desuden skal ikke bruge en hvilken som helst automatisk-Omdiriger/opfølgning-logik i dine anmodninger. HTTP-verber og anmodning om organisationer kan ikke videresendes til den nye URI.

I roden URI til overførsel og hentning af aktiver filer er https://yourstorageaccount.blob.core.windows.net/ hvor kontonavn lager er det samme, som du brugte under konfigurationen af Media Services-konto.

I følgende eksempel demonstrerer HTTP-anmodning i Media Services roden URI (https://media.windows.net/). Anmodningen får en 301 Omdiriger tilbage i svaret. Efterfølgende anmodninger bruger den nye URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Http-anmodning**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] Fra nu af bruges den nye URI i dette selvstudium.

## <a id="upload"></a>Oprette et nyt aktiv, og Overfør en videofil med REST API'ER

I Media Services, kan du overføre dine digitale filer til et aktiv. Objektet **aktiv** kan indeholde video, lyd, billeder, miniaturebilleder af websteder, tekst spor og undertekster filer (og metadata om filerne.)  Når filerne er overført til aktivet, gemmes dit indhold sikkert i skyen til yderligere behandling og streaming. 

En af de værdier, du skal angive, når du opretter et aktiv er aktiv oprettelse af indstillinger. Egenskaben **Indstillinger** er en optællingsværdi, der beskriver de krypteringsindstillinger, der kan oprettes et aktiv med. En gyldig værdi er en af værdierne på listen herunder ikke en kombination af værdier fra denne liste:

 
- **Ingen** = **0** - kryptering ikke bruges. Når du bruger denne indstilling, hvis er indholdet ikke beskyttet undervejs eller i resten i lagerplads.
    Hvis du planlægger at levere en MP4 ved hjælp af løbende overførsel Brug denne indstilling. 
- **StorageEncrypted** = **1** - krypterer Ryd indholdet lokalt ved hjælp af AES 256 bit kryptering og overfører det til Azure lager, hvor det er gemt, krypteret på resten. Aktiver, der er beskyttet med lagerplads kryptering er automatisk uden kryptering og placeret i et krypteret filsystem før kodning, og du kan også igen krypteret før uploade tilbage som et nyt output aktiv. Den primære use case for lagerplads kryptering er, når du vil sikre høj kvalitet input mediefilerne med stærk kryptering på resten på disk.
- **CommonEncryptionProtected** = **2** – Brug denne indstilling, hvis du sender indhold, der allerede er krypteret og beskyttet med almindelige kryptering eller PlayReady DRM (for eksempel udglattede Streaming beskyttet med PlayReady DRM).
- **EnvelopeEncryptionProtected** = **4** – Brug denne indstilling, hvis du sender HLS, der er krypteret med AES. Filerne er skal kodet og krypteret af transformere Manager.

### <a name="create-an-asset"></a>Oprette et aktiv

Et aktiv er en beholder for flere typer eller sæt af objekter i Media Services, herunder video, lyd, billeder, miniaturebilleder af websteder, tekstspor og undertekster filer. I REST-API kræver oprettelse af et aktiv sende POST-anmodning til Media Services og placere en hvilken som helst egenskabsoplysninger om din aktiv i anmodningsteksten.

I følgende eksempel viser, hvordan du opretter et aktiv.

**HTTP-anmodning**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**HTTP-svar**

Hvis det lykkes, returneres følgende:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Oprette en AssetFile

Objektet [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) repræsenterer en video eller lyd fil, der er gemt i en blob beholder. En aktiv-fil er altid knyttet til et aktiv, og et aktiv kan indeholde et eller flere AssetFiles. Den Media Services Encoder mislykkes, hvis et aktiv filobjekt ikke er knyttet til en digital fil i en blob beholder.

Når du har overført filen digitale medier til en blob beholder, kan du bruge **fletning** HTTP-anmodningen til at opdatere AssetFile med oplysninger om din mediefil (som vist senere i emnet). 

**HTTP-anmodning**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Oprette AccessPolicy med skrivetilladelse. 

Før du overfører filer til blob-lager, angive adgang politik rettigheder til at skrive på et aktiv. SENDE en HTTP-anmodning til de AccessPolicies enhed for at gøre dette. Definere en DurationInMinutes værdi efter oprettelse, eller du får vist fejlmeddelelsen 500 Intern Server tilbage i svaret. Du kan finde flere oplysninger om AccessPolicies, [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

I følgende eksempel viser, hvordan du opretter en AccessPolicy:
        
**HTTP-anmodning**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP-svar**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Få Overfør URL-adressen

Oprette en SAS Locator for at hente den faktiske Overfør URL-adresse. Locators definere starttidspunkt og type forbindelse slutpunkt for klienter, der vil have adgang til filer i et aktiv. Du kan oprette flere Locator enheder for en given AccessPolicy og aktiv par til at håndtere anmodninger om anden klient og behov. Hver af disse Locators bruger værdien starttidspunkt plus DurationInMinutes værdien af AccessPolicy til at finde ud af, hvor lang tid, der kan bruges en URL-adresse. Du kan finde flere oplysninger [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


En SAS URL-adresse har følgende format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Nogle overvejelser gælder:

- Du kan ikke have mere end fem entydige Locators, der er knyttet til en given aktiv ad gangen. Du kan finde flere oplysninger Locator.
- Hvis du har brug at overføre dine filer med det samme, skal du indstille din starttidspunkt til fem minutter, inden det aktuelle klokkeslæt. Dette skyldes, at der kan være ur skævhed mellem din klient og Media Services. Din starttidspunkt skal også være i den følgende DateTime-format: åååå-MM-DDTHH:mm:ssZ (for eksempel "2014-05-23T17:53:50Z").   
- Der kan være lidt 30-40 forsinket, efter en Locator oprettes til, når den er tilgængelig til brug. Dette problem gælder for både SAS URL-adresse og Origin Locators.

I følgende eksempel viser, hvordan du opretter en SAS URL-adressen Locator, som defineret af egenskaben Type i anmodningsteksten ("1" for en SAS locator) og "2" for en On Demand origin locator. Egenskaben **Path** returneres indeholder URL-adressen, som du skal bruge til at overføre din fil.
    
**HTTP-anmodning**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-svar**

Hvis det lykkes, returneres følgende svaret:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Overføre en fil til en objektbeholder til lagring af blob
    
Når du har AccessPolicy og angive Locator, er faktisk filen overført til en Azure Blob-lager objektbeholder ved hjælp af Azure lagerplads REST API'er. Du kan overføre side eller blokere BLOB. 

>[AZURE.NOTE] Du skal føje filnavnet på den fil, du vil overføre til den **sti** værdi, der er modtaget i det forrige afsnit. For eksempel https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Du kan finde flere oplysninger om at arbejde med Azure lagerplads BLOB se [Blob Service REST-API](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Opdatere AssetFile 

Nu, hvor du har overført din fil, skal du opdatere oplysningerne om FileAsset størrelse (og andre). Eksempel:
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

Hvis lykkes, følgende returneres: HTTP/1.1 204 intet indhold

## <a name="delete-the-locator-and-accesspolicy"></a>Slette Locator og AccessPolicy 

**HTTP-anmodning**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**HTTP-svar**

Hvis det lykkes, returneres følgende:

    HTTP/1.1 204 No Content 
    ...

**HTTP-anmodning**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-svar**

Hvis det lykkes, returneres følgende:

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Konfigurere streaming enheder med REST-API

Når du arbejder med Azure Media Services en af de mest almindelige scenarier leverer tilpasset bithastighed streaming til dine kunder. Klienten kan med tilpasset bithastighed streaming skifte til en højere eller lavere bithastighed stream, som videoen vises baseret på den aktuelle netværksbåndbredde, CPU-forbrug og andre faktorer. Media Services understøtter følgende tilpasset bithastigheden streaming teknologier: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere). 

Media Services indeholder dynamiske emballagen, som du kan bruge til at fremvise din tilpassede bithastighed MP4 eller udglattet Streaming kodet indhold i streaming formater, der understøttes af Media Services (MPEG TANKESTREG, HLS, bløde Streaming, HD'ER) uden at du behøver at pakke til disse streaming formater. 

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- få mindst én streaming enhed for den **streaming slutpunkt **, som du planlægger at levere dit indhold (beskrevet i dette afsnit).
- kode eller kode et din mezzanine (kilde) fil i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer (kodning trinnene vises senere i dette selvstudium),  

Med dynamisk emballagen, du skal kun bruge til at gemme og betale for filer i enkelt lagerplads format og Media Services opbygger og fungerer relevante svaret baseret på anmodninger fra en klient. 


>[AZURE.NOTE] Du kan finde oplysninger om priser detaljer [Media Services priser detaljer](http://go.microsoft.com/fwlink/?LinkId=275107).

Hvis du vil ændre antallet streaming reserveret enheder, skal du gøre følgende:
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Få det streaming slutpunkt, du vil opdatere

For eksempel Lad os få det første streaming slutpunkt i din konto (du kan have op til to streaming slutpunkter i kører tilstand ad gangen).

**Http-anmodning**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-svar**
    
Hvis det lykkes, returneres følgende:
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>Skalere streaming slutpunktet
 
**Http-anmodning**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**HTTP-svar**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Se, om status for en længerevarende handling

Tildeling af en hvilken som helst nye enheder tager omkring 20 minutter for at gennemføre. Kontrollere status for handlingen Brug metoden **Handlinger** , og Angiv id'et for handlingen. Handlingen-Id blev returneret som svar på den **skala** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Http-anmodning**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**HTTP-svar**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Kode kildefilen i et sæt tilpasset bithastighed MP4 filer

Efter ingesting aktiver i Media Services, medier kan kodes, transmuxed, vandmærke, og så videre, før den er leveret til klienter. Disse aktiviteter er planlagt og kører i forhold til flere forekomster af baggrunden rolle at sikre høj ydeevne og tilgængelighed. Disse aktiviteter kaldes job og hvert [Job](http://msdn.microsoft.com/library/azure/hh974289.aspx) består af atomisk opgaver, der gør det faktiske arbejde på filen aktiv. 

Som tidligere nævnt, når du arbejder med Azure Media Services, en af de mest almindelige scenarier leverer tilpasset bithastighed streaming til dine kunder. Media Services kan dynamisk pakke en række tilpasset bithastighed MP4 filer til et af følgende formater: HTTP Live Streaming (HLS), bløde Streaming, MPEG TANKESTREG og HD'ER (for kun Adobe PrimeTime/adgang til indehavere). 

For at kunne udnytte dynamiske emballagen, skal du gøre følgende:

- kode eller kode et din mezzanine (kilde) fil i et sæt af tilpassede bithastighed MP4 filer eller tilpasset bithastighed udglattede Streaming filer  
- få mindst én streaming enhed for streaming slutpunktet, som du planlægger at levere dit indhold. 

Følgende afsnit viser, hvordan du opretter en sag, der indeholder en kodning opgave. Opgaven, der angiver kode et mezzanine-fil i et sæt af tilpassede bithastighed MP4s ved hjælp af **Media Encoder Standard**. Sektionen viser også, hvordan til at overvåge jobbet behandling opgavefremdrift. Når jobbet er fuldført, vil du kunne oprette locators, der er nødvendige for at få adgang til dine aktiver. 

### <a name="get-a-media-processor"></a>Få en medier processor

En processor medier er en komponent, der håndterer en bestemt behandlingsopgave, som kodning, konvertering af filformat, kryptering eller dekryptering medieindhold i Media Services. Vi vil bruge Media Encoder Standard til kodning opgaven vises i dette selvstudium.

Følgende kode, som den encoder-id. 

**HTTP-anmodning**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Oprette en sag

Hvert Job kan have en eller flere opgaver afhængigt af typen behandling, du vil udføre. Gennem REST-API, kan du oprette job og deres relaterede opgaver på en af to måder: opgaver kan være defineret indbygget via egenskaben opgaver navigation på jobbet objekter eller via OData batchbehandling. Media Services SDK benytter batchbehandling. Opgaver er dog defineret indbygget for læsbarheden af kodeeksempler i dette emne. Finde oplysninger om batchbehandling, se [Open Data Protocol (OData) batchbehandling](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

I følgende eksempel viser, hvordan til at oprette og sende en sag med en opgave, der er angivet til at kode en video på en bestemt opløsning og -kvalitet. Følgende dokumentation afsnit indeholder liste over alle [opgave forudindstillinger](http://msdn.microsoft.com/library/mt269960) understøttes af Media Encoder almindelige processor.  

**HTTP-anmodning**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-svar**

Hvis det lykkes, returneres følgende svaret:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Der er nogle vigtige ting at være opmærksom på enhver Job anmodning:

- TaskBody egenskaber skal bruge ordret XML til at angive antallet input, eller Medtag aktiver, der anvendes af opgaven. Emnet opgave indeholder XML-skemadefinitionen for XML-filen.
- I definitionen TaskBody hver indre værdien af <inputAsset> og <outputAsset> skal være angivet som JobInputAsset(value) eller JobOutputAsset(value).
- En opgave kan have flere output aktiver. En JobOutputAsset(x) kan kun bruges én gang som output fra en opgave i en sag.
- Du kan angive JobInputAsset eller JobOutputAsset som et input aktiv for en opgave.
- Opgaver skal ikke udgør en cyklus.
- Parameteren value, du sender til JobInputAsset eller JobOutputAsset repræsenterer indeksværdien for et aktiv. De faktiske Aktiver er defineret i egenskaberne InputMediaAssets og OutputMediaAssets navigation på jobbet enhed definition. 

>[AZURE.NOTE] Da Media Services er bygget på OData v3, de enkelte aktiver i InputMediaAssets og OutputMediaAssets navigation egenskabssamlinger der henvises til en "__metadata: uri" navn / værdi-par. 

- InputMediaAssets knytter til en eller flere aktiver, du har oprettet i Media Services. OutputMediaAssets er oprettet af systemet. De ikke referere til et eksisterende aktiv.
- OutputMediaAssets kan navngives ved hjælp af attributten assetName. Hvis denne attribut er ikke til stede, og klik derefter på navnet på OutputMediaAsset er uanset tekstværdien af den <outputAsset> element er med et suffiks af værdien jobnavn eller værdien Job-Id (i de tilfælde, hvor egenskaben Name ikke er defineret). Eksempelvis hvis du angiver en værdi for assetName til "Eksempel", derefter OutputMediaAsset navn egenskaben ville være angivet til "Eksempel". Men hvis du ikke har angivet en værdi for assetName, men du angivet jobnavn til "NewJob", derefter navnet på OutputMediaAsset ville være "JobOutputAsset (værdi) _NewJob". 

    I følgende eksempel vises, hvordan du kan indstille egenskaben assetName:
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Sådan aktiverer du sammenkædning af opgave:

    - Et job skal have mindst to opgaver
    - Der skal være mindst én opgave, hvis input er output fra en anden opgave i jobbet.

Flere oplysninger under [oprette en sag kodning med Media Services REST-API](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Overvåge behandling opgavefremdrift

Du kan hente jobstatus ved hjælp af egenskaben State, som vist i følgende eksempel. 

**HTTP-anmodning**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-svar**

Hvis det lykkes, returneres følgende svaret:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Annullere et job

Media Services giver dig mulighed at annullere, der kører job via funktionen CancelJob. Dette opkald returnerer en 400 fejlkode, hvis du forsøger at annullere et Job, når dens tilstand annulleres, hvis du annullerer, fejl eller afsluttet.

I følgende eksempel viser, hvordan til at ringe til CancelJob.


**HTTP-anmodning**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


Hvis det lykkes, returneres en 204 svarkode med ingen meddelelsesteksten.

>[AZURE.NOTE] Du skal URL-kodning job-id (normalt nb:jid:UUID: somevalue) når overføre det i som en parameter til CancelJob.


### <a name="get-the-output-asset"></a>Få output aktiv 

Følgende kode viser, hvordan du anmoder om output aktiv Id. 


**HTTP-anmodning**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Udgive aktiv og få streaming og løbende overførsel URL-adresser med REST API'ER

For at streame eller hente et aktiv, skal du først "udgive" ved at oprette en locator. Locators giver adgang til filer, der findes i aktivet. Media Services understøtter to typer locators: OnDemandOrigin locators, bruges til at streame medier (for eksempel MPEG TANKESTREG, HLS eller udglattet Streaming) og Access signatur (SAS) locators bruges til at hente mediefiler.

Når du opretter locators, kan du oprette de URL-adresser, der bruges til at streame eller hente filerne. 


En streaming URL-adresse til jævn Streaming har følgende format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

En streaming URL-adresse til HLS har følgende format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

En streaming URL-adresse til MPEG TANKESTREG har følgende format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


En SAS URL-adresse, bruges til at hente filer har følgende format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

I dette afsnit viser, hvordan du udføre følgende opgaver skal "publicere" dine aktiver.  

- Oprette AccessPolicy med læsetilladelse 
- Oprette en SAS URL-adresse til at hente indhold 
- Oprette en origin URL-adresse til streaming indhold 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Oprette AccessPolicy med læsetilladelse

Før du henter eller streaming en hvilken som helst medieindhold, først definere en AccessPolicy med læsetilladelser og oprette de relevante Locator enhed, der angiver typen af levering ordning, du vil aktivere til dine kunder. Du kan finde flere oplysninger om de egenskaber, der er tilgængelige, [AccessPolicy objektegenskaber](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

I følgende eksempel viser, hvordan til at angive AccessPolicy for læsetilladelser til en given aktiv.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Hvis det lykkes, returneres en 201 succeskode der beskriver det AccessPolicy objekt, du har oprettet. Derefter kan du bruge AccessPolicy-Id sammen med det aktiv, der indeholder den fil, du vil levere (som et output aktiv) for at oprette objektet Locator aktiv Id.

>[AZURE.NOTE]
Grundlæggende arbejdsprocessen er den samme som overføre en fil, når ingesting et aktiv (som er beskrevet tidligere i dette emne). Desuden som overførsel af filer, hvis du (eller dine klienter) skal have adgang til dine filer med det samme, angive dine starttidspunkt til fem minutter, inden det aktuelle klokkeslæt. Denne handling er nødvendigt, fordi der kan være ur skævhed mellem klienten og Media Services. Starttidspunkt værdien skal være i den følgende DateTime-format: åååå-MM-DDTHH:mm:ssZ (for eksempel "2014-05-23T17:53:50Z").


###<a name="creating-a-sas-url-for-downloading-content"></a>Oprette en SAS URL-adresse til at hente indhold 

Følgende kode viser, hvordan du får en URL-adresse, der kan bruges til at hente en mediefil oprettet og overføres tidligere. AccessPolicy har læst tilladelser skal være angivet og Locator stien refererer til en SAS Hent URL-adresse.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Hvis det lykkes, returneres følgende svaret:

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


Egenskaben returnerede **sti** indeholder SAS URL-adressen.

>[AZURE.NOTE]
Hvis du henter lagerplads krypteret indhold, skal du manuelt dekryptere før gengivelse af det eller brug lagerplads dekryptering MediaProcessor i en behandlingsopgave output behandlede filer i Ryd til en OutputAsset og derefter hente fra aktivet. Se Oprette et kodning Job med Media Services REST-API kan finde flere oplysninger om behandling. Desuden kan SAS URL-adressen Locators ikke opdateres, når de er blevet oprettet. For eksempel kan du genbruge den samme Locator med en opdateret starttidspunkt. Dette er på grund af den måde, som er oprettet SAS URL-adresser. Hvis du vil have adgang til et aktiv til hentning, når en Locator er udløbet, skal du oprette et nyt kort med en ny starttidspunkt.

###<a name="download-files"></a>Hente filer

Når du har AccessPolicy og angive Locator, kan du hente filer ved hjælp af Azure lagerplads REST API'er.  

>[AZURE.NOTE] Du skal føje filnavnet på den fil, du vil hente til værdien **sti** , der er modtaget i det forrige afsnit. For eksempel https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Du kan finde flere oplysninger om at arbejde med Azure lagerplads BLOB se [Blob Service REST-API](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Som et resultat af kodning jobbet, du har udført tidligere (kode til tilpasset MP4 sæt), har du flere MP4-filer, som du kan hente gradvist. Eksempel:    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Oprette en streaming URL-adresse til streaming indhold


Følgende kode viser, hvordan du opretter en streaming Locator URL-adresse:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Hvis det lykkes, returneres følgende svaret:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Hvis du vil streame en jævn Streaming origin URL i en streaming medieafspiller, skal du føje stien egenskaben med navnet på den udglattede Streaming manifestet fil, efterfulgt af "/ manifestet".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Hvis du vil streame HLS, tilføje (format = m3u8 aapl) efter det "/ manifestet".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Hvis du vil streame MPEG TANKESTREG, tilføje (format = mpd-klokkeslæt-csf) efter det "/ manifestet".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Afspille dit indhold  

For at streame video, skal du bruge [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Indsæt en URL-adresse i en browser (for eksempel Internet Explorer, Chrome, Safari) for at teste løbende overførsel.


##<a name="next-steps-media-services-learning-paths"></a>Næste trin: Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Leder du efter noget andet?

Hvis dette emne ikke indeholder hvad du forventede, der mangler noget eller i en anden måde opfylder ikke dine behov, kan du give os din feedback, ved hjælp af den Disqus tråd nedenfor.



