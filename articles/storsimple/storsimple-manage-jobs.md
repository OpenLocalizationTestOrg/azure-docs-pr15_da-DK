<properties 
   pageTitle="Få vist og administrere StorSimple job | Microsoft Azure"
   description="I denne artikel beskrives siden StorSimple Manager job og hvordan du kan bruge det til at registrere seneste, aktuelle og planlagte sikkerhedskopieringsjob."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Bruge tjenesten StorSimple Manager til at få vist og administrere StorSimple job

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Oversigt

Gå til siden **sager** indeholder en enkelt central portal til visning og administrere jobs, der er blevet startet på enheder tilsluttet din StorSimple Manager-tjeneste. Du kan få vist planlagt, der kører, færdiggjort og mislykkedes job for flere enheder. Resultaterne vises i et tabelformat. 

![Gå til siden sager](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Du kan hurtigt finde de sager, du er interesseret i, ved at filtrere på felter, f.eks.:

- **Status** – job kan være åbent, planlagt, mislykkedes, fuldført, annullering eller annulleres.

- **Type** – job kan oprettes som et resultat af en planlagt eller en (**Tage sikkerhedskopiering**) efter behov sikkerhedskopi, klone, Gendan en enhed eller en opdateringshandling.

- **Enheder** – job er startet på en bestemt enhed, der er forbundet med din tjeneste.

- **Fra og til** -job kan filtreres på baggrund af dato og klokkeslæt området.

De filtrerede job er derefter tabelform på grundlag af følgende egenskaber:

- **Type** – sikkerhedskopi, Klon, Gendan, failover eller opdatering.

- **Status** – som kører, planlagt, mislykkedes, fuldført, annullering eller annulleres.

- **Enhed** – job kan være knyttet til et drev, en politik for sikkerhedskopiering eller en enhed. Et Klon job er knyttet til et drev, mens den planlagte sikkerhedskopiering er knyttet til en politik for sikkerhedskopiering. Der oprettes en enhed job som et resultat af en nedbrud (DR) eller en gendannelse.

- **Enhed** – navnet på enheden på jobbet er blevet startet.

- **I gang på** – det tidspunkt, hvor jobbet blev startet.

- **Status** – procentsatsen af færdiggørelse af et job, der kører. Dette skal altid være 100% for en færdige sag.

Listen over job opdateres, hver 30 sekunder.

Du kan udføre følgende arbejdsrelaterede handlinger på denne side:

- Se detaljer om job

- Annullere et job

## <a name="view-job-details"></a>Se detaljer om job

Udfør følgende trin for at få vist oplysninger om et job.

#### <a name="to-view-job-details"></a>Til at få vist oplysninger om job

1. Få vist det eller de job, du er interesseret i, ved at køre en forespørgsel med relevante filtre på siden **job** . Du kan søge for fuldført, kører, eller annulleret job.

2. Vælg et job.

3. Nederst på siden skal du klikke på **Detaljer**.

4. I dialogboksen **Oplysninger om sikkerhedskopi Job** kan du få vist status, detaljer, tid statistik og data statistik.

## <a name="cancel-a-job"></a>Annullere et job

Udfør følgende trin for at annullere en igangværende job.

### <a name="to-cancel-a-job"></a>Annullere et job

1. Få vist det, der kørende eller de job, du vil annullere ved at køre en forespørgsel med relevante filtre på siden **job** .

1. Vælg job.

1. Klik på **Annuller**nederst på siden.

1. Klik på **Ja**, når du bliver bedt om en bekræftelse.

Denne jobbet er nu annulleret.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [administrerer din StorSimple sikkerhedskopiering politikker](storsimple-manage-backup-policies.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
