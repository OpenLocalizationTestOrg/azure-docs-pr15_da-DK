<properties
   pageTitle="Bruge dashboardet StorSimple Manager enhed | Microsoft Azure"
   description="I denne artikel beskrives dashboardet StorSimple Manager service enhed, og hvordan du bruger den til at få vist lagringsmål og forbundne initiatorer og finde de serienummer og IQN."
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

# <a name="use-the-storsimple-manager-device-dashboard"></a>Bruge dashboardet StorSimple Manager enhed

## <a name="overview"></a>Oversigt

StorSimple Manager enhed dashboardet giver dig et overblik over oplysninger for en bestemt StorSimple-enhed, i modsætning til dashboardet service, som giver dig oplysninger om alle de enheder, der er inkluderet i din Microsoft Azure StorSimple løsning.

![Enhed dashboardside](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Dashboardet indeholder følgende oplysninger:

- **Diagramområde** – du kan se den relevante lagringsmål i diagramområdet øverst på dashboardet. I dette diagram kan du få vist måleredskaber for den samlede primære lager (mængden data, der er skrevet af værter til din enhed) og den samlede skylagring consumed ved enheden over en periode.

     I denne kontekst *primære lager* refererer til den samlede mængde af data, der er skrevet af værten og kan opdeles efter lydstyrken type: *primære lagdelte lagerplads* indeholder både lokalt gemte data og data lagdelt i skyen *primære lokalt fastgjort lagerplads* indeholder kun de data, der er gemt lokalt. *Cloud storage*, på den anden side er en måling af den samlede mængde af data, der er gemt i skyen. Dette omfatter lagdelte data og sikkerhedskopier. Bemærk, at data, der er gemt i skyen er deduplicated og komprimeret, mens primære lagerplads angiver mængden lagerplads, der er brugt før data er deduplicated og komprimeres. (Du kan sammenligne disse to tal for at få en ide om, komprimering rente). For begge primær og skylagring, de viste beløb baseres på registrering hyppigheden du konfigurerer. Eksempelvis hvis du vælger en frekvens for en uge, blive derefter diagrammet vist data for hver dag i den forrige uge.

     Du kan konfigurere diagrammet på følgende måde:

     - Vælg indstillingen **SKYEN LAGERFORBRUG** for at få vist mængden skylagring consumed over tid. Vælg de **Primære LAGDELT LAGERFORBRUG** og **Primære LOKALT FASTGJORT LAGERFORBRUG** indstillinger for at få vist den samlede lagerplads, der er skrevet af værten. I illustrationen, der er valgt begge muligheder. diagrammet viser derfor lagerplads beløb for både skyen og primære lager. Bemærk, at alle primære lager, der bruges før du installerer opdatering 2 er repræsenteret af den **Primære LAGDELT LAGERFORBRUG** linje.
     - Brug i rullemenuen i øverste højre hjørne af diagrammet til at angive en 1 uge, 1 måned, 3 måneder eller 1 års tidsperiode. Bemærk, at søjlediagrammet på øverste niveau er opdaterede kun én gang om dagen, og derfor vil afspejle den forrige dag totaler.

     Se [Brug af tjenesten StorSimple Manager til at overvåge enheden StorSimple](storsimple-monitor-device.md)kan finde flere oplysninger.

- **Brugen oversigt** – i området **brugen oversigt** , kan du se mængden primære lager, der bruges, mængden klargjort lager og den maksimale lagerkapacitet til din enhed. Ved at sammenligne tallene brugen til den maksimale mængde lagerplads, der er tilgængelig, kan du se et hurtigt overblik, hvis du har brug at få yderligere lagerplads. Bemærk, at denne oversigt opdateres hver 15 minutter, og på grund af forskellen i opdateringsfrekvens, viser muligvis andet tal end dem, der er vist i diagramområdet ovenfor, der opdateres dagligt. Se [Brug af tjenesten StorSimple Manager til at overvåge enheden StorSimple](storsimple-monitor-device.md)kan finde flere oplysninger.


- **Beskeder** – området **beskeder** indeholder en oversigt over de vigtige beskeder til din enhed. Beskeder er grupperet efter klassifikation, og der findes en optælling af antallet af beskeder på hvert Alvorlighedsniveau. Klik på den alvorlighed åbnet en relateret visning af fanen beskeder til at vise dig kun beskeder på det pågældende Alvorlighedsniveau for denne enhed.

- **Job** – området **sager** viser resultatet af seneste job aktivitet. Dette kan garantere, at systemet fungerer som forventet, eller det kan lade dig, at du skal udføre korrigerende handling. Hvis du vil se flere oplysninger om senest fuldførte job, skal du klikke på **sager lykkedes i de seneste 24 timer**.

- Området **Oversigt over** i højre side af dashboardet indeholder nyttige oplysninger som enhedsmodel, serienummer, status, beskrivelse og antal enheder.

Du kan også konfigurere failover og få vist forbundne initiatorer fra dashboardet for enhed.

De almindelige opgaver, der kan udføres på denne side er:

- Få vist forbundne initiatorer

- Finde serienummeret enhed

- Finde enhed destinationen IQN

## <a name="view-connected-initiators"></a>Få vist forbundne initiatorer

Du kan få vist de iSCSI-initiatorer, der er forbundet til din enhed ved at klikke på linket **Vis forbindelse initiatorer** angivet i området **Oversigt over** af din enhed dashboard. Denne side indeholder en tabelformat oversigt over de initiatorer, der har oprettet forbindelse til din enhed. For hver afsender, kan du se:

- ISCSI kvalificerede navn (IQN) af forbundne afsenderen.

- Navnet på den access-kontrolelement post (ACR), der gør det muligt for denne forbundne afsenderen.

- Forbundne afsenderen IP-adresse.

- De netværksgrænseflader, der er tilsluttet afsenderen på enheden lagerplads. Disse kan variere fra DATA 0 til DATA 5.

- Alle de enheder, der forbundne afsenderen har tilladelse til at få adgang til efter den aktuelle ACR konfiguration.

Hvis du kan se uventede initiatorer på denne liste eller ikke kan se de forventede resultater, kan du gennemse konfigurationen af ACR. Maksimalt 512 initiatorer kan oprette forbindelse til din enhed.

## <a name="find-the-device-serial-number"></a>Finde serienummeret enhed

Når du konfigurerer Microsoft Multipath i/o-(MPIO) på enheden, skal du muligvis serienummeret enhed. Udfør følgende trin for at finde serienummeret enhed.

#### <a name="to-find-the-device-serial-number"></a>Finde serienummeret enhed

1. Gå til **enheder** > **Dashboard**.

2. Find området **Oversigt over** i højre rude af dashboardet.

3. Rul ned, og Find serienummeret.

## <a name="find-the-device-target-iqn"></a>Finde enhed destinationen IQN

Når du konfigurerer udfordring CHAP Handshake Authentication Protocol () på enheden StorSimple, skal du muligvis enhed destinationen IQN. Udfør følgende trin for at finde enhed destinationen IQN.

### <a name="to-find-the-device-target-iqn"></a>Finde enhed destinationen IQN

1. Gå til **enheder** > **Dashboard**.

1. Find området **Oversigt over** i højre rude af dashboardet.

1. Rul ned, og Find destinationen IQN.

## <a name="next-steps"></a>Næste trin

- Lær mere om [StorSimple Manager servicedashboard](storsimple-service-dashboard.md).
- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
