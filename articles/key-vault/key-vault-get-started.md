<properties
    pageTitle="Introduktion til Azure-tasten samling | Microsoft Azure"
    description="Brug dette selvstudium til at hjælpe dig med at komme i gang med Azure-tasten samling til at oprette en hærdet objektbeholder i Azure, gemme og administrere cryptographic nøgler og hemmeligheder i Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Introduktion til Azure-tasten samling #
Azure-tasten samling findes i de fleste områder. Få mere at vide ved at se [tasten samling priser side](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion  
Brug dette selvstudium til at hjælpe dig med at komme i gang med Azure-tasten samling til at oprette en hærdet objektbeholder (en samling) i Azure, gemme og administrere cryptographic nøgler og hemmeligheder i Azure. Det vejleder dig gennem processen med at bruge Azure PowerShell til at oprette en samling af legitimationsoplysninger, der indeholder en nøgle eller adgangskode, kan du bruge med et Azure-program. Den derefter viser dig hvordan et program kan bruge denne tast eller adgangskode.

**Anslået tid at gennemføre:** 20 minutter

>[AZURE.NOTE]  Dette selvstudium omfatter ikke vejledning i, hvordan du kan skrive Azure-program, et af trinnene omfatter, nemlig Sådan Godkend et program du bruger en nøgle eller hemmeligt i den vigtige samling af legitimationsoplysninger.
>
>Dette selvstudium bruger Azure PowerShell. Du kan finde oplysninger på tværs af platforme kommandolinjen, [selvstudiet tilsvarende](key-vault-manage-with-cli.md).

Oversigt oversigtsoplysninger om Azure-tasten samling, du [Hvad er Azure-tasten samling?](key-vault-whatis.md)

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium, skal du have følgende:

- Et abonnement på Microsoft Azure. Hvis du ikke har en, kan du tilmelde dig en [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
- Azure PowerShell, **minimumversion af 1.1.0**. Hvis du vil installere Azure PowerShell og knytte den til abonnementet Azure, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). Hvis du allerede har installeret Azure PowerShell og ikke kender versionen, fra Azure PowerShell-konsollen, Skriv `(Get-Module azure -ListAvailable).Version`. Når du har Azure PowerShell version 0.9.1 gennem 0.9.8 installeret, kan du stadig bruge dette selvstudium med nogle mindre ændringer. For eksempel skal du bruge den `Switch-AzureMode AzureResourceManager` kommandoen samt nogle af kommandoerne Azure-tasten samling er blevet ændret. Du kan finde en liste over samling nøgle-cmdlet'er til versioner 0.9.1 gennem 0.9.8, [Azure nøgle samling cmdletter](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Et program, der skal konfigureres for at bruge tasten eller den adgangskode, du opretter i dette selvstudium. Et eksempelprogram er tilgængeligt fra [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Du kan finde instruktioner, ledsagende Vigtigt-filen.


Dette selvstudium henvender sig til Azure PowerShell begyndere, men det antages, at du forstår de grundlæggende begreber, som moduler, cmdletter og sessioner. Du kan finde flere oplysninger i [Introduktion til Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Brug cmdlet'en **Get-Help** for at få detaljeret hjælp til en hvilken som helst cmdlet, der vises i dette selvstudium.

    Get-Help <cmdlet-name> -Detailed

Skriv for at få hjælp til **Logon-AzureRmAccount** -cmdlet, f.eks.:

    Get-Help Login-AzureRmAccount -Detailed

Du kan også læse følgende selvstudier til at blive fortrolig med Azure ressourcestyring i Azure PowerShell:

- [Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)
- [Brug af Azure PowerShell med ressourcestyring](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Oprette forbindelse til dine abonnementer ##

Starte en session med Azure PowerShell og logge på din Azure-konto med følgende kommando:  

    Login-AzureRmAccount 

Bemærk, at hvis du bruger en bestemt forekomst af Azure, for eksempel Azure stat, bruge parameteren - miljø med denne kommando. Eksempel:`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Angiv dit brugernavn til Azure-konto og din adgangskode i pop op-browservinduet. Azure PowerShell får alle de abonnementer, der er knyttet til denne konto, og som standard, skal du bruger den første.

Hvis du har flere abonnementer, og du vil angive en bestemt én skal bruges til Azure-tasten samling, Skriv følgende for at se abonnementer til din konto:

    Get-AzureRmSubscription

Hvis du vil angive abonnementet, du bruger, Skriv:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Du kan finde flere oplysninger om konfiguration af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Oprette en ny ressourcegruppe ##

Når du bruger Azure Ressourcestyring, oprettes alle relaterede ressourcer i en ressourcegruppe. Vi opretter en ny ressourcegruppe navnet **ContosoResourceGroup** for dette selvstudium:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Oprette en vigtige samling ##

Brug cmdlet'en [Ny AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) til at oprette en vigtige samling af legitimationsoplysninger. Denne cmdlet har tre obligatoriske parametre: en **ressource gruppenavn**, et **vigtige samling navn**og den **geografiske placering**.

Hvis du bruger samling navnet på **ContosoKeyVault**, gruppen ressourcenavnet af **ContosoResourceGroup**og placeringen af **Østasien**, Skriv:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Output fra denne cmdlet viser egenskaberne for den vigtige samling af legitimationsoplysninger, som du lige har oprettet. De to vigtigste egenskaber er:

- **Samling af legitimationsoplysninger navn**: I eksemplet, er dette **ContosoKeyVault**. Du skal bruge dette navn til andre nøgle samling cmdletter.
- **Samling af legitimationsoplysninger URI**: I eksemplet, er dette https://contosokeyvault.vault.azure.net/. Programmer, der bruger din samling via dens REST-API skal bruge denne URI.

Azure-konto er nu tilladelse til at udføre handlinger på denne vigtige samling. Ingen andre er endnu.

>[AZURE.NOTE]  Hvis du får vist fejlen **abonnementet ikke er registreret til at bruge navneområde 'Microsoft.KeyVault'** , når du forsøger at oprette din nye vigtige samling, kan køre `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` og derefter køre kommandoen ny AzureRmKeyVault igen. Du kan finde yderligere oplysninger finder [Register-AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Tilføje en nøgle eller hemmeligt til den vigtige samling af legitimationsoplysninger ##

Hvis du vil Azure-tasten samling til at oprette en software-beskyttede nøgle for dig, Brug cmdlet'en [Tilføj AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) og skrive følgende:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Men hvis du har en eksisterende software-beskyttede nøgle i en. PFX-fil, der er gemt på din C:\ drev i en fil kaldet softkey.pfx, du vil overføre til Azure-tasten samling, Skriv følgende for at indstille den variable **securepfxpwd** for en adgangskode på **123** til den. PFX-filen:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Skriv følgende for at importere tasten fra den. PFX-fil, der beskytter nøglen af software i tjenesten nøgle samling af:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Du kan nu henvise til denne tast, du har oprettet eller overført til Azure-tasten samling ved hjælp af dens URI. Brug **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** til altid for at hente den aktuelle version, og følg **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** for at finde denne bestemte version.  

Hvis du vil have vist URI for denne nøgle, skal du skrive:

    $Key.key.kid

Hvis du vil føje et hemmeligt til samling, som er en adgangskode, med navnet SQLPassword og har værdien af Pa$ $w0rd til Azure-tasten samling, først konvertere værdien af Pa$ $w0rd til en sikker streng ved at skrive følgende:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Skriv derefter følgende:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Du kan nu henvise til denne adgangskode, du har føjet til Azure-tasten samling ved hjælp af dens URI. Brug **https://ContosoVault.vault.azure.net/secrets/SQLPassword** til altid for at hente den aktuelle version, og følg **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** for at finde denne bestemte version.

For at få vist URI for denne hemmeligt, Skriv:

    $secret.Id

Lad os se på tasten eller hemmeligt, som du lige har oprettet:

- For at få vist din nøgle, Skriv:`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- For at få vist din hemmeligt, Skriv:`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Nu er din nøgle samling og nøgle eller hemmeligt er klar til programmer til brug. Du skal Godkend programmer til at bruge dem.  

## <a id="register"></a>Registrere et program med Azure Active Directory ##

Dette trin vil normalt udføres af en udvikler, på en separat computer. Det er ikke specifik for Azure-tasten samling, men er her medtaget for fuldstændighed.


>[AZURE.IMPORTANT] For at fuldføre selvstudiet, din konto, samling af legitimationsoplysninger og det program, du vil registrere i dette trin skal alle være i den samme Azure mappe.

Programmer, der bruger en vigtige samling af legitimationsoplysninger skal godkende ved hjælp af et token fra Azure Active Directory. For at gøre dette, skal ejeren af programmet først registrere programmet i deres Azure Active Directory. I slutningen af registrering bliver ejeren af programmet følgende værdier:


- Et **Program-ID** (også kaldet en klient-ID) og **godkendelsesnøgle** (også kaldet delte hemmeligt). Programmet skal præsentere begge disse værdier til Azure Active Directory, at få et token. Hvordan programmet er konfigureret til at gøre dette afhænger af programmet. Til programmet nøgle samling eksempel angiver ejeren af programmet disse værdier i app.config-filen.

Til at registrere programmet i Azure Active Directory:

1. Log på portalen Azure klassisk.
2. Klik på **Active Directory**til venstre, og vælg derefter den mappe, hvor du vil registrere dit program. <br> <br> **Note:** Du skal vælge den samme mappe, der indeholder det Azure abonnement, som du har oprettet din nøgle samling. Hvis du ikke ved, hvilken mappe dette er, klik på **Indstillinger**, identificere det abonnement, som du har oprettet din nøgle samling, og notér navnet på den mappe, der vises i den sidste kolonne.

3. Klik på **programmer**. Hvis ingen apps er blevet føjet til adresselisten, vises på denne side kun linket **Tilføj en App** . Klik på linket, eller, du kan også klikke på **Tilføj** på kommandolinjen.
4.  I guiden **Tilføj programmer** på den **Hvad vil du gøre?** på siden skal du klikke på **Tilføj et program, udvikling af min organisation**.
5.  Angiv et navn for dit program på siden **Fortæl os om dit program** , og vælg derefter **WEB APPLICATION og/eller WEB API** (standard). Klik på ikonet **Næste** .
6.  Angiv **SIGN-ON URL-adresse** og **APP ID URI** -webprogrammet på siden **App egenskaber** . Hvis dit program ikke har disse værdier, kan du gøre dem til dette trin (for eksempel du kan angive http://test1.contoso.com for begge felter). Det betyder ikke noget, hvis der findes følgende websteder. Hvad er vigtige er, app ID URI for hvert program er forskellige for hvert program i mappen. Mappen bruger denne streng til at identificere din app.
7.  Klik på ikonet **udført** for at gemme ændringerne i guiden.
8.  Klik på **KONFIGURER**på siden **Hurtig Start** .
9.  Rul ned til sektionen **taster** , vælg varigheden, og klik derefter på **Gem**. Siden opdaterer og viser nu en nøgle-værdi. Du skal konfigurere dit program med denne nøgleværdi og værdien **Klient-ID** . (Instruktioner til denne konfiguration er program-specifikke).
10. Kopiér værdien klient-ID fra denne side, som du skal bruge i næste trin for at angive tilladelser til din samling af legitimationsoplysninger.

## <a id="authorize"></a>Tillade, at du skal bruge tasten eller hemmeligt ##

For at godkende program tilladelse til at få adgang til den tast eller hemmeligt i samling af legitimationsoplysninger, skal du bruge cmdlet'en  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) til.

Eksempelvis hvis navnet på din samling af legitimationsoplysninger er **ContosoKeyVault** og det program, du vil tillade, at du har en klient-ID'ET for 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, og du vil tillade, at du kan dekryptere og tilmelde taster i din samling, Kør følgende:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Hvis du vil tillade, at den samme program til at læse hemmeligheder i din samling, skal du køre følgende:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Hvis du vil bruge et hardware sikkerhed-modul (HSM) ##

Du kan importere eller oprette nøgler i hardware sikkerhed moduler (HSMs), som Efterlad aldrig i HSM rammen for større sikkerhed. HSMs er FIPS 140-2 niveau 2 valideres. Hvis dette krav ikke gælder for dig, springe dette afsnit, og gå til at [slette den vigtige samling af legitimationsoplysninger og tilhørende nøgler og hemmeligheder](#delete).

Hvis du vil oprette disse HSM beskyttet taster, skal du bruge [Azure nøgle samling Premium webtjenesten til at understøtte HSM beskyttet taster](https://azure.microsoft.com/pricing/free-trial/). Bemærk også, at denne funktion ikke er tilgængelig for Azure Kina.


Når du opretter den vigtige samling af legitimationsoplysninger, kan du tilføje parameteren **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Du kan føje software-beskyttede nøgler (som vist tidligere) og HSM-beskyttede nøgler til denne vigtige samling. Hvis du vil oprette en HSM beskyttet nøgle, skal du angive den **-Destination** -parameter til 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Du kan bruge følgende kommando for at importere en nøgle fra en. PFX-fil på computeren. Denne kommando importerer nøglen til HSMs i tjenesten nøgle samling:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


Den næste kommando importerer en "få din egen nøgle" (BYOK) pakke. Dette scenarie kan du oprette din nøgle i din lokale HSM og overføre den til HSMs i tjenesten nøgle samling uden tasten at forlade HSM rammen:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Finde mere detaljerede oplysninger om, hvordan du opretter denne BYOK pakke, se, [hvordan til at generere og overføre HSM beskyttet taster til Azure nøgle samling](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Slette vigtige samling og tilknyttede nøgler og hemmeligheder ##

Hvis du ikke længere har brug for den vigtige samling af legitimationsoplysninger og nøgle eller hemmeligt, den indeholder, kan du slette den vigtige samling af legitimationsoplysninger ved hjælp af [Fjern AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) cmdlet:

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Eller du kan slette en hel Azure ressourcegruppe, som indeholder den vigtige samling af legitimationsoplysninger og andre ressourcer, som du har brugt i den pågældende gruppe:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Andre Azure PowerShell-cmdletter ##

Andre kommandoer, der kan være nyttige til administration af Azure-tasten samling:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Denne kommando får et tabelformat visningen af alle nøgler og de valgte egenskaber.
- `$Keys[0]`: Denne kommando viser en komplet liste over egenskaberne for den angivne nøgle
- `Get-AzureKeyVaultSecret`: Denne kommando viser en tabelformat visning af alle hemmeligt navne og valgte egenskaber.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Eksempel hvordan du fjerner en bestemt nøgle.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Eksempel hvordan du fjerner en bestemt hemmeligt.


## <a id="next"></a>Næste trin ##

Du kan finde en opfølgende selvstudium, der bruger Azure-tasten samling i et webprogram, [Brug Azure nøgle samling fra et webprogram](key-vault-use-from-web-application.md).

For at se, hvordan din nøgle samling af legitimationsoplysninger anvendes skal du se [Azure nøgle samling logføring](key-vault-logging.md).

Du kan finde en liste over de seneste Azure PowerShell-cmdletter til Azure-tasten samling, [Azure nøgle samling cmdletter](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Se [i Azure nøgle samling developer guide](key-vault-developers-guide.md)til programmering referencer.
