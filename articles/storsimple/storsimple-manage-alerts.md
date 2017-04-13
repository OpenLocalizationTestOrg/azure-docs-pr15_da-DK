<properties 
   pageTitle="Få vist og administrere StorSimple beskeder | Microsoft Azure"
   description="I denne artikel beskrives StorSimple beskeder om betingelser og alvor, hvordan du kan konfigurere påmindelser og hvordan du bruger tjenesten StorSimple Manager til at administrere beskeder."
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
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Bruge tjenesten StorSimple Manager til at få vist og administrere StorSimple beskeder

## <a name="overview"></a>Oversigt

Fanen **beskeder** i tjenesten StorSimple Manager gør muligt for dig at gennemse, og fjern markeringen i StorSimple enhed – relateret beskeder på grundlag i realtid. Under denne fane kan du centralt overvåge systemtilstand problemer af dine StorSimple enheder og den overordnede Microsoft Azure StorSimple løsning.

I dette selvstudium beskrives almindelige beskeder om betingelser, alvorlighed niveauer og hvordan du kan konfigurere påmindelser. Derudover indeholder beskeder om oversigtsvejledning tabeller, som gør det muligt at hurtigt at finde en bestemt besked og svare.

![Siden beskeder](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Almindelige beskeder om betingelser

Enheden StorSimple genererer beskeder i svar til en række af betingelser. Følgende er de mest almindelige typer beskeder om betingelser:

- **Problemer med hardware** – disse beskeder fortæller dig om tilstanden for din hardware. De fortæller dig, hvis der er nødvendige firmwareopgraderinger, hvis en netværksgrænseflade har problemer, eller hvis der opstår et problem med en af dine datadrev.

- **Problemer med serverforbindelsen** – disse beskeder opstår, når der er problemer med at overføre data. Kommunikationsproblemer kan forekomme under overførsel af data til og fra kontoen Azure lagerplads eller på grund af manglende forbindelsen mellem enhederne og tjenesten StorSimple Manager. Kommunikationsproblemer er nogle af de sværeste at løse, fordi der er så mange punkter af fejl ved. Altid først skal du kontrollere netværksforbindelsen og internetadgang er tilgængelige før du fortsætter til mere avanceret fejlfinding. Hjælp til fejlfinding, skal du gå til [fejlfinding i forbindelse med med Cmdletten Test forbindelse](storsimple-troubleshoot-deployment.md).

- **Problemer med ydeevnen** – disse beskeder opstår, når dit system ikke udføre optimalt, som når det er meget belastet.

Desuden kan du se beskeder, der er relateret til sikkerhed, opdateringer eller mislykkede forsøg på jobbet.

## <a name="alert-severity-levels"></a>Alvorlighed niveauer

Beskeder har forskellige alvorlighed supportniveauer, afhængigt af den effekt, der skal have besked om situationen og behovet for et svar på beskeden. Alvorlighed niveauer er:

- **Kritisk** – denne besked er som svar på en betingelse, som påvirker vellykket ydeevnen for dit system. Handling er påkrævet at sikre, at StorSimple tjenesten ikke bliver afbrudt.

- **Advarsel** – denne betingelse kan blive kritiske, hvis ikke er løst. Du skal undersøge situationen og foretage dig noget, der kræves for at fjerne problemet.

- **Du kan finde oplysninger** – denne besked indeholder oplysninger, der kan være nyttige i sporing og administration af dit system.

## <a name="configure-alert-settings"></a>Konfigurere beskedindstillinger

Du kan vælge, om du vil have besked via mail af beskeder om betingelser for hver af dine StorSimple enheder. Du kan desuden identificere andre modtagere af Giv besked ved at indtaste deres mailadresser i feltet **andre e-mail-modtagere** , adskilt med semikolon.

>[AZURE.NOTE] Du kan angive maksimalt 20 mailadresser per enhed.

Når du aktiverer mailmeddelelse til en enhed, modtager medlemmer af listen besked om en e-mail-meddelelse, hver gang der opstår en vigtig besked. Meddelelserne, der sendes fra *storsimple-alerts-noreply@mail.windowsazure.com* og beskriver betingelsen besked. Modtagere kan klikke på **Ophæv abonnement** for at fjerne sig fra listen e-mail-meddelelse.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aktivere mailmeddelelse af beskeder for en enhed

1. Gå til **enheder** > **Konfigurer** til enheden.

2. Under **Beskedindstillinger**skal du angive følgende:

    1. Vælg **Ja**i feltet **Send mailmeddelelse** .

    2. I feltet **mail tjenesteadministratorer** Vælg **Ja** , hvis du vil have tjenesteadministratoren og alle medadministratorer modtage beskeder om meddelelser.

    3. Skriv mailadresserne på alle andre modtagere, der skal modtage beskeder om meddelelser i feltet **andre e-mail-modtagere** . Angiv navnene i formatet *someone@somewhere.com*. Brug semikolon til at adskille e-mail-adresser. Du kan konfigurere maksimalt 20 mailadresser per enhed. 

        ![Konfigurationen af beskeder-beskeder](./media/storsimple-manage-alerts/AlertNotify.png)

3. Hvis du vil sende en test e-mail-meddelelse, skal du klikke på pileikonet ud for **sende test mail**. Tjenesten StorSimple Manager vises meddelelser om status, som den videresender meddelelsen til test. 

4. Når følgende meddelelse vises, skal du klikke på **OK**. 

    ![Beskeder teste sendt en meddelelse om e-mail](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Hvis meddelelsen om opgaven test ikke kan afsendes, vises tjenesten StorSimple Manager en meddelelse. Klik på **OK**, vent et par minutter, og prøv derefter at sende din testmeddelelse igen. 

## <a name="view-and-track-alerts"></a>Få vist og registrere beskeder

StorSimple Manager service-dashboardet giver dig et hurtigt overblik over antallet af beskeder på dine enheder, der er arrangeret efter prioritet.

![Beskeder dashboard](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Klik på en prioritering åbnet fanen **beskeder** . Resultaterne indeholder kun de påmindelser, der svarer til prioritering.

![Beskeder rapport, der er fastsat til beskedtype](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Klikke på en besked på listen giver dig flere oplysninger for på beskeden, herunder sidst blev rapporteret beskeden, antallet af forekomster af den vigtige besked på enheden, og de anbefalede handlinger til at løse beskeden. Hvis det er en hardware-besked, kan det også identificere hardwarekomponenten.

![Eksempel på skrivebordsbesked hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Du kan kopiere oplysninger om påmindelser til en tekstfil, hvis du vil sende oplysningerne til Microsoft Support. Når du har efterfulgt af anbefaling og løst på beskeder om betingelse i det lokale miljø, skal du fjerne markeringen beskeden fra enheden ved at markere den vigtige besked på fanen **beskeder** og klikke på **Fjern**. Hvis du vil fjerne flere beskeder, vælge hver besked, skal du klikke på alle kolonner undtagen kolonnen **besked** og derefter klikke på **Fjern** , når du har markeret alle beskeder til at blive fjernet. Bemærk, at nogle påmindelser slettes automatisk, når problemet er løst, eller når påmindelsen opdateres med nye oplysninger.

Når du klikker på **Ryd**, har du mulighed for at angive kommentarer om beskeden og de trin, som du oprindeligt brugte for at løse problemet. Nogle hændelser, der fjernes af systemet, hvis en anden hændelse udløses med nye oplysninger. Det er tilfældet, kan du få vist følgende meddelelse.

![Ryd advarselsmeddelelse](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortere og gennemse beskeder

Du kan finde mere effektivt at køre rapporter på beskeder, så du kan gennemse og rydde dem i grupper. Desuden kan fanen **beskeder** vise op til 250 beskeder. Hvis du har overskredet pågældende antallet af beskeder, vises ikke alle vigtige beskeder i standardvisningen. Du kan kombinere følgende felter for at tilpasse, hvilke påmindelser vises:

- **Status** – du kan få vist **aktiv** eller **ryddet** beskeder. Aktive beskeder anvendes stadig på dit system, mens umarkeret beskeder er blevet enten manuelt fjernet som administrator eller slettes fra et program, da systemet opdateret betingelsen besked med nye oplysninger.

- **Alvorlighed** – du kan få vist beskeder på alle niveauer alvorlighed (kritisk, advarsel, oplysninger) eller blot en bestemt alvor, som kun vigtige beskeder.

- **Kilde** – du kan få vist beskeder fra alle kilder eller begrænse de vigtige beskeder til dem, der kommer fra enten tjenesten eller en eller alle enhederne.

- **Tidsinterval** – ved at angive **fra** og **til** datoer og tidsstempler, kan du se beskeder det tidsrum, du er interesseret i.

## <a name="alerts-quick-reference"></a>Oversigtsvejledning til beskeder

I følgende tabel vises nogle af de Microsoft Azure StorSimple vigtige beskeder, du kan støde på, samt yderligere oplysninger og anbefalinger hvor det er tilgængeligt. StorSimple enhed beskeder falder inden for en af følgende kategorier:

- [Skyen connectivity beskeder](#cloud-connectivity-alerts)

- [Klynge beskeder](#cluster-alerts)

- [Nedbrud gendannelse beskeder](#disaster-recovery-alerts)

- [Hardware-beskeder](#hardware-alerts)

- [Fejl i køjob](#job-failure-alerts)

- [Lokalt fastgjorte lydstyrken beskeder](#locally-pinned-volume-alerts)

- [Netværk beskeder](#networking-alerts)

- [Ydeevnen beskeder](#performance-alerts)

- [Sikkerhedsadvarsler](#security-alerts)

- [Understøttelse af pakke beskeder](#support-package-alerts)

- [Opdatere beskeder](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Skyen connectivity beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Kan ikke oprettes forbindelse til <*skyen legitimationsoplysninger navn*>.|Kan ikke oprette forbindelse til kontoen lagerplads.|Det ser ud til der kan være et forbindelsesproblem i med din enhed. Kør den `Test-HcsmConnection` cmdlet fra Windows PowerShell-grænsefladen til StorSimple på din enhed til at identificere og løse problemet. Hvis indstillingerne er korrekte, være problemet med legitimationsoplysningerne for kontoen lagerplads, påmindelsen blev opløftet. I dette tilfælde skal du bruge den `Test-HcsStorageAccountCredential` til at finde ud af, om der er problemer, som du kan løse.<ul><li>Kontrollér dine netværksindstillinger.</li><li>Kontrollere legitimationsoplysningerne lagerplads konto.</li></ul>|
|Vi har ikke modtaget en godkendelse gyldighed fra enheden til de seneste <*tal*> minutter.|Kan ikke oprette forbindelse til enhed.|Det ser ud til der er et forbindelsesproblem i med din enhed. Brug den `Test-HcsmConnection` cmdlet fra Windows PowerShell-grænsefladen til StorSimple på din enhed til at identificere og løse problemet, eller kontakte din netværksadministrator.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple funktionsmåde, når skyen connectivity mislykkes

Hvad sker der, hvis skyen forbindelse til min StorSimple enhed kører i fremstilling mislykkes?

Hvis skyen connectivity mislykkes på enheden StorSimple fremstilling, derefter afhængigt af tilstanden for din enhed, kan der ske følgende: 

- **For de lokale data på din enhed**: I et stykke tid, der vil være uden afbrydelser og læser fortsætter med at være served. Efterhånden som antallet af udestående IOs øges og overskrider en grænse, kunne læser starte mislykkes. 

    Afhængigt af mængden data på din enhed, vil skriver også stadig skal udføres for de første par timer efter afbrydelser i skyen forbindelsen. Skriver skal derefter langsommere og til sidst begynder at mislykkes, hvis skyen forbindelsen afbrydes til flere timer. (Der er midlertidige på enheden for data, der skal installeres til skyen. Dette område tømmes, når dataene sendes. Hvis connectivity mislykkes, skal data i dette område lagerplads kan ikke installeres i skyen, og EY mislykkes.)   

 
- **Data i skyen**: For de fleste skyen connectivity fejl, der returneres en fejl. Når forbindelsen er genoprettet, er i gang igen på IOs uden at brugeren har til at hente lydstyrken online. I sjældne tilfælde kan det være nødvendigt at få lydstyrken online fra portalen Azure klassisk brugerinput. 
 
- **Til skyen snapshots igangværende**: handlingen skal gentages nogle gange inden for 4-5 timer og hvis forbindelsen ikke bliver gendannet, skyen snapshots mislykkedes.


### <a name="cluster-alerts"></a>Klynge beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Enheden ikke kunne <*enhedens navn*>.|Enheden er i vedligeholdelsestilstand.|Enheden mislykkedes på grund af indtastning eller afslutte vedligeholdelsestilstand. Dette er normalt og ingen handling er det er nødvendigt. Når du har bekræftet denne besked, kan du fjerne det fra siden beskeder.|
|Enheden ikke kunne <*enhedens navn*>.|Enhedens firmware eller software blev lige opdateret.|Der opstod en Skift mellem klynger på grund af en opdatering. Dette er normalt og ingen handling er det er nødvendigt. Når du har bekræftet denne besked, kan du fjerne det fra siden beskeder.|
|Enheden ikke kunne <*enhedens navn*>.|Controller blev lukkes eller genstartes.|Enhed mislykkedes, fordi den aktive controller blev lukkes eller genstartes af en administrator. Ingen handling er nødvendig. Når du har bekræftet denne besked, kan du fjerne det fra siden beskeder.|
|Enheden ikke kunne <*enhedens navn*>.|Planlagte failover.|Kontrollér, at det var en planlagt failover. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.|
|Enheden ikke kunne <*enhedens navn*>.|Ikke-planlagt failover.|StorSimple er udviklet til at gendanne automatisk fra ikke-planlagt failover. Hvis du ser et stort antal disse beskeder, kan du kontakte Microsoft Support.|
|Enheden ikke kunne <*enhedens navn*>.|Andre/ukendt årsag.|Hvis du ser et stort antal disse beskeder, kan du kontakte Microsoft Support. Når problemet er løst, skal du fjerne markeringen i denne besked fra siden beskeder.|
|En kritiske enheden tjeneste rapporterer status som mislykket.|DataPath tjenestefejl. |Kontakt Microsoft Support for at få hjælp.|
|Virtuel IP-adresse til netværksgrænseflade <*DATA #*> rapporterer status som mislykket. |Andre/ukendt årsag. |Nogle gange midlertidige betingelser kan medføre disse beskeder. Hvis det er tilfældet, derefter fjernes denne besked automatisk efter et stykke tid. Hvis problemet fortsætter, skal du kontakte Microsoft Support.|
|Virtuel IP-adresse til netværksgrænseflade <*DATA #*> rapporterer status som mislykket.|Navn på grænseflade: <*DATA #*> IP-adresse <IP address> ikke er muligt online da der blev registreret en dublerede IP-adresse på netværket. |Kontroller, at den duplikerede IP-adresse er fjernet fra netværket eller omkonfigurere grænsefladen med en anden IP-adresse.|


### <a name="disaster-recovery-alerts"></a>Nedbrud gendannelse beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Gendannelse handlinger kunne ikke gendanne alle indstillingerne for denne tjeneste. Enhed konfigurationsdata findes i en inkonsekvent tilstand for visse enheder.|Data uoverensstemmelse fundet efter nedbrud.|Krypterede data på tjenesten er ikke synkroniseret med, på enheden. Godkend enheden <*enhedens navn*> fra StorSimple Manager til at starte synkroniseringen. Bruge Windows PowerShell-grænsefladen til StorSimple til at køre den `Restore-HcsmEncryptedServiceData` på enheden <*enhedens navn*> cmdlet at give den gamle adgangskode som input til denne cmdlet til at gendanne sikkerhedsprofil. Kør derefter den `Invoke-HcsmServiceDataEncryptionKeyChange` til at opdatere krypteringsnøglen service data. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.|


### <a name="hardware-alerts"></a>Hardware-beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Hardwarekomponent <*komponent-ID*> rapporterer status som <*status*>.||Nogle gange midlertidige betingelser kan medføre disse beskeder. Hvis det er tilfældet, ryddes denne besked automatisk efter et stykke tid. Hvis problemet fortsætter, skal du kontakte Microsoft Support.|
|Passive controller virker ikke.|Passive (sekundære) controller fungerer ikke.|Enheden er brugbar, men en af dine enheder korrekt. Du kan prøve at genstarte denne enhed. Hvis problemet ikke er løst, skal du kontakte Microsoft Support.|

### <a name="job-failure-alerts"></a>Fejl i køjob

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Sikkerhedskopiering af <*kilde lydstyrken gruppe-ID'ET*> mislykkedes.|Sikkerhedskopieringsjob mislykkedes.|Problemer med serverforbindelsen kan forhindre, at sikkerhedskopieringen fra har gennemført. Hvis der er ingen forbindelsesproblemer, at du har nået det maksimale antal sikkerhedskopier. Slette en hvilken som helst sikkerhedskopier, der ikke længere skal bruges, og prøv igen. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.|
|Klon for <*Sikkerhedskopiering Kildeelement id'er*> til <*destination lydstyrken serienumre*> mislykkedes.|Klon jobbet mislykkedes.|Opdater listen sikkerhedskopiering for at bekræfte, at sikkerhedskopieringen er stadig er gyldig. Hvis sikkerhedskopiering er gyldige, er det muligt, at skyen forbindelsesproblemer forhindrer handlingen Klon fra har gennemført. Hvis der er ingen forbindelsesproblemer, at du har nået lagergrænsen. Slette en hvilken som helst sikkerhedskopier, der ikke længere skal bruges, og prøv igen. Når du har taget nødvendige skridt for at løse problemet skal du fjerne markeringen denne besked fra siden beskeder.|
|Gendannelse af <*Sikkerhedskopiering Kildeelement id'er*> mislykkedes.|Gendan-job mislykkedes.|Opdater listen sikkerhedskopiering for at bekræfte, at sikkerhedskopieringen er stadig er gyldig. Hvis sikkerhedskopiering er gyldige, er det muligt, at skyen forbindelsesproblemer forhindrer gendannelsen fra har gennemført. Hvis der er ingen forbindelsesproblemer, at du har nået lagergrænsen. Slette en hvilken som helst sikkerhedskopier, der ikke længere skal bruges, og prøv igen. Når du har taget nødvendige skridt for at løse problemet skal du fjerne markeringen denne besked fra siden beskeder.|

### <a name="locally-pinned-volume-alerts"></a>Lokalt fastgjorte lydstyrken beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Oprettelse af lokale lydstyrken <*lydstyrken navn*> mislykkedes.| Lydstyrken oprettelse af jobbet mislykkedes. <*Fejlmeddelelse, der svarer til mislykkedes fejlkode*>.|Forbindelsesproblemer forhindrer muligvis handlingen mellemrum oprettelse af fra har gennemført. Lokalt fastgjorte enheder thickly er klargjort, og processen med at oprette mellemrum involverer går lagdelte enheder til skyen. Hvis der er ingen forbindelsesproblemer, at du har opbrugt lokale plads på enheden. Se, om mellemrum, findes på enheden, før du prøver igen denne handling.|
|Udvidelse af lokale lydstyrken <*lydstyrken navn*> mislykkedes.|Lydstyrken ændringer jobbet mislykkes på grund af <*fejlmeddelelse, der svarer til mislykkedes fejlkode*>.|Forbindelsesproblemer forhindrer muligvis handlingen lydstyrken udvidelse fra har gennemført. Lokalt fastgjorte enheder thickly er klargjort, og processen med at udvide den eksisterende plads omfatter går lagdelte enheder til skyen. Hvis der er ingen forbindelsesproblemer, at du har opbrugt lokale plads på enheden. Se, om mellemrum, findes på enheden, før du prøver igen denne handling.|
|Konvertering af lydstyrken <*lydstyrken navn*> mislykkedes.|Lydstyrken Konverteringsjobbet konvertere typen beholdning fra lokalt fastgjort til lagdelt mislykkedes.|Konvertering af lydstyrken fra type lokalt fastgjort til lagdelt blev ikke fuldført. Sørg for, at der er ingen forbindelsesproblemer forhindrer handlingen i at blive fuldført korrekt. Gå til [fejlfinding i forbindelse med med Test HcsmConnection cmdlet](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)til at løse problemer med serverforbindelsen.<br>Den oprindelige lokalt fastgjorte lydstyrke er nu markeret som en lagdelte lydstyrken da nogle af dataene fra lokalt fastgjorte lydstyrken er flyttet til skyen under konverteringen. Gældende lagdelte lydstyrken stadig optager lokale plads på den enhed, der ikke kan tages fra til fremtidige lokale drev.<br>Løse eventuelle problemer med serverforbindelsen, fjerne påmindelsen og konvertere denne lydstyrken tilbage til den oprindelige lokalt fastgjorte lydstyrken type at sikre, at alle dataene er gjort tilgængelig lokalt igen.|
|Konvertering af lydstyrken <*lydstyrken navn*> mislykkedes.|Lydstyrken Konverteringsjobbet konvertere typen beholdning fra lagdelt til lokalt fastgjort mislykkedes.|Konvertering af lydstyrken fra type lagdelt til lokalt fastgjort kunne ikke fuldføres. Sørg for, at der er ingen forbindelsesproblemer forhindrer handlingen i at blive fuldført korrekt. Gå til [fejlfinding i forbindelse med med Test HcsmConnection cmdlet](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)til at løse problemer med serverforbindelsen.<br>Den oprindelige lagdelte lydstyrke nu markeret som et lokalt fastgjorte lydstyrke, som en del af konverteringsprocessen fortsat vil indeholde data, der findes i skyen, mens den thickly klargjort plads på enheden for denne enhed kan ikke længere frigjort til fremtidige lokale drev.<br>Løse eventuelle problemer med serverforbindelsen, fjerne påmindelsen og konvertere denne lydstyrken tilbage til den oprindelige lagdelte lydstyrke type at sikre, at lokale mellemrum thickly klargjort på enheden kan være frigjort.|
|Nærmer sig lokale mellemrum forbrug for lokale snapshot af <*lydstyrken gruppenavn*>|Lokale snapshots for sikkerhedskopiering politikken muligvis snart løbe tør for plads og være ugyldiggjorte for at undgå host skrivefejl.|Hyppige lokale snapshots sammen med en høj data transportspand i de enheder, der er knyttet til denne politik for sikkerhedskopiering gruppe medfører lokale plads på enheden for at blive brugt hurtigt. Slette en hvilken som helst lokale snapshots, som ikke længere skal bruges. Også opdatere dine lokale øjebliksbillede planer for denne sikkerhedskopiering politik til at tage mindre hyppige lokale snapshots og sikre, at skyen snapshots der tages regelmæssigt. Hvis disse handlinger ikke er udført, kan snart være for småt lokale plads til disse snapshots og systemet sletter automatisk dem for at sikre, at host skriver fortsat er blevet behandlet.|
|Lokale snapshots for <*lydstyrken gruppenavn*> har ophævet.|De lokale snapshots for <*lydstyrken gruppenavn*> har ugyldiggjorte og derefter slettes, fordi de er blevet overstiger den lokale plads på enheden.|Gennemse lokal snapshot tidsplaner for denne sikkerhedskopiering politik for at sikre, at dette ikke gentages på et senere tidspunkt, og Slet eventuelle lokale snapshots, som ikke længere skal bruges. Hyppige lokale snapshots sammen med en høj data transportspand i de enheder, der er knyttet til denne politik for sikkerhedskopiering gruppe kan medføre lokale plads på enheden for at blive brugt hurtigt.|
|Gendannelse af <*Sikkerhedskopiering Kildeelement id'er*> mislykkedes.|Gendannelsesjobbet mislykkedes.|Hvis du har lokalt fastgjort eller en blanding af lokalt fastgjort og lagdelte enheder i denne sikkerhedskopiering politik, opdatere listen sikkerhedskopiering for at bekræfte, er at sikkerhedskopieringen stadig er gyldig. Hvis sikkerhedskopiering er gyldige, er det muligt, at skyen forbindelsesproblemer forhindrer gendannelsen fra har gennemført. De lokalt fastgjorte enheder, der er ved at blive gendannet som en del af denne øjebliksbillede gruppe har ikke alle deres data, der er hentet til enheden, og hvis du har en blanding af lagdelte og lokalt fastgjorte enheder i denne gruppe øjebliksbillede, de kan ikke synkroniseret med hinanden. Tage enhederne i denne gruppe offline på værten for at fuldføre gendannelsen, og prøv igen gendannelsen. Bemærk, at eventuelle ændringer til lydstyrke data, der blev udført under gendannelsen, går tabt.|

### <a name="networking-alerts"></a>Netværk beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Kunne ikke starte StorSimple tjenester.|DataPath fejl |Hvis problemet fortsætter, skal du kontakte Microsoft Support.|
|Dublerede IP-adresse, der findes til 'Data0'.| |Systemet har registreret en konflikt for IP-adresse '10.0.0.1'. Netværksressourcen 'Data0' på enheden *<device1>* er offline. Sørg for, at denne IP-adresse ikke bruges af enhver anden enhed i dette netværk. Fejlfinde netværksproblemer, skal du gå til [fejlfinding med cmdlet'en Get-NetAdapter til](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Du kan kontakte din netværksadministrator til at løse dette problem. Hvis problemet fortsætter, skal du kontakte Microsoft Support. |
|IPv4 (eller IPv6) adressen for 'Data0' er offline.| |Netværksressourcen 'Data0' med IP-adressen '10.0.0.1'. og præfiks længde '22' på enheden *<device1>* er offline. Sørg for, at de Skift porte, som denne grænseflade er tilsluttet er funktionsdygtige. Fejlfinde netværksproblemer, skal du gå til [fejlfinding med cmdlet'en Get-NetAdapter til](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Ydeevnen beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Afkrydsningsfeltet Indlæs enhed har overskredet <*grænseværdi*>.|Langsommere end forventet svar gange.|Enheden rapporter anvendelsen under belastet input/output. Dette kan medføre enheden ikke fungerer også som den skal. Gennemse arbejdsbelastninger, du har knyttet til enheden, og at fastlægge, om der er nogen, der blev flyttet til en anden enhed eller, der er ikke længere det er nødvendigt.<br>For at forstå den aktuelle status, skal du gå til [Brug tjenesten StorSimple Manager til at overvåge din enhed](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Sikkerhedsadvarsler

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Microsoft Support-sessionen er begyndt.|Fra tredjepart adgang til support session.|Bekræft denne adgang godkendes. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.|
|Adgangskode til <*element*> udløber <*tidsrum*>.|Udløb af adgangskoder nærmer.|Ændre din adgangskode, før det udløber.|
|Sikkerhed konfigurationsoplysninger mangler for <*element-ID*>.||De enheder, der er knyttet til denne lydstyrken objektbeholder kan ikke bruges til at gentage konfigurationen af StorSimple. For at sikre, at dine data er gemt på en sikker måde, anbefaler vi, at du sletter objektbeholderen lydstyrken og alle enheder, der er knyttet til objektbeholderen lydstyrken. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.|
|<*tal*> logonforsøg mislykkedes for <*element-ID*>.|Flere logonforsøg mislykkede.|Enheden er muligvis ikke under angreb eller en autoriseret bruger forsøger at oprette forbindelse til en forkert adgangskode.<ul><li>Kontakte din autoriserede brugere, og Bekræft, at disse forsøg er fra en gyldige kilde. Hvis du fortsat får stort antal mislykkede logonforsøg, kan du overveje at deaktivere fjernadministration og kontakte din netværksadministrator. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.</li><li>Kontrollér, at din øjebliksbillede Manager forekomster er konfigureret med den rigtige adgangskode. Når du har taget relevante handling, kan du fjerne markeringen denne besked fra siden beskeder.</li></ul>Gå til [ændre en adgangskode til udløbet enhed](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password)kan finde flere oplysninger.|
|Der opstod en eller flere fejl under ændring krypteringsnøglen service data.||Der opstod en fejl, der er registreret under ændre krypteringsnøglen service data. Når du har behandlet fejltilstande, kører på `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet fra Windows PowerShell-grænsefladen til StorSimple på din enhed for at opdatere tjenesten. Hvis problemet fortsætter, skal du kontakte Microsoft support. Når du løser problemet, kan du fjerne markeringen denne besked fra siden beskeder.|

### <a name="support-package-alerts"></a>Understøttelse af pakke beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Oprettelse af support-pakken mislykkedes.|StorSimple ikke kunne oprette pakken.|Udføre handlingen igen. Hvis problemet fortsætter, skal du kontakte Microsoft Support. Når du har løst problemet, skal du fjerne markeringen i denne besked fra siden beskeder.|

### <a name="update-alerts"></a>Opdatere beskeder

|Beskeder om tekst|Begivenhed|Få mere at vide / anbefalede handlinger|
|:---|:---|:---|
|Programrettelse installeret.|Opdatering af software/firmware er fuldført.|Denne programrettelse er installeret på din enhed.|
|Manuelle opdateringer, der er tilgængelige.|Meddelelse om tilgængelige opdateringer.|Bruge brugergrænsefladen i Windows PowerShell til StorSimple på din enhed til at installere disse opdateringer. <br>Gå til at [opdatere enheden StorSimple 8000 serie](storsimple-update-device.md)kan finde flere oplysninger.|
|Nye opdateringer tilgængelige.|Meddelelse om tilgængelige opdateringer.|Fra siden **vedligeholdelse** eller ved hjælp af brugergrænsefladen i Windows PowerShell til StorSimple på din enhed, kan du installere disse opdateringer. <br>Gå til at [opdatere enheden StorSimple 8000 serie](storsimple-update-device.md)kan finde flere oplysninger.|
|Der kunne ikke installere opdateringer.|Opdateringer blev ikke installeret korrekt.|Systemet kunne ikke installere opdateringerne. Fra siden **vedligeholdelse** eller ved hjælp af brugergrænsefladen i Windows PowerShell til StorSimple på din enhed, kan du installere disse opdateringer. Hvis problemet fortsætter, skal du kontakte Microsoft Support. <br>Gå til at [opdatere enheden StorSimple 8000 serie](storsimple-update-device.md)kan finde flere oplysninger.|
|Til automatisk at søge efter nye opdateringer.|Automatisk kontrol mislykkedes.|Du kan manuelt søge efter nye opdateringer fra siden **vedligeholdelse** .|
|Ny WUA agent tilgængelig.|Meddelelse om tilgængelige opdateringer.|Download de nyeste Windows Update-Agent, og installer det fra Windows PowerShell-grænseflade.|
|Version af firmware komponent <*komponent-ID*> stemmer ikke overens med hardware.|Firmware opdateringer blev ikke installeret korrekt.|Kontakt Microsoft Support.|

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple fejl og fejlfinding i forbindelse med en funktionel enhed](storsimple-troubleshoot-operational-device.md).

