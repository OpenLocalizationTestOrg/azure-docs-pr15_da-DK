<properties 
   pageTitle="Administrere kontoen StorSimple lagerplads | Microsoft Azure"
   description="Beskriver, hvordan du kan bruge siden StorSimple Manager Konfigurer til at tilføje, redigere, slette eller rotere tasterne sikkerhed for en lagerplads-konto, der er knyttet til den virtuelle StorSimple-matrix."
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
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Bruge tjenesten StorSimple Manager til at administrere lagerplads konti til StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt

Siden **Konfigurer** viser de globale service parametre, der kan oprettes i tjenesten StorSimple Manager. Disse parametre kan anvendes på alle de enheder, der er knyttet til tjenesten og indeholder:

- Lagerplads konti 
- Microsoft Access control-poster 

Dette selvstudium beskrives det, hvordan du kan bruge siden **Konfigurer** til at tilføje, redigere eller slette lagerplads konti til din StorSimple virtuelle matrix. Oplysningerne i dette selvstudium gælder kun for StorSimple virtuelle matrixen kører marts 2016 GA version –.

 ![Konfigurere side](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Lagerplads konti indeholder de legitimationsoplysninger, som enheden, der bruges til at få adgang til kontoen lagring af din tjenesteudbyder til skyen. Dette er legitimationsoplysninger som kontonavnet og det primære hurtigtast for Microsoft Azure-lager-konti. 

På siden **Konfigurer** vises alle lagerplads konti, der er oprettet for fakturering abonnementet i et tabelformat, der indeholder følgende oplysninger:

- **Navn** – det entydige navn, der er tildelt til kontoen, når det blev oprettet.
- **SSL aktiveret** – om feltet SSL er aktiveret og enhed i skyen kommunikation er via den sikre kanal.

De mest almindelige opgaver, der er relateret til lagerplads konti, der kan udføres på siden **Konfigurer** er:

- Tilføje en konto til lager 
- Redigere en lagerplads konto 
- Slette en lagerplads konto 


## <a name="types-of-storage-accounts"></a>Kontotyper lagerplads

Der findes tre typer lagerplads konti, der kan bruges med enheden StorSimple.

- **Automatisk oprettede lagerplads konti** – som navnet antyder, denne type lagerplads konto genereres automatisk, når tjenesten oprettes. Hvis du vil vide mere om, hvordan denne lagerplads konto er oprettet, skal du se [oprette en ny tjeneste](storsimple-ova-manage-service.md#create-a-service). 
- **Lagerplads konti i tjenesten abonnement** – dette er de Azure-lager-konti, der er knyttet til den samme abonnement som tjenesten. Få mere for at vide om, hvordan disse lagerplads konti er oprettet, kan du se [Om Azure lagerplads konti](../storage/storage-create-storage-account.md). 
- **Lagerplads konti uden for tjenesten abonnementet** – dette er de Azure-lager-konti, der ikke er knyttet til din tjeneste og sandsynligt fandtes før tjenesten blev oprettet.

Hver StorSimple virtuelle matrix opretter en objektbeholder (med et præfiks hcs) i den tilknyttede lagerplads konto. Denne beholder indeholder alle skyen dataene til din enhed. Slet ikke denne objektbeholder ved at åbne den i Azure-lager-tjenesten som denne handling kan medføre tab af data.

## <a name="add-a-storage-account"></a>Tilføje en konto til lager

Du kan tilføje en lagerplads konto til din StorSimple Manager tjenestekonfiguration ved at give et entydigt navn og legitimationsoplysninger, der er knyttet til kontoen lagerplads. Du har også mulighed for at aktivere tilstanden secure sockets layer (SSL) til at oprette en sikker kanal af netværkskommunikation mellem enheden og skyen.

Du kan oprette flere konti for en given skyen udbyder. Mens kontoen lagerplads gemmes, forsøger tjenesten at kommunikere med udbyderen skyen. Legitimationsoplysningerne, og den access-materiale, du har angivet godkendes på nuværende tidspunkt. En lagerplads konto oprettes kun, hvis godkendelsen lykkes. Hvis godkendelsen mislykkes, derefter vises en relevant fejlmeddelelse.

Ressourcestyring lagerplads konti, der er oprettet i Azure portal understøttes også med StorSimple. Ressourcestyring lagerplads konti vises ikke i rullelisten til valg af opbevaring konti, der er oprettet i portalen Azure klassisk vises. Ressourcestyring lagerplads konti skal tilføjes ved hjælp af fremgangsmåden til at tilføje en lagerplads konto som beskrevet nedenfor.

Fremgangsmåden for at tilføje en Azure klassisk lagerplads konto er beskrevet nedenfor.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Redigere en lagerplads konto

Du kan redigere en lagerplads-konto, der anvendes af din enhed. Hvis du redigerer en lagerplads-konto, som er i brug, er de felter, der er tilgængelige for redigering hurtigtast og SSL tilstanden for kontoen lagerplads. Du kan angive den nye lagerplads access nøgle eller ændre indstillingen **Aktiver SSL tilstand** og gemme de opdaterede indstillinger.

#### <a name="to-edit-a-storage-account"></a>Redigere en lagerplads konto

1. På landingsside service, Vælg din tjeneste, dobbeltklik på navnet på tjenesten og derefter klikke på **Konfigurer**.

2. Klik på **Tilføj/Rediger lagerplads konti**.

3. I dialogboksen **Add/Edit lagerplads konti** :

  1. Vælg en eksisterende konto, du vil ændre, i rullelisten over **Lagerplads konti**. 
  2. Hvis det er nødvendigt, kan du ændre indstillingen **Aktiver SSL-tilstand** .
  3. Du kan vælge at genoprette din lagerplads konto access-taster. Du kan finde flere oplysninger, se [genoprette tasterne lagerplads konto](storage-create-storage-account.md#manage-your-storage-access-keys). Angiv den nye kontonøgle til lagerplads. Dette er den primære hurtigtast for en Azure-lager-konto. 
  4. Klik på ikonet Kontrollér ![markere ikonet](./media/storsimple-ova-manage-storage-accounts/checkicon.png) til at gemme indstillingerne. Indstillingerne for opdateres på siden **Konfigurer** . 
  5. Klik på **Gem** for at gemme de opdaterede indstillinger nederst på siden. 

     ![Redigere en lagerplads konto](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Slette en lagerplads konto

> [AZURE.IMPORTANT] Du kan slette en lagerplads konto kun, hvis det ikke er i brug. Hvis en lagerplads konto er i brug, skal du have besked.

#### <a name="to-delete-a-storage-account"></a>Slette en lagerplads konto

1. På landingssiden StorSimple Manager service, Vælg din tjeneste, dobbeltklik på navnet på tjenesten og derefter klikke på **Konfigurer**.

2. Hold markøren over den konto, du vil slette, på listen tabelformat af lagerplads konti.

3. En sletteikonet (**x**) vises i kolonnen yderst til højre for den pågældende lagerplads konto. Klik på **x** for at slette legitimationsoplysningerne.

4. Når du bliver bedt om en bekræftelse, skal du klikke på **Ja** for at fortsætte med sletningen. Listen tabelformat opdateres afspejler ændringerne.

5. Klik på **Gem** for at gemme de opdaterede indstillinger nederst på siden.


## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [administrere dine StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
