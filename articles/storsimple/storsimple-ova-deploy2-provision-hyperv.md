<properties
   pageTitle="Installere StorSimple virtuelle matrix - klargøring i Hyper-V"
   description="Dette andet selvstudium i StorSimple virtuelle matrix installation omfatter klargøring af en virtuel enhed i Hyper-V."
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
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Installere StorSimple virtuelle matrix - Klargør et virtuelt matrix i Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Oversigt

Selvstudiet klargøring gælder for Microsoft Azure StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder eller StorSimple virtuelle enheder), der kører marts 2016 generelt tilgængelig (GA) version. Dette selvstudium beskriver, hvordan du klargør en StorSimple virtuelle matrix på et hostsystem kører Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2. I denne artikel gælder for installation af StorSimple virtuelle matrixer i Azure klassisk portal samt Microsoft Azure Government Cloud.

Du skal have administratorrettigheder for at blive klargjort og konfigurere en virtuel enhed. Klargøring og indledende konfigurationen kan tage omkring 10 minutter for at gennemføre.


## <a name="provisioning-prerequisites"></a>Klargøring af forudsætninger

Her finder du forudsætningerne til at klargøre en virtuel enhed på et hostsystem kører Hyper-V på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>For tjenesten StorSimple Manager

Før du begynder, skal du kontrollere, som:

-   Du har gennemført alle trin i [forberede portal til StorSimple virtuelle matrix](storsimple-ova-deploy1-portal-prep.md).

-   Du har hentet virtuel enhed billedet til Hyper-V fra Azure-portalen. Du kan finde flere oplysninger, se [trin 3: hente virtuel enhed billedet](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] Softwaren, der kører på den virtuelle StorSimple matrix kan kun bruges sammen med tjenesten Storsimple Manager.

### <a name="for-the-storsimple-virtual-device"></a>Til den virtuelle StorSimple-enhed

Før du installerer en virtuel enhed, skal du kontrollere, som:

-   Du har adgang til et hostsystem kører Hyper-V på Windows Server 2008 R2 eller nyere, der kan bruges til at en bestemmelse en enhed.

-   Host systemet er kan du bruge følgende ressourcer for at klargøre enheden virtuelle:

    -   Et minimum af 4 kerner.

    -   Mindst 8 GB RAM.

    -   Ét netværk brugergrænseflade.

    -   En 500 GB Virtuel disk for systemdata.

### <a name="for-the-network-in-the-datacenter"></a>For netværket i datacenteret

Før du begynder, skal du gennemse netværk kravene til at implementere en StorSimple virtuel enhed og konfigurere datacenter netværket korrekt. Du kan finde yderligere oplysninger finder [StorSimple virtuelle matrix netværk krav](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Trinvise klargøring

Hvis du vil klargøre og oprette forbindelse til en virtuel enhed, skal du udføre følgende trin:

1.  Sørg for, at host systemet har tilstrækkelige ressourcer til kravene mindste virtuel enhed.

2.  Klargøre en virtuel enhed i din hypervisor.

3.  Start den virtuelle enhed og hente IP-adresse.

Hver af disse trin er beskrevet i følgende afsnit.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Trin 1: Sikre, at host systemet opfylder minimumkravene virtuel enhed

Hvis du vil oprette en virtuel enhed, skal du:

-   Rollen Hyper-V er installeret på Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 SP1.

-   Microsoft Hyper-V Manager på en Microsoft Windows-klient, der er forbundet til værten.

Du skal sikre dig, den underliggende hardware (hostsystem), som du opretter den virtuelle enhed er i stand til at implementere enheden virtuelle følgende ressourcer:

- Et minimum af 4 kerner.
- Mindst 8 GB RAM.
- Ét netværk brugergrænseflade.
- En 500 GB Virtuel disk for systemdata.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Trin 2: Klargøring af en virtuel enhed i hypervisor

Udfør følgende trin for at klargøre en enhed i din hypervisor.

#### <a name="to-provision-a-virtual-device"></a>Til at klargøre en virtuel enhed

1.  På din Windows Server-vært skal du kopiere billedet virtuel enhed til et lokalt drev. Dette er den billede (Virtuelle eller VHDX), du har hentet via Azure-portalen. Noter til den placering, hvor du kopierede billedet, som du vil bruge dette senere i proceduren.

2.  Åbn **Server Manager**. Klik på **Funktioner** i øverste højre hjørne, og vælg **Hyper-V Manager**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Hvis du kører Windows Server 2008 R2, skal du åbne Hyper-V Manager. Klik på i Server Manager **roller > Hyper-V > Hyper-V Manager**.

1.  Højreklik på dit system node for at åbne genvejsmenuen i **Hyper-V Manager**, i ruden omfang, og klik derefter på **Ny** > **virtuelt**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Klik på **Næste**på siden **Oversigt** i guiden Ny virtuelt.

1.  Angiv et **navn** på siden **Angiv navn og en placering** til den virtuelle enhed. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Vælg billedtypen enhed, og klik derefter på **Næste**på siden **Angiv generering af** . Denne side vises ikke, hvis du bruger Windows Server 2008 R2.

    * Vælg **generering af 2** , hvis du har hentet et .vhdx billede til Windows Server 2012 eller nyere.
    * Vælg **generering af 1** , hvis du har hentet et .vhd billede til Windows Server 2008 R2 eller nyere.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  På siden **Tildel hukommelse** skal du angive en **Start hukommelse** af mindst **8192 MB**, ikke aktivere dynamiske hukommelse, og klik derefter på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Angiv den virtuelle parameter, der har forbindelse til internettet, og klik derefter på **Næste**på siden **Konfigurer netværk** .

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Vælg **Brug en eksisterende virtuel harddisk**, angive placeringen af det virtuelle enhed billede (.vhdx eller .vhd), og klik derefter på **Næste**på siden **Opret forbindelse virtuel harddisk** .

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Gennemse **Oversigt** , og klik derefter på **Udfør** for at oprette den virtuelle maskine. Men ikke kan springe frem endnu – du stadig har brug at tilføje nogle CPU-kerner og et andet drev. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Hvis du vil opfylder minimumkravene, skal du 4 kerner. Hvis du vil tilføje virtuelle processorer med systemet host markeret i vinduet **Hyper-V Manager** i højre rude under listen over **virtuelle maskiner**, Find den virtuelle maskine, du lige har oprettet. Markér og højreklik på navnet på computer, og vælg **Indstillinger**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Klik på **Processor**på siden **Indstillinger** i venstre rude. Angiv **antallet af virtuelle processorer** til 4 (eller mere) i højre rude. Klik på **Anvend**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Hvis du vil opfylder minimumkravene, skal du også tilføje en 500 GB virtuelle datadisk. På siden **Indstillinger** :

    1.  Vælg **SCSI-Controller**i venstre rude.
    2.  Vælg **Harddisk,** og klik på **Tilføj**i højre rude.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Vælg indstillingen **virtuel harddisk** , og klik på **Ny**på siden **harddisk** . Dette starter **Guiden på ny virtuelle harddisk**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Klik på **Næste**på siden **Oversigt** i guiden Ny virtuel harddisk.

1.  Acceptér standardindstillingen **VHDX** formatering på **siden Vælg Disk Format**. Klik på **Næste**. Du kan ikke se dette skærmbillede, hvis du kører Windows Server 2012 R2 eller Windows Server 2008 R2.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Angive virtuel harddisk type på **siden Vælg Disk Type**som **dynamisk udvidelse** (anbefales). Hvis du vælger **fast størrelse** disken, det fungerer også, men du skal muligvis vente i lang tid. Vi anbefaler, at du ikke bruge indstillingen **Differencing** . Klik på **Næste**. Bemærk, at **dynamisk udvidelse** er standard i Windows Server 2012 R2 og Windows Server 2012. I Windows Server 2008 R2 er standard **fast størrelse**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Giv en **navn** samt **placering** (du kan gå til en) på siden **Angiv navn og en placering** til cd'en data. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Vælg indstillingen **Opret en ny tom virtuel harddisk** på siden **Konfigurer Disk** , og Angiv størrelsen som **500 GB** (eller mere). Mens 500 GB er kravet om mindste, kan du altid klargøre en større disk. Bemærk, at du ikke kan udvide eller formindske disken klargjort én gang. Gennemgå sektionen størrelseshåndtag i dokumentets [bedste fremgangsmåder](storsimple-ova-best-practices.md#configuration-best-practices) for flere oplysninger om størrelsen af disk at blive klargjort. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Gennemgå oplysningerne i harddisken virtuelle data på siden **Oversigt** , og hvis opfyldt, kan du klikke på **Udfør** for at oprette disken. Guiden lukkes, og en virtuel harddisk, føjes til din computer.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Du kommer tilbage til siden **Indstillinger** . Klik på **OK** for at lukke siden **Indstillinger** og vende tilbage til vinduet Hyper-V Manager.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Trin 3: Start den virtuelle enhed og få IP-adresse

Udfør følgende trin for at starte enheden virtuelle og oprette forbindelse til den.

#### <a name="to-start-the-virtual-device"></a>Starte den virtuelle enhed

1.  Starte den virtuelle enhed.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Når enheden kører, Vælg enheden, højreklikke og vælge **Opret forbindelse**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Du kan være nødvendigt at vente 5-10 minutter til enheden for at være klar. En statusmeddelelse vises på konsollen til at angive status. Når enheden er klar, skal du gå til **handling**. Tryk på `Ctrl + Alt + Delete` til at logge ind i den virtuelle enhed. Standardbrugeren, er *StorSimpleAdmin* og standardadgangskoden, er *Adgangskode1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Af sikkerhedsmæssige årsager udløber administratoradgangskode enhed ved den første log på. Du bliver bedt om at ændre adgangskoden.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Angiv en adgangskode, der indeholder mindst 8 tegn. Adgangskoden skal opfylde mindst 3 af 4 følgende krav: store bogstaver, små bogstaver, numeriske og særlige tegn. Skriv adgangskoden for at bekræfte den igen. Du får besked, at adgangskoden er ændret.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Når adgangskoden er ændret, kan du genstarte den virtuelle enhed. Vent på kameraet, for at starte.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    Windows PowerShell console på enheden vises sammen med en statuslinje.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Trin 6-8 gælder kun, når der startes op i et ikke DHCP-miljø. Hvis du er i et DHCP-miljø, Spring disse trin over, og gå til trin 9. Hvis du startet af din enhed ikke DHCP-miljø, får du vist følgende skærmbillede.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Nu skal du konfigurere netværket.

1.  Brug den `Get-HcsIpAddress` kommandoen til at få vist de netværksgrænseflader aktiveret på enheden virtuelle. Hvis din enhed har en enkelt netværksgrænseflade, der er aktiveret, er det standardnavn, der er tildelt til denne grænseflade `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Brug den `Set-HcsIpAddress` til at konfigurere netværket. Et eksempel er vist nedenfor:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Når installationen er fuldført, og enheden er startet op, vises enheden bannertekst. Skal du notere IP-adresse og den URL-adresse, der vises i feltet bannertekst, til styring af enheden. Du skal bruge denne IP-adresse til at oprette forbindelse til internettet brugergrænseflade af enheden virtuelle og fuldføre den lokale installation og registrering.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Valgfrit) Udføre dette trin kun, hvis du installerer enheden i Government skyen. Du kan nu aktivere tilstanden USA national FIPS Information Processing Standard () på din enhed. FIPS 140 standard definerer cryptographic algoritmer, der er godkendt til brug af os national government computersystemer til beskyttelse af følsomme data.
    1. Hvis du vil aktivere FIPS-tilstand, skal du køre følgende cmdlet:

        `Enter-HcsFIPSMode`

    2. Genstarte enheden, når du har aktiveret FIPS-tilstand, så de cryptographic valideringer træder i kraft.

        > [AZURE.NOTE] Du kan aktivere eller deaktivere FIPS-tilstand på din enhed. Skiftende enheden mellem FIPS og ikke FIPS tilstand understøttes ikke.

Hvis enheden ikke opfylder kravene mindste konfiguration, får du vist en fejlmeddelelse i banner teksten (vist nedenfor). Du skal ændre konfigurationen af enheden, så der er tilstrækkelig kapacitet til at opfylde minimumskravene. Du kan derefter genstarte og oprette forbindelse til enheden. Se de mindste konfigurationskrav i [trin 1: sikre, at host systemet opfylder minimumkravene virtuel enhed](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Hvis du står over for andre fejl under den indledende konfiguration ved hjælp af webdelen lokale brugergrænseflade, se i følgende arbejdsprocesser på [Administrer dine StorSimple virtuelle matrix ved hjælp af webdelen lokale brugergrænseflade](storsimple-ova-web-ui-admin.md).

-   Kør diagnosticering test foretage [fejlfinding i web Brugergrænsefladen installationsprogrammet](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Generer log pakke og få vist logfiler](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![videoikon](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **Video, der er tilgængelige**

Se videoen for at se, hvordan du kan klargøre en StorSimple virtuelle matrix i Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Næste trin

-   [Konfigurere din StorSimple virtuelle matrix som en filserver](storsimple-ova-deploy3-fs-setup.md)

-   [Konfigurere din StorSimple virtuelle matrixen som en iSCSI-server](storsimple-ova-deploy3-iscsi-setup.md)
