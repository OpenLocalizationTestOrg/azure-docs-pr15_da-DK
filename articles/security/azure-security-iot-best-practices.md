<properties
   pageTitle="Internet ting Sikkerhed bedste fremgangsmåder | Microsoft Azure"
   description="I artiklen oversigt curated af Microsoft Internet af bedste fremgangsmåder for ting sikkerhed og generelle anbefalinger."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet ting Sikkerhed bedste fremgangsmåder

Sikring af infrastrukturen Internet af ting (IoT) er en kritiske virksomhed for alle de involverede med IoT løsninger. På grund af antallet enheder, der er involveret og hvilke fordelt på disse enheder, hvordan en sikkerhed begivenhed, der er relateret til forringer millioner IoT enheder er ikke-trivial og kan have udbredte virkning.

Derfor skal IoT sikkerhed en sikkerhed i dybde fremgangsmåde. Data skal være sikker i skyen og som kan det flyttes over private og offentlige netværk. Metoder skal være på plads til at klargøre sikkert IoT enhederne sig selv. Hvert lag fra enheden, netværket til skyen back end-skal stærke sikkerhed sikkerhed i forbindelse med.

Bedste fremgangsmåder for IoT kan kategoriseres på følgende måde:

- IoT hardware-producenten eller integrator
- IoT løsning udvikler
- IoT løsning deployer
- IoT løsning operator

I denne artikel indeholder en oversigt over [Af ting Sikkerhed bedste fremgangsmåder på internettet](../iot-suite/iot-security-best-practices.md). Se denne artikel for at få mere detaljerede oplysninger.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT hardware-producenten eller integrator

Hvis du er en IoT hardware fremstilling eller en hardware integrator, skal du følge de bedste fremgangsmåder nedenfor:

- **Omfang hardware til minimumskravene**: hardwaredesign skal indeholde mindst funktioner, der kræves til driften af hardwaren, og intet andet. 
- **Gøre hardware ændre bevis**: opbygge i mekanismer til at registrere fysisk forfalskning af hardware, som åbner enhed forsiden, fjerne en del af enhed osv. 
- **Opbygge omkring sikker hardware**: Hvis [forbrug](https://en.wikipedia.org/wiki/Cost_of_goods_sold) tillader, opbygge sikkerhedsfunktioner som sikker og krypterede lager og der er tillid til Platform modul TPM-baserede Start funktionalitet.
- **Foretage opgraderinger sikker**: opgradering firmware under levetiden for enheden er forekommer.

## <a name="iot-solution-developer"></a>IoT løsning udvikler

Hvis du udvikler en IoT løsning, skal du følge de bedste fremgangsmåder nedenfor:

- **Følg sikker software development metode**: udvikle software til sikker kræver bunden tanker om sikkerhed fra starten af projektet helt til dens implementering, test og installation.
- **Vælg open source-software med omtanke**: open source-software giver mulighed for at hurtigt at udvikle løsninger.
- **Integrere med omtanke**: mange af sikkerhedsbrist software findes på kanten af biblioteker og API'er. 

## <a name="iot-solution-deployer"></a>IoT løsning deployer

Følg de bedste fremgangsmåder nedenfor, hvis du er en IoT løsning deployer:

- **Installere hardware sikkert**: IoT installationer kan kræve hardware skal installeres på usikker placeringer, sådan som offentlige mellemrum eller fungerer landestandarder.
- **Beskytte godkendelse taster**: under installationen, hver enhed kræver enheds-id'er og tilhørende godkendelse taster genereret skytjenesten. Beskytte disse taster fysisk lige efter installationen. En hvilken som helst kompromitteret nøgle kan bruges af en skadelig enhed til som en eksisterende enhed.

## <a name="iot-solution-operator"></a>IoT løsning operator

Følg de bedste fremgangsmåder nedenfor, hvis du er en IoT løsning operator:

- **Hold systemer opdateret**: sikre enhed operativsystemer og alle enhedsdrivere er opdateret til de nyeste versioner. 
- **Beskyt mod skadelig aktivitet**: Hvis operativsystemet tillader det, placere seneste anti-virus og antimalware-funktionerne på hver enhed-operativsystemet. 
- **Overvågningslog ofte**: overvågning IoT infrastruktur for sikkerhedsrelaterede problemer er nøgle ved besvarelse sikkerhedsproblemer.
- **Fysisk beskytte IoT infrastrukturen**: de værste sikkerhedsangreb mod IoT infrastruktur er startet ved hjælp af fysisk adgang til enheder.
- **Beskyt skyen legitimationsoplysninger**: skyen godkendelse legitimationsoplysninger, der bruges til konfiguration og operativsystem en IoT installation er muligvis den nemmeste måde at få adgang og forringer et IoT system. 
