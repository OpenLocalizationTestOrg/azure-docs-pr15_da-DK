<properties 
   pageTitle="Kontakt Microsoft Support | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter en supportanmodning og starte en session med support på enheden StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Kontakt Microsoft Support

Hvis du støder på problemer med din Microsoft Azure StorSimple løsning, kan du oprette en serviceanmodning for teknisk support. Du muligvis også starte en session med support på enheden StorSimple i en onlinesession med din support engineering. I denne artikel vejleder dig gennem:

- Sådan oprettes en supportanmodning.
- Sådan startes en session med support i Windows PowerShell-grænsefladen i enheden StorSimple.

Gennemse [StorSimple 8000 serie Support SLA og oplysninger](https://msdn.microsoft.com/library/mt433077.aspx) , inden du opretter en supportanmodning.

## <a name="create-a-support-request"></a>Oprette en anmodning om support

Udfør følgende trin for at oprette en anmodning om support:

#### <a name="to-create-a-support-request"></a>Oprette en anmodning om support

1. Klik på navnet på din konto på [Azure klassisk portal](https://manage.windowsazure.com/)i øverste højre hjørne, og klik derefter på **Kontakt Microsoft Support**.

    ![Kontakt Microsoft Support via ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Du omdirigeres til den nye Azure portal (portal.azure.com). Klik på feltet **Ny understøtter anmodning** .

    ![Kontakt Microsoft Support via nye portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    I højre side af skærmen vises ruden **Ny understøtter anmodning** . 

    ![Nye understøttelse af anmodning om rude](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Udfør følgende i dialogboksen **grundlæggende** :                                
    1. Vælg **teknisk** **problemtype** på rullelisten.
    2. Vælg et **abonnement** på rullelisten.
    3. Vælg **StorSimple**fra rullelisten **Service** . 
    4. Vælg en **plan for Support** på rullelisten. Du har brug for en betalt support for at aktivere Teknisk Support.

4. Klik på **Næste**. Dialogboksen **Problem** vises.

    ![Nye understøttelse af anmodning om rude](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Udfør følgende i dialogboksen **problemet** :

    1.  Vælg **en prioritering** på rullelisten.
    2.  Vælg en **problemtype** på rullelisten.
    3.  Vælg en **kategori** på rullelisten. 
    4.  Beskriv dit problem, i feltet **Detaljer** .
    5.  Angiv den dato, klokkeslæt og tidszone, der svarer til den nyeste forekomst af dit problem, i feltet **tidsramme** .
    6.  Under **Filoverførsel**, skal du klikke på mappeikonet for at gå til din supportpakke.
    7.  Markér afkrydsningsfeltet **del diagnostiske oplysninger** .

6. Klik på **Næste**. Dialogboksen **kontaktoplysninger** vises.

    ![Nye understøttelse af anmodning om rude](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Angiv dine kontaktoplysninger, og vælg en kontaktmetode (telefon eller mail). 

8. Marker afkrydsningsfeltet **Gem kontakter ændringer for anmodninger om support i fremtiden** .

9. Klik på **Opret**.

Når du har sendt anmodningen, vil engineering en Support kontakte dig, så tidligt som muligt at fortsætte med at din anmodning.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starte en session med support i Windows PowerShell til StorSimple

For at foretage fejlfinding af eventuelle problemer, som du kan opleve med StorSimple-enhed, skal du kommunikere med Microsoft Support-teamet. Microsoft Support muligvis bruge en session med support til at logge på din enhed. 

Udfør følgende trin for at starte en session med support:

#### <a name="to-start-a-support-session"></a>Starte en session med support

1. Få adgang til enheden, direkte ved hjælp af seriel konsollen eller via en telnet-session fra en fjerncomputer. For at gøre dette skal du følge trinnene i [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. I den session, der åbnes, skal du trykke på **Enter** for at få en kommandoprompt.

3. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol.

4. Skriv følgende adgangskode for kommandoprompten: 

    `Password1`

5. Når du bliver spurgt, skal du skrive følgende kommando:

    `Enable-HcsSupportAccess`

6. En krypteret streng, der præsenteres for dig. Kopiér denne streng i et tekstredigeringsprogram som Notesblok.

7. Gem denne streng, og sende den via en e-mail-meddelelse til Microsoft Support. 

> [AZURE.IMPORTANT] Du kan deaktivere understøttelse af adgang ved at køre `Disable-HcsSupportAccess`. StorSimple enheden også forsøger at deaktivere understøttelse af adgang 8 tid, når sessionen er startet. Det er den bedste fremgangsmåde at ændre dine StorSimple enhed legitimationsoplysninger, når du har startet en session med support.
