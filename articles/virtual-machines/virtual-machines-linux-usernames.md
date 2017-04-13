<properties 
    pageTitle="Valg af brugernavne for Linux | Microsoft Azure" 
    description="Lær, hvordan du kan markere brugernavne for en Linux virtuel maskine i Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



#<a name="selecting-user-names-for-linux-on-azure"></a>Valg af brugernavne for Linux på Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Når du klargør en Linux virtuel maskine på Azure skal du angive navnet på en ikke-rod-bruger, som du senere kan bruge til at logge ind på VM. Du kan vælge navnet på den nye bruger, eller hvis klargøring via portalen Azure klassisk kan du acceptere standard "azureuser".

I de fleste tilfælde denne bruger findes ikke på den grundlæggende afbildning og oprettes under processen klargøring. Hvis brugeren findes i den grundlæggende VM afbildning, derefter konfigurerer Azure Linux agent blot adgangskode og/eller SSH nøgle for den pågældende bruger, der er baseret på de oplysninger, du har angivet, når du opretter VM.

**Men**Linux definerer et sæt af brugernavne, der ikke bør benyttes. Klargøring processen bliver **mislykkes** , hvis du forsøger at klargøre en Linux VM ved hjælp af en eksisterende systembruger, der er defineret som en bruger med UID 0-99. Et typisk eksempel er den `root` bruger, som har UID 0.

 - Se også: [Base Linux Standard - bruger-ID intervaller](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Følgende er en liste over almindelige indbyggede systembrugere til CentOS og Ubuntu, bør du undgå at bruge, når en Linux virtuel maskine på Azure. Denne liste er bare et eksempel, skal du se dokumentationen til din distribution til at sikre, at det brugernavn, du vælger ikke er i konflikt med en eksisterende bruger.


## <a name="centos"></a>CentOS
- abrt
- adm
- lyd
- placering
- CDROM
- cgred
- Daemon
- dbus
- dialout
- DIP
- disk
- diskettedrev
- FTP
- FTP
- spil
- Gopher
- haldaemon
- standse
- kmem
- Lås
- LP
- mail
- Mand
- mem
- nfsnobody
- ingen
- NTP
- operatoren
- oprofile
- postdrop
- omvendt polsk
- qpidd
- rod
- RPC
- rpcuser
- saslauth
- lukning
- slocate
- sshd
- stapdev
- stapusr
- Synkroniser
- for
- bånd
- Test
- tcpdump
- TTY
- brugere
- utempter
- utmp
- uucp
- vcsa
- video
- farvehjulet med


## <a name="ubuntu"></a>Ubuntu
- adm
- administrator
- lyd
- sikkerhedskopi
- placering
- CDROM
- crontab
- Daemon
- dialout
- DIP
- disk
- faxforside
- diskettedrev
- sikring
- spil
- gnats
- IRC
- kmem
- liggende
- libuuid
- liste
- LP
- mail
- Mand
- messagebus
- mlocate
- netdev
- Nyheder
- ingen
- nogroup
- operatoren
- plugdev
- proxy
- rod
- SASL
- skygge
- src
- SSH
- sshd
- personale
- sudo
- Synkroniser
- for
- Syslog
- bånd
- TTY
- brugere
- utmp
- uucp
- video
- tale
- whoopsie
- www-data

 