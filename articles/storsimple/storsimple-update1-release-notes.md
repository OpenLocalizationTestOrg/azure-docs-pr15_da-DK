<properties 
   pageTitle="StorSimple 8000 serie opdatering 1.2 produktbemærkninger | Microsoft Azure"
   description="Beskriver de nye funktioner, problemer og løsninger til StorSimple 8000 serie opdatering 1.2."
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
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>StorSimple 8000 serie opdatering 1.2 produktbemærkninger  

## <a name="overview"></a>Oversigt

Følgende produktbemærkninger beskriver de nye funktioner og identificere de kritiske åbne problemer for StorSimple 8000 serie opdatering 1.2. De indeholder også en liste over StorSimple software, -driver og disk firmware-opdateringer i denne version. 

Opdater 1.2 kan anvendes på en hvilken som helst StorSimple enhed kører Release (GA), Opdater 0,1, Opdater 0,2 eller Opdater 0,3 software. Opdatering 1.2 er ikke tilgængelig, hvis din enhed kører opdatering 1 eller Opdater 1.1. Hvis enheden kører Release (GA), kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at installere denne opdatering.

I følgende tabel vises de enhed softwareversioner, der svarer til opdateringer 1, 1.1 og 1.2.

| Hvis kører opdatering... | Dette er din enhed softwareversion. |
|---------------------|---------------------------------------|
| Opdatere 1.2          | 6.3.9600.17584                        |
| Opdatere 1.1          | 6.3.9600.17521                        |
| Opdatere 1.0          | 6.3.9600.17491                        |

Gennemgå oplysningerne i produktbemærkninger, før du installerer opdateringen i din StorSimple løsning. Finde flere oplysninger, hvordan du [installerer opdatering 1.2 på enheden StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Det tager ca. 5-10 timer at installere opdateringen (herunder Windows-opdateringer). 
- Opdatering 1.2 har software, LSI driver og disk firmware-opdateringer. For at installere skal du følge vejledningen i [installere opdatering 1.2 på enheden StorSimple](storsimple-install-update-1.md).
- For nye versioner, kan du ikke kan se opdateringer med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Søgningen efter opdateringer i et par dage igen som disse bliver tilgængelig snart.


## <a name="whats-new-in-update-12"></a>Hvad er nyt i opdatering 1.2

Disse funktioner er først udsendt med opdatering 1, blev gjort tilgængelige for et begrænset antal brugere. Med release opdatering 1.2 vil de fleste af StorSimple brugerne se de følgende nye funktioner og forbedringer:

- **Overførsel fra 5000-7000 serie til 8000 serie enheder** – denne version introducerer en ny overførsel funktion, som StorSimple 5000-7000 serie anvendelse giver brugerne mulighed at overføre deres data til en fysisk anvendelses StorSimple 8000 serie eller et virtuelt maskinen. Funktionen overførsel har to vigtige tilbud:                                                                  

    - **Forretningskontinuitet**, ved at aktivere overførsel af eksisterende data på 5000-7000 serie udstyr til 8000 serie udstyr.
    - **Forbedret funktion tilbud af 8000 serie hjælpemidler**, som effektivt Central administration af flere hjælpemidler via StorSimple Manager service bedre type hardware og de opdaterede firmware, virtuelle anvendelser, datamobilitet og funktioner i fremtidige køreplanen.

    Se [overførselsvejledningen](http://www.microsoft.com/download/details.aspx?id=47322) få mere at vide om, hvordan du overfører en StorSimple 5000-7000 række til en 8000 serie enhed. 

- **Tilgængelighed i portalen Azure Government** – StorSimple er nu tilgængelig på portalen Azure Government. Se, hvordan du kan [installere en StorSimple enhed i portalen Azure Government](storsimple-deployment-walkthrough-gov.md).

- **Understøttelse af andre skyen tjenesteudbydere** – de andre skyen tjenesteudbydere understøttes er Amazon S3, Amazon S3 med Ressourceposter, HP og OpenStack (beta).

- **Opdatering til seneste lagerplads API'er** – er med denne version StorSimple blevet opdateret til den seneste Azure-lager tjeneste API'er. StorSimple 8000 serie enheder, der kører før opdatering 1 softwareversioner (version 0.1, 0,2 og 0,3) bruger versioner af ældre end 17 juli 2009 Azure lagerplads Service API'er. Som angivet i opdaterede [meddelelse om fjernelse af lager service versioner](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)ved 1 August 2016, vil disse API'er være forældet. Det er vigtigt, at du anvender StorSimple 8000 serie Update 1 før August 1 2016. Hvis du undlader at gøre det, stopper StorSimple enheder fungerer korrekt.

- **Support til Zone overflødige lagerplads (ZRS)** – med opgraderingen til den nyeste version af API'er Storage StorSimple 8000 serien understøtter Zone overflødige lagerplads (ZRS) foruden lokalt overflødige lagerplads (LRS) og geografisk overflødige lagerplads (GRS). Se denne [artikel om Azure-lager redundans indstillinger](../storage/storage-redundancy.md) for ZRS detaljer.

- **Udvidet indledende installation og opdatering oplevelse** – er i denne udgave, installation og opdateringsprocesser blevet forbedret. Installationen via installationsguiden er forbedret for at give feedback til brugeren, hvis netværkskonfiguration og firewall-indstillinger er forkerte. Yderligere diagnosticering cmdletter har fået kan hjælpe dig med at foretage fejlfinding af netværk på enheden. Se [fejlfinding i forbindelse med installation artikel](storsimple-troubleshoot-deployment.md) finde flere oplysninger om den nye diagnosticering cmdletter, der bruges til fejlfinding.

## <a name="issues-fixed-in-update-12"></a>Problemer, der er rettet i opdatering 1.2

Den følgende tabel indeholder en oversigt over problemer, der er blevet løst i opdateringer 1.2, 1.1 og 1.    


| Nej. | Funktion | Problem | Løst i opdatering | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell til StorSimple | Når en bruger fjernadgang til StorSimple enhed ved hjælp af Windows PowerShell til StorSimple og derefter i gang konfigurationsguiden, opstod nedbrud så tidligt som datainput 0 IP blev. Denne fejl er nu løst i opdatering 1. | Opdatering 1 | Ja | Ja |
| 2 | Factory Nulstil | I visse tilfælde, når du har udført en fabriksindstillingerne StorSimple enheden blev fast og vises denne meddelelse: **Nulstil til factory er i gang (fase 8)**. Dette sket, hvis du trykker på CTRL + C, mens cmdlet var i gang. Denne fejl er nu rettet.| Opdatering 1 | Ja | Nej |
| 3 | Factory Nulstil | Når en mislykkedes dobbelt controller fabrik nulstillet, er du tilladelse til at fortsætte med enhed registrering. Dette er et resultat i en ikke-understøttede systemkonfiguration. Der vises en fejlmeddelelse i opdatering 1 og registrering er blokeret på en enhed, er en mislykkedes fabrik nulstillet. | Opdatering 1 | Ja | Nej |
| 4 | Factory Nulstil | I nogle tilfælde er opløftet falsk positiv uoverensstemmelse beskeder. Forkert uoverensstemmelse beskeder genereres ikke længere på enheder, der kører opdatering 1. | Opdatering 1 | Ja | Nej |
| 5 | Factory Nulstil | Hvis en fabriksindstillingerne blev afbrudt inden fuldførelse, enheden angivet tilstanden nedbrud og giver ikke dig adgang til Windows PowerShell til StorSimple. Denne fejl er nu rettet. | Opdatering 1 | Ja | Nej |
| 6 | Nedbrud | En nedbrud gendannelse (DR) fejl er blevet løst hvori DR ville mislykkes under registrering af sikkerhedskopier på målenheden. | Opdatering 1 | Ja | Ja |
| 7 | Overvågning indikatorer | I visse tilfælde overvågning indikatorer bag maskinen ikke angive korrekte status. Den blå Indikator blev slået fra. DATA 0 og DATA 1 indikatorer blinker, selv når disse grænseflader ikke er konfigureret. Problemet er løst og overvågning indikatorer nu angive den korrekte status.  | Opdatering 1 | Ja | Nej |
| 8 | Overvågning indikatorer | I visse tilfælde, når du har anvendt opdatering 1 det blå lys på den aktive controller slået fra derved gør det svært at identificere den aktive controller. Problemet er løst i denne programrettelse udgave.| Opdatere 1.2 | Ja | Nej |
| 9 | Netværksgrænseflader | I tidligere versioner kunne en StorSimple enhed, der er konfigureret med en ikke-distribueret gateway går offline. I denne udgave rutemetrik for Data 0 er blevet foretaget den laveste; derfor, selvom andre netværksgrænseflader er aktiveret til skyen, dirigeres al skyen trafik fra enheden via Data 0. | Opdatering 1 | Ja | Ja | 
| 10 | Sikkerhedskopier | En fejl i opdatering 1 som forårsagede sikkerhedskopier mislykkes efter 24 dage er blevet løst i programrettelse version 1.1 opdatering. | Opdatere 1.1 | Ja | Ja |
| 11 | Sikkerhedskopier | En fejl i tidligere versioner udløste dårlig ydeevne til skyen snapshots med lav Skift satser. Denne fejl er blevet løst i denne programrettelse udgave.| Opdatere 1.2 | Ja | Ja |
| 12 | Opdateringer | En fejl i opdatering 1, der rapporteres en mislykket opgradering og forårsaget enheder gå til tilstanden for nedbrud, er blevet løst i denne programrettelse udgave.| Opdatere 1.2 | Ja | Ja |


## <a name="known-issues-in-update-12"></a>Kendte problemer i opdatering 1.2

Den følgende tabel indeholder en oversigt over kendte problemer i denne udgave.

| Nej. | Funktion | Problem | Kommentarer/løsning | Gælder for fysiske enhed | Gælder for virtuel enhed |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Disken quorum | I sjældne tilfælde, hvis fleste diske i EBOD omslutning af en 8600 enhed ikke er tilsluttet medfører ingen disk quorum bliver lagerpulje offline. Det forbliver offline, selvom diskene er genoprettet. | Du skal genstarte enheden. Hvis problemet fortsætter, skal du kontakte Microsoft Support til næste trin. | Ja | Nej |
| 2 | Forkert enheds-ID | Når der udføres controller erstatning, kan controller 0 ses som controller 1. Under controller erstatning, når billedet er indlæst fra noden peer kan enheds-ID vises først som peer-controller-ID. I sjældne tilfælde ses problemet også efter en genstart af systemet. | Ingen brugerhandling er påkrævet. Denne situation vil løse selve, når controller erstatningen er fuldført. | Ja | Nej |
| 3 | Lagerplads konti | Brug af tjenesten Storage til at slette kontoen lagerplads er en ikke-understøttede scenarie. Dette vil føre til en situation, hvor du ikke kan hentes brugerdata. | Ja | Ja |
| 4 | Enhed failover | Flere failover af en volumenlicens objektbeholder fra den samme enhed kilde til anden destinationsenheder understøttes ikke. Enhed failover fra en enkelt inaktive enhed til flere enheder, bliver lydstyrken beholdere på første mislykkedes over enhed mister dataejerskab. Efter en failover, vil disse lydstyrken beholdere vises eller fungerer anderledes, når du får vist dem på portalen Azure klassisk. | | Ja | Nej |
| 5 | Installation | Under StorSimple Adapter til SharePoint-installation skal du angive en enhed IP-adresse i rækkefølge for at ændringerne kan fuldføres korrekt.    | | Ja | Nej |
| 6 | Webtjenesteproxy | Hvis din web proxykonfiguration har HTTPS som den angivne protokol, påvirkes din enhed service-kommunikation, og enheden går offline. Supportpakker genereres også i processen, forbrug betydelige ressourcer på din enhed. | Sørg for, at URL-adressen til web-proxy har HTTP som den angivne protokol. Få mere at vide ved at gå til [Konfigurer webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md). | Ja | Nej |
| 7 | Webtjenesteproxy | Hvis du konfigurerer og aktivere webtjenesteproxy på en registrerede enhed, skal du genstarte den aktive controller på din enhed. | | Ja | Nej |
| 8 | Høj skyen ventetid og høj i/o-arbejdsbelastning | Når enheden StorSimple støder på en kombination af meget høj skyen latenstider (rækkefølgen af sekunder) og høj i/o-arbejdsbelastningen, enhed enhederne gå i en nedgraderede, og i/O'er mislykkes muligvis med fejlen "enheden er ikke klar". | Du skal genstarte enheden enheder eller udføre en enhed failover til at gendanne fra denne situation manuelt. | Ja | Nej |
| 9 | Azure PowerShell | Når du bruger StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential & #124; Vælg-objekt - først 1 - Vent** for at markere det første objekt, så du kan oprette et nyt **VolumeContainer** -objekt, cmdlet returnerer alle objekter. | Ombryde cmdlet i parenteser på følgende måde: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Vælg-objekt - først 1 - Vent** | Ja | Ja |
| 10| Overførsel | Når flere lydstyrken objektbeholdere er gået til overførsel, er ETA til seneste sikkerhedskopi nøjagtig kun for objektbeholderen første lydstyrken. Desuden starter parallelle overførsel efter de første 4 sikkerhedskopier i objektbeholderen første lydstyrken overføres. | Vi anbefaler, at du overfører én lydstyrken objektbeholder ad gangen. | Ja | Nej |
| 11| Overførsel | Efter indstillingen Gendan er enheder ikke føjet til politikken sikkerhedskopiering eller gruppen virtuelle disk. | Du skal tilføje disse enheder til en politik for sikkerhedskopiering for at oprette sikkerhedskopier. | Ja | Ja |
| 12| Overførsel | Når overførslen er færdig, skal 5000/7000 serie enheden ikke få adgang til de overførte databeholdere. | Vi anbefaler, at du sletter overførte databeholdere, når overførslen er fuldført og anvendt. | Ja | Nej |
| 13| Klon og DR | En StorSimple enhed kører opdatering 1 kan ikke klone eller udføre nedbrud til en enhed kører før opdatering 1 software. | Du skal opdatere den destinationsadresse-enhed til at opdatere 1 for at tillade disse handlinger | Ja | Ja |
| 14 | Overførsel | Sikkerhedskopiering af konfiguration for overførslen kan mislykkes på en enhed med 5000-7000 serie, når der er lydstyrken grupper med nogen tilknyttede enheder. | Slette alle tomme lydstyrken grupper med nogen tilknyttede enheder, og prøv at konfiguration sikkerhedskopieringen.| Ja | Nej |

## <a name="physical-device-updates-in-update-12"></a>Fysisk enhedsopdateringer i opdatering 1.2

Hvis programrettelse opdatere 1.2 anvendes på en fysisk enhed (kører versioner før Update 1), ændrer softwareversionen til 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controller og firmware opdateringer i opdatering 1.2

Denne version opdaterer driveren og disk firmware på din enhed.
 
- Se [opdatere 1 ved LSI SAS enheder i Microsoft Azure StorSimple maskinen](https://support.microsoft.com/kb/3043005)kan finde flere oplysninger om opdateringen SAS controller. 

- Du kan finde flere oplysninger om disk firmware opdatere, skal du se [Disk firmware Update 1 til Microsoft Azure StorSimple maskinen](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Virtuel enhedsopdateringer i opdatering 1.2

Denne opdatering kan ikke anvendes til den virtuelle enhed. Nye virtuelle enheder skal oprettes. 

## <a name="next-steps"></a>Næste trin

- [Installere opdatering 1.2 på din enhed](storsimple-install-update-1.md).
 
