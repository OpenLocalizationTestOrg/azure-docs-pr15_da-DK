<properties 
    pageTitle="Konfigurere aktiv levering politikker ved hjælp af Media Services REST-API | Microsoft Azure" 
    description="Dette emne viser, hvordan du konfigurerer andet aktiv levering politikker ved hjælp af Media Services REST-API." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Konfigurere politikker for levering af aktiver

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Hvis du planlægger at levere dynamisk krypterede aktiver, et af trinnene i arbejdsprocessen for levering af indhold Media Services konfiguration af levering af politikker for aktiver. Politikken aktiv levering fortæller Media Services, hvordan du vil have for din aktiv blev leveret: i hvilke streaming protokol, skal din aktiv dynamisk pakkes (for eksempel, MPEG TANKESTREG, HLS, bløde Streaming eller alle), uanset om du vil kryptere dynamisk din aktiv, og hvordan (konvolut eller almindelige kryptering).

Dette emne beskrives det, hvorfor og hvordan du kan oprette og konfigurere politikker for levering af aktiv.

>[AZURE.NOTE]Hvis du vil kunne bruge dynamiske emballagen og dynamiske kryptering, skal du sikre dig at have mindst én enhed for tidsskala (også kaldet streaming enhed). Du kan finde flere oplysninger, se, [hvordan skalere en medier-tjeneste](media-services-portal-manage-streaming-endpoints.md).
>
>Din aktiv skal også indeholde et sæt af tilpassede bithastighed MP4s eller tilpasset bithastighed udglattede Streaming filer.

Du kan anvende forskellige politikker til det samme aktiv. For eksempel kan du anvende PlayReady kryptering på udglattede Streaming og AES konvolut kryptering MPEG TANKESTREG og HLS. En hvilken som helst protokoller, som ikke er defineret i en politik for levering af (for eksempel du tilføje en enkelt politik, som kun angiver HLS som protokollen) blokeres fra streaming. Undtagelsen er, hvis du har ingen defineret overhovedet aktiv levering politik. Alle protokoller tillades derefter på Ryd.

Hvis du vil afholde et lager krypterede aktiv, skal du konfigurere aktivets levering politik. Før din aktiv streames, streaming serveren fjerner lagerplads kryptering og streamer indholdet ved hjælp af den angivne levering politik. Angiv for eksempel typen for at give dine aktiver, der er krypteret med avancerede kryptering AES (Standard) konvolut krypteringsnøgle til **DynamicEnvelopeEncryption**. For at fjerne lagerplads kryptering og streame aktivet i Ryd skal du angive typen til **NoDynamicEncryption**. Eksempler på, hvordan du konfigurerer disse politiktyper Følg.

Afhængigt af hvordan du konfigurerer politikken aktiv levering vil du ikke kunne dynamisk pakke, dynamisk kryptere og streame følgende streaming-protokoller: jævn Streaming, HLS, MPEG TANKESTREG og HD'ER.

Den nedenstående liste viser formaterne, at du bruger til at streame Udjævn, HLS, bindestreg og HD'ER.

Udjævne Streaming:

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG BINDESTREG

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HD'ER

{streaming slutpunkt navn media services konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Flere oplysninger om, hvordan du publicerer et aktiv og opbygge en streaming URL-adresse, under [oprette en streaming URL-adresse](media-services-deliver-streaming-content.md).


##<a name="considerations"></a>Overvejelser i forbindelse med

- Du kan ikke slette en AssetDeliveryPolicy, der er knyttet til et aktiv, mens en anmodet arbejdsproces (streaming) locator findes for den pågældende aktiv. Det anbefales, at fjerne politikken fra aktivet før du sletter politikken.
- En streaming locator kan ikke oprettes på en krypteret aktiv lagerplads, når ingen aktiv levering politik er indstillet.  Hvis aktivet ikke lagerplads, der er krypteret, vil systemet kan du oprette en locator og streame aktivet i Ryd uden en aktiv levering politik.
- Du kan have flere aktiv levering politikker, som er knyttet til en enkelt aktiv, men du kan kun angive en metode til at håndtere en given AssetDeliveryProtocol.  Hvilket betyder, at hvis du forsøger at sammenkæde to levering politikker, som angiver den AssetDeliveryProtocol.SmoothStreaming protokol, der medfører en fejl, fordi systemet ikke ved, hvilket operativsystem du vil det skal anvendes, når en klient gør en jævn Streaming anmodning.
- Hvis du har et aktiv med en eksisterende streaming locator, kan du sammenkæde en ny politik for aktivet, fjerne sammenkædningen af en eksisterende politik fra aktivet eller opdatere en politik for levering, der er knyttet til aktivet.  Du først skal fjerne streaming locator, justere politikkerne og Opret streaming locator igen.  Du kan bruge den samme locatorId, når du genskabe streaming locator, men du skal sikre dig, der ikke give problemer med for klienter, da indhold kan cachelagres af udgangspunktet eller et efterfølgende CDN.

>[AZURE.NOTE] Når du arbejder med Media Services REST-API, gælder følgende overvejelser:
>
>Når du åbner objekter i Media Services, skal du angive bestemte sidehoved felter og værdier i HTTP-anmodninger. Se [konfiguration for Media Services RESTEN API udvikling](media-services-rest-how-to-use.md)kan finde flere oplysninger.

>Når du har oprettet forbindelse til https://media.windows.net, får du en 301 Omdiriger, der angiver en anden Media Services URI. Du skal foretage efterfølgende opkald til den nye URI som beskrevet i [tilslutning til Media Services ved hjælp af REST-API](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Ryd aktiv levering politik

###<a id="create_asset_delivery_policy"></a>Oprette aktiv levering politik
Følgende HTTP-anmodningen opretter en politik til levering af aktiver, der angiver, at ikke anvende dynamiske kryptering og levere strømmen i en af følgende protokoller: MPEG TANKESTREG, HLS og jævn Streaming protokoller. 

Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .   


Anmode om:
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Svar:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Linket aktiv med aktiv levering politik

Følgende HTTP-anmodningen link til den angivne aktiv til politikken aktiv levering til.

Anmode om:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Svar:

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption aktiv levering politik 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Oprette indhold nøgle af typen EnvelopeEncryption og sammenkæde det med aktivet

Når du angiver DynamicEnvelopeEncryption levering politik, skal du sørge for at sammenkæde dine aktiver med en indhold nøgle af typen EnvelopeEncryption. Finde flere oplysninger: [oprette en indhold nøgle](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Få levering URL-adresse

Få levering URL-adressen for den angivne leveringsmetode af tasten indhold er oprettet i ovenstående trin. En klient bruger returnerede URL-adressen til at anmode om en AES-nøgle, eller en PlayReady licens i rækkefølge til afspilning af det beskyttede indhold.

Angiv typen af URL-adresse at få i brødteksten i HTTP-anmodningen. Hvis du beskytter dit indhold med PlayReady, anmode om en Media Services PlayReady licens acquisition URL-adresse, du bruger 1 for keyDeliveryType: {"keyDeliveryType": 1}. Hvis du beskytter dit indhold med konvolut kryptering, anmode om en vigtige acquisition URL-adresse ved at angive 2 til keyDeliveryType: {"keyDeliveryType": 2}.

Anmode om:
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Svar:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Oprette aktiv levering politik

Følgende HTTP-anmodningen opretter **AssetDeliveryPolicy** , der er konfigureret til at anvende dynamisk konvolut kryptering (**DynamicEnvelopeEncryption**) til **HLS** -protokollen (i dette eksempel andre protokoller blokeres fra streaming). 


Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .   

Anmode om:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Svar:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Linket aktiv med aktiv levering politik

Se [linket aktiv med aktiv levering politik](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption aktiv levering politik 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Oprette indhold nøgle af typen CommonEncryption og sammenkæde det med aktivet

Når du angiver DynamicCommonEncryption levering politik, skal du sørge for at sammenkæde dine aktiver med en indhold nøgle af typen CommonEncryption. Finde flere oplysninger: [oprette en indhold nøgle](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Få levering URL-adresse

Få levering URL-adressen for leveringsmetode PlayReady af tasten indhold er oprettet i ovenstående trin. En klient bruger returnerede URL-adressen til at anmode om en PlayReady licens i rækkefølge til afspilning af det beskyttede indhold. Yderligere oplysninger finder du se [Få levering URL-adressen](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Oprette aktiv levering politik

Følgende HTTP-anmodningen opretter **AssetDeliveryPolicy** , der er konfigureret til at anvende dynamisk almindelige kryptering (**DynamicCommonEncryption**) på den **Udglattede Streaming** protocol (i dette eksempel andre protokoller blokeres fra streaming). 

Oplysninger om værdierne, kan du angive, når du opretter en AssetDeliveryPolicy finder du i afsnittet [typer bruges, når du definerer AssetDeliveryPolicy](#types) .   


Anmode om:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Hvis du vil beskytte dit indhold ved hjælp af Widevine DRM, opdatere AssetDeliveryConfiguration værdierne for at bruge WidevineLicenseAcquisitionUrl (som indeholder værdien af 7) og angive URL-adressen for en tjeneste til levering af licens. Du kan bruge de følgende AMS partnere kan hjælpe dig med at levere Widevine licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

Eksempel: 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Når du krypterer med Widevine, vil du kun skal kunne levere ved hjælp af bindestreg. Sørg for at angive TANKESTREG (2) i aktiv levering protokollen.
  
###<a name="link-asset-with-asset-delivery-policy"></a>Linket aktiv med aktiv levering politik

Se [linket aktiv med aktiv levering politik](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Datatyper, der bruges, når du definerer AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }


##<a name="media-services-learning-paths"></a>Media Services læringsstier

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Give feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
