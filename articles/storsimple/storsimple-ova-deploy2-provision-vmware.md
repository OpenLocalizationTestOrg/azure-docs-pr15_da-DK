<properties
   pageTitle="Installere StorSimple virtuelle matrix - klargøring i VMware"
   description="Dette andet selvstudium i StorSimple virtuelle matrix installation serie omfatter klargøring af en virtuel enhed i VMware."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Installere StorSimple virtuelle matrix - Klargør et virtuelt matrix i VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Oversigt 
Selvstudiet klargøring gælder for StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder eller StorSimple virtuelle enheder), der kører marts 2016 generelt tilgængelig (GA) version. Dette selvstudium beskrives, hvordan du klargør og oprette forbindelse til en StorSimple virtuelle matrix på et hostsystem kører VMware ESXi 5.5 og over. I denne artikel gælder for installation af StorSimple virtuelle matrixer i Azure klassisk portal samt Microsoft Azure Government Cloud.

Du skal have administratorrettigheder for at blive klargjort og oprette forbindelse til en virtuel enhed. Klargøring og indledende konfigurationen kan tage omkring 10 minutter for at gennemføre.


## <a name="provisioning-prerequisites"></a>Klargøring af forudsætninger

Her finder du forudsætningerne til at klargøre en virtuel enhed på et hostsystem kører VMware ESXi 5.5 og over.

### <a name="for-the-storsimple-manager-service"></a>For tjenesten StorSimple Manager

Før du begynder, skal du kontrollere, som:

-   Du har gennemført alle trin i [forberede portal til StorSimple virtuelle matrix](storsimple-ova-deploy1-portal-prep.md).

-   Du har hentet virtuel enhed billedet til VMware fra Azure-portalen. Du kan finde flere oplysninger, se [trin 3: hente virtuel enhed billedet](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Til den virtuelle StorSimple-enhed 

Før du installerer en virtuel enhed, skal du kontrollere, som:

-   Du har adgang til et hostsystem kører Hyper-V (2008 R2 eller nyere), der kan være brugt en bestemmelse en enhed.

-   Host systemet er kan du bruge følgende ressourcer for at klargøre enheden virtuelle:

    -   Et minimum af 4 kerner.

    -   Mindst 8 GB RAM.

    -   Ét netværk brugergrænseflade.

    -   En 500 GB Virtuel disk for systemdata.

### <a name="for-the-network-in-datacenter"></a>For netværket i datacenter 

Før du begynder, skal du kontrollere, som:

-   Du har gennemgået netværk kravene til at installere en virtuel enhed StorSimple og konfigureret datacenter netværket i overensstemmelse med kravene. Få mere at vide under [StorSimple virtuelle matrix systemkrav](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Trinvise klargøring 

Hvis du vil klargøre og oprette forbindelse til en virtuel enhed, skal du udføre følgende trin:

1.  Sørg for, at host systemet har tilstrækkelige ressourcer til kravene mindste virtuel enhed.

2.  Klargøre en virtuel enhed i din hypervisor.

3.  Start den virtuelle enhed og hente IP-adresse.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Trin 1: Sikre hostsystem opfylder minimumkravene virtuel enhed

Hvis du vil oprette en virtuel enhed, skal du:

-   Adgang til et hostsystem kører VMware ESXi Server 5.5 og over.

-   VMware vSphere-klienten på computeren til at administrere værten ESXi.

    -   Et minimum af 4 kerner.

    -   Mindst 8 GB RAM.

    -   Et netværkskort er tilsluttet netværket stand til at dirigere trafik til internettet. Den mindste internetbåndbredde skal være 5 Mbps tillade optimal fungerer på enheden.

    -   En 500 GB Virtuel disk til data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Trin 2: Klargøring af en virtuel enhed i hypervisor

Udfør følgende trin for at klargøre en virtuel enhed i din hypervisor.

1.  Kopiere billedet virtuel enhed på systemet. Dette er det billede, du har hentet via Azure klassisk portalen. 
    1.  Sørg for, at dette er den nyeste billedfil, du har hentet. Hvis du har hentet billedet tidligere, kan du hente det igen for at sikre, at du har den seneste billede. Seneste billedet har to filer (i stedet for én).
    2.  Noter til den placering, hvor du kopierede billedet, som du vil bruge dette senere i proceduren.

2.  Log på ESXi serveren ved hjælp af vSphere-klienten. Du skal have administratorrettigheder for at oprette en virtuel maskine.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Vælg den ESXi Server i vSphere-klienten, i sektionen lager i venstre rude.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Du først overfører VMDK til ESXi-serveren. Gå til fanen **konfiguration** i højre rude. Vælg **lagerplads**under **Hardware**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Vælg den datastore, hvor du vil overføre VMDK i højre rude under **Datastores**. Datastore skal have nok ledig plads om en OS og data disk.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Højreklik, og vælg **Gennemse Datastore**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Der vises en **Datastore Browser** -vindue.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  I værktøjslinjen klikke på ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) ikon for at oprette en ny mappe. Angiv navnet på mappen, og notere den. Du skal bruge denne mappenavn senere, når du opretter en virtuel maskine (anbefales bedste fremgangsmåde). Klik på **OK**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  Den nye mappe vises i venstre rude af **Datastore Browser**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Klik på ikonet Overfør ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) , og vælg **Overfør fil**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Nu skal du gennemse og peg på de VMDK filer, du har hentet. Der vil være to filer. Vælg en fil.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Klik på **Åbn**. Dette starter nu overførslen af VMDK filerne på den angivne datastore. Det kan tage flere minutter, før den fil.


1.  Når overførslen er fuldført, vises filen i datastore i den mappe, du har oprettet. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Du skal nu for at overføre den anden VMDK-fil til den samme datastore.

1.  Gå tilbage til vinduet vSphere klient. Højreklik med ESXi server er markeret, og vælg **nye virtuelle maskine**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Der vises et **oprette nye virtuelle maskine** vindue. Vælg indstillingen **brugerdefineret** på siden **konfiguration** . Klik på **Næste**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Angiv navnet på din virtuelle maskine, på siden **navn og placering** . Dette navn skal svare til navnet på mappen (anbefalede fremgangsmåde) du har angivet tidligere i trin 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Vælg en datastore, du vil bruge til at klargøre din VM på siden **lagerplads** .

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Vælg på siden **Virtual Machine Version** **virtuelt Version: 8**. Bemærk, at versioner 8-11 alle understøttes.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Vælg **Gæst operativsystem** som **Windows**på siden **operativsystem, som gæst** . Vælg **Microsoft Windows Server 2012 (64-bit)** **Version**, på rullelisten.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  På siden **CPU'er** du justere **antallet af virtuelle sockets** og **antal kerner per virtuelle socket** , så det **samlede antal kerner** er 4 (eller mere). Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  På siden **hukommelse** angive 8 GB (eller mere) RAM. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Angiv antallet af netværksgrænseflader på siden **netværk** . Kravet om mindste er et netværkskort.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  På siden **SCSI-Controller** skal du acceptere standardindstillingen **LSI logik SAS-controller**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  På siden **Vælg en Disk,** skal du vælge **Brug en eksisterende virtuel disk**. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Klik på **Gennemse**under **Disk filsti**, på siden **Vælg eksisterende Disk** . Dette åbner en dialogboks med en **Gennemse Datastores** . Gå til det sted, hvor du har overført VMDK. Du får nu vist kun én fil af DataStore, som er blevet flettet de to filer, du har overført. Vælg fil, og klik på **OK**. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Acceptere standardindstillingen på siden **Avancerede indstillinger** , og klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Gennemse alle de indstillinger, der er knyttet til den nye virtuelle maskine, på siden **klar til afsluttet** . Se **redigere indstillingerne virtuelt før den blev fuldført**. Klik på **Fortsæt**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Find enhedens hardware i fanen **Hardware** på siden **Egenskaber for virtuelle maskiner** . Vælg **ny harddisk**. Klik på **Tilføj**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Dette viser vinduet **Tilføj Hardware** . Vælg **harddisk** siden **Enhedstype** under **Vælg typen enhed, du vil tilføje**, og klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Vælg **Opret en ny virtuelle disk**på siden **Vælg en Disk** . Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Ændre **Diskstørrelse** til 500 GB (eller mere) på siden **Opret en Disk** . Vælg **Tynd klargøring**under **Klargøring af Disk**. Klik på **Næste**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Acceptere standardindstillingen på siden **Avancerede indstillinger** .

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Gennemgå disk indstillingerne på siden **klar til afsluttet** . Klik på **Udfør**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Du kan nu tilbage til siden virtuelt egenskaber. En ny harddisk er føjet til din virtuelle maskine. Klik på **Udfør**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Med din virtuelle maskine, der er valgt i højre rude, gå til fanen **Oversigt** . Gennemgå indstillingerne for din virtuelle maskine.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Din virtuelle maskine er nu klargjort. Næste trin er at hente IP-adresse og power på denne computer.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Trin 3: Start den virtuelle enhed og få IP-adresse

Udfør følgende trin for at starte enheden virtuelle og oprette forbindelse til den.

#### <a name="to-start-the-virtual-device"></a>Starte den virtuelle enhed

1.  Starte den virtuelle enhed. Vælg din enhed i vSphere Konfigurationsstyring, i venstre rude, og højreklik for at få vist genvejsmenuen. Vælg **Power** , og vælg derefter **Power på**. Dette bør Tænd din virtuelle maskine. Du kan få vist status i ruden nederst **Seneste opgaver** af vSphere-klienten.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Konfigurationsopgaver tager et par minutter at gennemføre. Når enheden kører, kan du gå til fanen **konsol** . Send Ctrl + Alt + Delete for at logge på enheden. Du kan alternativt, Placer markøren i vinduet console og trykke på Ctrl + Alt + Insert. Standardbrugeren, er *StorSimpleAdmin* og standardadgangskoden, er *Adgangskode1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Af sikkerhedsmæssige årsager udløber administratoradgangskode enhed ved den første log på. Du bliver bedt om at ændre adgangskoden.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Angiv en adgangskode, der indeholder mindst 8 tegn. Adgangskoden skal indeholde 3 af 4 af disse krav: store bogstaver, små bogstaver, numeriske og særlige tegn. Skriv adgangskoden for at bekræfte den igen. Du får besked, at adgangskoden er ændret.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Når adgangskoden er ændret, kan den virtuelle enhed genstart. Vent på Genstart for at fuldføre. Windows PowerShell console på enheden vises muligvis sammen med en statuslinje.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Trin 6-8 gælder kun, når der startes op i et ikke DHCP-miljø. Hvis du er i et DHCP-miljø, Spring disse trin over, og gå til trin 9. Hvis du startet af din enhed ikke DHCP-miljø, får du vist følgende skærmbillede. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Nu skal du konfigurere netværket.

1.  Brug den `Get-HcsIpAddress` kommandoen til at få vist de netværksgrænseflader aktiveret på enheden virtuelle. Hvis din enhed har en enkelt netværksgrænseflade, der er aktiveret, er det standardnavn, der er tildelt til denne grænseflade `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Brug den `Set-HcsIpAddress` til at konfigurere netværket. Et eksempel er vist nedenfor:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Når installationen er fuldført, og enheden er startet op, vises enheden bannertekst. Skal du notere IP-adresse og den URL-adresse, der vises i feltet bannertekst, til styring af enheden. Du skal bruge denne IP-adresse til at oprette forbindelse til internettet brugergrænseflade af enheden virtuelle og fuldføre den lokale installation og registrering.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Valgfrit) Udføre dette trin kun, hvis du installerer enheden i Government skyen. Du kan nu aktivere tilstanden USA national FIPS Information Processing Standard () på din enhed. FIPS 140 standard definerer cryptographic algoritmer, der er godkendt til brug af os national government computersystemer til beskyttelse af følsomme data.
    1. Hvis du vil aktivere FIPS-tilstand, skal du køre følgende cmdlet:
        
        `Enter-HcsFIPSMode`

    2. Genstarte enheden, når du har aktiveret FIPS-tilstand, så de cryptographic valideringer træder i kraft.

        > [AZURE.NOTE] Du kan aktivere eller deaktivere FIPS-tilstand på din enhed. Skiftende enheden mellem FIPS og ikke FIPS tilstand understøttes ikke.


Hvis enheden ikke opfylder kravene mindste konfiguration, får du vist en fejlmeddelelse i banner teksten (vist nedenfor). Du skal ændre konfigurationen af enheden, så der er tilstrækkelig kapacitet til at opfylde minimumskravene. Du kan derefter genstarte og oprette forbindelse til enheden. Se de mindste konfigurationskrav i [trin 1: sikre, at host systemet opfylder minimumkravene virtuel enhed](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Hvis du står over for andre fejl under den indledende konfiguration ved hjælp af webdelen lokale brugergrænseflade, se i følgende arbejdsprocesser på [Administrer dine StorSimple virtuelle matrix ved hjælp af webdelen lokale brugergrænseflade](storsimple-ova-web-ui-admin.md).

-   Kør diagnosticering test foretage [fejlfinding i web Brugergrænsefladen installationsprogrammet](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Generer log pakke og få vist logfilerne](storsimple-ova-web-ui-admin.md#generate-a-log-package)..

## <a name="next-steps"></a>Næste trin

-   [Konfigurere din StorSimple virtuelle matrix som en filserver](storsimple-ova-deploy3-fs-setup.md)

-   [Konfigurere din StorSimple virtuelle matrixen som en iSCSI-server](storsimple-ova-deploy3-iscsi-setup.md)

