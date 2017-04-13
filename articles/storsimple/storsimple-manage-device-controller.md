<properties
   pageTitle="Administrere StorSimple enhed enheder | Microsoft Azure"
   description="Lær, hvordan du stopper, genstart, lukke eller nulstille din StorSimple enhed enheder."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Administrere din StorSimple enhed enheder

## <a name="overview"></a>Oversigt

I dette selvstudium beskrives de forskellige handlinger, der kan udføres på din StorSimple enhed enheder. Enheder i enheden StorSimple er overflødige (peer) enheder i en aktiv / passiv konfiguration. Kun én controller er aktiv og behandles alle disk og netværk handlinger på et bestemt tidspunkt. Anden controlleren er i en passiv tilstand. Hvis den aktive controller mislykkes, skal aktiveres den passive controller automatisk.

Dette selvstudium indeholder trinvise instruktioner til at administrere enhed enheder ved hjælp af den:

- **Enheder** sektion på siden **vedligeholdelse** i tjenesten StorSimple Manager
- Windows PowerShell til StorSimple.

Vi anbefaler, at du administrere enhed enheder via tjenesten StorSimple Manager. Hvis en handling kan kun udføres ved hjælp af Windows PowerShell til StorSimple, gør selvstudiet den ned.

Når du har læst dette selvstudium, vil du kunne:

- Genstarte eller lukke en StorSimple enhed controller
- Lukke en StorSimple enhed
- Nulstille enheden StorSimple til fabriksindstillingerne


## <a name="restart-or-shut-down-a-single-controller"></a>Genstarte eller lukke en enkelt controller

Controller genstart eller lukning er ikke påkrævet som en del af normal drift. Lukning handlinger for en enkelt enhed controller er almindelige kun i tilfælde, hvor en mislykkede enhed hardwarekomponent kræver erstatning. Genstart controller muligvis også i en situation, hvor resultater påvirkes af for stor hukommelsesbelastning eller en defekt enhed. Du kan også skal genstarte en controller efter vellykket controller erstatning, hvis du vil aktivere og teste den erstattede controller.

Genstart af en enhed er ikke forstyrrende for forbundne initiatorer, hvis den passive controller er tilgængelig. Hvis en passive controller ikke er tilgængelige eller slukket deaktiveret, og derefter genstarte den aktive controller kan medføre afbrydelse af tjenesten og nedetid.

> [AZURE.IMPORTANT]

> - **En igangværende controller bør aldrig fjernes fysisk, da dette ville resultere i et tab af redundans og øget risiko nedetid.**

> - Følgende fremgangsmåde gælder kun for den fysiske StorSimple-enhed. Du kan finde oplysninger om, hvordan du starte, stoppe og genstarte den virtuelle enhed [fungerer sammen med den virtuelle enhed](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Du kan genstarte eller lukke en enkelt enhed controller ved hjælp af portalen Azure til klassisk StorSimple Manager tjenesten eller Windows PowerShell til StorSimple

For at administrere din enhed enheder fra portalen Azure klassisk skal du udføre følgende trin.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>At genstarte eller lukke en controller i klassisk portal

1. Gå til **enheder > vedligeholdelse**.

1. Gå til **Hardware Status** , og Bekræft, at status over begge enheder, der på enheden er i **orden**.

    ![Kontrollér StorSimple enhed enheder er sund](./media/storsimple-manage-device-controller/IC766017.png)

1. Fra bunden af siden **vedligeholdelse** , skal du klikke på **Administrer enheder**.

    ![Administrere StorSimple enhed enheder](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Hvis du ikke kan se **Administrere enheder**, skal du installere opdateringer. Se [opdatere enheden StorSimple](storsimple-update-device.md)kan finde flere oplysninger.

1. I dialogboksen **Skift Controller indstillinger** skal du gøre følgende:
    1. Vælg den controller, som du vil administrere rullelisten **Select Controller** . Indstillingerne er Controller 0 og 1 Controller. Disse controller identificeres som aktiv eller passiv.

        >[AZURE.NOTE] En controller kan ikke administreres, hvis det er ikke tilgængelig eller slukket deaktiveret, og det vises ikke i på rullelisten.

    2. Vælg **genstarte controller** eller **lukke controller**på rullelisten **Vælg handling** .

        ![Genstart StorSimple enhed passive controller](./media/storsimple-manage-device-controller/IC766020.png)
    3. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-device-controller/IC740895.png).

Dette vil genstarte eller lukke controlleren. Tabellen nedenfor opsummerer oplysninger om, hvad der sker afhængigt af de valg, du har foretaget i dialogboksen **Skift Controller indstillinger** .  


|Markering #|Hvis du vælger at...|Dette sker.|
|---|---|---|
|1.|Genstart passive controller.|Der oprettes et job for at genstarte controlleren, og du får besked, når jobbet er blevet oprettet. Dette starter controller genstart. Du kan overvåge genstartsprocessen ved at få adgang til **tjeneste > Dashboard > få vist logfilerne for handlingerne** og derefter filtrering efter parametre, der er specifikke for din tjeneste.|
|2.|Genstart den aktive controller.|Du får vist følgende advarsel: "Hvis du har genstartet den aktive controller, enheden, ikke den passive controller. Du vil fortsætte?" </br>Hvis du vælger at fortsætte med denne handling, de næste trin bliver identisk med dem, der bruges til at genstarte den passive controller (se markering 1).|
|3.|Luk den passive controller.|Du får vist følgende meddelelse: "når lukning er fuldført, du skal push knappen power på din controller aktiveres. Er du sikker på, at du vil lukke denne controller?" </br>Hvis du vælger at fortsætte med denne handling, de næste trin bliver identisk med dem, der bruges til at genstarte den passive controller (se markering 1).|
|4.|Lukke det aktive controller.|Du får vist følgende meddelelse: "når lukning er fuldført, du skal push knappen power på din controller aktiveres. Er du sikker på, at du vil lukke denne controller?" </br>Hvis du vælger at fortsætte med denne handling, de næste trin bliver identisk med dem, der bruges til at genstarte den passive controller (se markering 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>At genstarte eller lukke en controller i Windows PowerShell til StorSimple
Udfør følgende trin for at lukke eller genstarte en enkelt controller på enheden StorSimple fra Azure klassisk portal.


1. Få adgang til enheden ved hjælp af seriel konsollen eller en telnet-session fra en fjerncomputer. Oprette forbindelse til Controller 0 eller 1 Controller ved at følge trinnene i [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol.

1. I meddelelsen banner skal du notere den controller, du har forbindelse til (Controller 0 eller 1 Controller) og om det er aktivt eller passive (standby) controller.
    - For at lukke en enkelt controller, når du bliver spurgt, Skriv:

        `Stop-HcsController`

        Dette lukkes den controller, som du har forbindelse til. Hvis du stopper den aktive controller, derefter kan den ikke den passive controller før den lukkes.
    - For at genstarte en controller, når du bliver spurgt, Skriv:

        `Restart-HcsController`

        Dette vil genstarte den controller, som du har forbindelse til. Hvis du har genstartet den aktive controller, mislykkes til den passive controller før genstart.


## <a name="shut-down-a-storsimple-device"></a>Lukke en StorSimple enhed

Dette afsnit beskriver, hvordan du lukker en løbende eller en mislykkedes StorSimple enhed fra en fjerncomputer. En enhed er slået fra, når du lukker begge enhed enheder. En enhed lukning sker, når enheden er flyttes fysisk eller benyttes af tjenesten.

> [AZURE.IMPORTANT] Før du lukker enheden, kontrollerer du tilstanden af komponenterne til enhed. Gå til **enheder > vedligeholdelse > Hardware Status** og kontrollere, at LED status for alle komponenter er grønne. Kun en sund enhed har en grøn status. Hvis din enhed lukkes ned for at erstatte en defekt komponent, kan du se en mislykkedes (rød) eller en nedgraderede (gul) status for de respektive komponenter.

#### <a name="to-shut-down-a-storsimple-device"></a>Lukke en StorSimple enhed

1. Brug [genstarte eller lukke en controller](#restart-or-shut-down-a-single-controller) fremgangsmåde til at identificere og lukke den passive controller på din enhed. Du kan udføre denne handling i portalen Azure klassisk eller i Windows PowerShell til StorSimple.
2. Gentage ovenstående trin til at lukke den aktive controller.
3. Nu skal du se på det tilbage plan på enheden. Når de to enheder er helt lukket, blinker statusindikatorer på begge enheder skal rød. Hvis du vil slå fra enheden helt på nuværende tidspunkt skal du vende power parametre på både Power og køling moduler (PCMs) til positionen fra. Dette bør slå enheden.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Nulstille enheden til standardindstillingerne

> [AZURE.IMPORTANT] Hvis du vil nulstille din enhed til standardindstillingerne, skal du kontakte Microsoft Support. Nedenstående fremgangsmåde skal bruges kun i forbindelse med Microsoft Support.

Denne procedure beskriver, hvordan du nulstiller enheden Microsoft Azure StorSimple til standardindstillingerne ved hjælp af Windows PowerShell til StorSimple.
Nulstille en enhed, fjerner alle data og indstillinger fra hele klynge som standard.

Udfør følgende trin for at nulstille enheden Microsoft Azure StorSimple til standardindstillingerne:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Nulstille enheden til standardindstillingerne i Windows PowerShell for StorSimple

1. Få adgang til enheden gennem dens Seriel konsol. Markér banner meddelelsen for at sikre, at du har forbindelse til den aktive controller.

1. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol.

1. Når du bliver spurgt, skal du skrive følgende kommando for at nulstille den hele klynge, fjerne alle data, metadata og controller indstillinger:

    `Reset-HcsFactoryDefault`

    Hvis du vil nulstille i stedet for en enkelt controller, Brug cmdlet'en [Nulstil HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) med den `-scope` parameter.)

    Systemet genstartes flere gange. Du får besked, når afkrydsningsfeltet Nulstil er fuldført. Afhængigt af systemmodellen, kan det tage 45-60 minutter i en 8100 enhed og 60-90 minutter til en 8600 at afslutte denne proces.

    > [AZURE.TIP]

    > - Hvis du bruger opdatering 1.2 eller tidligere bruger den `–SkipFirmwareVersionCheck` parameter for at springe kontrollen firmware version (Ellers får du vist fejlen firmware uoverensstemmelse: Factory Nulstil kan ikke fortsætte på grund af en uoverensstemmelse i firmwareversioner. ).

    > - Factory Nulstil procedure kan mislykkes på StorSimple enheder, der kører opdatering 1 eller 1.1 i portalen Government og har udført en vellykket enkelt eller dobbelt controller erstatning (med erstatning enheder, der fulgte med før opdatering 1 software). Dette sker, når factory Nulstil billede er godkendt til tilstedeværelsen af en SHA1-fil på den controller, som ikke findes for før opdatering 1 software. Hvis du ser denne factory nulstille fejl, skal du kontakte Microsoft Support for at hjælpe dig med at de næste trin. Dette problem kan ikke ses med erstatning enheder, som blev sendt fra fabrik med opdatering 1 eller nyere.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Spørgsmål og svar om administration af enhed enheder

I dette afsnit, har vi opsummeres nogle af de ofte stillede spørgsmål om administration af StorSimple enhed enheder.

**Q.** Hvad sker der, hvis begge enheder på min enhed er sund og deaktiveret på og jeg genstarte eller lukke den aktive controller?

**A.** Hvis begge enheder på enheden er sund og deaktiveret, bliver du bedt om at bekræfte. Du kan vælge at:

- **Genstart den aktive controller** – du får besked, ved at genstarte en aktive controller medfører, at kameraet, for at skifte til den passive controller. Controlleren genstartes.

- **Lukke en aktiv controller** – du får besked, at lukke en aktiv controller medfører nedetid. Du skal også push knappen power på kameraet, for at slå controlleren.

**Q.** Hvad sker der, hvis den passive controller på min enhed er ikke tilgængelig eller deaktiveret væk fra, og jeg genstarte eller lukke den aktive controller?

**A.** Hvis den passive controller på enheden er ikke tilgængelig eller slukket deaktiveret, og du vælger at:

- **Genstart den aktive controller** – du får besked, fortsætter med handlingen medfører en midlertidig afbrydelse af tjenesten, og du bliver bedt om at bekræfte.

- **Lukke en aktiv controller** – du får besked, fortsætter med handlingen medfører nedetid og, skal du trykke på power-knappen på en eller begge enheder til tænd for enheden. Du bliver bedt om at bekræfte.

**Q.** Når betyder controller genstart eller lukning ikke kan gå videre?

**A.** Genstart af eller lukke en controller mislykkes muligvis, hvis:

- En enhed opdatering er i gang.

- Genstart controller er allerede i gang.

- En controller lukning er allerede i gang.

**Q.** Hvordan kan du finde ud af, hvis en controller blev genstartes eller lukkes?

**A.** Du kan kontrollere controller status på siden vedligeholdelse. Controller statussen vil angive, om en controller har genstartes eller lukkes. Siden beskeder indeholder også en informativ besked, hvis controlleren blev genstartes eller lukkes. Handlingerne controller genstart og lukning også er registreret i loggene på handlingen. Gå til at [få vist loggene handling](storsimple-service-dashboard.md#view-the-operations-logs)kan finde flere oplysninger om handlingen logfiler.

**Q.** Er der nogen betydning for i/O'er som et resultat af controller failover?

**A.** TCP-forbindelser mellem initiatorer og aktive controller nulstilles som et resultat af controller failover, men gendannes, når den passive controller antages det handling. Der kan være en midlertidig (mindre end 30 sekunder) pause i i/o-aktivitet mellem initiatorer og enheden i løbet af denne handling.

**Q.** Hvordan kommer jeg tilbage min controller til at vedligeholde, når den er lukket ned og fjernet?

**A.** Hvis du vil returnere en controller til tjenesten, skal du indsætte den i kabinettet som beskrevet i [erstatte et controller modul på enheden StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Næste trin

- Hvis du støder på problemer med din StorSimple enhed enheder, som du ikke kan løse ved hjælp af de fremgangsmåder, der er angivet i dette selvstudium [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md).

- Hvis du vil vide mere om ved hjælp af tjenesten StorSimple Manager skal du gå til [Brug tjenesten StorSimple Manager til at administrere enheden StorSimple](storsimple-manager-service-administration.md).
