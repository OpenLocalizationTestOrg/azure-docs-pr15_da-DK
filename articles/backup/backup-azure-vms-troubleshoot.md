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

## <a name="backup"></a>Sikkerhedskopi

| Sikkerhedskopieringen | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
|Sikkerhedskopi|    Kunne ikke udføre handlingen, som ikke længere findes VM. -Stop med at beskytte virtuelt uden at slette sikkerhedskopidataene. Få mere at vide på http://go.microsoft.com/fwlink/?LinkId=808124   |Dette sker, når den primære VM slettes, men politikken sikkerhedskopiering fortsætter med at søge efter en VM til at udføre en sikkerhedskopiering af. At rette fejlen: <ol><li> Genopret den virtuelle maskine med det samme navn og den samme ressource gruppenavn [skyen service navn],<br>(ELLER)</li><li> Stop med at beskytte virtuelt med eller uden at slette sikkerhedskopidataene. [Få mere at vide](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sikkerhedskopi|Kunne ikke kommunikere med VM agent for øjebliksbillede status. -Sørg for, at VM har adgang til internettet. Også opdatere VM agent, som nævnt i guiden fejlfinding på http://go.microsoft.com/fwlink/?LinkId=800034 |Denne fejl, hvis der opstår et problem med VM Agent eller netværksadgang til Azure infrastrukturen er blokeret i anden måde. Få mere at vide mere om fejlfinding af VM snapshot problemer.<br> Hvis VM agent ikke forårsager problemer, genstart derefter VM. Nogle gange en forkert VM tilstand kan medføre problemer med, og genstart VM nulstiller denne "ugyldig tilstand"|
|Sikkerhedskopi|    Gendannelse services lokalnummer mislykkedes. -Kontroller, at seneste virtuelt agent findes på den virtuelle maskine og agenttjenesten kører. Prøv igen sikkerhedskopieringen, og hvis det mislykkes, skal du kontakte Microsoft support.|   Denne fejl er opstået, når VM agent er forældet. Se "Ved at opdatere feltet VM Agent" nedenfor til at opdatere VM agent.|
|Sikkerhedskopi |Virtuelt findes ikke. -Prøv Kontroller, at virtuelt findes, eller Vælg en anden virtuel maskine. | Dette sker, når den primære VM slettes, men politikken sikkerhedskopiering fortsætter med at søge efter en VM til at udføre en sikkerhedskopiering af. At rette fejlen: <ol><li> Genopret den virtuelle maskine med det samme navn og den samme ressource gruppenavn [skyen service navn],<br>(ELLER)<br></li><li>Stop med at beskytte virtuelt uden at slette sikkerhedskopidataene. [Få mere at vide](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sikkerhedskopi |Kommandoen udførelse af mislykkedes. -En anden handling er i gang på dette element. Vent, indtil den forrige handling er fuldført, og prøv derefter igen |En eksisterende sikkerhedskopi eller gendanne job af VM kører og et nyt job kan ikke startes, mens den eksisterende sag kører.|
| Sikkerhedskopi | Kopiere virtuelle harddiske fra sikkerhedskopien samling fik timeout - Forsøg handlingen i et par minutter. Hvis problemet fortsætter, skal du kontakte Microsoft Support. | Dette sker, når der er for mange data der skal kopieres. Skal du kontrollere, om du har mindre end 16 datadisce. |
| Sikkerhedskopi | Sikkerhedskopi mislykkedes med en intern fejl – Forsøg handlingen i et par minutter. Hvis problemet fortsætter, kan du kontakte Microsoft Support | Du kan få denne fejl 2 årsager: <ol><li> Der er et midlertidigt problem i at få adgang til VM lagerplads. Kontroller [Azure Status](https://azure.microsoft.com/en-us/status/) for at se, om der er en hvilken som helst videre problem, der er relateret til Beregn/lagerplads/netværk i området. Prøv er forsøg sikkerhedskopiering indlæg problemet afhjulpet. <li>Den oprindelige VM er blevet slettet, og derfor sikkerhedskopi kan ikke gøres tilgængelig. Fjerne beskyttelsen VM for at bevare de sikkerhedskopierede data til en slettede VM, men forhindrer sikkerhedskopiering fejl, og vælg indstillingen for at bevare dataene. Dette standser tidsplanen for sikkerhedskopiering og også tilbagevendende fejlmeddelelser. |
| Sikkerhedskopi | Kunne ikke installere Azure gendannelse Services er lokalnummer på det valgte element - VM Agent en foreløbig nødvendige til Azure gendannelse Services filtypenavn. Installer Azure VM agent, og genstart handlingen registrering | <ol> <li>Kontrollér, om VM agent er blevet installeret korrekt. <li>Sørg for, at flaget på VM config er indstillet korrekt.</ol> [Læs mere](#validating-vm-agent-installation) om VM agent-installation, og hvordan du validere VM agent-installation. |
| Sikkerhedskopi | Filtypenavn installationen mislykkedes med fejlen "COM + kunne ikke tale med Microsoft Distributed Transaction koordinater | Det betyder som regel, at COM +-tjenesten ikke kører. Kontakt Microsoft support for hjælp til at løse dette problem. |
| Sikkerhedskopi | Øjebliksbillede mislykkedes med fejlen VSS handling "dette drev er låst af BitLocker drev kryptering. Du skal fjerne beskyttelsen dette drev fra Kontrolpanel. | Slå BitLocker for alle drev på VM og se, om VSS problemet er løst |
| Sikkerhedskopi | Virtuelle maskiner har virtuelle harddiske, der er gemt på Premium lagerplads der ikke understøttes for sikkerhedskopiering | Ingen |
| Sikkerhedskopi | Azure virtuelt blev ikke fundet. | Dette sker, når den primære VM slettes, men politikken sikkerhedskopiering fortsætter med at søge efter en VM til at udføre en sikkerhedskopiering af. At rette fejlen: <ol><li>Genopret den virtuelle maskine med det samme navn og den samme ressource gruppenavn [skyen service navn], <br>(ELLER) <li> Deaktivere beskyttelse for denne VM, så sikkerhedskopieringsjob ikke vil blive oprettet </ol> |
| Sikkerhedskopi | Virtuelt agent er ikke til stede på den virtuelle maskine – skal du installere den nødvendige foreløbig nødvendige VM agent, og genstart handlingen. | [Læs mere](#vm-agent) om VM agent-installation, og hvordan du validere VM agent-installation. |

## <a name="jobs"></a>Job

| Handling | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Annullere job | Annullering understøttes ikke af denne jobtype - Vent, indtil jobbet er fuldført. | Ingen |
| Annullere job | Jobbet er ikke i tilstanden fjernprocedurekald - Vent, indtil jobbet er fuldført. <br>ELLER<br> Det valgte job er ikke i tilstanden fjernprocedurekald - Vent, til at fuldføre opgaven.| I alle sandsynligheden for udfyldes næsten jobbet. Vent, indtil jobbet er fuldført |
| Annullere job | Jobbet kan ikke annullere, fordi det ikke er i gang – annullering understøttes kun for sager, som er i gang. Annuller forsøg på en igangværende job. | Dette sker på grund af en forbigående tilstand. Vent et minut, og prøv igen handlingen Annuller |
| Annullere job | Det lykkedes ikke at annullere jobbet - Vent, indtil job er fuldført. | Ingen |


## <a name="restore"></a>Gendanne
| Handling | Flere oplysninger om fejlen | Løsning |
| -------- | -------- | -------|
| Gendanne | Gendan mislykkedes med skyen interne fejl | <ol><li>Skybaseret tjeneste, du forsøger at gendanne er konfigureret med DNS-indstillingerne. Du kan kontrollere <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-Slot "Fremstilling" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Hvis der er konfigureret adresse, betyder det, at DNS-indstillingerne er konfigureret.<br> <li>Skybaseret tjeneste, som du vil du forsøger at gendanne er konfigureret med reserveret IP og eksisterende FOS i skybaseret tjeneste er stoppet tilstand.<br>Du kan kontrollere en skybaseret tjeneste har reserveret IP-adresse ved hjælp af følgende powershell-cmdletter:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-Slot "Fremstilling" $ Forhindring af datakørsel. ReservedIPName <br><li>Du forsøger at gendanne en virtuel maskine med følgende speciel netværkskonfigurationer i til samme skybaseret tjeneste. <br>-Virtuelle maskiner under indlæsning belastningsjusteringstjenesten konfiguration (interne og eksterne)<br>-Virtuelle maskiner med flere reserveret IP'er<br>-Virtuelle maskiner med flere netværkskort<br>Vælg en ny tjeneste i skyen i Brugergrænsefladen eller skal du se gendanne [overvejelser i forbindelse med](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) for FOS med særlige netværkskonfigurationer</ol> |
| Gendanne | Det valgte DNS-navn er allerede i brug – skal du angive et andet DNS-navn, og prøv igen. | Navnet på DNS-her refererer til navnet på tjenesten skyen (som regel slutter med. cloudapp.net). Dette skal være entydig. Hvis du støder på denne fejl, skal du vælge et andet VM navn under gendannelsen. <br><br> Bemærk, at denne fejl vises kun for brugere af portalen Azure. Gendannelsen via PowerShell lykkes, fordi den kun gendanner diskene og ikke oprette VM. Fejlen kan være gælder, når VM oprettes eksplicit ved du, når disken gendannelse. |
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
| Oprette politik | Kunne ikke oprettes politikken - skal du reducere opbevaring mulighederne for at fortsætte med konfiguration af politik. | Ingen |


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

## <a name="troubleshoot-vm-snapshot-issues"></a>Fejlfinding i VM øjebliksbillede
VM sikkerhedskopi er afhængig af udstedelse øjebliksbillede kommandoen til underliggende lagerplads. Ikke at have adgang til lagerplads eller forsinkelse i udførelse af øjebliksbillede opgave kan ikke sikkerhedskopien. Følgende kan medføre øjebliksbillede opgave mislykkedes.

1. Netværksadgang til lager er blokeret ved hjælp af NSG<br>
   Lær mere om, hvordan du [aktiverer netværksadgang](backup-azure-vms-prepare.md#2-network-connectivity) til lager ved hjælp af enten WhiteListing IP'er eller via proxy-server.
2.  FOS med Sql Server-sikkerhedskopiering konfigureret kan medføre øjebliksbillede opgave forsinkelse <br>
    Lav sikkerhedskopi problemer VSS fuld sikkerhedskopiering på Windows FOS som standard VM. Dette kan medføre forsinkelser i øjebliksbillede udførelse af på VM'er, der kører Sql Server og Sql Server er konfigureret til sikkerhedskopi. Angiv følgende registreringsdatabasenøgle, hvis du oplever sikkerhedskopiering mislykkede forsøg på grund af problemer med øjebliksbillede.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  VM status rapporteres forkert, fordi VM er lukket i RDP.  <br>
    Hvis du har lukker den virtuelle maskine i RDP, skal du se tilbage i portalen, VM status vises korrekt. Hvis ikke, Luk VM ved hjælp af 'Lukning' indstillingen i VM dashboard-portalen.
4.  Hvis mere end fire VM del i den samme skyen tjeneste, kan du konfigurere flere sikkerhedskopiering politikker for at fase sikkerhedskopiering klokkeslæt så ingen mere end fire VM sikkerhedskopier er startet på samme tid. Prøv at sprede sikkerhedskopiering start tidspunkter timen fra hinanden mellem politikker. 
5.  VM kører på høj CPU/hukommelse.<br>
    Hvis den virtuelle maskine kører på høj CPU usage(>90%) eller hukommelse, øjebliksbillede opgaven er i kø, forsinket og til sidst der får timeout. Prøv efter behov sikkerhedskopiere i disse situationer.

<br>

## <a name="networking"></a>Netværk
Skal have adgang til offentlige internettet til at fungere som alle udvidelser sikkerhedskopi filtypenavn. Ikke at have adgang til det offentlige internet kan forekomme i en række forskellige måder:

- Filtypenavn installationen kan mislykkes
- Handlingerne sikkerhedskopiering (som disk øjebliksbillede) kan mislykkes
- Visning af status for sikkerhedskopieringen kan mislykkes

Brug af til løsning af offentlige internetadresser blevet leddelte [her](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Du skal kontrollere DNS-konfigurationer for VNET og sikre, at de Azure URI'er kan oversættes.

Når navneoversættelse udføres korrekt, skal adgang til Azure IP'er også angives. Hvis du vil fjerne blokeringen af adgang til Azure infrastrukturen, skal du følge en af følgende fremgangsmåder:

1. WhiteList Azure datacenter IP-intervaller.
    - Hente en liste over [Azure datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) skal være whitelisted.
    - Fjerne blokeringen af de IP-adresser ved hjælp af [Ny NetRoute](https://technet.microsoft.com/library/hh826148.aspx) cmdlet. Køre denne cmdlet i VM Azure i en øgede PowerShell-vinduet (Kør som Administrator).
    - Føje regler til NSG (Hvis du har en sted) for at give adgang til IP-adresser.
2. Oprette en sti til HTTP-trafik til mailflow
    - Hvis du har installere en HTTP-proxy-server til at omdirigere trafikken nogle netværk begrænsning på sted (en netværk sikkerhedsgruppe, for eksempel). Trin til at implementere en HTTP-Proxy-server kan findes [her](backup-azure-vms-prepare.md#2-network-connectivity).
    - Føje regler til NSG (Hvis du har en sted) for at give adgang til internettet fra HTTP-Proxy.

>[AZURE.NOTE] DHCP skal være aktiveret i gæst til IaaS VM sikkerhedskopiering til at arbejde.  Hvis du har brug for en statiske privat IP-Adresser, skal du konfigurere den hjælp af platformen til. Indstillingen DHCP i VM skal aktiveres venstre.
Få vist flere oplysninger om at [angive en statisk interne Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).
