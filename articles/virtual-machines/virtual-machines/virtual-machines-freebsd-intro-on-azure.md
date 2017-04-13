<properties
   pageTitle="Introduktion til FreeBSD på Azure | Microsoft Azure"
   description="Få mere at vide om at bruge FreeBSD virtuelle maskiner på Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Introduktion til FreeBSD på Azure
Dette emne indeholder en oversigt over kører en FreeBSD virtuel maskine i Azure.

## <a name="overview"></a>Oversigt
FreeBSD til Microsoft Azure er en avanceret computerens operativsystem bruges til at power moderne servere, stationære computere, og integreret platforme. FreeBSD 10.3 billedet leveres af Microsoft Corporation og er tilgængelige i Azure. Det er baseret på den FreeBSD 10.3 version og Azure VM gæst Agent [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) er installeret. Agenten er ansvarlig for kommunikation mellem FreeBSD VM og Azure-strukturen for handlinger, som klargøring VM første gang det bruges (brugernavn, din adgangskode, værtsnavn osv.) og aktivere funktionalitet til selektiv VM filtypenavne.
Som for fremtidige versioner af FreeBSD er strategien for at holde dig orienteret og gøre de nyeste versioner tilgængelige, kort, når de er udgivet af FreeBSD release engineering-teamet. Den kommende version er [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementere en FreeBSD virtuel maskine
Implementere en FreeBSD virtuel maskine er nemt at bruge et billede fra [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>VM filtypenavne for FreeBSD
Følgende er understøttede VM udvidelser i FreeBSD.

### <a name="vmaccess"></a>VMAccess

Filtypenavnet [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) kan:

- Nulstille adgangskoden til den oprindelige sudo bruger.
- Opret en ny sudo bruger med den angivne adgangskode.
- Angiv den offentlige host nøgle med tasten givet.
- Nulstille den offentlige host nøgle, der er angivet under VM klargøringen Hvis tasten host ikke er angivet.
- Åbn SSH porten (22) og gendanne sshd_config, hvis reset_ssh er indstillet til sand.
- Fjern den eksisterende bruger.
- Kontrollere diske.
- Reparere en tilføjede disk.

### <a name="customscript"></a>CustomScript

Filtypenavnet [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) kan:

- Hvis, kan du hente de brugerdefinerede scripts fra Azure-lager eller ekstern offentlige storage (for eksempel GitHub).
- Kør scriptet posten punkt.
- Understøtter indbygget kommandoer.
- Konvertere Windows-typografi ny linje i udformning og Python scripts automatisk.
- Fjern Stykliste i udformning og Python scripts automatisk.
- Beskytte følsomme oplysninger i CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Godkendelse: brugernavne, adgangskoder og SSH taster
Når du opretter en FreeBSD virtuel maskine ved hjælp af portalen Azure, skal du angive et brugernavn, din adgangskode eller SSH offentlig nøgle.
Brugernavne til implementering af en FreeBSD virtuel maskine på Azure skal ikke svarer til navnene på Systemkonti (UID < 100) allerede findes i den virtuelle maskine ("rod" for eksempel).
På nuværende tidspunkt understøttes kun RSA SSH tasten. En flere linjer SSH nøgle skal begynde med "---STARTPIL SSH2 offentlig nøgle –" og slutter med "– slutningen SSH2 offentlig nøgle –".

## <a name="obtaining-superuser-privileges"></a>Bestil superbruger rettigheder
Den brugerkonto, der er angivet under virtuelt forekomst installation på Azure er en privilegerede konto. Pakke med sudo blev installeret på publicerede FreeBSD billede.
Når du er logget på via denne brugerkonto, kan du køre kommandoer som rod ved hjælp af kommandosyntaks.

    # sudo <COMMAND>

Du kan eventuelt få en rod shell ved hjælp af sudo -s.

## <a name="next-steps"></a>Næste trin
- Gå til [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) for at oprette en FreeBSD VM.
- Hvis du vil flytte dine egne FreeBSD til Azure, skal du se i [Opret og Overfør en FreeBSD virtuel harddisk til Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
