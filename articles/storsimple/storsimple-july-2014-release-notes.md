<properties 
   pageTitle="StorSimple 8000 slip version produktbemærkninger | Microsoft Azure"
   description="I denne artikel beskrives de nye funktioner, åbne problemer og løsninger tilgængelige for juli 2014 Microsoft Azure StorSimple version."
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

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>StorSimple 8000 serie slip Version produktbemærkninger - juli 2014 

## <a name="overview"></a>Oversigt

Følg produktbemærkninger identificere de kritiske åbne problemer for StorSimple 8000 serien juli 2014 generelt tilgængelig (GA) version af Microsoft Azure StorSimple. Denne version svarer til softwareversion 6.3.9600.17215.  

Medmindre andet er angivet, disse produktbemærkninger gælder for alle modeller på StorSimple enheden. Produktbemærkninger opdateres løbende; som er opdaget alvorlige problemer, der kræver en løsning, bliver vedkommende tilføjet. Før du installerer Microsoft Azure StorSimple-løsning, kan du overveje følgende oplysninger.  

## <a name="known-issues-in-this-release"></a>Kendte problemer i denne version
Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.  
 
| Nej. | Funktion | Problem | Kommentarer/løsning | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Factory Nulstil | I visse tilfælde, når du udfører en fabriksindstillingerne StorSimple enheden kan være fast og få vist denne meddelelse: **Nulstil til factory er i gang (fase 8)**. Dette sker, hvis du trykker på CTRL + C, mens cmdlet er i gang. | Ikke Tryk på CTRL + C efter start af en fabriksindstillingerne. Hvis du er allerede i denne tilstand, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Disken quorum | I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum bliver lagerpulje offline. Den forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 3 | Skyen øjebliksbillede fejl | I sjældne tilfælde snapshot af en sky mislykkes muligvis fejlen **Maksimum sikkerhedskopiering er nået**. Dette sker, hvis du overskrider 255 online kopier på den samme enhed fra den samme oprindelige lydstyrke, som er blevet slettet. | | Ja | Ja |
| 4 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som den peer-enheds-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. | Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 5 | Enhed overvågning diagrammer | De enhed overvågning diagrammer fungerer ikke, når grundlæggende i tjenesten StorSimple Manager eller NTLM-godkendelse er aktiveret i serverkonfiguration proxy til enheden. | Ændre web proxykonfiguration for den enhed, der er registreret med StorSimple Manager-tjenesten, så, at godkendelse er indstillet til ingen. Du kan køre på Windows PowerShell til StorSimple sæt-HcsWebProxy cmdlet. | Ja | Ja |
| 6 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata. | | Ja | Ja |
| 7 | Failback | En failback inden for 24 timer af nedbrud (DR) understøttes ikke. | | Ja | Nej |
| 8 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme kilde-enhed til anden destinationsenheder understøttes ikke. Failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. | | Ja | Nej |
| 9 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en IP-adresse til en enhed for at ændringerne kan fuldføres korrekt. | | Ja | Nej |
| 10 | Netværksgrænseflader | Netværksgrænseflader DATA 2 og 3 DATA er skiftet i softwaren. | Kontakt Microsoft Support, hvis du vil konfigurere disse grænseflader. | Ja | Nej |


 
