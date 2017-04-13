<properties 
   pageTitle="Få vist og administrere StorSimple virtuelle matrix job | Microsoft Azure"
   description="I denne artikel beskrives siden StorSimple Manager job og hvordan du kan bruge det til at registrere seneste og aktuelle job til den virtuelle StorSimple-matrix."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Bruge tjenesten StorSimple Manager til at få vist job til den virtuelle StorSimple-matrix

## <a name="overview"></a>Oversigt

Gå til siden **sager** indeholder en enkelt central portal til visning og administration af sager, der er startet på virtuelle matrixer (også kaldet lokale virtuelle enheder), der er tilsluttet din StorSimple Manager-tjeneste. Du kan få vist, der kører, færdiggjort og mislykkede job for flere virtuelle enheder. Resultaterne vises i et tabelformat. 

![Gå til siden sager](./media/storsimple-ova-manage-jobs/ovajobs1.png)

Du kan hurtigt finde de job, du er interesseret i, ved at filtrere på felter, f.eks.:

- **Status** – du kan søge efter alle, der kører, fuldført eller mislykkede job.
- **Fra og til** -job kan filtreres på baggrund af dato og klokkeslæt området.
- **Type** – typen kan være all, sikkerhedskopiering og gendannelse, failover, hente opdateringer eller installere opdateringer.
- **Enheder** – job er startet på en bestemt enhed har forbindelse til din tjeneste. De filtrerede job er derefter tabelform på grundlag af følgende egenskaber:

    - **Type** – typen kan være all, sikkerhedskopiering og gendannelse, failover, hente opdateringer eller installere opdateringer.

    - **Status** – job kan være alle, kører, fuldført, eller mislykkedes.

    - **Enhed** – job kan være forbundet med en volumenlicens, del eller enhed. 

    - **Enhed** – navnet på enheden på jobbet er blevet startet.

    - **Introduktion på** – den tid, når jobbet er blevet startet.

    - **Status** – procentsatsen af færdiggørelse af et job, der kører. Dette skal altid være 100% for en færdige sag.

Listen over job opdateres, hver 30 sekunder.

## <a name="view-job-details"></a>Se detaljer om job

Udfør følgende trin for at få vist oplysninger om et job.

#### <a name="to-view-job-details"></a>Til at få vist oplysninger om job

1. Få vist det eller de job, du er interesseret i, ved at køre en forespørgsel med relevante filtre på siden **job** . Du kan søge efter færdige eller kører job.

2. Vælg et job fra tabelformat listen over sager.

3. Nederst på siden skal du klikke på **Detaljer**.

4. Du kan få vist status, detaljer og tid statistik i dialogboksen **Detaljer** . Følgende illustration viser et eksempel i dialogboksen **Oplysninger om sikkerhedskopi-Job** .
 
    ![Jobbet oplysningssiden](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Jobbet fejl, når den virtuelle maskine er afbrudt midlertidigt i hypervisor

Når et job er i gang på din StorSimple virtuelle matrix og på enheden (virtuelt klargjort i hypervisor) er standset til større end 15 minutter, jobbet mislykkes. Dette er på grund af din StorSimple virtuelle matrix tid ved at blive synkroniseret med Microsoft Azure-tid. Et eksempel på en Gendan job fejl vises på følgende skærmbillede.

![Gendanne job fejl](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Disse fejl gælder for sikkerhedskopiering, gendannelse, opdatering og failover job. Hvis din virtuelle maskine er klargjort i Hyper-V, synkroniseres maskinen til sidst tid med din hypervisor. Når det sker, kan du genstarte tingene. 

## <a name="next-steps"></a>Næste trin

[Lær at bruge webdelen lokale Brugergrænsefladen til at administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
