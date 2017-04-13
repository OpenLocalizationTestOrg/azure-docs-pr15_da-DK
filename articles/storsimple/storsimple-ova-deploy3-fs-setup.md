<properties
   pageTitle="Installere StorSimple virtuelle matrix 3 - konfigurere den virtuelle enhed som filserver"
   description="Selvstudiet tredje i StorSimple virtuelle matrix installation får du konfigurerer en virtuel enhed som filserver."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Installere StorSimple virtuelle matrix - sæt op som filserver

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduktion 

I denne artikel gælder for Microsoft Azure StorSimple virtuelle matrix (også kaldet StorSimple lokale virtuel enhed eller StorSimple virtuel enhed), der kører marts 2016 generelt tilgængelig (GA) version. I denne artikel beskrives, hvordan du udfører indledende installation, registrere din StorSimple filserver, fuldføre konfigurationen enhed og opretter og oprette forbindelse til små og mellemstore virksomheder aktier. Dette er den sidste artikel i serien i installation selvstudier, der kræves for at installere helt din virtuelle matrix som en filserver eller en iSCSI-server.

Installation og konfiguration processen kan tage omkring 10 minutter for at gennemføre.


## <a name="setup-prerequisites"></a>Forudsætninger for installation

Før du konfigurerer og konfigurere din virtuelle StorSimple-enhed, skal du kontrollere, som:

-   Du har klargjort en virtuel enhed og tilsluttet som beskrevet i [klargøring en StorSimple virtuelle matrix i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) eller [klargøring en StorSimple virtuelle matrix i VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Du har tasten service registrering fra tjenesten StorSimple Manager, du har oprettet for at administrere StorSimple virtuelle enheder. Kan finde flere oplysninger under [trin 2: hente service registrering nøglen](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) til StorSimple virtuelle matrix.

-   Hvis dette er den anden eller efterfølgende virtuelle enhed, du vil registrere med en eksisterende StorSimple Manager-tjeneste, bør du have krypteringsnøglen service data. Denne tast blev oprettet, når den første enhed blev registreret med denne tjeneste. Hvis du har mistet denne tast, du se [få krypteringsnøglen service data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) til din StorSimple virtuelle matrix.

## <a name="step-by-step-setup"></a>Trinvise konfiguration

Brug følgende trinvise instruktioner til at oprette og konfigurere enheden StorSimple virtuelle.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Trin 1: Fuldføre lokale web Brugergrænsefladen konfigurationen og registrere din enhed 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Til at fuldføre konfigurationen og registrere enheden

1.  Åbn et browservindue, og oprette forbindelse til den lokale web brugergrænseflade. Type: 

    `https://<ip-address of network interface>`

    Bruge forbindelse URL-adresse, der er noteret i det forrige trin. Du får vist en fejlmeddelelse om, at der er et problem med webstedets sikkerhedscertifikat. Klik på **Fortsæt for at denne webside**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Log på web brugergrænseflade af enheden virtuelle som **StorSimpleAdmin**. Angiv den enhed administratoradgangskode, du har ændret i trin 3: Start den virtuelle enhed i [klargøring en StorSimple virtuelle matrix i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) eller i [klargøring en StorSimple virtuelle matrix i VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Du føres til **Startside** . Denne side beskrives de forskellige indstillinger, der kræves for at konfigurere og registrere den virtuelle enhed med tjenesten StorSimple Manager. Bemærk, at **netværksindstillinger**, **Web proxyindstillinger**og **tidsindstillinger** er valgfrit. Indstillingerne kun påkrævet er **Enhedsindstillinger** og **skyen indstillinger**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  På siden **Indstillinger for netværk** under **netværksgrænseflader**konfigureres DATA 0 automatisk for dig. Hver netværksgrænseflade er angivet som standard at hente IP-adresse automatisk (DHCP). Det vil sige, en IP-adresse, undernet og gateway automatisk tildeles (for både IPv4 og IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Hvis du har tilføjet mere end én netværksgrænseflade under klargøring af enheden, kan du konfigurere dem her. Bemærk du kan konfigurere dit netværk interface som IPv4 kun eller som både IPv4 og IPv6. IPv6-kun konfigurationer understøttes ikke.

1.  DNS-servere, der kræves, fordi de bruges, når enheden forsøger at kommunikere med dine cloud storage tjenesteudbydere eller til at løse enheden af navne, når der er konfigureret som en filserver. På siden **Indstillinger for netværk** under **DNS-servere**:

    1.  En primære og sekundære DNS-server konfigureres automatisk. Hvis du vælger at konfigurere statiske IP-adresser, kan du angive DNS-servere. For høj tilgængelighed anbefaler vi, at du konfigurerer en primær og en sekundær DNS-server.

    2.  Klik på **Anvend**. Dette vil anvende og validere indstillingerne.

2.  På siden **Indstillinger for lydenhed** :

    1.  Tildele et entydigt **navn** til din enhed. Dette navn kan være 1-15 tegn og kan indeholde bogstav, tal og bindestreger.

    2.  Klik på ikonet **filserver** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) for **typen** enhed, du opretter. En filserver, så du kan oprette delte mapper.

    3.  Enheden er en filserver, skal du slutte enheden til et domæne. Angiv et **domænenavn**.

    1.  Klik på **Anvend**.

2.  Der vises en dialogboks. Angiv dine legitimationsoplysninger til domænet i det angivne format. Klik på ikonet Kontrollér. Legitimationsoplysningerne til domænet kan bekræftes. Du får vist en fejlmeddelelse, hvis legitimationsoplysningerne, der er forkerte.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Klik på **Anvend**. Dette vil anvende og validere enhedsindstillingerne.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Sørg for, at din virtuelle matrix er i sin egen organisationsenhed (OU) til Active Directory og ingen objekter til gruppepolitik (GPO) er anvendt på eller nedarvet. Gruppepolitik kan installere programmer som antivirussoftware på StorSimple virtuelle matrixen. Installation af yderligere software understøttes ikke og kan føre til beskadigelse af data. 

1.  (Valgfrit) konfigurere web proxy-serveren. Men konfiguration af web proxy er valgfrit, Vær opmærksom på, at hvis du bruger en webproxy, du kan kun konfigurere det her.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    På siden **webtjenesteproxy** :

    1.  Angive **URL-adresse proxy** i dette format: *http://&lt;host IP-adresse eller FDQN&gt;: portnummer*. Bemærk, at HTTPS URL-adresser ikke understøttes.

    2.  Angiv **godkendelse** som **grundlæggende** eller **ingen**.

    3.  Hvis ved hjælp af godkendelse, skal du også angive et **brugernavn** og **adgangskode**.

    4.  Klik på **Anvend**. Dette vil validere og anvende konfigurerede web proxyindstillinger.

1.  (Valgfrit) konfigurere indstillingerne for enheden, som tidszone og de primære og sekundære NTP-servere. NTP-servere er nødvendige, fordi din enhed skal synkronisere tid, så den kan godkende med din skyen tjenesteudbydere.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    På siden **Indstillinger for klokkeslæt** :

    1.  Vælg den **tidszone** , der er baseret på den geografiske placering, hvor enheden blive implementeret på rullelisten. Standardtidszone for enheden er PST. Enheden anvender denne tidszone for alle planlagte operationer.

    2.  Angive en **primær NTP-server** til din enhed, eller Accepter standardværdien for time.windows.com. Sørg for, at dit netværk tillader NTP-trafik til at overføre fra dit datacenter til internettet.

    3.  Du kan også angive en **sekundær NTP-server** til din enhed.

    4.  Klik på **Anvend**. Dette vil validere og anvende de konfigurerede tidsindstillinger.

1.  Konfigurere skyen indstillingerne for din enhed. I dette trin skal du fuldføre konfigurationen lokal enhed og derefter registrere enheden med StorSimple Manager-tjenesten.

    1.  Angive den **tjenestens registreringsnøgle** , du har fået i [trin 2: hente service registrering nøglen](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) til StorSimple virtuelle matrix.

    2.  Springe dette trin over, hvis dette er den første enhed registrering med denne tjeneste, og gå til næste trin. Hvis dette ikke er den første enhed, du vil registrere med denne tjeneste, skal du angive **Service data krypteringsnøgle**. Denne nøgle er påkrævet med tjenesten registrering for at registrere flere enheder med tjenesten StorSimple Manager. Se Få mere at vide for at få den [tjeneste data krypteringsnøgle](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) på dit lokale websted brugergrænseflade.

    3.  Klik på **Registrer**. Dette vil genstarte enheden. Du kan være nødvendigt at vente 2-3 minutter, før enheden er blevet registreret. Når du har genstartet enheden føres du til siden Log på.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Gå tilbage til portalen Azure klassisk. Kontrollér, at enheden er oprettet forbindelse til tjenesten ved at søge efter status på siden **enheder** . Enhedens status skal være **aktiv**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Trin 2: Fuldføre konfigurationen påkrævet enhed

For at fuldføre konfigurationen af enheden for enheden StorSimple, skal du:

-   Vælg en lagerplads konto skal knyttes til din enhed.

-   Vælg krypteringsindstillinger for de data, der sendes til skyen.

Udfør følgende trin i [Azure klassisk portal](https://manage.windowsazure.com/) for at fuldføre konfigurationen påkrævet enhed.

#### <a name="to-complete-the-minimum-device-setup"></a>At fuldføre konfigurationen mindste enhed

1.  Vælg den enhed, du lige har oprettet fra siden **enheder** . Denne enhed skal vises som **aktiv**. Klik på pilen mod enhedens navn og derefter klikke på **Hurtig Start**.

2.  Klik på **Indstillinger for fuldført enhed** for at starte guiden Konfigurer enhed.

3.  I guiden Konfigurer enhed på siden **Grundlæggende indstillinger** skal du gøre følgende:

    1.  Angiv en lagerplads konto skal bruges sammen med din enhed. Du kan vælge en eksisterende konto lager i dette abonnement på rullelisten eller angive **Tilføj flere** for at vælge en konto fra et andet abonnement.

    2.  Definere krypteringsindstillingerne for alle data-i-resten (AES-kryptering), der skal sendes til skyen. Markér kombinationsboksen til at **aktivere cloud storage krypteringsnøgle**for at kryptere dine data. Angiv en cloud storage kryptering, der indeholder 32 tegn. Angiv igen tasten for at bekræfte den. En 256-bit AES-nøgle skal bruges sammen med den brugerdefinerede nøgle til kryptering.

    3.  Klik på ikonet Kontrollér ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Indstillingerne vil nu blive opdateret. Når indstillingerne er opdateret, vil knappen fuldført enhed opsætning være nedtonet. Du kommer tilbage til siden enhed **Hurtig Start** .

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Du kan ændre alle andre enhedsindstillingerne når som helst ved at åbne siden **Konfigurer** .

## <a name="step-3-add-a-share"></a>Trin 3: Føje en del

Udfør følgende trin i [Azure klassisk portal](https://manage.windowsazure.com/) for at oprette en del.

#### <a name="to-create-a-share"></a>Oprette en del

1.  På siden enhed **Hurtig Start** , klik på **Tilføj en del**. Dette starter guiden Tilføj et del.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Gør følgende på siden **Grundlæggende indstillinger** :

    1.  Angiv et entydigt navn til dit share. Navnet skal være en streng, der indeholder 3 til 127 tegn.

    2.  (Valgfrit) Angive en beskrivelse til dele. Beskrivelsen af hjælper med at identificere del ejere.

    3.  Vælg typen brugen for del. Typen brugen kan være **Tiered** eller **lokalt fastgjort**, med lagdelte som standard. Vælg et **lokalt fastgjort** share for arbejdsbelastninger, som kræver lokale garantier, lav latenstider og højere ydeevne. Vælg en **Tiered** del for alle andre data.

    Et lokalt fastgjorte del er thickly klargjort og sikrer, at de primære data delt forbliver lokalt på enheden og ikke spilder til skyen. En lagdelte del er på den anden side tyndt klargjort. Når du opretter en lagdelte del, 10% af det tomme område er klargjort på lokale niveau og 90% af det tomme område er klargjort i skyen. Eksempelvis hvis du har klargjort en 1 TB lydstyrken, 100 GB vil være placeret i det lokale område og 900 GB vil blive brugt i skyen når data niveauer. Det betyder også, hvis du løber tør for alle de lokale plads på enheden, kan du klargøre en lagdelte del.

1.  Angiv den klargjorte kapacitet for dit share. Bemærk, at den angivne kapacitet skal være mindre end den tilgængelige kapacitet. Hvis du bruger en lagdelte del, skal del størrelse være mellem 500 GB og 20 TB. Til et lokalt fastgjorte del skal du angive en del størrelse mellem 50 GB og 2 TB. Bruge den tilgængelige kapacitet som en vejledning til at klargøre en del. Hvis den tilgængelige lokale kapacitet er 0 GB, derefter kan du ikke du klargør lokale eller lagdelte aktier.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Tryk på pileikonet ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) at gå til den næste side.

1.  Tildele tilladelser til brugeren eller gruppen, får adgang til denne del på siden **Yderligere indstillinger** . Angiv navnet på brugeren eller brugergruppen i *<john@contoso.com>* format. Vi anbefaler, at du bruger en brugergruppe (i stedet for en enkelt bruger) til at tillade administratortilladelser til at få adgang til disse aktier. Når du har tildelt tilladelser her, kan du derefter bruge Windows Stifinder til at ændre disse tilladelser.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Klik på ikonet Kontrollér ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Der oprettes en del med de angivne indstillinger. Som standard aktiveres overvågning og sikkerhedskopiering for del.

## <a name="step-4-connect-to-the-share"></a>Trin 4: Oprette forbindelse til del

Nu skal du oprette forbindelse til den share(s), du har oprettet i ovenstående trin. Udføre disse trin på din Windows Server-vært.

#### <a name="to-connect-to-the-share"></a>Oprette forbindelse til del

1.  Tryk på ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + R. I vinduet Kør skal du angive den * \\ * som stien, erstatte *server filnavn* med enhedens navn, som du har knyttet til din filserver. Klik på **OK**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Dette åbner op Explorer. Du bør nu kunne se de aktier, du har oprettet som mapper. Vælg, og dobbeltklik på en del (mappe) for at se indholdet.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Du kan nu føje filer til disse aktier og tage en sikkerhedskopi.

![videoikon](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **Video, der er tilgængelige**

Se videoen for at se, hvordan du kan konfigurere og registrere en StorSimple virtuelle matrix som en filserver.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Næste trin

Lær at bruge webdelen lokale brugergrænseflade til at [administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
