<properties 
   pageTitle="PowerShell til administration af StorSimple | Microsoft Azure"
   description="Lær at bruge Windows PowerShell til StorSimple til at administrere din StorSimple enhed."
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
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Bruge Windows PowerShell til StorSimple til at administrere din enhed

## <a name="overview"></a>Oversigt

Windows PowerShell til StorSimple har en grænseflade til kommandolinjeparametre, du kan bruge til at administrere din Microsoft Azure StorSimple enhed. Som navnet antyder, er det en baseret på Windows PowerShell, kommandolinjeparametre brugergrænseflade, der er oprettet i en begrænset runspace. Fra perspektivet brugeren på kommandolinjen vises en begrænset runspace som en begrænset version af Windows PowerShell. Samtidig bevare nogle af de grundlæggende funktioner i Windows PowerShell, har denne grænseflade yderligere dedikeret cmdletter, der henvender sig til administration af din Microsoft Azure StorSimple enhed. 

I denne artikel beskrives Windows PowerShell til StorSimple funktioner, herunder hvordan du kan oprette forbindelse til denne brugergrænsefladen og indeholder links til trinvise procedurer eller arbejdsprocesser, som du kan udføre ved hjælp af denne grænseflade. Arbejdsprocesserne, der omfatter hvordan du kan registrere din enhed, konfigurere netværksgrænsefladen på din enhed, skal du installere opdateringer, der kræver kameraet, for at være i vedligeholdelsestilstand, ændre status for enheden, og foretage fejlfinding af eventuelle problemer, der kan opstå.

Når du har læst i denne artikel, vil du kunne:

- Oprette forbindelse til enheden StorSimple ved hjælp af Windows PowerShell til StorSimple.

- Administrere enheden StorSimple ved hjælp af Windows PowerShell til StorSimple.

- Få hjælp i Windows PowerShell til StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell til StorSimple cmdletter gør det muligt at administrere din StorSimple enhed fra en seriel konsol eller eksternt via Windows PowerShell remoting. Gå til [reference til cmdlet til Windows PowerShell til StorSimple](https://technet.microsoft.com/library/dn688168.aspx)kan finde flere oplysninger om hver af de enkelte-cmdletter, som kan bruges i denne grænseflade.

>- Azure PowerShell StorSimple cmdletter er en anden samling af cmdletter, kan du automatisere StorSimple service-niveau og overførselsopgaver fra kommandolinjen. Gå til [Azure StorSimple cmdlet reference](https://msdn.microsoft.com/library/azure/dn920427.aspx), du kan finde flere oplysninger om Azure PowerShell-cmdletter for StorSimple.

Du kan få adgang til Windows PowerShell til StorSimple ved hjælp af en af følgende fremgangsmåder:

- [Oprette forbindelse til StorSimple enhed Seriel konsol](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Oprette forbindelse fra en fjernplacering til StorSimple ved hjælp af Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Oprette forbindelse til Windows PowerShell til StorSimple via konsollen enhed seriel

Du kan [hente trykfarver](http://www.putty.org/) eller lignende terminal emulering software til at oprette forbindelse til Windows PowerShell til StorSimple. Du skal konfigurere trykfarver specifikt for at få adgang til Microsoft Azure StorSimple enheden. Følgende emner indeholder flere detaljerede oplysninger om, hvordan du konfigurerer trykfarver og oprette forbindelse til enheden. Forskellige menuindstillinger i konsollen seriel gennemgås også.

### <a name="putty-settings"></a>Trykfarver indstillinger

Sørg for, at du bruger følgende trykfarver indstillinger til at oprette forbindelse til Windows PowerShell-brugergrænsefladen fra seriel administrationskonsollen.

#### <a name="to-configure-putty"></a>Sådan konfigureres trykfarver

1. Vælg **tastatur**i dialogboksen trykfarver **konfigureres igen** i ruden **kategori** .

2. Kontrollér, at følgende indstillinger er markeret (dette er standardindstillingerne, når du starter en ny session). 

  	|Tastatur element|Vælg|
  	|---|---|
  	|Tilbage-tasten|Kontrolelement-? (127)|
  	|Home og End nøgler|Standard|
  	|Funktionstaster og tastatur|ESC [n ~|
  	|Starttilstanden for markøren taster|Normal|
  	|Starttilstanden for numeriske tastatur|Normal|
  	|Aktivere ekstra tastaturfunktioner|Ctrl-Alt adskiller sig fra AltGr|

    ![Understøttede trykfarver indstillinger](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Klik på **Anvend**.

4. Vælg **oversættelse**i ruden **kategori** .

5. Vælg **UTF-8**i listefeltet **Remote tegnsæt** .

6. Vælg **Brug Unicode stregtegning kodepunkter**under **håndtering af stregtegning tegn**. Følgende illustration viser de korrekte trykfarver valg.

    ![UTF trykfarver indstillinger](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Klik på **Anvend**.


Du kan nu bruge trykfarver til at oprette forbindelse til enhed seriel konsollen ved at benytte følgende fremgangsmåde.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Om serielle konsollen

Når du får adgang til Windows PowerShell grænseflade for enheden StorSimple via seriel-konsollen meddelelsen banner præsenteres, efterfulgt af menuindstillinger. 

Banner meddelelsen indeholder grundlæggende StorSimple enhedsoplysninger som den model, navn, installeret softwareversion og status for den controller, du får adgang til. Følgende billede viser et eksempel på en banner meddelelse.

![Seriel banner meddelelse](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Du kan bruge banner meddelelsen til at identificere, om den controller, du har forbindelse til er aktiv eller passiv.

Følgende billede viser de forskellige runspace indstillinger, der er tilgængelige i menuen Seriel konsol.

![Registrere din enhed 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Du kan vælge mellem følgende indstillinger:

1. **Log på med fuld adgang** Denne indstilling kan du oprette forbindelse (med de korrekte legitimationsoplysninger) **SSAdminConsole** runspace på den lokale controller. (Den lokale controller er den controller, som du aktuelt har adgang til via seriel konsollen på enheden StorSimple). Denne indstilling kan også bruges til at tillade Microsoft Support for at få adgang til ubegrænset runspace (en session med support) for at foretage fejlfinding af problemer med mulige enhed. Når du bruger indstilling 1 til at logge på, kan du tillade Microsoft Support-engineering at få adgang til ubegrænset runspace ved at køre en bestemt cmdlet. Få mere at vide ved at referere til [starte en session med support](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Log på peer-controller med fuld adgang** Denne indstilling er den samme som 1, bortset fra at du kan oprette forbindelse (med de korrekte legitimationsoplysninger) til **SSAdminConsole** runspace på peer-controller. Da StorSimple enheden er en høj tilgængelighed enhed med to enheder i en aktiv / passiv konfiguration, refererer peer til anden controlleren i den enhed, du får adgang til via konsollen seriel).
Svarer til indstilling 1, denne indstilling kan også bruges til at tillade Microsoft Support for at få adgang til ubegrænset runspace på en peer-controller.

3. **Opret forbindelse med begrænset adgang** Denne indstilling bruges til at få adgang til Windows PowerShell-grænsefladen i begrænset tilstand. Du bliver ikke bedt om legitimationsoplysninger. Denne indstilling opretter forbindelse til en mere begrænset runspace sammenlignet med indstillinger for 1 og 2.  Nogle af de opgaver, der er tilgængelige via indstilling 1, **ikke* kan udføres i denne runspace er:

    - Nulstil standardindstillinger
    - Ændre adgangskoden
    - Aktivere eller deaktivere understøttelse af adgang
    - Anvend opdateringer
    - Installere hotfixes 
                                                

    >[AZURE.NOTE] **Dette er den foretrukne indstilling, hvis du har glemt enhedsadgangskode og kan ikke oprette forbindelse via indstilling 1 eller 2.**

4. **Skift sprog** Denne indstilling kan du ændre det viste sprog på Windows PowerShell-grænseflade. Understøttede sprog er engelsk, japansk, russisk, fransk, syd koreansk, spansk, italiensk, tysk, kinesisk og portugisisk (Brasilien).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Oprette forbindelse fra en fjernplacering til StorSimple ved hjælp af Windows PowerShell til StorSimple

Du kan bruge Windows PowerShell remoting til at oprette forbindelse til din StorSimple enhed. Når du knytter denne måde, kan du ikke kan se en menu. (Du se en menu, hvis du bruger konsollen seriel på enheden for at oprette forbindelse. Oprette forbindelse fra en fjernplacering fører dig direkte til udgave af "indstilling 1 – fuld adgang" på konsollen seriel.) Med Windows PowerShell remoting, du opretter forbindelse til en bestemt runspace. Du kan også angive sproget i brugergrænsefladen. 

Det viste sprog er uafhængig af det sprog, som du fastsætter ved hjælp af indstillingen **Skift sprog** i menuen Seriel konsol. Remote PowerShell bliver automatisk løfte landestandard af enheden, du opretter forbindelse fra, hvis der ikke er angivet.

>[AZURE.NOTE] Hvis du arbejder med Microsoft Azure virtuelle værter og StorSimple virtuelle enheder, kan du bruge Windows PowerShell remoting og virtuelle værten at oprette forbindelse til den virtuelle enhed. Hvis du har konfigureret en del placering på den vært, hvor du vil gemme oplysninger fra Windows PowerShell-session, skal du være opmærksom, omfatter vigtigste alle kun godkendte brugere. Hvis du har konfigureret dele til at tillade adgang ved alle, og du opretter forbindelse uden at angive legitimationsoplysninger, ikke-godkendte anonyme hovedstolen bruges derfor, og du får vist en fejlmeddelelse. Til at løse dette problem, delt vært du skal aktivere kontoen Gæst og derefter give gæst konto fuld adgang til del, eller du skal angive gyldige legitimationsoplysninger sammen med Windows PowerShell-cmdlet.

Du kan bruge HTTP eller HTTPS til at oprette forbindelse via Windows PowerShell remoting. Bruge instruktionerne i følgende selvstudier:

- [Oprette forbindelse via HTTP](storsimple-remote-connect.md#connect-through-http)
- [Oprette forbindelse fra en fjernplacering ved hjælp af HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Sikkerhedsovervejelser i forbindelse

Når du vælger, hvordan du kan oprette forbindelse til Windows PowerShell til StorSimple, skal du overveje følgende:

- Oprette forbindelse direkte til enhed seriel konsollen ikke er sikker, men ikke oprette forbindelse til konsollen seriel via netværk parametre er. Vær forsigtig med sikkerhedsrisikoen, når du opretter forbindelse til enhed seriel over netværk parametre.

- Forbindelse via en HTTP-session kan tilbyde større sikkerhed end forbindelse via konsollen seriel via netværk. Selvom det ikke er den mest sikre metode, er det accepteres på netværk, der er tillid til.

- Forbindelse via en HTTPS-session er den mest sikre og den anbefalede indstilling.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrere enheden StorSimple ved hjælp af Windows PowerShell til StorSimple
I følgende tabel vises en oversigt over alle de almindelige administrationsopgaver og komplekse arbejdsprocesser, der kan udføres i grænsefladen Windows PowerShell for enheden StorSimple. Du kan finde flere oplysninger om hver af arbejdsprocessen, skal du klikke på den relevante post i tabellen.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell til StorSimple arbejdsprocesser

|Hvis du vil gøre dette...|Brug denne procedure.|
|---|---|
|Registrere din enhed|[Konfigurere og registrere enheden ved hjælp af Windows PowerShell til StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Konfigurere webtjenesteproxy</br>Vis web proxyindstillinger|[Konfigurere webtjenesteproxy for enheden StorSimple](storsimple-configure-web-proxy.md)|
|Redigere DATA 0 netværk interface indstillinger på din enhed|[Ændre DATA 0 netværksgrænsefladen til enheden StorSimple](storsimple-modify-data-0.md)|
|Stoppe en controller </br> Genstarte eller lukke en controller </br> Lukke en enhed</br>Nulstille enheden til standardindstillingerne|[Administrere enhed enheder](storsimple-manage-device-controller.md)|
|Installere vedligeholdelse tilstand opdateringer og hotfixes|[Opdatere din enhed](storsimple-update-device.md)|
|Angiv Vedligeholdelsestilstand </br>Afslut Vedligeholdelsestilstand|[StorSimple enhed tilstande](storsimple-device-modes.md)|
|Oprette en supportpakke</br>Dekryptere og redigere en supportpakke|[Oprette og administrere en supportpakke](storsimple-create-manage-support-package.md)|
|Starte en session med Support</br>|[Starte en session med support i Windows PowerShell til StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Få hjælp i Windows PowerShell til StorSimple

I Windows PowerShell til StorSimple findes cmdlet Hjælp. En online, opdaterede version af denne Hjælp findes også, som du kan bruge til at opdatere Hjælp på dit system.

Få hjælp i denne grænseflade svarer til, i Windows PowerShell, og de fleste af de Hjælp-relaterede cmdletter fungerer. Du kan finde hjælp til Windows PowerShell online i TechNet-biblioteket: [Scripting med Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Følgende er en kort beskrivelse af typerne af hjælp til denne Windows PowerShell-grænseflade, herunder hvordan du opdaterer Hjælp.

#### <a name="to-get-help-for-a-cmdlet"></a>At få hjælp til en-cmdlet

- Hvis du vil have hjælp til en hvilken som helst cmdlet eller funktionen, skal du bruge følgende kommando:`Get-Help <cmdlet-name>`

- For at få online Hjælp til en hvilken som helst cmdlet ved brug af tidligere cmdlet med den `-Online` parameter:`Get-Help <cmdlet-name> -Online`

- Fuld hjælp, kan du bruge den `–Full` parameter, og eksempler, bruge den `–Examples` parameter.

#### <a name="to-update-help"></a>Opdatere hjælp

Du kan nemt opdatere Hjælp i grænsefladen Windows PowerShell. Udfør følgende trin for at opdatere Hjælp på dit system.

#### <a name="to-update-cmdlet-help"></a>Opdatere cmdlet hjælp

1. Start Windows PowerShell med indstillingen **Kør som administrator** .

1. Ved kommandoprompten skal du indtaste: `Update-Help`

1. Opdaterede Hjælp-filerne installeres.

1. Når Hjælp-filerne er installeret, skal du skrive: `Get-Help Get-Command`. Derved vises en liste over cmdletter, Hjælp findes.


>[AZURE.NOTE] Log på den tilsvarende indstilling i menuen for at få en liste over alle tilgængelige cmdletter i en runspace, og kør den `Get-Command` cmdlet.

## <a name="next-steps"></a>Næste trin
Hvis du oplever problemer med enheden StorSimple, når du udfører én af de ovenfor arbejdsprocesser, se [værktøjer til fejlfinding i forbindelse med StorSimple installationer](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

