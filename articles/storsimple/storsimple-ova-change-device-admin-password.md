<properties 
   pageTitle="Ændre StorSimple virtuel enhed administratoradgangskode | Microsoft Azure"
   description="Beskriver, hvordan bruge Azure klassisk portalen eller webdelen StorSimple virtuelle matrix brugergrænseflade til at ændre administratoradgangskode enhed."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Ændre StorSimple virtuelle matrix enhed administratoradgangskode

## <a name="overview"></a>Oversigt

Når du bruger grænsefladen Windows PowerShell til at få adgang til den virtuelle StorSimple-enhed, skal du angive en enhed administratoradgangskode. Når StorSimple enheden er først klargjort og i gang, er standardadgangskoden *Adgangskode1*. Standardadgangskoden udløber for sikkerheden for dine data, første gang, du logger på, og det er nødvendigt at ændre denne adgangskode.

Du kan også bruge den lokale internettet brugergrænseflade eller portalen Azure klassisk ændre administratoradgangskode enhed til enhver tid, når enheden er distribueret i dit produktionsmiljø. Hver af disse procedurer er beskrevet i denne artikel.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Brug Azure klassisk portalen for at ændre adgangskoden

Udfør følgende trin for at ændre administratoradgangskode enhed via Azure klassisk portalen.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Ændre administratoradgangskode enhed via Azure klassisk-portalen

1. Klik på **enheder**i portalen > **konfiguration** til din enhed.

2. Rul ned til sektionen **Enhed administratoradgangskode** . Angiv en adgangskode, der indeholder fra 8 til 15 tegn. Adgangskoden skal være en kombination af store bogstaver, små bogstaver, numeriske og særlige tegn.

3. Bekræft adgangskoden.

4. Klik på **Gem** nederst på siden.

Administratoradgangskode enhed bør nu opdateres. Du kan bruge denne ændret adgangskode til at få adgang til enheden lokalt.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Brug webdelen StorSimple virtuelle matrix Brugergrænsefladen til at ændre adgangskoden

Udfør følgende trin for at ændre administratoradgangskode enhed via lokale internettet brugergrænseflade.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Ændre administratoradgangskode enhed via lokale internettet brugergrænseflade

1. Klik på **vedligeholdelse**på det lokale websted brugergrænseflade, > **ændre adgangskoden** til din enhed.

    ![ændre Adgangskode1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Skriv den **aktuelle adgangskode**.

3. Angiv en **Ny adgangskode**. Adgangskoden skal være mindst 8 tegn. Skal den indeholde 3 af 4 af følgende: store bogstaver, små bogstaver, numeriske og særlige tegn.

    Bemærk, at din adgangskode ikke må være den samme som de seneste 24 adgangskoder.

3. Skriv adgangskoden for at bekræfte den igen.

    ![ændre Adgangskode2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Nederst på siden skal du klikke på **Anvend**. Den nye adgangskode bliver herefter anvendt. Hvis ændringen af adgangskode ikke lykkes, vises følgende fejl.

    ![adgangskode fejl](./media/storsimple-ova-change-device-admin-password/image42.png)

    Når adgangskoden er blevet opdateret, får du besked. Derefter kan du bruge denne ændret adgangskode til at få adgang til enheden lokalt.

## <a name="next-steps"></a>Næste trin

Lær mere om at [administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
