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

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Bruge tjenesten StorSimple Manager til at få vist og administrere StorSimple jobs (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Oversigt

Gå til siden **sager** indeholder en enkelt central portal til visning og administrere jobs, der er blevet startet på enheder tilsluttet din StorSimple Manager-tjeneste. Du kan få vist planlagt, der kører, færdige, annullerede og mislykkedes job for flere enheder. Resultaterne vises i et tabelformat. 

![Gå til siden sager](./media/storsimple-manage-jobs-u2/jobs.png)

Du kan hurtigt finde de sager, du er interesseret i, ved at filtrere på felter, f.eks.:

- **Status** – job kan være åbent, fuldført, annulleres, mislykkedes, annullering eller fuldført med fejl.
- **Fra og til** -job kan filtreres på baggrund af dato og klokkeslæt området.
- **Type** – typen kan være sikkerhedskopiering og sikkerhedskopi manuelt Gendan Klon, og enheden failover, oprette lokalt fastgjort lydstyrke, ændre lydstyrken, opdatere, understøtter pakke eller klargøring af virtuel enhed.

- **Enheder** – job er startet på en bestemt enhed, der er forbundet med din tjeneste.
De filtrerede job er derefter tabelform på grundlag af følgende egenskaber:

    - **Type** – sikkerhedskopiering og sikkerhedskopi manuelt Gendan Klon, og enheden failover, oprette lokalt fastgjorte lydstyrke, ændre lydstyrken, opdatere, understøtter pakke eller klargøring af virtuel enhed.

    - **Status** – som kører, fuldført, annulleres, mislykkedes, annullering eller fuldført med fejl.

    - **Enhed** – job kan være knyttet til et drev, en politik for sikkerhedskopiering eller en enhed. Et Klon job er for eksempel knyttet til et drev, mens den planlagte sikkerhedskopiering er knyttet til en politik for sikkerhedskopiering. Der oprettes en enhed job som et resultat af en nedbrud (DR) eller en gendannelse.

    - **Enhed** – navnet på enheden på jobbet er blevet startet.

    - **Introduktion på** – den tid, når jobbet er blevet startet.

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
 
    ![Jobbet oplysningssiden](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Annullere et job

Udfør følgende trin for at annullere en igangværende job.

>[AZURE.NOTE] Nogle job, som ændring af en enhed for at ændre lydstyrken eller udvide et drev, kan ikke annulleres.

### <a name="to-cancel-a-job"></a>Annullere et job

1. Få vist det, der kørende eller de job, du vil annullere ved at køre en forespørgsel med relevante filtre på siden **job** .

1. Vælg job.

1. Klik på **Annuller**nederst på siden.

1. Klik på **Ja**, når du bliver bedt om en bekræftelse.

Denne jobbet er nu annulleret.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [administrerer din StorSimple sikkerhedskopiering politikker](storsimple-manage-backup-policies.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
