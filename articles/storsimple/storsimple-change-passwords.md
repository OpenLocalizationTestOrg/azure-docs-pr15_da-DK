<properties 
   pageTitle="Ændre adgangskoderne StorSimple | Microsoft Azure" 
   description="Beskriver, hvordan du bruger tjenesten StorSimple Manager til at ændre din StorSimple af snapshots og enhed adgangskoder for administratorer." 
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
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Bruge tjenesten StorSimple Manager til at ændre dine StorSimple adgangskoder

## <a name="overview"></a>Oversigt 

Azure klassisk portalen **Konfigurer** siden indeholder alle de enhedsparametre, du kan konfigurere på en enhed med StorSimple, der administreres af en StorSimple Manager-tjeneste. Dette selvstudium beskrives det, hvordan du kan bruge siden **Konfigurer** til at ændre din enhedsadministrator eller StorSimple øjebliksbillede Manager-adgangskode.

## <a name="change-the-device-administrator-password"></a>Ændre administratoradgangskode enhed

Når du bruger Windows PowerShell-grænseflade til at få adgang til StorSimple enheden, er du skal indtaste en enhed administratoradgangskode. Når den første StorSimple enhed er registreret med en tjeneste, er standardadgangskoden til denne grænseflade *Adgangskode1*. For sikkerheden for dine data skal du ændre denne adgangskode i slutningen af registreringsprocessen. Du kan ikke afslutte fra registreringsprocessen uden at ændre denne adgangskode. Du kan finde flere oplysninger, se [trin 3: konfigurere og registrere enheden via Windows PowerShell til StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Den adgangskode, der blev angivet først via brugergrænsefladen i Windows PowerShell under registrering kan derefter ændres via Azure klassisk-portalen. Udfør følgende trin for at ændre administratoradgangskode enhed.

#### <a name="to-change-the-device-administrator-password"></a>Ændre enhed administratoradgangskoden

1. Klik på **enheder**i portalen klassisk > **Konfigurer** til din enhed.

2. Rul ned til sektionen **Enhed administratoradgangskode** . Angiv en adgangskode, der indeholder fra 8 til 15 tegn. Adgangskoden skal være en kombination af 3 eller flere af store bogstaver, små bogstaver, numeriske og særlige tegn.

3. Bekræft adgangskoden.

4. Klik på **Gem** nederst på siden.

Administratoradgangskode enhed bør nu opdateres. Du kan bruge denne ændret adgangskode til at få adgang til Windows PowerShell-grænseflade.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Ændre adgangskoden StorSimple øjebliksbillede Manager

StorSimple øjebliksbillede Manager software findes på din Windows-vært og giver administratorer mulighed for at administrere sikkerhedskopier af enheden StorSimple i form af lokal og snapshots i skyen.

Når du konfigurerer en enhed i StorSimple øjebliksbillede Manager, bliver du bedt om at angive enhed IP-adressen og adgangskoden til at godkende enheden lagerplads. Denne adgangskode er først konfigureret via brugergrænsefladen i Windows PowerShell. Du kan finde flere oplysninger, se [trin 3: konfigurere og registrere enheden via Windows PowerShell til StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Den adgangskode, der blev angivet først via brugergrænsefladen i Windows PowerShell under registrering kan derefter ændres via portalen klassisk. Udfør følgende trin for at ændre StorSimple Snapshot Manager-adgangskoden.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Ændre adgangskoden StorSimple øjebliksbillede Manager

1. Klik på **enheder**i portalen klassisk > **Konfigurer** til din enhed.

2. Rul ned til sektionen **StorSimple øjebliksbillede Manager** . Angiv en adgangskode, der er 14 eller 15 tegn. Sørg for, at adgangskoden indeholder en kombination af 3 eller flere af store bogstaver, små bogstaver, numeriske og særlige tegn.

3. Bekræft adgangskoden.

4. Klik på **Gem** nederst på siden.

Den StorSimple øjebliksbillede Manager-adgangskode bør nu opdateres.
 

## <a name="next-steps"></a>Næste trin

- Lær mere om [StorSimple sikkerhed](storsimple-security.md).

- Lær mere om [ændring af enhedskonfigurationen af](storsimple-modify-device-config.md).

- Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
