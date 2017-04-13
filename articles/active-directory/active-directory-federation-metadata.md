<properties
    pageTitle="Azure AD sammenslutning Metadata | Microsoft Azure"
    description="I denne artikel beskrives sammenslutning Metadatadokumentet, hvor der publiceres Azure Active Directory for tjenester, der accepterer Azure Active Directory tokens."
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


# <a name="federation-metadata"></a>Sammenslutning metadata

Azure Active Directory (Azure AD) publicerer et dokument til sammenslutning metadata for tjenester, der er konfigureret til at acceptere de sikkerhedstokens, som Azure AD-problemer. Sammenslutning metadata dokumentformatet er beskrevet i [Web Services sammenslutning sprog (WS-sammenslutning) Version 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), som udvider [metadataene for OASIS sikkerhed program Markup Language (SAML) v2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Lejer-specifikke og lejer uafhængige metadata slutpunkter

Azure AD publicerer lejer-specifikke og lejer uafhængige slutpunkter.

Lejer-specifikke slutpunkter er udviklet til en bestemt lejer. Lejer-specifikke sammenslutning metadata indeholder oplysninger om lejeren, herunder oplysninger om lejer-specifikke udsteder og slutpunkt. Programmer, der begrænser adgangen til en enkelt lejer bruge lejer-specifikke slutpunkter.

Lejer uafhængige slutpunkter indeholder oplysninger, der er fælles for alle Azure AD-lejere. Disse oplysninger gælder for lejere, der er hostet hos *login.microsoftonline.com* og deles på tværs af lejere. Lejer uafhængige slutpunkter anbefales til flere lejer programmer, da de ikke er knyttet til en bestemt lejer.

## <a name="federation-metadata-endpoints"></a>Sammenslutning metadata slutpunkter

Azure AD publicerer sammenslutning metadata på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

For **lejeren-specifikke slutpunkter**, den `TenantDomainName` kan være en af følgende typer:

- Et registreret domænenavn på en Azure AD lejer, f.eks.: `contoso.onmicrosoft.com`.
- Den fast lejer-ID'ET for domænet, f.eks `72f988bf-86f1-41af-91ab-2d7cd011db45`.

For **lejeren uafhængige slutpunkter**, den `TenantDomainName` er `common`. Dette dokument vises kun de sammenslutning Metadata elementer, der er fælles for alle Azure AD-lejere, der er hostet hos login.microsoftonline.com.

For eksempel en lejer-specifikke slutpunkt kan være `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Lejer uafhængige slutpunkt er [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Du kan få vist sammenslutning Metadatadokumentet ved at skrive denne URL-adresse i en browser.

## <a name="contents-of-federation-metadata"></a>Indholdet af sammenslutning Metadata

Følgende afsnit indeholder oplysninger, der bruges til tjenester, der forbruger tokens udstedt af Azure AD.

### <a name="entity-id"></a>Enheds-ID

Den `EntityDescriptor` element indeholder en `EntityID` attributter. Værdien af den `EntityID` attribut repræsenterer udsteder, det vil sige, tjenesten for sikkerhedstoken (STS), der har udstedt tokenet. Det er vigtigt at validere udstederen, når du modtager et token.

Følgende metadata viser et eksempel lejer-specifikke `EntityDescriptor` element med en `EntityID` element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Du kan erstatte lejer-ID på lejer uafhængige slutpunktet med din lejer ID for at oprette en lejer-specifikke `EntityID` værdi. Den resulterende værdi være den samme som den token udsteder. Strategien, der kan bruges med flere lejer programmer til at validere udsteder for en given lejer.

Følgende metadata viser et eksempel lejer uafhængige `EntityID` element. Vær opmærksom på, som den `{tenant}` er en konstant, ikke en pladsholder.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Token signerende certifikater

Når en tjeneste modtager et token, som er udstedt af en Azure AD-lejer, skal signaturen for tokenet godkendes med en signerende nøgle, der er blevet publiceret i sammenslutning Metadatadokumentet. Sammenslutning metadata indeholder den offentlige del af certifikater, der bruger lejere til token signering. De rå byte certifikat vises i den `KeyDescriptor` element. Tokenet signeringscertifikat er gyldig til signering kun, når værdien af den `use` attribut er `signing`.

En sammenslutning metadata dokument, der er udgivet af Azure AD kan have flere signerende taster, som når Azure AD gøres klar til at opdatere det certifikat. Når en sammenslutning metadata dokument indeholder mere end ét certifikat, skal en tjeneste, som validere tokens understøtter alle certifikater i dokumentet.

Følgende metadata viser et eksempel `KeyDescriptor` element med en signerende nøgle.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Den `KeyDescriptor` elementet vises med to forskellige steder i sammenslutning Metadatadokumentet. i sektionen WS-sammenslutning-specifikke og sektionen SAML-specifikke. Certifikaterne udgivet i begge sektioner vil være den samme.

I sektionen WS-sammenslutning-specifikke, ville en WS-Federation metadata læser læse certifikater fra en `RoleDescriptor` element med den `SecurityTokenServiceType` type.

Følgende metadata viser et eksempel `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

I sektionen SAML-specifikke, ville en WS-Federation metadata læser læse certifikater fra en `IDPSSODescriptor` element.

Følgende metadata viser et eksempel `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Der er ingen forskelle i formatet af lejer-specifikke og lejer uafhængige certifikater.

### <a name="ws-federation-endpoint-url"></a>WS-Federation slutpunkt URL-adresse

Sammenslutning metadata indeholder URL-adressen, der er Azure AD-brug af enkelt logon og enkelt logge i WS-Federation protocol. Dette slutpunkt vises i den `PassiveRequestorEndpoint` element.

Følgende metadata viser et eksempel `PassiveRequestorEndpoint` element til en lejer-specifikke slutpunkt.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
For slutpunkt lejer uafhængige vises WS-Federation URL-adressen i slutpunktet WS-sammenslutning, som vist i følgende eksempel.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML protocol slutpunkt URL-adresse

Sammenslutning metadata indeholder URL-adressen, der bruger Azure AD for enkelt logon og enkelt logge i SAML 2.0-protokollen. Disse slutpunkterne vises i den `IDPSSODescriptor` element.

Log på og logge URL vises i den `SingleSignOnService` og `SingleLogoutService` elementer.

Følgende metadata viser et eksempel `PassiveResistorEndpoint` for en lejer-specifikke slutpunkt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

På samme måde udgivet slutpunkterne for almindelige SAML 2.0-protokollen slutpunkter i metadata lejer uafhængige sammenslutning, som vist i følgende eksempel.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
