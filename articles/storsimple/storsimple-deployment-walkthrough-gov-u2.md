<properties
   pageTitle="Installere StorSimple enhed i Government Portal | Microsoft Azure"
   description="I denne artikel beskrives de trin og bedste fremgangsmåder til implementering af tjenesten StorSimple opdatering 2 enhed og i portalen Azure Government."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="v-sharos" />

# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal-update-2"></a>Installere enheden lokale StorSimple i portalen Government (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Oversigt

Velkommen til Microsoft Azure StorSimple enhed installation. Disse installation selvstudier gælder for StorSimple 8000 serien kører opdatering 2-softwaren på portalen Azure Government. Denne serie af selvstudier omfatter en tjekliste for konfiguration, en liste over konfiguration forudsætninger og detaljerede konfigurationstrinnene for enheden StorSimple.

Oplysningerne i disse selvstudier antages det, at du har gennemgået sikkerhedsforanstaltninger og pakkes ud, rack og tommer tilsluttede enheden StorSimple. Hvis du stadig nødt til at udføre opgaverne, skal du starte med at gennemgå de [sikkerhedsforanstaltninger](storsimple-safety.md). Følg vejledningen for enhed-specifikke at Pak, rack Indlæs og kabel din enhed.

- [Pak, rack Indlæs og kabel din 8100](storsimple-8100-hardware-installation.md)
- [Pak, rack Indlæs og kabel din 8600](storsimple-8600-hardware-installation.md)

Du skal have administratorrettigheder for at fuldføre processen installation og konfiguration. Vi anbefaler, at du gennemser Tjekliste for konfiguration, inden du går i gang. Installation og konfiguration processen kan gå et stykke tid at fuldføre.

> [AZURE.NOTE] StorSimple installationsoplysninger publiceret på webstedet Microsoft Azure gælder for StorSimple 8000 serie-enheder. Yderligere oplysninger om de 7000 serie enheder, gå til: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Du kan finde oplysninger om installation 7000 serien, [StorSimple System Startvejledning](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Installation trin

Udføre disse trin, der er nødvendige for at konfigurere enheden StorSimple og forbinde den til din StorSimple Manager-tjeneste. Ud over de påkrævede trin er der valgfrit trin og procedurer, som du kan være nødvendigt at udfylde under installationen. Trinvis implementering vejledningen angiver, når du skal udføre hvert af disse valgfrit trin.


| Trin                                                                                   | Beskrivelse                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FORUDSÆTNINGER**                                                                      | Disse skal udføres med henblik på den kommende installation.                                                                                        |
|[Tjekliste for konfiguration af installation](#deployment-configuration-checklist)                                                     | Du kan bruge denne tjekliste til at indsamle og registrere oplysninger før og under installationen.                                                                       |
| [Forudsætninger for installation](#deployment-prerequsites)                                                               | Disse validere, miljøet er klar til installation.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **TRINVIS IMPLEMENTERING**                                                                   | Disse trin skal installere enheden StorSimple fremstilling.                                                                                      |
| [Trin 1: Oprette en ny tjeneste](#step-1-create-a-new-service)                                                         | Konfigurere skyen administration og lagerplads på enheden StorSimple. *Spring dette trin, hvis du har en eksisterende tjeneste til andre StorSimple enheder*.              |
| [Trin 2: Gøre tasten service registrering](#step-2-get-the-service-registration-key)                                               | Brug denne tast til at registrere og tilslutte enheden StorSimple med management-tjenesten.                                                                         |
| [Trin 3: Konfigurere og registrere enheden via Windows PowerShell til StorSimple](#step 3-configure-and-register-the-device-through-windows-powershell-for-storsimple) | Slutte enheden til dit netværk, og Registrer den med Azure at fuldføre konfigurationen ved hjælp af tjenesten administration.                                            |
| [Trin 4: Fuldfør konfigurationen mindste enhed](#step-4-complete-the-minimum-device-setup) </br>Valgfrit: Opdatere enheden StorSimple. | Brug management service til at fuldføre konfigurationen enhed og gør det muligt at angive lagerplads.                                                                      |
| [Trin 5: Oprette en lydstyrken objektbeholder](#step-5-create-a-volume-container)                                                      | Oprette en objektbeholder til klargøring enheder. En lydstyrken objektbeholder har lagerplads konto, båndbredde og krypteringsindstillinger for alle de enheder, der er indeholdt i den.    |
| [Trin 6: Oprette en volumenlicens](#step-6-create-a-volume)                                                               | Klargøre lagerplads diskenhed(er) på StorSimple enheden for serverne.                                                                                        |
| [Trin 7: Tilslutte, initialiseret og formatere en volumenlicens](#step-7-mount-initialize-and-format-a-volume) </br>Valgfrit: Konfigurere MPIO.            | Tilslut din servere til iSCSI-lager, der leveres af enheden. Du kan også konfigurere MPIO for at sikre, at serverne kan acceptere link-, Netværks- og fejl i brugergrænseflade.                                                                                                                                                              |
| [Trin 8: Tag en sikkerhedskopi](#step-8-take-a-backup)                                                                  | Konfigurere din sikkerhedskopiering politik til at beskytte dine data                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **ANDRE PROCEDURER**                                                                   | Du muligvis skal referere til disse procedurer, som du installerer din løsning.                                                                                    |
| [Konfigurere en ny konto lagerplads for tjenesten](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Søge efter og installere opdateringer](#scan-for-and-apply-updates)                                                   |                                                                                                                                                               |
| [Få IQN af en Windows Server-vært](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Oprette en sikkerhedskopi manuelt](#create-a-manual-backup)                                                                 |
| [Konfigurere MPIO](#configure-mpio)                                                                          |

## <a name="deployment-configuration-checklist"></a>Tjekliste for konfiguration af installation

Før du installerer enheden StorSimple, skal du indsamle oplysninger for at konfigurere softwaren på din enhed. Forberede nogle af disse oplysninger forvejen hjælper strømline processen med at udrulle StorSimple enhed i dit miljø. Hente og bruge denne tjekliste til Bemærk konfigurationsoplysninger, som du installerer din enhed.  

[Hente Tjekliste for konfiguration af StorSimple installation](http://www.microsoft.com/download/details.aspx?id=49159)  

## <a name="deployment-prerequisites"></a>Forudsætninger for installation

I følgende afsnit beskrives konfiguration forudsætningerne for din StorSimple Manager tjeneste og enheden StorSimple.

### <a name="for-the-storsimple-manager-service"></a>For tjenesten StorSimple Manager

Før du begynder, skal du kontrollere, som:

- Du har din Microsoft-konto med access legitimationsoplysninger.

- Du har kontoen Microsoft Azure-lager med access legitimationsoplysninger.

- Microsoft Azure-abonnement er aktiveret for tjenesten StorSimple Manager. Dit abonnement skal købes gennem [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Du har adgang til terminalemuleringssoftware som trykfarver.

### <a name="for-the-device-in-the-datacenter"></a>For enheden i datacenter

Inden du konfigurerer enheden, Sørg for, at:

- Enheden er pakkes fuldt ud, tilsluttet på et rack og fuldt kabel for power-, Netværks- og seriel adgang, som beskrevet i:

    -  [Pak, rack Indlæs og enheden 8100-kabel](storsimple-8100-hardware-installation.md)
    -  [Pak, rack Indlæs og enheden 8600-kabel](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>For netværket i datacenteret

Før du begynder, skal du kontrollere, som:

- Portene i firewallen datacenter er åbnet for at muliggøre iSCSI og trafik som beskrevet i [netværk krav til enheden StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)i skyen.

## <a name="step-by-step-deployment"></a>Trinvis implementering

Brug følgende trinvise instruktioner til at installere enheden StorSimple i datacenteret.

## <a name="step-1-create-a-new-service"></a>Trin 1: Oprette en ny tjeneste

En StorSimple Manager tjeneste kan administrere flere StorSimple enheder. Udfør følgende trin for at oprette en ny forekomst af tjenesten StorSimple Manager.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] Hvis du ikke har aktiveret automatisk oprettelse af en lagerplads konto med din tjeneste, skal du oprette mindst én lagerplads konto, når du har oprettet en tjeneste. Denne lagerplads konto der skal bruges, når du opretter en objektbeholder lydstyrken.
>
> * Hvis du ikke har oprettet en lagerplads konto automatisk, kan du gå til [konfigurere en ny konto lagerplads for tjenesten](#configure-a-new-storage-account-for-the-service) kan finde detaljerede oplysninger.
> * Hvis du har aktiveret automatisk oprettelse af en lagerplads-konto, gå til [trin 2: hente service registrering nøglen](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Trin 2: Gøre tasten service registrering

Når tjenesten StorSimple Manager er oppe at køre, skal du hente service registrering nøglen. Denne tast bruges til at registrere og tilslutte enheden StorSimple til tjenesten.

Udfør følgende trin på portalen Government.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Trin 3: Konfigurere og registrere enheden via Windows PowerShell til StorSimple

Bruge Windows PowerShell til StorSimple til at fuldføre den indledende opsætning af enheden StorSimple, som beskrevet i følgende procedure. Du skal bruge terminalemuleringssoftware til at udføre dette trin. Du kan finde yderligere oplysninger finder [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Trin 4: Fuldfør indstillinger for mindste enhed

For enhedskonfiguration af enheden StorSimple mindste er du påkrævet for at:

- Konfigurere den sekundære DNS-server.
- Aktivere iSCSI på mindst ét netværksgrænseflade.
- Tildele fast IP-adresser til begge enheder.

Udfør følgende trin på portalen Government at fuldføre konfigurationen mindste enhed.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Trin 5: Oprette en lydstyrken objektbeholder

En lydstyrken objektbeholder har lagerplads konto, båndbredde og krypteringsindstillinger for alle de enheder, der er indeholdt i den. Du skal oprette en lydstyrken beholder, før du kan starte klargøring enheder på enheden StorSimple.

Udfør følgende trin på portalen Government til at oprette en objektbeholder lydstyrken.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Trin 6: Oprette en volumenlicens

Når du opretter en lydstyrken objektbeholder, kan du klargør et lager lydstyrken på enheden StorSimple til dine servere. Udfør følgende trin på portalen Government til at oprette en volumenlicens.

> [AZURE.IMPORTANT] Azure StorSimple kan oprette kun tyndt klargjort enheder.  Du kan ikke oprette klargjort fuldt eller delvist klargjort enheder på en Azure StorSimple system.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Trin 7: Tilslutte, initialiseret og formatere en volumenlicens

Udfør disse trin på din Windows Server-vært.

> [AZURE.IMPORTANT]

> - For høj tilgængeligheden af din StorSimple løsning anbefaler vi, at du konfigurerer MPIO på din værtsservere (valgfrit) før konfiguration af iSCSI. MPIO konfiguration på værtsservere sikrer, at serverne kan acceptere et hyperlink, et netværk eller en fejl i brugergrænseflade.

> - Gå til [Konfigurere MPIO for enheden StorSimple](storsimple-configure-mpio-windows-server.md)for MPIO og iSCSI installation og konfiguration af instruktioner til Windows Server-vært. Disse kan også indeholde trinnene for at tilslutte, initialiseret og formater StorSimple drev.

> - Finde MPIO og iSCSI installation og konfiguration af instruktioner til en Linux-vært, gå til [Konfigurere MPIO til din StorSimple Linux-vært](storsimple-configure-mpio-on-linux.md)

Hvis du beslutter dig for ikke at konfigurere MPIO, kan du udføre følgende trin for at tilslutte, initialiseret og formatere dine StorSimple drev på en Windows Server-vært.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Trin 8: Tag en sikkerhedskopi

Sikkerhedskopier giver punkt-in-time beskyttelse af enheder og forbedre mulighed for gendannelse og minimere Gendan gange. Du kan foretage to typer sikkerhedskopi på enheden StorSimple: lokale snapshots og skyen snapshots. Hver af disse ekstra typer kan være **planlagt** eller **manuelt**.

Udfør følgende trin på portalen Government til at oprette en planlagt sikkerhedskopiering.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Du kan bruge en manuel sikkerhedskopiering når som helst. For procedurer, skal du gå til [oprette en sikkerhedskopi manuelt](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurere en ny konto lagerplads for tjenesten

Dette er et valgfrit trin, du skal bruge til at udføre kun, hvis du ikke har aktiveret automatisk oprettelse af en lagerplads konto med din tjeneste. En Microsoft Azure-lager konto kræves for at oprette en StorSimple lydstyrken beholder.

Hvis du vil oprette en Azure-lager-konto i et andet område skal du se [Om Azure lagerplads konti](../storage/storage-create-storage-account.md) til en trinvis vejledning.

Udfør følgende trin i portalen Government, på siden **StorSimple Manager** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Brug trykfarver til at oprette forbindelse til enhed seriel konsollen

Hvis du vil oprette forbindelse til Windows PowerShell til StorSimple, skal du bruge terminalemuleringssoftware som trykfarver. Du kan bruge trykfarver, når du får adgang til enheden direkte via konsollen seriel eller ved at åbne en telnet-session fra en fjerncomputer.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Søge efter og installere opdateringer

Opdatere din enhed, kan det tage flere timer. Udfør følgende trin for at søge efter og installere opdateringer på din enhed.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Opdatere din enhed

1.  Siden enhed **Hurtig Start** , klik på **enheder**. Vælg den fysiske enhed, skal du klikke på **vedligeholdelse** , og klik derefter på **Scanne opdateringer**.  
2.  Der oprettes et job til at søge efter tilgængelige opdateringer. Hvis der er tilgængelige opdateringer, ændres de **Scanne opdateringer** til **Installer opdateringer**. Klik på **Installer opdateringer**.
3.  Der oprettes en update-job. Overvåge status for opdateringen ved at navigere til **sager**.

    > [AZURE.NOTE] Når kørslen Opdater starter, vises det med det samme status som 50 procent. Status ændres til 100 procent, når update jobbet er fuldført. Der er ingen realtid status for opdateringsprocessen.

4.  Når enheden er opdateret, aktivere Data 2 og 3 Data netværksgrænseflader, hvis dette er deaktiveret.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Få IQN af en Windows Server-vært

Udfør følgende trin for at få iSCSI kvalificerede navn (IQN) på en Windows-vært, der kører Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Oprette en sikkerhedskopi manuelt

Udfør følgende trin på portalen Government til at oprette en manuel sikkerhedskopi efter behov for en enkelt lydstyrken på enheden StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Konfigurere MPIO

Multi-path i/o-(MPIO) er en valgfri funktion og er ikke installeret på Windows Server som standard. Det skal være installeret som en funktion via Server Manager. Gå til [Konfigurere MPIO for enheden StorSimple](storsimple-configure-mpio-windows-server.md)MPIO installationsvejledning.

Gå til [Konfigurere MPIO til din Linux vært](storsimple-configure-mpio-on-linux.md)for MPIO installationsvejledning til en StorSimple-enhed har forbindelse til en Linux-vært.

> [AZURE.NOTE] MPIO understøttes ikke på en StorSimple virtuel enhed i Azure.

## <a name="next-steps"></a>Næste trin

- Konfigurere en [virtuel enhed](storsimple-virtual-device-u2.md).

- Bruge [tjenesten StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) til at administrere din StorSimple enhed.
