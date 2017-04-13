<properties 
   pageTitle="StorSimple hardware-komponent erstatning | Microsoft Azure"
   description="Beskriver, hvordan du sikkert erstatte den PCMs, batteri, controller moduler, EBOD enheder, diskdrev og towerserverkabinet en StorSimple enhed."
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
   ms.workload="TBD"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>StorSimple hardware-komponent erstatning

## <a name="overview"></a>Oversigt

Hardware-komponent erstatning selvstudier beskriver hardwarekomponenterne i enheden Microsoft Azure StorSimple 8000 serie og de trin, der er nødvendige for at fjerne og erstatte dem. I denne artikel beskrives ikonerne, sikkerhed, indeholder markører til detaljerede selvstudier og viser en liste over de komponenter, der pære.

>[AZURE.IMPORTANT] Før du forsøger at fjerne eller erstatte en hvilken som helst StorSimple komponent, Sørg for, at du gennemser [safety ikonet konventioner](#safety-icon-conventions) og andre [sikkerhedsforanstaltninger](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Sikkerhed ikonet konventioner

I følgende tabel beskrives de safety ikoner, der bruges i disse selvstudier. Læg mærke til disse safety ikoner, mens du gennemgår trinnene for at fjerne og erstatte enhedskomponenter.

| Ikonet | Tekst | Yderligere oplysninger |
|:---- |:---- |:-----------|
|![Advarselsikonet](./media/storsimple-hardware-component-replacement/Warning.png)| **RISIKO!** | Angiver en farlig situation, hvis ikke undgås, vil resultere i dør eller alvorlige skader. Dette signal ord er begrænset til de mest avancerede situationer.|
|![Advarselsikonet](./media/storsimple-hardware-component-replacement/Warning.png)| **ADVARSEL!** | Angiver en farlig situation, hvis ikke undgås, kan det resultere i dør eller alvorlige skader.|
|![Advarselsikon](./media/storsimple-hardware-component-replacement/Caution.png)| **ADVARSEL!** |Angiver en farlig situation, hvis ikke undgås, kan det resultere i mindre eller moderat skade.|
|![Ikon for meddelelse](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **BEMÆRK:** | Angiver betragtes som vigtige, men ikke risiko-relaterede oplysninger.|
|![Elektriske stød-ikon](./media/storsimple-hardware-component-replacement/Electric.png) | **Elektriske stød** | Angiver Højspændingen.|
|![Ikon for tunge](./media/storsimple-hardware-component-replacement/Weight.png)| **Tunge**| |
|![Ikonet ingen bruger brugbare dele](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Ingen bruger brugbare dele** | Ikke har adgang til medmindre korrekt uddannelse.|
|![Læs vejledningen ikon](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Læse alle instruktioner først**| |
|![Tip risiko ikon](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Tip risiko**| |

### <a name="before-you-begin"></a>Inden du går i gang

Lære at kende safety oplysningerne om din enhed og safety ikoner, der bruges i dette selvstudium. Gå til [sikkert at installere og betjene enheden StorSimple](storsimple-safety.md) kan finde detaljerede oplysninger. Sørg for at gennemse de [sikkerhedsforanstaltninger](storsimple-safety.md#handling-precautions) , før du håndtere enheden StorSimple. 

Før du forsøger at erstatte en komponent, skal du overveje følgende oplysninger.

![Advarselsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![elektriske stød ikonet](./media/storsimple-hardware-component-replacement/Electric.png) **Advarsel!** 

- Jordforbindelse selv korrekt ved hjælp af en elektrostatiske afslutning eller antistatic passepartout ved håndtering af moduler og komponenterne i enheden StorSimple.

- Ikke berører en hvilken som helst kredsløb. Brug de medfølgende håndtag og hjælpelinjer under håndtering af komponenter, der muligvis har vises kredsløb.

![Advarselsikon](./media/storsimple-hardware-component-replacement/Warning.png) ![Bemærk ikonet](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **meddelelse:**

Når du erstatter et modul, **Efterlad aldrig en tom plads på bagsiden af omslutning**. Få en erstatnings eller tom modul før du fjerner delen problem.

## <a name="hardware-component-replacement-procedures"></a>Hardware-komponent erstatning procedurer

Enheden StorSimple 8000 serie består af flere plug-in'en moduler i primær og/eller EBOD bilag. 8100 har en enkelt primære omslutning, mens 8600 er en dobbelt omslutning enhed med en primær omslutning og en EBOD omslutning.

De primære hardwarekomponenter i din enhed er opsummeret i følgende tabeller. Klik på linket i kolonnen **erstatning procedure** for at gå til det tilknyttede selvstudium.

|Komponenter|# Præsenter|Plug-in'en modul?|Erstatning procedure
|:---------|:--------|:--------------|:---------------------|
| Kabinet|1|Nej|[Erstatte kabinettet på enheden StorSimple](storsimple-chassis-replacement.md) |
|Primære enheder|2|Ja| [Erstatte et controller modul på enheden StorSimple](storsimple-controller-replacement.md) |
|764W Power og køling moduler (PCMs)|2|Ja| [Erstatte en Power og køling modul på enheden StorSimple](storsimple-power-cooling-module-replacement.md) |
|Ekstra batteri|2|Ja| [Erstat ekstra batterimodul på enheden StorSimple](storsimple-battery-replacement.md) |
|Diskdrev|12|Ja| [Erstatte et diskdrev på enheden StorSimple](storsimple-disk-drive-replacement.md) |

**Tabel 1** Hardwarekomponenter i den primære omslutning

Den primære omslutning og EBOD omslutning variere i deres i/o-moduler. Desuden har PCMs forskellige effekt. PCMs i den primære omslutning er 764 U, mens de EBOD omslutning er 580 W. PCMs i den primære omslutning indeholder også en ekstra batterimodul.

|Komponenter|# Præsenter|Plug-in'en modul?| Erstatning procedure
|:---------|:--------|:--------------|:---------------------|
|Kabinet|1|Nej| [Erstatte kabinettet på enheden StorSimple](storsimple-chassis-replacement.md) |
|EBOD enheder|2|Ja| [Erstatte en EBOD controller på enheden StorSimple](storsimple-ebod-controller-replacement.md) |
|580W ved Power og køling moduler (PCMs)|2|Ja| [Erstatte en Power og køling modul på enheden StorSimple](storsimple-power-cooling-module-replacement.md) |
|Diskdrev|12|Ja| [Erstatte et diskdrev på enheden StorSimple](storsimple-disk-drive-replacement.md) |

**Tabel 2** Hardwarekomponenter i EBOD omslutning

Plug-in'en moduler på enheden er fremhævet i de følgende forsiden og bagsiden diagrammer. Du kan bruge disse diagrammer til at finde placeringen af de forskellige plug-in'en moduler, hvis erstatning er påkrævet. Forside diagrammet viser diskdrev og bag diagrammer over EBOD omslutning og Vis den primære omslutning plug-in'en moduler.

![Frontplane for enhed med diskdrev](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figur 1** Forsiden af enheden

|Etiket|Beskrivelse|
|:----|:----------|
|0 - 11|Diskdrev (i alt 12)|

Både den primære omslutning og EBOD omslutning har drev carrier moduler. Kabinettet indeholder tolv 3,5" diskdrev arrangeret i et 3 x 4-format.

![Backplane af enhed primære omslutning moduler](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figur 2** Bagsiden af den primære omslutning

|Etiket|Beskrivelse|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controller 0|
|4|Controller 1|

![Backplane af enhed EBOD omslutning plug-in'en moduler](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figur 3** Bagsiden af EBOD omslutning

|Etiket|Beskrivelse|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|EBOD Controller 0|
|4|EBOD Controller 1|

## <a name="field-replaceable-units"></a>Feltet pære enheder

De følgende felt pære enheder (FRU'er) er tilgængelige for enheden StorSimple:

- Kabinet (herunder panelet integreret handlinger)

- 764 W AC PCM

- 580 W AC PCM

- Harddisk med drev carrier modul

- Controller modul

- EBOD controller modul

- Ekstra batterimodul

- Rack tilslutte rail kit

Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at sortere en af disse erstatning enheder.

## <a name="next-steps"></a>Næste trin

Gennemgå alle [sikkerhedsoplysninger](storsimple-safety.md) , før du forsøger at erstatte en StorSimple hardware-komponent.
