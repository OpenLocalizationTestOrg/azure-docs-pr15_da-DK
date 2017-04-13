<properties
    pageTitle="Azure Single Sign-On SAML Protocol | Microsoft Azure"
    description="I denne artikel beskrives de enkelte Log på SAML protocol i Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="single-sign-on-saml-protocol"></a>Enkelt Sign-On SAML protokol

Denne artikel omhandler SAML 2.0 godkendelse mødeindkaldelser og svar, der understøtter Azure Active Directory (Azure AD) til Single Sign-On.

Protocol diagrammet nedenfor beskrives de enkelte sign-on – serie. Tjenesten skyen (tjenesteudbyder) bruger en HTTP Omdiriger binding til at overføre en `AuthnRequest` (anmodningen om godkendelse) element til Azure AD (identitetsudbyder). Azure AD derefter bruger en HTTP indlæg binding for at sende en `Response` element til skytjenesten.

![Single Sign-On arbejdsproces](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

For at få en brugergodkendelse, cloud services send en `AuthnRequest` element til Azure AD. En stikprøve SAML 2.0 `AuthnRequest` kan se ud som dette:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| ID | påkrævet | Azure AD bruger denne attribut til at udfylde de `InResponseTo` attributten for returnerede svaret. ID må ikke begynde med et tal, så en fælles strategi er føjes en streng som "-id" til Strengrepræsentationen af et GUID. For eksempel `id6c1c178c166d486687be4aaf5e482730` er et gyldigt ID. |
| Version | påkrævet | Dette bør være **2.0**.|
| IssueInstant | påkrævet | Dette er en dato/klokkeslæt-streng med en UTC værdi og [frem og tilbage format ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD forventer en dato / klokkeslætsværdi af denne type, men ikke evaluere eller brug værdien fra. |
| AssertionConsumerServiceUrl | valgfri | Hvis, skal det til den `RedirectUri` til tjenesten skyen i Azure AD. |
| ForceAuthn | valgfri | Hvis, skal det være falsk. En anden værdi, medfører en fejl.|
| IsPassive | valgfri | Hvis, skal det være falsk. En anden værdi, medfører en fejl. |  

Alle andre `AuthnRequest` attributter, som samtykke, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex og ProviderName er **ignoreres**.

Azure AD ignorerer også den `Conditions` element i `AuthnRequest`.

### <a name="issuer"></a>Udsteder

Den `Issuer` element i en `AuthnRequest` skal svare nøjagtigt til en af **ServicePrincipalNames** i skytjenesten i Azure AD. Dette er typisk angivet til **App ID URI** , der er angivet under registrering af programmet.

En eksempel SAML uddrag, der indeholder den `Issuer` element ser sådan ud:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Dette element anmoder om et bestemt ID navneformat i svaret og er valgfri i `AuthnRequest` elementer, der sendes til Azure AD.

En stikprøve `NameIdPolicy` element ser sådan ud:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Hvis `NameIDPolicy` er leveres, du kan medtage dens valgfrit `Format` attributter. Den `Format` attribut kan kun have én af følgende værdier en anden værdi, medfører en fejl.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory udsteder NameID kravet som en parvis id.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory udsteder NameID kravet i formatet for e-mail-adresse.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Denne værdi tillader Azure Active Directory for at vælge formatet krav. Azure Active Directory-problemer på NameID som en parvis id.

Du skal ikke medtage den `SPNameQualifer` attributter. Azure AD ignorerer den `AllowCreate` attributter.

### <a name="requestauthncontext"></a>RequestAuthnContext

Den `RequestedAuthnContext` element angiver de ønskede godkendelsesmetoder. Det er valgfrit i `AuthnRequest` elementer, der sendes til Azure AD. Azure AD understøtter kun én `AuthnContextClassRef` værdi: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Angivelse af område

Den `Scoping` element, der indeholder en liste med id-udbydere, er valgfri i `AuthnRequest` elementer, der sendes til Azure AD.

Hvis, du skal ikke medtage den `ProxyCount` attribut, `IDPListOption` eller `RequesterID` element, som de ikke understøttes.

### <a name="signature"></a>Signatur

Du skal ikke medtage en `Signature` element i `AuthnRequest` elementer, som Azure AD ikke understøtter logget anmodninger om godkendelse.

### <a name="subject"></a>Emne

Azure AD ignorerer den `Subject` element i `AuthnRequest` elementer.

## <a name="response"></a>Svar

Når en anmodede enkeltlogon er fuldført, indlæg Azure AD et svar til skybaseret tjeneste. Et eksempel svar til en vellykket sign-on – forsøg på ser sådan ud:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Svar

Den `Response` elementet indeholder resultatet af anmodningen om godkendelse. Azure AD-sæt på `ID`, `Version` og `IssueInstant` værdier i den `Response` element. Den indstiller også følgende attributter:

- `Destination`: Når enkeltlogon er fuldført, dette er indstillet til den `RedirectUri` af tjenesteudbyder (skytjeneste).
- `InResponseTo`: Dette er indstillet til den `ID` attributten for de `AuthnRequest` element, der startede svaret.

### <a name="issuer"></a>Udsteder

Azure AD-sæt på `Issuer` element til `https://login.microsoftonline.com/<TenantIDGUID>/` hvor <TenantIDGUID> er Azure AD-lejer lejer ID.

Et eksempel svar med udsteder element kan for eksempel se sådan ud:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Den `Status` element giver lykkes eller mislykkes af sign-on. Den indeholder den `StatusCode` element, der indeholder en kode eller et sæt indlejrede koder, der viser status for din anmodning. Den indeholder også den `StatusMessage` element, som indeholder brugerdefinerede fejlmeddelelser, der genereres under Enkeltlogon processen.

<!-- TODO: Add a authentication protocol error reference -->

Følgende er en SAML svar til et mislykkes enkeltlogon forsøg.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Program

Ud over den `ID`, `IssueInstant` og `Version`, Azure AD angiver følgende elementer i det `Assertion` element i svaret.

#### <a name="issuer"></a>Udsteder

Dette er indstillet til `https://sts.windows.net/<TenantIDGUID>/`hvor <TenantIDGUID> er Azure AD-lejer lejer ID.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Signatur

Azure AD signerer program som svar på en vellykket sign-on. Den `Signature` element indeholder en digital signatur, som skytjenesten kan bruge til at godkende kilden for at bekræfte integriteten af program.

Hvis du vil oprette denne digitale signatur, Azure AD bruger den signerende nøgle i den `IDPSSODescriptor` element i dets metadata dokument.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Emne

Dette angiver den konto, som er omfattet af sætningerne i program. Den indeholder en `NameID` element, der repræsenterer den godkendte bruger. Den `NameID` værdi er et målrettede id, der sendes kun til den tjenesteudbyder, der er målgruppen for tokenet. Det er fast – det kan tilbagekaldes, men tildeles aldrig. Det er også uigennemsigtig, fordi det ikke viser noget om brugeren og kan ikke bruges som et id for attributten forespørgsler.

Den `Method` attributten for de `SubjectConfirmation` element er altid angivet `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Betingelser

Dette element angiver betingelser, der definerer det acceptabel brug af SAML påstande.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Den `NotBefore` og `NotOnOrAfter` attributter angive det interval, hvor program er gyldig.

- Værdien af den `NotBefore` attribut er lig med til eller lidt (mindre end et sekund) senere end værdien af `IssueInstant` attributten for de `Assertion` element. Azure AD tager ikke højde for en hvilken som helst tidspunkt forskellen mellem selve og skytjenesten (service provider), og tilføjer ikke en hvilken som helst bufferen på nuværende tidspunkt.
- Værdien af den `NotOnOrAfter` attribut er 70 minutter senere end værdien af den `NotBefore` attributter.

#### <a name="audience"></a>Målgruppe

Indeholder en URI, der identificerer et målgruppen. Azure AD angiver værdien af dette element til værdien af `Issuer` element i den `AuthnRequest` , der har startet sign-on. Evaluere den `Audience` værdi, skal du bruge værdien af den `App ID URI` , der blev angivet under registrering af programmet.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Som det `Issuer` værdi, den `Audience` værdi skal svare nøjagtigt til en af de vigtigste tjenestenavne, der repræsenterer skytjenesten i Azure AD. Men hvis værdien af den `Issuer` elementet er ikke en URI-værdi, den `Audience` værdien i svaret er den `Issuer` værdi præfikset `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Dette indeholder krav om emnet eller bruger. Den følgende uddrag indeholder en stikprøve `AttributeStatement` element. På ellipsen angiver, at elementet kan indeholde flere attributter og værdier for attributten.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Gør krav navn** : værdien af den `Name` attribut (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) er brugerens hovednavn for den godkendte bruger, f.eks `testuser@managedtenant.com`.
- **ObjectIdentifier krav** : værdien af den `ObjectIdentifier` attribut (`http://schemas.microsoft.com/identity/claims/objectidentifier`) er den `ObjectId` for objektet directory, der repræsenterer den godkendte bruger i Azure AD. `ObjectId`er en fast globalt entydige og genbruge fejlsikret id for den godkendte bruger.

#### <a name="authnstatement"></a>AuthnStatement

Dette element imperative forudsætninger, program emnet blev godkendt af en bestemt måde på et bestemt tidspunkt.

- Den `AuthnInstant` attribut angiver det tidspunkt, hvor brugeren godkendt med Azure AD.
- Den `AuthnContext` element angiver konteksten godkendelse, der bruges til at godkende brugeren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
