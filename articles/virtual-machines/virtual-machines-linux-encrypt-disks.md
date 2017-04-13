<properties
   pageTitle="Kryptere diske på en Linux VM | Microsoft Azure"
   description="Sådan krypteres diske på en Linux VM ved hjælp af Azure CLI og implementeringsmodel ressourcestyring"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Kryptere diske på en Linux VM ved hjælp af Azure CLI
For udvidet VM (virtual machine) sikkerhed og overholdelse af regler, kan virtuelle disk i Azure krypteres på resten. Diske er krypteret med cryptographic taster, der er sikret i en Azure-tasten samling af legitimationsoplysninger. Du kan styre disse cryptographic nøgler og kan overvåge deres brug. I denne artikel indeholder oplysninger om hvordan du krypterer virtuelle disk på en Linux VM ved hjælp af Azure CLI og implementeringsmodel ressourcestyring.


## <a name="quick-commands"></a>Hurtig kommandoer
Hvis du har brug for til hurtigt at udføre opgaven, følgende oplysninger i afsnittet grundtallet kommandoer til at kryptere virtuelle disk på din VM. Mere detaljerede oplysninger og konteksten for hvert trin kan finde resten af dokumentet skal [starte her](#overview-of-disk-encryption).

Du skal bruge den [Nyeste Azure CLI](../xplat-cli-install.md) installeret og logget på ved hjælp af Ressourcestyring tilstand på følgende måde:

```
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne omfatter `myResourceGroup`, `myKeyVault`, og `myVM`.

Først skal du aktivere provideren Azure-tasten samling i abonnementet Azure og oprette en ressourcegruppe. I følgende eksempel oprettes et ressourcenavn gruppe `myResourceGroup` i den `WestUS` placering:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Oprette en Azure vigtige samling. I følgende eksempel oprettes en nøgle samling af legitimationsoplysninger med navnet `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Oprette en krypteringsnøgle i din nøgle samling og aktivere den til disk kryptering. I følgende eksempel oprettes en nøgle med navnet `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Oprette et slutpunkt, ved hjælp af Azure Active Directory til håndtering af godkendelsen og udveksling af af cryptographic taster fra nøgle samling. Den `--home-page` og `--identifier-uris` behøver ikke at være faktisk distribueret adresse. Det højeste niveau af sikkerhed, skal klient hemmeligheder bruges i stedet for adgangskoder. Azure CLI kan ikke aktuelt oprette klient hemmeligheder. Klient hemmeligheder kan kun oprettes i portalen Azure. I følgende eksempel oprettes Azure Active Directory ark med navnet `myAADApp` og bruger en adgangskode på `myPassword`. Angiv din egen adgangskode på følgende måde:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Bemærk de `applicationId` vises i output fra en foregående kommando. Dette program-ID bruges i de følgende trin:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Tilføje en datadisk til en eksisterende VM. I følgende eksempel adderes en datadisk til en VM med navnet `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Du kan se detaljerne for din nøgle samling og den nøgle, du har oprettet. Du har brug for den nøgle samling-ID, URI og nøgle URL-adresse i det sidste trin. I følgende eksempel gennemser detaljerne for en nøgle samling af legitimationsoplysninger med navnet `myKeyVault` og nøgle med navnet `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Kryptere disken, således at angive dine egne parameternavne i hele:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI indeholder ikke detaljeret fejl under krypteringsprocessen. Du kan finde flere oplysninger om fejlfinding, gennemse `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Som en foregående kommando har mange variabler, og du kan ikke få meget angivelse som hvorfor processen mislykkes, er et eksempel på en komplet kommando på følgende måde:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Til sidst skal gennemse kryptering status igen for at bekræfte, at din virtuelle disk nu er krypteret. I følgende eksempel kontrolleres status for en VM med navnet `myVM` i den `myResourceGroup` ressourcegruppe:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Oversigt over disk kryptering
Virtuelle disk på Linux FOS er krypteret på resten ved hjælp af [dm crypt](https://wikipedia.org/wiki/Dm-crypt). Der er gratis til at kryptere virtuelle disk i Azure. Cryptographic nøgler lagres i Azure-tasten samling ved hjælp af beskyttelse mod software, eller du kan importere eller oprette dine nøgler i Hardware sikkerhed moduler (HSMs), der er certificeret til at FIPS 140-2 niveau 2 standarder. Du kan bevare kontrollen over disse cryptographic nøgler og kan overvåge deres brug. Disse cryptographic taster bruges til at kryptere og dekryptere virtuelle disk, der er knyttet til din VM. Azure Active Directory ark indeholder en sikker metode til at udstede disse cryptographic taster, som er drevet FOS til og fra.

Processen til at kryptere en VM er som følger:

1. Oprette en krypteringsnøgle i en Azure-tasten samling af legitimationsoplysninger.
2. Konfigurere krypteringsnøgle så den kan bruges til at kryptere diske.
3. For at læse den krypteringsnøgle fra Azure-tasten samling skal du oprette et slutpunkt, ved hjælp af Azure Active Directory med de relevante tilladelser.
4. Udstede kommandoen til at kryptere din virtuelle disk, der angiver Azure Active Directory slutpunkt og relevante krypteringsnøgle skal bruges.
5. Azure Active Directory-slutpunkt, som den nødvendige krypteringsnøgle fra Azure-tasten samling.
6. De virtuelle disk er krypteret med de medfølgende krypteringsnøgle.


## <a name="supporting-services-and-encryption-process"></a>Understøttelse af tjenester og krypteringsproces
Disken kryptering er baseret på følgende yderligere komponenter:

- **Azure nøgle samling** - bruges til at beskytte cryptographic nøgler og hemmeligheder, der bruges til disk kryptering, der er relateret processen. 
  - Hvis der findes en, kan du bruge en eksisterende Azure-tasten samling. Du behøver ikke at angive en samling af legitimationsoplysninger-tasten til at kryptere diske.
  - For at adskille administrative grænser og vigtige synlighed, kan du oprette en dedikeret nøgle samling.
- **Azure Active Directory** - håndterer sikker udveksle af krævede cryptographic nøgler og godkendelse for den anmodede handlinger. 
  - Normalt kan du bruge en eksisterende Azure Active Directory-forekomst for plads til dit program. 
  - Programmet er flere af et slutpunkt for nøgle samling og Virtual Machine til at anmode om og få udstedt de korrekte cryptographic nøgler. Du udvikler ikke et faktisk program, der integreres med Azure Active Directory.


## <a name="requirements-and-limitations"></a>Krav og begrænsninger
Understøttede scenarier og krav til disk kryptering:

- Følgende Linux server SKU'er - Ubuntu, CentOS, SUSE og SUSE Linux Enterprise Server (SLES) og Red Hat Enterprise Linux.
- Alle de ressourcer (som nøgle samling, lagerplads konto og VM) skal være i samme Azure område og abonnement.
- Standard A, D, DS, G og GS række FOS.

Disken kryptering understøttes ikke i øjeblikket i følgende situationer:

- Grundlæggende trin FOS.
- VM'er, der er oprettet ved hjælp af implementeringsmodel klassisk.
- Deaktivere OS disk kryptering på Linux FOS.
- Ved at opdatere cryptographic tasterne på en allerede krypteret Linux VM.


## <a name="create-the-azure-key-vault-and-keys"></a>Oprette Azure-tasten samling og nøgler
For at færdiggøre resten af denne vejledning, skal du [Seneste Azure CLI](../xplat-cli-install.md) installeret og logget på ved hjælp af Ressourcestyring tilstand på følgende måde:

```
azure config mode arm
```

Erstat alle parametre for eksempel med dine egne navne, placering og nøgleværdier i hele eksempler kommandoen. I følgende eksempler bruges en konference af `myResourceGroup`, `myKeyVault`, `myAADApp`osv.

Det første trin er at oprette en Azure-tasten samling for at gemme dine cryptographic nøgler. Azure-tasten samling kan gemme taster, hemmeligheder eller adgangskoder, så du kan implementere dem sikkert i dine programmer og tjenester. Til kryptering af virtuelle disk, kan du bruge tasten samling af legitimationsoplysninger til at gemme en krypteringsnøgle, der bruges til at kryptere eller dekryptere din virtuelle disk. 

Aktivere Azure-tasten samling udbyderen i abonnementet Azure og derefter oprette en ressourcegruppe. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` placering:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure-tasten samling, der indeholder cryptographic nøgler og tilknyttede Beregn ressourcer som lager og VM selve ligge i samme område. I følgende eksempel oprettes en Azure-tasten samling af legitimationsoplysninger med navnet `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Du kan gemme cryptographic taster med software eller Hardware sikkerhed Model (HSM) beskyttelse. Ved hjælp af en HSM kræver en premium nøgle samling. Der er en ekstra omkostning til oprettelse af en premium nøgle samling af legitimationsoplysninger i stedet for standard nøgle samling, der gemmer software-beskyttede taster. Hvis du vil oprette en premium nøgle samling, i det foregående trin tilføje `--sku Premium` til kommandoen. I følgende eksempel bruges software-beskyttede taster, da vi oprettede et standard-tasten samling. 

Azure-platformen skal have tildelt adgang for at anmode om tasterne cryptographic, når VM starter dekryptere de virtuelle disk for både beskyttelse-modeller. Oprette en krypteringsnøgle i din nøgle samling, og derefter aktivere den til brug med virtuel disk kryptering. I følgende eksempel oprettes en nøgle med navnet `myKey` og gør det muligt for disk kryptering:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Oprette Azure Active Directory-program
Når virtuelle disk er krypteret eller dekrypteres, kan du bruge et slutpunkt til at håndtere godkendelsen og udveksling af af cryptographic taster fra nøgle samling. Dette slutpunkt, et Azure Active Directory-program, kan Azure-platformen til at anmode om de relevante cryptographic taster på vegne af VM. En standardforekomst Azure Active Directory er tilgængelig i dit abonnement, selvom mange organisationer har dedikeret Azure Active Directory-mapper.

Når du ikke opretter en komplette Azure Active Directory-program, den `--home-page` og `--identifier-uris` parametre i følgende eksempel behøver ikke at være faktisk distribueret adresse. I følgende eksempel også angiver en adgangskode-baserede hemmeligt i stedet for at generere nøgler fra i Azure portal. I øjeblikket, genererende taster kan ikke udføres fra Azure CLI. 

Oprette dit Azure Active Directory-program. I følgende eksempel oprettes et program med navnet `myAADApp` og bruger en adgangskode på `myPassword`. Angiv din egen adgangskode på følgende måde:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Skal du notere den `applicationId` , der returneres i output fra en foregående kommando. Dette program-ID bruges i nogle af de resterende trin. Opret derefter en tjenestens hovednavn (tjenestens Hovednavn), så programmet er tilgængeligt i dit miljø. Brugere kryptere eller dekryptere virtuelle disk, skal tilladelser på den krypteringsnøgle, der er gemt i nøgle samling af legitimationsoplysninger angives til at tillade Azure Active Directory-program til at læse de pågældende taster. 

Oprette hovednavn for tjenesten og angive de relevante tilladelser på følgende måde:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Tilføje en virtuel disk og kryptering status for gennemsyn
Til at kryptere faktisk nogle virtuelle disk giver mulighed for at tilføje en disk til en eksisterende VM. Føje 5Gb data disken til en eksisterende VM på følgende måde:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

De virtuelle disk krypteres ikke i øjeblikket. Gennemse den aktuelle kryptering status for din VM på følgende måde:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Kryptere virtuelle disk
Hvis du vil nu kryptere de virtuelle disk, skal samler du alle de forrige komponenter:

1. Angiv Azure Active Directory-program og din adgangskode.
2. Angiv den nøgle samling af legitimationsoplysninger for at gemme metadataene for krypterede disken.
3. Angiv tasterne cryptographic skal bruges til faktiske kryptering og dekryptering.
4. Angiv, om du vil kryptere OS disken, datadisce eller alle.

Giver mulighed for at gennemgå oplysningerne for dit Azure-tasten samling af legitimationsoplysninger og den nøgle, du har oprettet, som du har brug for nøgle samling ID, URI, og derefter de primære URL-adresse i det sidste trin:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Kryptere din virtuelle disk ved hjælp af output fra den `azure keyvault show` og `azure keyvault key show` kommandoer på følgende måde:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Som en foregående kommando har mange variabler, er i følgende eksempel kommandoen fuldført til reference:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI indeholder ikke detaljeret fejl under krypteringsprocessen. Du kan finde flere oplysninger om fejlfinding, gennemse `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` på VM du krypterer.

Endelig kan gennemse statussen kryptering igen for at bekræfte, at der nu er krypteret din virtuelle disk:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Tilføje yderligere data diske
Når du har krypterede data disken, kan du senere føjer flere virtuelle disk til din VM og også kryptere dem. Når du kører den `azure vm enable-disk-encryption` kommandoen, stiger sekvens version bruger den `--sequence-version` parameter. Denne sekvens version parameter giver dig mulighed at udføre gentagne handlinger på den samme VM.

For eksempel kan føje en anden virtuel disk til din VM på følgende måde:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Kør kommandoen til at kryptere de virtuelle disk denne tid føje den `--sequence-version` parameter og forøge værdien fra vores første kørsel på følgende måde:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Næste trin

- Finde flere oplysninger om administration af Azure-tasten samling, herunder slette cryptographic nøgler og vaults, [administrere nøgle samling ved hjælp af CLI](../key-vault/key-vault-manage-with-cli.md).
- Du kan finde flere oplysninger om disk kryptering, som forberede en krypteret brugerdefinerede VM til at overføre til Azure, [Azure Disk kryptering](../security/azure-security-disk-encryption.md).