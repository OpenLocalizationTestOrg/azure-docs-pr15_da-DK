<properties
    pageTitle="Konfigurere automatisk registrering af Windows medlem af et domæne enheder med Azure Active Directory | Microsoft Azure"
    description="Konfigurere dit domæne, der er joinforbundne Windows enheder til at registrere automatisk og automatisk med Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Konfigurere automatisk registrering af Windows medlem af et domæne enheder med Azure Active Directory

Hvis du vil bruge [Azure Active Directory enhed-baserede betinget adgang](active-directory-conditional-access.md), skal være registreret computerne Windows medlem af et domæne med Azure Active Directory (Azure AD). I denne artikel kan du lære, hvad du skal gøre for at konfigurere registrering af Windows medlem af et domæne enheder med Azure AD i din organisation.

Brug af betinget adgang i Azure AD giver dig disse fordele:

-   Forbedret enkeltlogon (SSO) oplevelse i Azure AD-apps gennem arbejds- eller skolekonto konti
-   Enterprise globale indstillinger på tværs af enheder
-   Adgang til Windows Store til virksomheder
-   Stærkere godkendelse og praktisk logge på med Windows Hej

> [AZURE.NOTE] Opdatering til Windows 10 November indeholder nogle af de forbedrede brugeroplevelser i Azure AD, men opdateringen Windows 10 Mærkedag understøttelse fuld af enhed-baserede betinget adgang. Se [Azure Active Directory enhed-baserede betinget access](active-directory-conditional-access.md)kan finde flere oplysninger om betinget adgang. Finde flere oplysninger om Windows 10-enheder i arbejdsområdet, og hvordan en bruger registrerer en Windows 10-enhed med Azure AD [Windows 10 for virksomheden: bruge enheder til arbejde](active-directory-azureadjoin-windows10-devices-overview.md).

Du kan registrere nogle tidligere versioner af Windows, herunder disse versioner:

-   Windows 8.1
-   Windows 7

Hvis du bruger en computer med Windows Server som en computer, kan du registrere disse platforme:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Forudsætninger

Primære kravet om automatisk registrering af medlem af et domæne enheder ved hjælp af Azure AD er at have en opdateret version af Azure Active Directory Connect (Azure AD-forbindelse).

Afhængigt af hvordan du har installeret Azure AD-forbindelse, og om du har brugt en hurtig eller brugerdefineret installation eller en lokal opgradering, kan at følgende forudsætninger være konfigureret automatisk:

-   **Forbindelsespunkt for tjenesten i lokale Active Directory**. For registrering af Azure AD-lejeroplysninger ved computere, der tilmelde Azure AD.

-   **Active Directory Federation Services (AD FS) udstedelse transformere regler**. Til computergodkendelse på registrering (gælder organisationsnetværket konfigurationer).

Hvis nogle enheder i organisationen ikke er medlem af et domæne Windows 10-enheder, skal du udføre følgende opgaver:

- Angive en politik i Azure AD, så brugerne kan registrere enheder
- Angive integreret Windows godkendelse (IWA) som en gyldig alternativ til multi-Factor authentication i AD FS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Angive en tjenesteforbindelsespunkt for registrering af Azure AD-lejer

Et service connection punkt-objekt skal findes i konfigurationen navngive kontekst partition af området på det domæne, hvor der er sammenføjet computere. Forbindelsespunkt for tjenesten indeholder registrering oplysninger om Azure AD-lejer, hvor computere registrerer. I en konfiguration med flere områder Active Directory, skal de være tjenesteforbindelsespunktet i alle områder, der er medlem af et domæne computere.

Angive forbindelsespunktet service på disse placeringer i Active Directory:

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] For et område med Active Directory domain navn *example.com*, konfigurationen navngive kontekst er CN = konfiguration, DC = eksempel DC = com.

Du kan søge efter Azure AD-lejer objekt og registrering værdier ved hjælp af følgende Windows PowerShell-script. (Erstat konteksten konfiguration naming i eksemplet med din konfiguration naming kontekst).

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

Den `$scp.Keywords` output viser Azure AD-lejeroplysninger:

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Hvis tjenesteforbindelsespunktet ikke findes, skal du oprette den ved at køre følgende PowerShell-script på serveren Azure AD-forbindelse:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Når du kører `$aadAdminCred = Get-Credential`, bruge formatet *user@example.com* for brugernavnet i dialogboksen **Hent-legitimationsoplysninger** .  
> Når du kører Cmdletten initialisering ADSyncDomainJoinedComputerSync, kan du erstatte [*forbindelse kontonavn*] med den domænekonto, der bruges i Active Directory connector-konto.  
> Cmdletten bruger Active Directory-PowerShell-modul, der er afhængig af Active Directory Web Services i et domænenavn fra domænecontrolleren. Active Directory Web Services understøttes i domænecontrollere i Windows Server 2008 R2 og nyere versioner. Brug System.DirectoryServices API via PowerShell til at oprette forbindelsespunkt for tjenesten ved domæne enheder i Windows Server 2008 eller tidligere versioner, og derefter tildele nøgleord værdierne.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Oprette AD FS regler for Chat enhed registrering i organisationsnetværk organisationer

I en samlet Azure AD-konfiguration, enheder stole på AD FS (eller på sammenslutningsserveren lokalt) til at godkende Azure AD. Derefter registrere de mod Azure Active Directory enhed registrering Service (Azure AD enhed registrering Service).

> [AZURE.NOTE] I en ikke-sammenkædet konfiguration bruger adgangskode hashes er synkroniseret til Azure AD og Windows 10 og Windows Server 2016 medlem af et domæne computere godkendelse mod Azure AD enhed Registration Service. En bruger godkendes ved hjælp af en legitimationsoplysninger, som brugeren skriver til deres lokale computerkonti, og som er videresendes til Azure AD via Azure AD-forbindelse. Til Windows 10 og Windows Server 2016 computere i en ikke-sammenkædet konfiguration, skal have du muligheder for at indstille en enhed-baseret nøglecenter i organisationen. Du kan finde yderligere oplysninger finder sektion hente Windows Installer-pakker til Windows 10 computere i denne artikel.

Til Windows 10 og Windows Server 2016 computere knytter Azure AD-forbindelse Enhedsobjektet i Azure AD til objektet lokale computer-konto. Følgende krav skal findes under godkendelse til Azure AD enhed registrering tjeneste for at afslutte registrering og oprette Enhedsobjektet:

- http://schemas.Microsoft.com/ws/2012/01/AccountType indeholder DJ værdi, som identificerer vigtigste godkenderen som en computer, der er medlem af et domæne.
- http://schemas.Microsoft.com/Identity/Claims/onpremobjectguid indeholder værdien for attributten **objectGUID** på den lokale computerkonto.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/primarysid indeholder computerens primære sikkerheds-id (SID), som svarer til attributværdien **objectSid** på den lokale computerkonto.
- http://schemas.Microsoft.com/ws/2008/06/Identity/Claims/issuerid indeholder den værdi, der bruger Azure AD at have tillid til tokenet udstedt fra AD FS eller fra den lokale sikkerhed Token STS (Service). Dette er vigtigt i en konfiguration med flere områder Active Directory. I denne konfiguration kan computere være knyttet til en skov end det, der opretter forbindelse til Azure AD på AD FS eller en lokal STS. Brug værdien for AD FS, http://<*domænenavn*>/adfs/services/sikkerhed og rettighedsadministration /, som hvor <*domænenavn*> er valideret domænenavnet i Azure AD.

Brug følgende PowerShell-script for at oprette disse regler manuelt i AD FS, i en session, der er forbundet til din server. Erstat den første linje med domænenavnet valideret organisation i Azure AD.

> [AZURE.NOTE] Hvis du ikke bruger AD FS til din lokale sammenslutningsserver, skal du følge din leverandørens vejledning til at oprette regler, der udsteder disse krav.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Brug kun de første tre regler, hvis dit miljø lokalt er et enkelt område. Hvis dine computere er i en anden skov end det, synkronisering med Azure AD, eller hvis du bruger alternative navne til dem, i konfigurationen af synkronisering, skal du også medtage de resterende regler.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Windows 10 og Windows Server 2016 medlem af et domæne computere godkende ved hjælp af IWA til aktive ark, der er hostet af AD FS WS-Trust. Sørg for, at slutpunktet er aktiv. Hvis du bruger webproxyen, skal du sørge for, at dette slutpunkt er udgivet via proxyen. Slutpunktet er adfs/services/sikkerhed og rettighedsadministration/13/windowstransport. Kontrollere, om den er aktiv i administrationskonsollen AD FS skal du gå til **tjenesten** > **slutpunkter**. Hvis du ikke bruger AD FS til din lokale sammenslutningsserver, skal du følge din leverandørens vejledning for at sikre dig, at det tilsvarende slutpunkt er aktiv.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Konfigurere AD FS til godkendelse af enhed registrering

Sørg for, at IWA er angivet som et gyldigt alternativ til at Multi-Factor godkendelse af enhed registrering i AD FS. For at gøre dette, skal du have en udstedelse transformere regel, der passerer gennem godkendelsesmetoden.

1. Gå til **AD FS**i administrationskonsollen AD FS > **Sikkerhed og rettighedsadministration relationer** > **Stole part har tillid til**.

2. Højreklik på Microsoft Office 365 identitet Platform afhængige part Hav tillid Tillidsobjektet, og vælg derefter **Rediger Kræv regler**.

3.  Vælg **Tilføj regel**under fanen **Udstedelse transformere regler** .

4.  Klik på **Send krav ved hjælp af en brugerdefineret regel**på listen **Kræv regel** skabelon.

5.  Klik på **Næste**.

6.  Angiv **Auth metode Kræv regel**i feltet **regelnavn krav** .

7.  I feltet **Kræv regel** skal du skrive følgende kommando:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. Angiv denne PowerShell-kommando på din sammenslutningsserver:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> afhængige part objekt navn for Azure AD afhængige part Hav tillid Tillidsobjektet. Dette objekt er som regel navngivet *Microsoft Office 365 identitet Platform*.



## <a name="deployment-and-rollout"></a>Installation og udrulning af

Når medlem af et domæne computere opfylder forudsætningerne, er de klar til at registrere med Azure AD.

Automatisk registrere de Windows 10 Mærkedag Update og Windows Server 2016 medlem af et domæne computere med Azure AD næste gang enheden genstartes eller når en bruger logger på Windows. Nye computere, der er knyttet til domænet Registrer Azure AD, når enheden genstartes efter domæne join-handling.

> [AZURE.NOTE] Windows 10 medlem af et domæne computere registrere automatisk Azure AD kun, hvis udrulning af gruppepolitik objektet er angivet.

Du kan bruge en Gruppepolitik objekt til at styre udrulning af automatisk registrering af Windows 10 og Windows Server 2016 medlem af et domæne computere. For at udrulle automatisk registrering af Windows 10 medlem af et domæne computere, kan du installere en Windows Installer-pakke på computere, du vælger.

> [AZURE.NOTE] Gruppepolitik for udrulning af kontrolelement udløser også registrering af Windows 8.1 medlem af et domæne computere. Du kan bruge politikken til registrering af Windows 8.1 medlem af et domæne computere. Eller hvis du har en blanding af Windows-versioner, herunder versioner af Windows 7 eller Windows Server, kan du registrere alle dine Windows 10 og Windows Server 2016 computere ved hjælp af en Windows Installer-pakke.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Oprette en Gruppepolitik objekt for at styre implementeringen af automatisk registrering

For at styre udrulning af automatisk registrering af medlem af et domæne computere med Azure AD, kan du installere Gruppepolitik **registrere computere, der er medlem af et domæne, som enheder** til de computere, du vil registrere. Du kan for eksempel installere politikken med en organisationsenhed eller til en sikkerhedsgruppe.

Hvis du vil angive politikken, skal du udføre disse trin:

1. Åbn Server Manager, og derefter gå til **værktøjer** > **Administration af gruppepolitik**.

2. Gå til domænenoden, der svarer til det domæne, hvor du vil aktivere automatisk registrering af Windows 10 eller Windows Server 2016 computere.

3. Højreklik på **Gruppepolitikobjekter**, og vælg derefter **Ny**.

4. Angiv et navn til objektet Gruppepolitik. For eksempel *Automatisk registrering til Azure AD*. Klik på **OK**.

4. Højreklik på dit nye Gruppepolitik objekt, og vælg derefter **Rediger**.

5. Gå til **Computerkonfiguration** > **politikker** > **Administrative skabeloner** > **Windows-komponenter** > **enhed registrering**. Højreklik på **Register domæne joinforbundne computere som enheder**, og vælg derefter **Rediger**.

    > [AZURE.NOTE] Denne skabelon til en gruppepolitik er blevet omdøbt fra tidligere versioner af konsollen Administration af gruppepolitik. Hvis du bruger en tidligere version af konsollen, gå til **Konfiguration computeren** > **politikker** > **Administrative skabeloner** > **Windows-komponenter** > **Arbejdsplads joinforbindelse** > **automatisk arbejdsplads join-klientcomputere**.

6. Vælg **aktiveret**, og vælg derefter **Anvend**.

7. Klik på **OK**.

8. Sammenkæde Gruppepolitik objektet til et sted, hvor dit valg. For eksempel kan du knytte den til en bestemt organisationsenhed. Du kan også sammenkæde det med en bestemt sikkerhedsgruppe af computere, der registrerer automatisk med Azure AD. For at angive denne politik for alle medlem af et domæne Windows 10 og Windows Server 2016 computere i din organisation, skal du sammenkæde Gruppepolitik objektet til domænet.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Hent Windows Installer-pakker til Windows 10 computere  

Hvis du vil registrere medlem af et domæne computere, der kører Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2, kan du hente og installere filerne Windows Installer-pakke (.msi):

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Installer pakken ved hjælp af et software fordeling system som System Center Configuration Manager. Pakken understøtter indstillingerne uovervåget standardinstallation med parameteren *stille* . System Center Configuration Manager 2016 giver yderligere fordele fra tidligere versioner, som muligheden for at spore færdige registreringer. Se [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)kan finde flere oplysninger.

Installationsprogrammet opretter en planlagt opgave på systemet, der kører i brugerens kontekst. Opgaven udløses, når brugeren logger ind på Windows. Opgaven registrerer automatisk enheden med Azure AD med brugerens legitimationsoplysninger efter godkendelse gennem IWA. For at se den planlagte opgave skal du gå til **Microsoft** > **Arbejdsplads deltage i**, og gå derefter til biblioteket Opgavestyring.



## <a name="next-steps"></a>Næste trin

- [Azure Active Directory-betinget adgang](active-directory-conditional-access.md)
