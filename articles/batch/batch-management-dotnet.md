<properties
    pageTitle="Konto ressourcestyring med batchen Management .NET | Microsoft Azure"
    description="Oprette, slette og ændre Azure batchen konto ressourcer med batchen Management .NET-bibliotek."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Administrere Azure batchen konti og kvoter med batchen Management .NET

> [AZURE.SELECTOR]
- [Azure-portalen](batch-account-create-portal.md)
- [Batchen Management .NET](batch-management-dotnet.md)

Du kan reducere vedligeholdelse faste i programmerne Azure batchen ved hjælp af [Batchen Management .NET] [ api_mgmt_net] bibliotek til at automatisere oprettelse af batchen en konto, sletningen, key management og kvote registrering.

- **Oprette og slette batchen konti** i et område. Hvis, som en uafhængig softwareproducent () for eksempel, du angiver en tjeneste til dine kunder, som hver er tildelt en separat batchen-konto til fakturering formål, kan du tilføje funktioner til oprettelse og sletning af konto til din kunde-portal.
- **Hent og Regenerer konto nøgler** fra et program på grund af kontiene batchen. Dette kan hjælpe dig med at overholde sikkerhedspolitikker, håndhæves periodiske overgang eller udløb konto taster. Når du har flere batchen konti i forskellige Azure områder, øges automatisering af denne overgang proces din løsning effektivitet.
- **Markér konto kvoter** og tage prøveversion og fejl gætteriet afgøre, hvilke batchen konti har hvilke begrænsninger. Oprette grupper, eller tilføje Beregn noder, du kan justere hvor proaktiv ved at markere kvoter for din konto, før du starter job, eller når disse beregne ressourcer oprettes. Du kan bestemme, hvilke konti kræver kvote øges før tildeling af yderligere ressourcer på disse konti.
- **Kombinere funktioner i andre Azure tjenester** til en komplet management oplevelse – ved hjælp af batchen Management .NET, [Azure Active Directory][aad_about], og [Azure ressourcestyring] [ resman_overview] sammen i det samme program. Ved hjælp af disse funktioner og deres API'er, kan du angive en frictionless godkendelse oplevelse, muligheden for at oprette og slette grupper, og de funktioner, der er beskrevet ovenfor til en til en komplet løsning.

> [AZURE.NOTE] Mens i denne artikel fokuserer på programmeringsmæssig styringen af dit batchen konti, nøgler og kvoter, kan du udføre mange af disse aktiviteter ved hjælp af [Azure portal][azure_portal]. Se [oprette en Azure batchen konto ved hjælp af portalen Azure](batch-account-create-portal.md) og [kvoter og -begrænsninger for tjenesten Azure batchen](batch-quota-limit.md)kan finde flere oplysninger.

## <a name="create-and-delete-batch-accounts"></a>Oprette og slette batchen konti

Som nævnt, er en af de primære funktioner i batchen Management API til at oprette og slette batchen konti på et Azure område. Gør du ved at bruge [BatchManagementClient.Account.CreateAsync] [ net_create] og [DeleteAsync][net_delete], eller deres synkron versionerne.

Følgende kodestykke opretter en konto, henter den nyoprettede konto fra tjenesten batchen og sletter den. I denne kodestykke og de andre i denne artikel `batchManagementClient` er en fuldt initialiseret forekomst af [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Programmer, der bruger biblioteket batchen Management .NET og klassens BatchManagementClient kræver **tjenesteadministratoren** eller **coadministrator** adgang til det abonnement, der ejer kontoen batchen administreres. Du kan finde flere oplysninger i afsnittet [Azure Active Directory](#azure-active-directory) og [AccountManagement] [ acct_mgmt_sample] eksempel-kode.

## <a name="retrieve-and-regenerate-account-keys"></a>Hente og for at gendanne konto taster

Indhente primære og sekundære konto taster fra en hvilken som helst batchen konto i dit abonnement ved hjælp af [ListKeysAsync][net_list_keys]. Du kan genoprette disse taster ved hjælp af [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Du kan oprette en arbejdsproces for strømlinet forbindelse til dine management-programmer. Først skal hente en kontonøgle for kontoen batchen, du vil administrere med [ListKeysAsync][net_list_keys]. Brug derefter denne tast, når initialisering af biblioteket batchen .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] klasse, som bruges, når initialisering af [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrollere Azure-abonnement og kvoter for batchen konto

Azure-abonnementer og de individuelle Azure tjenester som Batch alle har standard kvoter, begrænser antallet af visse enheder grænserne. Se [Azure-abonnement og grænser, kvoter, og begrænsninger](./../azure-subscription-service-limits.md)for standard-kvotaerne for Azure abonnementer. Standard kvoter for tjenesten batchen, under [kvoter og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md). Ved hjælp af biblioteket batchen Management .NET, kan du se disse kvoter i dine programmer. Dette kan du tage beslutninger om allokering, før du kan tilføje konti eller beregne ressourcer som grupper og beregne noder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrollere en Azure-abonnement til batchen konto kvoter

Før du opretter en batchen konto i et område, kan du kontrollere din Azure abonnement for at se, om du er i stand til at tilføje en konto i det pågældende område.

I nedenstående kodestykke vi først bruge [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] at få en samling af alle batchen konti, der er i et abonnement. Når vi har anskaffet denne samling, bestemme vi, hvor mange konti er i området mål. Derefter bruger vi [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] til at få kvoten batchen konto og finde ud af, hvor mange konti (hvis relevant) kan oprettes i det pågældende område.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

I kodestykket ovenfor, `creds` er en forekomst af [TokenCloudCredentials][azure_tokencreds]. For at se et eksempel på oprettelse af dette objekt skal du se [AccountManagement] [ acct_mgmt_sample] kodeeksempel på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrollere en batchen konto for Beregn kvoter for serverforbrug

Før du kan øge Beregn ressourcer i din batchen løsning, kan du se for at sikre, at de ressourcer, der skal tildeles ikke overstiger firmaets kvoter. Vi udskrive kvote oplysningerne om batchen kontoen i kodestykke nedenfor `mybatchaccount`. Du kan bruge disse oplysninger til at afgøre, om kontoen, der kan håndtere de yderligere ressourcer skal have oprettet i dit eget program.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Selvom der findes standard kvoter for Azure abonnementer og -tjenester, mange af disse begrænsninger kan opløftet ved at udstede en anmodning i [Azure portal][azure_portal]. Se eksempelvis [kvoter og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md) vejledning i at øge din kvoter for batchen konto.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Batch Management .NET, Azure AD og ressourcestyring

Når du arbejder med batchen Management .NET-bibliotek, du typisk også bruge [Azure Active Directory] [ aad_about] (Azure AD) og [Azure ressourcestyring][resman_overview]. Eksempelprojektet beskrevet nedenfor bruger både Azure Active Directory og ressourcestyring, mens det viser batchen Management .NET API.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure selve bruger Azure AD til godkendelse af dens kunder, tjenesteadministratorer og organisatoriske brugere. I forbindelse med batchen Management .NET, kan du bruge Azure AD for at godkende en administrator af abonnement eller samtidig administrator. Dette giver mulighed for biblioteket administration til at forespørge tjenesten batchen og Udfør de handlinger, der er beskrevet i denne artikel.

I eksempelprojektet gennemgås under Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) bruges til at spørge brugeren om deres Microsoft-legitimationsoplysninger. Når tjenesten administrator eller coadministrator legitimationsoplysninger leveres, kan programmet forespørgsel Azure for en liste over abonnementer – og kan oprette og slette både grupper og batchen konti.

### <a name="resource-manager"></a>Ressourcestyring

Når du opretter batchen konti med batchen Management .NET-bibliotek, du vil typisk oprette dem i en [ressourcegruppe][resman_overview]. Du kan oprette ressourcegruppen fra et program ved hjælp af [ResourceManagementClient] [ resman_client] klasse i [Ressourcestyring .NET] [ resman_api] bibliotek. Eller du kan tilføje en konto til en eksisterende ressourcegruppe, du har oprettet tidligere ved hjælp af [Azure portal][azure_portal].

## <a name="sample-project-on-github"></a>Eksempel-projekt på GitHub

For at se batchen Management .NET i handling skal du se [AccountManagment] [ acct_mgmt_sample] eksempelprojekt på GitHub. Dette program console viser oprettelse og brugen af [BatchManagementClient] [ net_mgmt_client] og [ResourceManagementClient][resman_client]. Det viser også brugen af Azure [Active Directory Authentication Library] [ aad_adal] (ADAL), som kræves af begge klienter.

Hvis du vil køre programmet korrekt, skal du først registrere den med Azure AD ved hjælp af portalen Azure. Følg trinnene i afsnittet [tilføje et program](../active-directory/active-directory-integrating-applications.md#adding-an-application) i [integration programmer med Azure Active Directory] [ aad_integrate] til at registrere eksempelprogrammet i din egen konto er som standard Directory. Sørg for at vælge **Oprindelige klientprogrammet** for typen program, og du kan angive en gyldig URI (såsom `http://myaccountmanagementsample`) for den **Omdirigere URI**– det ikke nødvendigt at være et reelt tal slutpunkt.

Når du har tilføjet dit program, du uddelegere **Access Azure Service Management som organisation** tilladelse til at programmet *Windows Azure Service Management API* programindstillinger i portalen:

![Tilladelser for tjenesteprogram Azure-portalen][2]

> [AZURE.TIP] Hvis **Windows Azure Service Management API** ikke vises under *tilladelser til andre programmer*, klik på **Tilføj program**, Vælg **Windows Azure Service Management API**og derefter klikke på markeringsknappen. Derefter stedfortrædertilladelser som angivet ovenfor.

Når du har tilføjet programmet, som beskrevet ovenfor, opdatere `Program.cs` i [AccountManagment] [ acct_mgmt_sample] eksempel projekt med dit program omdirigere URI og klient-ID. Du kan finde disse værdier under fanen **Konfigurer** i dit program:

![Programkonfiguration Azure-portalen][3]

[AccountManagment] [ acct_mgmt_sample] Northwind demonstrerer følgende handlinger:

1. Få fat på et sikkerhedstoken fra Azure AD ved hjælp af [ADAL][aad_adal]. Hvis brugeren ikke allerede er logget på, bliver de bedt om deres Azure legitimationsoplysninger.
2. Oprette en [SubscriptionClient] ved hjælp af det sikkerhedstoken, som fås fra Azure AD[ resman_subclient] til Azure-forespørgsel til en liste over abonnementer, der er knyttet til kontoen. Dette giver mulighed for markering af et abonnement, hvis der findes flere findes.
3. Oprette et legitimationsoplysninger objekt, der er knyttet til det markerede abonnement.
4. Oprette [ResourceManagementClient] [ resman_client] ved hjælp af legitimationsoplysningerne.
5. Brug [ResourceManagementClient] [ resman_client] til at oprette en ressourcegruppe.
6. Brug [BatchManagementClient] [ net_mgmt_client] til at udføre flere batchen konto handlinger:
  - Oprette en batchen konto i ressourcegruppen ny.
  - Få den nyoprettede konto fra tjenesten batchen.
  - Udskrive tasterne konto til den nye konto.
  - Genoprette en primær nøgle for kontoen.
  - Udskriv kvote oplysningerne for kontoen.
  - Udskrive oplysningerne kvote for abonnementet.
  - Udskrive alle konti i abonnementet.
  - Slette nyoprettede konto.
7. Slette ressourcegruppen.

Før du sletter den nyoprettede Batch konto og ressource gruppe, kan du undersøge både i [Azure portal][azure_portal]:

![Azure-portalen visning af ressourcegruppe og batchen konto][1]
<br />
*Azure portal viser ny ressourcegruppe og batchen konto*

[aad_about]: ../active-directory/active-directory-whatis.md "Hvad er Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Godkendelse scenarier, hvor Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integration af programmer med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
