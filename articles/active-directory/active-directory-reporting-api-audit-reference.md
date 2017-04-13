<properties
    pageTitle="Azure Active Directory overvågningslog API reference | Microsoft Azure"
    description="Hvordan du kan komme i gang med Azure Active Directory overvågningslog API"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory overvågningslog API reference

Dette emne er en del af en samling af emner om Azure Active Directory rapportering API.  
Azure AD rapportering giver dig en API, der gør det muligt at få adgang til overvågningslog data ved hjælp af kode eller relaterede værktøjer.
Omfanget af dette emne er til at give dig referenceoplysninger om **overvåge API**.

Se:

- [Overvågningslogge](active-directory-reporting-azure-portal.md#audit-logs) yderligere grundlæggende oplysninger
- [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md) til flere oplysninger om reporting API.

For spørgsmål, problemer eller feedback, skal du kontakte [AAD rapportering Hjælp](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Hvem kan få adgang til dataene?

- Brugere i rollen sikkerhed administrator eller sikkerhed Reader

- Globale administratorer

- En app, der har tilladelse til at få adgang til API (app tilladelse kan være konfiguration, kun baseret på globale administratortilladelse)



## <a name="prerequisites"></a>Forudsætninger

For at få adgang til denne rapport via API rapportering, skal du have:

- En [Azure Active Directory gratis eller bedre edition](active-directory-editions.md)

- Fuldført [forudsætninger for at få adgang til den Azure AD rapportering API](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Få adgang til API

Du kan enten få adgang til denne API via [Graph Explorer](https://graphexplorer2.cloudapp.net) eller fra et program med, for eksempel PowerShell. Hvis PowerShell til at fortolke korrekt OData filtersyntaksen bruges i AAD Graph RESTEN opkald, skal du skal bruge backtick (også kendt: accent grave) tegn til "escape"-tegnet $. Tegnet backtick fungerer som [Powershell's escape-tegn](https://technet.microsoft.com/library/hh847755.aspx), så PowerShell til at gøre en ordret fortolkning af tegnet $, og undgå forvirrende som et PowerShell variabelnavn (ie: $filter).

Fokus i dette emne er på Graph Stifinder. Du kan finde et PowerShell-eksempel dette [PowerShell-script](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>API slutpunkt


Du kan få adgang til denne API ved hjælp af følgende URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Der er ingen begrænsning på antallet af poster, der returneres af Azure AD overvågningslog API (ved hjælp af OData sideinddeling).
Opbevaring begrænsninger på rapportdata, se [Rapportering opbevaringspolitikker](active-directory-reporting-retention.md).

Dette opkald returnerer dataene i batches. Hver batchen maksimalt 1000 poster.  
Brug det næste link for at få den næste gruppe af poster. Få oplysninger, skiptoken fra det første sæt af returnerede poster. Spring over tokenet indstilles i slutningen af resultatet.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Understøttede filtre

Du kan indskrænke antallet af poster, der returneres af en API opkald i form af et filter.  
Til logon API relaterede data, der understøttes følgende filtre:

- **$top =\<antallet af poster der skal returneres\> ** – til at begrænse antallet af returnerede poster. Dette er en dyr handling. Du skal ikke bruge dette filter, hvis du vil returnere tusindvis af objekter.     
- **$filter =\<filter-sætning\> ** – til at angive typen poster, der interesserer dig, på grundlag af understøttede filterfelter



## <a name="supported-filter-fields-and-operators"></a>Understøttede filterfelter og operatorer

For at angive typen poster, der interesserer dig, kan du oprette et filter-sætning, der kan indeholde et eller en kombination af de følgende filterfelter:

- [activityDate](#activitydate) - definerer en dato eller et datointerval
- [activityType](#activitytype) - definerer typen af en aktivitet
- [aktivitet](#activity) - definerer aktiviteten som streng  
- [Agent/navn](#actorname) - definerer agenten i form af den Agent navn
- [Agent/objectid](#actorobjectid) - definerer agenten i form af den Agent-ID   
- [Agent/upn](#actorupn) - definerer agenten i form af den Agent princippet brugernavn (UPN) 
- [target/navn](#targetname) - definerer målet i form af den Agent navn
- [target/objectid](#targetobjectid) - definerer målet i form af den destinationsadresse-ID  
- [target/upn](#targetupn) - definerer agenten i form af den Agent princippet brugernavn (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Understøttede operatorer**: eq, Flet, le, BT, lt

**Eksempel**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Noter**:

dato/klokkeslæt skal være i UTC-format

----------

### <a name="activitytype"></a>activityType

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=activityType eq 'User'  

**Noter**:

store og små bogstaver

----------

### <a name="activity"></a>aktivitet

**Understøttede operatorer**: eq, indeholder, startsWith

**Eksempel**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Noter**:

store og små bogstaver

----------

### <a name="actorname"></a>Agent/navn

**Understøttede operatorer**: eq, indeholder, startsWith

**Eksempel**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Noter**:

case-insensitive

    

----------
### <a name="actorobjectid"></a>Agent/objectId

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>Target/navn

**Understøttede operatorer**: eq, indeholder, startsWith

**Eksempel**:

    $filter=targets/any(t: t/name eq 'some name')   

**Noter**:

Case-insensitive

----------

### <a name="targetupn"></a>Target/upn

**Understøttede operatorer**: eq, startsWith

**Eksempel**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Noter**:

- Case-insensitive
- Skal du tilføje det fulde navneområde, når forespørgsler Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>Target/objectId

**Understøttede operatorer**: eq

**Eksempel**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>Agent/upn

**Understøttede operatorer**: eq, startsWith

**Eksempel**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Noter**:

- Case-insensitive 
- Skal du tilføje det fulde navneområde, når forespørgsler Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Næste trin

- Vil du se eksempler på filtrerede systemaktiviteter? Se den [Azure Active Directory overvåge API eksempler](active-directory-reporting-api-audit-samples.md).

- Vil du vide mere om den Azure AD API-rapportering? Se [Introduktion til Azure Active Directory Reporting-API](active-directory-reporting-api-getting-started.md).