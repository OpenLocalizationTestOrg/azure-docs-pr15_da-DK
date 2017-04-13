<properties
    pageTitle="Azure AD-SAML Protocol Reference | Microsoft Azure"
    description="I denne artikel indeholder en oversigt over Single Sign-On og enkelt Sign-Out SAML profilerne i Azure Active Directory."
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
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Hvordan Azure Active Directory anvender SAML protokollen

Azure Active Directory (Azure AD) bruger SAML 2.0-protokol før programmer kan give en enkelt sign-on – oplevelse for brugerne. [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) og [enkelt hvor](active-directory-single-sign-out-protocol-reference.md) SAML profiler af Azure AD beskriver, hvordan SAML påstande, protokoller og bindinger bruges i tjenesten identitet udbyder.

SAML Protocol kræver identitetsudbyder (Azure AD) og tjenesteudbyder (programmet) til at udveksle oplysninger om sig selv.

Når et program er registreret med Azure AD, registrerer app-udvikleren sammenslutning-relaterede oplysninger med Azure AD. Dette omfatter **Omdirigere URI** og **Metadata URI** af programmet.

Azure AD bruger **Metadata URI** for skytjenesten til at hente den signerende nøgle og Log URI af skytjenesten. Hvis programmet ikke understøtter en metadata URI, udvikleren skal kontakte Microsoft support for at give Log URI og signering nøgle.

Azure Active Directory Fremviser lejer-specifikke og almindelige (lejer uafhængige) enkelt sign-on og enkelt hvor slutpunkter. Disse URL-adresser repræsenterer tilgængelige placeringer – de er ikke bare en id'er –, så du kan gå til slutpunktet at læse metadata.

 - Lejer-specifikke slutpunktet er placeret i `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Den <TenantDomainName> pladsholder repræsenterer et registreret domænenavn eller TenantID GUID for en Azure AD-lejer. Sammenslutning metadataene i contoso.com-lejeren er for eksempel på: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- Lejer uafhængige slutpunktet er placeret i `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. I denne adresse på slutpunkt vises **almindelige** , i stedet for en lejer domænenavn eller -ID.

Du kan finde oplysninger om de dokumenter, sammenslutning Metadata, der Azure AD publicerer [Sammenslutning Metadata](active-directory-federation-metadata.md).
