<properties
   pageTitle="Installere opdatering 2 på enheden StorSimple | Microsoft Azure"
   description="Forklarer, hvordan du installerer StorSimple 8000 serie opdatering 2 på enheden StorSimple 8000 serie."
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

# <a name="install-update-2-on-your-storsimple-device"></a>Installere opdatering 2 på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du installerer opdatering 2 på en StorSimple enhed kører en tidligere softwareversion via portalen Azure klassisk. Selvstudiet beskrives også de trin, der kræves til opdateringen, når en gateway er konfigureret på et netværksgrænseflade end DATA 0 StorSimple enhed, og du forsøger at opdatere fra en før opdatering 1 softwareversion.

Opdatering 2 indeholder enhed softwareopdateringer LSI driveropdateringer og disk firmware-opdateringer. Enhedssoftwaren og de LSI opdateringer er ikke-forstyrrende opdateringer og kan anvendes via Azure klassisk-portalen. Disken firmware-opdateringer er forstyrrende opdateringer og kan kun anvendes via Windows PowerShell brugergrænsefladen på enheden.

> [AZURE.IMPORTANT]

> -  Du kan ikke få vist opdatering 2 med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Søgningen efter opdateringer i et par dage igen som denne opdatering, vil være tilgængelige snart.
> - Et sæt af manuelle og automatiske foreløbig Kontroller gøres før Installer for at bestemme enhed sundheden med hensyn til hardware stat og netværk connectivity. Disse foreløbige kontrol udføres kun, hvis du anvender opdateringerne fra Azure klassisk portal.
> - Vi anbefaler, at du installerer software og -driver opdateringer via Azure klassisk-portalen. Du skal kun gå til Windows PowerShell brugergrænsefladen på enheden (for at installere opdateringer), hvis før opdatering gateway kontrollen mislykkes i portalen. Opdateringerne kan tage 4-7 timer at installere (herunder Windows-opdateringer). Være skal installeret vedligeholdelse tilstand opdateringer via Windows PowerShell brugergrænsefladen på enheden. Som vedligeholdelse tilstand-opdateringer er forstyrrende, vil disse resultere i gangen ned til din enhed.
> - Hvis kører valgfrit StorSimple Snapshot Manager, kan du sikre dig, at du har opgraderet dit Snapshot Manager version til opdatering 2 før opdatering af enheden.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Installere opdatering 2 via Azure klassisk-portalen

Udfør følgende trin for at opdatere din enhed til at [opdatere 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Opdatering 2 hjælper Microsoft med at adskille flere diagnostiske oplysninger fra enheden. Som et resultat, når vores operations-teamet identificerer enheder, der er problemer, er vi bedre udstyret til at indsamle oplysninger fra enheden og diagnosticere problemer. Ved at acceptere opdatering 2, at du har givet os til at levere denne proaktiv support.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Kontrollér, at enheden kører **StorSimple 8000 serie opdatering 2 (6.3.9600.17673)**. **Senest opdateret dato** bør også ændres. Du kan også se, at vedligeholdelse tilstand opdateringer er tilgængelige (denne meddelelse kan fortsætte med at blive vist i op til 24 timer, når du har installeret opdateringerne).

    Vedligeholdelse tilstand opdateringer er forstyrrende, der medfører enhed nedetid og kan kun anvendes via Windows PowerShell brugergrænsefladen på din enhed. I nogle tilfælde når du kører opdatering 1.2 være din disk firmware allerede opdateret, hvorefter du ikke behøver at installere vedligeholdelse tilstand opdateringer.

13. Hent de vedligeholdelse tilstand opdateringer ved hjælp af trinnene i [downloade hotfixes](#to-download-hotfixes) at søge efter og hente KB3121899, hvilke installationer disk firmwareopdateringer (de øvrige opdateringer skal allerede være installeret på nuværende tidspunkt).

13. Følg trinnene i [installere og bekræfte vedligeholdelse tilstand hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) til at installere vedligeholdelse tilstand opdateringer.


## <a name="install-update-2-as-a-hotfix"></a>Installere opdatering 2 som en programrettelse

Brug denne procedure, hvis du ikke kontrollen gatewayen, når du forsøger at installere opdateringerne via portalen Azure klassisk. Kontrollen mislykkes, når du har en gateway, der er tildelt til en ikke-DATA 0 netværksgrænseflade og enheden kører en softwareversion af før opdatering 1.

De softwareversioner, der kan opgraderes ved hjælp af metoden programrettelse er opdatering 0,1, Opdater 0,2, og Opdater 0,3, opdatering 1, Opdater 1.1 og opdatering 1.2. Metoden programrettelse omfatter følgende tre trin:

- Du kan hente hotfixene fra Microsoft Update-kataloget.
- Installer og Kontrollér almindelige tilstand hotfixes.
- Installere, og bekræft den vedligeholdelse tilstand programrettelse.

For at installere opdatering 2 som en programrettelse, skal du hente og installere følgende hotfixes:

| Rækkefølge  | KB        | Beskrivelse                    | Opdateringstype  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Softwareopdatering         |  Almindelige     |
| 2      | KB3121900 | LSI driver              |  Almindelige     |
| 3      | KB3080728 | Måde fix </br> Windows Server 2012 R2 |  Almindelige     |
| 4      | KB3090322 | Spaceport fix </br> Windows Server 2012 R2 |  Almindelige     |
| 5      | KB3121899 | Disken firmware           | Vedligeholdelse  |


> [AZURE.IMPORTANT]
>
> - Hvis enheden kører Release (GA) version, skal du kontakte [Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at opdateringen.
> - Denne procedure skal kun udføres én gang for at anvende opdatering 2. Du kan bruge Azure klassisk portalen til at anvende efterfølgende opdateringer.
> - Hver programrettelse installation kan tage om 20 minutter for at gennemføre. Samlet Installer tidspunkt er tæt på to timer.
> - Før du bruger denne procedure til at anvende opdateringen, Kontrollér, at begge enhed enheder er online og alle hardwarekomponenterne er sund.

Udfør følgende trin for at anvende denne opdatering som en programrettelse.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Næste trin

Lær mere om [opdatering 2-version](storsimple-update2-release-notes.md).
