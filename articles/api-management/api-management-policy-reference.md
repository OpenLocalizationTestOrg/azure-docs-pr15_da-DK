<properties 
    pageTitle="Azure API Management politikreference" 
    description="Få mere at vide om de politikker, der er tilgængelige for at konfigurere API administration." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Azure API Management politikreference

Dette afsnit indeholder et indeks for politikker i [API Management politikreference][]. Du kan finde oplysninger om at tilføje og konfigurere politikker [politikker API administration][].

Politik udtryk kan bruges som attributten værdier eller tekstværdier i en af politikkerne for administration af API, medmindre politikken angiver noget andet. Politikker som [Kontrolflow][] og [angive variabel][] politikker er baseret på politik udtryk. Få mere at vide under [Avanceret politikker][] og [politik udtryk][]

## <a name="policy-reference-index"></a>Politik reference indeks

-   [Access begrænsning politikker][]
    -   [Kontrollere HTTP sidehoved][] - gennemtvinger eksistensen og/eller værdien af en HTTP-Header.
    -   [Grænse opkald rente ved abonnement][] - forhindrer af API højest ved at begrænse opkald rente, på grundlag af per abonnement.
    -   [Grænse opkald rente med nøglen](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) – forhindrer af API højest ved at begrænse opkald rente, på grundlag af per nøgle.
    -   [Begræns opkalds IP'er][] - filtre afviser (gør det muligt for /) opkald fra bestemte IP-adresser og/eller -adresseområder.
    -   [Angive kvoten for anvendelse af abonnement][] - gør det muligt at gennemtvinge en vedvarende eller levetid opkald lydstyrken og/eller båndbredde kvote, på grundlag af per abonnement.
    -   [Angive kvote med nøglen](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - gør det muligt at gennemtvinge en vedvarende eller levetid opkald lydstyrken og/eller båndbredde kvote, på grundlag af per nøgle.
    -   [Validere JWT][] - håndhæves eksistensen og en JWT ud fra et bestemt HTTP sidehoved eller en bestemt forespørgselsparameter gyldighed.
-   [Avancerede politikker][]
    -   [Kontrolflow][] - anvender betinget politik-sætninger, der er baseret på resultatet af evalueringen af booleske [udtryk][].
    -   [Videresende anmodningen][] - videresender anmodningen til back end-tjenesten.
    -   [Log til begivenhed Hub][] - sender meddelelser i det angivne format til en meddelelse destination, der er defineret af en [logføring](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) enhed.
    -   [Prøv igen](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - forsøg udførelse af omsluttede politik-sætninger, hvis og indtil betingelsen er opfyldt. Udførelse af gentages på de angivne tidsintervaller og op til det angivne antal forsøg.
    -   [Returnere svar](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - afbrydes pipeline udførelse af og returnerer den angivne svar direkte til kalderen.
    -   [Send én måde indkaldelsen](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - sender en anmodning til den angivne URL-adresse uden venter på svar.
    -   [Send anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - sender en anmodning til den angivne URL-adresse.
    -   [Angiv anmodningsmetode](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - gør det muligt at ændre metoden HTTP for en anmodning.
    -   HTTP-statuskode ændres [angive status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - til den angivne værdi.
    -   [Angive variabel][] - fastholdes en værdi i en navngivet [kontekst][] variabel for senere adgang.
    -   [Sporing](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - tilføjer en streng til [API inspektion](../api-management/api-management-howto-api-inspector.md) output.
    -   [Vent](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - venter på omsluttede Send anmodning få værdi fra cachen eller kontrolelement flow politikker skal afsluttes, før du fortsætter.
-   [Godkendelsespolitikker for][]
    -   [Godkend med Basic][] - Godkend til en back end-tjeneste, der bruger basisgodkendelse.
    -   [Godkend med klientcertifikat][] - Godkend med en back end-tjenesten ved hjælp af klientcertifikater.
-   [Cachelagring af politikker][] 
    -   [Få fra cachen][] - udføre cache slå op og returnere et gyldigt cachelagrede svar, når de er tilgængelige.
    -   [Store til cache][] - cache svar efter konfigurationen af den angivne cache.
    -   [Få værdi fra cachen](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - Hent en cachelagrede element med nøglen.
    -   [Store værdi i cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - Store et element i cachen med nøglen.
    -   [Fjerne værdi fra cachen](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - Fjern et element i cachen med nøglen.
-   [Cross domæne politikker][] 
    -   [Tillad tværs af domæner opkald][] - gør API tilgængelig fra Adobe Flash- og Microsoft Silverlight browserbaserede klienter.
    -   [CORS][] - tilføjer tværs origin ressourcedeling (CORS) support til en handling eller en API til at tillade tværs af domæner opkald fra browserbaserede klienter.
    -   [JSONP][] - tilføjer JSON med udfyldning (JSONP) support til en handling eller en API for at tillade opkald i tværs af domæner fra JavaScript browserbaserede klienter.
-   [Transformation politikker][] 
    -   [Konvertere JSON til XML][] - anmodning om konverterer eller svar brødtekst fra JSON til XML.
    -   [Konvertere XML til JSON][] - anmodning om konverterer eller svar brødtekst fra XML til JSON.
    -   [Søge efter og erstatte strengen i brødteksten][] - finder en anmodning om eller svar understreng og erstatter det med en anden understreng.
    -   [Inputmaske URL-adresser i indhold][] - igen skriver (masker) links i svaret brødtekst, så de peger på linket tilsvarende via gatewayen.
    -   [Angive back end-tjeneste][] - ændres back end-tjenesten for anmodning om en indgående.
    -   [Angive brødtekst][] - sæt meddelelsesteksten indgående og udgående anmodninger om.
    -   [Angiv HTTP sidehoved][] - tildeler en værdi til en eksisterende svar og/eller anmodning om sidehoved eller tilføjer et nyt svar og/eller anmodning om sidehoved.
    -   [Angiv streng forespørgselsparameter][] - føjer, erstatter værdien af eller sletninger anmode om forespørgselsparameter streng.
    -   [Omskrivning URL-adresse][] - konverterer en anmodning om URL-adresse fra dens offentlige formular til formularen forventet af webtjenesten.

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om politik udtryk, den følgende video.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Access begrænsning politikker]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Kontrollere HTTP sidehoved]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Grænse opkald rente af abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Begrænse opkalds IP'er]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Angiv kvoten for anvendelse af abonnement]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validere JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Avancerede politikker]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Kontrolflow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set-variabel]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[udtryk]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[kontekst]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Videresende anmodning]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log til begivenhed Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Godkendelsespolitikker for]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Godkende med Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Godkende med klientcertifikat]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Cachelagring af politikker]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Få fra cachen]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Gemme til cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domæne politikker]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Tillad tværs af domæner]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation politikker]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Konvertere JSON til XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Konvertere XML til JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Søge efter og erstatte strengen i brødteksten]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Inputmaske URL-adresser i indhold]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Indstil back end-tjeneste]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Angiv brødtekst]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Angiv HTTP sidehoved]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Angiv streng forespørgselsparameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Omskrivning af URL-adresse]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Politikker for administration af API]: api-management-howto-policies.md
[Administration af API politikreference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Politik udtryk]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
