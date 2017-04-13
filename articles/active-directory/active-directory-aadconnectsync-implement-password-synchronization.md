<properties
    pageTitle="Implementere synkronisering af adgangskoder til Azure AD-forbindelse synkronisering | Microsoft Azure"
    description="Indeholder oplysninger om hvordan fungerer synkronisering af adgangskoder, og hvordan du aktiverer den."
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
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementere synkronisering af adgangskoder til Azure AD-forbindelse synkronisering
Dette emne indeholder de oplysninger, du har brug at synkronisere din brugeradgangskoder fra et lokalt Active Directory (AD) til en skybaseret Azure Active Directory (Azure AD).

## <a name="what-is-password-synchronization"></a>Hvad er synkronisering af adgangskoder
Sandsynligheden for, at du blokeres fra at udføre dit arbejde på grund af en glemt adgangskode er relateret til antallet af forskellige adgangskoder, du skal huske. Flere adgangskoderne skal du huske, jo højere sandsynligheden til at glemme en. Spørgsmål og opkald om nulstilling af adgangskoder og andre adgangskode-relaterede problemer kræver i de fleste helpdesk-ressourcer.

Synkronisering af adgangskoder er en funktion til at synkronisere brugeradgangskoder fra et lokalt Active Directory til en skybaseret Azure Active Directory (Azure AD).
Denne funktion gør det muligt at logge på Azure Active Directory-tjenester (såsom Office 365, Microsoft Intune, CRM Online og Azure AD-domænetjenester) ved hjælp af den samme adgangskode, du bruger til at logge på din lokale Active Directory.

![Hvad er Azure AD-forbindelse](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Ved at reducere antallet af adgangskoder, som brugerne har brug for til kun én synkronisering af adgangskoder hjælper dig med at:

- Forbedre produktiviteten dine brugere
- Reducere omkostningerne din helpdesk  

Hvis du vælger for at bruge [**sammenslutning med AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), kan du også du kan også aktivere synkronisering af adgangskoder som en sikkerhedskopi i tilfælde af infrastrukturen AD FS mislykkes.

Synkronisering af adgangskoder er en udvidelse til directory-synkronisering funktion implementeret ved synkronisering af Azure AD-forbindelse. Hvis du vil bruge synkronisering af adgangskoder i dit miljø, skal du:

- Installer Azure AD-forbindelse  
- Konfigurere katalogsynkronisering mellem dine lokale AD og din Azure Active Directory
- Aktivere synkronisering af adgangskoder

Yderligere oplysninger finder du [integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Se [synkronisering af adgangskoder og FIPS](#password-synchronization-and-fips)kan finde flere oplysninger om Active Directory Domain Services, der er konfigureret til synkronisering af FIPS og adgangskode.

## <a name="how-password-synchronization-works"></a>Sådan fungerer synkronisering af adgangskoder
Tjenesten Active Directory domain gemmer adgangskoder i form af en hash-værdi repræsentation af faktisk brugeradgangskode. En hashværdi er et resultat af en envejs matematisk funktion (den "*krypteres algoritme*"). Der findes ingen metode til at gendanne resultatet af en envejs funktion til almindelig tekst-version af en adgangskode. Du kan ikke bruge en adgangskode hash til at logge på dit lokale netværk.

For at synkronisere din adgangskode, henter Azure AD-forbindelse Synkroniser din adgangskode hash fra lokale Active Directory. Behandling af ekstra sikkerhed anvendes på giver adgangskode resultat, før den er synkroniseret til tjenesten Azure Active Directory-godkendelse. Adgangskoder synkroniseres på hver bruger og i kronologisk rækkefølge.

Faktisk dataflow af synkroniseringen adgangskode minder om synkronisering af brugerdata som vist navn eller mailadresser. Men synkroniseres adgangskoder hyppigere end vinduet standard directory-synkronisering for andre attributter. Processen til adgangskode synkronisering kører hver 2 minutter. Du kan ikke ændre hyppigheden for denne proces. Når du synkroniserer en adgangskode, overskriver den eksisterende adgangskode i skyen.

Første gang, du aktiverer funktionen adgangskode synkronisering, udfører den en indledende synkronisering af adgangskoder for alle brugere i omfang. Du kan ikke eksplicit definere et undersæt af brugernes adgangskoder, du vil synkronisere.

Når du ændrer en lokal adgangskode, er den opdaterede adgangskode synkroniseret, oftest bruger i løbet af få minutter.
Funktionen adgangskode synkronisering forsøg automatisk af mislykkede synkroniseringsforsøg. Hvis der opstår en fejl under et forsøg på at synkronisere en adgangskode, logføres fejlen i din Logbog.

Synkronisering af en adgangskode har ingen indvirkning på den bruger i øjeblikket er logget på.
Den aktuelle sky tjenesten session påvirkes ikke af en ændring af synkroniserede adgangskode, der opstår, mens du er logget på til en skybaseret tjeneste med det samme. Men, når skytjenesten kræver dig med at godkende igen, skal du angive den nye adgangskode.

> [AZURE.NOTE] Synkronisering af adgangskoder understøttes kun for den type objekt bruger i Active Directory. Det understøttes ikke for iNetOrgPerson objekttypen.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Sådan fungerer synkronisering af adgangskoder med Azure AD-domænetjenester
Du kan også bruge synkroniseringsfunktionen adgangskode til at synkronisere dine lokale adgangskoder til [Azure AD-domænetjenester](../active-directory-domain-services/active-directory-ds-overview.md). Dette scenario giver Azure AD Domain Services til at godkende dine brugere i skyen med alle de metoder, der er tilgængelige i dine lokale AD. Opleve dette scenario er svarer til at bruge Active Directory Migration værktøjet (ADMT) i et lokalt miljø.

### <a name="security-considerations"></a>Overvejelser om sikkerheden
Når du synkroniserer adgangskoder, vises ikke almindelig tekst-version af din adgangskode til synkroniseringsfunktionen adgangskode til Azure AD, eller nogen af de tilknyttede tjenester.

Også, der er ingen krav på lokale Active Directory til at gemme adgangskoden i et kan dekrypteres krypteret format. En samlet giver Active Directory adgangskode resultat bruges til transmission mellem lokale AD og Azure Active Directory. Samlet af giver adgangskode resultat kan ikke bruges til at få adgang til ressourcer i dit lokale miljø.

### <a name="password-policy-considerations"></a>Overvejelser i forbindelse med adgangskode politik
Der findes to typer adgangskodepolitikker, der påvirkes ved at aktivere synkronisering af adgangskoder:

1. Kompleksitet adgangskodepolitik
2. Udløbspolitik for adgangskode

**Kompleksitet adgangskodepolitik**  
Når du aktiverer synkronisering af adgangskoder, tilsidesætter kompleksitet adgangskodepolitikker i din lokale Active Directory kompleksitet politikker i skyen for synkroniserede brugere. Du kan bruge alle gyldige adgangskoder i din lokale Active Directory til at få adgang til Azure AD-tjenester.

> [AZURE.NOTE] Adgangskoder for brugere, der er oprettet direkte i skyen er stadig underlagt adgangskodepolitikker, som defineret i skyen.

**En udløbspolitik for adgangskode**  
Hvis en bruger er omfattet af synkronisering af adgangskoder, er adgangskoden til skyen angivet til "*Aldrig at udløbe*".
Du kan fortsætte med at logge på din skytjenester, ved hjælp af en synkroniseret adgangskode, som er blevet udløbet i dit lokale miljø. Adgangskoden skyen er opdateret, næste gang du ændrer adgangskode i det lokale miljø.

### <a name="overwriting-synchronized-passwords"></a>Overskrive synkroniseret adgangskoder
En administrator kan manuelt nulstille din adgangskode, ved hjælp af Windows PowerShell.

I dette tilfælde den nye adgangskode tilsidesætter din synkroniserede adgangskode, og alle adgangskodepolitikker, der er defineret i skyen anvendes på den nye adgangskode.

Hvis du ændrer din lokale adgangskode igen, den nye adgangskode er synkroniseret til skyen og tilsidesætter den manuelt opdaterede adgangskode.

## <a name="enabling-password-synchronization"></a>Aktivere synkronisering af adgangskoder
Synkronisering af adgangskoder er automatisk aktiveret, når du installerer Azure AD-forbindelse ved hjælp af **Express indstillinger**. Få mere at vide i [Introduktion til Azure AD-forbindelse ved hjælp af hurtig indstillinger](./connect/active-directory-aadconnect-get-started-express.md).

Hvis du bruger brugerdefinerede indstillinger, når du installerer Azure AD-forbindelse, kan du aktivere synkronisering af adgangskoder på siden bruger-logon. Du kan finde flere detaljer, [brugerdefineret installation af Azure AD-forbindelse](./connect/active-directory-aadconnect-get-started-custom.md).

![Aktivere synkronisering af adgangskoder](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Synkronisering af adgangskoder og FIPS
Hvis serveren er blevet låst ifølge national FIPS Information Processing Standard (), er derefter MD5 blevet deaktiveret.

**Aktivere MD5 til synkronisering af adgangskoder, skal du udføre følgende trin:**

1. Gå til **%programfiles%\Azure AD Sync\Bin**.
2. Åbn **miiserver.exe.config**.
3. Gå til noden **konfiguration/runtime** (i slutningen af filen).
4. Tilføj følgende node:`<enforceFIPSPolicy enabled="false"/>`
5. Gem dine ændringer.

Til reference er dette klip hvordan det skal se sådan ud:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Finde oplysninger om sikkerhed og FIPS i [AAD synkronisering af adgangskoder, kryptering og FIPS overholdelse](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Fejlfinding i forbindelse med synkronisering af adgangskoder
Hvis adgangskoder ikke synkroniserer som forventet, kan det enten være et undersæt af brugere eller for alle brugere.

- Hvis du har et problem med enkelte objekter, kan du se [fejlfinding i forbindelse med ét objekt, der ikke synkronisering af adgangskoder](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Hvis du har et problem, hvor ingen adgangskoder synkroniseres skal du se [fejlfinding på problemer hvor ingen adgangskoder synkroniseres](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Fejlfinding i forbindelse med ét objekt, der ikke synkronisering af adgangskoder
Du kan nemt foretage fejlfinding af problemer med synkronisering af adgangskoder ved at gennemgå status for et objekt.

Start i **Active Directory-brugere og computere**. Find brugeren, og Bekræft, at **brugeren skal skifte adgangskode ved næste logon** er valgt.
![Active Directory produktiv adgangskoder](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Hvis det er markeret, derefter Bed brugeren om at logge på og ændre adgangskoden. Midlertidige adgangskoder er ikke synkroniseret til Azure AD.

Hvis den korrekte i Active Directory, er næste trin at følge brugeren i programmet til synkronisering. Ved at følge brugeren fra lokale Active Directory Azure AD, kan du se, om der er en beskrivende fejl på objektet.

1. Starte **[Synkronisering Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Klik på **forbindelser**.
3. Vælg **Active Directory Connector** brugeren er placeret i.
4. Vælg **Søg Connector-plads**.
5. Find den bruger, du leder efter.
6. Vælg fanen **om datakildeafstamning** , og Sørg for, at mindst én regel for synkronisering viser **Synkronisering af adgangskoder** som **Sand**. I standardkonfiguration, navnet på reglen synkronisering er **i fra AD - bruger AccountEnabled**.  
    ![Oplysninger om datakildeafstamning om en bruger](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. Derefter [skal du følge brugeren](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) gennem metaverse til sted i Azure AD-forbindelse. Objektet forbindelse mellemrum skal have en udgående regel til **Synkronisering af adgangskoder** , der er indstillet til **Sand**. I standardkonfiguration er navnet på reglen Synkroniser **ud til AAD - brugere deltage i**.  
    ![Egenskaber for forbindelse for en bruger](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. For at se adgangskode Synkroniser oplysninger for objektet for den seneste uge, skal du klikke på **Log. …**.  
    ![Objekt logoplysninger](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

Statuskolonnen kan have følgende værdier:

Status | Beskrivelse
---- | -----
Succes | Adgangskoden er blevet synkroniseret.
FilteredByTarget | Adgangskode er konfigureret til **brugeren skal skifte adgangskode ved næste logon**. Adgangskode er ikke blevet synkroniseret.
NoTargetConnection | Ingen objekter i metaverse eller i Azure AD-forbindelse mellemrum.
SourceConnectorNotPresent | Ingen objekter, der findes i det lokale Active Directory connector område.
TargetNotExportedToDirectory | Objektet i Azure AD connector-plads har endnu ikke eksporteret.
MigratedCheckDetailsForMoreInfo | Post i logfilen blev oprettet før build 1.0.9125.0 og vises i den ældre tilstand.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Foretage fejlfinding af problemer, hvor ingen adgangskoder synkroniseres
Start ved at køre scriptet i sektionen [Hent status for adgangskode synkroniseringsindstillinger](#get-the-status-of-password-sync-settings). Det giver dig et overblik over adgangskode synkronisering af konfigurationen.  
![PowerShell-script output fra adgangskode synkroniseringsindstillinger](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Hvis funktionen ikke er aktiveret i Azure AD, eller hvis kanal synkroniseringsstatus ikke er aktiveret, derefter køre guiden Opret forbindelse installation. Vælg **Tilpas indstillinger for synkronisering** , og fjern markeringen af synkronisering af adgangskoder. Denne ændring deaktiverer midlertidigt funktionen. Derefter køre guiden igen og igen Aktivér synkronisering af adgangskoder. Kør scriptet igen for at bekræfte, at konfigurationen er korrekte.

Hvis scriptet viser, at er der ingen godkendelse gyldighed, derefter køre scriptet i [udløse en komplet synkronisering af alle adgangskoder](#trigger-a-full-sync-of-all-passwords). Dette script kan også bruges til andre scenarier, hvor konfigurationen er korrekt, men som ikke er synkroniseret adgangskoder.

#### <a name="get-the-status-of-password-sync-settings"></a>Hent status for adgangskode synkroniseringsindstillinger

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Udløse en komplet synkronisering af alle adgangskoder
Du kan udløse en komplet synkronisering af alle adgangskoder ved hjælp af følgende script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Næste trin

* [Azure Active Directory forbinde-synkronisering: Tilpasse indstillinger for synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrere dine lokale identiteter med Azure Active Directory](active-directory-aadconnect.md)
