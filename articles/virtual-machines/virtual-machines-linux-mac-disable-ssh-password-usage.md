<properties
    pageTitle="Deaktivere SSH adgangskoder på din Linux VM ved at konfigurere SSHD | Microsoft Azure"
    description="Beskytte din Linux VM på Azure ved at deaktivere adgangskode-logon til SSH."
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
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Deaktivere SSH adgangskoder på din Linux VM ved at konfigurere SSHD

I denne artikel fokuserer på, hvordan du låser login sikkerheden for din Linux VM.  Så snart SSH port 22 åbnes til verden BOT'er start forsøger at logge på ved at gætte adgangskoder.  Hvad vi vil gøre i denne artikel er Deaktiver adgangskode logon via SSH.  Ved helt at fjerne muligheden for at bruge adgangskoder beskytter vi Linux VM fra denne type råstyrke angreb.  En ekstra bonus er vi vil konfigurere Linux SSHD at kun tillade logon via SSH offentlige og private nøgler langt den sikreste måde at logge på Linux.  De mulige kombinationer af det ville kræve for at gætte privat nøgle er store, og derfor modvirker BOT'er fra lige Generer til at forsøge at råstyrke SSH taster.


## <a name="goals"></a>Mål

- Konfigurere SSHD for ikke at tillade:
  - Adgangskode-logon
  - Rod brugerens logon
  - Udfordring response-godkendelse
- Konfigurere SSHD tillade:
  - kun SSH vigtige logon
- Genstart SSHD mens stadig er logget på
- Teste den nye SSHD-konfiguration

## <a name="introduction"></a>Introduktion

[SSH defineret](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD er den SSH Server, der kører på Linux VM.  SSH er en klient, der kører fra en shell på computeren MacBook eller Linux.  SSH er også den protokol, der bruges til at sikre og kryptere kommunikationen mellem computeren og Linux VM.

I denne artikel, er det vigtigt at holde åbne én logge på din Linux VM for hele gennemgang.  Derfor vi åbnes to klienter og SSH til Linux VM fra dem begge.  Vi bruger den første terminal til at ændre SSHDs konfigurationsfil og genstart tjenesten SSHD.  Vi bruger den anden terminal til at teste ændringerne, når tjenesten genstartes.  Da vi er ved at deaktivere SSH adgangskoder og stole absolut på SSH taster, hvis dine SSH nøgler ikke er korrekte, og du afbryde forbindelsen til VM, bliver VM på permanent låst og ingen vil ikke kunne logge på det kræve at blive slettet og genoprettet.

## <a name="prerequisites"></a>Forudsætninger

- [Oprette SSH taster på Linux- og Mac for Linux FOS i Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Azure-konto
  - [gratis prøveversion tilmelding](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure-portalen](http://portal.azure.com)
- Linux VM kører på azure
- SSH offentlige og private nøgler par i`~/.ssh/`
- SSH offentlig nøgle i `~/.ssh/authorized_keys` på Linux VM
- Sudo rettigheder til VM
- Port 22 åben

## <a name="quick-commands"></a>Hurtig kommandoer

_Erfaren Linux Admins der blot ønsker TLDR version start her.  Detaljeret forklaring og gennemgang springe for alle andre, som ønsker dette afsnit._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Detaljeret gennemgang

Log på Linux VM på terminal 1 (T1).  Log på Linux VM på terminal 2 (T2).

Vi vil redigere filen SSHD konfiguration på T2.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Herfra kan vi redigere indstillingerne for at deaktivere adgangskoder og aktivere SSH vigtige logon.  Der findes mange indstillingerne i denne fil, du skal undersøge og ændre for at sikre Linux og SSH så beskyttede, som du har brug for.

#### <a name="disable-password-logins"></a>Deaktivere adgangskode logon

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Aktivere offentlig nøgle-godkendelse

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Deaktivere rod logon

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Deaktivere udfordring response-godkendelse

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Genstart SSHD

Kontrollér, at du stadig er logget på fra T1 shell.  Dette er vigtigt, så du ikke få låst ude af din VM Hvis dine SSH nøgler ikke er korrekte, da adgangskoder nu er deaktiveret.  Hvis alle indstillinger er forkerte på din Linux VM du kan bruge T1 til at rette sshd_config, mens du stadig vil blive logført i og SSH vil holde forbindelsen åben under tjenesten SSHD genstarte.

Fra T2 køre:

##### <a name="on-the-debian-family"></a>På Debian familien

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>På RedHat familie

```
username@macbook$ sudo service sshd restart
```

Adgangskoder er nu deaktiveret på din VM beskyttelse mod råstyrke adgangskode logonforsøg.  Tilladt du kan logge på hurtigere og meget mere sikker med kun SSH taster.
