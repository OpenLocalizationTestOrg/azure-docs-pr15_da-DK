<properties
    pageTitle="Fejlfinding i forbindelse med Azure virtuelt sikkerhedskopi | Microsoft Azure"
    description="Fejlfinding i forbindelse med sikkerhedskopiering og gendannelse af Azure virtuelle maskiner"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Fejlfinding i forbindelse med Azure virtuelt sikkerhedskopi

> [AZURE.SELECTOR]
- [Gendannelse services samling](backup-azure-vms-troubleshoot.md)
- [Sikkerhedskopiering samling](backup-azure-vms-troubleshoot-classic.md)

Du kan foretage fejlfinding af fejl, mens sikkerhedskopiering Azure med oplysninger er angivet i tabellen nedenfor.

## <a name="discovery"></a>Registrering

| Sikkerhedskopieringen | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Registrering | Der kunne ikke finde nye elementer - Microsoft Azure Backup er stødt på og intern fejl. Vent et par minutter, og prøv derefter igen. | Prøv igen søgningen efter 15 minutter.
| Registrering | Det lykkedes ikke at opdage nye elementer – en anden registrering handling er allerede i gang. Vent, indtil den aktuelle registrering-handling er fuldført. | Ingen |

## <a name="register"></a>Registrer
| Sikkerhedskopieringen | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Registrer | Antallet af datadisce, der er knyttet til den virtuelle maskine overskredet den understøttede grænse – skal du fjerne nogle datadisce på denne virtuelle maskine, og prøv igen. Azure sikkerhedskopi understøtter op til 16 datadisce, der er knyttet til en Azure virtuelt til sikkerhedskopi | Ingen |
| Registrer | Microsoft Azure Backup opstod en intern fejl – Vent et par minutter og derefter prøve igen. Hvis problemet fortsætter, skal du kontakte Microsoft Support. | Du kan få denne fejl på grund af en af følgende ikke-understøttede konfiguration af VM på Premium LRS. <br> Premium lagerplads FOS kan sikkerhedskopieres, ved hjælp af gendannelse services samling. [Lær mere](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Registrer | Registreringen mislykkedes med timeout for operation installere Agent | Kontrollér, om OS-versionen af den virtuelle maskine understøttes. |
| Registrer | Kommando-udførelse mislykkedes - en anden handling er i gang på dette element. Vent, indtil den forrige handling er fuldført | Ingen |
| Registrer | Virtuelle maskiner har virtuelle harddiske, der er gemt på Premium lagerplads der ikke understøttes for sikkerhedskopiering | Ingen |
| Registrer | Virtuelt agent er ikke til stede på den virtuelle maskine – skal du installere den nødvendige foreløbig nødvendige VM agent, og genstart handlingen. | [Læs mere](#vm-agent) om VM agent-installation, og hvordan du validere VM agent-installation. |

## <a name="backup"></a>Sikkerhedskopi

| Sikkerhedskopieringen | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Sikkerhedskopi | Kunne ikke kommunikere med VM agent for øjebliksbillede status. Øjebliksbillede VM underordnede opgave timeout. -Skal du se fejlfinding vejledning om, hvordan du kan løse dette problem. | Denne fejl, hvis der opstår et problem med VM Agent eller netværksadgang til Azure infrastrukturen er blokeret i anden måde. Få mere at vide om [fejlfinding af VM øjebliksbillede problemer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Hvis VM agent ikke forårsager problemer, genstart derefter VM. Nogle gange en forkert VM tilstand kan medføre problemer med, og genstart VM nulstiller denne "ugyldig tilstand" |
| Sikkerhedskopi | Sikkerhedskopi mislykkedes med en intern fejl – Forsøg handlingen i et par minutter. Hvis problemet fortsætter, kan du kontakte Microsoft Support | Kontroller, om der er et midlertidigt problem i at få adgang til VM lagerplads. Kontroller [Azure Status](https://azure.microsoft.com/en-us/status/) for at se, om der er en hvilken som helst videre problem, der er relateret til Beregn/lagerplads/netværk i området. Prøv er forsøg sikkerhedskopiering indlæg problemet afhjulpet. |
| Sikkerhedskopi | Kunne ikke udføre handlingen, som ikke længere findes VM. | Kan ikke foretages sikkerhedskopiering, som er blevet slettet VM konfigureret til sikkerhedskopi. Stoppe yderligere sikkerhedskopier ved at gå til beskyttet visning af elementer, Markér beskyttet element, og klik på Stop beskyttelse. Du kan bevare data ved at vælge bevare sikkerhedskopi data indstilling. Du kan senere genoptage beskyttelse til denne virtuelle computer ved at klikke på Konfigurer på beskyttelse mod registreret elementer visning|
| Sikkerhedskopi | Kunne ikke installere Azure gendannelse Services er lokalnummer på det valgte element - VM Agent en foreløbig nødvendige til Azure gendannelse Services filtypenavn. Installer Azure VM agent, og genstart handlingen registrering | <ol> <li>Kontrollér, om VM agent er blevet installeret korrekt. <li>Sørg for, at flaget på VM config er indstillet korrekt.</ol> [Læs mere](#validating-vm-agent-installation) om VM agent-installation, og hvordan du validere VM agent-installation. |
| Sikkerhedskopi | Kommando-udførelse mislykkedes - en anden handling er i øjeblikket i gang på dette element. Vent, indtil den forrige handling er fuldført, og prøv derefter igen | En eksisterende sikkerhedskopi eller gendanne job af VM kører og et nyt job kan ikke startes, mens den eksisterende sag kører. |
| Sikkerhedskopi | Filtypenavn installationen mislykkedes med fejlen "COM + kunne ikke tale med Microsoft Distributed Transaction koordinater | Det betyder som regel, at COM +-tjenesten ikke kører. Kontakt Microsoft support for hjælp til at løse dette problem. |
| Sikkerhedskopi | Øjebliksbillede mislykkedes med fejlen VSS handling "dette drev er låst af BitLocker drev kryptering. Du skal fjerne beskyttelsen dette drev fra Kontrolpanel. | Slå BitLocker for alle drev på VM og se, om VSS problemet er løst |
| Sikkerhedskopi | Virtuelle maskiner har virtuelle harddiske, der er gemt på Premium lagerplads der ikke understøttes for sikkerhedskopiering | Ingen |
| Sikkerhedskopi | Azure virtuelt blev ikke fundet. | Dette sker, når den primære VM slettes, men politikken sikkerhedskopiering fortsætter med at søge efter en VM til at udføre en sikkerhedskopiering af. At rette fejlen: <ol><li>Genopret den virtuelle maskine med det samme navn og den samme ressource gruppenavn [skyen service navn], <br>(ELLER) <li> Deaktivere beskyttelse for denne VM, så efterfølgende sikkerhedskopier ikke kan udløses. </ol> |
| Sikkerhedskopi | Virtuelt agent er ikke til stede på den virtuelle maskine – skal du installere den nødvendige foreløbig nødvendige VM agent, og genstart handlingen. | [Læs mere](#vm-agent) om VM agent-installation, og hvordan du validere VM agent-installation. |

## <a name="jobs"></a>Job
| Handling | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Annullere job | Annullering understøttes ikke af denne jobtype - Vent, indtil jobbet er fuldført. | Ingen |
| Annullere job | Jobbet er ikke i tilstanden fjernprocedurekald - Vent, indtil jobbet er fuldført. <br>ELLER<br> Det valgte job er ikke i tilstanden fjernprocedurekald - Vent, til at fuldføre opgaven.| I alle sandsynligheden for udfyldes næsten jobbet. Vent, indtil jobbet er fuldført |
| Annullere job | Jobbet kan ikke annullere, fordi det ikke er i gang – annullering understøttes kun for sager, som er i gang. Annuller forsøg på en igangværende job. | Dette sker på grund af en forbigående tilstand. Vent et minut, og prøv igen handlingen Annuller |
| Annullere job | Det lykkedes ikke at annullere jobbet - skal du vente, indtil job er fuldført. | Ingen |


## <a name="restore"></a>Gendanne
| Handling | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Gendanne | Gendan mislykkedes med skyen interne fejl | <ol><li>Skybaseret tjeneste, du forsøger at gendanne er konfigureret med DNS-indstillingerne. Du kan kontrollere <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-Slot "Fremstilling" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Hvis der er konfigureret adresse, betyder det, at DNS-indstillingerne er konfigureret.<br> <li>Skybaseret tjeneste, som du vil du forsøger at gendanne er konfigureret med reserveret IP og eksisterende FOS i skybaseret tjeneste er stoppet tilstand.<br>Du kan kontrollere en skybaseret tjeneste har reserveret IP-adresse ved hjælp af følgende powershell-cmdletter:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-Slot "Fremstilling" $ Forhindring af datakørsel. ReservedIPName <br><li>Du forsøger at gendanne en virtuel maskine med følgende speciel netværkskonfigurationer i til samme skybaseret tjeneste. <br>-Virtuelle maskiner under indlæsning belastningsjusteringstjenesten konfiguration (interne og eksterne)<br>-Virtuelle maskiner med flere reserveret IP'er<br>-Virtuelle maskiner med flere netværkskort<br>Vælg en ny tjeneste i skyen i Brugergrænsefladen eller skal du se gendanne [overvejelser i forbindelse med](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) for FOS med særlige netværkskonfigurationer</ol> |
| Gendanne | Det valgte DNS-navn er allerede i brug – skal du angive et andet DNS-navn, og prøv igen. | Navnet på DNS-her refererer til navnet på tjenesten skyen (som regel slutter med. cloudapp.net). Dette skal være entydig. Hvis du støder på denne fejl, skal du vælge et andet VM navn under gendannelsen. <br><br> Denne fejl vises kun for brugere af portalen Azure. Gendannelsen via PowerShell lykkes, fordi den kun gendanner diskene og ikke oprette VM. Fejlen kan være gælder, når VM oprettes eksplicit ved du, når disken gendannelse. |
| Gendanne | Den angivne virtuelle netværkskonfiguration ikke er korrekt – skal du angive en anden virtuelle netværkskonfiguration og prøve igen. | Ingen |
| Gendanne | Den angivne skytjeneste bruger en reserveret IP-adresse, som ikke stemmer overens med konfigurationen af den virtuelle maskine, ved at blive gendannet – skal du angive en anden skybaseret tjeneste, som ikke anvender reserveret IP-adresse eller vælge en anden gendannelse tidspunkt at gendanne fra. | Ingen |
| Gendanne | Skybaseret tjeneste har nået grænsen for antal input slutpunkter - prøv igen ved at angive en anden skybaseret tjeneste eller ved hjælp af et eksisterende slutpunkt. | Ingen |
| Gendanne | Sikkerhedskopiering samling- og destinationswebsteder lagerplads konto er i to forskellige områder - sikre, at kontoen lagerplads, der er angivet i gendannelsen er i samme Azure område som den ekstra samling af legitimationsoplysninger. | Ingen |
| Gendanne | Lagerplads-konto, der er angivet for gendannelsen ikke er understøttes – kun Basic/Standard lagerplads konti med lokalt overflødige eller geografisk overflødige Replikeringsindstillinger understøttes. Vælg en understøttede lagerplads konto | Ingen |
| Gendanne | Lagerplads kontotype, der er angivet for gendannelsen er ikke online - Sørg for, at kontoen lagerplads, der er angivet i gendannelsen er online | Dette kan ske på grund af en midlertidig fejl eller på grund af en afbrydelse i Azure-lager. Vælg en anden lagerplads konto. |
| Gendanne | Ressourcegruppe kvote er nået – Slet nogle grupper fra Azure-portalen, eller kontakte Azure support for at øge begrænsningerne. | Ingen |
| Gendanne | Markerede undernet findes ikke - Vælg et undernet, som findes | Ingen |


## <a name="policy"></a>Politik
| Handling | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Oprette politik | Kunne ikke oprettes politik – skal du reducere opbevaring mulighederne for at fortsætte med konfiguration af politik. | Ingen |


## <a name="vm-agent"></a>VM Agent

### <a name="setting-up-the-vm-agent"></a>Konfiguration af VM Agent
Typisk findes VM Agent allerede i FOS, der er oprettet fra galleriet Azure. Virtuelle maskiner, der overføres fra lokale datacentre vil ikke have VM Agent, der er installeret. For disse FOS skal VM Agent være installeret eksplicit. Få mere at vide om at [installere VM agent på en eksisterende VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

For Windows FOS:

- Hent og Installer [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du skal have administratorrettigheder for at fuldføre installationen.
- [Opdatere egenskaben VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) til at angive, at agenten er installeret.

For Linux FOS:

- Du kan installere nyeste version af [Linux agent](https://github.com/Azure/WALinuxAgent) fra github.
- [Opdatere egenskaben VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) til at angive, at agenten er installeret.


### <a name="updating-the-vm-agent"></a>Opdatere VM Agent
For Windows FOS:

- Opdatere VM Agent er så enkelt som geninstallere [VM Agent binære filer](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Skal du sikre dig, at ingen sikkerhedskopieringen kører, mens VM Agent opdateres.

For Linux FOS:

- Følg vejledningen på [Opdatering af Linux VM Agent](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validere VM Agent-installation
Sådan kontrolleres versionen VM Agent på Windows FOS:

1. Log på den Azure virtuelle maskine, og gå til mappen *C:\WindowsAzure\Packages*. Du skal finde filen WaAppAgent.exe Præsenter.
2. Højreklik på filen, gå til **Egenskaber**, og vælg derefter fanen **Detaljer** . Feltet produktversion skal være 2.6.1198.718 eller nyere





