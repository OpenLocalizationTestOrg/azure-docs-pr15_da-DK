<properties
    pageTitle="Oprette SSH taster på Linux- og Mac | Microsoft Azure"
    description="Oprette og bruge SSH taster på Linux- og Mac for ressourcestyring og klassisk installation modeller på Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Oprette SSH taster på Linux- og Mac for Linux FOS i Azure

Du kan oprette virtuelle maskiner på Azure, som standard til at bruge SSH taster til godkendelse, fjernes behovet for adgangskoder til at logge på med en SSH nøglepar.  Adgangskoder kan brydes, og Åbn din FOS op til nådesløse råstyrke forsøg på at gætte din adgangskode. VM'er, der er oprettet med Azure skabeloner eller `azure-cli` kan medtage dit SSH offentlig nøgle som en del af den installation, hvis du fjerner en installationskonfiguration af indlæg.  Hvis du opretter forbindelse til en Linux VM fra Windows, se [dette dokument.](virtual-machines-linux-ssh-from-windows.md)

I artiklen kræver:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) logget på med`azure login`

- Azure CLI _skal være i_ Azure ressourcestyring tilstand`azure config mode arm`

## <a name="quick-commands"></a>Hurtig kommandoer

Erstat eksemplerne i følgende kommandoer, med dine egne valg.

SSH nøgler er som standard bevares den `.ssh` directory.  

```bash
cd ~/.ssh/
```

Hvis du ikke har en `~/.ssh` directory den `ssh-keygen` kommandoen opretter den for dig med de rette tilladelser.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Skriv navnet på den fil, der er gemt i den `~/.ssh/` directory:

```bash
id_rsa
```

Angiv adgangskoden for id_rsa:

```bash
correct horse battery staple
```

Der er nu en `id_rsa` og `id_rsa.pub` SSH vigtige par i den `~/.ssh` directory.

```bash
ls -al ~/.ssh
```

Tilføje den nye nøgle til `ssh-agent` på Linux- og Mac (også føjet til OSX nøglering):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Kopiér SSH offentlig nøgle til din Linux-Server:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Teste logon ved hjælp af taster i stedet for en adgangskode:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Detaljeret gennemgang

Brug af SSH offentlige og private nøgler er den nemmeste måde at logge på din Linux-servere. [Kryptering med offentlig nøgle](https://en.wikipedia.org/wiki/Public-key_cryptography) indeholder en meget mere sikker måde til at logge på din Linux eller BSD VM i Azure end adgangskoder, der kan være brute tvunget langt nemmere. Din offentlige nøgle kan blive delt med alle; men kun du (eller infrastrukturen lokal sikkerhed) i besiddelse af din privat nøgle.  Tasten SSH privat skal have en [meget sikker adgangskode](https://www.xkcd.com/936/) (kilde:[xkcd.com](https://xkcd.com)) til at beskytte den.  Denne adgangskode er blot at få adgang til private SSH nøgle og **er ikke** adgangskoden til brugerkonto.  Når du føjer en adgangskode til din SSH nøgle, krypterer privat nøgle, så den private nøgle er ubrugelig uden adgangskoden for at låse den op.  Hvis en hacker stjålet din private nøgle, og tasten ikke har en adgangskode, vil de kunne bruge denne privat nøgle til at logge på en hvilken som helst servere, der har den tilsvarende offentlige nøgle.  Hvis en privat nøgle er beskyttet med adgangskode, den kan ikke bruges af pågældende hacker, give en ekstra sikkerhedslag til infrastrukturen på Azure.

I denne artikel opretter *ssh rsa* formateret vigtige filer, som er anbefalet til installationer på ressourcestyring.  *SSH rsa* -nøgler er påkrævet i [portalen](https://portal.azure.com) til både klassisk og ressourcestyring installationer.

## <a name="create-the-ssh-keys"></a>Oprette tasterne SSH

Azure kræver mindst 2048-bit ssh rsa formatere offentlige og private nøgler. Oprette taster Brug `ssh-keygen`, der beder om en række spørgsmål og derefter skriver en privat nøgle og en tilsvarende offentlig nøgle. Når der oprettes en Azure VM, kopieres den offentlige nøgle til `~/.ssh/authorized_keys`.  SSH taster i `~/.ssh/authorized_keys` bruges til at udfordring klienten, så det svarer til den tilsvarende private nøgle på en SSH logon-forbindelse.

## <a name="using-ssh-keygen"></a>Brug af ssh keygen

Denne kommando opretter en adgangskode, sikret (krypteret) SSH nøglepar ved hjælp af 2048-bit RSA, og det er kommenterede nemmere at identificere den.  

Start ved at ændre kataloger, så alle dine ssh taster er oprettet i denne mappe.

```bash
cd ~/.ssh
```

Hvis du ikke har en `~/.ssh` directory den `ssh-keygen` kommandoen opretter den for dig med de rette tilladelser.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Kommandoen forklaring_

`ssh-keygen`= det program, der bruges til at oprette de pågældende taster

`-t rsa`= type nøgle til at oprette, som er den [RSA format](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bit over vigtigste

`-C "myusername@myserver"`= en kommentar, der er føjet til slutningen af den offentlige nøgle fil, der nemt identificere den.  Normalt en mail, der bruges som kommentaren, men du kan bruge det fungerer bedst til din infrastruktur.

### <a name="using-pem-keys"></a>Ved hjælp af PEM nøgler

Hvis du bruger klassiske installere model (Azure klassisk Portal eller Azure Service Management CLI `asm`), skal du muligvis bruge PEM formateret SSH taster for at få adgang til din Linux FOS.  Sådan kan du oprette en PEM nøgle fra en eksisterende SSH offentlig nøgle og en eksisterende x509 certifikat.

Hvis du vil oprette en PEM er formateret nøgle fra en eksisterende SSH offentlig nøgle:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Eksempel på ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Gemte vigtige filer:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

Nøgle navn til denne artikel.  Har du et vigtige par med navnet **id_rsa** er standard, og nogle værktøjer måske havde forventet **id_rsa** privat nøgle filnavnet, så har du en er en god ide. Mappen `~/.ssh/` er standardplaceringen for SSH vigtige par og SSH config-filen.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
En oversigt over de `~/.ssh` directory. `ssh-keygen`opretter den `~/.ssh` directory, hvis det er ikke til stede og også angiver tilstandene for den korrekte ejerskab og filer.

Vigtige adgangskode:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`refererer til en adgangskode, som "en adgangskoden."  Det er *på det kraftigste* anbefales at tilføje en adgangskode til din nøgle par. Uden en adgangskode, beskytte parret nøgle, kan alle med filen privat nøgle bruge det til at logge på en server, der har den tilsvarende offentlige nøgle. Tilføje en adgangskode giver bedre beskyttelse i tilfælde af en person er kunne få adgang til dine private nøgler fil givet dig tid til at ændre tasterne, bruges til at godkende dig.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Brug ssh agent til at gemme adgangskoden privat nøgle

For at undgå at skrive adgangskoden privat nøgle fil med hvert SSH logon, kan du bruge `ssh-agent` til at cachelagre adgangskoden privat nøgle fil. Hvis du bruger en Mac, OSX Nøgleringen sikkert gemmer privat nøgle adgangskoderne, når du starter `ssh-agent`.

Kontrollér, at første `ssh-agent` kører

```bash
eval "$(ssh-agent -s)"
```

Tilføj nu den private nøgle til `ssh-agent` ved hjælp af kommandoen `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Privat nøgle adgangskoden er nu gemt i `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Oprette og konfigurere en SSH konfigurationsfil

Det er en anbefalede bedste fremgangsmåde til at oprette og konfigurere en `~/.ssh/config` fil for at øge hastigheden af log in-programmer og til at optimere din SSH klient adfærd.

I følgende eksempel viser en standard konfiguration.

### <a name="create-the-file"></a>Oprette filen

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Rediger filen for at tilføje den nye SSH konfiguration:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Eksempel på `~/.ssh/config` fil:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Denne SSH config giver dig sektioner for hver serveren for at aktivere hver have sin egen dedikeret vigtige par. Standardindstillingerne (`Host *`) er for en hvilken som helst værter, der ikke matcher nogen af de specifikke værter højere op i filen config.


### <a name="config-file-explained"></a>Konfigurationsfil forklaring

`Host`= navnet på værten der med navnet på terminalen.  `ssh fedora22`fortæller `SSH` til at bruge værdierne i indstillinger for blokering mærket `Host fedora22` NOTE: det kan være en hvilken som helst etiket, der er logisk for at se din formatet og repræsenterer ikke den faktiske hostname af enhver server.

`Hostname 102.160.203.241`= IP-adresse eller DNS-navn til den server, der åbnes.

`User myusername`= remote brugerkontoen til bruges til at logge på serveren.

`PubKeyAuthentication yes`= fortæller SSH, du vil bruge en SSH nøgle til at logge på.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= SSH privat nøgle og tilsvarende offentlig nøgle skal bruges til godkendelse.


## <a name="ssh-into-linux-without-a-password"></a>SSH til Linux uden en adgangskode

Nu hvor du har en SSH vigtige par og en konfigurerede SSH konfigurationsfil, er du kunne logge på din Linux VM hurtigt og sikkert. Første gang du logger på en server, ved hjælp af en SSH nøgle kommandoen instruktionerne du til adgangskoden for den pågældende vigtige fil.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Kommandoen forklaring

Når `ssh fedora22` udføres SSH først finder og indlæser nogen indstillinger fra den `Host fedora22` Bloker, og klik derefter indlæse alle de resterende indstillinger fra den sidste blok `Host *`.

## <a name="next-steps"></a>Næste trin

Næste op er at oprette Azure Linux FOS ved hjælp af den nye SSH offentlige nøgle.  Azure VM'er, der er oprettet med en offentlig nøgle SSH som logon er bedre sikret end VM'er, der er oprettet med standard login metoden adgangskoder.  Azure VM'er, der er oprettet ved hjælp af SSH taster er som standard konfigureret med adgangskoder, der er deaktiveret, undgår brute tvunget gætte forsøg.

- [Oprette en sikker Linux VM ved hjælp af en Azure skabelon](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Oprette en sikker Linux VM ved hjælp af portalen Azure](virtual-machines-linux-quick-create-portal.md)
- [Oprette en sikker Linux VM ved hjælp af Azure CLI](virtual-machines-linux-quick-create-cli.md)
