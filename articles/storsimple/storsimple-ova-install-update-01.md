<properties 
   pageTitle="Installere opdateringer på en StorSimple virtuelle matrix | Microsoft Azure"
   description="Beskriver, hvordan du kan bruge webdelen StorSimple virtuelle matrix brugergrænseflade til at anvende opdateringer ved hjælp af metoden portal og programrettelse"
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
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Installere opdateringer på din StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt

I denne artikel beskrives de trin, der kræves for at installere opdateringer på din StorSimple virtuelle matrix via lokale internettet Brugergrænsefladen og via portalen Azure klassisk. Du skal anvende softwareopdateringer eller hotfixes til at holde din StorSimple virtuelle matrix opdateret. 

Husk på, hvordan du installerer en opdatering eller programrettelse genstarter din enhed. Da der StorSimple virtuelle matrixen er en enkelt node enhed, en hvilken som helst I/O igangværende afbrydes, og enheden oplever nedetid. 

Før du anvender en opdatering, vi anbefaler, at du enheder eller aktier offline på værtsplaceringen for første og klik derefter på enheden. Derved minimeres enhver mulighed for beskadigelse af data.

> [AZURE.IMPORTANT] Hvis du kører opdatering 0,1 eller GA softwareversioner, skal du bruge metoden programrettelse via lokale internettet brugergrænseflade for at installere opdatering 0,3. Hvis du kører opdatering 0,2, anbefaler vi, at du installerer opdateringer via Azure klassisk-portalen.

## <a name="use-the-local-web-ui"></a>Brug den lokale web brugergrænseflade 
 
Der er to trin, når du bruger lokale internettet brugergrænseflade:

- Hent opdateringen eller programrettelsen
- Installere opdateringen eller denne programrettelse

### <a name="download-the-update-or-the-hotfix"></a>Hent opdateringen eller denne programrettelse

Udfør følgende trin for at hente softwareopdateringen fra kataloget til Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hente opdateringen eller denne programrettelse

1. Start Internet Explorer, og gå til [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Hvis dette er første gang, du bruger Microsoft Update-kataloget på denne computer, kan du klikke på **Installer** , når du bliver bedt om at installere tilføjelsesprogrammet Microsoft Update-kataloget.
  
3. Angiv Knowledge Base (KB) antallet af denne programrettelse, du vil hente i søgeboksen i Microsoft Update-kataloget. Angiv **3182061** for opdatering 0,3, og klik derefter på **Søg**.

    Listen programrettelse vises eksempelvis **StorSimple virtuelle matrix opdatering 0,3**.

    ![Søg katalog](./media/storsimple-ova-install-update-01/download1.png)

4. Klik på **Tilføj**. Opdater føjes til kurven.

5. Klik på **Vis kurv**.

6. Klik på **Hent**. Angive eller **Gå** til en lokal placering, hvor du vil placere de hentede vises. Opdateringerne hentes til den angivne placering og placeret i en undermappe med det samme navn som opdateringen. Mappen kan også kopieret til et netværksshare, som kan nås fra enheden.

7. Åbn mappen kopierede, skal du se en fil, Microsoft Update enkeltstående Package `WindowsTH-KB3011067-x64`. Denne fil bruges til at installere opdatering eller programrettelse.


### <a name="install-the-update-or-the-hotfix"></a>Installere opdateringen eller denne programrettelse

Kontroller, at du har opdateringen eller denne programrettelse hentet enten lokalt på din vært eller tilgængelige via et netværksshare, før opdatering eller programrettelse installationen. 

Brug denne metode til at installere opdateringer på en enhed kører GA eller opdatere 0.1 softwareversioner. Denne procedure fører mindre end 2 minutter at gennemføre. Udfør følgende trin for at installere opdateringen eller programrettelse.


#### <a name="to-install-the-update-or-the-hotfix"></a>Installere opdateringen eller programrettelsen

1. Gå til **vedligeholdelse**i den lokale internettet brugergrænseflade, > **Softwareopdatering**.

    ![opdatere enhed](./media/storsimple-ova-install-update-01/update1m.png)

2. Angiv navnet på filen med opdateringen eller denne programrettelse i **opdatere filsti**. Du kan også gå til installationsfilen opdatering eller programrettelse, hvis placeret på et netværksshare. Klik på **Anvend**.

    ![opdatere enhed](./media/storsimple-ova-install-update-01/update2m.png)

3.  Der vises en advarsel. Angivne dette er en enkelt node-enhed, når opdateringen er installeret, enheden genstartes, og der er nedetid. Klik på ikonet Kontrollér.

    ![opdatere enhed](./media/storsimple-ova-install-update-01/update3m.png)

4. Opdater starter. Når enheden er opdateret, genstarter den. Lokale Brugergrænsefladen er ikke tilgængelig i denne varighed.

    ![opdatere enhed](./media/storsimple-ova-install-update-01/update5m.png)

5. Når genstart er fuldført, kommer du til siden **Log på** . Gå til **vedligeholdelse**for at bekræfte, at programmet til enheden er opdateret på det lokale websted Brugergrænsefladen > **Softwareopdatering**. Den viste softwareversion skal være **10.0.0.0.0.10288.0** til opdatering 0,3.

    > [AZURE.NOTE] Vi rapportere softwareversioner i en lidt anden måde i det lokale web Brugergrænsefladen og Azure klassisk portalen. For eksempel lokale internettet Brugergrænsefladen rapporterer **10.0.0.0.0.10288** og Azure klassisk portalen rapporterer **10.0.10288.0** for den samme version. 

    ![opdatere enhed](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Bruge Azure klassisk portal

Hvis kører opdatering 0,2, anbefaler vi, at du installerer opdateringer via portalen Azure klassisk. Portalen fremgangsmåde kræver, at brugeren at scanne, hente og derefter installere opdateringerne. Denne procedure fører omkring 7 minutter for at gennemføre. Udfør følgende trin for at installere opdateringen eller programrettelse.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Når installationen er fuldført (som angivet ved jobstatus ved 100%), skal du gå til **enheder > vedligeholdelse > softwareopdateringer**. Den viste softwareversion skal være 10.0.10288.0.

![opdatere enhed](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Næste trin

Lær mere om at [administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
