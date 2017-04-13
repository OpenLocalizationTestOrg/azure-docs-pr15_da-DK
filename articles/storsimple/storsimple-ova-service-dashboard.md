<properties 
   pageTitle="StorSimple Manager servicedashboard - virtuelle matrix | Microsoft Azure"
   description="I denne artikel beskrives dashboardet StorSimple Manager service og forklarer, hvordan du bruger den til at overvåge tilstanden for din StorSimple virtuelle matrix."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Bruge dashboardet StorSimple Manager service til StorSimple virtuelle systemet

## <a name="overview"></a>Oversigt

Siden StorSimple Manager dashboard giver en oversigt over visning af de StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder eller virtuelle enheder), der er forbundet til tjenesten StorSimple Manager fremhæve dem, der kræver en systemadministrator opmærksomhed. Dette selvstudium introducerer dashboard-siden, forklarer dashboardindhold og funktionen og beskriver de opgaver, som du kan udføre fra denne side.

![Tjenesten dashboard](./media/storsimple-ova-service-dashboard/dashboard1.png)

Dashboardet StorSimple Manager service viser følgende oplysninger:

- I **diagramområdet øverst på siden,** kan du se de relevante før mailadvarslen sendes til din virtuelle enheder. Du kan få vist den primære lager, der bruges på tværs af alle virtuelle enheder, samt den skylagring consumed ved virtuelle enheder over en periode. Brug kontrolelementerne i øverste højre hjørne af diagrammet, til at angive relativ eller absolut brugen og for at se en 1 uge, 1 måned, 3 måneder eller 1 års tidsskala. Bruge kontrolelementet Opdater ![opdatering](./media/storsimple-ova-service-dashboard/refresh-control.png) til at opdatere diagrammet.

- **Oversigt over brugen** viser den primære lagerplads, der er klargjort og consumed ved alle virtuelle enheder i forhold til den samlede lagerplads, der er tilgængelige på tværs af alle virtuelle enheder. **Provisioned** refererer til mængden lagerplads, der er forberedt og allokeret til brug, **bruges** refererer til brugen af aktier eller enheder, som vises ved de initiatorer, der er forbundet til de virtuelle enheder og **Maks. kapacitet** viser den maksimale kapacitet for alle virtuelle enheder.

- Området **beskeder** indeholder et øjebliksbillede af alle de aktive beskeder på tværs af alle virtuelle enheder, der er grupperet efter alvorlighed. Hvis du klikker på en prioritering åbner siden **beskeder** , fastsat for at få vist disse beskeder. Du kan klikke på en enkelt besked for at få vist yderligere oplysninger om den advarsel, herunder eventuelle anbefalede handlinger på siden **beskeder** . Du kan også fjerne den vigtige besked, hvis problemet er løst.

- Området **sager** giver et øjebliksbillede af seneste job på tværs af alle virtuelle enheder, der er tilsluttet din tjeneste. Der er links, som du kan bruge til at se på job, der aktuelt er i gang, og hvilke vellykkede eller mislykkede i de seneste 24 timer. 

- Området **Oversigt over** til højre på siden indeholder nyttige oplysninger som Tjenestestatus samlede antal virtuelle enheder, der har forbindelse til tjenesten, placering af tjenesten og oplysninger om det abonnement, der er knyttet til tjenesten. Der er også et link til loggen handlinger. Klik på linket for at få vist en liste over alle færdige StorSimple Manager servicehandlinger. 

Du kan bruge siden StorSimple Manager dashboard til at starte følgende opgaver:

- Få tasten service registrering.
- Få vist loggene handling.

## <a name="get-the-service-registration-key"></a>Få tasten service registrering

Tasten service registrering bruges til at registrere en StorSimple virtuel enhed med StorSimple Manager-tjenesten, så enheden, der vises i Azure klassisk portal til yderligere management handlinger. Tasten er oprettet på den første virtuelle enhed og deles med de resterende virtuelle enheder. 

Klik på **Registreringsnøgle** (nederst på siden) åbnet dialogboksen **Tjenestens registreringsnøgle** , hvor du kan kopiere den aktuelle service registreringsnøgle til Udklipsholder eller genoprette nøglen service registrering.

![registreringsnøgle](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Genoprette nøglen påvirker ikke tidligere registrerede virtuelle enheder: den påvirker kun de virtuelle enheder, der er registreret med tjenesten, når nøglen gendannes.

Du kan finde flere oplysninger om at få tasten service registrering, gå til [Hent tasten service registrering](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Få vist loggene handlinger

Du kan få vist loggene handlingen ved at klikke på handlingen logfiler link tilgængelig i ruden **Oversigt over** af dashboardet. Dette kommer du til administrationssiden af tjenester, hvor du kan filtrere og se loggene bestemte til din StorSimple Manager-tjeneste.

![Logge af handlinger](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Næste trin

Lær, hvordan du [bruger den lokale web Brugergrænsefladen til at administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).