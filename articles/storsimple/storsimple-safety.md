<properties 
   pageTitle="Sikkerhed for enheden StorSimple | Microsoft Azure"
   description="Beskriver safety konventioner, retningslinjer og overvejelser i forbindelse med, og hvordan sikkert at installere og betjene enheden StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="safely-install-and-operate-your-storsimple-device"></a>Installere og betjene enheden StorSimple på en sikker måde

![Advarselsikon](./media/storsimple-safety/IC740879.png)
![læst Safety meddelelse](./media/storsimple-safety/IC740885.png) **læse sikkerhed og sundhed oplysninger**

Læs alle sikkerhed og sundhed oplysninger i denne artikel, der gælder for enheden Microsoft Azure StorSimple. Hold alle udskrevne hjælpelinjer, der fulgte med enheden StorSimple til fremtidig brug. Fejl ved at følge vejledningen og korrekt konfigurere, bruge og sig til dette produkt kan øge risikoen for alvorlige skade eller dør eller skader på enheden eller enheder. Der findes også en [version af denne vejledning](http://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Sikkerhed ikonet konventioner

Her er de ikoner, som du vil finde, når du har gennemgået de sikkerhedsforanstaltninger skal overholdes ved at installere og køre din Microsoft Azure StorSimple enhed.

| Ikonet  | Beskrivelse  |
|:------|:-------------| 
|![Ikon for risiko](./media/storsimple-safety/IC740879.png) **risiko!**|Angiver en farlig situation, hvis ikke undgås, vil resultere i dør eller alvorlige skader. Dette signal ord er skal begrænses til de mest avancerede situationer.| 
|![Advarselsikon](./media/storsimple-safety/IC740879.png) **Advarsel!**|Angiver en farlig situation, hvis ikke undgås, kan det resultere i dør eller alvorlige skader.|
|![Advarselsikon](./media/storsimple-safety/IC740879.png) **Advarsel!**|Angiver en farlig situation, hvis ikke undgås, kan det resultere i mindre eller moderat skade.|
|![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**|Angiver betragtes som vigtige, men ikke risiko-relaterede oplysninger.|
|![Ikon for elektriske stød](./media/storsimple-safety/IC740882.png) **elektriske stød** |Højspændingen|
|![Ikon for tunge](./media/storsimple-safety/IC740883.png) **tunge**| |
|![Ingen bruger brugbare dele ikonet](./media/storsimple-safety/IC740879.png) **ingen bruger brugbare dele**|Ikke har adgang til medmindre korrekt uddannelse.|
|![Læs Safety meddelelse ikonet](./media/storsimple-safety/IC740885.png)**læse alle instruktioner først**| |
|![Tip risiko ikonet](./media/storsimple-safety/IC740886.png) **Tip risiko**| |


## <a name="handling-precautions"></a>Håndtering af forholdsregler

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![tunge ikonet](./media/storsimple-safety/IC740883.png) **Advarsel!** 


Du kan reducere risikoen for skade:

- En fuldt konfigureret omslutning kan afveje op til 32 kg (70 gram); Forsøg ikke at ophæve alene.
- Før du flytter omslutning, altid sikre, at to personer er tilgængelige til at håndtere tykkelsen. Vær opmærksom på, at én person, der forsøger at ophæve denne vægt kan opretholde skader.
- Ikke løft omslutning af håndtagene på Power og køling moduler (PCMs) placeret bag på enheden. Disse er ikke designet til at tage tykkelsen.

## <a name="connection-precautions"></a>Forholdsregler for forbindelse

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![elektriske stød ikonet](./media/storsimple-safety/IC740882.png) **Advarsel!**

Du kan reducere risikoen for skade, elektriske stød eller dør:

- Når drevet af flere AC kilder, Afbryd alle levering power til fuldført isolation.

- Permanent træk stikket til enheden før du flytter dem, eller hvis du tror, at den er blevet beskadiget på nogen måde.

- Give en sikker elektriske earth-forbindelse til el levering-kabler. Kontroller, at kendskab af omslutning opfylder nationale og lokale før du anvender power.

- Sørg for, at power forbindelsen afbrydes altid før fjernelse af en PCM fra omslutning.

- Da der plug på levering netledningen er primære Frakobl enhed, du sikre, at socket udtag er placeret i nærheden af udstyret og er nem adgang.

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![elektriske stød ikonet](./media/storsimple-safety/IC740882.png) **Advarsel!**

For at reducere risikoen for høj temperatur eller brand fra de elektriske forbindelser:

- Give en passende power kilde elektriske overbelastning beskyttelse opfylder kravene gennemgås i den tekniske specifikation.

- Brug ikke bifurcated el-kabler ("J" kundeemner).

- For at overholde sikkerhedskravene, emission og termisk krav, intet omslag skal fjernes, og alle pladser skal udfyldes med plug-in'en moduler eller drive tomme celler.

- Sørg for, at udstyret, der bruges på en måde, der er angivet af producenten. Hvis dette udstyr bruges på en måde, der ikke er angivet af producenten, kan den beskyttelse, som udstyret blive forringet.

![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**

For at fungere korrekt din udstyr og for at forhindre beskadigelse af produkt:

- RJ45 porte bag enheden er til en Ethernet-forbindelse. Disse skal ikke forbindelse til en telenet.

- Sørg for at installere enheden i et rack, der kan rumme en forside-tilbage køling design.

- Alle plug-in'en moduler og tomme plader er en del af systemomslutning. Disse skal kun fjernes, når en erstatning kan tilføjes med det samme. Systemet må ikke køre uden alle moduler eller tomme celler i stedet.

## <a name="rack-system-precautions"></a>Rack-systemet forholdsregler

De følgende sikkerhedskrav skal betragtes som når du opretter forbindelse til enhed i et skab rack.

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![Tip risiko ikonet](./media/storsimple-safety/IC740886.png) **Advarsel!**
 
Du kan reducere risikoen for skade fra et tip over:

- Rack-design skal understøtte den samlede mængde installerede bilag og bør omfatte stabilisering funktioner, der er egnet til at forhindre, at racket vægt eller bliver trykket under installation eller normal brug.

- Under indlæsning af et rack, fylder rack fra bunden, og Tøm oppefra og ned.

- Mere end én omslutning af racket ikke dias ad gangen for at undgå risikoen for rack toppling over.

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![elektriske stød ikonet](./media/storsimple-safety/IC740882.png) **Advarsel!**

Du kan reducere risikoen for skade, elektriske stød eller dør:

- Racket skal have et sikkert elektriske fordeling system. Det skal angive omslutning overliggende aktuelle beskyttelse og skal ikke overstyres med det samlede antal bilag, der er installeret. Elektriske power forbrug bedømmelse vises på navnepladen skal overholdes.

- Elektriske fordeling systemet skal angive en pålidelig begrundelse for hver omslutning i racket.

- Designet af elektriske fordeling systemet skal tage hensyn til den samlede bunden udsivning aktuelle fra alle power leverancer i alle bilag. Bemærk, at hver power levering i hver omslutning en bunden uautoriseret videregivelse strøm på 1,0 glidende gennemsnit maksimum ved 60 Hz 264 volt. Racket kan kræve etiketter med "høj uautoriseret videregivelse aktuel. Bunden (earth) forbindelse er væsentlige før der oprettes forbindelse en levering".

- Rack, når konfigureret med bilag, skal opfylde sikkerhedskravene i UL 60950-1 og IEC 60950-1/da 60950-1.

![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**

Til stort køling af rack-system:

- Sørg for, rack-design tager hensyn til den maksimale omslutning operativsystem omgivende temperatur af 35 grader Celsius (95 grader Fahrenheit).

- Systemet styres med low-pressure, bag udstødningsgas installation (tilbage tryk oprettet af rack døre og hindringer ikke at overstige 5 Pascal [0,5 mm vand måler]).

## <a name="power-cooling-module-pcm-precautions"></a>Power køling modul (PCM) forholdsregler

Enheden er udviklet til at fungere med to PCMs. Hver af PCMs har en power levering og en to-akse vifte. Systemet tillader under en kritiske betingelse til en fejl i en power levering, mens du fortsætter med normal handlinger. To PCMs (og dermed power forsyninger) altid være installeret. En enkelt PCM giver ikke overflødige power. Derfor kan fejlen for endnu en PCM medføre nedetid eller tab af data.

![Advarselsikon](./media/storsimple-safety/IC740879.png) ![elektriske stød ikonet](./media/storsimple-safety/IC740882.png) **Advarsel!**

Du kan reducere risikoen for skade, elektriske stød eller dør:

- Fjern ikke overfladen fra PCM. Der er risiko for elektrisk stød i. Til at returnere PCM og få en erstatnings, [Kontakt Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**

For at fungere korrekt din udstyr og for at forhindre beskadigelse af produkt:

- Du skal erstatte mislykkedes PCM inden for 24 timer. Når en PCM er fjernet for erstatning, skal erstatningen fuldføres inden for 10 minutter efter fjernelse.

- Fjern ikke en PCM, medmindre en erstatning kan installeres med det samme. Omslutning skal ikke drevet uden alle moduler på plads.

## <a name="electrostatic-discharge-esd-precautions"></a>Forholdsregler for elektrostatiske afslutning (ESD)

![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**

Se følgende ESD-relaterede sikkerhedsforanstaltninger.

- Sørg for, at du har installeret og markeret en passende antistatic håndled eller ankelleddene strop.

- Se alle traditionelt ESD forholdsregler, når håndtering af moduler og komponenter.

- Undgå at berøring med backplane komponenter og modul forbindelser.

- ESD skader er ikke omfattet af garanti.

## <a name="battery-disposal-precautions"></a>Batteri salg forholdsregler

Power levering bruger en speciel batteri til at beskytte indholdet af hukommelse under midlertidige, kortvarig strømsvigt. Batteriet er placeret i PCM. Husk følgende oplysninger på om batteriet.

![Advarselsikon](./media/storsimple-safety/IC740879.png) **Advarsel!**

Du kan reducere risikoen for shorts, fire, cirkeladskillelse, skade eller dør:

- Fjerne anvendte batterier i overensstemmelse med nationale/internationale bestemmelser.

- Ikke disassemblering, mase, eller termisk over 60 grader Celsius (140 grader Fahrenheit) eller forbrænde. Erstat PCM batteriet med et medfølgende batteri. Brug af en anden batteri kan udgør en risiko for brand eller cirkeladskillelse.

- Brug beskyttelse end store bogstaver på batterierne, hvis disse fjernes fra power levering.

![Bemærk ikonet](./media/storsimple-safety/IC740881.png) **meddelelse:**

Når forsendelses eller ellers transport af batterierne med fly, følge IATA Lithium batteri vejledning dokumentet findes på [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Når du har gennemgået disse safety meddelelser, er næste trin at Pak, rack og kabel din enhed.

## <a name="next-steps"></a>Næste trin

- Gå til [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md)for en 8100-enhed.

- Gå til [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md)for en 8600-enhed.

