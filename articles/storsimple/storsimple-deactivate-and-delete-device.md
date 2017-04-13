<properties 
   pageTitle="Deaktivere og slette en StorSimple enhed | Microsoft Azure"
   description="Beskriver, hvordan du fjerner StorSimple enhed fra tjeneste ved først deaktivere den og derefter slette den."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Deaktivere og slette en StorSimple enhed

## <a name="overview"></a>Oversigt

Du kan ønske at tage en enhed med StorSimple af service (f.eks, hvis du erstatter eller opgradering af din enhed, eller hvis du ikke længere bruger StorSimple). Hvis det er tilfældet, skal du deaktivere enheden, før du kan slette den. Deaktivere afbryder forbindelsen mellem enheden og tjenesten tilsvarende StorSimple Manager. Dette selvstudium beskrives det, hvordan du fjerner en enhed med StorSimple fra tjenesten ved at deaktivere den og derefter slette den. 

Når du har deaktiveret en enhed, vil de data, der blev gemt lokalt på enheden ikke længere være tilgængelige. Kun de data, der er knyttet til den enhed, der blev gemt i skyen, kan gendannes.  

>[AZURE.WARNING] Deaktivering af er en PERMANENT handling og kan ikke fortrydes. En deaktiveret enhed kan ikke registreres med tjenesten StorSimple Manager, medmindre den nulstilles først af en factory. 
>
>Fabriksindstillingerne Nulstil proces sletter alle data, der blev gemt lokalt på din enhed. Det er derfor vigtigt, at du foretager et skyen øjebliksbillede af dine data, inden du deaktiverer en enhed. Dette kan du gendanne alle data på et senere tidspunkt.

Dette selvstudiet beskrives, hvordan du:

- Deaktivere en enhed og slette dataene
- Deaktivere en enhed og bevare dataene

Det også forklarer, hvordan deaktivering af dokumentbiblioteksserver og sletningen fungerer på en virtuel StorSimple-enhed.

>[AZURE.NOTE] Før du har deaktiveret en StorSimple fysisk eller virtuel enhed, skal du sørge for at stoppe eller slette klienter og værter, der er afhænger af enheden.

## <a name="deactivate-and-delete-data"></a>Deaktivere og slette data

Hvis du er interesseret i at slette enheden helt og ikke vil bevare dataene på enheden, derefter skal du udføre følgende trin.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Deaktivere enheden og slette dataene  

1. Før du deaktivere en enhed, skal du slette alle lydstyrken beholdere (og enhederne) der er knyttet til enheden. Du kan slette lydstyrken beholdere kun, når du har slettet de tilknyttede sikkerhedskopier.

2. Deaktivere enheden på følgende måde:

    1. Vælg den enhed, du vil deaktivere, og klik på **Deaktiver**nederst på siden, på siden StorSimple Manager service **enheder** .

    2. Der vises en bekræftelsesmeddelelse. Klik på **Ja** for at fortsætte. Deaktiver processen starter og tage et par minutter at gennemføre.

3. Når deaktiveringen, kan du slette enheden helt. Slette en enhed, fjerne den fra listen over enheder, der har forbindelse til tjenesten. Tjenesten kan derefter administrere slettede enheden ikke længere. Brug følgende trin til at slette enheden:

    1. Vælg en deaktiveret enhed, du vil slette, på siden StorSimple Manager service **enheder** .

    2. Klik på **Slet**nederst på siden.

    3. Du bliver bedt om at bekræfte. Klik på **Ja** for at fortsætte.

    Det kan tage et par minutter til enheden for at blive slettet.

## <a name="deactivate-and-retain-data"></a>Deaktivere og beholde data

Hvis du er interesseret i at slette enheden, men vil bevare dataene, derefter udføre følgende trin.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Deaktivere en enhed og bevare dataene 

1. Deaktivere enheden. Alle lydstyrken beholdere og snapshot af enheden, forbliver.

    1. Vælg den enhed, du vil deaktivere, og klik på **Deaktiver**nederst på siden, på siden StorSimple Manager service **enheder** .

    2. Der vises en bekræftelsesmeddelelse. Klik på **Ja** for at fortsætte. Deaktiver processen starter og tage et par minutter at gennemføre.

2. Du kan nu mislykkes over lydstyrken beholdere og de tilknyttede snapshots. For procedurer, skal du gå til [Failover og genoprettelse efter nedbrud for enheden StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Når du deaktivering af og failover, kan du slette enheden helt. Slette en enhed, fjerne den fra listen over enheder, der har forbindelse til tjenesten. Tjenesten kan derefter administrere slettede enheden ikke længere. Benytte følgende fremgangsmåde for at slette enheden:
 
    1. Vælg en deaktiveret enhed, du vil slette, på siden StorSimple Manager service **enheder** .

    2. Klik på **Slet**nederst på siden.

    3. Du bliver bedt om at bekræfte. Klik på **Ja** for at fortsætte.

    Det kan tage et par minutter til enheden for at blive slettet.

## <a name="deactivate-and-delete-a-virtual-device"></a>Deaktivere og slette en virtuel enhed

For en StorSimple virtuel enhed deallocates deaktivering af den virtuelle maskine. Du kan derefter slette den virtuelle maskine og de ressourcer, der er oprettet, da den blev klargjort. Når den virtuelle enhed er deaktiveret, kan ikke gendannes til den forrige tilstand. 

Deaktivering af resulterer i følgende handlinger:

- Den virtuelle enhed StorSimple fjernes.

- OSDisk og datadisce, der er oprettet for den virtuelle enhed StorSimple fjernes.

- Tjenesten hostet og virtuelt netværk, der blev oprettet under klargøringen bevares. Hvis du ikke bruger disse objekter, skal du slette dem manuelt.

- Skyen snapshots, der er oprettet af den virtuelle enhed StorSimple bevares.

## <a name="next-steps"></a>Næste trin
- For at gendanne deaktiveret enheden til fabriksindstillingerne, skal du gå til [på enheden til standardindstillingerne](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- For teknisk hjælp, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md).

- Du kan få mere at vide om at bruge tjenesten StorSimple Manager, kan du gå til [bruge tjenesten StorSimple Manager for at administrere din StorSimple-enhed](storsimple-manager-service-administration.md). 
