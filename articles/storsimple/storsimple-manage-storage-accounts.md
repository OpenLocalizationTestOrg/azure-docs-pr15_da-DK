<properties 
   pageTitle="Administrere kontoen StorSimple lagerplads | Microsoft Azure"
   description="Forklarer, hvordan du kan bruge siden StorSimple Manager Konfigurer til at tilføje, redigere, slette eller rotere tasterne sikkerhed for en lagerplads konto."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Bruge tjenesten StorSimple Manager til at administrere din lagerplads konto

## <a name="overview"></a>Oversigt

Siden **Konfigurer** viser alle de globale service-parametre, der kan oprettes i tjenesten StorSimple Manager. Disse parametre kan anvendes på alle de enheder, der er knyttet til tjenesten og indeholder:

- Lagerplads konti 
- Båndbredde skabeloner 
- Microsoft Access control-poster 

Dette selvstudium beskrives det, hvordan du kan bruge siden **Konfigurer** til at tilføje, redigere, eller Slet lagerplads konti eller rotere tasterne sikkerhed for en lagerplads konto.

 ![Konfigurere side](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Lagerplads konti indeholder de legitimationsoplysninger, som enheden, der bruges til at få adgang til kontoen lagring af din tjenesteudbyder til skyen. Dette er legitimationsoplysninger som kontonavnet og det primære hurtigtast for Microsoft Azure-lager-konti. 

På siden **Konfigurer** vises alle lagerplads konti, der er oprettet for fakturering abonnementet i et tabelformat, der indeholder følgende oplysninger:

- **Navn** – det entydige navn, der er tildelt til kontoen, når det blev oprettet.
- **SSL aktiveret** – om feltet SSL er aktiveret og enhed i skyen kommunikation er via den sikre kanal.
- **Bruges ved** – antallet enheder ved hjælp af kontoen lagerplads.

De mest almindelige opgaver, der er relateret til lagerplads konti, der kan udføres på siden **Konfigurer** er:

- Tilføje en konto til lager 
- Redigere en lagerplads konto 
- Slette en lagerplads konto 
- Vigtige rotation af lagerplads konti 

## <a name="types-of-storage-accounts"></a>Kontotyper lagerplads

Der findes tre typer lagerplads konti, der kan bruges med enheden StorSimple.

- **Automatisk oprettede lagerplads konti** – som navnet antyder, denne type lagerplads konto genereres automatisk, når tjenesten oprettes. Du kan få mere at vide om, hvordan denne lagerplads konto er oprettet, [trin 1: oprette en ny tjeneste](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) i [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough.md). 
- **Lagerplads konti i tjenesten abonnement** – dette er de Azure-lager-konti, der er knyttet til den samme abonnement som tjenesten. Få mere for at vide om, hvordan disse lagerplads konti er oprettet, kan du se [Om Azure lagerplads konti](../storage/storage-create-storage-account.md). 
- **Lagerplads konti uden for tjenesten abonnementet** – dette er de Azure-lager-konti, der ikke er knyttet til din tjeneste og sandsynligt fandtes før tjenesten blev oprettet.

## <a name="add-a-storage-account"></a>Tilføje en konto til lager

Du kan tilføje en lagerplads konto ved at give et entydigt navn og legitimationsoplysninger, der er knyttet til kontoen lagerplads (med udbyderen af den angivne sky). Du har også mulighed for at aktivere tilstanden secure sockets layer (SSL) til at oprette en sikker kanal af netværkskommunikation mellem enheden og skyen.

Du kan oprette flere konti for en given skyen udbyder. Vær opmærksom på, men, når en lagerplads konto er oprettet, kan du ændre udbyderen af skyen.

Mens kontoen lagerplads gemmes, forsøger tjenesten at kommunikere med udbyderen skyen. Legitimationsoplysningerne, og den access-materiale, du har angivet godkendes på nuværende tidspunkt. En lagerplads konto oprettes kun, hvis godkendelsen lykkes. Hvis godkendelsen mislykkes, derefter vises en relevant fejlmeddelelse.

Ressourcestyring lagerplads konti, der er oprettet i Azure portal understøttes også med StorSimple. Ressourcestyring lagerplads konti vises ikke i rullelisten for markerede når du forsøger at oprette en lydstyrken beholder, kun lagerplads konti, der er oprettet i portalen Azure klassisk vises. Ressourcestyring lagerplads konti skal tilføjes ved hjælp af fremgangsmåden til at tilføje en lagerplads-konto, der er beskrevet nedenfor.

> [AZURE.NOTE] Fremgangsmåden til at tilføje en lagerplads konto varierer afhængigt af versionerne StorSimple software, du bruger. Sørg for at den korrekte fremgangsmåde til din StorSimple version.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Redigere en lagerplads konto

Du kan redigere en lagerplads-konto, der bruges af en objektbeholder lydstyrken. Hvis du redigerer en lagerplads-konto, som er i brug, er det eneste felt, der er tilgængelige for redigering hurtigtast for kontoen lagerplads. Du kan angive nye lagerplads hurtigtast og gemme de opdaterede indstillinger.

#### <a name="to-edit-a-storage-account"></a>Redigere en lagerplads konto

1. På landingsside service, Vælg din tjeneste, dobbeltklik på navnet på tjenesten og derefter klikke på **Konfigurer**.

2. Klik på **Tilføj/Rediger lagerplads konti**.

3. I dialogboksen **Add/Edit lagerplads konti** :

  1. Vælg en eksisterende konto, du vil ændre, i rullelisten over **Lagerplads konti**. Det kan også omfatte kontiene lagerplads, der blev oprettet automatisk, når tjenesten blev oprettet.
  2. Hvis det er nødvendigt, kan du ændre indstillingen **Aktiver SSL-tilstand** .
  3. Du kan vælge at rotere hurtigtaster din lagerplads konto. Se [tasten rotation af lagerplads konti](#key-rotation-of-storage-accounts) kan finde flere oplysninger om, hvordan du udfører vigtige rotation.
  4. Klik på ikonet Kontrollér ![markere ikonet](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) til at gemme indstillingerne. Indstillingerne for opdateres på siden **Konfigurer** . Klik på **Gem** for at gemme de opdaterede indstillinger.

     ![Redigere en lagerplads konto](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Slette en lagerplads konto

> [AZURE.IMPORTANT] Kun, hvis det ikke bruges af en volumenlicens objektbeholder, kan du slette en lagerplads konto. Hvis en lagerplads konto bruges ved en lydstyrken objektbeholder, først slette objektbeholderen lydstyrken og derefter slette kontoen tilknyttede lagerplads.

#### <a name="to-delete-a-storage-account"></a>Slette en lagerplads konto

1. På landingssiden StorSimple Manager service, Vælg din tjeneste, dobbeltklik på navnet på tjenesten og derefter klikke på **Konfigurer**.

2. Hold markøren over den konto, du vil slette, på listen tabelformat af lagerplads konti.

3. En sletteikonet (**x**) vises i kolonnen yderst til højre for den pågældende lagerplads konto. Klik på **x** for at slette legitimationsoplysningerne.

4. Når du bliver bedt om en bekræftelse, skal du klikke på **Ja** for at fortsætte med sletningen. Listen tabelformat opdateres afspejler ændringerne.

## <a name="key-rotation-of-storage-accounts"></a>Vigtige rotation af lagerplads konti

Vigtige rotation er ofte et krav i datacentre af sikkerhedsmæssige årsager. 

> [AZURE.NOTE] Følgende vigtige rotation oplysninger og rotation fremgangsmåde gælder for Microsoft Azure-lager-konti. Hvis du bruger en anden skybaseret tjenesteudbyder, kan du administrere lagerplads konto taster via denne udbyders dashboard.
 
Hvert Microsoft Azure-abonnement kan have en eller flere tilknyttede lagerplads konti. Adgang til disse konti styres af tasterne abonnement og adgang for hver konto lagerplads. 

Når du opretter en konto med lagerplads, genererer Microsoft Azure to 512-bit lagerplads access-taster, der bruges til godkendelse, når der opnås adgang til kontoen lagerplads. Har du to lagerplads access-taster, kan du gendanne de pågældende taster med ingen afbrudt lagerplads eller access i den pågældende tjeneste. Den nøgle, der er aktiveret i øjeblikket er den *primære* nøgle og tasten sikkerhedskopiering kaldes en *sekundær* nøgle. En af disse to nøgler skal angives, når enheden Microsoft Azure StorSimple får adgang til udbyderen skyen lagerplads.

## <a name="what-is-key-rotation"></a>Hvad er vigtige rotation?

Programmer bruger kun én af de pågældende taster typisk til at få adgang til dine data. Efter en bestemt periode, kan du få dine programmer, skifte til at bruge den anden nøgle. Når du har skiftet programmerne til den sekundære nøgle, kan du trække den første tast og derefter oprette en ny nøgle. Ved hjælp af de to nøgler på denne måde kan dine programmer adgang til data uden at der påløber en hvilken som helst nedetid.

Tasterne lagerplads konto gemmes altid i tjenesten i en krypteret form. Disse kan dog nulstilles via tjenesten StorSimple Manager. Tjenesten kan få den primære nøgle og sekundær nøgle for alle kontiene, lagring i det samme abonnement, herunder konti, der er oprettet i tjenesten Storage som standard opbevaring konti, der er oprettet, da tjenesten StorSimple Manager første gang blev oprettet. Tjenesten StorSimple Manager kan altid få disse taster fra Azure klassisk portalen og derefter gemme dem i en krypteret måde.

## <a name="rotation-workflow"></a>Rotation arbejdsproces

En Microsoft Azure-administrator kan genoprette eller ændre nøglen primær eller sekundær ved direkte adgang til kontoen lagerplads (via tjenesten Microsoft Azure-lager). Tjenesten StorSimple Manager se ikke denne ændring automatisk.

For at informere StorSimple Manager-tjenesten for ændringen, har brug for du at få adgang til tjenesten StorSimple Manager få adgang til kontoen lagerplads, og synkroniser derefter tasten primær eller sekundær (afhængigt af hvilken af skærmene blev ændret). Tjenesten derefter får den seneste nøgle krypterer tasterne og sender den krypterede nøgle til enheden.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Synkronisere taster til lagerplads konti i samme abonnement som tjenesten (kun Azure)

1. Klik på fanen **Konfigurer** på siden **Services** .

2. Klik på **Tilføj/Rediger lagerplads konti**.

3. I dialogboksen skal du gøre følgende:

  1. Vælg kontoen, lagerplads med nøglen, som du vil synkronisere. Tasterne lagerplads konto er krypteret, når de vises.
  2. I tjenesten StorSimple Manager skal du opdatere den nøgle, der tidligere er blevet ændret i tjenesten Microsoft Azure-lager. Hvis den primære hurtigtast blev ændret (gendannede), skal du klikke på **Synkroniser primær nøgle**. Hvis en sekundær nøgle blev ændret, kan du klikke på **Synkroniser sekundær nøgle**.

    ![synkronisere taster](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Synkronisere taster til lagerplads konti uden for serviceabonnementet

1. Klik på fanen **Konfigurer** på siden **Services** .

2. Klik på **Tilføj/Rediger lagerplads konti**.

3. I dialogboksen skal du gøre følgende:

  1. Vælg kontoen, lagerplads med nøglen adgang, som du vil opdatere.
  2. Du skal opdatere hurtigtast lagerplads i tjenesten StorSimple Manager. I dette tilfælde kan du se hurtigtast lagerplads. Angiv den nye nøgle i feltet **Lagerplads konto hurtigtast**y. 
  3. Gem dine ændringer. Din lagerplads kontonøgle access bør nu opdateres.

## <a name="next-steps"></a>Næste trin

- Lær mere om [StorSimple sikkerhed](storsimple-security.md).
- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
