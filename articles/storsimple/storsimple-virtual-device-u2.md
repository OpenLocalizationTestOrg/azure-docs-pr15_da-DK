<properties 
   pageTitle="StorSimple virtuel enhed opdatering 2 | Microsoft Azure"
   description="Lær at oprette, installere og administrere en StorSimple virtuel enhed i et Microsoft Azure virtuelt netværk. (Gælder StorSimple opdatering 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Installere og administrere en StorSimple virtuel enhed i Azure


##<a name="overview"></a>Oversigt
Den virtuelle enhed StorSimple 8000 serie er en ekstra funktion, der følger med din Microsoft Azure StorSimple løsning. Den virtuelle StorSimple enhed kører på en virtuel maskine i et virtuelt Microsoft Azure-netværk, og du kan bruge den til at sikkerhedskopiere og klone data fra din værter. Dette selvstudium beskrives, hvordan du installere og administrere en virtuel enhed i Azure og er gældende for alle de virtuelle enheder, der kører softwareversion opdatering 2 og nederste.


#### <a name="virtual-device-model-comparison"></a>Sammenligning af datamodeller virtuel enhed

Den virtuelle enhed StorSimple er tilgængelig i to modeller, en standard 8010 (tidligere kendt som 1100) og en premium 8020 (introduceret i opdatering 2). En sammenligning af de to modeller tabelform nedenfor.


| Enhedsmodel          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Maksimale kapacitet**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Azure VM**              | Standard_A3 (4 kerner, 7 GB hukommelse)                                                                      | Standard_DS3 (4 kerner, 14 GB hukommelse)                                                                                                                          |
| **Versionskompatibilitet** | Versioner, der kører allerede opdatere 2 eller nyere                                             | Køre opdatering, 2 eller nyere versioner                                                                                                  |
| **Område tilgængelighed**   | Alle Azure områder                                                         | Azure områder, der understøtter Premium lagerplads<br></br>Se en liste over områder, [understøttes områder for 8020](#supported-regions-for-8020) |
| **Lagerplads type**          | Anvender Azure Standard lager til lokale drev<br></br> Lær, hvordan du [opretter en Standard-lager-konto]() | Anvender Azure Premium lager til lokale drev<sup>2</sup> <br></br>Lær, hvordan du [opretter en Premium lagerplads konto](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Arbejdsbelastningen vejledning**     | Element niveau hentning af filer fra sikkerhedskopier                                              | Udviklingscenter i skyen og teste scenarier, lav ventetid, højere ydeevne arbejdsmængder <br></br>Sekundær enhed til nedbrud                                                                                            |
 
<sup>1</sup> *tidligere kendt som 1100*.

<sup>2</sup> *både 8010 og 8020 bruge Azure Standard lager til skyen niveau. Forskellen findes kun i det lokale niveau i enheden*.

#### <a name="supported-regions-for-8020"></a>Understøttede områder for 8020

Premium lagerplads områder, der understøttes i øjeblikket for 8020 tabelform nedenfor. Denne liste opdateres løbende, som Premium lagerplads bliver tilgængelig i flere områder. 

| S. Nej.                                                  | Understøttes i øjeblikket i områder |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | Centrale USA                     |
| 2                                                       |  Indtastning af østasiatiske USA                       |
| 3                                                       |  Indtastning af østasiatiske USA 2                     |
| 4                                                       | Vest USA                        |
| 5                                                       | Nord Europe                   |
| 6                                                       | Vest Europe                    |
| 7                                                       | Sydøstasien                 |
| 8                                                       | Japan øst                     |
| 9                                                       | Japan vest                     |
| 10                                                      | Australien øst                 |
| 11                                                      | Australien Sydøst *           |
| 12                                                      | Østasien *                     |
| 13                                                      | Syd centrale USA *              |

* Premium lagerplads blev åbnet for nylig i disse geos.

I denne artikel beskrives trinvise processen med at udrulle en StorSimple virtuel enhed i Azure. Når du har læst i denne artikel, skal du:

- Forstå, hvordan den virtuelle enhed er forskellig fra den fysiske enhed.

- Ikke kunne oprette og konfigurere den virtuelle enhed.

- Oprette forbindelse til den virtuelle enhed.

- Få mere at vide om at arbejde med den virtuelle enhed.

Dette selvstudium gælder for alle de StorSimple virtuelle enheder, der kører opdatering 2 eller nyere. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Hvordan den virtuelle enhed er forskellig fra den fysiske enhed

Den virtuelle enhed StorSimple er en kun software-version af StorSimple, der kører på en enkelt node i en Microsoft Azure virtuel maskine. Den virtuelle enhed understøtter genoprettelse efter nedbrud scenarier, hvor enheden fysisk er ikke tilgængelig, og er velegnet til brug i på elementniveau hentning fra sikkerhedskopier, nedbrud i det lokale miljø og skyen Udviklingscenter og test scenarier.

#### <a name="differences-from-the-physical-device"></a>Forskelle i forhold til den fysiske enhed

I følgende tabel vises nogle vigtige forskelle mellem StorSimple virtuelle enheden og den fysiske StorSimple-enhed.

|                             | Fysisk enhed                                          | Virtuel enhed                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Placering**                    | Er placeret i datacenteret.                               | Kører i Azure.                                                                            |
| **Netværksgrænseflader**          | Har seks netværksgrænseflader: DATA 0 til DATA 5.                  | Har kun et netværkskort: DATA 0.                                        |
| **Registrering**                | Registreret under trinnet konfiguration.                | Registrering er en separat opgave.                                                          |
| **Tjenesten data krypteringsnøglen** | Genoprette på den fysiske enhed og derefter opdatere den virtuelle enhed med den nye nøgle.           | Kan ikke genoprette fra den virtuelle enhed. |


## <a name="prerequisites-for-the-virtual-device"></a>Forudsætninger for den virtuelle enhed

I følgende afsnit beskrives konfiguration forudsætningerne for enheden StorSimple virtuelle. Før du anvender en virtuel enhed, du gennemgå [sikkerhedsovervejelser i forbindelse med en virtuel enhed](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Azure krav

Før du klargør den virtuelle enhed, skal du foretage følgende forberedelser i dit miljø, Azure:

- Til den virtuelle enhed [konfigurere et virtuelt netværk på Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Hvis bruger Premium lagerplads, skal du oprette et virtuelt netværk i en Azure område, der understøtter Premium lagerplads. Yderligere oplysninger om [områder, der understøttes i øjeblikket for 8020](#supported-regions-for-8020).
- Det anbefales at bruge den standard DNS-server, der leveres af Azure i stedet for at angive dine egne DNS-servernavn. Hvis navnet på din DNS-server ikke er gyldige, eller hvis DNS-serveren ikke er kunne løse IP-adresser korrekt, mislykkes oprettelsen af den virtuelle enhed.
- Punkt-til-websted og websted til websted er valgfrit, men ikke påkrævet. Hvis du vil, kan du konfigurere disse indstillinger for mere avancerede scenarier. 
- Du kan oprette [virtuelle Azure-computere](../virtual-machines/virtual-machines-linux-about.md) (værtsservere) i det virtuelle netværk, som kan bruge de enheder, der vises af den virtuelle enhed. Disse servere skal opfylde følgende krav:                            
    - Være Windows eller Linux FOS med iSCSI afsender, der er installeret.
    - Køre i det samme virtuelle netværk som den virtuelle enhed.
    - Ikke kunne oprette forbindelse til iSCSI-destinationen for den virtuelle enhed gennem den interne IP-adressen på den virtuelle enhed.

- Sørg for, at du har konfigureret understøttelse af iSCSI og skyen trafik på det samme virtuelle netværk.


#### <a name="storsimple-requirements"></a>StorSimple krav

Foretag følgende opdateringer til din Azure StorSimple tjeneste, før du opretter en virtuel enhed:


- Tilføj [access control-poster](storsimple-manage-acrs.md) for de VM'er, der vil være værtsservere til den virtuelle enhed.

- Bruge en [lagerplads konto](storsimple-manage-storage-accounts.md#add-a-storage-account) i det samme område som den virtuelle enhed. Lagerplads konti i forskellige områder kan medføre dårlig ydeevne. Du kan bruge en Standard- eller Premium lagerplads konto med den virtuelle enhed. Du kan finde flere oplysninger om, hvordan du opretter en [Standard-lagerplads konto]() eller en [Premium lagerplads konto](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Bruge en anden lagerplads konto for oprettelse af virtuelle enhed fra den, der bruges til dine data. Bruge den samme lagerplads-konto, kan det medføre dårlig ydeevne.

Sørge for, at du har følgende oplysninger, inden du går i gang:

- Azure klassisk portalen kontoen med access legitimationsoplysninger.

- En kopi af krypteringsnøglen service data fra enheden fysisk.


## <a name="create-and-configure-the-virtual-device"></a>Oprette og konfigurere den virtuelle enhed

Før du kan udføre disse procedurer, Sørg for, at du er opfyldt [forudsætninger for den virtuelle enhed](#prerequisites-for-the-virtual-device). 

Når du har oprettet et virtuelt netværk, konfigureret en StorSimple Manager-tjeneste, og registreret enheden fysisk StorSimple med tjenesten, kan du bruge følgende trin til at oprette og konfigurere en StorSimple virtuel enhed. 

### <a name="step-1-create-a-virtual-device"></a>Trin 1: Oprette en virtuel enhed

Udfør følgende trin for at oprette den virtuelle StorSimple-enhed.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Hvis oprettelse af den virtuelle enhed mislykkes i dette trin, kan du ikke har forbindelse til internettet. Du kan finde flere oplysninger, gå til [fejlfinding i forbindelse med Internet connectivity fejl](#troubleshoot-internet-connectivity-errors) når creatig en virtuel enhed.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Trin 2: Konfigurere og registrere den virtuelle enhed

Før du starter denne procedure, Sørg for, at du har en kopi af krypteringsnøglen service data. Tjenesten data krypteringsnøglen blev oprettet, når du har konfigureret din første StorSimple enhed, og du er blevet bedt om at gemme den i et sikkert sted. Hvis du ikke har en kopi af krypteringsnøglen service data, skal du kontakte Microsoft Support for at få hjælp.

Udfør følgende trin for at konfigurere og registrere enheden StorSimple virtuelle.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Trin 3: (Valgfrit) Rediger konfigurationsindstillinger enhed

I følgende afsnit beskrives enhed konfigurationsindstillingerne behov for StorSimple virtuel enhed, hvis du vil bruge EXTENSIBLE StorSimple øjebliksbillede Manager eller ændre Enhedsadministrator adgangskoden.

#### <a name="configure-the-chap-initiator"></a>Konfigurere CHAP afsenderen

Denne parameter indeholder de legitimationsoplysninger, som enheden virtuelle (mål) forventer fra initiatorer (servere), der forsøger at få adgang til enhederne. Initiatorer give et CHAP-brugernavn og en CHAP adgangskode til at identificere sig selv til din enhed under denne godkendelse. Detaljeret vejledning, skal du gå til [Konfigurere CHAP til din enhed](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Konfigurere CHAP mål

Denne parameter indeholder de legitimationsoplysninger, der bruger enheden virtuelle, når en CHAP-aktiverede afsenderen anmoder om fælles eller tovejs-godkendelse. Enheden virtuelle bruger Vend CHAP brugernavn og omvendt CHAP-adgangskode til at identificere sig til afsenderen under denne godkendelsesproces. Bemærk, at CHAP target indstillinger er globale indstillinger. Når disse er anvendt, bruge alle de enheder, der er knyttet til den virtuelle enhed lagerplads CHAP-godkendelse. Detaljeret vejledning, skal du gå til [Konfigurere CHAP til din enhed](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurere den StorSimple øjebliksbillede Manager-adgangskode

StorSimple øjebliksbillede Manager software findes på din Windows-vært og giver administratorer mulighed for at administrere sikkerhedskopier af enheden StorSimple i form af lokal og snapshots i skyen.

>[AZURE.NOTE] Til den virtuelle enhed er din Windows-vært en Azure virtuel maskine.

Når du konfigurerer en enhed i StorSimple øjebliksbillede Manager, bliver du bedt om at angive StorSimple enhed IP-adressen og adgangskoden til at godkende enheden lagerplads. Detaljeret vejledning, skal du gå til [konfigurere StorSimple øjebliksbillede Manager-adgangskode](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Ændre administratoradgangskode enhed 

Når du bruger grænsefladen Windows PowerShell til at få adgang til den virtuelle enhed, skal du angive en enhed administratoradgangskode. For sikkerheden for dine data er du påkrævet for at ændre denne adgangskode, før den virtuelle enhed kan bruges. Detaljeret vejledning, skal du gå til [Konfigurer enhed administratoradgangskode](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Oprette forbindelse fra en fjernplacering til den virtuelle enhed
Fjernadgang til enheden virtuelle via Windows PowerShell-grænseflade er ikke aktiveret som standard. Du skal aktivere fjernstyring på den virtuelle enhed først og derefter aktivere den på den klient, der bruges til at få adgang til din virtuelle enhed.

To trin til at oprette forbindelse fra en fjernplacering er beskrevet nedenfor.

### <a name="step-1-configure-remote-management"></a>Trin 1: Konfigurere fjernadministration

Udfør følgende trin for at konfigurere fjernadministration for enheden StorSimple virtuelle.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Trin 2: Få fjernadgang til den virtuelle enhed

Når du har aktiveret fjernadministration på konfigurationssiden af StorSimple enhed, kan du bruge Windows PowerShell remoting at oprette forbindelse til den virtuelle enhed fra en anden virtuel maskine i det samme virtuelle netværk for eksempel kan du oprette forbindelse fra værten for VM, som du konfigureres og bruges til at forbinde iSCSI. I de fleste installationer, vil du allerede har åbnet et offentlige slutpunkt for at få adgang til din vært VM, som du kan bruge til at få adgang til den virtuelle enhed.

>[AZURE.WARNING] **For at øge sikkerheden anbefales det at bruge HTTPS, når du opretter forbindelse til slutpunkterne og derefter slette slutpunkterne, når du har fuldført PowerShell-fjernsession.**

Du skal følge fremgangsmåden i [tilslutning fra en fjernplacering til enheden StorSimple](storsimple-remote-connect.md) til konfiguration af remoting til den virtuelle enhed.

## <a name="connect-directly-to-the-virtual-device"></a>Oprette forbindelse direkte til den virtuelle enhed

Du kan også oprette forbindelse direkte til den virtuelle enhed. Hvis du vil oprette forbindelse direkte til den virtuelle enhed fra en anden computer uden for det virtuelle netværk eller uden for Microsoft Azure-miljø, skal du oprette flere slutpunkter, som det er beskrevet i følgende procedure. 

Udfør følgende trin for at oprette et offentligt slutpunkt på den virtuelle enhed.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Vi anbefaler, at du oprette en forbindelse fra en anden virtuel maskine i det samme virtuelle netværk, fordi denne øvelse minimerer antallet af offentlige slutpunkterne på netværket virtuel. Når du bruger denne metode, kan du blot oprette forbindelse til den virtuelle maskine via en Fjernskrivebord-session og derefter konfigurere denne virtuelle maskine til brug som en anden Windows-klient på et lokalt netværk. Du behøver ikke at føje offentlige portnummeret, fordi porten allerede kendes.

## <a name="work-with-the-storsimple-virtual-device"></a>Arbejde med den virtuelle StorSimple-enhed

Nu, hvor du har oprettet og konfigureret den virtuelle StorSimple-enhed, er du klar til at begynde at arbejde med den. Du kan arbejde med lydstyrken beholdere, enheder og sikkerhedskopiering politikker på en virtuel enhed samme måde som i en fysisk StorSimple enhed. den eneste forskel er, at du har brug at sikre dig, at du vælger den virtuelle enhed fra Enhedslisten. Se [bruge tjenesten StorSimple Manager for at administrere en virtuel enhed](storsimple-manager-service-administration.md) til en trinvis vejledning i de forskellige opgaver til dokumentstyring for den virtuelle enhed.

I følgende afsnit beskrives nogle af forskellene du kan støde på, når du arbejder med den virtuelle enhed.

### <a name="maintain-a-storsimple-virtual-device"></a>Vedligeholde en virtuel StorSimple-enhed

Fordi det er en enhed med kun software, er vedligeholdelse til den virtuelle enhed minimale, når der sammenlignes med vedligeholdelse for den fysiske enhed. Du har følgende indstillinger:

- **Softwareopdateringer** – du kan få vist den dato, hvor softwaren sidst blev opdateret, sammen med en opdatere statusmeddelelser. Du kan bruge knappen **Søg opdateringer** nederst på siden til at foretage en manuel scanning, hvis du vil søge efter nye opdateringer. Hvis der er tilgængelige opdateringer, skal du klikke på **Installer opdateringer** til at installere. Da der er kun en enkelt grænseflade på den virtuelle enhed, betyder det, vises der en lille tjenesten blev afbrudt når der anvendes opdateringer. Den virtuelle enhed lukkes og genstart (hvis det er nødvendigt) for at anvende eventuelle opdateringer, der er blevet frigivet. Gå til en trinvis fremgangsmåde, at [opdatere din enhed](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Understøtter pakke** – du kan oprette og overføre en supportpakke for at hjælpe Microsoft Support til at foretage fejlfinding af problemer med enheden virtuelle. Gå til at [oprette og administrere en supportpakke](storsimple-create-manage-support-package.md)en trinvis fremgangsmåde.

### <a name="storage-accounts-for-a-virtual-device"></a>Lagerplads konti til en virtuel enhed

Der oprettes lagerplads konti til brug af tjenesten StorSimple Manager, efter den virtuelle enhed og efter den fysiske enhed. Når du opretter kontiene lagerplads, anbefales det, at du bruger et område-id i det fulde navn for at sikre, at området er konsistente alle systemkomponenter. Det er vigtigt, at alle komponenter er i det samme område for at forhindre problemer med ydeevnen for en virtuel enhed.

Gå til en trinvis fremgangsmåde, at [tilføje en lagerplads konto](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Deaktivere en virtuel StorSimple-enhed

Deaktivere en virtuel enhed sletter VM og de ressourcer, der er oprettet, da den blev klargjort. Når den virtuelle enhed er deaktiveret, kan ikke gendannes til den forrige tilstand. Før du har deaktiveret den virtuelle enhed, skal du sørge for at stoppe eller slette klienter og værter, der afhænger af den.

Deaktivere en virtuel enhed resulterer i følgende handlinger:

- Den virtuelle enhed fjernes.

- OS disken og datadisce, der er oprettet for den virtuelle enhed fjernes.

- Tjenesten hostet og virtuelt netværk, der er oprettet under klargøringen bevares. Hvis du ikke bruger dem, skal du slette dem manuelt.

- Skyen snapshots, der er oprettet for den virtuelle enhed bevares.

Finde en trinvis, gå til [Deaktiver og slette enheden StorSimple](storsimple-deactivate-and-delete-device.md).

Så snart den virtuelle enhed vises som deaktiveret på siden StorSimple Manager, kan du slette den virtuelle enhed fra liste over enheder på siden **enheder** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Starte, stoppe og genstarte en virtuel enhed
I modsætning til StorSimple fysisk enheden er der ingen strøm, på eller power fra knappen til at overføre på en virtuel StorSimple-enhed. Der kan være situationer, hvor du har brug for at afslutte og genstarte den virtuelle enhed. For eksempel kræver visse opdateringer, at VM genstartes for at fuldføre opdateringen. Den nemmeste måde at starte stop og genstart en virtuel enhed er at bruge virtuelle maskiner Management Console.

Når du ser på Management Console, er den virtuelle Enhedsstatus **kører** fordi den er startet som standard, når den er oprettet. Du kan starte, stoppe eller genstarte en virtuel maskine når som helst.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Nulstille til fabriksindstillingerne

Hvis du beslutter, at du blot ønsker at starte forfra med enheden virtuelle, kan du bare deaktivere og slette den og derefter oprette en ny. Ligesom når enheden fysisk er nulstillet, vil din nye virtuelle enhed ikke have nogen opdateringer, der er installeret på. derfor Sørg for at søge efter opdateringer inden du bruger den.


## <a name="fail-over-to-the-virtual-device"></a>Skifte til den virtuelle enhed

Nedbrud (DR) er et af de vigtigste scenarier, som den virtuelle enhed StorSimple er udviklet til. I dette scenarie skal fysiske StorSimple enhed eller hele datacenter muligvis ikke tilgængelige. Heldigvis kan du bruge en virtuel enhed til at gendanne handlinger på en anden placering. Under DR, lydstyrke beholdere fra kildeenheden ændre ejerskab og overføres til den virtuelle enhed. Forudsætninger for DR er, at den virtuelle enhed er oprettet og konfigureret, alle enheder i objektbeholderen lydstyrken er blevet ført offline og objektbeholderen lydstyrken har en tilknyttet skyen øjebliksbillede.

>[AZURE.NOTE] 
>
> - Når du bruger en virtuel enhed som den sekundære enhed til DR, Husk på, at 8010 har 30 TB Standard-lager og 8020 har 64 TB Premium-lager. Den højere kapacitet 8020 virtuelle enhed kan være mere egnet til et scenarie med DR.
> - Du kan ikke failover eller Klon fra en enhed kører opdatering 2 til en enhed, der kører før opdatering 1 software. Du kan dog ikke over en enhed, der kører opdatering 2 til en enhed kører opdatering 1 (1.1 eller 1.2)

Gå til en trinvis fremgangsmåde, Sådan [flytter du til en virtuel enhed](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Lukke eller slette den virtuelle enhed

Hvis du tidligere har konfigureret og brugt en virtuel enhed men nu vil stoppe med at periodisering af Beregn gebyrer til dens brug StorSimple, kan du lukke den virtuelle enhed. Lukke den virtuelle enhed slettet ikke, dens operativsystem eller datadisce i lagerplads. Det forhindrer gebyrer periodisering af på dit abonnement, men fortsat lagerplads gebyrer om en OS og data disk.

Hvis du sletter eller lukke den virtuelle enhed, vises som **Offline** på siden enheder i tjenesten StorSimple Manager. Du kan vælge at deaktivere eller slette enheden, hvis du også vil slette de sikkerhedskopier, der er oprettet af den virtuelle enhed. Få mere at vide under [Deaktiver og Slet en StorSimple enhed](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Fejlfinding i forbindelse med Internet connectivity fejl 

Under oprettelse af en virtuel enhed, hvis der er ingen forbindelse til internettet, mislykkes oprettelse af trin. Fejlfinde Hvis fejlen er på grund af forbindelse til internettet, skal du udføre følgende trin på Azure klassisk portalen:

1. Oprette en Windows server 2012 virtuel maskine i Azure. Denne virtuelle computer der skal bruges samme lagerplads konto, VNet og undernet, som bruges af enheden virtuelle. Hvis du allerede har en eksisterende Windows Server-vært i Azure med samme lagerplads konto, Vnet og undernet, kan du også bruge den til at foretage fejlfinding af forbindelse til internettet.
2. Fjernadgang til log ind i den virtuelle maskine, der er oprettet i det foregående trin. 
3. Åbne et kommandovindue i den virtuelle maskine (gevinst + R og derefter skrive `cmd`).
4. Kør følgende cmd kommandoprompten.

    `nslookup windows.net`

5. Hvis `nslookup` mislykkes, og derefter Internet connectivity fejl forhindrer den virtuelle enhed i registrering til tjenesten StorSimple Manager. 
6. Foretag de ønskede ændringer til virtuelle netværk for at sikre, at den virtuelle enhed er få adgang til Azure websteder som "windows.net".

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere en virtuel enhed](storsimple-manager-service-administration.md).
 
- Forstå, hvordan du kan [gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier](storsimple-restore-from-backup-set.md). 

