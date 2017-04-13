<properties
   pageTitle="StorSimple systemkrav | Microsoft Azure"
   description="I denne artikel beskrives software, netværk, og høj tilgængelighedskrav og bedste praksis for en Microsoft Azure StorSimple løsning."
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
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>StorSimple software, høj tilgængelighed og netværk krav

## <a name="overview"></a>Oversigt

Velkommen til Microsoft Azure StorSimple. I denne artikel beskrives vigtige systemkrav og bedste fremgangsmåder til enheden StorSimple og for lagerplads klienter adgang til enheden. Vi anbefaler, at du gennemser oplysningerne grundigt, før du installerer systemet StorSimple, og derefter henvise til den efter behov under installation og efterfølgende handling.

Systemkravene omfatter:

- **Softwarekrav til lagerplads klienter** - i denne artikel beskrives de understøttede operativsystemer og eventuelle yderligere betingelser for disse operativsystemer.
- **Netværk krav til StorSimple-enhed** – indeholder oplysninger om de porte, der skal være åbne i din firewall til at tillade iSCSI, skyen eller management trafik.
- **Høj tilgængelighedskrav til StorSimple** - beskrives høj tilgængelighed kravene samt de bedste fremgangsmåder til computeren StorSimple enhed og host. 


## <a name="software-requirements-for-storage-clients"></a>Softwarekrav til lagerplads klienter

Der er følgende softwarekrav for lagerplads klienter, få adgang til din StorSimple enhed.

| Understøttede operativsystemer | Version, der kræves | Ekstra krav/noter |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012 OG 2012R2 |StorSimple iSCSI-enheder understøttes til brug på kun følgende Windows disktyper:<ul><li>Simple disk på grundlæggende disk</li><li>Simple og spejlede lydstyrken på dynamisk disk</li></ul>Windows Server 2012 tynd klargøring og ODX funktioner understøttes, hvis du bruger en StorSimple iSCSI lydstyrken.<br><br>StorSimple kan oprette tyndt klargjort og fuldt klargjort enheder. Det kan ikke oprette delvist klargjort enheder.<br><br>Omformatere en tyndt klargjort lydstyrken kan tage lang tid. Vi anbefaler, at slette lydstyrken og derefter oprette en ny i stedet for omformatere. Hvis du stadig foretrækker at omformatere en volumenlicens:<ul><li>Kør følgende kommando før omformatere for at undgå mellemrum videreudnyttelse forsinkelser: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Når formateringen er fuldført, skal du bruge følgende kommando til at genaktivere pladsen:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Anvende Windows Server 2012 programrettelsen, som beskrevet i [KB 2878635](https://support.microsoft.com/kb/2870270) til din computer med Windows Server.</li></ul></li></ul></ul> Hvis du konfigurerer StorSimple øjebliksbillede Manager eller StorSimple Adapter til SharePoint, skal du gå til [Softwarekrav til valgfrie komponenter](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 og 6.0 | Understøttet med VMWare vSphere som iSCSI-klienten. VAAI Bloker funktion understøttes med VMware vSphere på StorSimple enheder.
| Linux RHEL/CentOS | 5, 6 og 7 | Understøttelse af Linux iSCSI-klienter med Åbn iSCSI afsenderen versioner 5, 6 og 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX understøttes ikke i øjeblikket med StorSimple.

## <a name="software-requirements-for-optional-components"></a>Softwarekrav til valgfrie komponenter

Der er følgende softwarekrav til valgfrit StorSimple komponenter (StorSimple af snapshots og StorSimple Adapter til SharePoint).

| Komponent | Host platform | Ekstra krav/noter |
| --------------------------- | ---------------- | ------------- |
| StorSimple øjebliksbillede Manager | Windows Server 2008R2 SP1, 2012 og 2012R2 | Brug af StorSimple øjebliksbillede Manager på Windows Server er påkrævet for sikkerhedskopiering/gendannelse af spejlede dynamiske diske og for en hvilken som helst program-ensartet sikkerhedskopier.<br> StorSimple øjebliksbillede Manager understøttes kun på Windows Server 2008 R2 SP1 (64-bit), Windows 2012 R2 og Windows Server 2012.<ul><li>Hvis du bruger vinduet Server 2012, skal du installere .NET 3.5 – 4.5, før du installerer StorSimple øjebliksbillede Manager.</li><li>Hvis du bruger Windows Server 2008 R2 SP1, skal du installere Windows Management Framework 3.0, før du installerer StorSimple øjebliksbillede Manager.</li></ul> |
| StorSimple Adapter til SharePoint | Windows Server 2008R2 SP1, 2012 og 2012R2 |<ul><li>StorSimple Adapter til SharePoint understøttes kun på SharePoint 2010, og SharePoint 2013.</li><li>RBS kræver SQL Server Enterprise Edition, version 2008 R2 eller 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Netværk krav til enheden StorSimple

Enheden StorSimple er en låst enhed. Men porte skal åbnes i din firewall til at tillade iSCSI, skyen og af administrationstrafik. I følgende tabel vises de porte, der skal åbnes i din firewall. I denne tabel refererer *i* eller *indgående* til den retning, hvorfra indgående klientanmodninger få adgang til din enhed. *Ud* eller *udgående* refererer til den retning, hvori enheden StorSimple sender data eksternt, ud over installationen: eksempelvis udgående til internettet.

| Port Nej<sup>1,2</sup> | Ind eller ud | Port omfang | Påkrævet | Noter |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Ud |  WAN | Nej |<ul><li>Udgående port bruges til at hente opdateringer til internetadgang.</li><li>Den udgående webtjenesteproxy er bruger konfigurerbar.</li><li>Hvis du vil tillade opdateringer, skal denne port også være åben til controller fast IP'er.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Ud | WAN | Ja |<ul><li>Udgående port bruges til at få adgang til data i skyen.</li><li>Den udgående webtjenesteproxy er bruger konfigurerbar.</li><li>Hvis du vil tillade opdateringer, skal denne port også være åben til controller fast IP'er.</li><li>Denne port bruges også i begge enheder for Spildopsamling.</li></ul>|
|UDP 53 (DNS) | Ud | WAN | I nogle tilfælde se noter. |Denne port er påkrævet, kun, hvis du bruger en internetbaserede DNS-server. |
| UDP 123 (NTP) | Ud | WAN | I nogle tilfælde se noter. |Denne port er påkrævet, kun, hvis du bruger en internetbaserede NTP-server. |
| TCP 9354 | Ud | WAN | Ja |Den udgående port bruges af StorSimple enheden til at kommunikere med tjenesten StorSimple Manager. |
| 3260 iSCSI) | I | LAN | Nej | Denne port bruges til at få adgang til data via iSCSI.|
| 5985 | I | LAN | Nej | Indgående port bruges af StorSimple øjebliksbillede Manager til at kommunikere med StorSimple enheden.<br>Denne port bruges også, når du fra en fjernplacering opretter forbindelse til Windows PowerShell til StorSimple via HTTP. |
| 5986 | I | LAN | Nej | Denne port bruges, når du fra en fjernplacering opretter forbindelse til Windows PowerShell til StorSimple via HTTPS. |

<sup>1</sup> ingen indgående porte skal åbnes på det offentlige Internet.

<sup>2</sup> Hvis flere porte udføre en gateway-konfiguration, udgående distribueret trafik rækkefølgen bestemmes baseret på den port routing rækkefølge beskrevet i [Port routing](#routing-metric), nedenfor.

<sup>3</sup> controller fast IP'er på enheden StorSimple skal være distribueret og få forbindelse til internettet. Fast IP-adresser bruges til vedligeholdelse af opdateringer til enheden. Hvis enheden enheder ikke kan oprette forbindelse til internettet via fast IP'er, kan du ikke opdatere enheden StorSimple.

> [AZURE.IMPORTANT] Sørg for, at firewallen ikke redigere eller dekryptere en hvilken som helst SSL-trafik mellem Azure og StorSimple enhed.

### <a name="url-patterns-for-firewall-rules"></a>URL-adressen mønstre for firewallregler

Netværksadministratorer kan ofte konfigurere avancerede firewallregler baseret på URL-adressen mønstrene for at filtrere det indgående og udgående trafik. Enheden StorSimple og StorSimple Manager-tjenesten er afhængige af andre Microsoft-programmer som Azure Service Bus, Azure Active Directory-adgangskontrol, lagerplads konti og Microsoft Update-servere. De URL-adresse mønstre, der er knyttet til disse programmer kan bruges til at konfigurere firewallregler. Det er vigtigt at forstå, at de URL-adresse mønstre, der er knyttet til disse programmer kan ændre. Dette kræver tur. netværksadministratoren til at overvåge og opdatere firewallregler for din StorSimple som, og når det er nødvendigt.

Vi anbefaler, at du angiver din firewallregler for udgående trafik, baseret på StorSimple fast IP-adresser, liberally i de fleste tilfælde. Du kan dog Brug oplysningerne nedenfor til at angive avancerede firewallregler, der skal bruges til at oprette sikker miljøer.

> [AZURE.NOTE] Enheden (kilde) IP'er altid skal du vælge alle aktiveret netværksgrænseflader. Destinationen IP'er skal du vælge [Azure datacenter IP-intervaller](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>URL-adressen mønstre for Azure-portal
| URL-adressen mønster                                                      | Komponent/funktionalitet                                           | Enhed IP'er                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Tjenesten StorSimple Manager<br>Access Control Service<br>Azure Service Bus| Cloud-aktiverede netværksgrænseflader        |
|`https://*.backup.windowsazure.com`|Enhed registrering| Kun DATA 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Tilbagekaldte |Cloud-aktiverede netværksgrænseflader |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure-lager konti og overvågning | Cloud-aktiverede netværksgrænseflader        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update-servere<br>                             | Controller fast IP'er kun               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controller fast IP'er kun   |
| `https://*.partners.extranet.microsoft.com/*`                    | Support-pakken                                                  | Cloud-aktiverede netværksgrænseflader        |

#### <a name="url-patterns-for-azure-government-portal"></a>URL-adressen mønstre til Azure Government portal
| URL-adressen mønster                                                      | Komponent/funktionalitet                                           | Enhed IP'er                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Tjenesten StorSimple Manager<br>Access Control Service<br>Azure Service Bus| Cloud-aktiverede netværksgrænseflader        |
|`https://*.backup.windowsazure.us`|Enhed registrering| Kun DATA 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Tilbagekaldte |Cloud-aktiverede netværksgrænseflader |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Azure-lager konti og overvågning | Cloud-aktiverede netværksgrænseflader        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Microsoft Update-servere<br>                             | Controller fast IP'er kun               |
| `http://*.deploy.akamaitechnologies.com`                         |Akamai CDN |Controller fast IP'er kun   |
| `https://*.partners.extranet.microsoft.com/*`                    | Support-pakken                                                  | Cloud-aktiverede netværksgrænseflader        |

### <a name="routing-metric"></a>Routing metrisk

En routing metrikværdi er knyttet til grænsefladerne og gateways, distribuere dataene til de angivne netværk. Routing metrisk bruges af routing-protokollen til at beregne den bedste sti til en given destination, hvis det lærer forskellige stier findes til samme destination. Nederst routing-metrik, jo højere prioritet.

Hvis flere netværksgrænseflader og gateways er konfigureret til at kanal trafik kommer routing målene i konteksten af StorSimple ind Afspil for at bestemme den relative rækkefølge, hvori grænsefladerne få anvendes. Routing målepunkter kan ikke ændres af brugeren. Du kan bruge den `Get-HcsRoutingTable` til at udskrive routing tabel (og målepunkter) på enheden StorSimple. Yderligere oplysninger om Get-HcsRoutingTable cmdlet i [foretage fejlfinding af StorSimple installation](storsimple-troubleshoot-deployment.md).

De routing metriske algoritmer er forskellige afhængigt af versionerne software, der kører på enheden StorSimple.

**Versioner før opdatering 1**

Dette omfatter softwareversioner før opdatering 1 som GA, 0,1, 0,2 eller 0,3 version. Den rækkefølge, der er baseret på routing målepunkter er som følger:

   *Sidste konfigureret 10 GbE netværk interface > andre 10 GbE netværk interface > sidste konfigureret 1 GbE netværk interface > andre 1 GbE netværksgrænseflade*


**Versioner begyndende fra opdatering 1 og før opdatering 2**

Dette omfatter softwareversioner som 1, 1.1 eller 1.2. Den rækkefølge, der er baseret på routing målepunkter er besluttet på følgende måde:

   *DATA 0 > sidste konfigureret 10 GbE netværk interface > andre 10 GbE netværk interface > sidste konfigureret 1 GbE netværk interface > andre 1 GbE netværksgrænseflade*

   I opdatering 1 routing metrisk data 0 er gjort den laveste; derfor dirigeres alle cloud-trafikken gennem DATA 0. Gøre en note i denne, hvis der er mere end én skyen aktiverede netværksgrænseflade på enheden StorSimple.


**Versioner begyndende fra opdatering 2**

Opdatering 2 har flere netværk-relaterede forbedringer og routing målepunkter er ændret. Funktionsmåden kan forklares på følgende måde.

- Et sæt af foruddefinerede værdier, der er tildelt til netværksgrænseflader.   

- Overvej et eksempel på en tabel med værdier, der er tildelt til forskellige netværksgrænseflader, når de er cloud-aktiveret eller skyen deaktiveret, men med en konfigureret gateway er vist nedenfor. Bemærk de værdier, der er tildelt her er kun eksempler på værdier.


  	| Netværksgrænsefladen | Cloud aktiveret | Cloud-deaktiveret med gateway |
  	|-----|---------------|---------------------------|
  	| Data 0  | 1            | -                        |
  	| Data 1  | 2            | 20                       |
  	| Data 2  | 3            | 30                       |
  	| Data 3  | 4            | 40                       |
  	| Data 4  | 5            | 50                       |
  	| Data 5  | 6            | 60                       |


- Den rækkefølge, hvori skyen trafik sendes via netværksgrænseflader er:

    *Data 0 > Data 1 > dato 2 > Data 3 > Data 4 > Data 5*

    Dette kan forklares ved i følgende eksempel.

    Overvej en StorSimple enhed med to skyen aktiverede netværksgrænseflader, Data 0 og Data 5. Data 1 til 4 Data er skyen deaktiveret, men har en konfigureret gateway. Den rækkefølge, hvori sendes trafik for denne enhed bliver:

    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*

    *tal i parentes angiver, hvor de respektive routing målepunkter.*

    Hvis Data 0 mislykkes, bliver skyen trafikken få sendt til Data 5. Da der en gateway er konfigureret på andre netværk, hvis både Data 0 og Data 5 skulle mislykkes, gennemgår skyen trafikken rapportdata 1.


- Hvis en cloud-aktiverede netværkskort mislykkes, er 3 nye forsøg med en 30 anden forsinkelse til at oprette forbindelse til brugergrænsefladen. Hvis alle gentagelserne mislykkes, sendes trafikken til den næste tilgængelige cloud-aktiverede grænseflade ifølge tabellen routing. Hvis alle skyen aktiverede netværket grænseflader fejl, mislykkes enheden til anden controlleren (ingen genstart i dette tilfælde).

- Hvis der er en VIP fejl for en iSCSI-aktiverede netværksgrænseflade, vil der være 3 nye forsøg med en forsinkelse i to sekunder. Dette problem har opholdt sig den samme fra tidligere versioner. Hvis alle iSCSI netværksgrænseflader mislykkes, skal en controller failover forekomme (vedlagt genstart).


- En besked opstår også på enheden StorSimple, når der er en VIP fejl. Gå til [beskeder om oversigtsvejledning](storsimple-manage-alerts.md)kan finde flere oplysninger.

- Med hensyn til forsøg, vil iSCSI tilsidesætter skyen.

    Overvej følgende eksempel: A StorSimple enhed har to netværksgrænseflader aktiveret, Data 0 og 1 Data. Data 0 er cloud-aktiverede rapportdata 1 er begge skyen og iSCSI-aktiverede. Ingen andre netværksgrænseflader på denne enhed er aktiveret til skyen eller iSCSI.

    Hvis rapportdata 1 opstår, givet det er grænsefladen for det sidste iSCSI-netværk, kan det resultere i en controller failover rapportdata 1 på anden controlleren.


### <a name="networking-best-practices"></a>Netværk bedste fremgangsmåder

Ud over ovenstående netværk krav til optimal ydeevne for din StorSimple løsning, skal du overholde følgende anbefalede fremgangsmåder:

- Sørg for, at enheden StorSimple har en dedikeret 40 Mbps båndbredde (eller mere) tilgængelig til enhver tid. Denne båndbredde skal ikke deles (eller allokering bør sikres ved hjælp af QoS-politikker) med andre programmer.

- Kontroller netværksforbindelsen til internettet er tilgængelig til enhver tid. Sporadisk eller upålidelig internetforbindelser til de enheder, herunder uden Internetforbindelse, der er nogen art, medfører en ikke-understøttede konfiguration.

- Isolere iSCSI og skyen trafik ved har dedikeret netværksgrænseflader på din enhed for adgang til iSCSI og skyen. Finde flere oplysninger, hvordan du kan [ændre netværksgrænseflader](storsimple-modify-device-config.md#modify-network-interfaces) på enheden StorSimple.

- Brug ikke en Link sammenlægning Control Protocol (LACP) konfiguration til dit netværksgrænseflader. Dette er en ikke-understøttede konfiguration.


## <a name="high-availability-requirements-for-storsimple"></a>Høj tilgængelighedskrav til StorSimple

Hardwareplatformen, der følger med løsningen StorSimple har tilgængelighed og pålidelighed funktioner, som giver en foundation til en høj tilgængelighed, fejlsikret storage infrastruktur i dit datacenter. Der er dog krav og bedste fremgangsmåder, du skal overholde for at sikre tilgængeligheden af din StorSimple løsning. Før du installerer StorSimple, gennemgå omhyggeligt følgende krav og bedste fremgangsmåder til StorSimple enhed og tilsluttede værtscomputere.

Gå til [Brug tjenesten StorSimple Manager til at overvåge hardwarekomponenter og status](storsimple-monitor-hardware-status.md) og [StorSimple hardware-komponentudskiftning](storsimple-hardware-component-replacement.md)kan finde flere oplysninger om overvåge og vedligeholde hardwarekomponenterne i enheden StorSimple.

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Høj tilgængelighedskrav og procedurer for enheden StorSimple

Gennemgå følgende oplysninger grundigt for at sikre høj tilgængeligheden af enheden StorSimple.

#### <a name="pcms"></a>PCMs

StorSimple enheder omfatter overflødige, hot-swap power og køling moduler (PCMs). Hver PCM har tilstrækkelig kapacitet til at levere tjenesten til hele kabinettet. For at sikre høj tilgængelighed, skal være installeret begge PCMs.

- Forbind din PCMs til forskellige power kilder til at levere tilgængelighed, hvis en power kilde mislykkes.
- Hvis en PCM mislykkes, skal du anmode om en erstatning med det samme.
- Fjerne en mislykkedes PCM kun, når du har erstatning og er klar til at installere den.
- Fjern ikke begge PCMs samtidigt. Modulet PCM indeholder ekstra batterimodul. Fjerne begge af PCMs medfører en lukning uden batteri beskyttelse, og vil ikke blive gemt enhedens tilstand. Gå til [Bevar ekstra batterimodul](storsimple-battery-replacement.md#maintain-the-backup-battery-module)kan finde flere oplysninger om batteriet.

#### <a name="controller-modules"></a>Controller moduler

StorSimple enheder omfatter overflødige, hot-swap controller moduler. Controller moduler fungerer på en aktiv/passiv måde. Til enhver tid, ét controller modul er aktive og leverer service, mens andre controller modulet er passive. Modulet passive controller er tændt og bliver funktionsdygtige, hvis det aktive controller modul mislykkes eller er fjernet. Hver controller modulet har tilstrækkelig kapacitet til at levere tjenesten til hele kabinettet. Begge controller moduler skal være installeret for at sikre høj tilgængelighed.

- Sørg for, at begge controller moduler er installeret til enhver tid.

- Hvis et controller modul mislykkes, skal du anmode om en erstatning med det samme.

- Fjerne et mislykkedes controller modul, kun, når du har erstatning og er klar til at installere den. Fjerne et modul til udvidede perioder påvirker ventilation og dermed køling af systemet.

- Sørg for, at netværksforbindelser til begge controller moduler er identiske, og de forbundne netværksgrænseflader har en identisk netværkskonfiguration.

- Hvis et controller modul mislykkes eller skal erstatning, skal du kontrollere, at andre controller modulet er i tilstanden aktiv før du erstatter modulet mislykkedes controller. For at bekræfte, at en controller er aktiv, skal du gå til [identificere den aktive controller på din enhed](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Fjern ikke begge controller moduler på samme tid. Hvis en controller failover er i gang, ikke lukke modulet standby controller eller fjerne det fra kabinettet.

- Vent mindst fem minutter, før du fjerner enten controller modul efter en controller failover.

#### <a name="network-interfaces"></a>Netværksgrænseflader

StorSimple enhed controller moduler hver har fire 1 Gigabit og to 10 Gigabit Ethernet-netværksgrænseflader.

- Sørg for, at netværksforbindelserne til begge controller moduler er identiske, og netværket grænseflader, der er forbundet controller modul grænseflader for at få en identisk netværkskonfiguration.

- Når det er muligt, kan du installere netværksforbindelser på tværs af forskellige parametre til at sikre tjenesten kører ved netværk enhedsfejl.

- Når at koble den eneste eller den sidste resterende iSCSI-aktiverede grænseflade (med IP'er tildelt), kan du deaktivere grænsefladen først og derefter fjerne kablerne. Hvis grænsefladen er frakoblet først, kan det medføre den aktive controller overføres til den passive controller. Hvis den passive controller har også dens tilsvarende grænseflader, der er ikke tilsluttet, derefter genstartes begge enheder flere gange inden du sætter dig på en controller.

- Forbind mindst to datagrænseflader til netværket fra hver controller modul.

- Hvis du har aktiveret to 10 GbE grænseflader, installere dem på tværs af forskellige parametre.

- Når det er muligt, kan du bruge MPIO på servere for at sikre, at serverne kan acceptere et hyperlink, et netværk eller en fejl i brugergrænseflade.

Du kan finde flere oplysninger om netværk enheden til høj tilgængelighed og ydeevne, skal du gå til [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs og harddiske

StorSimple enheder omfatter dækkende tilstand diske (SSDs) og harddiske (harddiske), der er beskyttet via spejlvendt mellemrum. Brug af spejlede mellemrum sikrer, at enheden er kunne acceptere fejl på en eller flere SSDs eller harddiske.

- Sørg for, at alle SSD og harddisk moduler er installeret.

- Hvis en SSD eller harddisk mislykkes, skal du anmode om en erstatning med det samme.

- Hvis en SSD eller harddisk mislykkes eller skal udskiftes, skal du kontrollere, at du fjerner kun SSD eller harddisk, der kræver erstatning.

- Fjern ikke mere end én SSD eller harddisk fra systemet når som helst tidspunkt.
En fejl i forbindelse med 2 eller flere diske af bestemt type (harddisk, SSD) eller på hinanden følgende fejl i en kort klokkeslætsformat ramme kan medføre system fejlfunktion og potentielle datatab. Hvis dette sker, skal [du kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at få hjælp.

- I stedet, overvåge **Hardware Status** på siden **vedligeholdelse** for drev i SSDs og harddiske. Grøn markering status angiver, at diskene er sund eller OK, mens et rødt udråbstegn Peg angiver en mislykkedes SSD eller harddisk.

- Vi anbefaler, at du konfigurerer skyen snapshots for alle enheder, du har brug for at beskytte i tilfælde af systemfejl.

#### <a name="ebod-enclosure"></a>EBOD omslutning

StorSimple enhedsmodel 8600 omfatter en udvidet gruppe af diske (EBOD) omslutning ud over den primære omslutning. En EBOD indeholder EBOD enheder og harddiske (harddiske), der er beskyttet via spejlvendt mellemrum. Brug af spejlede mellemrum sikrer, at enheden er kunne acceptere fejl i en eller flere harddiske. EBOD omslutning er forbundet til den primære omslutning gennem overflødige SAS kabler.

- Sørg for, at begge EBOD omslutning controller moduler, både SAS kabler og alle harddiskdrevene er installeret til enhver tid.

- Hvis et EBOD omslutning controller modul mislykkes, skal du anmode om en erstatning med det samme.

- Hvis et EBOD omslutning controller modul mislykkes, skal du kontrollere, at andre controller modulet er aktiv, før du erstatter det fejlbehæftede modul. For at bekræfte, at en controller er aktiv, skal du gå til [identificere den aktive controller på din enhed](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Overvåge status for komponenten i tjenesten StorSimple Manager ved at åbne **vedligeholdelse**under en EBOD controller modul erstatning, løbende > **Hardware status**.

- Hvis et SAS kabel mislykkes eller skal udskiftes (Microsoft Support skal være nødvendigt for at gøre disse bestemmelse), skal du kontrollere, at du fjerner kun det SAS kabel, der skal udskiftes.

- Samtidig Fjern ikke begge SAS kabler fra systemet når som helst tidspunkt.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Høj tilgængelighed anbefalinger til host computere

Gennemgå omhyggeligt disse bedste fremgangsmåder for at sikre høj tilgængeligheden af værter har forbindelse til din StorSimple enhed.

- Konfigurere StorSimple med [to noder fil server klyngekonfigurationer][1]. Ved at fjerne enkelt points failure og opbygning af i redundans i host side, bliver hele løsningen meget tilgængelig.

- Brug konstant er tilgængelig (CA) deler tilgængelig med Windows Server 2012 (små og mellemstore virksomheder 3.0) for høj tilgængelighed under failover over lagerplads enheder. Se denne [videodemonstration](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)for yderligere oplysninger om konfiguration af fil serverklynger og konstant er tilgængelig aktierne med Windows Server 2012.

## <a name="next-steps"></a>Næste trin

- [Få mere at vide om begrænsninger for StorSimple system](storsimple-limits.md).
- [Lær, hvordan du installerer StorSimple-løsning](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
