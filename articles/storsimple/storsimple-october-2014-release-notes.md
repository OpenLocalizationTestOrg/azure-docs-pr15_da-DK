<properties 
    pageTitle="StorSimple 8000 opdatering 0.1 produktbemærkninger | Microsoft Azure"
    description="I denne artikel beskrives de nye funktioner og løsninger, åbne problemer og løsninger tilgængelige for oktober 2014 Microsoft Azure StorSimple release (opdatering 0,1)."
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
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>StorSimple 8000 serie opdatering for 0.1 produktbemærkninger – oktober 2014  

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere de kritiske åbne problemer for StorSimple 8000 serie opdatering 0,1 udgivet i oktober 2014. De indeholder også en liste over StorSimple-software og firmware opdateringer, der findes i denne udgave. Dette er den første udgave, når den StorSimple 8000 serie slip version er gjort alment tilgængelig i marts 2014 og svarer til softwareversion 6.3.9600.17312.  

Vi anbefaler, at du søge efter og anvende tilgængelige opdateringer med det samme, når du har installeret enheden. Du kan også aktivere automatiske opdateringer til at hente og installere vigtige opdateringer fra Microsoft, så snart de er udgivet. Finde flere oplysninger, hvordan du [opdaterer enheden StorSimple](storsimple-update-device.md).  

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringerne i din StorSimple løsning.  

>[AZURE.IMPORTANT]
> 
-   Du kan bruge tjenesten StorSimple Manager og ikke Windows PowerShell til StorSimple for at installere opdateringerne til oktober.  
-   Opdateringerne, der typisk gøre om 3 timer for at gennemføre.  
-   Oktober udgave af StorSimple indeholder ikke nogen opdateringer til den virtuelle StorSimple-enhed. Du kan stadig anvende tilgængelige opdateringer til Windows, herunder seneste sikkerhedsrettelser, men du se ikke en ændring i version til den virtuelle enhed.  

Kontrollér, at følgende forudsætninger er opfyldt før opdatering af enheden StorSimple.  

- Sørg for, at begge enhed enheder kører, før du søge efter opdateringer. Hvis enten controller ikke kører, mislykkes scanningen. For at bekræfte, at enheder er i en sund tilstand, skal du gå til **Hardware Status** under siden **vedligeholdelse** . Hvis der er komponenter, **har du brug for opmærksomhed**, skal du kontakte Microsoft Support før du yderligere.  
- Sikre, at fast IP-adresser for begge Controller 0 og 1 Controller er distribueret og kan oprette forbindelse til internettet, som bruges til vedligeholdelse opdateringer til enheden. Du kan bruge [testforbindelsen cmdlet](https://technet.microsoft.com/library/hh849808.aspx) til ping en kendte adresse uden for netværket som outlook.com, for at bekræfte, at controlleren har forbindelse til det eksterne netværk.  
- Sørg for, at de nødvendige udgående porte er tilgængelige på enheden StorSimple for udgående kommunikation. Se den [netværk krav til enheden StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)kan finde flere oplysninger.  
- Hvis enhed softwareversionen er ældre end 6.3.9600.17312 (oktober 2014 opdatering), kan du deaktivere Data 2 og 3 Data porte, hvis aktiveret, før du starter opdateringen. Hvis du lader Data 2 eller 3 Data porte, der er aktiveret, når du anvender opdateringen, kan det medføre din enhed controller gå til tilstanden nedbrud. Vær opmærksom på, når du deaktiverer netværksgrænseflader, alle de tilknyttede enheder, tages offline og I/O vil blive afbrudt for varigheden af opdateringen.  

## <a name="whats-new-in-the-october-release"></a>Hvad er nyt i oktober udgivelsen

Denne opdatering omfatter følgende forbedringer:

- Du kan nu bruge tjenesten StorSimple Manager brugergrænseflade til at administrere din enhed enheder. Management handlinger omfatter genstart ved lukning, eller slå en controller. Gå til [Administrer StorSimple enhed enheder](storsimple-manage-device-controller.md)kan finde flere oplysninger.  
- Du kan planlægge tildeling af WAN båndbredde efter dag i ugen og af tidspunktet kombinationer. Dette kan du bedre at udnytte WAN båndbredde belastet. Forskellige båndbredde skabeloner er tilladt for forskellige lydstyrken beholdere. Gå til at [administrere dine StorSimple båndbredde skabeloner](storsimple-manage-bandwidth-templates.md)kan finde flere oplysninger.  
- Du kan konfigurere mailbeskeder proaktiv besked på administratorer og andre eksisterende eller eventuelt kommende problemer. Gå til [konfigurere beskedindstillinger](storsimple-manage-alerts.md#configure-alert-settings)kan finde flere oplysninger.  

## <a name="issues-fixed-in-the-october-release"></a>Problemer, der løses i oktober-versionen


Den følgende tabel indeholder en oversigt over problemer, der er blevet løst i denne opdatering.  

| Nej. | Funktion | Problem | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Netværksgrænseflader | I den tidligere udgave, er skiftet netværksgrænseflader DATA 2 og 3 DATA i softwaren. Problemet er løst i denne opdatering. Fjern indstillingerne, og deaktivere disse netværksgrænseflader, før du har installeret opdateringen. Når du har installeret opdateringen, er du nødt til at omkonfigurere disse grænseflader. | Ja | Nej |
| 2 | Support-pakken | I den tidligere udgave, hvis du har kørt Windows PowerShell **Eksportér HcsSupportPackage** cmdlet for at hente loggene Baseboard Management Controller (BMC) handlingen mislykkedes med følgende advarsel: "Handlingen blev fuldført på denne controller, men mislykkedes på peer-controller på grund af følgende fejl. Kontroller om peer'en er sund og om den aktuelle node kan oprette forbindelse til peer'en." Problemet er løst nu. | Ja | Nej |
| 3 | Enhed failover | I den tidligere udgave var der mulighed for af data uoverensstemmelse, hvis et **opdage sikkerhedskopi** job mislykkedes under en enhed failover. Problemet er løst nu. | Ja | Nej |
| 4 | Enhed failover | Sikkerhedskopier er synlige i den tidligere udgave, når du har en enhed failover, men objektbeholderen tilknyttede lydstyrken blev ikke fundet på den destinationsadresse-enhed. Problemet er løst nu. | Ja | Nej |
| 5 | Enhed failover | I den tidligere udgave opstod der en fejl under optælling af skyen sikkerhedskopier under registreringsdatabasen-Gendan handlingen, der kan potentielt føre til data uoverensstemmelse, hvis der er problemer med forbindelse til skyen. | Ja | Nej |
| 6 | Firmware-opdatering | I den tidligere udgave, kørslen enhed firmware opdatering mislykkedes og vises en fejl som angivet, at cmdlet'erne ikke var genkendeligt og opdateringen mislykkedes som et resultat. Der gik derefter controlleren i tilstanden nedbrud. Problemet er løst nu. | Ja | Nej |
| 7 | Installation | Fejlene skyldes enheden ikke blive afbildning korrekt under installationen er nu rettet. | Ja | Nej |
| 8 | Factory Nulstil | Nu kan du eventuelt springe kontrollen firmware til fabriksindstillingerne. Dette er en ændring fra den tidligere udgave. | Ja | Nej |
| 9 | Factory Nulstil | Når en fabrik Nulstil cmdlet er kørt, foretaget firmware version Kontroller i den tidligere udgave, kun for visse hardwarekomponenter. Yderligere firmware kontrol er foretaget efter den første genstart i processen, hvilket kan medføre Nulstil mislykkes. Denne fix sikrer, at alle firmware Kontroller foretages, når fabriksindstillingerne nulstiller cmdlet køres og før den første system genstart. | Ja | Nej |
| 10 | Lagerplads konto vigtige rotation | **Aktiver HcsmServiceDataEncryptionKeyChange** cmdlet bruges til at rotere tasterne lagerplads konto nu beder om brugeren skal angive krypteringsnøglen service data. Dette er en ændring fra den tidligere udgave, hvor tjenesten data krypteringsnøglen blev overført som en indbygget parameter. | Ja | Nej |
| 11 | Failback inden for 24 timer | Under nedbrud, oprydningen på kildeenheden ikke sker cleanly, medfører failback mislykkes. Problemet er løst i denne udgave. | Ja | Nej |

## <a name="known-issues-in-the-october-release"></a>Kendte problemer i oktober-versionen

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.

| Nej. | Funktion | Problem | Kommentarer/løsning | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Factory Nulstil | I visse tilfælde, når du udfører en fabriksindstillingerne StorSimple enheden kan være fast og få vist denne meddelelse: **Nulstil til factory er i gang (fase 8)**. Dette sker, hvis du trykker på CTRL + C, mens cmdlet er i gang. | Ikke Tryk på CTRL + C efter start af en fabriksindstillingerne. Hvis du er allerede i denne tilstand, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Factory Nulstil | Gøre ikke factory Nulstil en StorSimple enhed, der opdateres fra GA til oktober 2014 slip. | Denne handling virker kun, hvis en programrettelse er installeret. Kontakt Microsoft Support for at få denne påkrævet programrettelse. | Ja | Nej | 
| 3 | Disken quorum | I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum bliver lagerpulje offline. Det forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 4 | Skyen øjebliksbillede fejl | I sjældne tilfælde et snapshot af en sky mislykkes muligvis fejlen **Maksimum sikkerhedskopiering er nået**. Dette sker, hvis du overskrider 255 online kopier på den samme enhed fra den samme oprindelige lydstyrke, som er blevet slettet. | | Ja | Ja |
| 5 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som peer-controller-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. |Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 6 | Enhed overvågning diagrammer | De enhed overvågning diagrammer fungerer ikke, når grundlæggende i tjenesten StorSimple Manager eller NTLM-godkendelse er aktiveret i serverkonfiguration proxy til enheden. | Ændre web proxykonfiguration for den enhed, der er registreret med StorSimple Manager-tjenesten, så, at godkendelse er indstillet til ingen. Du kan køre på Windows PowerShell til StorSimple sæt-HcsWebProxy cmdlet. | Ja | Ja |
| 7 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata. | | Ja | Ja |
| 8 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme enhed kilde til anden destinationsenheder understøttes ikke. | Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. | Ja | Nej |
| 9 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt.    | | Ja | Nej |
| 10 | Webtjenesteproxy | Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed. | Sørg for, at URL-adressen til web-proxy har HTTP som den angivne protokol. Du kan finde flere oplysninger om, hvordan du [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md). | Ja | Nej |
| 11 | Webtjenesteproxy | Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed. | | Ja | Nej |
| 12 | Høj skyen ventetid og høj i/o-arbejdsbelastning | Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar". | Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt. | Ja | Nej |

## <a name="physical-device-updates-in-the-october-release"></a>Fysisk enhedsopdateringer i oktober release

Når disse opdateringer er anvendt på en fysisk enhed, ændres softwareversionen til 6.3.9600.17312. Medmindre andet er angivet, disse produktbemærkninger gælder for alle modeller af StorSimple enhed. Du kan finde flere oplysninger om disse opdateringer, [oktober 2014 fysisk maskinen software opdatering til Microsoft Azure StorSimple maskinen](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Serielt vedhæftet SCSI (SAS) controller og firmware-opdateringer i oktober slip

Denne version opdaterer driveren og firmware på SAS controller af enheden fysisk. Opdatere yderligere oplysninger om SAS-controller, se [oktober 2014 opdatere ved LSI SAS enheder i Microsoft Azure StorSimple maskinen](http://support.microsoft.com/kb/2987020).   

Denne version gælder også en akkumuleret firmware-opdatering, der løser pålidelighedsproblemer med enheden hardwarekomponenter. Du kan finde flere oplysninger om firmwareopdateringen, [oktober 2014 firmware opdatering til Microsoft Azure StorSimple maskinen](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Virtuel enhedsopdateringer i oktober release

Denne version indeholder ikke nogen opdateringer til den virtuelle enhed. Anvende denne opdatering ændrer ikke softwareversion af en virtuel enhed.
 
