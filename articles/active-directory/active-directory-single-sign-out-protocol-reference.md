<properties
    pageTitle="Azure enkelt log af SAML Protocol | Microsoft Azure"
    description="I denne artikel beskrives de enkelte Sign-Out SAML Protocol i Azure Active Directory"
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


# <a name="single-sign-out-saml-protocol"></a>Enkelt hvor SAML protokol

Azure Active Directory (Azure AD) understøtter SAML 2.0 web browser enkelt hvor profil. For enkelt hvor for at fungere korrekt, skal Azure AD registrere metadata URL-adressen under registrering af programmet. Azure AD får log URL-adresse og den signerende nøgle i skytjenesten fra metadataene. Azure AD bruger tasten signerende til at kontrollere signaturen i de indgående LogoutRequest, og LogoutURL bruges til at omdirigere brugerne, når de har logget af.

Hvis skytjenesten ikke understøtter et slutpunkt metadata, når programmet er registreret, skal udvikleren kontakte Microsoft support for at give log URL-adresse og signerende nøgle.

Dette diagram viser processen Azure AD enkelt hvor arbejdsprocessen.

![Enkelt log af arbejdsproces](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

Skybaseret tjeneste sender en `LogoutRequest` meddelelsen til Azure AD til at angive, at en session er afsluttet. Den følgende uddrag viser et eksempel `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

Den `LogoutRequest` element, der sendes til Azure AD kræver følgende attributter:

- `ID`: Dette identificerer hvor anmodningen. Værdien af `ID` skal ikke begynde med et tal. Den typiske fremgangsmåde er at føje **id** til Strengrepræsentationen af et GUID.

- `Version`: Angive værdien af dette element til **2.0**. Denne værdi er påkrævet.

- `IssueInstant`: Dette er en `DateTime` streng med en koordinere (Coordinated Universal Time) værdi og [frem og tilbage format ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD forventer en værdi af denne type, men gennemtvinger ikke.

- Den `Consent`, `Destination`, `NotOnOrAfter` og `Reason` attributter ignoreres, hvis de findes i en `LogoutRequest` element.

### <a name="issuer"></a>Udsteder

Den `Issuer` element i en `LogoutRequest` skal svare nøjagtigt til en af **ServicePrincipalNames** i skytjenesten i Azure AD. Dette er typisk angivet til **App ID URI** , der er angivet under registrering af programmet.

### <a name="nameid"></a>NameID

Værdien af den `NameID` element skal svare nøjagtigt til den `NameID` på den bruger, der signeres ud.
## <a name="logoutresponse"></a>LogoutResponse

Azure AD-sender en `LogoutResponse` som svar på en `LogoutRequest` element. Den følgende uddrag viser et eksempel `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Azure AD-sæt på `ID`, `Version` og `IssueInstant` værdier i den `LogoutResponse` element. Angives også på `InResponseTo` element til værdien af den `ID` attributten for de `LogoutRequest` , der fremkaldes svaret.

### <a name="issuer"></a>Udsteder

Azure AD indstiller denne værdi til `https://login.microsoftonline.com/<TenantIdGUID>/` hvor <TenantIdGUID> er Azure AD-lejer lejer ID.

Evaluere værdien af den `Issuer` element, bruges værdien af **App ID URI** angivet under registrering af programmet.

### <a name="status"></a>Status

Azure AD-bruger den `StatusCode` element i den `Status` element til at angive lykkes eller mislykkes logge. Når det ikke lykkes at logge på, på `StatusCode` element kan også indeholde brugerdefinerede fejlmeddelelser.
