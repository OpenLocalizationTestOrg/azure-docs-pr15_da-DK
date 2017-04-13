<properties
    pageTitle="Secure din nøgle samling | Microsoft Azure"
    description="Administrere adgangstilladelser for vigtige samling til administration af vaults og nøgler og hemmeligheder. Godkendelse og autorisation model til vigtige samling og hvordan du sikrer din nøgle samling"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Secure din nøgle samling

Azure-tasten samling er en skybaseret tjeneste, der beskytter krypteringsnøgler og hemmeligheder (såsom certifikater, strenge, adgangskoder) til dine programmer i skyen. Da disse data er følsomme og business kritiske, du vil sikre adgangen til dine vigtige vaults, så kun autoriserede programmer og brugere kan få adgang til vigtige samling. I denne artikel indeholder en oversigt over vigtige samling access-model, forklarer godkendelse og autorisation og beskriver, hvordan du sikker adgang til vigtige samling til dine skyen programmer med et eksempel.

## <a name="overview"></a>Oversigt

Adgang til en vigtige samling styres via to separate grænseflader: Administration af plan og dataplan. Begge planer er stort godkendelse og godkendelse påkrævet til før et opkald (en bruger eller et program) kan få adgang til vigtige samling. Godkendelse opretter identitet, mens godkendelse bestemmer, hvilke handlinger, hvem der har tilladelse til at udføre.

Både management plan og dataplan bruger godkendelse af Azure Active Directory. Om tilladelse bruger management plan rollebaseret adgangskontrol (RBAC), mens dataplan bruger vigtige samling access-politik.

Her er en kort oversigt over emnerne:

[Godkendelse ved hjælp af Azure Active Directory](#authentication-using-azure-active-direcrory) - dette afsnit beskrives det, hvordan en opkalds godkender med Azure Active Directory for at få adgang til en vigtige samling via administration plan og dataplan. 

[Administration af plan og dataplan](#management-plane-and-data-plane) - Management plan og dataplan er to access-planer, der bruges til at få adgang til dine vigtige samling. Hvert enkelt access plan understøtter bestemte handlinger. I dette afsnit beskrives access slutpunkterne, handlinger understøttes, og få adgang til metode bruges af hvert enkelt plan. 

[Administration af plan adgangskontrol](#management-plane-access-control) – i dette afsnit, vil vi se på tillade adgang til administration af plan handlinger ved hjælp af rollebaseret adgangskontrol.

[Data plane adgangskontrol](#data-plane-access-control) – i dette afsnit beskrives, hvordan du bruge tasten samling access-politik til at styre adgang til plan.

[Eksempel](#example) - i dette eksempel beskrives, hvordan du konfigurerer adgangskontrol for dine vigtige samling tillade tre forskellige teams (sikkerhed team udviklere/operatorer og revisorer) til at udføre bestemte opgaver for at udvikle, administrere og overvåge et program i Azure.


## <a name="authentication-using-azure-active-directory"></a>Godkendelse ved hjælp af Azure Active Directory

Når du opretter en vigtige samling af legitimationsoplysninger i et Azure-abonnement, er den automatisk knyttet til det abonnement Azure Active Directory-lejer. Alle ringer (brugere og programmer) skal være registreret i denne lejer at få adgang til denne vigtige samling. Et program eller en bruger skal godkende med Azure Active Directory for at få adgang til vigtige samling. Dette gælder for begge management plan og data plan adgang. I begge tilfælde et program kan få adgang til på vigtige samling på to måder:

-  **bruger + app access** - normalt er til programmer, få adgang til vigtige samling på vegne af en bruger, der er logget på. Azure PowerShell, Azure Portal er eksempler på denne type adgang. Der er to måder til at give adgang til brugere: én måde er at give adgang til brugere, så de kan få adgang til vigtige samling fra et program og anden måde er at give en brugeradgang til vigtige samling kun, når de bruger et bestemt program (kaldes sammensat identitet). 
-  **kun app access** - for programmer, der kører daemon services, baggrund job osv. Programmets identitet er tildelt adgang til den vigtige samling af legitimationsoplysninger.

I begge typer programmer, programmet godkender med Azure Active Directory ved hjælp af de [understøttede godkendelsesmetoder](../active-directory/active-directory-authentication-scenarios.md) og får et token. Anvendte godkendelsesmetode, afhænger af programtypen. Derefter programmet bruges dette token og sender anmodning om REST-API til vigtige samling. I tilfælde af management plan access distribueres anmodningerne via Azure ressourcestyring slutpunkt. Når du åbner dataplan, programmer taler direkte til en nøgle samling slutpunkt. Se flere oplysninger om [hele godkendelse forløb](../active-directory/active-directory-protocols-oauth-code.md). 

Ressourcenavnet, programmet anmoder om et token er forskellige, afhængigt af om programmet er at få adgang til administration af plan eller dataplan. Derfor er ressourcenavnet enten management plan eller data plan slutpunkt er beskrevet i tabellen i en nyere afsnit, afhængigt af det Azure-miljø.

Har du en enkelt metode til godkendelse til både administration og dataplan har sin egen fordele:

- Organisationer kan centralt styre adgangen til alle vigtige vaults i deres organisation
- Hvis en bruger forlader, miste de med det samme adgangen til alle vigtige vaults i organisationen
- Organisationer kan tilpasse godkendelse via indstillingerne i Azure Active Directory (for eksempel aktivering Multi-Factor godkendelse for ekstra sikkerhed)

## <a name="management-plane-and-data-plane"></a>Administration af plan og dataplan

Azure-tasten samling er en Azure service, der er tilgængelige via Azure ressourcestyring implementeringsmodel. Når du opretter en vigtige samling af legitimationsoplysninger, får du en virtuel beholder i, kan du oprette andre objekter som taster, hemmeligheder og certifikater. Kan du adgang til dine vigtige samling bruge management plan og dataplan til at udføre bestemte handlinger. Administration af plan brugergrænseflade bruges til at administrere din nøgle samling sig selv som oprette, slette, ved at opdatere vigtige samling attributter og angive access politikker for dataplan. Data plan brugergrænseflade bruges til at tilføje, slette, ændre og bruge taster, hemmeligheder og certifikater, der er gemt i din nøgle samling.

Management plan og data plan grænsefladerne bruges via forskellige slutpunkter (se tabellen). Den anden kolonne i tabel beskrives DNS-navne til disse slutpunkter i forskellige Azure miljøer. Den tredje kolonne i denne artikel beskrives de handlinger, du kan udføre fra hver access-plan. Hvert enkelt access plan også har sit eget access control system: for administration af plan adgangskontrol er angivet ved hjælp af Azure Resource Manager Role-Based Access kontrolelement (RBAC), mens du for data plan adgangskontrol er angivet ved hjælp af vigtige samling access-politik.

| Access plan | Access slutpunkter | Handlinger | Access kontrolelement ordning|
|--------------|------------------|--------------------|--------|
| Administration af plan|**Global:**<br> Management.Azure.com:443<br><br> **Azure Kina:**<br> Management.chinacloudapi.CN:443<br><br> **Azure amerikanske:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Tyskland:**<br> Management.microsoftazure.de:443 | Oprette/læst/Opdater/slette vigtige samling <br> Indstille access politikker for vigtige samling<br>Sæt mærker til vigtige samling | Azure ressource Manager rollebaseret adgangskontrol (RBAC) |
| Dataplan | **Global:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.azure.net:443<br><br> **Azure Kina:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.azure.cn:443<br><br> **Azure amerikanske:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> &lt;samling af legitimationsoplysninger navn&gt;. vault.microsoftazure.de:443 | For nøgler: Dekryptere, krypterer, UnwrapKey, WrapKey, bekræfte, logge, få, liste, opdatere, oprette, importere, slette, sikkerhedskopiering, gendanne<br><br> For hemmeligheder: får, på listen, Slet | Vigtige samling access-politik|

Management plan og data plan adgang til kontrolelementerne for arbejde uafhængigt af hinanden. Eksempelvis hvis du vil give adgang til en programmer for at bruge tasterne i en vigtige samling, du skal kun bruge til at tildele adgangstilladelser plan, der vises ved hjælp af vigtige samling access politikker, og der kræves ingen styring plan adgang til dette program. Og omvendt, hvis du vil en bruger skal kunne læse samling egenskaber og de mærker, men ikke har adgang til taster, hemmeligheder eller certifikater, kan du give denne bruger, 'læseadgang' ved hjælp af RBAC og ikke adgang til dataplan er påkrævet.

## <a name="management-plane-access-control"></a>Adgangskontrol til administration af plan

Administration af plan består af handlinger, der påvirker den vigtige samling af legitimationsoplysninger sig selv. For eksempel kan du oprette eller slette en vigtige samling af legitimationsoplysninger. Du kan få en liste over vaults i et abonnement. Du kan hente vigtige samling egenskaber (såsom SKU, mærker) og angive vigtige samling access politikker, der bestemmer de brugere og programmer, der kan få adgang til nøgler og hemmeligheder i den vigtige samling af legitimationsoplysninger. Administration af plan adgangskontrol bruger RBAC. Se den komplette liste over vigtige samling handlinger, der kan udføres via management plan i tabellen før sektion. 

### <a name="role-based-access-control-rbac"></a>Rollebaseret adgangskontrol (RBAC)
Hver Azure abonnement har en Azure Active Directory. Brugere, grupper og programmer fra denne mappe kan have tildelt adgang for at administrere ressourcer i Azure-abonnement, der bruger implementeringsmodel Azure ressourcestyring. Denne type af adgangskontrol kaldes rollebaseret adgangskontrol (RBAC). Du kan bruge [Azure-portalen](https://portal.azure.com/), [Azure CLI værktøjer](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)eller [Azure ressourcestyring REST API'er](https://msdn.microsoft.com/library/azure/dn906885.aspx)for at administrere denne adgang.

Med Azure ressourcestyring modellen oprette du din nøgle samling i en ressource gruppe og kontrollere adgang til denne vigtige samling management plan ved hjælp af Azure Active Directory. For eksempel kan du give brugere eller en gruppe mulighed for at administrere vigtige vaults i en bestemt ressourcegruppe.

Du kan give adgang til brugere, grupper og programmer på et bestemt område ved at tildele relevante RBAC roller. For eksempel for at give adgang til en bruger til at administrere vigtige vaults ville du tildeler en foruddefineret rolle 'vigtige samling bidragyder' til denne bruger på et bestemt område. Omfanget i dette tilfælde der ville være et abonnement, en ressourcegruppe eller blot en bestemt vigtige samling. En rolle, der er tildelt på abonnementsniveau gælder for alle grupper og ressourcer i dette abonnement. En rolle, der er tildelt på ressourcegruppeniveau gælder for alle ressourcer i denne ressourcegruppe. En rolle, der er tildelt til en bestemt ressource gælder kun for ressourcen. Der er flere foruddefinerede roller (se [RBAC: indbyggede roller](../active-directory/role-based-access-built-in-roles.md)), og hvis de foruddefinerede roller ikke opfylder dine behov, kan du også definere dine egne roller.

>[AZURE.IMPORTANT] Bemærk, at hvis en bruger har bidragyder tilladelser (RBAC) til et vigtige samling management plan, hun kan give sig adgang til dataplan, ved at angive produktnøglen samling access-politik, som styrer adgang til dataplan. Derfor anbefales det at tæt styre, hvem der har 'Bidragyder' adgang til dine vigtige vaults at sikre, at kun autoriserede personer kan få adgang til og administrere dine vigtige vaults, taster, hemmeligheder og certifikater.

## <a name="data-plane-access-control"></a>Data plan adgangskontrol

Det vigtigste samling dataplan består af handlinger, der påvirker objekterne i en vigtige samling, som taster, hemmeligheder og certifikater.  Dette omfatter nøgle handlinger som oprette, importere, opdatering, liste, sikkerhedskopiering og gendannelse taster cryptographic handlinger som log, bekræfte, kryptere, dekryptere, Ombryd, og udpakke og angive mærker og andre attributter for taster. På samme måde, hemmeligheder, den indeholder, er, klar, liste, slette.

Dataadgang plan er tildelt ved at angive access politikker for en vigtige samling af legitimationsoplysninger. En bruger, gruppe eller et program skal have tilladelser som bidragydere (RBAC) til administration af plan til en vigtige samling lov til at indstille access politikker for den vigtige samling. En bruger, gruppe eller -programmer kan få adgang for at udføre bestemte handlinger for taster eller hemmeligheder i en vigtige samling af legitimationsoplysninger. vigtige samling understøtter op til 16 access poster til en vigtige samling af legitimationsoplysninger. Oprette en Azure Active Directory-sikkerhedsgruppe og tilføje brugere til den pågældende gruppe til at give data plan adgang til flere brugere til en vigtige samling af legitimationsoplysninger.

### <a name="key-vault-access-policies"></a>vigtige samling Access politikker

vigtige samling access politikker give tilladelser til nøgler, hemmeligheder og certifikater separat. For eksempel kan du give en brugeradgang til kun taster, men ingen tilladelser for hemmeligheder. Tilladelser til at få adgang til taster eller hemmeligheder eller certifikater, der er dog på niveauet for samling af legitimationsoplysninger. Med andre ord understøtter vigtige samling access-politik ikke niveau objekttilladelser. Du kan bruge [Azure-portalen](https://portal.azure.com/), [Azure CLI værktøjer](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)eller [vigtige samling Management REST API'er](https://msdn.microsoft.com/library/azure/mt620024.aspx) indstille access politikker for en vigtige samling af legitimationsoplysninger.

>[AZURE.IMPORTANT] Bemærk, at vigtige samling access politikker gælder på niveauet for samling af legitimationsoplysninger. Når en bruger har fået tilladelse til at oprette og slette taster, kan hun udføre disse handlinger på alle nøgler i den vigtige samling.

## <a name="example"></a>Eksempel

Antag, at du udvikler et program, der bruger et certifikat til SSL, Azure-lager til lagring af data, og bruger en RSA 2048-bit-nøgle for Log handlinger. Antag, at dette program kører i et VM (eller angive en VM skala). Du kan bruge en vigtige samling af legitimationsoplysninger til at gemme alle programmet hemmeligheder og bruge vigtige samling af legitimationsoplysninger til at gemme det bootstrap certifikat, der bruges af programmet til at godkende med Azure Active Directory.

Så er her en oversigt over alle nøgler og hemmeligheder skal gemmes i en vigtige samling af legitimationsoplysninger.

- **SSL-certifikat** - bruges til SSL
- **Lagerplads nøgle** – bruges til at få adgang til lagerplads konto
- **RSA 2048-bit nøgle** – bruges til logge handlinger
- **Bootstrap certifikat** – som bruges til at godkende til Azure Active Directory, du får adgang til vigtige samling af legitimationsoplysninger til at hente tasten lager og bruge tasten RSA til signering.

Nu Lad os mødes de personer, der administrere, implementere og revision dette program. Vi bruger tre roller i dette eksempel.

- **Security team** – dette er typisk IT-medarbejdere fra 'office af sikringsofficer (ledende Sikkerhedsvagt)' eller tilsvarende, ansvarlig for det stort opbevaring af hemmeligheder som SSL-certifikater, RSA nøgler, der bruges til signering, strenge for databaser, lagerplads konto taster.
- **Udviklere/operatorer** - dette er de personer, som udvikle dette program og derefter installere det i Azure. Typisk de er ikke en del af sikkerhed-teamet, og derfor skal de ikke har adgang til følsomme oplysninger, som SSL-certifikater, RSA-nøgler, men de installere programmet skal have adgang til dem, der er.
- **Revisorer** - det er normalt et andet sæt af personer, isolerede fra udviklere og generelle IT-medarbejdere. Deres ansvar er at gennemse stort brug og vedligeholdelse af certifikater, taster osv og sikre, at med data sikkerhedsstandarder. 

Der er én flere rolle, der ikke er omfattet af dette program, men relevante her skal angives, og det er den abonnement (eller ressourcegruppe) administrator. Abonnement administratoren konfigurerer indledende adgangstilladelser for teamet sikkerhed. Her antager vi, at abonnement administratoren har tildelt adgang til security-teamet til en ressourcegruppe i hvilke alle de ressourcer, der kræves for dette program er bosat.

Nu Lad os se, hvilke handlinger udfører hver rolle i forbindelse med dette program.

- **Sikkerhed team**
    - Oprette vigtige vaults
    - Slår nøgle samling logføring
    - Tilføje taster/hemmeligheder
    - Oprette en sikkerhedskopi af taster til nedbrud
    - Angive vigtige samling adgang for at give tilladelser til brugere og programmer for at udføre bestemte handlinger
    - Med jævne mellemrum Brug taster/hemmeligheder
- **Udviklere/operatorer**
    - Få referencer til initialiseres og SSL certifikater (thumbprints) lagerplads nøgle (hemmeligt URI) og logge nøgle (tasten URI) fra security-teamet
    - Udvikle og installere programmet, som har adgang til nøgler og hemmeligheder fra et program
- **Revisorer**
    - Gennemse brugen logfiler for at bekræfte korrekt brug tasten/hemmeligt og overholdelse af data sikkerhedsstandarder

Nu Lad os se, hvilke adgangstilladelser til vigtige samling er nødvendige for hver rolle (og programmet) til at udføre deres tildelte opgaver. 

| Brugerrolle    | Tilladelser til dokumentstyring plan | Data plan tilladelser |
|--------------|------------------------------|------------------------|
|Security Team|vigtige samling bidragyder|Taster: Sikkerhedskopiér, oprette, slette, få, importere, liste, gendanne <br> Hemmeligheder: alle|
|Udviklere/fører| vigtige samling installere tilladelse, så FOS de installere kan hente hemmeligheder fra den vigtige samling af legitimationsoplysninger | Ingen |
|Revisorer| Ingen | Taster: liste<br>Hemmeligheder: liste|
|Program| Ingen | Taster: Log<br>Hemmeligheder: få |

>[AZURE.NOTE] Revisorer skal listen tilladelse til nøgler og hemmeligheder, så de kan undersøge attributter for nøgler og hemmeligheder, der ikke udledes i loggene, såsom mærker, aktivering og udløbsdato.

Ud over tilladelse til at vigtige samling, skal alle tre roller også have adgang til andre ressourcer. For at kunne installere FOS (eller Web Apps osv.) Udviklere/operatorer skal også have 'Bidragyder' adgang til disse ressourcetyper. Revisorer skal læseadgang til kontoen lagerplads, hvor loggene vigtige samling er gemt.

Da fokus i denne artikel er sikring af adgang til dine vigtige samling, vi kun illustrere de relevante dele, der vedrører dette emne og springe over oplysninger om installation af certifikater, få adgang til nøgler og hemmeligheder fra et program osv. Disse oplysninger findes allerede et andet sted. Implementering af certifikater, der er gemt i vigtige samling til FOS dækkes i et [blogindlæg](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), og der er [kodeeksempler](https://www.microsoft.com/download/details.aspx?id=45343) tilgængelige, der viser, hvordan du bruger bootstrap certifikat til at godkende Azure AD til at få adgang til vigtige samling.

De fleste af adgangstilladelserne kan tildeles ved hjælp af Azure portalen, men hvis du vil give detaljerede tilladelser du muligvis bruge Azure PowerShell (eller Azure CLI) for at opnå det ønskede resultat. 

De følgende PowerShell-kodestykker forudsætter:

- Azure Active Directory-administrator har oprettet sikkerhedsgrupper, der repræsenterer de tre roller, nemlig Contoso Security Team, Contoso App Devops Contoso App Auditors. Administratoren har også føjet brugere til de grupper, de tilhører.

- **ContosoAppRG** er ressourcegruppen, hvor alle ressourcer, der er placeret. **contosologstorage** er, hvor logfilerne er gemt. 

- Vigtige samling **ContosoKeyVault** og lager konto, der bruges til vigtige samling logge **contosologstorage** skal være den samme Azure placering


Først tildeler abonnement administratoren 'centrale samling bidragyder' og ' Brugeradministrator Access' roller til sikkerhed teamet. Dette giver mulighed for sikkerhed team til at administrere adgang til andre ressourcer og administrere vigtige vaults i ressourcegruppen ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Følgende script viser, hvordan sikkerhed teamet kan oprette en vigtige samling, konfiguration af logføring og angive adgangstilladelser til andre roller og -programmet. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

Den brugerdefinerede rolle, der er defineret, kan kun tildeles til abonnementet, hvor ressourcegruppe ContosoAppRG er oprettet. Hvis de samme brugerdefinerede roller, der skal bruges til andre projekter i andre abonnementer, kan det er omfang har flere abonnementer, der er tilføjet.

Den brugerdefinerede rolletildeling for udviklere/operatorer for tilladelsen "installere/handling" er fastsat til ressourcegruppen. Denne måde kun FOS, som er oprettet i ressourcegruppe 'ContosoAppRG' får hemmeligheder (SSL-certifikat og bootstrap cert). En hvilken som helst VM'er, der opretter et medlem af Udviklingscenter/ops team i andre Ressourcegruppe vil ikke kunne få disse hemmeligheder, selvom de vidste hemmeligt URI'er.

I dette eksempel vises et enkelt scenarie. Virkelige liv scenarier kan være mere komplekse, og du muligvis tilpasse tilladelser til din nøgle samling, der er baseret på dine behov. Antag f.eks., i vores eksempel vi sikkerhed teamet får nøgle og hemmeligt referencer (URI'er og thumbprints) det udviklere/operatorer team skal referere til i deres programmer. Det vil sige, de behøver at give udviklere/operatorer en hvilken som helst dataadgang plan. Bemærk også, at dette eksempel fokuserer på sikring af dine vigtige samling. Lignende betaling skal tildeles til at sikre [dine FOS](https://azure.microsoft.com/services/virtual-machines/security/), [lagerplads konti](../storage/storage-security-guide.md) og andre Azure ressourcer for.

>[AZURE.NOTE] Bemærk: Dette eksempel vises, hvordan vigtige samling access låses fremstilling. Udviklerne bør have sin egen abonnement eller resourcegroup hvor de har fuld tilladelse til at administrere deres vaults, FOS og lagerplads konto hvor de udvikling af programmet.


## <a name="resources"></a>Ressourcer

-   [Azure Active Directory rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)

    Denne artikel forklares det Azure Active Directory rollebaseret adgangskontrol, og hvordan det fungerer.

-   [RBAC: Indbygget i roller](../active-directory/role-based-access-built-in-roles.md)

    I denne artikel indeholder oplysninger om alle de indbyggede roller tilgængelige i RBAC.

-   [Forstå ressourcestyring installation og klassisk installation](../resource-manager-deployment-model.md)

    I denne artikel beskrives de ressourcestyring installation og klassisk installation modeller og beskriver fordelene ved at bruge grupperne ressourcestyring og ressource

-    [Administrere rollebaseret adgangskontrol med Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

     I denne artiklen forklares, hvordan du administrerer rollebaseret adgangskontrol med Azure PowerShell

-   [Administration af rollebaseret adgangskontrol med REST API'ER](../active-directory/role-based-access-control-manage-access-rest.md)

    I denne artikel viser, hvordan du bruger REST-API til at administrere RBAC.

-   [Rollebaseret adgangskontrol til Microsoft Azure fra Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Dette er et link til en video på kanal 9 fra 2015 MS Ignite konferencen. I denne session de taler om adgang til administration og rapporteringsfunktioner i Azure og udforske bedste metoder til sikring af adgang til Azure abonnementer ved hjælp af Azure Active Directory.

-   [Give adgang til webprogrammer ved hjælp af OAuth 2.0 og Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    I denne artikel beskrives fuldført OAuth 2.0-rutediagram til godkendelse af med Azure Active Directory.

-   [vigtige samling Management REST API'er](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Dette dokument er referencen til de REST API'er til at administrere din nøgle samling fra et program, herunder indstille vigtige samling access-politik.

-   [vigtige samling REST API'er](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Link til vigtige samling REST-API referencedokumentation.

-   [Vigtige adgangskontrol](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Link til dokumentationen til tasten access control reference.

-   [Hemmeligt adgangskontrol](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Link til dokumentationen til tasten access control reference.

-   [Angive](https://msdn.microsoft.com/library/mt603625.aspx) og [fjerne](https://msdn.microsoft.com/library/mt619427.aspx) vigtige samling access-politik ved hjælp af PowerShell

    Links til at referere til dokumentationen til PowerShell-cmdlet'er til at administrere vigtige samling access-politik.

## <a name="next-steps"></a>Næste trin

Se [Introduktion til Azure vigtige samling](key-vault-get-started.md)et få Introduktion selvstudium for en administrator.

Se [Azure vigtige samling logføring](key-vault-logging.md)kan finde flere oplysninger om brugen af logføring til vigtige samling.

Se [om nøgler og hemmeligheder](https://msdn.microsoft.com/library/azure/dn903623.aspx)kan finde flere oplysninger om brug af nøgler og hemmeligheder med Azure vigtige samling.

Hvis du har spørgsmål om vigtige samling, skal du besøge [Azure vigtige samling fora](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
