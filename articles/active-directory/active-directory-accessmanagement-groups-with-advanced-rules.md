
<properties
    pageTitle="Brug af attributter til at oprette avancerede regler | Microsoft Azure"
    description="Hvordan-på er for at oprette avancerede regler til en gruppe, herunder understøttede udtryk regel operatorer og parametre."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>Oprette avancerede regler ved hjælp af attributter

Portalen Azure klassisk giver dig mulighed for at oprette avancerede regler for at aktivere mere komplekse attribut-baseret dynamisk medlemskaber for grupper af Azure Active Directory (Azure AD).  

Når en hvilken som helst attributterne for en bruger ændring systemet evaluerer alle regler for dynamisk gruppe i en mappe, skal du se Hvis attributten ændringen af brugeren vil udløse en gruppe tilføjer eller fjerner. Hvis en bruger, der opfylder en regel for en gruppe, føjes de som et medlem til den pågældende gruppe. Hvis de ikke længere opfylder reglen for en gruppe, de er medlem af, fjernes de som en medlemmer fra den pågældende gruppe.

## <a name="to-create-the-advanced-rule"></a>Sådan oprettes den avancerede regel

1. Vælg **Active Directory**i [Azure klassisk portal](https://manage.windowsazure.com), og Åbn derefter organisationens adressekartotek.

2. Vælg fanen **grupper** , og Åbn derefter den gruppe, du vil redigere.

3. Vælg fanen **Konfigurer** , Vælg indstillingen **Avanceret regel** , og angiv derefter den avancerede regel i tekstfeltet.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Bygning af brødteksten i en avanceret regel

Den avancerede regel, som du kan oprette for dynamisk medlemskaberne for grupper er grundlæggende en binær udtryk, der består af tre dele og resultater i en SAND eller FALSK-resultatet. De tre dele er:

- Venstre parameter
- Binær operator
- Højre konstant

En komplet avanceret regel ser nogenlunde sådan ud: (leftParameter binaryOperator "RightConstant"), hvor åbning og afsluttende parentes der kræves til hele binære udtrykket, der kræves til højre konstanten dobbelte anførselstegn, og syntaksen for parameteren venstre er user.property. En avanceret regel kan bestå af mere end ét binære udtryk adskilt af- og- eller, og - ikke logiske operatorer.
Følgende er eksempler på en korrekt bygget avanceret regel:

- (user.department - eq "Salg")- eller (user.department - eq "Marketing")
- (user.department - eq "Salg")- og - ikke (user.jobTitle-indeholder "SDE")

Du kan finde den komplette liste over understøttede parametre og udtryk regel operatorer afsnittene herunder.

Den samlede længde af brødteksten i Avanceret reglen må ikke overstige 2.048 tegn.

> [AZURE.NOTE]
>Streng og regex handlinger er bogstaver. Du kan også udføre Null Kontroller, brug af $null som en konstant, for eksempel, user.department - eq $null.
Strenge, der indeholder tilbud "bør escape-tegn ved hjælp af ' tegn, for eksempel user.department - eq \`"Salg".

## <a name="supported-expression-rule-operators"></a>Understøttede udtryk regel operatorer
I følgende tabel vises alle de understøttede udtryk regel operatorer og deres syntaks skal bruges i brødteksten i den avancerede regel:

| Operatoren        | Syntaksen for         |
|-----------------|----------------|
| Ikke er lig med      | -ne            |
| Er lig med          | -eq            |
| Ikke starter med | -notStartsWith |
| Starter med     | -startsWith    |
| Ikke indeholder    | -notContains   |
| Indeholder        | -indeholder      |
| Ikke svarer til       | -notMatch      |
| Sammenlign           | -svarer         |


## <a name="query-error-remediation"></a>Forespørgsel fejl afhjælpning
I følgende tabel vises potentielle fejl og hvordan du kan rette dem, hvis de opstår

| Forespørgsel analysér fejl     | Brug af fejl       | Justerede forbrug             |
|-----------------------|-------------------|-----------------------------|
| Fejl: Attribut understøttes ikke.                                      | (user.invalidProperty - eq "Værdi")       | (user.department - eq "værdi")<br/>Egenskaben skal svare til en [understøttet listen egenskaber](#supported-properties).                          |
| Fejl: Operator understøttes ikke på attributten.                       | (user.accountEnabled-indeholder SAND)                                                                               | (user.accountEnabled - eq SAND)<br/>Egenskaben er af Skriv boolesk. Bruge understøttede operatorer (-eq eller - ne) på boolesk type fra ovenstående liste.                                                                                                                                   |
| Fejl: Forespørgsel kompileringsfejl.                                      | (user.department - eq "Salg")- og (user.department - eq "Marketing")(user.userPrincipalName-match"*@domain.ext") | (user.department - eq "Salg")- og (user.department - eq "Marketing")<br/>Logisk operator skal svare til en understøttet egenskaber herover. (user.userPrincipalName-svarer til ".*@domain.ext")or(user.userPrincipalName -svarer til "@domain.ext$")Error i det regulære udtryk. |
| Fejl: Binær udtryk er ikke i højre format.                     | (user.department – eq "Salg") (user.department - eq "Salg") (user.department-eq "Salg")                             | (user.accountEnabled - eq SAND)- og (user.userPrincipalName-indeholder"alias@domain")<br/>Forespørgslen indeholder flere fejl. Parentes ikke i rigtige sted.                                                                                                                            |
| Fejl: Opstod ukendt fejl under konfiguration af dynamiske medlemskaber. | (user.accountEnabled - eq "sand" og user.userPrincipalName-indeholder"alias@domain")                               | (user.accountEnabled - eq SAND)- og (user.userPrincipalName-indeholder"alias@domain")<br/>Forespørgslen indeholder flere fejl. Parentes ikke i rigtige sted.                                                                                                                            |

## <a name="supported-properties"></a>Understøttede egenskaber
Følgende er alle brugeregenskaber, som du kan bruge i dine avancerede regel:

### <a name="properties-of-type-boolean"></a>Egenskaber for Skriv boolesk

Tilladte operatorer

* -eq


* -ne


| Egenskaber     | Tilladte værdier  | Brugen                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | Sand falsk      | user.accountEnabled - eq SAND)  |
| dirSyncEnabled | Sand falsk null | (user.dirSyncEnabled - eq SAND) |

### <a name="properties-of-type-string"></a>Egenskaber for typen streng

Tilladte operatorer

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -indeholder


* -notContains


* -svarer


* -notMatch

| Egenskaber                 | Tilladte værdier                                                                                        | Brugen                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| by                       | En strengværdi eller et $null                                                                           | (user.city - eq "værdi")                                   |
| land                    | En strengværdi eller et $null                                                                            | (user.country - eq "værdi")                                |
| afdeling                 | En strengværdi eller et $null                                                                          | (user.department - eq "værdi")                             |
| vist navn                | En hvilken som helst strengværdi                                                                                 | (user.displayName - eq "værdi")                            |
| facsimileTelephoneNumber   | En strengværdi eller et $null                                                                           | (user.facsimileTelephoneNumber - eq "værdi")               |
| givenName                  | En strengværdi eller et $null                                                                           | (user.givenName - eq "værdi")                              |
| Stilling                   | En strengværdi eller et $null                                                                           | (user.jobTitle - eq "værdi")                               |
| mail                       | En strengværdi eller et $null (SMTP-adresse på brugeren)                                                  | (user.mail - eq "værdi")                                   |
| mailNickName               | En hvilken som helst strengværdi (mail-alias for brugeren)                                                            | (user.mailNickName - eq "værdi")                           |
| Mobile                     | En strengværdi eller et $null                                                                           | (user.mobile - eq "værdi")                                 |
| objectId                   | GUID for brugerobjektet                                                                            | (user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | Ingen DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - eq "DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | En strengværdi eller et $null                                                                            | (user.physicalDeliveryOfficeName - eq "værdi")             |
| Postnummer                 | En strengværdi eller et $null                                                                            | (user.postalCode - eq "værdi")                             |
| preferredLanguage          | ISO-koden for 639-1                                                                                        | (user.preferredLanguage - eq "en-US")                      |
| sipProxyAddress            | En strengværdi eller et $null                                                                            | (user.sipProxyAddress - eq "værdi")                        |
| stat                      | En strengværdi eller et $null                                                                            | (user.state - eq "værdi")                                  |
| streetAddress              | En strengværdi eller et $null                                                                            | (user.streetAddress - eq "værdi")                          |
| Efternavn                    | En strengværdi eller et $null                                                                            | (user.surname - eq "værdi")                                |
| telephoneNumber            | En strengværdi eller et $null                                                                            | (user.telephoneNumber - eq "værdi")                        |
| usageLocation              | To bogstavsbetegnede landekode                                                                           | (user.usageLocation - eq "USA")                             |
| userPrincipalName          | En hvilken som helst strengværdi                                                                                     | (user.userPrincipalName - eq"alias@domain")               |
| userType                   | medlem gæst $null                                                                                    | (user.userType - eq "Medlem")                              |

### <a name="properties-of-type-string-collection"></a>Egenskaber for datatypen strengsamling

Tilladte operatorer

* -indeholder


* -notContains

| Poperties      | Tilladte værdier                        | Brugen                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | En hvilken som helst strengværdi                      | (user.otherMails-indeholder"alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses-indeholder "SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Filtypenavn attributter og brugerdefinerede attributter
Filtypenavn attributter og brugerdefinerede attributter understøttes i regler for dynamisk medlemskab.

Filtypenavn attributter der synkroniseres fra lokal vinduet Server AD og tage formatet af "ExtensionAttributeX", hvor X er lig med 1-15.
Et eksempel på en regel, der bruges en udvidelsesattribut ville være

(user.extensionAttribute15 - eq "Marketing")

Brugerdefinerede attributter der synkroniseres fra lokal Windows Server AD eller fra en forbundet SaaS program og formatet af "user.extension_[GUID]\__ [attribut]", hvor [GUID] er det entydige id i AAD for det program, der oprettede attributten i AAD og [attribut] er navnet på attributten, som det blev oprettet.
Er et eksempel på en regel, der bruger en brugerdefineret attribut

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Det brugerdefinerede attributnavn kan findes i mappen ved at forespørge en bruger er attribut med Graph Stifinder og søge efter attributnavnet.

## <a name="direct-reports-rule"></a>Direkte underordnede regel
Nu kan du udfylde medlemmer i en gruppe, der er baseret på attributten manager for en bruger.

**Konfigurere en gruppe som en "Manager" gruppe**

1. Klik på **Active Directory**i Azure klassisk-portalen, og klik derefter på navnet på organisationens adressekartotek.

2. Vælg fanen **grupper** , og Åbn derefter den gruppe, du vil redigere.

3. Vælg fanen **Konfigurer** , og vælg derefter **Avancerede regel**.

4. Skriv reglen med følgende syntaks:

    Direkte rapporter for *direkte rapporter til {obectID_of_manager}*. Et eksempel på en gyldig regel for direkte rapporter er

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    hvor er "62e19b97-8b3d-4d4a-a106-4ce66896a863" objectID af manager. Objekt-ID'ET kan findes i den Azure AD under **fanen profil** på siden bruger for den bruger, som er leder.

3. Når du gemmer denne regel, bliver alle brugere, der opfylder reglen sammenføjet som medlemmer af gruppen. Det kan tage nogle minutter til gruppen for at udfylde først.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Oprette regler for enhedsobjekter ved hjælp af attributter

Du kan også oprette en regel, der markerer enhedsobjekter for medlemskab i en gruppe. Du kan bruge følgende enhed attributter:

| Egenskaber              | Tilladte værdier                  | Brugen                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| vist navn             | en hvilken som helst strengværdi                | (device.displayName - eq "Rob Iphone")                       |
| deviceOSType            | en hvilken som helst strengværdi                | (device.deviceOSType - eq "IOS")                             |
| deviceOSVersion         | en hvilken som helst strengværdi                | (enhed. OSVersion - eq "9.1")                                |
| isDirSynced             | Sand falsk null                 | (device.isDirSynced - eq "sand")                             |
| isManaged               | Sand falsk null                 | (device.isManaged - eq "falsk")                              |
| isCompliant             | Sand falsk null                 | (device.isCompliant - eq "sand")                             |
| deviceCategory          | en hvilken som helst strengværdi                | (device.deviceCategory - eq "")                              |
| deviceManufacturer      | en hvilken som helst strengværdi                | (device.deviceManufacturer - eq "Microsoft")                 |
| deviceModel             | en hvilken som helst strengværdi                | (device.deviceModel - eq "IPhone 7 +")                        |
| deviceOwnership         | en hvilken som helst strengværdi                | (device.deviceOwnership - eq "")                             |
| Domænenavn              | en hvilken som helst strengværdi                | (device.domainName - eq "contoso.com")                       |
| enrollmentProfileName   | en hvilken som helst strengværdi                | (device.enrollmentProfileName - eq "")                       |
| isRooted                | Sand falsk null                 | (device.deviceOSType - eq "sand")                            |
| managementType          | en hvilken som helst strengværdi                | (device.managementType - eq "")                              |
| organizationalUnit      | en hvilken som helst strengværdi                | (device.organizationalUnit - eq "")                          |
| deviceId                | en gyldig deviceId                | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [AZURE.NOTE]
> Disse enhed regler kan ikke oprettes ved hjælp af rullemenuen "simpel regel" i portalen Azure klassisk.


## <a name="additional-information"></a>Yderligere oplysninger
Disse artikler giver yderligere oplysninger om Azure Active Directory.

* [Fejlfinding i forbindelse med dynamisk medlemskaber for grupper](active-directory-accessmanagement-troubleshooting.md)

* [Adgangskontrol til ressourcer med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Azure Active Directory-cmdletter til konfiguration af gruppeindstillinger](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Artikel indeks til programadministration i Azure Active Directory](active-directory-apps-index.md)

* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
