<properties 
   pageTitle="Konfiguration af StorSimple virtuelle matrix iSCSI server | Microsoft Azure"
   description="Beskriver, hvordan du kan udføre indledende installation, registrere StorSimple iSCSI-serveren og opsætningen af enhed."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Installere StorSimple virtuelle matrix – konfigurere enheden virtuelle som en iSCSI-server

![iSCSI konfiguration procesforløb](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Oversigt

Selvstudiet installation gælder for Microsoft Azure StorSimple virtuelle matrix (også kaldet StorSimple lokale virtuel enhed eller den virtuelle enhed StorSimple), der kører marts 2016 generelt tilgængelig (GA) version. Dette selvstudium beskrives, hvordan du udfører indledende installation, registrere din StorSimple iSCSI-server, fuldføre konfigurationen enhed og derefter oprette, tilslutte, initialiseret og formatere enhederne på din StorSimple virtuel enhed iSCSI-server. StorSimple konfigurationsoplysninger i denne artikel gælder kun for StorSimple virtuelle matrixer. 

Fremgangsmåden tage her cirka 30 minutter til 1 hour at fuldføre. De oplysninger, der er udgivet i denne artikel gælder kun for StorSimple virtuelle matrixer.

## <a name="setup-prerequisites"></a>Forudsætninger for installation

Før du konfigurerer og konfigurere din StorSimple virtuelle enhed, skal du kontrollere, som:

- Du har klargjort en virtuel enhed og forbundet til netværket, som beskrevet i [Installere StorSimple virtuelle matrix - klargøring en virtuel matrix i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) eller [Installere StorSimple virtuelle matrix - klargøring en virtuel matrix i VMware](storsimple-ova-deploy2-provision-vmware.md).

- Du har tasten service registrering fra tjenesten StorSimple Manager, du har oprettet for at administrere StorSimple virtuelle enheder. Kan finde flere oplysninger under **trin 2: hente service registrering nøglen** i [Installere StorSimple virtuelle matrix - forberede på portalen](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Hvis dette er den anden eller efterfølgende virtuelle enhed, du vil registrere med en eksisterende StorSimple Manager-tjeneste, bør du have krypteringsnøglen service data. Denne tast blev oprettet, når den første enhed blev registreret med denne tjeneste. Hvis du har mistet denne tast, kan du se **få krypteringsnøglen service data** i [Brug Webbrugergrænsefladen til at administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Trinvise konfiguration 

Brug følgende trinvise instruktioner til at oprette og konfigurere enheden StorSimple virtuelle:

-  [Trin 1: Fuldføre lokale web Brugergrænsefladen konfigurationen og registrere din enhed](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Trin 2: Fuldføre konfigurationen påkrævet enhed](#step-2-complete-the-required-device-setup)
-  [Trin 3: Føje en volumenlicens](#step-3-add-a-volume)
-  [Trin 4: Tilslutte, initialiseret og formatere en volumenlicens](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Trin 1: Fuldføre lokale web Brugergrænsefladen konfigurationen og registrere din enhed 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Til at fuldføre konfigurationen og registrere enheden

1. Åbn et browservindue, og oprette forbindelse til internettet Brugergrænsefladen ved at skrive:

    `https://<ip-address of network interface>`

    Bruge forbindelse URL-adresse, der er noteret i det forrige trin. Du får vist en fejlmeddelelse, der giver dig besked om, at der er et problem med webstedets sikkerhedscertifikat. Klik på **Fortsæt for at denne webside**.

    ![sikkerhed certifikatfejl](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Log på web brugergrænseflade af enheden virtuelle som **StorSimpleAdmin**. Angiv den enhed administratoradgangskode, du har ændret i trin 3: starte den virtuelle enhed i [Installere StorSimple virtuelle matrix - klargøring en virtuel enhed i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) eller [Installere StorSimple virtuelle matrix - klargøring en virtuel enhed i VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Log på siden](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Du føres til **Startside** . Denne side beskrives de forskellige indstillinger, der kræves for at konfigurere og registrere den virtuelle enhed med tjenesten StorSimple Manager. Bemærk, at **netværksindstillinger**, **Web proxyindstillinger**og **tidsindstillinger** er valgfrit. Indstillingerne kun påkrævet er **Enhedsindstillinger** og **skyen indstillinger**.

    ![Startsiden](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. På siden **Indstillinger for netværk** under **netværksgrænseflader**konfigureres DATA 0 automatisk for dig. Hver netværksgrænseflade er angivet som standard at hente en IP-adresse automatisk (DHCP). Derfor tildeles en IP-adresse, undernet og gateway automatisk (for både IPv4 og IPv6).

    Når du planlægger at installere enheden som en iSCSI-server (til at klargøre Bloker lagerplads), anbefaler vi, at du deaktiverer indstillingen **få IP-adresse til automatisk** og konfigurerer statiske IP-adresser.

    ![Siden med indstillinger](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Hvis du har tilføjet mere end én netværksgrænseflade under klargøring af enheden, kan du konfigurere dem her. Bemærk du kan konfigurere dit netværk interface som IPv4 kun eller som både IPv4 og IPv6. IPv6-kun konfigurationer understøttes ikke.

5. DNS-servere, der kræves, fordi de bruges, når enheden forsøger at kommunikere med dine cloud storage tjenesteudbydere eller til at løse enheden ved navn, hvis den er konfigureret som en filserver. På siden **Indstillinger for netværk** under **DNS-servere**:

    1. En primære og sekundære DNS-server konfigureres automatisk. Hvis du vælger at konfigurere statiske IP-adresser, kan du angive DNS-servere. For høj tilgængelighed anbefaler vi, at du konfigurerer en primær og en sekundær DNS-server.

    2. Klik på **Anvend**. Dette vil anvende og validere indstillingerne.

6. På siden **Indstillinger for lydenhed** :

    1. Tildele et entydigt **navn** til din enhed. Dette navn kan være 1-15 tegn og kan indeholde bogstav, tal og bindestreger.

    2. Klik på ikonet **iSCSI server** ![iSCSI serverikonet](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) for **typen** enhed, du opretter. En iSCSI-server, kan du til levering blokere lager.

    3. Angiv, om du vil denne enhed skal være medlem af et domæne. Hvis enheden er en iSCSI-server, er tilslutter sig domænet valgfrit. Hvis du beslutter at ikke deltage i din iSCSI-server til et domæne, skal du klikke på **Anvend**, vent for at ændringerne kan anvendes og derefter gå videre til næste trin.

        Hvis du vil deltage i enheden til et domæne. Angiv et **domænenavn**, og klik derefter på **Anvend**.

        > [AZURE.NOTE] Hvis du deltager i din iSCSI-server til et domæne, og sikre, at din virtuelle matrix er i sin egen organisationsenhed (OU) til Microsoft Azure Active Directory og ingen objekter til gruppepolitik (GPO) anvendes til den.

    5. Der vises en dialogboks. Angiv dine legitimationsoplysninger til domænet i det angivne format. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Legitimationsoplysningerne til domænet kan bekræftes. Du får vist en fejlmeddelelse, hvis legitimationsoplysningerne, der er forkerte.

        ![legitimationsoplysninger](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Klik på **Anvend**. Dette vil anvende og validere enhedsindstillingerne.
 
7. (Valgfrit) konfigurere web proxy-serveren. Men konfiguration af web proxy er valgfrit, Vær opmærksom på, at hvis du bruger en webproxy, du kan kun konfigurere det her.

    ![konfigurere webtjenesteproxy](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    På siden **webtjenesteproxy** :

    1. Angive **URL-adresse proxy** i dette format: *http://host-IP adresse* eller *FDQN:Port nummer*. Bemærk, at HTTPS URL-adresser ikke understøttes.

    2. Angiv **godkendelse** som **grundlæggende** eller **ingen**.

    3. Hvis du bruger godkendelse, skal du også angive et **brugernavn** og **adgangskode**.

    4. Klik på **Anvend**. Dette vil validere og anvende konfigurerede web proxyindstillinger.
 
8. (Valgfrit) konfigurere indstillingerne for enheden, som tidszone og de primære og sekundære NTP-servere. NTP-servere er nødvendige, fordi din enhed skal synkronisere tid, så den kan godkende med din skyen tjenesteudbydere.

    ![Tidsindstillinger](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    På siden **Indstillinger for klokkeslæt** :

    1. Vælg den **tidszone** , der er baseret på den geografiske placering, hvor enheden blive implementeret på rullelisten. Standardtidszone for enheden er PST. Enheden anvender denne tidszone for alle planlagte operationer.

    2. Angive en **primær NTP-server** til din enhed, eller Accepter standardværdien for time.windows.com. Sørg for, at dit netværk tillader NTP-trafik til at overføre fra dit datacenter til internettet.

    3. Du kan også angive en **sekundær NTP-server** til din enhed.

    4. Klik på **Anvend**. Dette vil validere og anvende de konfigurerede tidsindstillinger.

9. Konfigurere skyen indstillingerne for din enhed. I dette trin skal du fuldføre konfigurationen lokal enhed og derefter registrere enheden med StorSimple Manager-tjenesten.

    1. Angive den **tjenestens registreringsnøgle** , du har fået i **trin 2: hente service registrering nøglen** i [Installere StorSimple virtuelle matrix - forberede på portalen](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Hvis dette ikke er den første enhed, du vil registrere med denne tjeneste, skal du angive **Service data krypteringsnøgle**. Denne nøgle er påkrævet med tjenesten registrering for at registrere flere enheder med tjenesten StorSimple Manager. Referere til [få krypteringsnøglen service data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) på din lokale web brugergrænseflade kan finde flere oplysninger.

    3. Klik på **Registrer**. Dette vil genstarte enheden. Du kan være nødvendigt at vente 2-3 minutter, før enheden er blevet registreret. Når du har genstartet enheden føres du til siden Log på.

       ![Registrere enhed](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Gå tilbage til portalen Azure klassisk. Kontrollér, at enheden er oprettet forbindelse til tjenesten ved at søge efter status på siden **enheder** . Enhedens status skal være **aktiv**.

    ![Enheder side](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Trin 2: Fuldføre konfigurationen påkrævet enhed

For at fuldføre enhedskonfigurationen af enheden StorSimple, skal du:

- Vælg en lagerplads konto skal knyttes til din enhed.

- Vælg krypteringsindstillinger for de data, der sendes til skyen.

Udfør følgende trin i Azure klassisk portalen for at fuldføre konfigurationen påkrævet enhed.

#### <a name="to-complete-the-minimum-device-setup"></a>At fuldføre konfigurationen mindste enhed

1. Vælg den enhed, du lige har oprettet, på siden **enheder** . Denne enhed skal vises som **aktiv**. Klik på pilen ud på enhedens navn og derefter klikke på **Hurtig Start**.

    ![Enheder side](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Klik på **Indstillinger for fuldført enhed** for at starte guiden Konfigurer enhed.

    ![Konfigurere guiden enhed](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. I guiden Konfigurer enhed på siden **Grundlæggende indstillinger for** at gøre følgende:

   1. Angiv en lagerplads konto skal bruges sammen med din enhed. Du kan vælge en eksisterende konto lagerplads fra på rullelisten i dette abonnement, eller du kan angive **Tilføj flere** for at vælge en konto fra et andet abonnement.

   2. Definere krypteringsindstillingerne for alle dataene på resten, der skal sendes til skyen. (StorSimple bruger AES 256 kryptering). For at kryptere dine data skal du markere afkrydsningsfeltet **Aktivér cloud storage kryptering** . Angiv en cloud storage kryptering, der indeholder 32 tegn. Angiv igen tasten for at bekræfte den.

   3. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Grundlæggende indstillinger](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Indstillingerne vil nu blive opdateret. Når indstillingerne er opdateret, knappen fuldført enhed konfiguration ikke tilgængelig. Du kommer tilbage til siden enhed **Hurtig Start** .                                                        

>[AZURE.NOTE]Du kan ændre alle andre enhedsindstillingerne når som helst ved at åbne siden **Konfigurer** .

## <a name="step-3-add-a-volume"></a>Trin 3: Føje en volumenlicens

Udfør følgende trin i Azure klassisk portalen for at oprette en disk.

#### <a name="to-create-a-volume"></a>At oprette en disk

1. På siden enhed **Hurtig Start** , klik på **Tilføj en enhed**. Dette starter guiden Tilføj et lydstyrken.

2. I guiden Tilføj et lydstyrken, under **Grundlæggende indstillinger**skal du gøre følgende:

    1. Angiv et entydigt navn til din enhed. Navnet skal være en streng, der indeholder 3 til 127 tegn.

    2. Angive en beskrivelse til lydstyrken. Beskrivelsen af hjælper med at identificere ejerne lydstyrken.

    3. Vælg typen brugen for lydstyrken. Typen brugen kan være **Tiered lydstyrken** eller **lokalt fastgjort lydstyrken.** (**Tiered lydstyrken** er standard). Vælg **lokalt fastgjort** **lydstyrken**for arbejdsbelastninger, som kræver lokale garantier, lav latenstider og højere ydeevne. Vælg **Tiered** **lydstyrken**for alle andre data.

        Et lokalt fastgjorte lydstyrken er thickly klargjort og sikrer, at de primære data i lydstyrken forbliver på enheden og ikke spilder til skyen. Hvis du opretter et lokalt fastgjorte lydstyrken, søger enheden efter ledig plads på de lokale lag til at klargøre en mængde den ønskede størrelse. Oprette et lokalt fastgjorte lydstyrken kan kræve går eksisterende data fra enheden til skyen, og den tid, det tager at oprette lydstyrken muligvis lang. Den samlede tid, afhænger af størrelsen på klargjort lydstyrken, tilgængelige netværksbåndbredde og data på din enhed.

        En lagdelte mængde på den anden side er tyndt klargjort og kan oprettes meget hurtigt. Når du opretter en lagdelte lydstyrken, cirka 10% af det tomme område er klargjort på lokale niveau og 90% af det tomme område er klargjort i skyen. Eksempelvis hvis du har klargjort en 1 TB lydstyrken, 100 GB vil være placeret i det lokale område og 900 GB vil blive brugt i skyen når data niveauer. Du kan ikke dette indebærer tur. er, at hvis du løber tør for den lokale plads på enheden, til at klargøre en lagdelte del (fordi 10% ikke er tilgængelig).

    4. Angiv den klargjorte kapacitet til din enhed. Bemærk, at den angivne kapacitet skal være mindre end den tilgængelige kapacitet. Hvis du opretter en lagdelte lydstyrken, skal størrelsen være mellem 500 GB og 5 TB. Angive en lydstyrken størrelse mellem 50 GB og 500 GB til et lokalt fastgjorte lydstyrken. Du kan bruge den tilgængelige kapacitet som en vejledning til klargøring af en volumenlicens. Hvis den tilgængelige lokale kapacitet er 0 GB, så du ikke vil have adgang til Klargør et lokalt fastgjorte eller en lagdelte lydstyrken.

        ![Grundlæggende indstillinger](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Tryk på pileikonet ![pileikonet](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) for at gå til den næste side.

3. Tilføje en ny access kontrolelement post (ACR) på siden **Yderligere indstillinger** :

    1. Angiv et **navn** til din ACR.

    2. Angiv iSCSI under **iSCSI afsenderen navn**, kvalificerede navn (IQN) på din Windows-vært. Hvis du ikke har IQN, gå til [tillæg A: få IQN af en Windows Server-vært](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Vi anbefaler, at du aktiverer en standard-sikkerhedskopi ved at markere afkrydsningsfeltet **Aktiver en sikkerhedskopi af standard for denne enhed** . Standard sikkerhedskopien opretter en politik, der kører på 22:30 hver dag (enhed tid) og opretter et skyen øjebliksbillede af denne enhed.

        ![flere indstillinger](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Dette starter lydstyrken oprettelse af jobbet. Du får vist meddelelsen status som følger.

        ![opgavefremdrift meddelelse](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Der oprettes en enhed med de angivne indstillinger. Som standard aktiveres overvågning og sikkerhedskopiering for lydstyrken.

    5. For at bekræfte, at lydstyrken er blevet oprettet, skal du gå til siden **enheder** . Du bør se lydstyrken er angivet.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Trin 4: Tilslutte, initialiseret og formatere en volumenlicens

Udfør følgende trin for at tilslutte, initialiseret og formatere dine StorSimple drev på en Windows Server-vært.

#### <a name="to-mount-initialize-and-format-a-volume"></a>For at tilslutte, initialiseret og formatere en volumenlicens

1. Start Microsoft iSCSI afsenderen.

2. Klik på **Oplev Portal**i vinduet **iSCSI afsenderen egenskaber** under fanen **registrering** .

    ![Opdag portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Leverer din iSCSI-aktiverede netværkskort IP-adresse i dialogboksen **Opdage destinationsadresse-portalen** , og klik derefter på **OK**.

    ![IP-adresse](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Find **Discovered destinationer**i vinduet **iSCSI afsenderen egenskaber** under fanen **destinationer** . (Hver lydstyrken bliver et opdaget mål). Enhedens status skal vises som **inaktiv**.

    ![opdaget destinationer](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Vælg en destination enhed, og klik derefter på **Opret forbindelse**. Når enheden er tilsluttet, ændre status skal til **tilsluttet**. (Du kan finde flere oplysninger om brug af Microsoft iSCSI afsenderen se [installation og konfiguration af Microsoft iSCSI afsenderen] [1].

    ![Vælg destination enhed](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Tryk på Windows-tasten + X på din Windows-vært, og klik derefter på **Kør**.

7. Skriv **Diskmgmt.msc**i dialogboksen **Kør** . Klik på **OK**, og dialogboksen **Disk Management** vises. Højre rude, vises enhederne på din vært.

8. I vinduet **Diskadministration** vises de aktiverede enheder, som vist i nedenstående illustration. Højreklik på det opdagede drev (Klik på disknavnet på), og klik derefter på **Online**.

    ![Diskadministration af](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Højreklik, og vælg **Initialiseret Disk**.

    ![Initialisering disk 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Vælg disk til initialiseret i dialogboksen, og klik derefter på **OK**.

    ![Initialisering disk 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Guiden Ny enkel lydstyrken starter. Vælg en diskstørrelse, og klik derefter på **Næste**.

    ![Guiden Nyt lydstyrken 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Tildele et drev brev til lydstyrken, og klik derefter på **Næste**.

    ![Guiden Nyt lydstyrken 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Angiv parametrene for at formatere lydstyrken. **På Windows Server understøttes kun NTFS.** Angiv AUS til 64 KB. Angive en etiket til din enhed. Det er en anbefalede bedste praksis for dette navn skal være identisk med navnet lydstyrken du angav på enheden StorSimple virtuelle. Klik på **Næste**.

    ![Guiden Nyt lydstyrken 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Kontrollér værdierne til din enhed, og klik derefter på **Udfør**.

    ![Guiden Nyt lydstyrken 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Enhederne vises som **Online** på siden **Disk Management** .

    ![enheder online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Næste trin

Lær at bruge webdelen lokale brugergrænseflade til at [administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Tillæg A: få IQN af en Windows Server-vært

Udfør følgende trin for at få iSCSI kvalificerede navn (IQN) på en Windows-vært, der kører Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>At få IQN af en Windows-vært

1. Start Microsoft iSCSI afsenderen på din Windows-vært.

2. Vælg og kopierer strengen i feltet **Afsender navn** i vinduet **iSCSI afsenderen egenskaber** under fanen **konfiguration** .

    ![iSCSI afsenderen egenskaber](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Gem denne streng.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



