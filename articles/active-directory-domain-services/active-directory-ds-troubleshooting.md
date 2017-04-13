<properties
    pageTitle="Azure Active Directory-domænetjenester: Fejlfindingsvejledningen | Microsoft Azure"
    description="Fejlfinding i forbindelse med vejledning til Azure AD-domænetjenester"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - fejlfindingsvejledningen
I denne artikel finder tip til fejlfinding af de problemer, der kan opstå, når du konfigurerer eller administration af Azure Active Directory (AD) Domain Services.


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan ikke aktivere Azure AD Domain Services for dine Azure AD-mappe
I dette afsnit får du foretager fejlfinding af fejl, når du forsøger at aktivere Azure AD-domænetjenester til adresselisten og mislykkes eller skifter tilbage til 'Deaktiveret'.

Vælg de fejlfindingstrin, der svarer til en fejlmeddelelse om, du støder på.

|**Fejlmeddelelse**|**Opløsning**|
|---|:---|
|*Navn contoso100.com er allerede i brug på dette netværk. Angiv et navn, der ikke er i brug.*|[Domæne navnekonflikt i det virtuelle netværk](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Tjenesten har ikke tilstrækkelige tilladelser til det program, kaldet 'Azure AD-domæne Services Synkroniser'. Slette det program, der hedder 'Azure AD-domæne Services Synkroniser', og prøv derefter at aktivere Domain Services for din Azure AD-lejer.*|[Domain Services har ikke tilstrækkelige tilladelser til programmet Azure AD-domæne Services synkronisering](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Programmet Domain Services i din Azure AD-lejer har ikke de nødvendige tilladelser til at aktivere Domain Services. Slette programmet med program-id d87dcbc6-a371-462e-88e3-28ad15ec4e64, og prøv derefter at aktivere Domain Services for din Azure AD-lejer.*|[Programmet Domain Services er ikke konfigureret korrekt i din lejer](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Microsoft Azure AD-programmet er deaktiveret i din Azure AD-lejer. Aktivere programmet med program-id 00000002-0000-0000-c000-000000000000, og prøv derefter at aktivere Domain Services for din Azure AD-lejer.*|[Programmet Microsoft Graph er deaktiveret i din Azure AD-lejer](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Domæne navnekonflikter
**Fejlmeddelelse:**

*Navn contoso100.com er allerede i brug på dette netværk. Angiv et navn, der ikke er i brug.*

**Afhjælpning:**

Sørg for, at du ikke har et eksisterende domæne med det samme domænenavn, der er tilgængelige på det pågældende virtuelle netværk. For eksempel Antag, at du har et domæne kaldet 'contoso.com' allerede findes i den valgte virtuelt netværk. Senere, forsøger du at aktivere en Azure AD-domænetjenester administrerede domæne med det samme domænenavn (det vil sige, ' contoso.com') på det pågældende virtuelle netværk. Der opstår en fejl, når du forsøger at aktivere Azure AD-domænetjenester.

Denne fejl er på grund af navnekonflikter for domænenavnet på det pågældende virtuelle netværk. I dette tilfælde skal du bruge et andet navn til at konfigurere domænet Azure AD-domænetjenester administrerede. Du kan også deaktivere klargøre det eksisterende domæne og derefter fortsætte med at aktivere Azure AD-domænetjenester.


### <a name="inadequate-permissions"></a>Tilstrækkelige tilladelser
**Fejlmeddelelse:**

*Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Tjenesten har ikke tilstrækkelige tilladelser til det program, kaldet 'Azure AD-domæne Services Synkroniser'. Slette det program, der hedder 'Azure AD-domæne Services Synkroniser', og prøv derefter at aktivere Domain Services for din Azure AD-lejer.*

**Afhjælpning:**

Kontrollér, om der er et program med navnet 'Azure AD-domæne Services Synkroniser' i Azure AD-biblioteket. Hvis programmet findes, kan du slette den og derefter genaktivere Azure AD-domænetjenester.

Du kan udføre følgende trin til at kontrollere for tilstedeværelse af programmet og slette den, hvis programmet findes:

  1. Gå til **Azure klassisk portal** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Vælg noden **Active Directory** i den venstre rude.
  3. Vælg Azure AD-lejer (bibliotek), du vil aktivere Azure AD-domænetjenester.
  4. Gå til fanen **programmer** .
  5. Vælg indstillingen **programmer firmaet ejer** i rullemenuen.
  6. Kontrollere, om et program kaldet **Azure AD-domæne Services synkronisering**. Hvis programmet findes, kan du fortsætte til at slette den.
  7. Når du har slettet programmet, du forsøge at aktivere Azure AD-domænetjenester igen.


### <a name="invalid-configuration"></a>Ugyldig konfiguration
**Fejlmeddelelse:**

*Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Programmet Domain Services i din Azure AD-lejer har ikke de nødvendige tilladelser til at aktivere Domain Services. Slette programmet med program-id d87dcbc6-a371-462e-88e3-28ad15ec4e64, og prøv derefter at aktivere Domain Services for din Azure AD-lejer.*

**Afhjælpning:**

Kontrollér, hvis du har et program med navnet 'AzureActiveDirectoryDomainControllerServices' (med et program-id for d87dcbc6-a371-462e-88e3-28ad15ec4e64) i Azure AD-biblioteket. Hvis dette program findes, skal du slette det og derefter aktivere Azure AD-domænetjenester igen.

Brug følgende PowerShell-script til at finde programmet, og Slet den.

> [AZURE.NOTE] Dette script bruger **Azure AD PowerShell version 2** -cmdletter. En komplet liste over alle tilgængelige cmdletter og for at hente modulet, skal du læse [AzureAD PowerShell referencedokumentation](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph deaktiveret
**Fejlmeddelelse:**

Domain Services kunne ikke aktiveres i denne Azure AD-lejer. Microsoft Azure AD-programmet er deaktiveret i din Azure AD-lejer. Aktivere programmet med program-id 00000002-0000-0000-c000-000000000000, og prøv derefter at aktivere Domain Services for din Azure AD-lejer.

**Afhjælpning:**

Kontrollér, hvis du har deaktiveret et program med id 00000002-0000-0000-c000-000000000000. Dette program er Microsoft Azure AD-program og leverer Graph API-adgang til din Azure AD-lejer. Azure AD-domænetjenester kræver dette program er aktiveret til at synkronisere din Azure AD-lejer til dit administrerede domæne.

Aktivér dette program for at rette fejlen, og prøv derefter at aktivere Domain Services for din Azure AD-lejer.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Brugere kan ikke logge på Azure AD-domænetjenester administrerede domænet
Hvis en eller flere brugere i din Azure AD-lejer er i stand til at logge på det nyoprettede administrerede domæne, kan du udføre følgende fejlfindingstrin:

- **Logon ved hjælp af UPN-formatet:** Prøv at logge på ved hjælp af UPN-formatet (for eksempel 'joeuser@contoso.com') i stedet for formatet SAMAccountName (CONTOSO\joeuser). SAMAccountName kan automatisk oprettet for brugere, hvis UPN præfiks er alt for lange eller er den samme som en anden bruger på administrerede domænet. UPN-formatet er sikkert at være entydig i en Azure AD-lejer.

> [AZURE.NOTE] Vi anbefaler ved hjælp af UPN-formatet til at logge på Azure AD-domænetjenester administrerede domænet.

- Sørg for, at du har [aktiveret synkronisering af adgangskoder](active-directory-ds-getting-started-password-sync.md) i overensstemmelse med de trin, der er beskrevet i Introduktion.

- **Eksterne konti:** Sørg for, at den pågældende brugerkonto ikke er en ekstern konto i Azure AD-lejer. Eksempler på eksterne konti omfatter Microsoft-konti (for eksempel 'joe@live.com') eller brugerkonti fra en ekstern Azure AD-mappe. Da Azure AD-domænetjenester ikke har legitimationsoplysninger for sådanne brugerkonti, disse brugere kan ikke logge på administrerede domænet.

- **Synkroniseret konti:** Hvis de pågældende brugerkonti synkroniseres fra en lokal adresseliste, kan du kontrollere, at:
    - Du har installeret eller opdateret til den [nyeste anbefalede version af Azure AD-forbindelse](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Du har konfigureret Azure AD-forbindelse til at [udføre en komplet synkronisering](active-directory-ds-getting-started-password-sync.md).

    - Afhængigt af størrelsen på adresselisten, kan det tage et øjeblik, før brugerkonti og legitimationsoplysninger hashes skal være tilgængeligt i Azure AD-domænetjenester. Kontrollér, at du venter længe, før du prøver igen godkendelse (afhængigt af størrelsen på adresselisten - et par timer til en dag eller to for store mapper).

    - Hvis problemet fortsætter, efter bekræftelse af de foregående trin, kan du prøve at genstarte tjenesten Microsoft Azure Active Directory-synkronisering. Start en kommandoprompt fra computeren synkronisering og udføre følgende kommandoer:
      1. net stop 'Microsoft Azure Active Directory-synkronisering'
      2. net start 'Microsoft Azure Active Directory-synkronisering'

- **Kun skyen konti**: Hvis den pågældende brugerkonto er en skybaseret kun brugerkonto, kan du sikre dig, at brugeren har ændret adgangskode, når du har aktiveret Azure AD-domænetjenester. Dette trin får legitimationsoplysninger, der kræves til Azure AD-domænetjenester skal genereres hashes.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Brugere, der er fjernet fra din Azure AD-lejer fjernes ikke fra dit administrerede domæne
Azure AD beskytter dig mod utilsigtet sletning af user-objekter. Når du sletter en brugerkonto fra din Azure AD-lejer, flyttes det tilsvarende brugerobjekt til papirkurven. Når slettehandlingen er synkroniseret til din administrerede domæne, hvor det tilsvarende brugerkontoen være markeret deaktiveret. Denne funktion kan du gendanne eller sletningen brugerkontoen senere.

For at fjerne brugerkontoen fuldt ud fra dit administrerede domæne, skal du slette brugeren permanent fra din Azure AD-lejer. Brug Fjern-MsolUser PowerShell-cmdlet med den '-RemoveFromRecycleBin' alt, som beskrevet i denne [MSDN-artikel](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Kontakt os
Kontakte produktteamet Azure Active Directory Domain Services til [dele feedback eller for at få support] (aktiv-directory-katalogtjeneste-Kontakt-us.md).
