<properties
    pageTitle="Azure AD-forbindelse synkronisering: forstå deklarativ klargøring udtryk | Microsoft Azure"
    description="Beskriver de deklarative klargøring udtryk."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD-forbindelse synkronisering: forstå deklarativ klargøring af udtryk
Azure AD-forbindelse Synkroniser bygger på deklarativ klargøring først introduceret i Forefront identitet Manager 2010. Gør det muligt at implementere fuldført identitet integration business-logik uden at skulle skrive kompilerede kode.

En vigtig del af deklarativ klargøring er det udtryksprog, der bruges i attributten flyder. Sproget er et undersæt af Microsoft Visual Basic® for Applications (VBA). Dette sprog bruges i Microsoft Office og brugere med oplevelse af VBScript også genkende den. Deklarativ klargøring udtryksprog kun ved hjælp af funktioner og er ikke et strukturerede sprog. Der er ingen metoder eller sætninger. Funktioner i stedet indlejrede til udtrykkelig program forløb.

Yderligere oplysninger finder du [Velkommen til Visual Basic for Applications sprogreference til Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Attributterne er kraftigt skrevet. En funktion accepterer kun attributterne for den korrekte type. Det er også store og små bogstaver. Både funktionsnavne og navne på attributter skal have stort hus eller en fejl er opstået.

## <a name="language-definitions-and-identifiers"></a>Sprog definitioner og -id'er

- Funktioner har et navn, efterfulgt af argumenter i kantede parenteser: funktionsnavn (argumentet 1, argument N).
- Attributter er identificeret med kantede parenteser: [attributeName]
- Parametre er identificeret med procenttegn: ParameterName %
- Strengkonstanter er omgivet af anførselstegn: eksempelvis "Contoso" (Bemærk: skal bruge lige anførselstegn "" og ikke intelligent anførselstegn "")
- Numeriske værdier er udtrykt uden anførselstegn og forventes at være decimal. Hexadecimale værdier er præfikset & H. For eksempel 98052 og HFF
- Booleske værdier udtrykkes med konstanter: SAND, FALSK.
- Indbyggede konstanter og konstanter udtrykkes med kun deres navn: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funktioner
Deklarativ klargøring bruger mange funktioner til at aktivere mulighed for at transformere attributværdier. Disse funktioner kan indlejres, så resultatet fra én funktion er blev sendt til en anden funktion.

`Function1(Function2(Function3()))`

Den komplette liste over funktioner kan findes i [funktionsreference](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parametre
En parameter er defineret, enten via en forbindelse eller af en administrator ved hjælp af PowerShell. Parametre indeholder normalt værdier, der er forskellige fra system til system, for eksempel navnet på domænet, brugeren er placeret i. Disse parametre kan bruges i attributten flyder.

Active Directory Connector beregnet følgende parametre for indgående synkronisering regler:

| Parameternavn | Kommentar |
| --- | --- |
| Domain.Netbios | NetBIOS-formatet for det domæne, der er i øjeblikket der importeres, for eksempel FABRIKAMSALES |
| Domain.FQDN | FULDE format på det domæne, der er i øjeblikket der importeres, for eksempel sales.fabrikam.com |
| Domain.LDAP | LDAP-formatet for det domæne, der er i øjeblikket der importeres, for eksempel DC = salg, DC = fabrikam, DC = com |
| Forest.Netbios | NetBIOS-formatet for det område navn i øjeblikket der importeres, for eksempel FABRIKAMCORP |
| Forest.FQDN | FULDE formatet for det område navn i øjeblikket der importeres, for eksempel fabrikam.com |
| Forest.LDAP | LDAP-formatet for det område navn i øjeblikket der importeres, for eksempel DC = fabrikam, DC = com |

Systemet indeholder følgende parameter, som bruges til at få id for forbindelsen i øjeblikket kører:  
`Connector.ID`

Her er et eksempel, der udfylder metaverse attribut domænet med NetBIOS-navnet på det domæne, hvor brugeren er placeret:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatorer
Du kan bruge følgende operatorer:

- **Sammenligning**: <, < =, <>, =, >, > =
- **Matematik**: +, -, \*, -
- **Streng**: & (sammenkædning)
- **Logisk**: & & (og), fuldført. (eller)
- **Evaluering rækkefølge**:)

Operatorer evalueres venstre mod højre og har den samme evaluering prioritet. Det vil sige, den \* (multiplikator) er ikke evalueres, før - (subtraktion). 2\*(5 + 3) er ikke den samme som 2\*5 + 3. Kantede parenteser () bruges til at ændre evalueringsrækkefølgen,, når venstre mod højre evaluering rækkefølge ikke er relevant.

## <a name="multi-valued-attributes"></a>Med flere valgmuligheder attributter
Funktionerne kan fungere på både enkeltværdi- og med flere valgmuligheder attributter. Funktionen fungerer over hver værdi for med flere valgmuligheder attributter og gælder den samme funktion for hver værdi.

Eksempel:  
`Trim([proxyAddresses])`Gøre en Trim på hver værdi i attributten proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`For hver værdi med en @-sign, erstatte domænet med @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Se efter SIP-adressen og fjerne det fra værdierne.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om af konfigurationsmodellen i [Forstå deklarativ klargøring](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Se hvordan deklarativ klargøring er anvendte out of box i [forstå standardkonfiguration](active-directory-aadconnectsync-understanding-default-configuration.md).
- Se, hvordan du ændrer praktiske ved hjælp af deklarativ klargøring i [Sådan foretager en ændring af standard-konfigurationen](active-directory-aadconnectsync-change-the-configuration.md).

**Oversigt oversigtsemner**

- [Azure AD-forbindelse synkronisering: forstå og tilpasse synkronisering](active-directory-aadconnectsync-whatis.md)
- [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)

**Referenceemner**

- [Azure AD-forbindelse synkronisering: funktioner Reference](active-directory-aadconnectsync-functions-reference.md)
