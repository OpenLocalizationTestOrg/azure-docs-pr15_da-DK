<properties 
   pageTitle="Administration af tjenesten StorSimple Manager | Microsoft Azure"
   description="Lær at administrere din StorSimple enhed ved hjælp af tjenesten StorSimple Manager i portalen Azure klassisk."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Brug tjenesten StorSimple Manager til at administrere din StorSimple enhed

## <a name="overview"></a>Oversigt

I denne artikel beskrives grænsefladen StorSimple Manager service, herunder hvordan du opretter forbindelse til det, de forskellige muligheder og links ud til de bestemte arbejdsprocesser, der kan udføres via Brugergrænsefladen. Denne vejledning er gældende for begge; StorSimple fysiske og den virtuelle enhed.

Når du har læst i denne artikel, vil du lære at:

- Oprette forbindelse til tjenesten StorSimple Manager.
- Navigere i brugergrænsefladen i StorSimple Manager.
- Administrere enheden StorSimple via tjenesten StorSimple Manager


## <a name="connect-to-storsimple-manager-service"></a>Oprette forbindelse til tjenesten StorSimple Manager.

Tjenesten StorSimple Manager kører i Microsoft Azure og opretter forbindelse til flere StorSimple enheder. Du kan bruge en central klassisk Microsoft Azure-portal kører i en browser til at administrere disse enheder. Hvis du vil oprette forbindelse til tjenesten StorSimple Manager, skal du gøre følgende.

#### <a name="to-connect-to-the-service"></a>Oprette forbindelse til tjenesten

1. Gå til [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Ved hjælp af din Microsoft-kontolegitimationsoplysninger, log på portalen Microsoft Azure klassisk (findes i ruden øverst til højre).

1. Rul ned i venstre navigationsrude for at få adgang til tjenesten StorSimple Manager.


## <a name="navigate-storsimple-manager-service-ui"></a>Navigere StorSimple Manager service brugergrænseflade

Navigationsmæssige hierarkiet for tjenesten StorSimple Manager brugergrænseflade vises i den følgende tabel.

- **StorSimple Manager** landingsside tager dig til Brugergrænsefladen service niveau siderne gælder for alle enheder i en tjeneste.

- **Enheder** siden tager dig til – niveau for enheden Brugergrænsefladen sider, som er gældende for en bestemt enhed.

- **Lydstyrken beholdere** siden fører dig til siden lydstyrken, der viser alle de enheder, der er knyttet til en enhed.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple Manager service navigationsmæssige hierarki

|Landingsside|Tjenesten niveau sider|Niveau for enheden sider|Niveau for enheden sider|
|---|---|---|---|
|Tjenesten StorSimple Manager|Tjenesten dashboard|Enhed dashboard||
||Enheder →|Skærm|
||Kataloget sikkerhedskopiering|Lydstyrke containers→|Enheder|
||Konfigurere (Service)|Politikker for sikkerhedskopiering||
||Job|Konfigurere (enhed)|
||Beskeder|Vedligeholdelse|

![Video er til rådighed](./media/storsimple-manager-service-administration/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der vejleder dig gennem StorSimple Manager service-brugergrænseflade, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrere StorSimple enhed ved hjælp af tjenesten StorSimple Manager

I følgende tabel vises en oversigt over alle de almindelige administrationsopgaver og komplekse arbejdsprocesser, der kan udføres i tjenesten StorSimple Manager brugergrænseflade. Disse opgaver er organiseret baseret på siderne brugergrænseflade, som de er startet.

Du kan finde flere oplysninger om hver af arbejdsprocessen, skal du klikke på den relevante procedure i tabellen.

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager arbejdsprocesser

|Hvis du vil gøre dette...|Gå til denne side for Brugergrænsefladen...|Brug denne procedure.|
|---|---|---|
|Oprette en tjeneste</br>Slette en tjeneste</br>Få tjenestens registreringsnøgle</br>Genoprette tjenestens registreringsnøgle|Tjenesten StorSimple Manager|[Installere en StorSimple Manager-tjeneste](storsimple-manage-service.md)
|Ændre krypteringsnøglen service data</br>Få vist loggene handling|StorSimple Manager service → Dashboard|[Brug dashboardet StorSimple Manager service](storsimple-service-dashboard.md)|
|Deaktivere en enhed</br>Slette en enhed|StorSimple Manager service → enheder|[Deaktivere eller slette en enhed](storsimple-deactivate-and-delete-device.md)|
|Få mere at vide om nedbrud gendannelse og enhed failover</br>Failover til en fysisk enhed</br>Failover til en virtuel enhed</br>Business løbende nedbrud (BCDR)|StorSimple Manager service → enheder|[Failover og genoprettelse efter nedbrud for enheden StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Listen sikkerhedskopier af en enhed</br>Vælg et sæt sikkerhedskopier</br>Slette et sæt sikkerhedskopier|StorSimple Manager service → sikkerhedskopi katalog|[Administrere sikkerhedskopier](storsimple-manage-backup-catalog.md)|
|Klone en volumenlicens|StorSimple Manager service → sikkerhedskopi katalog|[Klone en volumenlicens](storsimple-clone-volume.md)|
|Gendanne et sæt sikkerhedskopier|StorSimple Manager service → sikkerhedskopi katalog|[Gendanne et sæt sikkerhedskopier](storsimple-restore-from-backup-set.md)|
|Om lagerplads konti</br>Tilføje en konto til lager</br>Redigere en lagerplads konto</br>Slette en lagerplads konto</br>Vigtige rotationen af lagerplads konti|Konfigurere StorSimple Manager service →|[Administrere lager konti](storsimple-manage-storage-accounts.md)|
|Om båndbredde skabeloner</br>Tilføje en båndbredde skabelon</br>Redigere en båndbredde skabelon</br>Slette en båndbredde skabelon</br>Bruge en standardskabelon båndbredde</br>Oprette en skabelon til varer hele dagen båndbredde, der starter på et bestemt tidspunkt|Konfigurere StorSimple Manager service →|[Administrere båndbredde skabeloner](storsimple-manage-bandwidth-templates.md)|
|Om access kontrolelement poster</br>Oprette en access-kontrolelement post</br>Redigere en post til kontrol af adgang</br>Slette en post til kontrol af adgang|Konfigurere StorSimple Manager service →|[Administrere adgang kontrolelement poster](storsimple-manage-acrs.md)|
|Se detaljer om job</br>Annullere et job|StorSimple Manager → servicejob|[Administrere job](storsimple-manage-jobs.md)
|Modtage påmindelser</br>Administrer beskeder</br>Gennemse beskeder|StorSimple Manager service → beskeder|[Få vist og administrere StorSimple beskeder](storsimple-manage-alerts.md)
|Få vist forbundne initiatorer</br>Finde serienummeret enhed</br>Finde destinationen IQN|StorSimple Manager service → enheder → Dashboard|[Brug dashboardet StorSimple enhed](storsimple-device-dashboard.md)|
|Oprette overvågning diagrammer|StorSimple Manager service → enheder → overvåge|[Overvåge enheden StorSimple](storsimple-monitor-device.md)|
|Tilføje en lydstyrken objektbeholder</br>Ændre en lydstyrken objektbeholder</br>Slette en lydstyrken objektbeholder|StorSimple Manager service → enheder → lydstyrken objektbeholdere|[Administrere lydstyrken objektbeholdere](storsimple-manage-volume-containers.md)|
|Tilføje en disk</br>Ændre en volumenlicens</br>Tage en lydstyrken offline</br>Slette en volumenlicens</br>Overvåge en volumenlicens|StorSimple Manager service → enheder → lydstyrken beholdere → enheder|[Administrere enheder](storsimple-manage-volumes.md)|
|Ændre indstillinger for lydenhed</br>Ændre tidsindstillinger</br>Redigere indstillinger for DNS.md</br>Konfigurere netværksgrænseflader|StorSimple Manager service → enheder → konfigurere|[Ændre enhedskonfiguration for enheden StorSimple](storsimple-modify-device-config.md)|
|Vis web proxyindstillinger|StorSimple Manager service → enheder → konfigurere|[Konfigurere webtjenesteproxy til din enhed](storsimple-configure-web-proxy.md)|
|Ændre enhed administratoradgangskode</br>Ændre StorSimple øjebliksbillede Manager-adgangskode|StorSimple Manager service → enheder → konfigurere|[Ændre StorSimple adgangskoder](storsimple-change-passwords.md)|
|Konfigurere fjernadministration|StorSimple Manager service → enheder → konfigurere|[Oprette forbindelse fra en fjernplacering til enheden StorSimple](storsimple-remote-connect.md)|
|Konfigurere beskedindstillinger|StorSimple Manager service → enheder → konfigurere|[Få vist og administrere StorSimple beskeder](storsimple-manage-alerts.md)|
|Konfigurere CHAP til enheden StorSimple|StorSimple Manager service → enheder → konfigurere|[Konfigurere CHAP til enheden StorSimple](storsimple-configure-chap.md)|
|Tilføje en ekstra politik</br>Tilføje eller ændre en tidsplan</br>Slette en politik for sikkerhedskopiering</br>Tage en sikkerhedskopi manuelt</br>Oprette en brugerdefineret sikkerhedskopiering politik med flere enheder og tidsplaner|StorSimple Manager service → enheder → sikkerhedskopi politikker|[Administrere politikker for sikkerhedskopiering](storsimple-manage-backup-policies.md)|
|Stoppe enhed enheder</br>Genstart enheden enheder</br>Lukke enhed enheder</br>Nulstille enheden til fabriksindstillingerne</br>(Oven er til lokal enhed kun)|StorSimple Manager service → enheder → vedligeholdelse|[Administrere StorSimple enhed controller](storsimple-manage-device-controller.md)|
|Få mere at vide om StorSimple hardwarekomponenter</br>Overvåge hardware status</br>(Oven er til lokal enhed kun)|StorSimple Manager service → enheder → vedligeholdelse|[Overvåge hardwarekomponenter](storsimple-monitor-hardware-status.md)|
|Oprette en supportpakke|StorSimple Manager service → enheder → vedligeholdelse|[Oprette og administrere en supportpakke](storsimple-create-manage-support-package.md)|
|Installere softwareopdateringer|StorSimple Manager service → enheder → vedligeholdelse|[Opdatere din enhed](storsimple-update-device.md)|


##<a name="next-steps"></a>Næste trin
Hvis du oplever problemer med den daglige drift af enheden StorSimple eller med en af de tilhørende hardwarekomponenter, skal referere til:

- [Fejlfinding i forbindelse med en funktionel enhed](storsimple-troubleshoot-operational-device.md)
- [Brug StorSimple overvågning indikator indikatorer](storsimple-monitoring-indicators.md)

Hvis du ikke kan løse problemerne, og du vil oprette en serviceanmodning skal du henvise til [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md).
