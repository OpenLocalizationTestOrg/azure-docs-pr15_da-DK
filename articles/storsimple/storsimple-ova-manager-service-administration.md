<properties 
   pageTitle="Administration af StorSimple Manager virtuelle matrix | Microsoft Azure"
   description="Lær at administrere din StorSimple lokale virtuelle matrix ved hjælp af tjenesten StorSimple Manager i portalen Azure klassisk."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Brug tjenesten StorSimple Manager til at administrere din StorSimple virtuelle matrix

![konfiguration af arbejdsgang](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Oversigt

I denne artikel beskrives grænsefladen StorSimple Manager service, herunder hvordan du opretter forbindelse til den og de forskellige muligheder, og indeholder links til de bestemte arbejdsprocesser, der kan udføres via Brugergrænsefladen. 

Når du har læst i denne artikel, du, hvordan du:

- Oprette forbindelse til tjenesten StorSimple Manager
- Navigere i brugergrænsefladen i StorSimple Manager.
- Administrere din StorSimple virtuelle matrix via tjenesten StorSimple Manager

> [AZURE.NOTE] Gå til [Brug tjenesten StorSimple Manager til at administrere din StorSimple enhed](storsimple-manager-service-administration.md)for at få vist de tilgængelige til StorSimple 8000 serie enhed management indstillinger.

## <a name="connect-to-the-storsimple-manager-service"></a>Oprette forbindelse til tjenesten StorSimple Manager

Tjenesten StorSimple Manager kører i Microsoft Azure og opretter forbindelse til flere StorSimple virtuelle matrixer. Du kan bruge en central klassisk Microsoft Azure-portal kører i en browser til at administrere disse enheder. Hvis du vil oprette forbindelse til tjenesten StorSimple Manager, skal du gøre følgende.

#### <a name="to-connect-to-the-service"></a>Oprette forbindelse til tjenesten

1. Gå til [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Ved hjælp af din Microsoft-kontolegitimationsoplysninger, log på portalen Microsoft Azure klassisk (findes i ruden øverst til højre).

3. Rul ned i venstre navigationsrude for at få adgang til tjenesten StorSimple Manager.

    ![Rul ned til tjenesten](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Navigere tjenesten StorSimple Manager brugergrænseflade

Navigationsmæssige hierarkiet for tjenesten StorSimple Manager brugergrænseflade vises i den følgende tabel.

- Landingssiden **StorSimple Manager** tager dig til Brugergrænsefladen service niveau siderne gælder for alle virtuelle matrixer inden for en tjeneste.

- Siden **enheder** tager dig til gælder for en bestemt virtuelle matrix-niveau for enheden brugergrænseflade til sider.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple Manager service navigationsmæssige hierarki

|Landingsside|Tjenesten niveau sider|Niveau for enheden sider|
|---|---|---|
|Tjenesten StorSimple Manager|Dashboard (service)|Dashboard (enhed)|
||Enheder →|Skærm|
||Kataloget sikkerhedskopiering|Aktier (filserver) eller </br>Enheder (iSCSI-server)|
||Konfigurere (service)|Konfigurere (enhed)|
||Job|Vedligeholdelse|
||Beskeder|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Bruge tjenesten StorSimple Manager til at udføre administrationsopgaver

I følgende tabel vises en oversigt over alle de almindelige administrationsopgaver og komplekse arbejdsprocesser, der kan udføres i tjenesten StorSimple Manager brugergrænseflade. Disse opgaver er organiseret baseret på siderne brugergrænseflade, som de er startet.

Du kan finde flere oplysninger om hver af arbejdsprocessen, skal du klikke på den relevante procedure i tabellen.

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager arbejdsprocesser

|Hvis du vil gøre dette...|Gå til denne side for Brugergrænsefladen...|Brug denne procedure|
|---|---|---|
|Oprette en tjeneste</br>Slette en tjeneste</br>Få tasten service registrering</br>Genoprette nøglen service registrering|Tjenesten StorSimple Manager|[Installere tjenesten StorSimple Manager](storsimple-ova-manage-service.md)|
|Ændre krypteringsnøglen service data</br>Få vist loggene handlinger|StorSimple Manager service → Dashboard|[Brug dashboardet StorSimple service](storsimple-ova-service-dashboard.md)|
|Deaktivere en virtuel matrix</br>Slette en virtuel matrix|StorSimple Manager service → enheder|[Deaktivere eller slette en virtuel matrix](storsimple-ova-deactivate-and-delete-device.md)|
|Nedbrud gendannelse og enhed failover</br>Failover forudsætninger</br>Failover til en virtuel enhed</br>Business løbende nedbrud (BCDR)</br>Fejl under nedbrud|StorSimple Manager service → enheder|[Nedbrud gendannelse og enhed sekundær server til din StorSimple virtuelle matrix](storsimple-ova-failover-dr.md)|
|Sikkerhedskopiere aktier og enheder</br>Tage en sikkerhedskopi manuelt</br>Ændre tidsplanen for sikkerhedskopiering</br>Få vist eksisterende sikkerhedskopier|StorSimple Manager service → sikkerhedskopi katalog|[Sikkerhedskopiere din StorSimple virtuelle matrix](storsimple-ova-backup.md)|
|Gendanne aktier fra et sæt sikkerhedskopier</br>Gendanne enheder fra et sæt sikkerhedskopier</br>På elementniveau gendannelse (kun filserver)|StorSimple Manager service → sikkerhedskopi katalog|[Gendanne fra en sikkerhedskopi af din StorSimple virtuelle matrix](storsimple-ova-restore.md)|
|Om lagerplads konti</br>Tilføje en konto til lager</br>Redigere en lagerplads konto</br>Slette en lagerplads konto|Konfigurere StorSimple Manager service →|[Administrer lagerplads konti for StorSimple virtuelle matrixen](storsimple-ova-manage-storage-accounts.md)|
|Om access kontrolelement poster</br>Tilføje eller ændre en access kontrolelement post </br>Slette en post til kontrol af adgang|Konfigurere StorSimple Manager service →|[Administrere adgang kontrolelement poster for den virtuelle StorSimple-matrix](storsimple-ova-manage-acrs.md)|
|Se detaljer om job|StorSimple Manager → servicejob| [Administrere StorSimple virtuelle matrix job](storsimple-ova-manage-jobs.md)|
|Konfigurere beskedindstillinger</br>Modtage påmindelser</br>Administrer beskeder</br>Gennemse beskeder|StorSimple Manager service → beskeder|[Få vist og administrere beskeder til den virtuelle StorSimple-matrix](storsimple-ova-manage-alerts.md)|
|Ændre administratoradgangskode enhed|StorSimple Manager service → enheder → konfigurere|[Ændre StorSimple virtuelle matrix enhed administratoradgangskode](storsimple-ova-change-device-admin-password.md)|
|Installere softwareopdateringer|StorSimple Manager service → enheder → vedligeholdelse|[Opdatere din virtuelle matrix](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Du skal bruge [lokale web brugergrænseflade](storsimple-ova-web-ui-admin.md) til følgende opgaver:
>
>- [Hente krypteringsnøglen service data](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Oprette en supportpakke](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Stop og genstart en virtuel matrix](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Næste trin
Gå til [bruge webdelen StorSimple Brugergrænsefladen til at administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md)for oplysninger om web Brugergrænsefladen og hvornår det skal bruges.
