<properties
   pageTitle="Installere StorSimple virtuelle matrix 1 - portalen forberedelse"
   description="Første selvstudium til at udrulle StorSimple virtuelle matrix omfatter forberede portalen"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Installere StorSimple virtuelle matrix - forberede portalen

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Oversigt

I denne artikel gælder for Microsoft Azure StorSimple virtuelle matrix (også kaldet StorSimple lokale virtuel enhed eller StorSimple virtuel enhed), der kører marts 2016 generelt tilgængelig (GA) version. Dette er den første artikel i serien i installation selvstudier, der kræves for at installere helt din virtuelle matrixen som en filserver eller en iSCSI-server. I denne artikel beskrives den forberedelse, der kræves til at oprette og konfigurere StorSimple Manager tjenesten før klargøring af en virtuel matrix. I denne artikel indeholder også links ud af, at en tjekliste for konfiguration af installation og konfiguration forudsætninger.

Du skal have administratorrettigheder for at fuldføre processen installation og konfiguration. Vi anbefaler, at du gennemser Tjekliste for konfiguration af installation, inden du går i gang. Portalen forberedelse tager mindre end 10 minutter.

De oplysninger, der er udgivet i denne artikel gælder for installation af StorSimple virtuelle matrixer i Azure klassisk portal samt Microsoft Azure Government Cloud.

### <a name="get-started"></a>Komme i gang

Arbejdsprocessen for installation består af forberede portalen, klargøring af en virtuel matrix i din virtuelt miljø, og Fuldfør konfigurationen. For at komme i gang med installationen StorSimple virtuelle matrix som en filserver eller en iSCSI-server, skal du henvise til følgende tabelform ressourcer (artikler og videoer).

#### <a name="deployment-articles"></a>Installation artikler

Se følgende artikler i den angivne rækkefølge til at udrulle dine StorSimple virtuelle matrix.

| **#** | **I dette trin**                          | **Du skal gøre dette...**                                                         | **Brug disse dokumenter.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Konfigurere portalen Azure klassisk**       | Oprette og konfigurere din StorSimple Manager tjeneste før klargøring af en StorSimple virtuel enhed.  |[Forberede dig på portalen](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Klargøre den virtuelle matrix**           | Klargøre Hyper-V, og oprette forbindelse til en virtuel enhed StorSimple på et hostsystem kører Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. <br></br> <br></br> Klargøre VMware, og oprette forbindelse til en StorSimple lokale virtuel enhed på et hostsystem kører VMware ESXi 5.5 og over.<br></br>| [Klargør et virtuelt matrix i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Klargør et virtuelt matrix i VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Konfigurere den virtuelle matrix**              | Til din filserver, udfører indledende installation, registrere din StorSimple filserver og fuldføre konfigurationen enhed. Du kan derefter klargøre små og mellemstore virksomheder aktier. <br></br> <br></br> Udføre indledende installation til din iSCSI-server, Registrer StorSimple iSCSI-serveren, og afslutte konfigurationen af enhed. Du kan derefter klargøre iSCSI-enheder.| [Konfigurere virtuelle matrix som filserver](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Konfigurere virtuelle matrixen som iSCSI-server](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Installation videoer

| **At udføre dette trin...** |  **Se denne video.**|
|----------------|-------------|
| Trinvis vejledning i at komme i gang med det virtuelle StorSimple-matrix. | [Introduktion til den virtuelle StorSimple-matrix](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| En trinvis vejledning til at klargøre en StorSimple virtuelle matrix i Hyper-V.|[Oprette en StorSimple virtuelle matrix](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|En trinvis vejledning til at konfigurere og registrere en StorSimple virtuelle matrix|[Konfigurere en StorSimple virtuelle matrix](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|En trinvis vejledning til at oprette aktier, sikkerhedskopiere aktier og gendanne data i en StorSimple virtuelle matrix konfigureret som en filserver|[Brug den virtuelle StorSimple-matrix](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Trinvise instruktioner til failover og genoprettelse efter nedbrud af en StorSimple virtuelle matrix|[StorSimple virtuelle matrix nedbrud](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Nu kan du begynde at konfigurere Azure klassisk portalen.

## <a name="configuration-checklist"></a>Tjekliste for konfiguration af

Tjekliste for konfiguration beskrives de oplysninger, du har brug for at indsamle, før du konfigurerer softwaren på enheden StorSimple. Forberede oplysningerne forvejen hjælper strømline processen med at udrulle StorSimple enhed i dit miljø. Afhængigt af, om enheden StorSimple virtuelle installeres som en filserver eller en iSCSI-server, skal du en af de følgende kontrollister.

-   Hent [Tjekliste for Server konfiguration af StorSimple virtuelle matrix fil](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Hent [StorSimple virtuelle matrix iSCSI Tjekliste for konfiguration af Server](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Forudsætninger

Her finder du konfiguration forudsætningerne for din StorSimple Manager-tjeneste, enheden StorSimple virtuelle og datacenter netværket.

### <a name="for-the-storsimple-manager-service"></a>For tjenesten StorSimple Manager

Før du begynder, skal du kontrollere, som:

-   Du har din Microsoft-konto med access legitimationsoplysninger.

-   Du har kontoen Microsoft Azure-lager med access legitimationsoplysninger.

-   Abonnementet Microsoft Azure skal være aktiveret for tjenesten StorSimple Manager.

### <a name="for-the-storsimple-virtual-device"></a>Til den virtuelle StorSimple-enhed

Før du installerer en virtuel enhed, skal du kontrollere, som:

-   Du har adgang til et hostsystem kører Hyper-V på Windows Server 2008 R2 eller nyere eller VMware (ESXi 5,5 eller nyere), der kan bruges til at en bestemmelse en enhed.

-   Host systemet er kan du bruge følgende ressourcer for at klargøre enheden virtuelle:

    -   Et minimum af 4 kerner.

    -   Mindst 8 GB RAM.

    -   Ét netværk brugergrænseflade.

    -   En 500 GB Virtuel disk for systemdata.

### <a name="for-the-datacenter-network"></a>For netværket, datacenter

Før du begynder, skal du kontrollere, som:

-   Netværk i dit datacenter er konfigureret i overensstemmelse med netværk kravene til enheden StorSimple. Du kan finde yderligere oplysninger finder [StorSimple virtuelle matrix-systemkrav](storsimple-ova-system-requirements.md).

-   Enheden StorSimple virtuelle har en dedikeret 5 Mbps internetbåndbredde (eller mere) tilgængelig til enhver tid. Denne båndbredde skal ikke deles med andre programmer.

## <a name="step-by-step-preparation"></a>Trinvise forberedelse

Brug følgende trinvise instruktioner til at forberede din portal til tjenesten StorSimple Manager.

## <a name="step-1-create-a-new-service"></a>Trin 1: Oprette en ny tjeneste

En enkelt forekomst af tjenesten StorSimple Manager kan administrere flere StorSimple 1200 enheder. Udfør følgende trin for at oprette en ny forekomst af tjenesten StorSimple Manager. Hvis du har en eksisterende StorSimple Manager-tjeneste til at administrere dine 1200 enheder, kan du springe dette trin over og gå til [trin 2: hente service registrering nøglen](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Hvis du ikke har aktiveret automatisk oprettelse af en lagerplads konto med din tjeneste, skal du oprette mindst én lagerplads konto, når du har oprettet en tjeneste.
>

> - Hvis du ikke har oprettet en lagerplads konto automatisk, kan du gå til [konfigurere en ny konto lagerplads for tjenesten](#optional-step-configure-a-new-storage-account-for-the-service) kan finde detaljerede oplysninger.
>

> - Hvis du har aktiveret automatisk oprettelse af en lagerplads-konto, gå til [trin 2: hente service registrering nøglen](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Trin 2: Gøre tasten service registrering


Når tjenesten StorSimple Manager er oppe at køre, skal du hente service registrering nøglen. Denne tast bruges til at registrere og forbinde enheden StorSimple med tjenesten.

Udfør følgende trin på [Azure klassisk portal](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> Tasten service registrering bruges til at registrere alle de StorSimple Manager-enheder, der har brug for at registrere med din StorSimple Manager-tjeneste.

## <a name="step-3-download-the-virtual-device-image"></a>Trin 3: Hent virtuel enhed-billede

Når du har tasten service registrering, skal du hente den relevante virtuel enhed billede for at klargøre en virtuel enhed på systemet host. Virtuel Enhedsbillederne operativsystemet og kan hentes fra siden Hurtig Start i portalen Azure klassisk.

> [AZURE.IMPORTANT] Softwaren, der kører på den virtuelle StorSimple matrix kan kun bruges sammen med tjenesten Storsimple Manager.


Udfør følgende trin på [Azure klassisk portal](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Med det virtuelle enhed billede

1.  Klik på den tjeneste, du har oprettet, på siden **StorSimple Manager-tjenesten** . Dette kommer du til siden **Hurtig Start** . (Du kan klikke på Hurtig start-ikonet ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) adgang til **Hurtig Start** siden når som helst.)

1.  Klik på linket svarer til det billede, du vil hente fra Microsoft Download Center. Billedfilerne er cirka 4,8 GB.

    -   VHDX for Hyper-V på Windows Server 2012 og nyere

    -   Virtuel harddisk til Hyper-V på Windows Server 2008 R2 og nyere

    -   VMDK til VMWare ESXi 5.5 og nyere

2.  Hente og udpakke filen til et lokalt drev, foretage en note på, hvor udpakket filen er placeret.

![videoikon](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Video, der er tilgængelige**

Se videoen til en trinvis vejledning til at komme i gang med StorSimple virtuelle matrixen.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Valgfrit: konfigurere en ny konto lagerplads for tjenesten

Dette er et valgfrit trin, der skal udføres kun, hvis du ikke har aktiveret automatisk oprettelse af en lagerplads konto med din tjeneste.

Hvis du vil oprette en Azure-lager-konto i et andet område, se, [hvordan du opretter en lagerplads konto](storage-create-storage-account.md#create-a-storage-account) til en trinvis vejledning.

Udfør følgende trin på [Azure klassisk portal](https://manage.windowsazure.com/) på siden StorSimple Manager til at tilføje en eksisterende konto til Microsoft Azure-lager.

#### <a name="to-add-a-storage-account"></a>Tilføje en lagerplads-konto

1.  Vælg din tjeneste på landingssiden tjenesten StorSimple Manager, og dobbeltklik på den. Dette kommer du til siden **Hurtig Start** . Vælg siden **Konfigurer** .

2.  Klik på **Add/edit lagerplads konto**. I dialogboksen **Add/Edit lagerplads konto** skal du gøre følgende:

    1.  Klik på **Tilføj ny**.

    1.  Angiv et navn til kontoen lagerplads.

    1.  Angiv den primære **Hurtigtast** for kontoen Microsoft Azure-lager.

    1.  Vælg **Aktivér SSL-tilstand** til at oprette en sikker kanal af netværkskommunikation mellem enheden og skyen. Fjern afkrydsningsfeltet **Aktiver SSL-tilstand** , kun, hvis du arbejder i en privat sky.

    1.  Klik på ikonet Kontrollér ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Du får besked, når lagerplads konto er blevet oprettet.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Den nyoprettede lagerplads konto vises på siden **Konfigurer** under **lagerplads konti**. Klik på **Gem** for at gemme den nyoprettede lagerplads konto. Klik på **OK** , når du bliver bedt om at bekræfte.


## <a name="next-step"></a>Næste trin

Næste trin er at klargøre en virtuel maskine til enheden StorSimple virtuelle. Afhængigt af din værtsoperativsystemet, skal du se den detaljerede vejledning i:

-   [Klargøre en StorSimple virtuelle matrix i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Klargøre en StorSimple virtuelle matrix i VMware](storsimple-ova-deploy2-provision-vmware.md)
