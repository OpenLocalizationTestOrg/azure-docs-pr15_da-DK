<properties 
   pageTitle="StorSimple 8000 opdatering 0,2 produktbemærkninger | Microsoft Azure"
   description="I denne artikel beskrives de nye funktioner og løsninger, åbne problemer og løsninger tilgængelige for januar 2015 Microsoft Azure StorSimple release (opdatering 0,2)."
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
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>StorSimple 8000 serie opdatering for 0,2 produktbemærkninger - januar 2015

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger identificere de kritiske åbne problemer efter januar 2015 udgave af Microsoft Azure StorSimple. De indeholder også en liste over StorSimple-software og firmware opdateringer, der findes i denne udgave. Dette er den anden version, når den StorSimple 8000 serie slip version blev foretaget alment tilgængelig i marts 2014.
  
Denne opdatering ændrer ikke den fysiske enhed softwareversion fra oktober update. Den stadig være version 6.3.9600.17312. Det billede, der bruges af virtuel enhed billedet er ændret i denne udgave. Derfor alle de nye virtuelle enheder oprettet, efter 20-1-2015, vises versionen som 6.3.9600.17361.  

Gennemgå følgende oplysningerne i produktbemærkninger til opdateringen januar 2015.

> [AZURE.IMPORTANT]  
>
>- Denne opdatering er ikke tilgængelig via Windows Update og kan ikke installeres, som andre opdateringer. Enheden vil ikke modtage opdateringen, selvom du har anvendt opdateringerne ved hjælp af portalen Azure klassisk. Denne opdatering gælder kun for virtuelle enheder, der er oprettet efter 20 januar 2015. 
> 
>- Januar udgave af StorSimple indeholder ikke nogen opdateringer til den fysiske StorSimple-enhed. Du kan stadig anvende tilgængelige opdateringer til Windows på den virtuelle enhed, herunder seneste sikkerhedsrettelser, men du se ikke en ændring i version til den fysiske StorSimple-enhed.

## <a name="whats-new-in-the-january-release"></a>Hvad er nyt i januar-versionen

Denne opdatering indeholder en løsning, der er relateret til de enheder, gå offline på den virtuelle enhed. (Se [fejl rettet i denne udgave](#issues-fixed-in-the-january-release)).  

Opdater indeholder ikke nye funktioner eller funktionalitet.  

## <a name="issues-fixed-in-the-january-release"></a>Problemer, der løses i januar-versionen

I følgende tabel beskrives det problem, der blev rettet i denne opdatering.

|Nej.|Funktion|Problem|Gælder for fysiske enhed|Gælder for virtuel enhed 
|---|-------|-----|--------------------------|-------------------------
|1|Enheder gå offline|Når høj skyen latenstider fastholdes for nogle minutter, gå StorSimple virtuel enhed enhederne offline på værter. Denne fix øges grænsen for skyen latenstider, derved minimere de situationer, der medfører enheder til at skifte til offline på hosts.|Nej|Ja  

## <a name="known-issues-in-the-january-release"></a>Kendte problemer i januar-versionen

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.
 
|Nej.|Funktion|Problem|Kommentarer/løsning|Gælder for fysiske enhed|Gælder for virtuel enhed 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Factory Nulstil|  I visse tilfælde, når du udfører en fabriksindstillingerne StorSimple enheden kan være fast og få vist denne meddelelse: **Nulstil til factory er i gang (fase 8).** Dette sker, hvis du trykker på CTRL + C, mens cmdlet er i gang.| Ikke Tryk på CTRL + C efter start af en fabriksindstillingerne. Hvis du er allerede i denne tilstand, skal du kontakte Microsoft Support til næste trin.|Ja|Nej|
|2|Disken quorum| I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum bliver lagerpulje offline. Det forbliver offline, selvom diskene er genoprettet.|Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin.|Ja |Nej
|3|Skyen øjebliksbillede fejl|I sjældne tilfælde et snapshot af en sky mislykkes muligvis fejlen **Maksimum sikkerhedskopiering er nået**. Dette sker, hvis du overskrider 255 online kopier på den samme enhed fra den samme oprindelige lydstyrke, som er blevet slettet.||Ja|Ja
|4|Forkert enheds-ID|Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som peer-controller-ID.  I sjældne tilfælde ses problemet også efter en genstart af systemet.|Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført.|Ja|Nej 
|5| Enhed overvågning diagrammer|De enhed overvågning diagrammer fungerer ikke, når grundlæggende i tjenesten StorSimple Manager eller NTLM-godkendelse er aktiveret i serverkonfiguration proxy til enheden.|Ændre web proxykonfiguration for den enhed, der er registreret med StorSimple Manager-tjenesten, så, at godkendelse er indstillet til ingen. Du kan køre på Windows PowerShell til StorSimple sæt-HcsWebProxy cmdlet.|Ja|Ja
|6| Lagerplads konti|Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata.|| Ja |  Ja
|7|Enhed failover| Flere failover af en volumenlicens objektbeholder fra den samme enhed kilde til anden destinationsenheder understøttes ikke.| Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk.|Ja|Nej
|8| Installation|Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt.||Ja|Nej
|9| Webtjenesteproxy|Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed.|Sørg for, at URL-adressen til web-proxy har HTTP som den angivne protokol. Se flere oplysninger om, hvordan du [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md).|Ja |Nej
|10|Webtjenesteproxy|  Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed.|| Ja |Nej
|11|Høj skyen ventetid og høj i/o-arbejdsbelastning|Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar".|Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt.|Ja|Nej

## <a name="physical-device-updates-in-the-january-release"></a>Fysisk enhedsopdateringer i januar release

Denne opdatering indeholder ikke andre ændringer af StorSimple enheden.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Serielt vedhæftet SCSI (SAS) controller og firmware-opdateringer i januar slip

Denne version indeholder ikke nogen opdateringer til den controller serielt vedhæftet SCSI (SAS) eller firmwaren. Driver opdateringen blev i oktober 2014 version. 

## <a name="virtual-device-updates-in-the-january-release"></a>Virtuel enhedsopdateringer i januar release

Denne version indeholder et opdateret billede til den virtuelle enhed. Alle de virtuelle enheder, der er oprettet efter 20 januar 2015 vises softwareversion som 6.3.9600.17361.

 
