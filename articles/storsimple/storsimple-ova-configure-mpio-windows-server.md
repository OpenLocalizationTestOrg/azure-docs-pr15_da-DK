<properties 
   pageTitle="Konfigurere MPIO på værten StorSimple virtuelle matrix | Microsoft Azure"
   description="Beskriver, hvordan du konfigurerer Multipath i/o-(MPIO) til din StorSimple virtuelle matrix, der er forbundet til en vært, der kører Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Konfigurere Multipath I/O på Windows Server vært for den virtuelle StorSimple-matrix

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du installerer Multipath i/o-funktion (MPIO) på din Windows Server-vært, anvende bestemte konfigurationsindstillinger for kun StorSimple enheder og derefter kontrollere MPIO for StorSimple enheder. I procedure antages det, at din StorSimple 1200 virtuelle matrix med to netværksgrænseflader er forbundet til en Windows Server vært med to netværksgrænseflader. Oplysningerne i denne artikel gælder kun for den virtuelle matrix. Gå til [Konfigurere MPIO til StorSimple vært](storsimple-configure-mpio-windows-server.md)for oplysninger om StorSimple 8000 serie enheder. 

Funktionen MPIO i Windows Server hjælper med at opbygge høj tilgængelighed, fejlsikret storagekonfigurationer. MPIO bruger overflødige fysiske sti komponenter – kort, kabler og parametre – til at oprette logiske stier mellem serveren og storage-enhed. Hvis der er en komponentfejl, der forårsager en logisk sti mislykkes, bruger benytter flere stier logik en alternativ sti for I/O, så programmer kan stadig åbne deres data. Desuden afhængigt af din konfiguration kan MPIO også forbedre ydeevnen ved igen justering af belastning på tværs af alle disse stier. Se [Oversigt over MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO oversigt og funktioner")kan finde flere oplysninger.  

For høj-tilgængeligheden af din StorSimple løsning, kan du konfigurere MPIO på Windows Server værter har forbindelse til din StorSimple 1200 virtuelle matrix (også kendt som den lokale virtuelle enhed). Værtsservere kan derefter acceptere et hyperlink, et netværk eller en fejl i brugergrænseflade. 

Skal du følge disse trin for at konfigurere MPIO: 

- Forudsætninger for konfiguration

- Trin 1: Installer MPIO på værtsplaceringen for Windows Server

- Trin 2: Konfigurer MPIO for StorSimple enheder

- Trin 3: Indlæs StorSimple enheder på værten

Hver af ovenstående trin er beskrevet i følgende afsnit.


## <a name="prerequisites"></a>Forudsætninger

Dette afsnit indeholder oplysninger om konfiguration forudsætningerne til værten for Windows Server og din virtuelle matrix.

### <a name="on-windows-server-host"></a>På Windows Server vært

-  Sørg for, at din Windows Server-vært har 2 netværksgrænseflader aktiveret.


### <a name="on-storsimple-virtual-array"></a>På StorSimple virtuelle matrix

- Den virtuelle matrix skal konfigureres som en iSCSI-server. For at få mere for at vide, kan du se [konfigurere virtuelle matrix som en iSCSI-server](storsimple-ova-deploy3-iscsi-setup.md). En eller flere netværksgrænseflader skal være aktiveret i matrixen.   

- Netværksgrænseflader på din virtuelle matrix skal være tilgængelig fra værten for Windows Server.

- En eller flere enheder skal oprettes på din StorSimple virtuelle matrix. Se [tilføje en lydstyrken](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) på din StorSimple 1200 virtuelle matrix for at få mere for at vide. I denne procedure oprettede vi 3 enheder (2 lokalt fastgjort og 1 lagdelte lydstyrken som vist nedenfor) på den virtuelle matrix.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Hardwarekonfiguration til StorSimple virtuelle matrix

I nedenstående figur vises hardwarekonfiguration for høj tilgængelighed og justering af belastning benytter flere stier til Windows Server host og StorSimple virtuelle matrix, der bruges i denne procedure.  

![MPIO hardwarekonfiguration](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Som vist i den foregående figur:

- Din StorSimple virtuelle matrix klargjort på Hyper-V er en enkelt node aktive enhed konfigureret som en iSCSI-server.

- To virtuelt netværksgrænseflader er aktiveret på din matrix. På det lokale websted brugergrænseflade for din 1200 virtuelle matrix, kan du kontrollere, at to netværksgrænseflader er aktiveret ved at navigere til **Netværksindstillinger** , som vist nedenfor:

    ![Netværksgrænseflader aktiveret på 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Bemærk IPv4-adresserne for aktiveret netværksgrænseflader (Ethernet, Ethernet 2 som standard) og gemme til senere brug på værten.

- To netværksgrænseflader er aktiveret på din Windows Server-vært. Hvis de forbundne grænseflader til host og matrix, der er på det samme undernet, vil være der 4 stier tilgængelige. Det var tilfældet i denne procedure. Men hvis hver netværksgrænseflade på grænsefladen matrix og host er på et andet IP-undernet (og ikke distribueret), derefter kun 2 stier vil være tilgængelige.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Trin 1: Installer MPIO på værtsplaceringen for Windows Server

MPIO er en valgfri funktion på Windows Server, og den installeres ikke som standard. Det skal være installeret som en funktion via Server Manager. For at installere denne funktion på din Windows Server-vært, skal du udføre følgende procedure.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Trin 2: Konfigurer MPIO for StorSimple enheder

MPIO skal konfigureres til at identificere StorSimple enheder. For at konfigurere MPIO at genkende StorSimple enheder skal du udføre følgende trin.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Trin 3: Indlæs StorSimple enheder på værten

Når MPIO er konfigureret på Windows Server, diskenhed(er), der er oprettet på matrixen StorSimple kan være tilsluttet og kan derefter drage fordel af MPIO til redundans. Hvis du vil tilslutte en enhed, skal du udføre følgende trin.

#### <a name="to-mount-volumes-on-the-host"></a>At tilslutte enheder på værten

1. Åbn vinduet **iSCSI afsenderen egenskaber** på Windows Server-vært. Klik på **Server Manager > Dashboard > Funktioner > iSCSI afsenderen**.
2. Klik på fanen registrering i dialogboksen **iSCSI afsenderen egenskaber** , og klik derefter på **Opdage Target Portal**.
3. Gør følgende i dialogboksen **Opdage Target portalen** :
    
    - Angiv IP-adressen på grænsefladen for det første aktiverede netværk på din StorSimple virtuelle matrix. Det vil sige **Ethernet**som standard. 
    - Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** .

    >[AZURE.IMPORTANT] **Hvis du bruger et privat netværk til iSCSI-forbindelser, Angiv port DATA, der er forbundet til det private netværk IP-adresse.**

4. Gentag trin 2-3 for en anden netværksgrænseflade (for eksempel Ethernet-2) på din matrix. 

5. Vælg fanen **destinationer** i dialogboksen **iSCSI afsenderen egenskaber** . For din virtuelle matrix, skal du se hver lydstyrken overflade som destination under **Registrerede destinationer**. I dette tilfælde ville være opdaget tre destinationer (svarende til tre enheder).

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Klik på **Opret forbindelse** for at oprette en iSCSI-session med din StorSimple matrix. En i dialogboksen **Opret forbindelse til destinationen** vises. Markér afkrydsningsfeltet **Aktiver med flere sti** . Klik på **Avanceret**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Gør følgende i dialogboksen **Avancerede indstillinger** :                                       
    -    Vælg **Microsoft iSCSI afsenderen**på rullelisten **Lokale Adapter** .
    -    Vælg IP-adressen på værten på rullelisten **Afsenderen IP-adresse** .
    -    Vælg IP-adresse på matrix grænseflade på rullelisten **Target Portal** IP-adresse.
    -    Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Klik på **Egenskaber**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Klik på **Tilføj Session**i dialogboksen **Egenskaber** .

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Markér afkrydsningsfeltet **Aktiver med flere stien** i dialogboksen **Opret forbindelse til destination** . Klik på **Avanceret**.
11. I dialogboksen **Avancerede indstillinger** :                                        
    -  Vælg Microsoft iSCSI afsenderen på rullelisten **lokale adapter** .
    -  Vælg den IP-adresse, der svarer til værten på rullelisten **Afsenderen IP-adresse** . I dette tilfælde opretter du to netværksgrænseflader på matrixen til en enkelt netværksgrænseflade på værten. Denne grænseflade er derfor på samme måde som knyttet til den første session.
    -  Vælg den anden data-grænseflade aktiveret på matrixen IP-adresse på rullelisten **Target Portal IP-adresse** .
    -  Klik på **OK** for at vende tilbage til dialogboksen iSCSI afsenderen egenskaber. Du har tilføjet en anden session til destinationen.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Når du har tilføjet de ønskede sessioner (stier), i dialogboksen **iSCSI afsenderen egenskaber** , vælg destinationen, og klik på **Egenskaber**. Bemærk fire sessionen id'er, der svarer til de mulige sti permutationer under fanen sessioner i dialogboksen **Egenskaber** . Annullere en session, Markér afkrydsningsfeltet ud for en session-id, og klik derefter på **Afbryd forbindelse**.
 
    - For at få vist enheder, der præsenteres i sessioner, Vælg fanen **enheder** . Hvis du vil konfigurere politikken MPIO for en bestemt enhed, skal du klikke på **MPIO**. Den **
    -  Oplysninger om** i dialogboksen vises. På den **MPIO** under fanen kan du vælge den rigtige **Indlæs Balance politik** Indstillinger. Du kan også få vist den **aktiv** eller **Standby ** sti type.

10. Gentag trin 8-11 for at tilføje yderligere sessioner (stier) til destinationen. Med to grænseflader på værten og to på den virtuelle matrix, kan du føje op til fire sessioner for hver destination. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Du skal gentage disse trin for hver lydstyrken (overflader som destination).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Åbn **Computer Management** ved at navigere til **Server Manager > Dashboard > Computer Management**. I venstre rude skal du klikke på **lagerplads > Disk Management**. Den diskenhed(er), der er oprettet på den virtuelle StorSimple-matrix, der er synlige for denne vært vises under **Administration af diskplads** som ny disken(e).

13. Initialisering på disk, og Opret en ny enhed. Vælg en allokering enhedsstørrelse (AUS) på 64 KB under formateringsprocessen. Gentag processen for alle de tilgængelige enheder.

    ![Diskadministration af](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Højreklik på **disken** under **Administration af Disk**, og vælg **Egenskaber**.

15. Klik på fanen **MPIO** i dialogboksen **Egenskaber for flere sti Disk enhed** .

    ![Egenskaber for disk](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Klik på **Detaljer** i afsnittet **DTU navn** og bekræfte, at parametrene er angivet til standardparametre. Standardparametre er:

    - Stien bekræfte periode = 30
    - Antal forsøg = 3
    - Fjernelse af PDO periode = 20
    - Interval mellem forsøg = 1
    - Stien bekræfte aktiveret = ikke er markeret.

    >[AZURE.NOTE] **Rediger ikke standardparametre.**


## <a name="next-steps"></a>Næste trin

Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple virtuelle matrix](storsimple-ova-manager-service-administration.md).
 
