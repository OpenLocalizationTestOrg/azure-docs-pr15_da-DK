<properties 
   pageTitle="Installere tjenesten StorSimple Manager for StorSimple virtuelle matrix | Microsoft Azure"
   description="Forklarer, hvordan du kan oprette og slette tjenesten StorSimple Manager i portalen Azure klassisk, og beskriver, hvordan du administrerer tasten service registrering."
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
   ms.date="05/19/2016"
   ms.author="alkohli" />

# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>Installere tjenesten StorSimple Manager for StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt

Tjenesten StorSimple Manager kører i Microsoft Azure og opretter forbindelse til flere StorSimple enheder. Når du opretter tjenesten, kan du bruge det til at administrere enhederne fra portalen Microsoft Azure klassisk kører i en browser. Dette kan du overvåge alle de enheder, der har forbindelse til tjenesten StorSimple Manager fra et centralt sted, og minimere administrative arbejde.

Landingssiden StorSimple Manager viser alle de StorSimple Manager-tjenester, du kan bruge til at administrere din StorSimple lagerenheder. For hver StorSimple Manager-tjeneste vises følgende oplysninger på siden StorSimple Manager:

- **Navn** – det navn, der blev tildelt til din StorSimple Manager-tjenesten, da det blev oprettet. Navnet på tjenesten kan ikke ændres, når tjenesten er blevet oprettet.

- **Status** – status for tjenesten, som kan være **aktive**, **oprette**eller **Online**.

- **Placering** – geografiske placering, som skal installeres StorSimple enhed.

- **Abonnement** – fakturering abonnementet, der er knyttet til din tjeneste.

De almindelige opgaver, der kan udføres via siden StorSimple Manager er:

- Oprette en tjeneste
- Slette en tjeneste
- Få tasten service registrering
- Genoprette nøglen service registrering

I dette selvstudium beskrives, hvordan du udfører hver af disse opgaver. Oplysningerne i denne artikel gælder kun for StorSimple virtuelle matrixer. Gå til at [installere en StorSimple Manager service](storsimple-manage-service.md)kan finde flere oplysninger om StorSimple 8000 serie.

## <a name="create-a-service"></a>Oprette en tjeneste

Brug indstillingen **Hurtig oprettelse** til at oprette en StorSimple Manager-tjeneste, hvis du vil installere enheden StorSimple. Hvis du vil oprette en tjeneste, vil du have:

- Et abonnement med en Enterprise Agreement
- En aktiv Microsoft Azure-lager konto
- Oplysningerne om fakturering, der bruges til at administrere adgang

Du kan også vælge at generere en standardkonto lagerplads, når du opretter tjenesten.

En enkelt tjeneste kan administrere flere enheder. En enhed kan dog spænder over flere tjenester. En stor virksomhed kan have flere forekomster af tjenesten til at arbejde med forskellige abonnementer, organisationer eller endda installation placeringer.  

> [AZURE.NOTE] Du har brug for separate forekomster af tjenesten StorSimple Manager til at administrere StorSimple 8000 serie enheder og StorSimple virtuelle matrixer.

Udfør følgende trin for at oprette en tjeneste.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>Slette en tjeneste

Før du sletter en tjeneste, skal du kontrollere, at ingen forbundne enheder bruger den. Hvis tjenesten er i brug, skal du deaktivere de tilsluttede enheder. Handlingen Deaktiver bryde forbindelsen mellem enheden og -tjenesten, men bevare dataene enhed i skyen. 

> [AZURE.IMPORTANT] Efter en tjeneste er blevet slettet, kan ikke fortrydes handlingen. 

Udfør følgende trin for at slette en tjeneste.

### <a name="to-delete-a-service"></a>Slette en tjeneste

1. Vælg den tjeneste, du vil slette, på siden **StorSimple Manager service** .

1. Klik på **Slet** nederst på siden.

1. Klik på **Ja** i bekræftelsesmeddelelsen. Det kan tage et par minutter til at blive slettet-tjenesten.

## <a name="get-the-service-registration-key"></a>Få tasten service registrering

Når du har oprettet en tjeneste, skal du registrere enheden StorSimple med tjenesten. Hvis du vil registrere din første StorSimple enhed, skal du tasten service registrering. Hvis du vil registrere flere enheder med en eksisterende StorSimple-tjeneste, skal du både tasten registrering og nøglen service data kryptering (som er oprettet på den første enhed under registrering). Se [få krypteringsnøglen service data fra lokale internettet brugergrænseflade](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)kan finde flere oplysninger om tjenesten data krypteringsnøglen. 

Udfør følgende trin for at få tasten service registrering.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Holde tasten service registrering i et sikkert sted. Du skal bruge denne tast samt til at registrere flere enheder med denne tjeneste krypteringsnøgle service data. Efter at opnå tasten service registrering, skal du konfigurere din enhed via Windows PowerShell til StorSimple-grænseflade.

## <a name="regenerate-the-service-registration-key"></a>Genoprette nøglen service registrering

Du skal genoprette en nøgle til registrering af tjenesten, hvis du er påkrævet for at udføre vigtige rotation, eller hvis på listen over administratorer af tjenester er ændret. Når du genoprette nøglen, bruges den nye nøgle kun for din registrering efterfølgende enheder. De enheder, der allerede er blevet registreret påvirkes ikke af denne proces.

Udfør følgende trin for at genoprette en tjeneste registreringsnøgle.

### <a name="to-regenerate-the-service-registration-key"></a>At genoprette nøglen service registrering

1. Klik på **Registreringsnøgle**på siden **StorSimple Manager-tjenesten** .

1. Klik på **genoprette**i dialogboksen **Service registrering-nøglen** .

1. Du får vist en bekræftelsesmeddelelse. Klik på **OK** for at fortsætte med gendannelse.

1. En ny tjeneste registreringsnøgle vises.

1. Kopiér denne nøgle og gemme den til registrering af alle nye enheder med denne tjeneste.

1. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-ova-manage-service/image7.png) for at lukke denne dialogboks.


## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [komme i gang](storsimple-ova-deploy1-portal-prep.md) med en StorSimple virtuelle matrix.
    
- Lær, hvordan du [administrerer enheden StorSimple](storsimple-ova-web-ui-admin.md).

 
