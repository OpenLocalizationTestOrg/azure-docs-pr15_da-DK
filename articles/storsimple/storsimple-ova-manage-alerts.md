<properties 
   pageTitle="Få vist og administrere StorSimple virtuelle matrix beskeder | Microsoft Azure"
   description="Beskriver StorSimple virtuelle matrix beskeder om betingelser og alvor, og hvordan du bruger tjenesten StorSimple Manager til at administrere beskeder."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Bruge tjenesten StorSimple Manager til at få vist og administrere beskeder til den virtuelle StorSimple-matrix

## <a name="overview"></a>Oversigt

Fanen **beskeder** i tjenesten StorSimple Manager gør muligt for dig at gennemse, og fjern markeringen i StorSimple virtuelle matrix-relaterede beskeder på grundlag i realtid. Under denne fane kan du centralt overvåge systemtilstand problemer af din StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder) og den overordnede Microsoft Azure StorSimple løsning.

I dette selvstudium beskrives, hvordan du kan konfigurere påmindelser, almindelige besked betingelser, alvorlighed niveauer, og hvordan du kan få vist og registrere beskeder. Derudover indeholder beskeder om oversigtsvejledning tabeller, som gør det muligt at hurtigt at finde en bestemt besked og svare.

![Siden beskeder](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurere beskedindstillinger

Du kan vælge, om du vil have besked via mail af beskeder om betingelser for hver af dine StorSimple virtuelle enheder. Du kan desuden identificere andre modtagere af Giv besked ved at indtaste deres mailadresser i feltet **andre e-mail-modtagere** , adskilt med semikolon.

>[AZURE.NOTE] Du kan angive maksimalt 20 mailadresser per virtuel enhed.

Når du aktiverer mailmeddelelse om en virtuel enhed, modtager medlemmer af listen besked om en e-mail-meddelelse, hver gang der opstår en vigtig besked. Meddelelserne, der sendes fra *storsimple-alerts-noreply@mail.windowsazure.com* og beskriver betingelsen besked. Modtagere kan klikke på **Ophæv abonnement** for at fjerne sig fra listen e-mail-meddelelse.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Aktivere mailmeddelelse af beskeder for en virtuel enhed

1. Gå til **enheder** > **konfiguration** for den virtuelle enhed. Gå til afsnittet **beskedindstillinger** .

    ![indstillinger for beskeder](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Under **beskedindstillinger**skal du angive følgende:

    1. Vælg **Ja**i feltet **Send mailmeddelelse** .

    2. I feltet **mail tjenesteadministratorer** Vælg **Ja** , hvis du vil have tjenesteadministratoren og alle medadministratorer modtage beskeder om meddelelser.

    3. Skriv mailadresserne på alle andre modtagere, der skal modtage beskeder om meddelelser i feltet **andre e-mail-modtagere** . Angiv navnene i formatet *someone@somewhere.com*. Brug semikolon til at adskille e-mail-adresser. Du kan konfigurere maksimalt 20 mailadresser per virtuel enhed. 

        ![konfigurationen af beskeder-beskeder](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Klik på **Gem** for at gemme din konfiguration nederst på siden.

4. Hvis du vil sende en test e-mail-meddelelse, skal du klikke på pileikonet ud for **sende test mail**. Tjenesten StorSimple Manager vises meddelelser om status, som den videresender meddelelsen til test. 

5. Når følgende meddelelse vises, skal du klikke på **OK**. 

    ![Beskeder teste sendt en meddelelse om e-mail](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Hvis meddelelsen om opgaven test ikke kan afsendes, vises tjenesten StorSimple Manager en meddelelse. Klik på **OK**, vent et par minutter, og prøv derefter at sende din testmeddelelse igen. 

    Meddelelsen om opgaven test bliver stil med følgende.

    ![Eksempel på beskeder test mail](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Almindelige beskeder om betingelser

Din StorSimple virtuelle matrix genererer beskeder i svar til en række af betingelser. Følgende er de mest almindelige typer beskeder om betingelser:

- **Problemer med serverforbindelsen** – disse beskeder opstår, når der er problemer med at overføre data. Kommunikationsproblemer kan forekomme under overførsel af data til og fra kontoen Azure lagerplads eller på grund af manglende forbindelsen mellem de virtuelle enheder og tjenesten StorSimple Manager. Kommunikationsproblemer er nogle af de sværeste at løse, fordi der er så mange punkter af fejl ved. Altid først skal du kontrollere netværksforbindelsen og internetadgang er tilgængelige før du fortsætter til mere avanceret fejlfinding. Gå til [StorSimple virtuelle matrix systemkrav](storsimple-ova-system-requirements.md)for oplysninger om porte og firewall-indstillinger. Hjælp til fejlfinding, skal du gå til [fejlfinding i forbindelse med med Cmdletten Test forbindelse](storsimple-troubleshoot-deployment.md).

- **Problemer med ydeevnen** – disse beskeder opstår, når dit system ikke udføre optimalt, som når det er meget belastet.

Desuden kan du se beskeder, der er relateret til sikkerhed, opdateringer eller mislykkede forsøg på jobbet.

## <a name="alert-severity-levels"></a>Alvorlighed niveauer

Beskeder har forskellige alvorlighed supportniveauer, afhængigt af den effekt, der skal have besked om situationen og behovet for et svar på beskeden. Alvorlighed niveauer er:

- **Kritisk** – denne besked er som svar på en betingelse, som påvirker vellykket ydeevnen for dit system. Handling er påkrævet at sikre, at StorSimple tjenesten ikke bliver afbrudt.

- **Advarsel** – denne betingelse kan blive kritiske, hvis ikke er løst. Du skal undersøge situationen og foretage dig noget, der kræves for at fjerne problemet.

- **Du kan finde oplysninger** – denne besked indeholder oplysninger, der kan være nyttige i sporing og administration af dit system.

## <a name="view-and-track-alerts"></a>Få vist og registrere beskeder

StorSimple Manager service-dashboardet giver dig et hurtigt overblik over antallet af beskeder på din virtuelle enheder, der er arrangeret efter prioritet.

![Beskeder dashboard](./media/storsimple-ova-manage-alerts/alerts6.png)

Klik på en prioritering åbnet fanen **beskeder** . Resultaterne indeholder kun de påmindelser, der svarer til prioritering.

![Beskeder rapport, der er fastsat til beskedtype](./media/storsimple-ova-manage-alerts/alerts7.png)

Klikke på en besked på listen giver dig flere oplysninger for på beskeden, herunder sidst blev rapporteret beskeden, antallet af forekomster af den vigtige besked på enheden, og de anbefalede handlinger til at løse beskeden.

Du kan kopiere oplysninger om påmindelser til en tekstfil, hvis du vil sende oplysningerne til Microsoft Support. Når du har fulgt anbefalingen og løst på beskeder om betingelse i det lokale miljø, skal du fjerne markeringen beskeden fra ved at markere den vigtige besked på fanen **beskeder** og klikke på **Fjern**. Hvis du vil fjerne flere beskeder, vælge hver besked, skal du klikke på alle kolonner undtagen kolonnen **besked** og derefter klikke på **Fjern** , når du har markeret alle beskeder til at blive fjernet. Bemærk, at nogle påmindelser slettes automatisk, når problemet er løst, eller når påmindelsen opdateres med nye oplysninger.

Når du klikker på **Ryd**, har du mulighed for at angive kommentarer om beskeden og de trin, som du oprindeligt brugte for at løse problemet. 

![beskeder om kommentarer](./media/storsimple-ova-manage-alerts/clear-alert.png)

Klik på ikonet Kontrollér ![Kontrollér-ikon](./media/storsimple-ova-manage-alerts/check-icon.png) for at gemme dine kommentarer.

Nogle hændelser, der fjernes af systemet, hvis en anden hændelse udløses med nye oplysninger. Det er tilfældet, kan du få vist følgende meddelelse.

![Ryd advarselsmeddelelse](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Sortere og gennemse beskeder

Fanen **beskeder** kan vise op til 250 beskeder. Hvis du har overskredet pågældende antallet af beskeder, vises ikke alle vigtige beskeder i standardvisningen. Du kan kombinere følgende felter for at tilpasse, hvilke påmindelser vises:

- **Status** – du kan få vist **aktiv** eller **ryddet** beskeder. Aktive beskeder anvendes stadig på dit system, mens umarkeret beskeder er blevet enten manuelt fjernet som administrator eller slettes fra et program, da systemet opdateret betingelsen besked med nye oplysninger.

- **Alvorlighed** – du kan få vist beskeder på alle niveauer alvorlighed (kritisk, advarsel, oplysninger) eller blot en bestemt alvor, som kun vigtige beskeder.

- **Kilde** – du kan få vist beskeder fra alle kilder eller begrænse de vigtige beskeder til dem, der kommer fra tjenesten eller et eller alle de virtuelle enheder.

- **Tidsinterval** – ved at angive **fra** og **til** datoer og tidsstempler, kan du se beskeder det tidsrum, du er interesseret i.

## <a name="alerts-quick-reference"></a>Beskeder oversigtsvejledning

I følgende tabel vises nogle af de Microsoft Azure StorSimple vigtige beskeder, du kan støde på, samt yderligere oplysninger og anbefalinger hvor det er tilgængeligt. StorSimple virtuel enhed beskeder falder inden for en af følgende kategorier:

- [Skyen connectivity beskeder](#cloud-connectivity-alerts)

- [Konfiguration af beskeder](#configuration-alerts)

- [Fejl i køjob](#job-failure-alerts)

- [Ydeevnen beskeder](#performance-alerts)

- [Sikkerhedsadvarsler](#security-alerts)

- [Opdatere beskeder](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Skyen connectivity beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Enhed *<device name>* er ikke forbundet til skyen.|Navngivet enheden kan ikke oprette forbindelse til skyen. |Kan ikke oprette forbindelse til skyen. Det kan skyldes et af følgende:<ul><li>Der kan være et problem med netværksindstillinger på din enhed.</li><li>Der kan være et problem med kontolegitimationsoplysninger lagerplads.</li></ul>Gå til [lokale web Brugergrænsefladen](storsimple-ova-web-ui-admin.md) på enheden kan finde flere oplysninger om fejlfinding af problemer med serverforbindelsen.|


### <a name="configuration-alerts"></a>Konfiguration af beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Konfiguration af lokale virtuel enhed understøttes ikke.|Langsom ydeevne.|Den aktuelle konfiguration kan medføre forringet ydeevne. Sørg for, at din server opfylder mindste konfiguration. Gå til [StorSimple virtuelle matrix krav](storsimple-ova-system-requirements.md)kan finde flere oplysninger. 
|Du kører af klargjort diskplads på <*enhedens navn*>.|Advarsel om disk.|Du løbe tør for klargjort diskplads. Hvis du vil frigøre plads, overveje at flytte arbejdsbelastninger til en anden enhed eller del eller slette data.

### <a name="job-failure-alerts"></a>Fejl i køjob

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Sikkerhedskopi af <*enhedens navn*> kunne ikke fuldføres.|Sikkerhedskopieringen mislykkes.|Kan ikke oprette en sikkerhedskopi. Overvej følgende:<ul><li>Problemer med serverforbindelsen kan forhindre, at sikkerhedskopieringen fra har gennemført. Sørg for, at der er ingen problemer med serverforbindelsen. Gå til [lokale web brugergrænseflade](storsimple-ova-web-ui-admin.md) til den virtuelle enhed kan finde flere oplysninger om fejlfinding af problemer med serverforbindelsen.</li><li>Du har nået tilgængelige lagergrænsen. Hvis du vil frigøre plads, bør du overveje at slette eventuelle sikkerhedskopier, der ikke længere skal bruges.</li></ul> Løse problemerne, fjern beskeden, og prøv derefter igen.|
|Gendannelse af <*enhedens navn*> kunne ikke fuldføres.|Gendanne job mislykkedes.|Kunne ikke gendanne fra sikkerhedskopi. Overvej følgende:<ul><li>Listen over sikkerhedskopiering eventuelt ikke er gyldige. Opdater listen for at bekræfte, at det er stadig er gyldig.</li><li>Forbindelsesproblemer forhindrer muligvis gendannelsen fra har gennemført. Sørg for, at der er ingen problemer med serverforbindelsen.</li><li>Du har nået tilgængelige lagergrænsen. Hvis du vil frigøre plads, bør du overveje at slette eventuelle sikkerhedskopier, der ikke længere skal bruges.</li></ul>Løse problemerne, fjern beskeden, og prøv igen gendannelsen.|
|Klon af <*enhedens navn*> kunne ikke fuldføres.|Klone job mislykkedes.|Kan ikke oprette en kopi. Overvej følgende:<ul><li>Listen over sikkerhedskopiering eventuelt ikke er gyldige. Opdater listen for at bekræfte, at det er stadig er gyldig.</li><li>Forbindelsesproblemer forhindrer muligvis handlingen Klon fra har gennemført. Sørg for, at der er ingen problemer med serverforbindelsen.</li><li>Du har nået tilgængelige lagergrænsen. Hvis du vil frigøre plads, bør du overveje at slette eventuelle sikkerhedskopier, der ikke længere skal bruges.</li></ul>Løse problemerne, fjern beskeden, og prøv derefter igen.|

### <a name="performance-alerts"></a>Ydeevnen beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Du oplever uventede forsinkelser i dataoverførsel.|Langsom dataoverførsel.|Variere den benyttede fejl opstår, når du overskrider basis af en lagringstjeneste skalerbarhed mål. Tjenesten storage gør dette for at sikre, at ingen enkelt klient eller lejer kan bruge tjenesten fra andre. Gå til [skærm, diagnosticere, og foretage fejlfinding af Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md)kan finde flere oplysninger om fejlfinding i forbindelse med kontoen Azure-lager.
|Du løbe tør for plads på harddisken lokale reservation på <*enhedens navn*>.|Langsomme svartid.|10% af den samlede klargjorte størrelse for <*enhedens navn*> er reserveret på den lokale enhed, og du har nu lidt reserveret område. Arbejdsbelastningen på <*enhedens navn*> genererer en højere rente transportspand, eller du kan have senest overføres en stor mængde data. Dette kan medføre reduceret ydeevne. Overvej en af følgende fremgangsmåder til at løse dette:<ul><li>Øge skyen båndbredde til denne enhed.</li><li>Reducere eller Flyt arbejdsbelastninger til en anden enhed eller del.</li></ul>

### <a name="security-alerts"></a>Sikkerhedsadvarsler

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Adgangskode til <*enhedens navn*> udløber <*tal*> dage.|Advarsel om adgangskode.| Adgangskoden udløber i < tal < dage. Overvej at ændre din adgangskode. Gå til [ændre StorSimple virtuelle matrix enhed administratoradgangskode](storsimple-ova-change-device-admin-password.md)kan finde flere oplysninger.

### <a name="update-alerts"></a>Opdatere beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Nye opdateringer er tilgængelige for din enhed.|Opdateringer til StorSimple virtuelle matrixen er tilgængelige.|Du kan installere nye opdateringer fra siden **vedligeholdelse** .|
|Kan ikke søge efter nye opdateringer på <*enhedens navn*>.|Opdater fejl. |Der opstod en fejl under installation af nye opdateringer. Du kan installere opdateringerne manuelt. Hvis problemet fortsætter, skal du kontakte [Microsoft Support](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Næste trin

- [Få mere at vide om StorSimple virtuelle matrixen](storsimple-ova-overview.md).


