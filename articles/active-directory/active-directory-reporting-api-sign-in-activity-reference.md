<properties
    pageTitle="Azure Active Directory-logon aktivitetsrapport API reference | Microsoft Azure"
    description="Reference til Azure Active Directory-logon aktivitet rapport API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory-logon aktivitetsrapport API reference


Dette emne er en del af en samling af emner om Azure Active Directory rapportering API.  
Azure AD rapportering giver dig en API, der gør det muligt at få adgang til logon aktivitet rapportdata ved hjælp af kode eller relaterede værktøjer.
Omfanget af dette emne er til at give dig referenceoplysninger om **logon aktivitet rapport API**.

Se:

- [Log på aktiviteter](active-directory-reporting-azure-portal.md#sign-in-activities) for flere oplysninger
- [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md) til flere oplysninger om reporting API.

For spørgsmål, problemer eller feedback, skal du kontakte [AAD rapportering Hjælp](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Hvem kan få adgang til API dataene?

- Brugere i rollen sikkerhed administrator eller sikkerhed Reader

- Globale administratorer

- En app, der har tilladelse til at få adgang til API (app tilladelse kan være konfiguration, kun baseret på globale administratortilladelse)



## <a name="prerequisites"></a>Forudsætninger

Få adgang til denne rapport via reporting API, skal du have:

- En [Azure Active Directory Premium P1 eller P2 edition](active-directory-editions.md)

- Fuldført [forudsætninger for at få adgang til den Azure AD rapportering API](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Få adgang til API

Du kan enten få adgang til denne API via [Graph Explorer](https://graphexplorer2.cloudapp.net) eller fra et program med, for eksempel PowerShell. Hvis PowerShell til at fortolke korrekt OData filtersyntaksen bruges i AAD Graph RESTEN opkald, skal du skal bruge backtick (også kendt: accent grave) tegn til "escape"-tegnet $. Tegnet backtick fungerer som [Powershell's escape-tegn](https://technet.microsoft.com/library/hh847755.aspx), så PowerShell til at gøre en ordret fortolkning af tegnet $, og undgå forvirrende som et PowerShell variabelnavn (ie: $filter).

Fokus i dette emne er på Graph Stifinder. Du kan finde et PowerShell-eksempel dette [PowerShell-script](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>API slutpunkt

Du kan få adgang til denne API ved hjælp af følgende basis-URI:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



På grund af mængden data har denne API en begrænsning på en million returneres poster. 

Dette opkald returnerer dataene i batches. Hver batchen maksimalt 1000 poster.  
Brug det næste link for at få den næste gruppe af poster. Få oplysninger, [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) fra det første sæt af returnerede poster. Spring over tokenet indstilles i slutningen af resultatet.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Understøttede filtre

Du kan indskrænke antallet af poster, der returneres af en API opkald i form af et filter.  
Til logon API relaterede data, der understøttes følgende filtre:

- **$top =\<antallet af poster der skal returneres\> ** – til at begrænse antallet af returnerede poster. Dette er en dyr handling. Du skal ikke bruge dette filter, hvis du vil returnere tusindvis af objekter.  
- **$filter =\<filter-sætning\> ** – til at angive typen poster, der interesserer dig, på grundlag af understøttede filterfelter



## <a name="supported-filter-fields-and-operators"></a>Understøttede filterfelter og operatorer

For at angive typen poster, der interesserer dig, kan du oprette et filter-sætning, der kan indeholde et eller en kombination af de følgende filterfelter:

- [signinDateTime](#signindatetime) - definerer en dato eller et datointerval

- [bruger-id](#userid) - definerer en bestemt bruger baseret på bruger-ID.

- [userPrincipalName](#userprincipalname) - definerer en bestemt bruger baseret brugerens brugerens hovednavn (UPN)

- [sikkerhedskontrol](#appid) - definerer en bestemt app baseret på app-ID

- [appDisplayName](#appdisplayname) - definerer en bestemt app baseret på app visningsnavn

- [loginStatus](#loginStatus) - definerer status for logon (succes / fejl)


> [AZURE.NOTE] Når du bruger Graph Explorer, er du at skulle bruge mellem store og små for de enkelte bogstaver din filterfelter.


Hvis du vil indskrænke omfanget af de returnerede data, kan du oprette kombinationer af de understøttede filtre og filterfelter. For eksempel returnerer følgende sætning de øverste 10 poster mellem juli 1st 2016 og juli 6th 2016:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Understøttede operatorer**: eq, Flet, le, BT, lt

**Eksempel**:

Ved hjælp af en bestemt dato

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



Ved hjælp af et datointerval    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Noter**:

Parameteren dato/klokkeslæt skal være i UTC-format 


----------

### <a name="userid"></a>bruger-id

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Noter**:

Værdien af bruger-id er en strengværdi



----------

### <a name="userprincipalname"></a>userPrincipalName

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Noter**:

Værdien af userPrincipalName er en strengværdi

----------

### <a name="appid"></a>Sikkerhedskontrol

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Noter**:

Værdien af sikkerhedskontrol er en strengværdi

----------


### <a name="appdisplayname"></a>appDisplayName

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Noter**:

Værdien af appDisplayName er en strengværdi

----------

### <a name="loginstatus"></a>loginStatus

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=loginStatus+eq+'1'  


**Noter**:

Der er to muligheder for loginStatus: 0 - succes, 1 - fejl

----------



## <a name="next-steps"></a>Næste trin

- Vil du se eksempler på filtrerede logon aktiviteter? Se [eksempler på Azure Active Directory-logon aktivitet rapport API](active-directory-reporting-api-sign-in-activity-samples.md).

- Vil du vide mere om den Azure AD API-rapportering? Se [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md).