<properties
    pageTitle="Oprette forbindelse medlem af et domæne enheder til Azure AD til Windows 10 har | Microsoft Azure"
    description="Forklarer, hvordan administratorer kan konfigurere Gruppepolitik for at aktivere enheder til at være medlem af et domæne til virksomhedens netværk."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser

Opret forbindelse til domænet er de traditionelle metode organisationer har tilsluttet enheder til arbejde for de sidste 15 år og meget mere. Det har aktiveret brugere at logge på deres enheder ved hjælp af Windows Server Active Directory (Active Directory) arbejdet eller skolen konti og tilladt IT for at administrere disse enheder. Organisationer bruger typisk afbildning metoder til at klargøring enheder til brugere og generelt Brug Center Configuration Manager SCCM (System) eller en gruppepolitik til at administrere dem.

Forbindelse til et domæne i Windows 10 indeholder følgende fordele, når du opretter forbindelse enheder til Azure Active Directory (Azure AD):

- Enkeltlogon (SSO) til Azure AD ressourcer fra et vilkårligt sted
- Adgang til enterprise Windows Store ved hjælp af arbejds- eller skolekonto konti (ingen Microsoft-konto, der har påkrævet)
- Virksomhed-kompatibel roaming af brugerindstillinger på tværs af enheder ved hjælp af arbejds- eller skolekonto konti (ingen Microsoft-konto, der har påkrævet)
- Stærke godkendelse og praktisk-logon til arbejds- eller skolekonto konti med Microsoft Passport og Windows Hej
- Muligheden for at begrænse adgangen kun til enheder, der er i overholder med indstillinger for Gruppepolitik organisatoriske enhed

## <a name="prerequisites"></a>Forudsætninger

Opret forbindelse til domænet stadig være nyttige. Men hvis du vil hente Azure AD fordelene ved SSO, globale indstillinger med arbejds- eller skolekonti, og Åbn Windows Store med arbejds- eller skolekonto konti, skal du bruge følgende:

- Azure AD-abonnement
- Azure AD-forbindelse til at udvide den lokale mappe til Azure AD
- Politik, der er ved at oprette forbindelse medlem af et domæne enheder til Azure AD
- Windows 10 build (build 10551 eller nyere) til enheder

Hvis du vil aktivere Microsoft Passport til arbejde og Windows Hej, skal du også følgende:

- Offentlig nøgle infrastruktur for bruger certifikater udstedelse.
- System Center Configuration Manager version 1509 til Technical Preview. Yderligere oplysninger finder du se [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) og [System Center Configuration Manager-teamets Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Dette er påkrævet for at installere brugercertifikater, der er baseret på Microsoft Passport taster.

Som et alternativ til kravet PKI installation, kan du gøre følgende:

- Have et par domænecontrollere med Windows Server 2016 Active Directory Domain Services.

Hvis du vil aktivere betinget adgang, kan du oprette indstillinger for Gruppepolitik, der giver adgang til medlem af et domæne enheder med ingen yderligere installationer. Hvis du vil administrere adgangskontrol baseret på overholdelse af enheden, skal du følgende:

- System Center Configuration Manager version 1509 til Technical Preview for Passport scenarier

## <a name="deployment-instructions"></a>Installationsvejledning



### <a name="step-1-deploy-azure-active-directory-connect"></a>Trin 1: Installere Azure Active Directory forbindelse

Azure AD-forbindelse gør det muligt at klargøre lokale computere som enhedsobjekter i skyen. Hvis du vil installere Azure AD-forbindelse, du referere til "Installer Azure AD Connect" i artiklen [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

 - Hvis du har fulgt en [brugerdefineret installation til Azure AD-forbindelse](./connect/active-directory-aadconnect-get-started-custom.md) (ikke hurtig installation), skal du følge fremgangsmåden, **oprette en tjeneste forbindelsespunkt i lokale Active Directory**, senere i dette trin.
 - Hvis du har en samlet konfiguration med Azure AD før du installerer Azure AD-forbindelse (for eksempel, hvis du har installeret Active Directory Federation Services (AD FS) før), derefter den fremgangsmåde **Konfigurer AD FS Kræv regler** , senere i dette trin.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Oprette et forbindelsespunkt for tjenesten i lokale Active Directory

Medlem af et domæne enheder anvender forbindelsespunkt for tjenesten til at finde Azure AD-lejeroplysninger på tidspunktet for automatisk registrering med tjenesten Azure enhed registrering.

Kør følgende PowerShell-kommandoer på serveren Azure AD-forbindelse:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Når du kører cmdlet $aadAdminCred = Get-legitimationsoplysninger, bruge formatet *user@example.com* for brugernavnet på de legitimationsoplysninger, der er angivet, når pop op-vinduet Get-legitimationsoplysninger vises.

Når du kører cmdlet initialisering ADSyncDomainJoinedComputerSync..., kan du erstatte [*forbindelse kontonavn*] med den domænekonto, der bruges som Active Directory connector-konto.

#### <a name="configure-ad-fs-claim-rules"></a>Konfigurere AD FS Kræv regler
Konfigurere AD FS Kræv regler aktiverer omgående registrering af en computer med Azure enhed registreringstjenesten ved at tillade computere at godkende ved hjælp af Kerberos/NTLM via AD FS. Uden dette trin skal får computere til Azure AD i en forsinket måde (underlagt Azure AD-forbindelse Synkroniser gange).

>[AZURE.NOTE]
Hvis du ikke har AD FS som sammenslutning server lokalt, Følg instruktionerne i din leverandør af til at oprette reglerne, der gør krav.

På AD FS-server (eller på en session med forbindelse til serveren for AD FS), kan du køre følgende PowerShell-kommandoer:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Windows 10 computere kan godkende ved hjælp af Windows-integreret godkendelse til WS-Trust aktive ark hostet af AD FS. Sørg for, at dette slutpunkt er aktiveret. Hvis du bruger Web godkendelse af proxyen, også sørge for, at dette slutpunkt er udgivet via proxyen. Du kan gøre dette ved at markere adfs/services/trust/13/windowstransport. Det skal vises som aktiveret i AD FS management console under **tjeneste** > **slutpunkter**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Trin 2: Konfigurer automatisk enhed registrering via gruppepolitik i Active Directory

Du kan bruge Gruppepolitik i Active Directory til at konfigurere dine Windows 10 enheder medlem af et domæne til at registrere automatisk med Azure AD.

> [AZURE.NOTE]
> Seneste oplysninger om, hvordan du konfigurerer automatisk enhed registrering se, [hvordan du konfigurerer automatisk registrering af Windows-domæne joinforbundne enheder med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Denne skabelon til en gruppepolitik er blevet omdøbt i Windows 10. Hvis du kører værktøjet Gruppepolitik fra en computer med Windows 10, vises politikken som: <br>
> **Registrere domæne tilsluttet computere som enheder**<br>
> Politikken er på følgende placeringer:<br>
> ***Computeren konfiguration/politikker/Administrative skabeloner/Windows komponenter/enhed registrering***


## <a name="additional-information"></a>Yderligere oplysninger
* [Windows 10 for virksomheden: måder at bruge enheder for arbejde](active-directory-azureadjoin-windows10-devices-overview.md)
* [Hvis du vil udvide skyen funktioner til Windows 10-enheder via Azure Active Directory deltage](active-directory-azureadjoin-user-upgrade.md)
* [Få mere at vide om brugsscenarier til Azure AD deltage](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tilslutte enheder, der er medlem af et domæne til Azure AD til Windows 10 oplevelser](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurere Azure AD deltage](active-directory-azureadjoin-setup.md)
