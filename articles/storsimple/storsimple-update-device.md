<properties
   pageTitle="Opdatere enheden StorSimple | Microsoft Azure"
   description="Forklarer, hvordan du bruger funktionen StorSimple opdateringer til at installere almindelig og vedligeholdelse tilstand opdateringer og hotfixes."
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
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Opdatere enheden StorSimple 8000 serie

## <a name="overview"></a>Oversigt

Funktionerne til StorSimple opdateringer kan du let holde enheden StorSimple opdateret. Afhængigt af opdateringstypen, kan du anvende opdateringer til enheden via Azure klassisk portalen eller via Windows PowerShell-grænseflade. I dette selvstudium beskrives typerne opdatering og hvordan du installerer hver af dem.

Du kan anvende to typer af enhedsopdateringer: 

- Almindelige (eller Normal tilstand) opdateringer
- Vedligeholdelse tilstand opdateringer

Du kan installere regelmæssige opdateringer via Azure klassisk portal eller Windows PowerShell men, skal du bruge Windows PowerShell til at installere vedligeholdelse tilstand opdateringer. 

De enkelte opdatering er beskrevet separat, nedenfor.

### <a name="regular-updates"></a>Regelmæssige opdateringer

Regelmæssige opdateringer er ikke-forstyrrende opdateringer, der kan installeres, når enheden er i Normal tilstand. Disse opdateringer anvendes på hver enhed controller via webstedet Microsoft Update. 

> [AZURE.IMPORTANT] En controller failover kan opstå under opdateringsprocessen. Men dette påvirker ikke systemets tilgængelighed eller handling.

- Få mere at vide om, hvordan du installerer regelmæssige opdateringer via Azure klassisk-portalen, i [Installer regelmæssige opdateringer via Azure klassisk portal(#install-regular-updates-via-the-azure-classic-portal).

- Du kan også installere regelmæssige opdateringer via Windows PowerShell til StorSimple. Yderligere oplysninger finder du [installere regelmæssige opdateringer via Windows PowerShell til StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Vedligeholdelse tilstand opdateringer

Vedligeholdelse tilstand opdateringer er forstyrrende som disk firmwareopgraderinger. Disse opdateringer kræver enheden, der skal lægges til vedligeholdelsestilstand. Yderligere oplysninger finder du [trin 2: Angiv vedligeholdelse tilstand](#step2). Du kan ikke bruge Azure klassisk portal til at installere vedligeholdelse tilstand opdateringer. I stedet skal du bruge Windows PowerShell til StorSimple. 

Du kan finde oplysninger om, hvordan du installerer vedligeholdelse tilstand opdateringer, [installere vedligeholdelse tilstand opdateringer via Windows PowerShell til StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Vedligeholdelse tilstand opdateringer skal anvendes særskilt til hver controller. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installere regelmæssige opdateringer via Azure klassisk-portalen

Du kan bruge portalen Azure klassisk installere opdateringer til enheden StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installere regelmæssige opdateringer via Windows PowerShell til StorSimple

Du kan også bruge Windows PowerShell til StorSimple til at anvende almindelig (Normal tilstand) opdateringer.

> [AZURE.IMPORTANT] Selvom du kan installere regelmæssige opdateringer ved hjælp af Windows PowerShell til StorSimple, anbefales det, at du installerer regelmæssige opdateringer via Azure klassisk portalen. Begynder med opdatering 1, udføres foreløbig kontrol før du installerer opdateringer fra portalen. Disse foreløbige Kontroller skal tilsidesætte fejl og sikre en jævnere oplevelse. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installere vedligeholdelse tilstand opdateringer via Windows PowerShell til StorSimple

Du kan bruge Windows PowerShell til StorSimple til at anvende vedligeholdelse tilstand opdateringer til enheden StorSimple. Alle i/o-anmodninger er standset i denne tilstand. Tjenester som permanente-RAM (NVRAM) eller den klyngetjeneste er stoppet. Begge enheder er genstartet, når du indtaster eller afslutte denne tilstand. Når du afslutter denne tilstand, fortsætter alle tjenesterne, og du skal være sund. (Det kan tage et par minutter).

Hvis du vil anvende vedligeholdelse tilstand opdateringer, får du en besked via Azure klassisk administrationsportalen, at du har opdateringer, der skal være installeret. Denne besked indeholder vejledning til brug af Windows PowerShell til StorSimple til at installere opdateringerne. Når du opdaterer din enhed, kan du bruge den samme fremgangsmåde til at ændre enheden til almindelige tilstand. Finde en trinvis vejledning [trin 4: Afslut vedligeholdelse tilstand](#step4).

> [AZURE.IMPORTANT] 
> 
> - Kontroller, at begge enhed enheder er sund ved at kontrollere **Hardware Status** på siden **vedligeholdelse** i portalen Azure klassisk før redigeringstilstand vedligeholdelse. Hvis controlleren ikke er sund, skal du kontakte Microsoft Support til de næste trin. Gå til Kontakt Microsoft Support kan finde flere oplysninger. 
> - Når du er i vedligeholdelsestilstand, skal du installere opdateringen, på én controller og derefter klikke på anden controlleren.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Trin 1: Oprette forbindelse til seriel konsollen<a name="step1">

Først skal Brug et program som trykfarver til at få adgang til seriel konsollen. Følgende procedure forklarer, hvordan du bruger trykfarver til at oprette forbindelse til seriel konsollen.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Trin 2: Angiv Vedligeholdelsestilstand<a name="step2">

Når du opretter forbindelse til konsollen, kan du kontrollere, om der er opdateringer til at installere, og Angiv Vedligeholdelsestilstand for at installere dem.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Trin 3: Installere dine opdateringer<a name="step3">

Dernæst skal installere dine opdateringer.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Trin 4: Afslut Vedligeholdelsestilstand<a name="step4">

Til sidst skal Afslut vedligeholdelsestilstand.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installere hotfixes via Windows PowerShell til StorSimple

I modsætning til opdateringer til Microsoft Azure StorSimple installeres hotfixes fra en delt mappe. Som med opdateringer, er der to typer hotfixes: 

- Almindelige hotfixes 
- Vedligeholdelse tilstand hotfixes  

I det følgende forklares, hvordan du bruger Windows PowerShell til StorSimple almindelig og vedligeholdelse tilstand lynreparationer installeres.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Hvad sker der med opdateringer, hvis du udfører en fabrik nulstilling af enheden?

Hvis en enhed er nulstillet til fabriksindstillingerne, derefter går alle opdateringerne tabt. Når factory-Nulstil enheden er registreret og konfigureret, skal du installerer opdateringer via Azure klassisk portal og/eller Windows PowerShell til StorSimple manuelt. Du kan finde flere oplysninger om factory nulstillet, skal du se [Nulstil enheden til standardindstillingerne](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Næste trin

- Lær mere om at [bruge Windows PowerShell til StorSimple til at administrere din StorSimple enhed](storsimple-windows-powershell-administration.md).
- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
