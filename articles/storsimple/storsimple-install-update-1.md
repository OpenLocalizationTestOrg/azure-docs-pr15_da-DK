<properties
   pageTitle="Installere opdatering 1.2 på enheden StorSimple | Microsoft Azure"
   description="Forklarer, hvordan du installerer StorSimple 8000 serie opdatering 1.2 på enheden StorSimple 8000 serie."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Installere opdatering 1.2 på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudiet beskrives, hvordan du installerer opdatering 1.2 på en enhed med StorSimple, der kører en softwareversion af før opdatering 1. Selvstudiet dækker også de ekstra trin, der kræves for opdateringen, når en gateway er konfigureret på et netværksgrænseflade end DATA 0 StorSimple enhed.

Opdatering 1.2 indeholder enhed softwareopdateringer, LSI driveropdateringer og disk firmware-opdateringer. Software og LSI driveropdateringer er ikke-forstyrrende opdateringer og kan anvendes via Azure klassisk-portalen. Disken firmware-opdateringer er forstyrrende opdateringer og kan kun anvendes via Windows PowerShell brugergrænsefladen på enheden.

Afhængigt af hvilken version enheden kører, kan du finde Hvis opdatering 1.2 anvendes. Du kan se softwareversionen af din enhed ved at navigere til sektionen **Oversigt over** på din enhed **Dashboard**.

</br>

| Hvis kører softwareversion...   | Hvad sker der i portalen?                              |
|---------------------------------|--------------------------------------------------------------|
| Release - GA                    | Hvis du kører Release-versionen (GA), gælder ikke denne opdatering. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at opdatere din enhed.|
| Opdatere 0,1.                      | Portal gælder opdatering 1.2.                                |
| Opdatere 0,2                      | Portal gælder opdatering 1.2.                                |
| Opdatere 0,3                      | Portal gælder opdatering 1.2.                                |
| Opdatering 1                        | Denne opdatering er ikke tilgængelige.                           |
| Opdatere 1.1                      | Denne opdatering er ikke tilgængelige.                           |

</br>

> [AZURE.IMPORTANT]

> -  Du kan ikke få vist opdatering 1.2 med det samme, fordi vi udføre en faseinddelt udrulning af opdateringer. Søgningen efter opdateringer i et par dage igen som denne opdatering, vil være tilgængelige snart.
> - Denne opdatering indeholder et sæt manuelle og automatiske foreløbig kontrolfunktioner til at bestemme enhed tilstanden med hensyn til hardware stat og netværk connectivity. Disse foreløbige kontrol udføres kun, hvis du anvender opdateringerne fra Azure klassisk portal.
> - Vi anbefaler, at du installerer software og -driver opdateringer via Azure klassisk-portalen. Du skal kun gå til Windows PowerShell brugergrænsefladen på enheden (for at installere opdateringer), hvis før opdatering gateway kontrollen mislykkes i portalen. Opdateringerne kan tage 5-10 timer at installere (herunder Windows-opdateringer). Vedligeholdelse tilstand opdateringer skal installeres via Windows PowerShell brugergrænsefladen på enheden. Som vedligeholdelse tilstand-opdateringer er forstyrrende, vil disse resultere i gangen ned til din enhed.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Installere opdatering 1.2 via Azure klassisk-portalen

Udfør følgende trin for at opdatere din enhed til at [opdatere 1.2](storsimple-update1-release-notes.md). Brug denne procedure, hvis du har en gateway, der er konfigureret på DATA 0 netværksgrænseflade på din enhed.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Kontrollér, at enheden kører **StorSimple 8000 serie opdatering 1.2 (6.3.9600.17584)**. **Senest opdateret dato** bør også ændres. Du kan også se, at vedligeholdelse tilstand opdateringer er tilgængelige (denne meddelelse kan fortsætte med at blive vist i op til 24 timer, når du har installeret opdateringerne).

    Vedligeholdelse tilstand opdateringer er forstyrrende, der medfører enhed nedetid og kan kun anvendes via Windows PowerShell brugergrænsefladen på din enhed.

    ![Vedligeholdelsessiden] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Vedligeholdelsessiden")

13. Hent de vedligeholdelse tilstand opdateringer ved hjælp af trinnene i [downloade hotfixes]( #to-download-hotfixes) at søge efter og hente KB3063416, hvilke installationer disk firmwareopdateringer (de øvrige opdateringer skal allerede være installeret på nuværende tidspunkt).

13. Følg trinnene i [installere og bekræfte vedligeholdelse tilstand hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) til at installere vedligeholdelse tilstand opdateringer.

14. Gå til siden **vedligeholdelse** og nederst på siden i Azure klassisk portal, skal du klikke på **Scanne opdateringer** for at søge efter en hvilken som helst Windows-opdateringer og derefter klikke på **Installer opdateringer**. Du er færdig, når alle opdateringerne er installeret.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Installere opdatering 1.2 på en enhed, der indeholder en gateway, der er konfigureret til en ikke-DATA 0 netværksgrænseflade

Du skal bruge denne procedure, kun, hvis du ikke kontrollen gatewayen, når du forsøger at installere opdateringerne via portalen Azure klassisk. Kontrollen mislykkes, når du har en gateway, der er tildelt til en ikke-DATA 0 netværksgrænseflade og enheden kører en softwareversion af før opdatering 1. Hvis enheden ikke har en gateway på en ikke-DATA 0 netværksgrænseflade, kan du opdatere din enhed direkte fra Azure klassisk portal. Se [installere opdatering 1.2 via Azure klassisk-portalen](#install-update-1.2-via-the-azure-classic-portal).

De softwareversioner, der kan opgraderes ved hjælp af denne metode er Update 0,1, Opdater 0,2 og Update 0,3.


> [AZURE.IMPORTANT]
>
> - Hvis enheden kører Release (GA) version, skal du kontakte [Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at opdateringen.
> - Denne procedure skal kun udføres én gang for at anvende opdatering 1.2. Du kan bruge Azure klassisk portalen til at anvende efterfølgende opdateringer.

Hvis enheden kører før opdatering 1 software, og den har en gateway, der er angivet for et netværksgrænseflade end DATA 0, kan du anvende opdatering 1.2 i følgende to måder:

- **Mulighed 1**: hente opdateringen og anvende den ved hjælp af den `Start-HcsHotfix` cmdlet fra Windows PowerShell-grænseflade på enheden. Dette er den anbefalede metode. **Brug ikke denne metode til at anvende opdatering 1.2, hvis din enhed kører Update 1.0 eller Opdater 1.1.**

- **Mulighed 2**: fjerne gateway-konfigurationen og installere opdateringen direkte fra Azure klassisk portalen.


Detaljerede anvisninger for hver af disse er angivet i de følgende afsnit.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Mulighed 1: Brug af Windows PowerShell til StorSimple anvende opdatering 1.2 som en programrettelse

Du skal bruge denne procedure, kun, hvis du kører opdatering 0,1, 0,2, 0,3 og hvis din gateway check mislykkes, når du forsøger at installere opdateringer fra Azure klassisk portal. Hvis du kører Release (GA) software, kan du [Microsoft Support](storsimple-contact-microsoft-support.md) for at opdatere din enhed.

For at installere opdatering 1.2 som en programrettelse, skal du hente og installere følgende hotfixes:

| Rækkefølge  | KB        | Beskrivelse             | Opdateringstype  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Softwareopdatering         |  Almindelige     |
| 2      | KB3043005 | LSI SAS controller opdatering |  Almindelige     |
| 3      | KB3063416 | Disken firmware           | Vedligeholdelse  |

Før du bruger denne procedure til at anvende opdateringen, Sørg for, at:

- Begge enhed enheder er online.

Udfør følgende trin for at anvende opdatering 1.2. **Opdateringerne kan tage omkring 2 timer for at gennemføre (cirka 30 minutter til software, 30 minutter for driveren 45 minutter for disk firmware).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Mulighed 2: Brug Azure klassisk portalen for at anvende opdatering 1.2 Når du har fjernet gateway-konfigurationen

Denne fremgangsmåde gælder kun for StorSimple enheder, der kører en softwareversion af før opdatering 1 og har en gateway på et netværksgrænsefladen end DATA 0. Du skal fjerne indstillingen gateway før anvendelse af denne opdatering.

Opdater kan tage et par timer at gennemføre. Hvis din hosts findes i forskellige undernet, kan fjerne gateway konfiguration på iSCSI-grænseflader medføre nedetid. Vi anbefaler, at du konfigurerer DATA 0 for iSCSI-trafik for at reducere nedetiden.

Udfør følgende trin for at deaktivere netværksgrænsefladen med gateway og derefter anvende opdateringen.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Næste trin

Lær mere om [opdatering 1.2 version](storsimple-update1-release-notes.md).
