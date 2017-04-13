<properties 
   pageTitle="Automatisere DR til filshares på StorSimple ved hjælp af Azure websted genoprettelse | Microsoft Azure"
   description="I denne artikel beskrives de trin og bedste fremgangsmåder for oprettelse af en løsning til genoprettelse efter genoprettelse for filshares hostes på StorSimple lagerplads."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatiseret nedbrud-løsning med Azure gendannelse af websteder til filshares hostes på StorSimple

## <a name="overview"></a>Oversigt

Microsoft Azure StorSimple er en hybrid cloud storageløsning, der løser lede ustrukturerede data ofte tilknyttet filshares. StorSimple bruger skylagring som en udvidelse af de lokale løsning og automatisk niveauer data på tværs af lokale lager og skylagring. Integreret databeskyttelse, med lokale og snapshots i skyen, fjerner behovet for en udsigt lagerplads infrastruktur.

[Azure gendannelse af websteder](../site-recovery/site-recovery-overview.md) er en Azure-baseret tjeneste, som indeholder nedbrud gendannelse (DR) funktioner ved at orchestrating gentagelse, failover og gendannelse af virtuelle maskiner. Azure gendannelse af websteder understøtter et antal gentagelse teknologier konsekvent gentage, beskytte og problemfrit mislykkes over virtuelle computere og programmer til privat/offentlige eller hostet skyer.

Brug af Azure gendannelse af websteder, virtuelt gentagelse og StorSimple skyen snapshot-funktioner, kan du beskytte hel fil server-miljø. I tilfælde af en forstyrrelse, kan du bruge et enkelt klik til at hente dine filshares online i Azure på blot nogle få minutter.

I dette dokument forklares detaljerede oplysninger om, hvordan du kan oprette en løsning til genoprettelse efter genoprettelse til din filshares hostes på StorSimple lagring, og udføre planlagte, ikke-planlagt og teste failover ved hjælp af en plan for et enkelt klik. Det viser egentlig, hvordan du kan ændre gendannelse planlægge i din samling af legitimationsoplysninger Azure gendannelse af websteder for at aktivere StorSimple failover i nedbrud scenarier. Desuden beskriver det understøttede konfigurationer og forudsætninger. Dette dokument forudsætter, at du allerede kender, grundlæggende om gendannelse af Azure websteder og StorSimple arkitekturer.

## <a name="supported-azure-site-recovery-deployment-options"></a>Understøttede Azure gendannelse af websteder installationsindstillinger

Kunder kan installere filservere som fysiske servere eller virtuelle maskiner (FOS), der kører på Hyper-V eller VMware og derefter oprette filshares fra enheder udskåret tør for lagerplads StorSimple. Azure gendannelse af websteder kan beskytte både fysiske og virtuelle installationer til et sekundært websted eller til Azure. Dette dokument omhandler detaljerne for en DR-løsning med Azure som webstedet gendannelse for en filserver VM hostes på Hyper-V og med filshares på StorSimple lagerplads. Andre scenarier, hvor filserver VM er på en VMware VM eller en fysisk maskine kan implementeres på samme måde.

## <a name="prerequisites"></a>Forudsætninger

Implementere en løsning til et enkelt klik nedbrud genoprettelse, som bruger Azure gendannelse af websteder til filshares hostes på StorSimple lagerplads har følgende forudsætninger:

-   Lokale Windows Server 2012 R2 filserver VM hostes på Hyper-V eller VMware eller en fysisk maskine

-   StorSimple lagerplads enhed i det lokale miljø, der er registreret med Azure StorSimple manager

-   StorSimple skyen maskinen oprettet i Azure StorSimple manager (det kan være informeret lukning tilstand)

-   Filshares hostes på de enheder, der er konfigureret på StorSimple storage-enhed

-   [Gendannelse af websteder azure services samling](../site-recovery/site-recovery-vmm-to-vmm.md) oprettet i et Microsoft Azure-abonnement

Desuden, hvis Azure er webstedet gendannelse, køre [værktøj til Azure virtuelt parathed vurdering](http://azure.microsoft.com/downloads/vm-readiness-assessment/) på FOS at sikre, at de er kompatible med Azure FOS og Azure gendannelse af websteder.

For at undgå problemer (hvilket kan medføre højere omkostninger) Sørg for, at du opretter din StorSimple skyen maskinen, automatisering konto og lagerplads ventetid konti i samme område.

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivere DR StorSimple filshares  

Hver komponent i det lokale miljø, skal være beskyttet for at aktivere fuldført gentagelse og gendannelse. Dette afsnit beskrives, hvordan du:

-   Konfigurere Active Directory og DNS gentagelse (valgfrit)

-   Bruge Azure gendannelse af websteder til at aktivere beskyttelse af filserver VM

-   Aktivér beskyttelse af StorSimple enheder

-   Konfigurere netværket

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurere Active Directory og DNS gentagelse (valgfrit)

Hvis du vil beskytte de maskiner, der kører Active Directory og DNS, så de er tilgængelige på webstedet DR, skal du eksplicit beskytte dem (så fil-serverne er tilgængeligt efter fejl over med godkendelse). Der findes to anbefalede indstillinger, der er baseret på kompleksiteten af kundens lokalt miljø.

#### <a name="option-1"></a>Valgmulighed 1

Hvis kunden har et lille antal programmer, en enkelt domænecontrolleren for hele lokale websted og ned over hele webstedet, og klik derefter det anbefales at bruge gentagelse Azure gendannelse af websteder kan replikeres domænecontroller computer til en sekundær websted (dette er relevant for både-til-websted og websted til Azure).

#### <a name="option-2"></a>Mulighed 2

Hvis kunden har et stort antal programmer, der kører en Active Directory-område og over nogle programmer vil ned ad gangen og derefter anbefales til at konfigurere en ekstra domænenavn fra domænecontrolleren på webstedet DR (enten en sekundær websted eller i Azure).

Se [automatiseret DR løsning til Active Directory og DNS-ved hjælp af Azure gendannelse af websteder](../site-recovery/site-recovery-active-directory.md) for at få instruktioner når du foretager et domænenavn fra domænecontrolleren tilgængelig på webstedet DR. I resten af dette dokument antager vi domænecontrolleren ikke er tilgængelig på webstedet DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Bruge Azure gendannelse af websteder til at aktivere beskyttelse af filserver VM

Dette trin kræver, at du forbereder lokale filer server-miljø, oprette og forberede en samling af legitimationsoplysninger Azure gendannelse af websteder og aktivere Filbeskyttelse VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Sådan forbereder du dig lokale filer server-miljø

1.  Angiv **kontrol af brugerkonti** til **aldrig at give besked**. Dette er påkrævet, så du kan bruge Azure automatiseringsscripts til at oprette forbindelse iSCSI-destinationer efter fejl forfra ved at Azure gendannelse af websteder.

    1.  Tryk på Windows-tasten + Q og søge efter **kontrol af Brugerkonti**.

    2.  Vælg **Indstillinger for Skift kontrol af brugerkonti**.

    3.  Træk linjen til bunden mod **Aldrig besked**.

    4.  Klik på **OK** , og vælg derefter **Ja** når du bliver bedt om.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Installere VM Agent på hver af filserver FOS. Dette er påkrævet, så du kan køre Azure automatiseringsscripts på den mislykkedes over FOS.

    1.  [Hente agenten](http://aka.ms/vmagentwin) til `C:\\Users\\<username>\\Downloads`.

    2.  Åbn Windows PowerShell i Administrator-tilstand (Kør som Administrator), og angiv derefter følgende kommando for at gå til placeringen af overførslen:

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] Filnavnet kan blive ændret afhængigt af versionen.

1.  Klik på **Næste**.

2.  Acceptér **Vilkårene af aftalen** , og klik derefter på **Næste**.

3.  Klik på **Udfør**.


1.  Opret filshare på ved hjælp af enheder udskåret tør for lagerplads StorSimple. Se [Brug af tjenesten StorSimple Manager til at administrere enheder](storsimple-manage-volumes.md)kan finde flere oplysninger.

    1.  Tryk på Windows-tasten + Q og Søg efter **iSCSI**på din lokale FOS.

    2.  Vælg **iSCSI afsenderen**.

    3.  Vælg fanen **konfiguration** og kopiere og indsætte afsenderen navnet.

    4.  Log på [Azure klassisk portal](https://manage.windowsazure.com/).

    5.  Vælg fanen **StorSimple** , og vælg derefter tjenesten StorSimple Manager, der indeholder den fysiske enhed.

    6.  Oprette lydstyrken beholdere til opbevaring og derefter oprette diskenhed(er). (Disse enheder er filen share(s) på filserver FOS.) Kopiere og indsætte afsenderen navnet og give et passende navn på Access Control-poster, når du opretter enhederne.

    7.  Vælg **Konfigurer** fane og note ned IP-adressen på enheden.

    8.  Gå til **iSCSI afsenderen** igen på din lokale FOS og indtaste IP-adresse i sektionen Opret hurtig forbindelse. Klik på **Opret hurtig forbindelse** (enheden bør nu tilsluttet).

    9.  Åbn Azure Management-portalen, og vælg fanen **enheder og enheder** . Klik på **Konfigurer automatisk**. Den enhed, du lige har oprettet skal vises.

    10. Vælg fanen **enheder** i portalen, og vælg derefter **oprette en ny virtuel enhed.** (Denne virtuelle enhed vil blive brugt, hvis der opstår en failover). Den nye virtuelle enhed kan opbevares i offlinetilstand for at undgå ekstra omkostninger. Gå til afsnittet **virtuelle maskiner** på portalen for at gøre den virtuelle enhed offline, og lukke den.

    11. Gå tilbage til de lokale FOS og åbne Disk Management (Tryk på Windows-tasten + X, og vælg **Disk Management**).

    12. Du vil bemærke nogle ekstra diske (afhængigt af antallet enheder, du har oprettet). Højreklik på den første opgave, Vælg **Initialiseret Disk**, og vælg **OK**. Højreklik på **Unallocated** sektionen, vælge **Ny simpel lydstyrken**, tildele den til et drevbogstav og har afsluttet guiden.

    13. Gentag trin l om en alle disk. Du kan nu se alle diske på **Denne PC** i Windows Stifinder.

    14. Brug rollen File and lagerplads Services til at oprette filshares på disse enheder.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Oprette og forberede en samling af legitimationsoplysninger Azure gendannelse af websteder

Se [dokumentationen til Azure gendannelse af websteder](../site-recovery/site-recovery-hyper-v-site-to-azure.md) til Introduktion til Azure gendannelse af websteder, inden du beskytter filserver VM.

#### <a name="to-enable-protection"></a>Aktivere beskyttelse

1.  Afbryd iSCSI destination(er) fra de lokale FOS, du vil beskytte via Azure gendannelse af websteder:

    1.  Tryk på Windows-tasten + Q og søge efter **iSCSI**.

    2.  Vælg **konfigurere iSCSI afsenderen**.

    3.  Afbryde forbindelsen til StorSimple enheden, som du tidligere har forbindelse. Alternativt kan du skifte fra filserveren for et par minutter når du aktiverer beskyttelse.

    > [AZURE.NOTE] Dette medfører filshares er ikke tilgængelig i øjeblikket

1.  [Aktiver virtuelt beskyttelse](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) af filserver VM fra portalen Azure gendannelse af websteder.

2.  Når den indledende synkronisering begynder, kan du forbinde destinationen igen. Gå til iSCSI afsenderen, Vælg enheden, StorSimple, og klik på **Opret forbindelse**.

3.  Når synkroniseringen er fuldført, og status for VM er **beskyttet**, vælge VM, Vælg fanen **Konfigurer** og opdatere netværket VM i overensstemmelse hermed (dette er det netværk, mislykkedes over VM(s) skal være en del af). Hvis netværket ikke vises, betyder det, at Synkroniser stadig er foregår.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivér beskyttelse af StorSimple enheder

Hvis du ikke har valgt indstillingen **Aktiver en sikkerhedskopi af standard for denne enhed** for StorSimple enheder, gå til **Politikker for sikkerhedskopiering** i tjenesten StorSimple Manager, og oprette en passende sikkerhedskopiering politik for alle enheder. Vi anbefaler, at du angiver hyppigheden for sikkerhedskopier til gendannelse punkt formålet (frigivne Produktionsordre), som du gerne vil se for programmet.

### <a name="configure-the-network"></a>Konfigurere netværket

Konfigurere netværksindstillinger i Azure gendannelse af websteder til filserver VM, så VM netværkene er knyttet til den korrekte DR netværk efter failover.

Du kan vælge VM i **VMM skyen** eller **Gruppen beskyttelse** for at konfigurere netværksindstillinger, som vist i nedenstående illustration.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

Du kan oprette en plan for gendannelse i ASR automatisere failover af på filshares. Hvis der forekommer afbrydelser, kan du tage filshares i et par minutter med et enkelt klik. Hvis du vil aktivere denne automatisering, skal du en Azure automatisering konto.

#### <a name="to-create-the-account"></a>At oprette kontoen

1.  Gå til portalen Azure klassisk, og gå til afsnittet **automatisering** .

1.  Oprette en ny konto med automation. Beholde den i det samme geografisk/område, StorSimple skyen maskinen og lagerplads konti blev oprettet.

2.  Klik på **Ny** &gt; **App Services** &gt; **automatisering** &gt; **Runbook** &gt; **Fra galleriet** for at importere alle de nødvendige runbooks til automatisering konto.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Tilføj følgende runbooks fra ruden **Nedbrud** i galleriet:

    -   Skifte over StorSimple lydstyrken objektbeholdere

    -   Rydde op i StorSimple enheder efter Test Failover (TFO)

    -   Tilslutte enheder på StorSimple enhed efter failover

    -   Starte StorSimple virtuelle anvendelse

    -   Fjerne brugerdefineret script filtypenavn i Azure VM

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publicere alle scripts ved at vælge runbook kontoen automatisering og gå til fanen **redigering** . Når dette trin vises fanen **Runbooks** på følgende måde:

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Gå til fanen **Aktiver** i den automatiske konto skal du klikke på **Tilføj indstilling** &gt; **Tilføj legitimationsoplysninger**, og Tilføj din Azure legitimationsoplysninger – name aktiv AzureCredential.

    Bruge Windows PowerShell-legitimationsoplysninger. Dette skal være en legitimationsoplysninger, der indeholder en Org-ID-brugernavn og din adgangskode med adgang til dette Azure abonnement og med Multi-Factor authentication er deaktiveret. Det er nødvendigt at godkende på vegne af brugeren under failover og for at få vist filer server enhederne på webstedet DR.

1.  Vælg fanen **Aktiver** kontoen automatisering, og klik derefter på **Tilføj indstilling** &gt; **Tilføj variabel** og tilføje følgende variabler. Du kan vælge at kryptere disse aktiver. Disse variabler er gendannelse plan-specifikke. Hvis din gendannelse plan (som du opretter i næste trin) navn er TestPlan og derefter dine variabler skal være TestPlan StorSimRegKey, TestPlan AzureSubscriptionName osv.

    -   *RecoveryPlanName* **-StorSimRegKey**: tasten registrering for tjenesten StorSimple Manager.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: navnet på det Azure abonnement.

    -   *RecoveryPlanName* **-ResourceName**: navnet på den ressource, StorSimple, der indeholder StorSimple enheden.

    -   *RecoveryPlanName* **-DeviceName**: den enhed, der skal være failed over.

    -   *RecoveryPlanName* **-TargetDeviceName**: feltet StorSimple skyen maskinen som beholdere der skal være failed over.

    -   *RecoveryPlanName* **-VolumeContainers**: en kommasepareret streng med lydstyrken beholdere findes på enheden, der skal være failed over; volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: navnet på tjenesten destinationsadresse-enhed (findes i afsnittet **virtuelt** : navnet på tjenesten er den samme som DNS-navnet).

    -   *RecoveryPlanName* **-StorageAccountName**: kontonavn lager, hvor scriptet (som har til at køre på den mislykkedes over VM) gemmes. Det kan være en hvilken som helst lagerplads-konto, der har plads til at gemme scriptet midlertidigt.

    -   *RecoveryPlanName* **-StorageAccountKey**: hurtigtast for kontoen ovenfor lagerplads.

    -   *RecoveryPlanName* **-ScriptContainer**: navnet på den beholder, hvori scriptet være gemt i skyen. Hvis objektbeholderen ikke findes, oprettes den.

    -   *RecoveryPlanName* **-VMGUIDS**: af, hvordan du beskytter en VM, Azure gendannelse af websteder tildeler hver VM et entydigt ID, der giver oplysninger om den mislykkedes over VM. Vælg fanen **Gendannelse Services** for at få VMGUID, og klik derefter på **Beskyttet element** &gt; **Beskyttelse grupper** &gt; **maskiner** &gt; **Egenskaber**. Hvis du har flere FOS, skal du tilføje GUID'erne som en kommasepareret streng.

    -   *RecoveryPlanName* **-AutomationAccountName** – på navnet på den automatiske konto, du har tilføjet runbooks og aktiverne.

    Eksempelvis hvis navnet på den gendannelse plan er fileServerpredayRP, skal derefter dine **Aktiver** vises fanen således når du har tilføjet alle aktiverne.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Gå til afsnittet **Gendannelsestjenester** , og vælg den samling af legitimationsoplysninger Azure gendannelse af websteder, du oprettede tidligere.

2.  Vælg fanen **Gendannelse planer** og oprette en ny gendannelse plan på følgende måde:

    en.  Angiv et navn, og vælg den relevante **Gruppen beskyttelse**.

    b.  Vælg FOS gruppen beskyttelse, som du vil medtage i den gendannelse plan.

    c.  Markér afkrydsningsfeltet for at åbne visningen gendannelse planlægge tilpasning, efter at genoprettelsen plan der oprettes.

    d.  Vælg **Luk computeren for alle grupper**, skal du klikke på **Script**, og vælg **Tilføj et primære side script før alle gruppe lukning**.

    e.  Vælg kontoen automatisering (hvor du har tilføjet runbooks), og vælg derefter runbook **mislykkes over-StorSimple-volumenlicens-beholdere** .

    f.  Klik på **gruppe 1: starte**skal du vælge **virtuelle maskiner**og tilføje de VM'er, der skal være beskyttet i gendannelse plan.

    g.  Klik på **gruppe 1: starte**, Vælg **Script**, og Tilføj alle de følgende scripts i rækkefølge, som **efter gruppe 1** trin.

    - Start-StorSimple-virtuelle-maskinen runbook
    - Mislykkes over-StorSimple-volumenlicens-beholdere runbook
    - Indlæs enheder efter failover runbook
    - Fjerne brugerdefinerede-script-forlængelse runbook

1.  Tilføje en manuel handling efter de ovenstående 4 scripts i den samme **gruppe 1: efter trin** sektion. Denne handling er det sted, hvor du kan kontrollere, at alt fungerer korrekt. Denne handling skal tilføjes som en del test ydelse (derfor kun vælge **Teste Failover** afkrydsningsfeltet).

2.  Tilføj oprydning scriptet på samme måde som du brugte til de andre runbooks efter handlingen manuelt. Gemme gendannelse plan.

    > [AZURE.NOTE] Når du kører en test failover, skal du kontrollere alt på trinnet til manuel handling, fordi de StorSimple enheder, der havde blevet klonet på målenheden bliver slettet som en del af oprydningen, når handlingen manuel er fuldført.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Udføre en test failover

Referere til [Active Directory DR løsning](../site-recovery/site-recovery-active-directory.md) medfølgende vejledning til bestemte overvejelser til Active Directory under sekundære test. Lokalt konfigurationen er ikke forstyrres overhovedet, når test sekundære opstår. De StorSimple enheder, der er knyttet til den lokale VM sammenkædes med StorSimple skyen maskinen på Azure. Der åbnes en VM til testformål i Azure og klonede enhederne er knyttet til VM.

#### <a name="to-perform-the-test-failover"></a>Udføre sekundære test

1.  Vælg dit websted gendannelse samling på Azure klassisk portalen.

1.  Klik på den gendannelse plan, der er oprettet for filserver VM.

2.  Klik på **Test Failover**.

3.  Vælg det virtuelle netværk til at starte processen test failover.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Når det sekundære miljø er oprettet, kan du udføre dit valideringer.

2.  Når valideringer er fuldført, skal du klikke på **Valideringer fuldført**. Failover testmiljø der ryddes, og TFO operationen skal udføres.

## <a name="perform-an-unplanned-failover"></a>Udføre en ikke-planlagt failover

Under failover en ikke-planlagt StorSimple enhederne er mislykkedes til den virtuelle enhed, en replika VM vil blive bragt på Azure og enhederne, der er knyttet til VM.

#### <a name="to-perform-an-unplanned-failover"></a>Til at udføre en ikke-planlagt failover

1.  Vælg dit websted gendannelse samling på Azure klassisk portalen.

1.  Klik på den gendannelse plan, der er oprettet for filserver VM.

2.  Klik på **Failover** , og vælg derefter **Ikke-planlagt Failover**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Vælg netværket, mål og derefter klikke på ikonet Kontrollér ✓ til at starte processen failover.

## <a name="perform-a-planned-failover"></a>Udføre en planlagt failover

Lokale fil under en planlagt failover server VM lukkes problemfrit og en sky, som er taget sikkerhedskopiering øjebliksbillede af enhederne på StorSimple enhed. StorSimple enhederne er mislykkedes til den virtuelle enhed, der åbnes en replika VM på Azure, og enhederne, der er knyttet til VM.

#### <a name="to-perform-a-planned-failover"></a>Til at udføre en planlagt failover

1.  Vælg dit websted gendannelse samling på Azure klassisk portalen.

1.  Klik på den gendannelse plan, der er oprettet for filserver VM.

2.  Klik på **Failover** , og vælg derefter **Planlagt Failover**.

3.  Vælg netværket, mål og derefter klikke på ikonet Kontrollér ✓ til at starte processen failover.

## <a name="perform-a-failback"></a>Udføre en failback

Under en failback er StorSimple lydstyrken objektbeholdere mislykkedes tilbage til den fysiske enhed efter er taget en sikkerhedskopi.

#### <a name="to-perform-a-failback"></a>Til at udføre en failback

1.  Vælg dit websted gendannelse samling på Azure klassisk portalen.

1.  Klik på den gendannelse plan, der er oprettet for filserver VM.

2.  Klik på **Failover** , og vælg **planlagt failover** eller **ikke-planlagt failover**.

3.  Klik på **Skift retning**.

4.  Vælg det relevante datasynkronisering og VM oprettelse af indstillinger.

5.  Klik på ikonet Kontrollér ✓ til at starte processen failback.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Bedste fremgangsmåder

### <a name="capacity-planning-and-readiness-assessment"></a>Kapacitet planlægning og parathed vurdering


#### <a name="hyper-v-site"></a>Hyper-V-websted

Bruge [bruger kapacitet teamplanlægning værktøj](http://www.microsoft.com/download/details.aspx?id=39057) til at designe den server, lager og netværksinfrastruktur for dit Hyper-V replika-miljø.

#### <a name="azure"></a>Azure

Du kan køre [værktøj til Azure virtuelt parathed vurdering](http://azure.microsoft.com/downloads/vm-readiness-assessment/) på FOS at sikre, at de er kompatible med Azure FOS og Azure websted gendannelsestjenester. Værktøjet parathed vurdering kontrollerer VM konfigurationer og advarer når konfigurationer er ikke kompatibelt med Azure. Det udsteder for eksempel en advarsel, hvis en C:-drevet er større end 127 GB.


Planlægning af kapacitet består af mindst to vigtige processer:

-   Tilknytning lokale Hyper-V FOS til Azure VM størrelser (såsom A6, A7, A8 og A9).

-   Finde ud af det nødvendige internetbåndbredde.

## <a name="limitations"></a>Begrænsninger

- På nuværende tidspunkt kan kun 1 StorSimple enhed være failed over (til en enkelt StorSimple skyen maskinen). Scenarie med en filserver, der strækker sig over flere StorSimple enheder understøttes ikke endnu.

- Hvis du får en fejl under aktivering beskyttelse til en VM, skal du kontrollere, at du har afbrudt iSCSI-destinationer.

- Alle lydstyrken beholdere, der er grupperet sammen på grund af sikkerhedskopiering politikker, der strækker sig over på tværs af lydstyrken beholdere bliver ikke gennemført sammen.

- Alle enheder i lydstyrken beholdere, du har valgt bliver ikke gennemført.

- Enheder, føje op til mere end 64 TB kan ikke være failed over, fordi den maksimale kapacitet for en enkelt StorSimple skyen maskinen er 64 TB.

- Hvis den planlagte/ikke-planlagt sekundære mislykkes, og FOS er oprettet i Azure, derefter ikke ren af FOS. I stedet, gøre en failback. Hvis du sletter FOS derefter kan lokale FOS ikke slås igen.

- Efter en failover, hvis du ikke kunne se enhederne, gå til FOS, åbne Disk Management, scanne diskene igen og derefter få dem vist online.

- I nogle tilfælde drevbogstaver i webstedet DR muligvis anderledes end de bogstaver i det lokale miljø. Hvis dette sker, skal du løse problemet manuelt, når sekundære er fuldført.

- Multi-Factor authentication skal være deaktiveret for de Azure legitimationsoplysninger, der er angivet i den automatiske konto som et aktiv. Hvis denne godkendelse ikke er deaktiveret, scripts, ikke tilladelse til at køre automatisk, og den gendannelse plan mislykkes.

- Failover job timeout: feltet StorSimple script afbrydes, hvis sekundære lydstyrken beholdere tager længere tid end grænsen Azure gendannelse af websteder i script (aktuelt 120 minutter).

- Sikkerhedskopieringsjob timeout: feltet StorSimple script får timeout, hvis sikkerhedskopien af enheder tager længere tid end grænsen Azure gendannelse af websteder i script (aktuelt 120 minutter).
 
    > [AZURE.IMPORTANT] Køre sikkerhedskopien manuelt fra Azure-portalen, og derefter køre gendannelse planen igen.

- Klone job timeout: feltet StorSimple script får timeout, hvis klone af drev tager længere tid end grænsen Azure gendannelse af websteder i script (aktuelt 120 minutter).

- Tid synkroniseringsfejl: feltet StorSimple scripts fejl ud af, siger, at sikkerhedskopierne var mislykkes, selvom at sikkerhedskopieringen er gået igennem i portalen. En mulig årsag til dette kan være, at StorSimple maskinen tid måske ikke synkroniseret med det aktuelle klokkeslæt i en tidszone.
 
    > [AZURE.IMPORTANT] Synkronisere maskinen tiden med det aktuelle klokkeslæt i en tidszone.

- Maskinen failover fejl: feltet StorSimple script kan mislykkes, hvis der er en maskinen failover, når den gendannelse plan kører.
    
    > [AZURE.IMPORTANT] Kør gendannelse planen, når maskinen sekundære er fuldført.

## <a name="summary"></a>Oversigt

Brug af Azure gendannelse af websteder, kan du oprette en komplet automatiseret nedbrud gendannelse plan til en filserver VM har filshares hostes på StorSimple lagerplads. Du kan starte sekundære i sekunder fra et vilkårligt sted i tilfælde af en afbrydelser og få det program, op at køre i et par minutter.
