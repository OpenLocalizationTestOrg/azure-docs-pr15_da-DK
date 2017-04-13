<properties 
   pageTitle="StorSimple Snapshot Manager MMC menuen Handlinger | Microsoft Azure"
   description="Beskriver, hvordan du bruger de almindelige Microsoft Management Console (MMC) menuen Handlinger i StorSimple øjebliksbillede Manager."
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
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Bruge MMC menuen Handlinger i StorSimple øjebliksbillede Manager

## <a name="overview"></a>Oversigt

I StorSimple øjebliksbillede Manager, vil du se de følgende handlinger, der er angivet på alle handling menuer og alle variationer af ruden **Handlinger** . 

- Få vist
- Nyt vindue herfra 
- Opdater 
- Eksportere liste 
- Hjælp 

Disse handlinger er en del af Microsoft Management Console (MMC) og er ikke specifikke til StorSimple øjebliksbillede Manager. Dette selvstudium beskriver disse handlinger, og hvordan du bruger hver af dem i StorSimple øjebliksbillede Manager.

## <a name="view"></a>Få vist

Du kan bruge indstillingen **Vis** til at ændre visningen **resultater** ruden og ændre visningen console-vinduet. 

#### <a name="to-change-the-results-pane-view"></a>Ændre visningen resultater rude

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på en node eller Udvid noden og skal du højreklikke på et element i ruden **resultater** , og klik derefter på indstillingen **Vis** i ruden **omfang** . 

3. Hvis du vil tilføje eller fjerne de kolonner, der vises i ruden **resultater** , skal du klikke på **Tilføj/fjern kolonner**. Dialogboksen **Tilføj/fjern kolonner** vises.

    ![Tilføje eller fjerne kolonner fra ruden med søgeresultater](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Udfyld formularen på følgende måde:

    - Vælg elementer på listen **tilgængelige** kolonner, og klik på **Tilføj** for at føje dem til listen **viste kolonner** . 

    - Klik på elementer på listen **viste kolonner** , og klik på **Fjern** for at fjerne dem fra listen. 

    - Vælg et element på listen **viste** kolonner, og klik på **Flyt op** eller **Flyt ned** for at flytte elementet op eller ned på listen. 

    - Klik på **Gendan standardindstillinger** at vende tilbage til standard **resultater** ruden konfigurationen. 

5. Når du er færdig med at vælge indstillinger, klik på **OK**. 

#### <a name="to-change-the-console-window-view"></a>Ændre visningen console-vinduet

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på en node i ruden **omfang** , klik på **Vis**, og klik derefter på **Tilpas**. Dialogboksen **Tilpas** vises.

    ![Tilpasse console-vinduet](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Markér eller fjern markeringen i afkrydsningsfelterne for at vise eller skjule elementer i vinduet console. Når du er færdig med at vælge indstillinger, klik på **OK**.

## <a name="new-window-from-here"></a>Nyt vindue herfra

Du kan bruge indstillingen **Nyt vindue herfra** til at åbne et nyt console-vindue.

#### <a name="to-open-a-new-console-window"></a>Åbne et nyt console-vindue

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på en hvilken som helst node i ruden **omfang** , og klik derefter på **Nyt vindue herfra**. 

    Der vises et nyt vindue, der viser kun området, du har valgt. Hvis du højreklikker på noden **Sikkerhedskopiering politikker** , det nye vindue for eksempel vises kun noden **Politikker for sikkerhedskopiering** i ruden **område** og en liste over definerede sikkerhedskopiering politikker i ruden **resultater** . Se eksemplet nedenfor.

    ![Nyt vindue herfra](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Opdater

Du kan bruge handlingen **Opdater** opdatere vinduet console.

#### <a name="to-update-the-console-window"></a>Opdatere vinduet console

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på en hvilken som helst node i ruden **omfang** eller Udvid noden og højreklik på et element i ruden **resultater** , og derefter klikke på **Opdater**. 

## <a name="export-list"></a>Eksportere liste

Du kan bruge handlingen **Eksporter listen** til at gemme en liste i en fil med kommaseparerede værdier (CSV). For eksempel kan du eksportere listen over sikkerhedskopiering politikker eller kataloget til sikkerhedskopiering. Du kan derefter importere csv-filen til et regnearksprogram til analyse.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Gemme en liste i en fil med kommaseparerede værdier (CSV)

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Højreklik på en hvilken som helst node i ruden **omfang** eller Udvid noden og skal du højreklikke på et element i ruden **resultater** , og klik derefter på **Eksporter listen**. 

3. Dialogboksen **Eksporter listen** vises. Udfyld formularen på følgende måde: 

    1. Skriv et navn til CSV-filen i **feltet filnavn,** eller klik på pilen til at vælge på rullelisten.

    2. Klik på pilen i feltet **Filtype** , og vælg en filtype på rullelisten.

    3. For at gemme kun valgte elementer skal du markere rækkerne, og klik derefter på afkrydsningsfeltet **Gem kun markerede rækker** . For at gemme alle eksporterede lister skal du fjerne markeringen i afkrydsningsfeltet **Gem kun markerede rækker** .

    4. Klik på **Gem**.

    ![Eksportere liste som en fil med kommaseparerede værdier](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Hjælp

Du kan bruge menuen **Hjælp** til at få vist tilgængelig online Hjælp til StorSimple af snapshots og MMC.

#### <a name="to-view-available-online-help"></a>Sådan får du vist tilgængelige onlinehjælp

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på en node i ruden **omfang** eller Udvid noden og skal du højreklikke på et element i ruden **resultater** , og klik derefter på **Hjælp**. 

## <a name="next-steps"></a>Næste trin

- Lær mere om [brugergrænsefladen StorSimple øjebliksbillede Manager](storsimple-use-snapshot-manager.md).
- Lær mere om at [bruge StorSimple Snapshot Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
