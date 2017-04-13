<properties 
   pageTitle="StorSimple 8000 opdatering 0,3 produktbemærkninger | Microsoft Azure"
   description="I denne artikel beskrives de nye funktioner og løsninger, åbne problemer og løsninger tilgængelige for februar 2015 Microsoft Azure StorSimple release (opdatering 0,3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>StorSimple 8000 serie opdatering for 0,3 produktbemærkninger - februar 2015

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere kritiske åbne problemer for StorSimple 8000 serie opdatering 0,3 udgivet i februar 2015. De indeholder også en liste over StorSimple software og firmware opdateringer, der findes i denne udgave. Dette er den tredje version, når den StorSimple 8000 serie slip version blev foretaget alment tilgængelig i marts 2014.
  
Denne opdatering ændrer ikke enhed softwareversion fra januar opdateringen. Den stadig være version 6.3.9600.17312. Du kan kontrollere, om opdateringen er installeret ved at markere den **Sidste opdateret** dato. Hvis datoen er 2-10-2015 eller nyere, så opdateringen er blevet installeret.  

Vi anbefaler, at du søge efter og anvende tilgængelige opdateringer med det samme, når du har installeret enheden StorSimple. Du kan også aktivere automatiske opdateringer til at hente og installere vigtige opdateringer fra Microsoft, så snart de er udgivet. Se [opdatere enheden StorSimple](storsimple-update-device.md)kan finde flere oplysninger.  

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringen i din StorSimple løsning.  

>[AZURE.IMPORTANT]   
>
> - Du kan bruge tjenesten StorSimple Manager og ikke Windows PowerShell til StorSimple for at installere februar-opdatering.   
> - Det tager omkring en time at installere denne opdatering. Hvis du installerer akkumulerede opdateringer, kan processen dog tage om 3 timer for at gennemføre.  
> - Februar udgave af StorSimple indeholder ikke nogen opdateringer til den virtuelle StorSimple-enhed. Du kan stadig anvende tilgængelige opdateringer til Windows på den virtuelle enhed, herunder seneste sikkerhedsrettelser, men du se ikke en ændring i version til den virtuelle enhed.  

Sørg for, at følgende forudsætninger er opfyldt før opdatering af enheden StorSimple.  

- Sørg for, at begge enhed enheder kører, før du søge efter opdateringer. Hvis enten controller ikke kører, mislykkes scanningen. For at bekræfte, at enheder er i en sund tilstand, skal du gå til **Hardware Status** under siden **vedligeholdelse** . Hvis der er komponenter, **har du brug for opmærksomhed**, skal du kontakte Microsoft Support før du yderligere.
- Sørg for, fast IP'er til både controller 0 og 1-controller er distribueret og kan oprette forbindelse til internettet, som bruges til vedligeholdelse opdateringer til enheden. Du kan bruge [testforbindelsen cmdlet](https://technet.microsoft.com/library/hh849808.aspx) til ping en kendte adresse uden for netværket, som outlook.com, for at bekræfte, at controlleren har forbindelse til det eksterne netværk.
- Sørg for, at port 80 og 443 er tilgængelig på enheden StorSimple for udgående kommunikation. Se den [netværk krav til enheden StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)kan finde flere oplysninger.
- Hvis enhed softwareversionen er ældre end 6.3.9600.17312 (oktober 2014 opdatering), kan du deaktivere Data 2 og 3 Data porte, hvis aktiveret, før du starter opdateringen. Efterlade Data 2 eller 3 Data porte, der er aktiveret, når du har installeret opdateringen, kan det medføre, at din enhed controller gå til tilstanden nedbrud. Vær opmærksom på, når du deaktiverer netværksgrænseflader, alle de tilknyttede enheder skal gøres tilgængelig offline, og i/O'er vil blive afbrudt for varigheden af opdateringen.  
  
## <a name="whats-new-in-the-february-release"></a>Hvad er nyt i februar-versionen

Denne opdatering indeholder en løsning for fabriksindstillingerne nulstille problem, der er indtruffet på enheder, der har opgraderet fra GA slip til oktober 2014-udgivelsen. Få mere at vide ved at se [fejl rettet i denne udgave](#issues-fixed-in-the-february-release).   

Denne opdatering indeholder ikke nye funktioner eller funktionalitet.  

## <a name="issues-fixed-in-the-february-release"></a>Problemer, der løses i februar-versionen

I følgende tabel beskrives det problem, der blev rettet i denne opdatering.  
 
| Nej. | Funktion | Problem | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Factory Nulstil | Du forsøger at udføre en fabrik nulstille på en enhed, der oprindeligt var GA release (version 6.3.9600.17215) installeret, men er blevet opdateret til oktober release (version 6.3.9600.17312). Fabriksindstillingerne nulstille mislykkes, og enheden bliver ustabil. | Ja | Nej |


## <a name="known-issues-in-the-february-release"></a>Kendte problemer i februar-versionen

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.
 
| Nej. | Funktion | Problem | Kommentarer/løsning | Gælder for fysiske enhed  | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Factory Nulstil | I visse tilfælde, når du udfører en fabriksindstillingerne StorSimple enheden kan være fast og få vist denne meddelelse: **Nulstil til factory er i gang (fase 8)**. Dette sker, hvis du trykker på CTRL + C, mens cmdlet er i gang. | Ikke Tryk på CTRL + C efter start af en fabriksindstillingerne. Hvis du er allerede i denne tilstand, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Disken quorum | I sjældne tilfælde, hvis størstedelen af diske i EBOD omslutning af en 8600device er afbrudt, hvilket resulterer i ingen disk quorum bliver lagerpulje offline. Den forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 3 | Skyen øjebliksbillede fejl | I sjældne tilfælde snapshot af en sky mislykkes muligvis fejlen **Maksimum sikkerhedskopiering er nået**. Dette sker, hvis du overskrider 255 online kopier på den samme enhed fra den samme oprindelige lydstyrke, som er blevet slettet. |  | Ja | Ja |
| 4 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som den peer-enheds-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. | Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 5 | Enhed overvågning diagrammer | De enhed overvågning diagrammer fungerer ikke, når grundlæggende i tjenesten StorSimple Manager eller NTLM-godkendelse er aktiveret i serverkonfiguration proxy til enheden. | Ændre web proxykonfiguration for den enhed, der er registreret med StorSimple Manager-tjenesten, så, at godkendelse er indstillet til ingen. Du kan køre på Windows PowerShell til StorSimple sæt-HcsWebProxy cmdlet. | Ja | Ja |
| 6 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata. |  | Ja | Ja |
| 7 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme enhed kilde til anden destinationsenheder understøttes ikke.  Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. |   | Ja | Nej |
| 8 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt. |  | Ja | Nej |
| 9 | Webtjenesteproxy | Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed. | Sørg for, at URL-adressen til web-proxy har HTTP som den angivne protokol. Du kan finde flere oplysninger om, hvordan du [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md). | Ja | Nej |
| 10 | Webtjenesteproxy | Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed. |  | Ja | Nej |
| 11 | Høj skyen ventetid og høj i/o-arbejdsbelastning | Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar". | Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt. | Ja | Nej |

## <a name="physical-device-updates-in-the-february-release"></a>Fysisk enhedsopdateringer i februar release

Opdateringen løser factory Nulstil-problem, der er indtruffet på enheder, der har opgraderet fra GA slip til oktober 2014-udgivelsen. Det indeholder ikke andre opdateringer til StorSimple enheden.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Serielt vedhæftet SCSI (SAS) controller og firmware-opdateringer i februar slip

Denne version indeholder ikke nogen opdateringer til den controller serielt vedhæftet SCSI (SAS) eller firmwaren. Driver opdateringen blev i oktober 2014 version.  

## <a name="virtual-device-updates-in-the-february-release"></a>Virtuel enhedsopdateringer i februar release

Denne version indeholder ikke nogen opdateringer til den virtuelle enhed. Anvende denne opdatering ændrer ikke softwareversion af en virtuel enhed.
 
