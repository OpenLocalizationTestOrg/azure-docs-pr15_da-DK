<properties
    pageTitle="Azure vigtige samling logføring | Microsoft Azure"
    description="Brug dette selvstudium til at hjælpe dig med at komme i gang med Azure-tasten samling logføring."
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
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Azure vigtige samling logføring #
Azure-tasten samling findes i de fleste områder. Få mere at vide ved at se [tasten samling priser side](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion  
Når du har oprettet en eller flere vigtige vaults, vil du højst sandsynligt til at overvåge hvordan og hvornår dine vigtige vaults er få adgang til, og af hvem. Du kan gøre dette ved at aktivere logføring til tasten samling af legitimationsoplysninger, som gemmer oplysninger på en Azure-lager-konto, du angiver. Der oprettes der automatisk en ny objektbeholder med navnet **indsigt-logfiler-auditevent** for kontoen angivne lagerplads, og du kan bruge denne samme lagerplads-konto til indsamling af logfiler for flere vigtige vaults.

Du kan få adgang til dine oplysninger om logføring højst, 10 minutter efter nøglen samling handling. I de fleste tilfælde, vil det være hurtigere end dette.  Det er op til dig med at administrere din logge på din lagerplads konto:

- Brug standard Azure access metoder til at sikre logfilerne ved at begrænse, hvem der kan få adgang til dem.
- Slet logfiler, du vil ikke længere skal huske på kontoen lagerplads.

Brug dette selvstudium til at hjælpe dig med at komme i gang med Azure-tasten samling logføring for at oprette kontoen lagerplads, aktivere logføring af og fortolke af logføringsoplysninger, der indsamles.  


>[AZURE.NOTE]  Dette selvstudium omfatter ikke vejledning i, hvordan du opretter vigtige vaults, nøgler og hemmeligheder. Disse oplysninger, skal du se [Introduktion til Azure nøgle samling](key-vault-get-started.md). Eller på tværs af platforme kommandolinjen vejledning i [selvstudiet tilsvarende](key-vault-manage-with-cli.md).
>
>I øjeblikket, kan du konfigurere Azure-tasten samling i portalen Azure. I stedet bruge disse Azure PowerShell instruktioner.

De logfiler, som du har indsamlet kan visualiseres ved hjælp af Log analytics fra pakken handlinger i administration. Du kan finde yderligere oplysninger finder [Azure nøgle samling (Preview)-løsning i Log Analytics](../log-analytics/log-analytics-azure-key-vault.md).

Oversigt oversigtsoplysninger om Azure-tasten samling, du [Hvad er Azure-tasten samling?](key-vault-whatis.md)

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium, skal du have følgende:

- En eksisterende vigtige samling, som du har brugt.  
- Azure PowerShell, **minimumversion af 1.0.1**. Hvis du vil installere Azure PowerShell og knytte den til abonnementet Azure, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). Hvis du allerede har installeret Azure PowerShell og ikke kender versionen, fra Azure PowerShell-konsollen, Skriv `(Get-Module azure -ListAvailable).Version`.  
- Tilstrækkelige lagerplads på Azure for din nøgle samling loggene.


## <a id="connect"></a>Oprette forbindelse til dine abonnementer ##

Starte en session med Azure PowerShell og logge på din Azure-konto med følgende kommando:  

    Login-AzureRmAccount

Angiv dit brugernavn til Azure-konto og din adgangskode i pop op-browservinduet. Azure PowerShell får alle de abonnementer, der er knyttet til denne konto, og som standard, skal du bruger den første.

Hvis du har flere abonnementer, du muligvis angive en bestemt én, der blev brugt til at oprette din Azure-tasten samling af legitimationsoplysninger. Skriv følgende for at se abonnementer til din konto:

    Get-AzureRmSubscription

Hvis du vil angive det abonnement, der er knyttet til din nøgle samling, du vil logføring, Skriv:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Du kan finde flere oplysninger om konfiguration af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Oprette en ny firmapost lagerplads for dit loggene ##

Selvom du kan bruge en eksisterende konto lagerplads for dit loggene, skal vi oprette en ny lagerplads-konto, der skal være dedikeret til tasten samling logfiler. Fordel for når vi nødt til at angive det senere gemmer vi detaljerne i en variabel med navnet **systemadministratorens**.

Yderligere lette administration ved bruger vi også samme ressourcegruppe som den, der indeholder vores vigtige samling. Denne ressourcegruppe hedder **ContosoResourceGroup** fra den [Introduktion – selvstudium](key-vault-get-started.md), og vi fortsætter med at bruge Østasien placeringen. Erstatte disse værdier for dine egne, som det er relevant:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Hvis du beslutter at bruge en eksisterende konto lagerplads, skal den bruge samme abonnement som din nøgle samling af legitimationsoplysninger og det skal bruge implementeringsmodel ressourcestyring i stedet for implementeringsmodel klassisk.

## <a id="identify"></a>Identificere den vigtige samling af legitimationsoplysninger for din loggene ##

I vores få Introduktion selvstudium blev vores vigtige samling navnet **ContosoKeyVault**, så vi fortsætter med at bruge navnet, og gemme oplysninger til en variabel med navnet **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Aktivere logføring ##

For at aktivere logføring til tasten samling, bruger vi cmdlet'en Set-AzureRmDiagnosticSetting til sammen med de variabler, vi oprettede til vores nye lagerplads konto og vores vigtige samling. Vi kan også angive den **-aktiveret** flag til **$true** og angive kategorien til AuditEvent (kun kategorien for nøgle samling logføring):


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Output til dette omfatter:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Dette bekræfter, at logføring er nu aktiveret for din nøgle samling, gemme oplysninger til kontoen lagerplads.

Du kan også kan du også angive opbevaringspolitik for din loggene så ældre logfiler, slettes automatisk. For eksempel angive opbevaringspolitik ved hjælp af **- RetentionEnabled** flag til **$true** og angive **RetentionInDays -** parameter til **90** , så logfiler, der er ældre end 90 dage, slettes automatisk.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Hvad er logget:

- Alle godkendte REST-API-anmodninger er logget på, som indeholder mislykkede anmodninger som et resultat af adgangstilladelser, systemfejl eller forkert anmodninger.
- Handlinger på tasten vault selve, som omfatter oprettelse, sletning, indstilling vigtige samling access politikker, og opdaterer vigtige samling attributter såsom mærker.
- Handlinger på nøgler og hemmeligheder i den vigtige samling af legitimationsoplysninger, som omfatter oprettelse, ændring eller sletning af disse taster og hemmeligheder; handlinger som log skal bekræfte kryptere dekryptere, ombryde og udpakke taster, og få hemmeligheder, listen nøgler og hemmeligheder og deres versioner.
- Ikke-godkendte anmodninger, der medfører et 401 svar. For eksempel anmodninger, der ikke har en bærertoken eller er ugyldig eller udløbet eller har et ugyldigt token.  


## <a id="access"></a>Få adgang til din logfiler ##

Vigtige samling logfiler er gemt i objektbeholderen **indsigt-logfiler-auditevent** i kontoen lagerplads du har angivet. Du kan få vist alle BLOB i denne beholder, Skriv:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Output ser nogenlunde sådan ud:

**Objektbeholder Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Navn**

**----**

**resourceId = / ABONNEMENTER/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/udbydere/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / ABONNEMENTER/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/udbydere/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

** resourceId = / ABONNEMENTER/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/udbydere/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Som du kan se dette output, BLOB følge en navngivningsregel: **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Du kan bruge UTC dato og klokkeslæt-værdier.

Fordi den samme konto lagerplads kan bruges til at indsamle logge for flere ressourcer, er fuld ressource-ID i blob navn meget nyttigt at få adgang til eller hente lige, du skal bruge BLOB. Men før vi gør dette, har vi gennemgår først Sådan hentes alle BLOB.

Først skal oprette en mappe for at hente BLOB. Eksempel:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Og får en liste over alle BLOB:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Pipe denne liste via 'Get-AzureStorageBlobContent' at hente BLOB til vores destinationsmappe:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Når du kører denne anden kommando på **/** afgrænser blob navnene i oprette en fuld mappestrukturen under destinationsmappen, og denne struktur bruges til at hente og BLOB som filer.

Du kan hente selektivt BLOB ved at bruge jokertegn. Eksempel:

- Hvis du har flere vigtige vaults og vil hente logfiler for kun én vigtige samling, hedder CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Hvis du har flere grupper og ønsker at hente logfiler for kun én ressourcegruppe, kan du bruge `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Hvis du vil hente alle logfiler for januar 2016 måned, skal du bruge `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Nu er du klar til at starte studere Nyheder i loggene. Men før du går videre til, to yderligere parametre for Get-AzureRmDiagnosticSetting, som du måske brug for at vide:

- At forespørge på status for diagnosticering indstillingerne for dine vigtige samling ressource:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Sådan deaktiveres logføring for dine vigtige samling ressource:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Fortolke nøgle samling logfilerne ##

Individuelle BLOB er gemt som tekst, der er formateret som en JSON blob. Dette er en post i eksempel logfilen i at køre `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


I følgende tabel vises de feltnavne og beskrivelser.


| Feltnavn        | Beskrivelse |
| ------------- |-------------|
| tid      | Dato og klokkeslæt (UTC).|
| resourceId      | Azure ressourcestyring ressource-ID. For nøgle samling loggene er dette altid er tasten samling ressource-ID.|
| operationName      | Navnet på handlingen, som beskrevet i den næste tabel.|
| operationVersion      | Dette er den REST-API-version, der er blevet anmodet af klienten.|
| kategori      | AuditEvent er enkelt, som findes værdien for nøgle samling loggene.|
| resultType      | Resultat af REST-API anmodning.|
| resultSignature      | HTTP-status.|
| resultDescription     | Yderligere beskrivelse om resultatet, når de er tilgængelige.|
| durationMs      | Tid, det tog for at behandle REST-API-anmodning, i millisekunder. Dette omfatter ikke netværksventetid, så den tid, du måle på klientsiden ikke kan svarer til nuværende tidspunkt.|
| callerIpAddress      | IP-adressen på den klient, der har oprettet den.|
| correlationId      | Et valgfrit GUID, der kan gå klienten for at koordinere klientsiden logfiler med logfiler over service-side (tasten samling).|
| identitet      | Identitet fra det token, der blev angivet, når du foretager REST-API anmodningen. Det er normalt en "brugere", en "service sikkerhedskonto" eller en kombination "bruger + sikkerhedskontrol" som i tilfælde af en anmodning om resultatet af en Azure PowerShell-cmdlet.|
| Egenskaber      | Dette felt skal indeholde forskellige oplysninger, der er baseret på handlingen (operationName). I de fleste tilfælde indeholder kundeoplysninger (useragent strengen overføres af klienten), de nøjagtige REST-API anmodning URI og HTTP-statuskode. Desuden, når et objekt, der returneres som et resultat af en anmodning om (for eksempel KeyCreate eller VaultGet) indeholder også nøgle URI (som "id"), samling URI eller hemmeligt URI.|




Feltværdier **operationName** er i ObjectVerb format. Eksempel:

- Alle vigtige samling handlinger har den ' samling`<action>`' formatere, såsom `VaultGet` og `VaultCreate`.

- Alle de vigtigste operationer har den ' nøgle`<action>`' formatere, såsom `KeySign` og `KeyList`.

- Alle hemmeligt handlinger har den ' hemmeligt`<action>`' formatere, såsom `SecretGet` og `SecretListVersions`.

I følgende tabel vises de operationName og tilsvarende REST-API-kommando.

| operationName        | REST-API kommandoen |
| ------------- |-------------|
| Godkendelse      | Via Azure Active Directory slutpunkt|
| VaultGet      | [Få oplysninger om en vigtige samling](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Oprette eller opdatere en vigtige samling](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Slette en vigtige samling](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Opdatere en vigtige samling](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Liste over alle vigtige vaults i en ressourcegruppe](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Oprette en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Få oplysninger om en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importere en nøgle til en samling af legitimationsoplysninger](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Sikkerhedskopiering af en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Slette en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Gendanne en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Log på med en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Bekræfte med en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Ombryde en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Fjerne ombrydning af en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Krypter med en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Dekryptere med en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Opdatere en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Liste over tasterne i en samling af legitimationsoplysninger](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [Liste over versionerne af en nøgle](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Oprette et hemmeligt](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Få hemmeligt](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Opdatere en hemmeligt](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Slette et hemmeligt](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Listen hemmeligheder i en samling af legitimationsoplysninger](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Listen versioner af et hemmeligt](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Næste trin ##

Du kan finde et selvstudium, der bruger Azure-tasten samling i et webprogram, [Brug Azure nøgle samling fra et webprogram](key-vault-use-from-web-application.md).

Se [i Azure nøgle samling developer guide](key-vault-developers-guide.md)til programmering referencer.

Du kan finde en liste over Azure PowerShell 1.0-cmdlet'er til Azure-tasten samling, [Azure nøgle samling cmdletter](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Selvstudium til vigtige rotation, og log revision med Azure-tasten samling, se, [hvordan du konfiguration nøgle samling med start til slut vigtige rotation og revision](key-vault-key-rotation-log-monitoring.md).
