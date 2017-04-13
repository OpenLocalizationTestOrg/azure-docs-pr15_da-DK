<properties 
   pageTitle="Ændre konfigurationen af StorSimple enheden | Microsoft Azure" 
   description="Beskriver, hvordan du bruger tjenesten StorSimple Manager til at omkonfigurere en StorSimple enhed, der allerede er installeret." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Brug tjenesten StorSimple Manager til at ændre konfigurationen af StorSimple enhed

## <a name="overview"></a>Oversigt 

Azure klassisk portalen **Konfigurer** siden indeholder alle de enhedsparametre, du kan konfigurere på en enhed med StorSimple, der administreres af en StorSimple Manager-tjeneste. Dette selvstudium beskriver, hvordan du kan bruge siden **Konfigurer** til at udføre følgende opgaver i niveau for enheden:

- Ændre indstillinger for lydenhed 
- Ændre tidsindstillinger 
- Ændre DNS-indstillinger 
- Ændre netværksgrænseflader
- Ombyt eller gentildele IP'er

## <a name="modify-device-settings"></a>Ændre indstillinger for lydenhed

Enhedsindstillingerne medtage det fulde navn på enheden og beskrivelsen af enhed.

En StorSimple-enhed, der er forbundet med tjenesten StorSimple Manager er tildelt et standardnavn. Standardnavnet afspejler normalt serienummeret for enheden. For eksempel angiver navnet på en enhed, der er 15 tegn, som 8600-SHX0991003G44HT følgende:

- **8600** – angiver enhedsmodel.
- **SHX** – angiver webstedet produktionsanlæg.
- **0991003** - angiver et bestemt produkt.
- **G44HT**- de sidste 5 cifre forøges for at oprette entydige serienumre. Det kan være et sekventielt sæt ikke.

Du kan bruge portalen Azure klassisk ændre enhedens navn og tildele den et entydigt navn efter eget valg. Det fulde navn kan indeholde tegn og kan være op til 64 tegn.

Du kan også angive en beskrivelse af enhed. En enhed beskrivelse er som regel hjælper med at identificere ejeren og den fysiske placering af enheden. Beskrivelsesfeltet skal indeholde færre end 256 tegn.
 
## <a name="modify-time-settings"></a>Ændre tidsindstillinger

Din enhed skal synkronisere tid for at godkende med udbyderen skyen lagerplads. Vælg din tidszone på rullelisten, og Angiv op til to netværk tid Protocol (NTP)-servere. Den primære NTP-server er påkrævet og er angivet, når du bruger Windows PowerShell til StorSimple til at konfigurere din enhed. Du kan angive standard Windows Server **time.windows.com** som NTP-serveren. Du kan få vist den primære NTP serverkonfiguration via Azure klassisk administrationsportalen, men du skal bruge grænsefladen Windows PowerShell til at ændre den.

Den sekundære NTP serverkonfiguration er valgfrit. Du kan bruge portalen klassisk til at konfigurere en sekundær NTP-server. 

Når du konfigurerer NTP-serveren, kan du sikre dig, at dit netværk tillader NTP trafikken til at overføre fra dit datacenter til internettet. Når du angiver en offentlig NTP-server, skal du sikre dig, at dit netværksfirewalls og andre sikkerhedsenheder, er konfigureret til at tillade NTP-trafik til og fra det eksterne netværk. Hvis tovejs NTP-trafik ikke er tilladt, skal du bruge en intern NTP-server (en Windows-domænecontrolleren giver denne funktion). Hvis enheden ikke kan synkronisere tid, kan det muligvis ikke kommunikere med udbyderen skyen lagerplads.

For at se en liste over offentlige NTP-servere skal du gå til [NTP-servere Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Hvad sker der, hvis enheden er implementeret i en anden tidszone?

Hvis enheden er installeret i en anden tidszone, ændres tidszone enhed. Da der alle sikkerhedskopiering politikker bruger en enhed tidszone, justerer automatisk sikkerhedskopiering politikker i overensstemmelse med den nye tidszone. Ingen brugerhandlinger er påkrævet.

## <a name="modify-dns-settings"></a>Ændre DNS-indstillinger

En DNS-server bruges, når enheden forsøger at kommunikere med udbyderen skyen lagerplads. For høj tilgængelighed skal du konfigurere både primært og sekundære DNS-servere under den indledende enhed installation. Hvis du vil omkonfigurere den primære DNS-server, skal du bruge Windows PowerShell-grænseflade på enheden StorSimple.

Hvis du vil ændre den sekundære DNS-server, kan du bruge Azure klassisk portal.



## <a name="modify-network-interfaces"></a>Ændre netværksgrænseflader

Din enhed har seks enhed netværksgrænseflader, hvoraf fire er 1 GbE og to hvoraf er 10 GbE. Disse grænseflader er mærket som DATA 0 – DATA 5. DATA 0, RAPPORTDATA 1, DATA 4 og 5 DATA er 1 GbE, mens DATA 2 og 3 DATA er 10 GbE netværksgrænseflader.

Konfigurere **Netværksindstillinger grænseflade** for hver af grænsefladerne, der skal bruges. For at sikre høj tilgængelighed, anbefaler vi, at du har mindst to iSCSI-grænseflader og to cloud-aktiverede brugergrænseflader på din enhed. Vi anbefaler, men kræver ikke, at ubrugt grænseflader være deaktiveret.

Når du konfigurerer nogen af netværk grænsefladerne, skal du konfigurere virtuelle IP-adresse (VIP).

DATA 0 er skyen aktiveret som standard. Når du konfigurerer DATA 0, behøver du også konfigurere to fast IP-adresser, en for hver controller. Disse fast IP-adresser kan bruges til at få direkte adgang til enhed enheder og er praktiske, når du installerer opdateringer på enheden, eller når du får adgang til enheder med henblik på fejlfinding.

I StorSimple 8000 serie opdatering 1 routing metrisk data 0 er indstillet til den laveste; derfor, hvis din enhed kører StorSimple 8000 serie opdatering 1, dirigeres al skyen trafik gennem DATA 0. Gør en note af dette, hvis du har mere end én skyen aktiverede netværksgrænseflade på enheden StorSimple.

>[AZURE.NOTE] Fast IP-adresserne til controller bruges til vedligeholdelse af opdateringer til enheden. Fast IP'er skal derfor distribueret og få forbindelse til internettet.

For hver netværksgrænseflade vises følgende parametre:

- **Hastighed** – ikke en bruger kan konfigureres parameter. DATA 0, RAPPORTDATA 1, DATA 4 og 5 DATA er altid 1 GbE, mens DATA 2 og 3 DATA er 10 GbE grænseflader.

     >[AZURE.NOTE] Hastighed og duplex er altid automatisk-forhandling. Store rammer understøttes ikke.
 
- **Interface tilstand** – en grænseflade kan aktiveres eller deaktiveres. Hvis det er aktiveret, vil enheden forsøge at bruge brugergrænsefladen. Vi anbefaler, at kun disse grænseflader, der er tilsluttet netværket og bruges er aktiveret. Deaktivere en hvilken som helst grænseflader, som du ikke bruger.

- **Brugergrænseflade type** – denne parameter gør det muligt at isolere iSCSI-trafik fra cloud storagetrafik. Denne parameter kan være et af følgende:

    - **Skyen aktiveret** – når aktiveret, enheden skal bruge denne grænseflade til at kommunikere med skyen.
    - **iSCSI aktiveret** – når aktiveret, enheden anvender denne grænseflade til at kommunikere med iSCSI-vært.

    Vi anbefaler, at du isolere iSCSI-trafik fra cloud storagetrafik. Bemærk også, hvis din vært er placeret i det samme undernet som din enhed, ikke behøver du at tildele en gateway men hvis din vært er i et andet undernet end din enhed, du skal tildeles en gateway.

- **IP-adresse** – det kan være IPv4 eller IPv6 eller begge dele. Både IPv4 og IPv6-adresse familier understøttes for enhed netværksgrænseflader. Når du bruger IPv4, kan du angive en 32-bit IP-adresse (*xxx.xxx.xxx.xxx*) i prik decimal notation. Når du bruger IPv6, du skal blot angive et 4-cifret præfiks, og en 128-bit adresse oprettes automatisk til din enhed netværk-grænseflade baseret på præfikset.

- **Undernet** – dette refererer til undernetmasken og er konfigureret via Windows PowerShell-brugergrænsefladen.

- **Gateway** -dette er den standardgateway, der skal bruges ved denne grænseflade, når den forsøger at kommunikere med noder, der ikke er i samme IP-adresseområder (undernet). Standardgatewayen skal være i det samme adresseområde (undernet) som grænsefladen IP-adresse, som fastsat ved undernetmasken.

- **Fast IP-adresse** – dette felt er tilgængelig, kun mens du konfigurerer DATA 0 interface. For handlinger som opdateringer eller foretage fejlfinding af enheden, du muligvis direkte adgang til enhed controlleren. Fast IP-adressen kan bruges til at få adgang til både aktiv og passiv domænecontrolleren på din enhed.

Du kan omkonfigurere Controller 0 og 1 Controller via Azure klassisk portalen.

>[AZURE.NOTE] 
>
>- Bekræft for at sikre kan fungere korrekt, interface hastighed og dupleks på Skift, hver enhedsgrænseflade er forbundet til. Skift grænseflader skal enten forhandler med eller konfigureres Gigabit Ethernet (1000 Mbps) og være fuld dupleks. Grænseflader operativsystem ved lavere hastigheder eller i halv dupleks kan medføre problemer med ydeevnen.
>
>- For at minimere afbrydelser og nedetid, anbefaler vi, at du aktiverer portfast på hver af de Skift porte, grænsefladen iSCSI netværk på din enhed skal oprette forbindelse til. Dette sikrer, at netværksforbindelsen kan oprettes hurtigt i tilfælde af en failover.
 
## <a name="swap-or-reassign-ips"></a>Ombyt eller gentildele IP'er

I øjeblikket, hvis alle netværk grænseflader på controlleren er tildelt en VIP, der er i brug (ved den samme enhed eller en anden enhed i netværket), mislykkes derefter controlleren. Derfor, du skal følge stort fremgangsmåden, hvis du Skift VIPs for grænsefladen enhed netværk, fordi du vil oprette en dublerede IP-situation.

Udfør følgende trin for at udskifte eller tildele VIPs på grund af netværksgrænseflader:

#### <a name="to-reassign-ips"></a>Tildele igen IP'er

1. Fjern markeringen i begge grænseflader IP-adresse.

2. Når IP-adresser er fjernet, kan du tildele de nye IP-adresser til de respektive grænseflader.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [konfigurerer MPIO for enheden StorSimple](storsimple-configure-mpio-windows-server.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
     
