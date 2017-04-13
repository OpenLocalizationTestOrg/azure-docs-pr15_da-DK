<properties
   pageTitle="StorSimple virtuelle matrix systemkrav"
   description="Få mere at vide om software og netværk krav til din StorSimple virtuelle matrix"
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
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>StorSimple virtuelle matrix systemkrav

## <a name="overview"></a>Oversigt

I denne artikel beskrives vigtige systemkrav til Microsoft Azure StorSimple virtuelle systemet (også kaldet StorSimple lokale virtuel enhed eller StorSimple virtuel enhed) og for at få adgang til matrixen lagerplads klienter. Vi anbefaler, at du gennemser oplysningerne grundigt, før du installerer systemet StorSimple, og derefter henvise til den efter behov under installation og efterfølgende handling.

Systemkravene omfatter:

-   **Softwarekrav til lagerplads klienter** - i denne artikel beskrives de understøttede virtualization platforme, webbrowsere, iSCSI-initiatorer, små og mellemstore virksomheder klienter, mindste virtuel enhed krav og eventuelle yderligere betingelser for disse operativsystemer.

-   **Netværk krav til StorSimple-enhed** – indeholder oplysninger om de porte, der skal være åbne i din firewall til at tillade iSCSI, skyen eller management trafik.

Den StorSimple oplysninger om systemkrav udgivet i denne artikel gælder kun for StorSimple virtuelle matrixer.

- Gå til [systemkravene til enheden StorSimple 8000 serie](storsimple-system-requirements.md)for 8000 serie enheder.
 
- Gå til [systemkravene til enheden StorSimple 5000-7000 serie](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements)for 7000 serie enheder.


## <a name="software-requirements"></a>Softwarekrav

Softwarekrav Medtag oplysninger om de understøttede webbrowsere, små og mellemstore virksomheder-versioner, virtualization platforme og mindste virtuel enhed krav.

### <a name="supported-virtualization-platforms"></a>Understøttede virtualization platforme


| **Hypervisor** | **Version**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 eller nyere versioner |
| VMware ESXi    | 5,5 og nyere                        |

### <a name="virtual-device-requirements"></a>Virtuel enhed krav

| **Komponent**                                | **Krav**            |
|----------------------------------------------|----------------------------|
| Mindste antal virtuelle processorer (kerner) | 4                          |
| Mindste hukommelse (RAM)                         | 8 GB                       |
| Disken mellemrum<sup>1</sup>                       | OS disk - 80 GB <br></br>Datadisk - 500 GB til 8 TB|
| Mindste antal netværk-grænseflader       | 1                          |
| Mindste internetbåndbredde<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> – tyk klargjort

<sup>2</sup> – netværkskrav kan variere efter den daglige data Skift rente. Eksempelvis hvis en enhed skal bruge til at sikkerhedskopiere 10 GB eller flere ændringer under en dag, kan derefter den daglige sikkerhedskopiering over en 5 Mbps forbindelse tage op til 4.25 timer (Hvis dataene ikke kunne komprimeres eller deduplicated).

### <a name="supported-web-browsers"></a>Understøttede webbrowsere

| **Komponent**     | **Version** | **Ekstra krav/noter** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Seneste version  |                                   |
| Internet Explorer | Seneste version  | Testet med Internet Explorer 11  |
| Google Chrome     | Seneste version  | Testet med Chrome 46             |

### <a name="supported-storage-clients"></a>Understøttede lagerplads klienter 

Der er følgende softwarekrav til iSCSI-initiatorer, få adgang til din StorSimple virtuelle matrix (konfigureret som en iSCSI-server).

| **Understøttede operativsystemer** | **Version, der kræves** | **Ekstra krav/noter** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012 OG 2012R2 |StorSimple kan oprette tyndt klargjort og fuldt klargjort enheder. Det kan ikke oprette delvist klargjort enheder. StorSimple iSCSI-enheder understøttes kun: <ul><li>Simple enheder på en Windows grundlæggende disk.</li><li>Windows NTFS til formatering af en volumenlicens.</li>|


Der findes følgende softwarekrav for små og mellemstore virksomheder klienter, få adgang til din StorSimple virtuelle matrix (konfigureret som en filserver).

| **Små og mellemstore virksomheder Version** |
|-------------|
| Små og mellemstore virksomheder 2.x     |
| SMÅ OG MELLEMSTORE VIRKSOMHEDER, 3.0     |
| SMÅ OG MELLEMSTORE VIRKSOMHEDER 3.02    |

> [AZURE.IMPORTANT] Undlad at kopiere eller gemme filer, der er beskyttet af Windows System (ENCRYPTING File) til filserver StorSimple virtuelle matrix; Dette kan medføre en ikke-understøttede konfiguration. 

## <a name="networking-requirements"></a>Krav til netværk 

I følgende tabel vises de porte, der skal åbnes i din firewall til at tillade iSCSI, små og mellemstore virksomheder, skyen eller management trafik. I denne tabel refererer *i* eller *indgående* til den retning, hvorfra indgående klientanmodninger få adgang til din enhed. *Ud* eller *udgående* refererer til den retning, hvori enheden StorSimple sender data eksternt, ud over installationen: eksempelvis udgående til internettet.

| **Port nummer<sup>1</sup>** | **Ind eller ud** | **Port omfang** | **Påkrævet**              | **Noter**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Ud           | WAN            | Nej                        | Udgående port bruges til at hente opdateringer til internetadgang. <br></br>Den udgående webtjenesteproxy er bruger konfigurerbar. |
| TCP 443 (HTTPS)          | Ud           | WAN            | Ja                       | Udgående port bruges til at få adgang til data i skyen. <br></br>Den udgående webtjenesteproxy er bruger konfigurerbar. |
| UDP 53 (DNS)             | Ud           | WAN            | I nogle tilfælde se noter. | Denne port er påkrævet, kun, hvis du bruger en internetbaserede DNS-server. <br></br> **Bemærk**: Hvis du anvender en filserver, anbefaler vi bruger lokale DNS-server.|
| UDP 123 (NTP)            | Ud           | WAN            | I nogle tilfælde se noter. | Denne port er påkrævet, kun, hvis du bruger en internetbaserede NTP-server.<br></br> **Note:** Hvis du anvender en filserver, anbefaler vi synkroniseringen tid med din Active Directory-domænecontrollere.  |
| TCP 80 (HTTP)           | I            | LAN            | Ja                       | Dette er den indgående port for lokale Brugergrænsefladen på StorSimple enheden lokal administration. <br></br> **Bemærk**: få adgang til lokale Brugergrænsefladen via HTTP omdirigerer automatisk til HTTPS.|
| TCP 443 (HTTPS)          | I            | LAN            | Ja                       | Dette er den indgående port for lokale Brugergrænsefladen på StorSimple enheden lokal administration.|
| TCP 3260 iSCSI)         | I            | LAN            | Nej                        | Denne port bruges til at få adgang til data via iSCSI.|

<sup>1</sup> ingen indgående porte skal åbnes på det offentlige Internet.

> [AZURE.IMPORTANT] Sørg for, at firewallen ikke redigere eller dekryptere en hvilken som helst SSL-trafik mellem Azure og StorSimple enhed.


### <a name="url-patterns-for-firewall-rules"></a>URL-adressen mønstre for firewallregler 

Netværksadministratorer kan ofte konfigurere avancerede firewallregler baseret på URL-adressen mønstrene for at filtrere det indgående og udgående trafik. Din virtuelle matrix og tjenesten StorSimple Manager afhænger af andre Microsoft-programmer som Azure Service Bus, Azure Active Directory-adgangskontrol, lagerplads konti og Microsoft Update-servere. De URL-adresse mønstre, der er knyttet til disse programmer kan bruges til at konfigurere firewallregler. Det er vigtigt at forstå, at de URL-adresse mønstre, der er knyttet til disse programmer kan ændre. Dette kræver tur. netværksadministratoren til at overvåge og opdatere firewallregler for din StorSimple som, og når det er nødvendigt. 

Vi anbefaler, at du angiver din firewallregler for udgående trafik, baseret på StorSimple fast IP-adresser, liberally i de fleste tilfælde. Du kan dog Brug oplysningerne nedenfor til at angive avancerede firewallregler, der skal bruges til at oprette sikker miljøer.

> [AZURE.NOTE] 
> 
> - Altid skal du vælge enheden (kilde) IP'er til skyen aktiverede netværksgrænseflader. 
> - Destinationen IP'er skal du vælge [Azure datacenter IP-intervaller](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| URL-adressen mønster                                                      | Komponent/funktionalitet                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Tjenesten StorSimple Manager<br>Access Control Service<br>Azure Service Bus|
|`http://*.backup.windowsazure.com`|Enhed registrering|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Tilbagekaldte |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure-lager konti og overvågning |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update-servere<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*`                    | Support-pakken                                                  |
| `http://*.data.microsoft.com `                   | Telemetri-tjenesten i Windows, under [opdatering til kundeoplevelsen og diagnosticering telemetri](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Næste trin

-   [Forberede dig på portalen for at installere din StorSimple virtuelle matrix](storsimple-ova-deploy1-portal-prep.md)
