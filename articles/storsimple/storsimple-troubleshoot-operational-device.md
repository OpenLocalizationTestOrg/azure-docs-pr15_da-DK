<properties 
   pageTitle="Fejlfinding i forbindelse med en udløst StorSimple enhed | Microsoft Azure"
   description="Beskriver, hvordan du diagnosticere og løse fejl, der opstår på en enhed med StorSimple, der aktuelt er installeret og i funktion."
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

# <a name="troubleshoot-an-operational-storsimple-device"></a>Fejlfinding i forbindelse med en funktionel StorSimple-enhed

## <a name="overview"></a>Oversigt

I denne artikel indeholder en praktisk fejlfinding vejledning til at løse problemer med konfiguration, der kan opstå, når enheden StorSimple er installeret og bruges. Den beskriver almindelige problemer, mulige årsager og anbefalede trin for at hjælpe dig med at løse problemer, der kan opstå, når du kører Microsoft Azure StorSimple. Disse oplysninger gælder for både den fysiske enhed StorSimple lokalt og den virtuelle StorSimple-enhed.

I slutningen af denne artikel, du kan finde en liste over fejlkoder, der kan opstå under Microsoft Azure StorSimple handling, samt trin kan du drage skal løse fejlene. 

## <a name="setup-wizard-process-for-operational-devices"></a>Konfigurationen af guiden til aktive enheder

Du kan bruge konfigurationsguiden ([Aktiver HcsSetupWizard][1]) til at kontrollere konfigurationen af enheden og udføre korrigerende handling, hvis det er nødvendigt.

Når du kører installationsguiden på en enhed med tidligere har konfigureret og drift, er procesforløbet anderledes. Du kan ændre kun følgende poster:

- IP-adresse, undernetmaske og gateway
- Primær DNS-server
- Primære NTP-server
- Konfiguration af valgfrit web proxy

Konfigurationsguiden udfører ikke de handlinger, der er relateret til adgangskode indsamling og enhed registrering.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fejl, der opstår under efterfølgende kører i installationsguiden

I følgende tabel beskrives fejl, du kan støde på, når du kører konfigurationsguiden på en funktionel enhed, mulige årsager til fejl, og anbefalede handlinger til at løse dem. 

| Nej. | Fejlmeddelelse eller betingelse | Mulige årsager | Anbefalede handling |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Fejl 350032: Denne enhed er allerede blevet deaktiveret. | Du får vist denne fejl, hvis du kører installationsguiden på en enhed, der er deaktiveret. | [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) til næste trin. En deaktiveret enhed kan ikke sættes i tjenesten. En fabrik Nulstil kan der kræves, før enheden kan aktiveres igen. |
|  2  | Kalde HcsSetupWizard: ERROR_INVALID_FUNCTION (undtagelse fra HRESULT: 0x80070001) | Opdateringen til DNS-server går ned. DNS-indstillingerne er globale indstillinger og anvendes på tværs af alle aktiveret netværksgrænseflader. | Aktivere grænsefladen, og Anvend DNS-indstillingerne igen. Dette kan afbryde netværk for andre aktiveret grænseflader, fordi disse indstillinger er global. |
|  3  | Enheden ser ud til at være online i serviceportalen StorSimple Manager, men når du forsøger at fuldføre mindste konfigurationen og gemme konfigurationen, mislykkes handlingen. | Under den indledende installation webproxy blev ikke er konfigureret, selvom der var en faktisk proxy-server i stedet. | Brug [Test HcsmConnection cmdlet] [ 2] til at finde fejlen. [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) , hvis du ikke kan løse problemet. |
|  4  | Kalde HcsSetupWizard: Værdi falder ikke inden for det forventede område. | En forkert undernetmaske giver denne fejl. Mulige årsager er: <ul><li> Undernetmasken er mangler eller er tomt.</li><li>IPv6-præfiks formatet er forkert.</li><li>Grænsefladen er skyen aktiverede, men gatewayen er manglende eller forkerte.</li></ul>Bemærk, at DATAENE 0 automatisk skyen aktiverede Hvis konfigureret gennem konfigurationsguiden. | For at bestemme problemet skal du bruge undernet 0.0.0.0 eller 256.256.256.256 og derefter se på output. Angiv korrekte værdier for undernetmaske, gateway og IPv6-præfiks, efter behov. |
 
## <a name="error-codes"></a>Fejlkoder

Fejl vises i numerisk rækkefølge.

|Fejlnummer|Fejltekst eller beskrivelse|Anbefalede brugerhandling|
|:---|:---|:---|
|10502|Der opstod en fejl under adgang til kontoen lagerplads.|Vent et par minutter, og prøv derefter igen. Hvis problemet fortsætter, kan du Kontakt Microsoft Support til næste trin.|
|40017|Sikkerhedskopieringen mislykkes, når en enhed, der er angivet i politikken sikkerhedskopiering ikke blev fundet på enheden.|Forsøge at sikkerhedskopiere igen handlingen, hvis fejlen opstår igen, skal du kontakte Microsoft Support. til næste trin.|
|40018|Sikkerhedskopieringen mislykkes, når ingen af de enheder, der er angivet i politikken sikkerhedskopiering blev ikke fundet på enheden. |Forsøge at sikkerhedskopiere igen handlingen, hvis fejlen opstår igen, skal du kontakte Microsoft Support. til næste trin.|
|390061|Systemet er optaget eller ikke tilgængelig.|Vent et par minutter, og prøv derefter igen. Hvis problemet fortsætter, kan du Kontakt Microsoft Support til næste trin.|
|390143|Der opstod en fejl med fejlkode 390143. (Ukendt fejl).|Hvis det ikke løser problemet, skal du kontakte Microsoft Support til næste trin.|

## <a name="next-steps"></a>Næste trin

Hvis du ikke kan løse problemet, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at få hjælp. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
