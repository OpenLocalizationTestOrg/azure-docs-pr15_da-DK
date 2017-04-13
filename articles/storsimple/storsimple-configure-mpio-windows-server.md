<properties 
   pageTitle="Konfigurere MPIO til enheden StorSimple | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du konfigurerer Multipath i/o-(MPIO) til enheden StorSimple har forbindelse til en vært, der kører Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurere Multipath I/O til enheden StorSimple

Microsoft indbygget understøttelse af funktionen Multipath i/o-(MPIO) i Windows Server til Hjælp build meget tilgængelig, fejlsikret SAN konfigurationer. MPIO bruger overflødige fysiske sti komponenter – kort, kabler og parametre – til at oprette logiske stier mellem serveren og storage-enhed. Hvis der er en komponentfejl, der forårsager en logisk sti mislykkes, bruger benytter flere stier logik en alternativ sti for I/O, så programmer kan stadig åbne deres data. Desuden afhængigt af din konfiguration kan MPIO også forbedre ydeevnen ved igen justering af belastning på tværs af alle disse stier. Se [Oversigt over MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO oversigt og funktioner")kan finde flere oplysninger.  

Høj-tilgængeligheden af din StorSimple løsning, skal MPIO konfigureres på enheden StorSimple. Når MPIO er installeret på din hostservere, der kører Windows Server 2012 R2, kan serverne derefter acceptere et hyperlink, et netværk eller en fejl i brugergrænseflade. 

MPIO er en valgfri funktion på Windows Server, og den installeres ikke som standard. Det skal være installeret som en funktion via Server Manager. Dette emne beskrives de trin, du skal følge for at installere og bruge funktionen MPIO på en vært, der kører Windows Server 2012 R2 og forbindelse til en StorSimple fysisk enhed.

>[AZURE.NOTE] **Denne fremgangsmåde gælder kun StorSimple 8000 ved serier. MPIO understøttes ikke i øjeblikket på en virtuel StorSimple-enhed.**

Skal du følge disse trin for at konfigurere MPIO på enheden StorSimple:

- Trin 1: Installer MPIO på værtsplaceringen for Windows Server

- Trin 2: Konfigurer MPIO for StorSimple enheder

- Trin 3: Indlæs StorSimple enheder på værten

- Trin 4: Konfigurere MPIO til høj tilgængelighed og justering af belastning

Hver af ovenstående trin er beskrevet i følgende afsnit.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Trin 1: Installer MPIO på værtsplaceringen for Windows Server

For at installere denne funktion på din Windows Server-vært, skal du udføre følgende procedure.

#### <a name="to-install-mpio-on-the-host"></a>Installere MPIO på værten

1. Åbn Server Manager på din Windows Server-vært. Som standard starter Server Manager, når et medlem af gruppen Administratorer logger på en computer, der kører Windows Server 2012 R2 eller Windows Server 2012. Hvis den Server Manager ikke allerede er åben, skal du klikke på **Start > Server Manager**.
![Server Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Klik på **Server Manager > Dashboard > Tilføj roller og funktioner**. Dette starter guiden **Tilføj roller og funktioner** .
![Tilføje funktioner guiden 1 og roller](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. I guiden **Tilføj roller og funktioner** skal du gøre følgende:

    - Klik på **Næste**på siden **Oversigt** .
    - Acceptér standardindstillingen af installationen af **rollebaseret eller funktion-baseret** på siden **Vælg installationstype** . Klik på **Næste**. ![Tilføje roller og funktioner guiden 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Vælg **Vælg en server fra puljen server**på siden **Vælg destinations-serveren** . Host serveren skal registreres automatisk. Klik på **Næste**.
    - Klik på **Næste**på siden **Vælg serverroller** .
    - Vælg **Multipath I/O**på siden **Vælg funktioner** , og klik på **Næste**. ![Tilføje funktioner guiden 5 og roller](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - På siden **Bekræft installationsindstillinger** bekræfte valget af og vælg derefter **genstarte destinationsserveren automatisk Hvis det er nødvendigt**, som vist nedenfor. Klik på **Installer**. ![Tilføje roller og funktioner guiden 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Du får besked, når installationen er fuldført. Klik på **Luk** for at lukke guiden. ![Tilføje roller og funktioner guiden 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Trin 2: Konfigurer MPIO for StorSimple enheder

MPIO skal konfigureres til at identificere StorSimple enheder. For at konfigurere MPIO at genkende StorSimple enheder skal du udføre følgende trin.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Sådan konfigureres MPIO for StorSimple enheder

1. Åbn **MPIO konfiguration**. Klik på **Server Manager > Dashboard > Funktioner > MPIO**.

2. Vælg fanen **Opdage med flere kurver** i dialogboksen **Egenskaber for MPIO** .

3. Vælg **Tilføj understøttelse af iSCSI-enheder**, og klik derefter på **Tilføj**.  
![Egenskaber for MPIO opleve flere stier](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Genstart serveren, når du bliver bedt om.
5. Klik på fanen **MPIO enheder** i dialogboksen **Egenskaber for MPIO** . Klik på **Tilføj**.
    </br>![MPIO egenskaber MPIO enheder](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. I dialogboksen **Tilføj MPIO Support** under **Enhedens Hardware-ID**, du angive din enhed serienummer. Du kan få serienummeret enhed ved at få adgang til din StorSimple Manager tjeneste og navigere til **enheder > Dashboard**. Serienummeret enhed vises i højre **Hurtig Glance** rude af dashboardet for enhed.
    </br>![Tilføje MPIO Support](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Genstart serveren, når du bliver bedt om.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Trin 3: Indlæs StorSimple enheder på værten

Når MPIO er konfigureret på Windows Server, kan være tilsluttet diskenhed(er), der er oprettet på StorSimple enheden, og kan derefter drage fordel af MPIO til redundans. Hvis du vil tilslutte en enhed, skal du udføre følgende trin.

#### <a name="to-mount-volumes-on-the-host"></a>At tilslutte enheder på værten

1. Åbn vinduet **iSCSI afsenderen egenskaber** på Windows Server-vært. Klik på **Server Manager > Dashboard > Funktioner > iSCSI afsenderen**.
2. Klik på fanen registrering i dialogboksen **iSCSI afsenderen egenskaber** , og klik derefter på **Opdage Target Portal**.
3. Gør følgende i dialogboksen **Opdage Target portalen** :
    
    - Angiv dataport af enheden StorSimple IP-adresse (for eksempel angive DATA 0).
    - Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** .

    >[AZURE.IMPORTANT] **Hvis du bruger et privat netværk til iSCSI-forbindelser, Angiv port DATA, der er forbundet til det private netværk IP-adresse.**

4. Gentag trin 2-3 for en anden netværksgrænseflade (for eksempel DATA 1) på din enhed. Husk på, at disse grænseflader skal være aktiveret for iSCSI. Hvis du vil vide mere om dette, skal du se [Rediger netværksgrænseflader](storsimple-modify-device-config.md#modify-network-interfaces).
5. Vælg fanen **destinationer** i dialogboksen **iSCSI afsenderen egenskaber** . Du bør se StorSimple enhed destinationen IQN under **Registrerede destinationer**.
 ![iSCSI afsenderen fanen Egenskaber for destinationer](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Klik på **Opret forbindelse** for at oprette en iSCSI-session med enheden StorSimple. En i dialogboksen **Opret forbindelse til destinationen** vises.

7. Markér afkrydsningsfeltet **Aktiver med flere stien** i dialogboksen **Opret forbindelse til destination** . Klik på **Avanceret**.

8. Gør følgende i dialogboksen **Avancerede indstillinger** :                                       
    -    Vælg **Microsoft iSCSI afsenderen**på rullelisten **Lokale Adapter** .
    -    Vælg IP-adressen på værten på rullelisten **Afsenderen IP-adresse** .
    -    Vælg IP af enhed brugergrænseflade på rullelisten **Target Portal** IP-adresse.
    -    Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** .

9. Klik på **Egenskaber**. Klik på **Tilføj Session**i dialogboksen **Egenskaber** .
10. Markér afkrydsningsfeltet **Aktiver med flere stien** i dialogboksen **Opret forbindelse til destination** . Klik på **Avanceret**.
11. I dialogboksen **Avancerede indstillinger** :                                        
    -  Vælg Microsoft iSCSI afsenderen på rullelisten **lokale adapter** .
    -  Vælg den IP-adresse, der svarer til værten på rullelisten **Afsenderen IP-adresse** . I dette tilfælde skal opretter du to netværksgrænseflader på enheden til en enkelt netværksgrænseflade på værten. Denne grænseflade er derfor på samme måde som knyttet til den første session.
    -  Vælg IP-adressen for den anden data-grænseflade aktiveret på enheden, på rullelisten **Target Portal IP-adresse** .
    -  Klik på **OK** for at vende tilbage til dialogboksen iSCSI afsenderen egenskaber. Du har tilføjet en anden session til destinationen.

12. Åbn **Computer Management** ved at navigere til **Server Manager > Dashboard > Computer Management**. I venstre rude skal du klikke på **lagerplads > Disk Management**. Den diskenhed(er), der er oprettet på StorSimple enhed, der er synlige for denne vært vises under **Administration af diskplads** som ny disken(e).

13. Initialisering på disk, og Opret en ny enhed. Markér en blokstørrelse på 64 KB under formateringsprocessen.
![Diskadministration af](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Højreklik på **disken** under **Administration af Disk**, og vælg **Egenskaber**.
15. I den Model, StorSimple ### **Med flere stier diskplads egenskaber for enheden** dialogboksen skal du klikke på fanen **MPIO** .
![StorSimple 8100 med flere stier Disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Klik på **Detaljer** i afsnittet **DTU navn** og bekræfte, at parametrene er angivet til standardparametre. Standardparametre er:

    - Stien bekræfte periode = 30
    - Antal forsøg = 3
    - Fjernelse af PDO periode = 20
    - Interval mellem forsøg = 1
    - Stien bekræfte aktiveret = ikke er markeret.


>[AZURE.NOTE] **Rediger ikke standardparametre.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Trin 4: Konfigurere MPIO til høj tilgængelighed og justering af belastning

For flere sti baseret på høj tilgængelighed og justering af belastning, skal flere sessioner føjes manuelt til erklære de forskellige stier, der er tilgængelige. Eksempelvis hvis værten har to grænseflader tilsluttet SAN og enheden har to grænseflader, der er forbundet til SAN, skal du fire sessioner, der er konfigureret med stort sti permutationer (kun to sessioner skal hvis hver DATA brugergrænsefladen og host interface er på et andet IP-undernet og ikke er distribueret).

>[AZURE.IMPORTANT] **Vi anbefaler, at du ikke blande 1 GbE og 10 GbE netværksgrænseflader. Hvis du bruger to netværksgrænseflader, skal begge grænseflader være den samme type.**

Følgende procedure beskriver, hvordan tilføje sessioner, når en StorSimple enhed med to netværksgrænseflader er tilsluttet en vært med to netværksgrænseflader.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurere MPIO til høj tilgængelighed og justering af belastning

1. Udføre en registrering af destinationen: Klik på **Oplev Portal**i dialogboksen **iSCSI afsenderen egenskaber** under fanen **registrering** .
2. Angiv IP-adressen på et af enhed netværk grænsefladerne i dialogboksen **Opret forbindelse til destination** .
3. Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** .

4. Vælg fanen **destinationer** i dialogboksen **iSCSI afsenderen egenskaber** , fremhæve opdaget destinationen, og klik derefter på **Opret forbindelse**. Dialogboksen **Opret forbindelse til destination** vises.

5. I dialogboksen **Opret forbindelse til destination** :
    
    - Lad være standard destinationen valgte indstilling for **Tilføj denne forbindelse** til listen over foretrukne destinationer. Dette vil gøre enheden automatisk forsøge at genstarte forbindelsen, hver gang dette computeren genstarter.
    - Markér afkrydsningsfeltet **Aktiver med flere sti** .
    - Klik på **Avanceret**.

6. I dialogboksen **Avancerede indstillinger** :
    - Vælg **Microsoft iSCSI afsenderen**på rullelisten **Lokale Adapter** .
    - Vælg IP-adressen på værten på rullelisten **Afsenderen IP-adresse** .
    - Vælg IP-adressen på grænsefladen data aktiveret på enheden på rullelisten **Target Portal IP-adresse** .
    - Klik på **OK** for at vende tilbage til dialogboksen iSCSI afsenderen egenskaber.

7. Klik på **Egenskaber**, og klik på **Tilføj Session**i dialogboksen **Egenskaber** .

8. Markér afkrydsningsfeltet **Aktiver med flere stien** i dialogboksen **Opret forbindelse til destinationen** , og klik derefter på **Avanceret**.

9. I dialogboksen **Avancerede indstillinger** :
    1. Vælg **Microsoft iSCSI afsenderen**på rullelisten **lokale adapter** .
    2. Vælg den IP-adresse, der svarer til den anden brugergrænseflade på værten på rullelisten **Afsenderen IP-adresse** .
    3. Vælg IP-adressen for den anden data-grænseflade aktiveret på enheden, på rullelisten **Target Portal IP-adresse** .
    4. Klik på **OK** for at vende tilbage til dialogboksen **iSCSI afsenderen egenskaber** . Du er nu tilføjet en anden session til destinationen.

10. Gentag trin 8-10 – Hvis du vil tilføje yderligere sessioner (stier) til destinationen. Du kan føje op til fire sessioner med to grænseflader på værten og to på enheden.

11. Når du har tilføjet de ønskede sessioner (stier), i dialogboksen **iSCSI afsenderen egenskaber** , vælg destinationen, og klik på **Egenskaber**. Bemærk fire sessionen id'er, der svarer til de mulige sti permutationer under fanen sessioner i dialogboksen **Egenskaber** . Annullere en session, Markér afkrydsningsfeltet ud for en session-id, og klik derefter på **Afbryd forbindelse**.

12. For at få vist enheder, der præsenteres i sessioner, Vælg fanen **enheder** . Hvis du vil konfigurere politikken MPIO for en bestemt enhed, skal du klikke på **MPIO**. Dialogboksen **Oplysninger om mobilenhed** vises. Du kan vælge de relevante **Indlæs Balance** -politikindstillinger under fanen **MPIO** . Du kan også få vist typen **aktiv** eller **Standby** sti.

## <a name="next-steps"></a>Næste trin

Lær mere om [ved hjælp af tjenesten StorSimple Manager for at ændre konfigurationen af StorSimple enhed](storsimple-modify-device-config.md).
 
