<properties 
   pageTitle="Erstatte en StorSimple enhed controller | Microsoft Azure"
   description="Beskriver, hvordan du fjerner og erstatter en eller begge controller moduler på enheden StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Erstatte et controller modul på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du fjerner og erstatter en eller begge controller moduler i en StorSimple enhed. Det beskriver også den underliggende logik for de enkelte og dobbelte controller erstatning scenarier.

>[AZURE.NOTE] Før du udfører controller erstatning, anbefaler vi, at du altid opdaterer din controller firmware til den nyeste version.
>
>Hvis du vil forhindre skader på enheden StorSimple, ikke fjerner controlleren indtil LED-indikatorerne vises som et af følgende:
>
>- Alle lys er fra.
>- LED 3 ![grøn markering ikonet](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), og ![rødt kryds ikonet](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) er blinker, og LED 0 og LED 7 er **på**.

Den følgende tabel viser de understøttede controller erstatning scenarier.

|Store og små bogstaver|Erstatning scenarie|Det er relevant procedure|
|:---|:-------------------|:-------------------|
|1|Én controller i en fejltilstand, og anden controlleren er sund og aktive.|[Enkelt controller erstatning](#replace-a-single-controller), som beskriver [logik bag en enkelt controller erstatning](#single-controller-replacement-logic)samt [erstatning trin](#single-controller-replacement-steps).|
|2|Begge enheder har mislykkedes og udskiftes. Kabinettet, disk and.disk omslutning er sund.|[Skærmdump af dobbelt controller erstatning](#replace-both-controllers), som beskriver [logik bag en dobbelt controller erstatning](#dual-controller-replacement-logic)samt [erstatning trin](#dual-controller-replacement-steps). |
|3|Er skiftet enheder fra den samme enhed eller forskellige enheder. Kabinettet, diske og disk bilag er sund.|Advarselsmeddelelse en slot uoverensstemmelse vises.|
|4|Én controller mangler, og anden controlleren mislykkes.|[Skærmdump af dobbelt controller erstatning](#replace-both-controllers), som beskriver [logik bag en dobbelt controller erstatning](#dual-controller-replacement-logic)samt [erstatning trin](#dual-controller-replacement-steps).|
|5|En eller begge enheder mislykkedes. Du kan ikke få adgang til enheden via seriel konsol eller Windows PowerShell remoting.|[Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) af en manuel controller erstatning procedure.|
|6|Enheder har en anden build-version, som muligvis skyldes:<ul><li>Enheder har en anden softwareversion.</li><li>Enheder har en anden firmwareversion.</li></ul>|Hvis controller softwareversioner er forskellige, erstatning logik registrerer, og opdaterer softwareversion på erstatning controller.<br><br>Hvis controller firmwareversioner er forskellige, og den gamle firmwareversion er **ikke** automatisk kan opgraderes, vises der en meddelelse i portalen Azure klassisk. Du skal søge efter opdateringer og installere opdateringerne til firmware.</br></br>Hvis controller firmwareversioner er forskellige, og den gamle firmwareversion kan automatisk opgraderes, controller erstatning logik registrerer dette, og når controlleren er startet, firmware vil automatisk blive opdateret.|

Du vil fjerne et controller modul, hvis det mislykkes. En eller begge controller-moduler kan mislykkes, hvilket kan medføre en enkelt controller erstatning eller dobbelt controller erstatning. Erstatning procedurer og logik bag dem ved at se følgende:

- [Erstatte en enkelt controller](#replace-a-single-controller)
- [Erstatte begge enheder](#replace-both-controllers)
- [Fjerne en controller](#remove-a-controller)
- [Indsætte en controller](#insert-a-controller)
- [Identificere den aktive controller på din enhed](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Gennemgå oplysningerne om sikkerhed i [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md)før fjerne og erstatte en controller.

## <a name="replace-a-single-controller"></a>Erstatte en enkelt controller

Når en af de to enheder på Microsoft Azure StorSimple enheden mislykkes, der fungerer forkert eller mangler, skal du erstatte en enkelt controller. 

### <a name="single-controller-replacement-logic"></a>Enkelt controller erstatning logik

I en enkelt controller erstatning, skal du først fjerne den mislykkede controller. (Den resterende controller i enheden er den aktive controller). Når du indsætter erstatning controlleren, sker der følgende handlinger:

1. Erstatning controller starter med det samme kommunikation med StorSimple enheden.

2. Et øjebliksbillede af den virtuelle harddisk (Virtuelle) til den aktive controller kopieres på erstatning controller.

3. Snapshot er ændret, så når erstatning controller starter fra denne Virtuelle, den genkendes som standby controller.

4. Når ændringerne er fuldført, starter erstatning controlleren som standby controller.

5. Når begge enheder, der kører, bliver klyngen online.

### <a name="single-controller-replacement-steps"></a>Enkelt controller erstatning trin

Du kan udføre følgende trin, hvis en af enheder i enheden Microsoft Azure StorSimple mislykkes. (Anden controlleren skal være aktiv og køre. Hvis begge enheder mislykkes eller funktionsfejl, gå til [dobbelt controller erstatning trinnene](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Det kan tage 30-45 minutter til controller at genstarte og gendanne helt fra enkelt controller erstatning procedure. Den samlede tid for hele proceduren, er herunder vedhæfte kablerne cirka to timer.

#### <a name="to-remove-a-single-failed-controller-module"></a>Hvis du vil fjerne en enkelt mislykkedes controller modul

1. Gå til tjenesten StorSimple Manager i Azure klassisk portalen, klik på fanen **enheder** , og klik derefter på navnet på den enhed, du vil overvåge.

2. Gå til **vedligeholdelse > Hardware Status**. Status for enten Controller 0 eller 1 Controller skal være rød, hvilket angiver en fejl.

    >[AZURE.NOTE] Den mislykkede controller i en enkelt controller erstatning er altid en standby controller.

3. Brug figur 1 og den følgende tabel for at finde modulet mislykkedes controller.  

    ![Backplane af enhed primære omslutning moduler](./media/storsimple-controller-replacement/IC740994.png)

    **Figur 1** Bagsiden af StorSimple enhed

  	|Etiket|Beskrivelse|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controller 0|
  	|4|Controller 1|

4. Fjerne alle forbundne netværkskablerne fra data portene på den mislykkede controller. Hvis du bruger en 8600 model, også fjerne de SAS kabler, der forbinder controlleren til EBOD controller.

5. Følg trinnene i [fjerne en controller](#remove-a-controller) til at fjerne den mislykkede controller. 

6. Installere factory erstatning i det samme stik, hvorfra mislykkedes controller er blevet fjernet. Dette udløser enkelt controller erstatning logik. Du kan finde yderligere oplysninger finder [enkelt controller erstatning logik](#single-controller-replacement-logic).

7. Mens enkelt controller erstatning logik skrider frem i baggrunden, Genopret kablerne. Sørg for at oprette forbindelse alle kabler præcis på samme måde, at de er tilsluttet før erstatning.

8. Når controlleren genstarter, skal du se **Controller status** og den **klynge status** på Azure klassisk portalen for at bekræfte, at controlleren er tilbage til tilstanden sund og er i standby.

>[AZURE.NOTE] Hvis du overvåger enhed via seriel konsollen, kan du se flere genstarter, mens controlleren gendanne fra fremgangsmåden erstatning. Når menuen Seriel konsol præsenteres, så du ved, erstatning er fuldført. Hvis menuen ikke vises i to timer af at starte controller erstatning, kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Erstatte begge enheder

Når begge enheder på Microsoft Azure StorSimple enheden er mislykket, er korrekt, eller mangler, skal du erstatte begge enheder. 

### <a name="dual-controller-replacement-logic"></a>Skærmdump af dobbelt controller erstatning logik

I to controller erstatning, skal du først fjerne begge mislykkedes enheder og derefter indsætte erstatninger. Når to erstatning enheder er indsat, sker der følgende handlinger:

1. Erstatning controller i slot 0 kontrollerer følgende:
 
   1. Bruger det aktuelle versioner af firmware og software?

   2. Er det en del af klyngen?

   3. Er den peer-controller, kører og er de grupperede?
                            
    Hvis ingen af disse betingelser er sande, ser controlleren ud for den seneste daglige sikkerhedskopiering (findes i **nonDOMstorage** på drev S). Controlleren kopierer den seneste øjebliksbillede af den virtuelle harddisk fra sikkerhedskopien.

2. Controller i slot 0 bruger snapshot til billede for sig selv.

3. I mellemtiden venter controller i stik 1 controller 0 til at fuldføre afbildning og starte.

4. Når controller 0 er startet, registrerer controller 1 den klynge, der er oprettet af controller 0, der udløser enkelt controller erstatning logik. Du kan finde yderligere oplysninger finder [enkelt controller erstatning logik](#single-controller-replacement-logic).

5. Herefter skal køre begge enheder og klyngen kommer online.

>[AZURE.IMPORTANT] Følgende to controller erstatning, når StorSimple enheden er konfigureret, er det vigtigt, at du foretager en manuel sikkerhedskopiering af enheden. Daglig sikkerhedskopier af enhed konfigurationen er ikke udløst indtil efter 24 timer er gået. Arbejde med [Microsoft Support](storsimple-contact-microsoft-support.md) for at foretage en manuel sikkerhedskopi af din enhed.

### <a name="dual-controller-replacement-steps"></a>Skærmdump af dobbelt controller erstatning trin

Arbejdsprocessen er påkrævet, når begge over enheder i enheden Microsoft Azure StorSimple har ikke. Det kan ske i et datacenter, køling systemet går ned, hvor, og som et resultat på begge enheder mislykkes inden for kort tid. Afhængigt af om StorSimple enheden er slået til eller fra, og om du bruger en 8600 eller en 8100 model, er en anden række trin er påkrævet.

>[AZURE.IMPORTANT] Det kan tage 45 minutter til 1 hour til controller at genstarte og gendanne helt fra en dobbelt controller erstatning procedure. Den samlede tid for hele proceduren, er herunder vedhæfte kablerne omkring 2,5 timer.

#### <a name="to-replace-both-controller-modules"></a>Erstatte begge controller moduler

1. Hvis enheden er slået fra, du springe dette trin over og gå videre til næste trin. Hvis enheden er slået til, slå fra enheden.
                                        
    1. Hvis du bruger en 8600 model, slå fra den primære omslutning først, og deaktiver derefter EBOD omslutning.

    2. Vent, indtil enheden er lukket ned helt. Alle indikatorer bag ved enheden, bliver fra.

2. Fjerne alle netværkskablerne, der er forbundet til dataporte. Hvis du bruger en 8600 model, også fjerne de SAS kabler, der forbinder de primære omslutning til EBOD omslutning.

3. Fjerne begge enheder fra StorSimple enheden. Yderligere oplysninger finder du se [fjerne en controller](#remove-a-controller).

4. Indsætte factory erstatningen for Controller 0 først og derefter indsætte Controller 1. Du kan finde flere oplysninger, kan du se [indsætte en controller](#insert-a-controller). Dette udløser dobbelt controller erstatning logik. Du kan finde yderligere oplysninger finder [dobbelt controller erstatning logik](#dual-controller-replacement-logic).

5. Mens controller erstatning logik skrider frem i baggrunden, Genopret kablerne. Sørg for at oprette forbindelse alle kabler præcis på samme måde, at de er tilsluttet før erstatning. Se de detaljerede instruktioner til din model kablet din enhed sektion af [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Slå StorSimple enhed. Hvis du bruger en 8600 model:

    1. Sørg for, at EBOD omslutning er slået til første.

    2. Vent, indtil EBOD omslutning kører.

    3. Slå den primære omslutning.

    4. Efter den første controller genstarter og er i en sund tilstand, skal systemet køre.

    >[AZURE.NOTE] Hvis du overvåger enhed via seriel konsollen, kan du se flere genstarter, mens controlleren gendanne fra fremgangsmåden erstatning. Når menuen Seriel konsol vises, så du ved, erstatning er fuldført. Hvis menuen ikke vises i 2,5 timer af at starte controller erstatning, kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Fjerne en controller

Brug følgende fremgangsmåde til at fjerne et fejlbehæftede controller modul fra enheden StorSimple.

>[AZURE.NOTE] Der findes følgende illustrationer til controller 0. Til controller 1 ville disse kan fortrydes.

#### <a name="to-remove-a-controller-module"></a>Fjerne et controller modul

1. Forstå modul låsen mellem din rulleboks og pege.

2. Let disse dine rulleboks og pege sammen til holderen controller.

    ![Frigive controller låsen](./media/storsimple-controller-replacement/IC741047.png)

    **Figur 2** Frigive controller låsen

2. Bruge låsen som et håndtag til slide controller af kabinettet.

    ![Hvis du flytter controller af kabinet](./media/storsimple-controller-replacement/IC741048.png)

    **Figur 3** Hvis du flytter controller af kabinettet

## <a name="insert-a-controller"></a>Indsætte en controller

Benyt følgende fremgangsmåde for at installere et factory-leveret controller modul, når du fjerner et fejlbehæftede modul fra enheden StorSimple.

#### <a name="to-install-a-controller-module"></a>Installere et controller modul

1. Kontrollér, om der er skader på interface forbindelser. Installer ikke modulet, hvis nogen af connector ben er beskadiget eller bøjet.

2. Skub modulet controller i kabinettet, mens låsen frigives fuldt ud. 

    ![Hvis du flytter controller til kabinet](./media/storsimple-controller-replacement/IC741053.png)

    **Figur 4** Hvis du flytter controller i kabinettet

3. Starte lukke låsen mens du fortsætter med at push modulet controller i kabinettet controller modulet indsat. Låsen kan deltage til vejledning controlleren til det ønskede sted.

    ![Lukke controller låsen](./media/storsimple-controller-replacement/IC741054.png)

    **Figur 5** Lukke controller låsen

4. Du er færdig, når låsen fastgøres til det ønskede sted. **OK** LED skulle nu være på.  

    >[AZURE.NOTE] Det kan tage op til 5 minutter, før controlleren, og LED at aktivere.

5. For at bekræfte, at erstatningen er gået igennem i portalen Azure klassisk, gå til **enheder**for > **vedligeholdelse** > **Hardware Status**, og Sørg for, at både controller 0 og 1-controller er sund (status er grøn).

## <a name="identify-the-active-controller-on-your-device"></a>Identificere den aktive controller på din enhed

Der er mange situationer, som enhed førstegangsbrugere registrering eller controller erstatning, som kræver, at finde den aktive controller på en enhed med StorSimple. Den aktive controller behandler alle disk firmware og netværk operationer. Du kan bruge en af følgende metoder til at identificere den aktive controller:

- [Bruge Azure klassisk portal til at identificere den aktive controller](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Bruge Windows PowerShell til StorSimple til at identificere den aktive controller](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Markér den fysiske enhed for at identificere den aktive controller](#check-the-physical-device-to-identify-the-active-controller)

Hver af disse procedurer er beskrevet ud.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Bruge Azure klassisk portal til at identificere den aktive controller

Gå til **enheder**på Azure-klassisk portalen > **vedligeholdelse**, og Rul til sektionen **enheder** . Her kan du kontrollere, hvilke controller er aktiv.

![Identificere aktive controller i Azure klassisk portal](./media/storsimple-controller-replacement/IC752072.png)

**Figur 6** Azure klassisk portal, der viser den aktive controller

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Bruge Windows PowerShell til StorSimple til at identificere den aktive controller

Når du får adgang til din enhed via konsollen seriel, vises meddelelsen banner. Banner meddelelsen indeholder grundlæggende enhedsoplysninger som den model, navn, installeret softwareversion og status for den controller, du får adgang til. Følgende billede viser et eksempel på en banner meddelelse:

![Seriel banner meddelelse](./media/storsimple-controller-replacement/IC741098.png)

**Figur 7** Banneret meddelelse viser controller 0 som aktiv

Du kan bruge banner meddelelsen til at afgøre, om den controller, du har forbindelse til er aktiv eller passiv.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Markér den fysiske enhed for at identificere den aktive controller

Til at identificere den aktive controller på din enhed, Find blå FØRT oven over DATAENE 5 port på bagsiden af den primære omslutning.

Hvis denne LED blinker, controlleren er aktiv, og anden controlleren er i standby tilstand. Brug følgende diagram og tabel som en hjælp.

![Enhed primære omslutning backplane med dataporte](./media/storsimple-controller-replacement/IC741055.png)

**Figur 8** Bagsiden af primære omslutning med dataporte og overvågning indikatorer

|Etiket|Beskrivelse|
|:----|:----------|
|1-6|DATA 0 – 5 netværksporte|
|7|Blå led-Indikator|


## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardware komponent erstatning](storsimple-hardware-component-replacement.md).
