<properties
   pageTitle="Nøglen Vault Developer's Guide | Microsoft Azure"
   description="Udviklere kan bruge Azure nøgle Vault administrerer kryptografiske nøgler i Microsoft Azure-miljøet. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Azure nøgle Vault Developer's Guide
Ved hjælp af nøglen Vault, du vil kunne opnå sikker adgang til følsomme oplysninger fra dine programmer således, at:

- Nøgler og hemmeligheder beskyttet uden at skulle skrive koden selv og kan nemt bruge dem i dine programmer.
- Du vil kunne have dine egne kunder og administrere deres egne nøgler, så du kan koncentrere dig om at give kernen softwarefunktioner. På denne måde skal programmerne ikke eje ansvar eller potentielle påtager sig ansvaret for kundernes lejer nøgler og hemmeligheder.
- Programmet kan bruge nøglerne til signering og kryptering endnu holder de nøgleadministration eksterne fra dit program således, at løsningen er egnet til et program, der er geografisk fordelt.

- Med udgivelsen af nøgle Vault September 2016, programmerne kan nu gøre brug af nøgle Vault certifikater. Yderligere oplysninger, se **om nøgler og certifikater hemmeligheder,** artikel i [RESTEN reference](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Mere generelle oplysninger om Azure nøgle Vault, se [nøgle Vault](key-vault-whatis.md).

## <a name="videos"></a>Videoer
Denne video viser dig, hvordan du opretter dine egne nøgler vault og hvordan du bruger den fra eksempelprogrammet 'Hej nøgle Vault'.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Links til ressourcer, der er nævnt i videoen:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure nøgle Vault eksempelkode](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Hvis du vil vide du mere Følg [Nøgle Vault Blog](http://aka.ms/kvblog) og deltage i [Nøglen Vault Forum](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Oprettelse og administration af centrale Vaults

Før du arbejder med Azure nøgle Vault i din kode, kan du oprette og administrere vaults gennem RESTEN, Resource Manager skabeloner, PowerShell eller CLI, som beskrevet i følgende artikler:

- [Opret og Administrer nøgler Vaults med RESTEN](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Opret og Administrer nøgler Vaults med PowerShell](key-vault-get-started.md)
- [Opret og Administrer nøgler Vaults med CLI](key-vault-manage-with-cli.md)
- [Oprette en nøgle vault og tilføje en hemmelighed via en Azure Resource Manager-skabelon](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operationer mod nøgle vaults er godkendt gennem AAD og tilladt gennem nøgle Vaults egne adgangspolitik, defineret pr. vault.

## <a name="coding-with-key-vault"></a>Kodning af nøgle Vault

Nøgle Vault styringssystem for programmører, der består af flere grænseflader med RESTEN som grundlag, [Nøgle Vault RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Du kan, efter vellykket tilladelse gøre følgende:

- Administrer kryptografiske nøgler ved hjælp af [oprettelse](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [opdatering](https://msdn.microsoft.com/library/azure/dn903616.aspx), [slette](https://msdn.microsoft.com/library/azure/dn903611.aspx) og andre operationer

- Administrere hemmeligheder ved hjælp af [få](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Opdater](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Slet](https://msdn.microsoft.com/library/azure/dn903613.aspx) og andre operationer

- Bruger kryptografiske nøgler med [fortegn](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Kontroller](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) og [Krypter](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[dekryptere](https://msdn.microsoft.com/library/azure/dn878097.aspx) operationer

Der findes følgende SDK'er til at arbejde med Key Vault:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK-dokumentationen](https://msdn.microsoft.com/library/mt765854.aspx)|[Node.js SDK-dokumentationen](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK-pakken på Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Node.js SDK-pakken](https://www.npmjs.com/package/azure-keyvault)|

Du kan finde flere oplysninger om 2.x versionen af .NET SDK, [Produktbemærkninger](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Eksempler på kode
Komplet eksempler på brug af nøgle Vault med dine programmer, finder du:

- Eksempelprogrammet .NET *HelloKeyVault* og Azure web service eksempel. [Azure nøgle Vault-kodeeksempler](http://www.microsoft.com/download/details.aspx?id=45343)
- Selvstudium til at lære at bruge Azure nøgle Vault fra et webprogram i Azure. [Bruge Azure nøgle Vault fra et webprogram](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Sådan gør du

Følgende artikler og scenarier indeholder opgaven konkret vejledning til arbejdet med Azure nøgle Vault:

- [Skift nøgle vault lejer ID efter abonnement flyttet](key-vault-subscription-move-fix.md) - når du flytter abonnementet Azure fra lejer A til B lejer, din eksisterende nøgle vaults er ikke tilgængelige ved sikkerhedskonti (brugere og programmer) i lejer B. rette dette ved hjælp af denne vejledning.
- [Adgang til nøglen Vault bag firewall](key-vault-access-behind-firewall.md) - adgang til en nøgle vault klientprogrammet nøgle vault skal kunne få adgang til flere endepunkter for forskellige funktioner.

- [Sådan Generer og Transfer HSM-Protected taster til Azure nøgle Vault](key-vault-hsm-protected-keys.md) - dette hjælper dig med at planlægge, oprette og derefter overføre dine egne HSM-beskyttede nøgler til brug sammen med Azure nøgle Vault.
- [Sådan overføres sikkert værdier (f.eks. adgangskoder) under installationen](../resource-manager-keyvault-parameter.md) - når du skal overføre en sikker værdi (f.eks. en adgangskode) som en parameter under installationen, kan du gemme værdien som en hemmelighed i en Azure nøgle Vault og reference værdien i andre skabeloner til Resource Manager.
- [Sådan bruges nøgle Vault for extensible nøgleadministration med SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - den SQL Server-tilslutning til Azure nøgle Vault gør det muligt for SQL Server og SQL i en VM at udnytte tjenesten Azure nøgle Vault som udbyder Extensible Key Management (EKM) til at beskytte sine egne krypteringsnøgler til programmer link; Gennemskuelig datakryptering, sikkerhedskopiering kryptering og kryptering på blokniveau kolonne.
- [Hvordan du installerer certifikater til FOS fra nøgle Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - en sky-program, der kører i et VM på Azure skal have et certifikat. Hvordan du får dette certifikat til dette VM i dag?
- [Sådan setup nøgle Vault med overvågning og til slut nøgle rotation](key-vault-key-rotation-log-monitoring.md) - dette beskrives, hvordan du setup nøgle rotation og overvågning med Azure nøgle Vault.

Du kan finde yderligere opgave-specifikke retningslinjer for integration og ved hjælp af nøglen Vaults med Azure, [Ryan Jensen Azure Resource Manager skabelon eksempler for nøgle Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integreret med nøgle Vault

Disse artikler handler om andre scenarier og -tjenester, der gør os om eller integrere med nøgle Vault.

- [Azure harddiskkryptering](../security/azure-security-disk-encryption.md) udnytter branchens standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows og [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux til at give kryptering af diskenheder til OS og data-diske. Løsningen er integreret med Azure nøgle Vault kan hjælpe dig med at styre og administrere disk krypteringsnøgler og hemmeligheder i abonnementet nøgle vault, samtidig med at sikre, at alle data i de virtuelle maskine diske er krypteret i ro. i din Azure storage.


## <a name="supporting-libraries"></a>Understøttelse af biblioteker

- [Microsoft Azure nøgle Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) indeholder `IKey` og `IKeyResolver` grænseflader til at finde nøgler fra id'er og udføre handlinger med nøgler.

- [Microsoft Azure nøgle Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) giver udvidede muligheder for Azure nøgle Vault.

## <a name="other-key-vault-resources"></a>Andre nøgle Vault-ressourcer
- [Vigtigste Vault Blog](http://aka.ms/kvblog)
- [Vigtigste Vault-Forum](http://aka.ms/kvforum)
