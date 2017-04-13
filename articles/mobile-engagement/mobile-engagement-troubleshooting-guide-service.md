<properties 
   pageTitle="Azure Mobile aftale fejlfindingsvejledningen - tjenesten" 
   description="Fejlfinding i forbindelse med vejledninger til Azure Mobile aftale" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-service-issues"></a>Fejlfinding i forbindelse med vejledning til problemer med tjenesten

Følgende er mulige problemer, der kan opstå med hvordan Azure Mobile aftale kører.

## <a name="service-outages"></a>Tjenesten udfald

### <a name="issue"></a>Problem
- Problemer, der vises til skyldes Azure Mobile aftale serviceafbrydelser.

### <a name="causes"></a>Årsager
- Problemer, der ser ud til at skyldes Azure Mobile aftale Service udfald kan skyldes flere forskellige problemer:
    - Isolerede problemer, der oprindeligt vises systematiske på alle Azure Mobile aftale
    - Kendte problemer, der er forårsaget af server udfald (ikke altid viser serverens status):
    - Planlægning forsinkelser Targeting fejl Badge update-problemer, og statistik stop indsamler, opslagsnål Stop arbejde, kan API'EN stoppe med at virke, nye apps eller brugere ikke oprettes, DNS-fejl og timeoutfejl i Brugergrænsefladen, API eller Apps på en enhed.
    - Skyen afhængighed udfald [Azure Tjenestestatus](http://status.azure.com/)
    - Push-besked om tjenester (PNS) afhængighed udfald
    - App Store udfald

1) Hvis du vil teste for at se, om problemet er systematiske kan du teste den samme funktion fra en anden
   
   - Azure Mobile aftale integreret program
   - Test enhed
   - Test enhed OS version
   - Kampagnens
   - Brugerkonto
   - Browser (Internet Explorer, Firefox, Chrome osv.)
   - Computer

2) At afprøve, om problemet påvirker kun Brugergrænsefladen eller API:

   - Teste den samme funktion fra både Azure Mobile aftale Brugergrænsefladen og Azure Mobile aftale API'S.

3) At afprøve, om problemet skyldes netværket mobiltelefon:

   - Test, mens du har forbindelse til internettet via Wi-Fi, og mens forbindelse via netværket 3G mobiltelefon.
   - Bekræft, at din firewall ikke blokerer en Azure Mobile aftale IP-adresser eller porte.

4) At afprøve, om problemet er med din enhed:

   - Test, hvis din enhed kan oprette forbindelse til Azure Mobile aftale med en anden Azure Mobile aftale integreret app.
   - Test, du kan generere begivenheder, job og går ned fra din telefon, der kan ses i Azure Mobile aftale Brugergrænsefladen. 
   - Afprøve, om du kan sende pushmeddelelser fra Azure Mobile aftale brugergrænseflade til din enhed, der er baseret på dens enheds-ID. 

5) At afprøve, om problemet skyldes din App:

   - Installere og teste dit program fra en emulator i stedet for fra en fysisk enhed:
   
6) Til at afprøve om er OS opgraderinger til slutbrugeren enheder, der kræver en SDK opgradering til at løse problemet:

   - Test dit program på forskellige enheder med forskellige versioner af Operativsystemet.
   - Bekræft, at du bruger den nyeste version af SDK.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Forbindelsen og forkerte oplysninger om problemer

### <a name="issue"></a>Problem
- Problemer med at logge Azure Mobile aftale Brugergrænsefladen.
- Forbindelsesfejl med Azure Mobile aftale API'S.
- Problemer med overførsel af App oplysninger mærker via API'EN enhed.
- Problemer med at hente logfiler eller eksporterede data fra Azure Mobile aftale.
- Forkerte oplysninger, der vises i Brugergrænsefladen Azure Mobile aftale.
- Forkerte oplysninger, der vises i Azure Mobile aftale logfiler.

### <a name="causes"></a>Årsager
* Bekræft din brugerkonto har tilstrækkelige tilladelser til at udføre opgaven.
* Bekræft, at problemet ikke er isolerede til én computer eller dit lokale netværk.
* Bekræft, at, tjenesten Azure Mobile aftale ikke har nogen rapporteret afbrydelser.
* Bekræft, at filerne App oplysninger mærke følger alle disse regler:
    - Anvende kun UTF8 tegnsæt (ANSI-tegnsættet ikke understøttes).
    - Bruge et komma "," som separatortegnet (du kan åbne en tjeneste at anmode om at ændre separatortegnet .csv fra et komma "," til et andet tegn som et semikolon ";").
    - Bruge alle små bogstaver til booleske værdier, "true" og "false".
    - Bruge en fil, der er mindre end den maksimale filstørrelse af 35MB.
 
