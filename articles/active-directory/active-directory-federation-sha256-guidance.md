<properties
    pageTitle="Rediger signatur hashalgoritme for sikkerhed og rettighedsadministration besvare part i Office 365 | Microsoft Azure"
    description="Denne side indeholder retningslinjer til at ændre SHA algoritme til sammenslutning tillid med Office 365"
    keywords="SHA1, SHA256, O365, sammenslutning aadconnect adfs, ad fs, og Skift sha sammenslutning sikkerhed og rettighedsadministration, stole part Hav tillid til"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="samueld"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="change-signature-hash-algorithm-for-office-365-replying-party-trust"></a>Ændre signatur hashalgoritme for Office 365 besvarelse part Hav tillid til

## <a name="overview"></a>Oversigt

Azure Active Directory Federation Services (AD FS) signerer dens tokens til Microsoft Azure Active Directory til at sikre, at de ikke kan være ændret. Denne signatur kan være baseret på SHA1 eller SHA256. Azure Active Directory understøtter nu tokens, der er signeret med en SHA256 algoritme, og vi anbefaler, at indstille algoritmen signering med token til SHA256 for det højeste niveau af sikkerhed. I denne artikel beskrives de trin, der er behov for at angive den signering med token algoritme til mere sikker SHA256.

## <a name="change-the-token-signing-algorithm"></a>Ændre algoritmen signering med token

Når du har angivet algoritmen signatur med et af de to processer nedenfor, logger AD FS tokens til Office 365 stole part tillid med SHA256. Du behøver ikke at foretage konfigurationsændringer i ekstra, og denne ændring har ingen indvirkning på din mulighed for at få adgang til Office 365 eller andre Azure AD-programmer.

### <a name="ad-fs-management-console"></a>AD FS-administrationskonsollen

1. Åbne AD FS management console på den primære AD FS-server.
2. Udvid noden AD FS, og klik på **Stole part har tillid til**.
3. Højreklik på din Office 365/Azure afhængige part Hav tillid til, og vælg **Egenskaber**.
4. Vælg fanen **Avanceret** , og vælg secure hashalgoritmen SHA256.
5. Klik på **OK**.

![SHA256 signerende algoritme – MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-cmdletter

1. Åbn PowerShell under administratorrettigheder på en hvilken som helst AD FS-server.
2. Angive secure hashalgoritmen ved hjælp af cmdlet'en **Set-AdfsRelyingPartyTrust** til.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Også læse

* [Reparere Office 365 trust med Azure AD-forbindelse](./active-directory-aadconnect-federation-management.md#repairing-the-trust)
