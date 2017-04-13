<properties
   pageTitle="Installere opdatering 3 på enheden StorSimple | Microsoft Azure"
   description="Forklarer, hvordan du installerer StorSimple 8000 serie Update 3 på enheden StorSimple 8000 serie."
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>Installere opdatering 3 på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium forklares det, hvordan du installerer opdatering 3 på en StorSimple enhed kører en tidligere softwareversion via Azure klassisk portalen og ved hjælp af metoden programrettelse. Metoden programrettelse bruges, når en gateway er konfigureret på et netværksgrænseflade end DATA 0 på StorSimple enheden, og du forsøger at opdatere fra en før opdatering 1 softwareversion.

Opdatering 3 indeholder enhedssoftware, LSI driver og firmware, måde og Spaceport opdateres. Hvis opdaterer fra opdatering 2 eller en tidligere version, kan du kan også påkrævet for at anvende iSCSI, WMI, og i visse tilfælde disk firmware-opdateringer. Den enhedssoftware, WMI, iSCSI, LSI driver, Spaceport og måde rettelser er ikke-forstyrrende opdateringer og kan anvendes via Azure klassisk-portalen. Disken firmware-opdateringer er forstyrrende opdateringer og kan kun anvendes via Windows PowerShell brugergrænsefladen på enheden. 

> [AZURE.IMPORTANT]

> - Et sæt af manuelle og automatiske foreløbig Kontroller gøres før Installer for at bestemme enhed sundheden med hensyn til hardware stat og netværk connectivity. Disse foreløbige kontrol udføres kun, hvis du anvender opdateringerne fra Azure klassisk portal.
> - Vi anbefaler, at du installerer software og -driver opdateringer via Azure klassisk-portalen. Du skal kun gå til Windows PowerShell brugergrænsefladen på enheden (for at installere opdateringer), hvis før opdatering gateway kontrollen mislykkes i portalen. Afhængigt af den version, du er ved at opdatere fra opdateringerne, kan det tage 1,5 2,5 timer at installere. Vedligeholdelse tilstand opdateringer skal installeres via Windows PowerShell brugergrænsefladen på enheden. Som vedligeholdelse tilstand-opdateringer er forstyrrende, vil disse resultere i gangen ned til din enhed.
> - Hvis kører valgfrit StorSimple øjebliksbillede Manager, kan du sikre dig, at du har opgraderet dit øjebliksbillede Manager version til opdatering 2 før opdatering af enheden.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Installere opdatering 3 via Azure klassisk-portalen

Udfør følgende trin for at opdatere din enhed til at [opdatere 3](storsimple-update3-release-notes.md).


> [AZURE.NOTE]
Hvis du anvender opdatering 2 eller nyere (herunder Update 2.1), vil Microsoft kunne hente yderligere diagnostiske oplysninger fra enheden. Som et resultat, når vores operations-teamet identificerer enheder, der er problemer, er vi bedre udstyret til at indsamle oplysninger fra enheden og diagnosticere problemer. Ved at acceptere opdatering 2 eller nyere, du har givet os til at levere denne proaktiv support.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Kontrollér, at enheden kører **StorSimple 8000 serie Update 3 (6.3.9600.17759)**. **Senest opdateret dato** bør også ændres. 

    Hvis du opdaterer fra en tidligere version end opdatering 2, kan du også se, at vedligeholdelse tilstand opdateringerne er tilgængelig (denne meddelelse kan fortsætte med at blive vist i op til 24 timer, når du har installeret opdateringerne).

    Vedligeholdelse tilstand opdateringer er forstyrrende, der medfører enhed nedetid og kan kun anvendes via Windows PowerShell brugergrænsefladen på din enhed. I nogle tilfælde når du kører opdatering 1.2 være din disk firmware allerede opdateret, hvorefter du ikke behøver at installere vedligeholdelse tilstand opdateringer.

    Hvis du opdaterer fra opdatering 2 eller nyere, vil din enhed skulle nu være opdateret. Du kan springe de resterende trin.

13. Hent de vedligeholdelse tilstand opdateringer ved hjælp af trinnene i [downloade hotfixes](#to-download-hotfixes) at søge efter og hente KB3121899, hvilke installationer disk firmwareopdateringer (de øvrige opdateringer skal allerede være installeret på nuværende tidspunkt).

13. Følg trinnene i [installere og bekræfte vedligeholdelse tilstand hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) til at installere vedligeholdelse tilstand opdateringer. 

  

## <a name="install-update-3-as-a-hotfix"></a>Installere opdatering 3 som en programrettelse

Brug denne procedure, hvis du ikke kontrollen gatewayen, når du forsøger at installere opdateringerne via portalen Azure klassisk. Kontrollen mislykkes, når du har en gateway, der er tildelt til en ikke-DATA 0 netværksgrænseflade og enheden kører en softwareversion af før opdatering 1.

De softwareversioner, der kan opgraderes ved hjælp af metoden programrettelse er:

- Opdatere 0.1, 0,2 eller, 0,3
- Opdatere 1, 1.1, 1.2
- Opdatere 2, 2.1, 2.2 

> [AZURE.IMPORTANT]
>
> - Hvis enheden kører Release (GA) version, skal du kontakte [Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at opdateringen.

Metoden programrettelse omfatter følgende tre trin:

1.  Du kan hente hotfixene fra Microsoft Update-kataloget.

2.  Installer og Kontrollér almindelige tilstand hotfixes.

3.  Installere, og bekræft den vedligeholdelse tilstand programrettelse (kun ved opdatering fra før opdatering 2 software).


#### <a name="download-updates-for-your-device"></a>Hente opdateringer til din enhed

**Hvis enheden kører opdatering 2.1 eller 2.2**, skal du hente og installere følgende hotfixes i den angivne rækkefølge:

| Rækkefølge  | KB        | Beskrivelse                    | Opdateringstype  | Installere tid |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3186843 | Softwareopdatering & #42;  |  Almindelige <br></br>Ikke-forstyrrende     | ~ 45 minutter |
| 2.      | KB3186859 | LSI driver og firmware             |  Almindelige <br></br>Ikke-forstyrrende      | ~ 20 minutter |
| 3.      | KB3121261 | Måde og Spaceport fix </br> Windows Server 2012 R2 |  Almindelige <br></br>Ikke-forstyrrende      | ~ 20 minutter |

& #42  *Note software update består af to binære filer: enhed softwareopdatering med foranstillet `all-hcsmdssoftwareupdate` og den Cis og Mds agent med foranstillet `all-cismdsagentupdatebundle`. Softwareopdatering enhed skal være installeret før Cis og Mds agent. Du skal også genstarte den aktive controller via den `Restart-HcsController` cmdlet, når du har anvendt Cis og Mds agent opdatere (og før anvendelse af de resterende opdaterer).* 


**Hvis enheden kører opdatering 0,1, 0,2, 0,3, 1.0, 1.1, 1.2, eller 2.0**, skal du hente og installere følgende hotfixes ud over software, LSI driver og firmware-opdateringer (vist i ovenstående tabel), i den angivne rækkefølge:

| Rækkefølge  | KB        | Beskrivelse                    | Opdateringstype  | Installere tid |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3146621 | iSCSI-pakke | Almindelige <br></br>Ikke-forstyrrende  | ~ 20 minutter |
| 5.      | KB3103616 | WMI-pakke |  Almindelige <br></br>Ikke-forstyrrende      | ~ 12 min. |


<br></br>

**Hvis enheden kører version 0,2, 0,3, 1.0, 1.1, og 1.2**, kan du også skal installere disk firmwareopdateringer oven over alle de opdateringer, der vises i de foregående tabeller. Du kan kontrollere, om du skal bruge disk firmware-opdateringer ved at køre den `Get-HcsFirmwareVersion` cmdlet. Hvis du kører disse firmwareversioner: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, og du ikke behøver at installere disse opdateringer.


| Rækkefølge  | KB        | Beskrivelse                    | Opdateringstype  | Installere tid |
|--------|-----------|-------------------------|------------- |-------------|
| 6.      | KB3121899 | Disken firmware              |  Vedligeholdelse <br></br>Forstyrrende      | ~ 30 minutter |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Denne procedure skal kun udføres én gang for at anvende opdatering 3. Du kan bruge Azure klassisk portalen til at anvende efterfølgende opdateringer.
> - Hvis opdatering fra opdatering 2.2, er det samlede Installer tidspunkt tæt 1.1 timer.
> - Før du bruger denne procedure til at anvende opdateringen, Kontrollér, at både enhed enheder er online og alle hardwarekomponenterne er sund.

Udfør følgende trin for at hente og installere hotfixene.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Næste trin

Lær mere om [opdatering 3-versionen](storsimple-update3-release-notes.md).
