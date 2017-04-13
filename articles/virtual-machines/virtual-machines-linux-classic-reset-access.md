<properties
        pageTitle="Nulstille Linux VM adgangskode og SSH nøgle fra CLI | Microsoft Azure"
        description="Sådan bruger filtypenavnet VMAccess fra Azure kommandolinjen (CLI) til at nulstille en Linux VM adgangskode eller SSH nøgle, løse SSH konfigurationen, og Markér disk konsistens"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Hvordan du kan nulstille en Linux VM adgangskode eller SSH nøgle, løse SSH konfigurationen, og Markér diskplads konsistens ved hjælp af filtypenavnet VMAccess


Hvis du ikke oprette forbindelse til en Linux virtuel maskine på Azure på grund af en glemt adgangskode, en forkert Secure Shell (SSH) nøgle, eller et problem med konfigurationen SSH bruge filtypenavnet VMAccessForLinux med Azure CLI til at nulstille adgangskoden eller SSH nøgle, løse SSH konfigurationen, og Markér disk konsistens. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Med CLI Azure bruge du kommandoen **azure vm lokalnummer set** fra din kommandolinjen (fest, Terminal, kommandoprompt) til access-kommandoer. Køre **azure Hjælp vm lokalnummer angive** for at se detaljerede lokalnummer formatet.

Med Azure CLI, kan du udføre følgende opgaver:

+ [Nulstille adgangskoden](#pwresetcli)
+ [Nulstilling tasten SSH](#sshkeyresetcli)
+ [Nulstille adgangskoden og tasten SSH](#resetbothcli)
+ [Oprette en ny brugerkonto til sudo](#createnewsudocli)
+ [Nulstil SSH konfiguration](#sshconfigresetcli)
+ [Slette en bruger](#deletecli)
+ [Få vist status for filtypenavnet VMAccess](#statuscli)
+ [Kontrollere konsistens tilføjede diske](#checkdisk)
+ [Reparere tilføjede diske på din Linux VM](#repairdisk)


## <a name="prerequisites"></a>Forudsætninger

Du skal gøre følgende:

- Du skal [installere Azure CLI](../xplat-cli-install.md) og [oprette forbindelse til dit abonnement](../xplat-cli-connect.md) til at bruge Azure ressourcer, der er knyttet til din konto.
- Angive den korrekte tilstand for den klassiske implementeringsmodel ved at skrive følgende ved kommandoprompten:
        
        azure config mode asm
        
- Har en ny adgangskode eller et sæt af SSH taster, hvis du vil nulstille enten et. Du behøver ikke disse, hvis du vil nulstille SSH konfigurationen.


## <a name="pwresetcli"></a>Nulstille adgangskoden

1. Oprette en fil med navnet PrivateConf.json med disse linjer. Erstatte de kantede parenteser og & #60; pladsholder & #62; værdier med dine egne oplysninger.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Kør denne kommando, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Nulstilling tasten SSH

1. Oprette en fil med navnet PrivateConf.json med disse indhold. Erstatte de kantede parenteser og & #60; pladsholder & #62; værdier med dine egne oplysninger.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Kør denne kommando, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Nulstille adgangskoden såvel tasten SSH

1. Oprette en fil med navnet PrivateConf.json med disse indhold. Erstatte de kantede parenteser og & #60; pladsholder & #62; værdier med dine egne oplysninger.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Kør denne kommando, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Oprette en ny brugerkonto til sudo

Hvis du glemmer dit brugernavn, kan du bruge VMAccess til at oprette en ny med sudo nøglecenter. I dette tilfælde vil den eksisterende brugernavn og adgangskode, ikke blive ændret.

For at oprette en ny sudo bruger med adgang adgangskode, skal du bruge scriptet i [nulstille adgangskoden](#pwresetcli) og angive det nye brugernavn.

For at oprette en ny sudo bruger med SSH vigtige adgang, skal du bruge scriptet i [nulstilling tasten SSH](#sshkeyresetcli) og angive det nye brugernavn.

Du kan også bruge [nulstille adgangskoden og tasten SSH](#resetbothcli) til at oprette en ny bruger med både adgangskode og SSH vigtige adgang.

## <a name="sshconfigresetcli"></a>Nulstil SSH konfiguration

Hvis SSH konfigurationen er i en uønsket tilstand, kan du også miste adgangen til VM. Du kan bruge filtypenavnet VMAccess nulstille konfigurationen til standardtilstanden. For at gøre det, skal du blot indstille nøglen "reset_ssh" til "True". Filtypenavnet genstart SSH serveren, åbne SSH porten på din VM og nulstille SSH konfigurationen til standardværdier. Brugerkontoen (navn, din adgangskode eller SSH nøgler) ændres ikke.

> [AZURE.NOTE] Filen SSH konfiguration, som bliver nulstille er placeret i /etc/ssh/sshd_config.

1. Oprette en fil med navnet PrivateConf.json med dette indhold.

        {
        "reset_ssh":"True"
        }

2. Kør denne kommando, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Slette en bruger

Hvis du vil slette en brugerkonto uden at logge ind til VM direkte, kan du bruge dette script.

1. Oprette en fil med navnet PrivateConf.json med indholdet, erstatter brugernavn for at fjerne til & #60; usernametoremove & #62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Kør denne kommando, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Få vist status for filtypenavnet VMAccess

For at få vist status for filtypenavnet VMAccess, du Kør denne kommando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< en name = 'checkdisk' <</a>kontrollere konsistens tilføjede diske

Hvis du vil køre fsck på alle diske i din Linux virtuelle maskine, skal du gøre følgende:

1. Oprette en fil med navnet PublicConf.json med dette indhold. Kontrol af disken tager en boolesk værdi for om du vil kontrollere diske, der er knyttet til din virtuelle maskine eller ej. 

        {   
        "check_disk": "true"
        }

2. Kør denne kommando til at udføre, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Reparere diske 

Bruge filtypenavnet VMAccess til at nulstille Indlæs konfiguration på din Linux virtuelle maskine for at reparere diske, der ikke er ved at tilslutte eller har tilslutningen konfigurationsfejl. Erstat navnet på harddisken til & #60; yourdisk & #62;.

1. Oprette en fil med navnet PublicConf.json med dette indhold. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Kør denne kommando til at udføre, og Erstat navnet på din computer og virtuelle & #60; vm navn & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Næste trin

* Hvis du vil bruge Azure PowerShell-cmdlet'er eller Azure ressourcestyring skabeloner til at nulstille adgangskoden eller SSH nøgle, løse SSH konfigurationen, og Kontrollér disk konsistens, finder du i [VMAccess lokalnummer dokumentation på GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Du kan også bruge [Azure portal](https://portal.azure.com) at nulstille adgangskoden eller SSH tast til en Linux VM implementeret i den klassiske implementeringsmodel. Du kan ikke i øjeblikket bruger portalen gør denne til en Linux VM implementeret i implementeringsmodel ressourcestyring.

* Se [om virtuelt extensions og funktioner](virtual-machines-linux-extensions-features.md) for flere oplysninger om brug af VM udvidelser til Azure virtuelle maskiner.
