<properties 
   pageTitle="Bedste fremgangsmåder for StorSimple virtuelle matrix | Microsoft Azure"
   description="Beskriver de bedste fremgangsmåder til implementering og administration af StorSimple virtuelle matrixen."
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
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Bedste fremgangsmåder for StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt

Microsoft Azure StorSimple virtuelle matrix er en integreret storageløsning, der administrerer lager opgaver mellem en lokal virtuel enhed kører i et hypervisor og Microsoft Azure skylagring. StorSimple virtuelle matrix er effektive og rentable alternativ til at 8000 serie fysisk matrixen. Den virtuelle matrix kan køre på den eksisterende hypervisor infrastruktur, understøtter både iSCSI- og de små og mellemstore virksomheder protokoller og er velegnet til scenarier i office remote office/gren. Gå til [Microsoft Azure StorSimple oversigt](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)kan finde flere oplysninger om StorSimple løsningerne.

Denne artikel omhandler de bedste fremgangsmåder, der er implementeret under den indledende installation, installation og administration af StorSimple virtuelle matrixen. Disse bedste fremgangsmåder giver valideret retningslinjer for konfiguration og administration af din virtuelle matrix. I denne artikel er rettet mod IT-administratorer, installere og administrere de virtuelle matrixer i deres datacentre.

Vi anbefaler, at en periodiske Gennemse de bedste fremgangsmåder for at sikre, at enheden er stadig er i overensstemmelse, når der foretages ændringer til konfiguration eller handlingen strømmen. Skal du støder på problemer under implementering af disse bedste fremgangsmåder på din virtuelle matrix, kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at få hjælp.

## <a name="configuration-best-practices"></a>Konfiguration af bedste fremgangsmåder 

Disse bedste fremgangsmåder dækker de retningslinjer, der skal følges under den indledende installation og installation af de virtuelle matrixer. Disse bedste fremgangsmåder omfatter dem, der er relateret til klargøring af den virtuelle maskine, indstillinger for Gruppepolitik, sidetilpasning, konfiguration af netværk, konfigurere lagerplads konti og oprette aktier og enheder til den virtuelle matrix. 

### <a name="provisioning"></a>Klargøring 

StorSimple virtuelle matrix er et virtuelt (VM) klargjort på hypervisor (Hyper-V eller VMware) host serveren. Når den virtuelle maskine, kan du sikre, at din vært er kunne allokeres tilstrækkelige ressourcer. Få mere at vide ved at gå til [ressourcens minimumkrav](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) til at klargøre en matrix. 

Implementere følgende anbefalede fremgangsmåder, når den virtuelle matrix:


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Virtuelt type**   | **Generering af 2** VM til brug med Windows Server 2012 eller nyere og et *.vhdx* billede. <br></br> **Generering af 1** VM til brug med et Windows Server 2008 eller nyere og et *.vhd* billede.                                                                                                              | Brug virtuelt version 8-11, når du bruger *.vmdk* billede.                                                                      |
| **Hukommelse type**            | Konfigurere som **statisk hukommelse**. <br></br> Brug ikke indstillingen **dynamiske hukommelse** .            |                                                    |
| **Datatypen disk**         | Klargøre som **dynamisk udvidelse**.<br></br> **Fast størrelse** tager lang tid. <br></br> Brug ikke indstillingen **differencing** .                                                                                                                   | Brug indstillingen **tynd klargøring** .                                                                                      |
| **Ændring af data disk** | Udvidelse eller formindske er ikke tilladt. Et forsøg på at gøre det resulterer i tab af alle de lokale data på enhed.                       | Udvidelse eller formindske er ikke tilladt. Et forsøg på at gøre det resulterer i tab af alle de lokale data på enhed. |

### <a name="sizing"></a>Størrelseshåndtag

Når sidetilpasning din StorSimple virtuelle matrix, skal du overveje følgende faktorer:

- Lokale reservation for enheder eller aktier. Cirka 12% af det tomme område er reserveret på lokale niveau for hver klargjort lagdelte lydstyrken eller del. Omkring er 10% af det tomme område også reserveret til et lokalt fastgjorte lydstyrken for filsystemet.
- Indirekte øjebliksbillede. Stort set er 15% plads på det lokale niveau reserveret til snapshots.
- Behovet for gendanner. Hvis gør Gendan som en ny handling, skal størrelseshåndtag højde for plads til Gendan. Gendan er udført for at en del eller størrelsen af den samme størrelse eller større.
- Nogle bufferen bør der tildeles til en hvilken som helst uventede vækst.

Baseret på foregående faktorer, repræsenteres størrelseshåndtag kravene af følgende ligning:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Følgende eksempler viser, hvordan du kan tilpasse størrelsen af et virtuelt matrix, der er baseret på dine krav.

#### <a name="example-1"></a>Eksempel 1:
Din virtuelle matrix vil du kunne 

- klargøre en 2 TB lagdelte lydstyrken eller del.
- klargøre en 1 TB lagdelte lydstyrken eller del.
- klargøre en 300 GB lokalt fastgjorte lydstyrken, eller dele.


For den foregående enheder eller aktier, og Lad os beregnes kravene til diskplads på den lokale niveau. 

For hver lagdelte lydstyrken/del, vil lokale reservation først skal være lig med 12% af lydstyrken/del størrelse. For lokalt fastgjorte lydstyrken/del er lokale reservation 10% af den lokalt fastgjorte lydstyrken/del størrelse (ud over den klargjorte størrelse). I dette eksempel skal du

- 240 GB lokale reservation (til en 2 TB lagdelt lydstyrken/del)
- 120 GB lokale reservation (for en 1 TB lagdelt lydstyrken/del)
- 330 GB for lokalt fastgjorte lydstyrken eller del (tilføje 10% af lokale reservation til 300 GB klargjort størrelse)

Den samlede mængde plads, der kræves på det lokale niveau hidtil er: 240 GB + 120 GB + 330 GB = 690 GB.

Andet, skal vi mindst meget plads på lokale niveau som den største enkelt reservation. Dette ekstra beløb, der bruges i tilfælde af, at du har brug at gendanne fra et snapshot af en sky. I dette eksempel er den største lokale reservation 330 GB (herunder reservation af filsystem), så du skal tilføje, til 690 GB: 690 GB + 330 GB = 1020 GB.
Hvis vi udført efterfølgende yderligere gendanner, kan vi altid frigøre plads fra forrige gendannelsen.

Tredje, skal vi 15% af dit samlede lokale plads hidtil til at gemme lokale snapshots, så kun 85% af den er tilgængelig. I dette eksempel, der ville være omkring 1020 GB = 0.85&ast;klargjort data disk TB. Så, der ville være klargjort data disken (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (hvor der afrundes til nærmeste kvartil)

Factoring i uventede vækst og nye gendanner, skal du klargøre en lokal disk af omkring 1,25-1,5 TB.

> [AZURE.NOTE] Vi anbefaler også, at den lokale disk tyndt klargjort. Denne anbefaling skyldes, at gendanne plads er kun nødvendigt, når du vil gendanne data, som er ældre end fem dage. På elementniveau gendannelse kan du gendanne data for de sidste fem dage uden at kræve ekstra plads til gendannelse.

#### <a name="example-2"></a>Eksempel 2: 
Din virtuelle matrix vil du kunne 

- klargøre en 2 TB lagdelt lydstyrken
- klargøre en 300 GB lokalt fastgjort lydstyrken

Baseret på 12% af lokal diskplads reservation for lagdelte enheder/aktier og 10% for lokalt fastgjorte enheder/aktier, vi har brug for

- 240 GB lokale reservation (til 2 TB lagdelt lydstyrken/del)
- 330 GB for lokalt fastgjorte lydstyrken eller del (føje 10% af lokale reservation til 300 GB klargjort mellemrum)

Samlet plads på det lokale niveau er: 240 GB + 330 GB = 570 GB

Mindste lokale plads til Gendan er 330 GB. 

15% af total disken bruges til at gemme snapshots, så kun 0.85 er tilgængelig. Så diskstørrelse er (900&ast;(1/0.85)) = 1.06 TB ~ 1,25 TB (hvor der afrundes til nærmeste kvartil) 

Factoring i en hvilken som helst uventede forøgelse, kan du klargøre en lokal disk 1,25-1,5 TB.


### <a name="group-policy"></a>Gruppepolitik

Gruppepolitik er en infrastruktur, der gør det muligt at implementere specifikke konfigurationer for brugere og computere. Indstillinger for Gruppepolitik er indeholdt i Gruppepolitik gruppepolitikobjekter, som er knyttet til de følgende Active Directory-domænetjenester (AD DS) objektbeholdere: websteder, domæner eller organisatoriske enheder (afdelinger). 

Hvis din virtuelle matrix er medlem af et domæne, kan gruppepolitikobjekter anvendes til den. Disse gruppepolitikobjekter kan installere programmer som en antivirussoftware, der kan påvirke driften af StorSimple virtuelle matrixen.

Derfor anbefaler vi, at du:

-   Sørg for, at din virtuelle matrix er i sin egen organisationsenhed (OU) til Active Directory. 

-   Sørg for, at ingen objekter til gruppepolitik (GPO'er) er anvendt på din virtuelle matrix. Du kan blokere nedarvning for at sikre, at den virtuelle matrix (underordnet node) automatisk ikke arver gruppepolitikobjekter, der er fra overordnet. Gå til [Bloker for nedarvning](https://technet.microsoft.com/library/cc731076.aspx)kan finde flere oplysninger.


### <a name="networking"></a>Netværk

Netværkskonfiguration for din virtuelle matrix sker via lokale internettet brugergrænseflade. Et virtuelt netværk interface er aktiveret via den hypervisor, hvor den virtuelle matrix er klargjort. Brug siden [Netværksindstillinger](storsimple-ova-deploy3-fs-setup.md) til at konfigurere virtuelt netværk interface IP-adresse, undernet og gatewayen.  Du kan også konfigurere den primære og sekundære DNS-server, indstillinger for tid og valgfrit proxyindstillinger til din enhed. De fleste af netværkskonfigurationen er en enkeltstående installation. Gennemse [StorSimple netværk krav](storsimple-ova-system-requirements.md#networking-requirements) før du installerer den virtuelle matrix.

Når du installerer din virtuelle matrix, anbefaler vi, at du følger disse bedste fremgangsmåder:

-   Sørg for, at det netværk, hvor den virtuelle matrix er installeret altid har kapacitet til at angive 5 Mbps internetbåndbredde (eller mere). 

    -   Har du brug for internetbåndbredde varierer afhængigt af din arbejdsbyrde egenskaber og rente ændring af data.

    -   Ændring af data, der kan løses er direkte proportional din internetbåndbredde. Som et eksempel, når du tager en sikkerhedskopi, kan en 5 Mbps båndbredde rumme en ændring af data på omkring 18 GB i otte timer. Med fire gange så meget båndbredde (20 Mbps), kan du håndtere fire gange mere Dataændring (72 GB). 

-   Sørg for forbindelse til internettet altid er tilgængelig. Sporadisk eller upålidelig internetforbindelser til enhederne, der kan medføre tab af adgang til data i skyen, hvilket kan medføre en ikke-understøttede konfiguration.

-   Hvis du planlægger at implementere enheden som en iSCSI-server: 
    -   Vi anbefaler, at du deaktiverer indstillingen **få IP-adresse automatisk** (DHCP). 
    -   Konfigurere statiske IP-adresser. Du skal konfigurere en primær og en sekundær DNS-server.

    -   Hvis definerer flere netværkgrænseflader på din virtuelle matrix, kun den første netværksgrænseflade (er som standard denne grænseflade **Ethernet**) kan få fat i skyen. For at styre typen trafik, kan du oprette flere virtuelt netværkgrænseflader på din virtuelle matrix (konfigureret som en iSCSI-server) og forbinde disse grænseflader til forskellige undernet.

-   Sådan begrænses skyen båndbredden kun (bruges af den virtuelle matrix), konfigurere (throttling) på routeren eller firewallen. Hvis du definerer (throttling) i din hypervisor, sænker den alle de protokoller, herunder iSCSI og små og mellemstore virksomheder i stedet for bare skyen båndbredden. 

-   Sikre, at tid synkroniseringen af hypervisors er aktiveret. Hvis din virtuelle matrix med Hyper-V, markere i Hyper-V-administrationen, gå til **indstillinger &gt; Integration Services**, og Sørg for, at **synkronisering af klokkeslæt** er markeret.

### <a name="storage-accounts"></a>Lagerplads konti

StorSimple virtuelle matrix kan knyttes til en enkelt lagerplads konto. Denne konto lagerplads kan være en automatisk oprettede lagerplads konto, en konto i det samme abonnement som tjenesten, eller en lagerplads-konto, der er relateret til et andet abonnement. Finde flere oplysninger, hvordan du [administrerer lager konti til din virtuelle matrix](storsimple-ova-manage-storage-accounts.md).

Brug nedenstående anbefalinger til lagerplads konti, der er knyttet til din virtuelle matrix.

-   Når du knytter flere virtuelle matrixer med en enkelt lagerplads konto, angive den maksimale kapacitet (64 TB) for et virtuelt matrix og den maksimale størrelse (500 TB) for et lager konto. Denne funktion begrænser antallet af komplette virtuelle matrixer, som kan knyttes til denne konto for lagerplads at om 7.

-   Når du opretter en ny konto lagerplads
    -   Vi anbefaler, at du opretter den i området tættest på den remote office/gren office, hvor din StorSimple virtuelle matrix er installeret for at minimere latenstider.

    -   Tage i betragtning, at du ikke kan flytte en lagerplads konto på tværs af forskellige områder. Du kan også flytte en tjeneste på tværs af abonnementer.

    -   Bruge en lagerplads-konto, der implementerer redundans mellem datacentre. Geografisk overflødigt lagerplads (GRS), Zone overflødige lagerplads (ZRS) og lokalt overflødige lagerplads (LRS) understøttes alle til brug sammen med din virtuelle matrix. Gå til [Azure lagerplads gentagelse](../storage/storage-redundancy.md)kan finde flere oplysninger om de forskellige typer lagerplads konti.


### <a name="shares-and-volumes"></a>Aktier og enheder

For din StorSimple virtuelle matrix, kan du klargøre aktier, når den er konfigureret som en filserver og enheder, når konfigureret som en iSCSI-server. De bedste fremgangsmåder til oprettelse af aktier og enheder er relateret til størrelsen og den type, der er konfigureret.

#### <a name="volumeshare-size"></a>Lydstyrken/del størrelse

På din virtuelle matrix, kan du klargøre aktier, når den er konfigureret som en filserver og enheder, når konfigureret som en iSCSI-server. De bedste fremgangsmåder til oprettelse af aktier og enheder, der er relateret til størrelsen og den type, der er konfigureret. 

Husk på følgende anbefalede fremgangsmåder, når aktier eller enheder på enheden virtuelle.

-   Filstørrelser i forhold til klargjort størrelsen på en lagdelte del kan påvirke den tiering ydeevne. Arbejde med store filer kan medføre en langsom niveau ud. Når du arbejder med store filer, anbefales det, at filen største er mindre end 3% af del størrelsen.

-   Maksimalt 16 enheder/aktier kan oprettes på den virtuelle matrix. Hvis lokalt fastgjort, kan enheder/aktierne være mellem 50 GB til 2 TB. Hvis lagdelt, skal enheder/aktierne være mellem 500 GB til 20 TB. 

-   Når du opretter en beholdning, faktor i den forventede data forbrug samt fremtidig vækst. Mens lydstyrken ikke kan udvides senere, kan du altid gendanne til en større enhed.

-   Når lydstyrken er blevet oprettet, kan du formindske størrelsen på lydstyrken på StorSimple.
   
-   Når du skriver til en lagdelte lydstyrken på StorSimple, når dataene lydstyrken når en bestemt grænse (i forhold til det lokale mellemrum reserveret til lydstyrken), er IO begrænset. Fortsætter med at skrive til denne lydstyrken langsommere IO betydeligt. Selvom du kan skrive til en lagdelte lydstyrken større end dens klargjort kapacitet (vi ikke aktivt stopper bruger fra at skrive ud over den klargjorte kapacitet), kan du se en besked til den effekt, du har oversubscribed. Når du får vist beskeden, er det meget vigtigt, at du tager korrigerende foranstaltninger som Slet lydstyrken data eller gendanne lydstyrken til en større lydstyrken (lydstyrken udvidelse understøttes ikke i øjeblikket).

-   I tilfælde af nedbrud gendannelse brug, som antallet tilladte aktier/enheder er 16 og det maksimale antal aktier/enheder, der kan behandles parallelt er også 16, har hvor mange aktier/Bind ikke indflydelse på dine frigivne Produktionsordre og RTOs. 

#### <a name="volumeshare-type"></a>Lydstyrken/del type

StorSimple understøtter to lydstyrken/del typer, der er baseret på brugen: lokalt fastgjort og niveauer. Lokalt fastgjorte enheder/aktier er thickly klargjort, mens de lagdelte enheder/aktier tyndt er klargjort. 

Vi anbefaler, at du implementerer følgende anbefalede fremgangsmåder, når du konfigurerer StorSimple enheder/aktier:

-   Identificere typen beholdning baseret på de arbejdsbelastninger, som du vil installere, før du opretter en volumenlicens. Bruge lokalt fastgjorte enheder til arbejdsbelastninger, som kræver lokale garantier data (selv under en skyen afbrydelse) og, der kræver lav skyen latenstider. Når du opretter en lydstyrken på din virtuelle matrix, du kan ikke ændre typen beholdning fra lokalt fastgjort til lagdelte eller *omvendt*. Oprette lokalt fastgjorte enheder som et eksempel, når du installerer SQL arbejdsbelastninger eller arbejdsbelastninger, som vært virtuelle maskiner (VM'er) bruge lagdelte enheder til fil del arbejdsmængder.

-   Markere indstillingen for mindre ofte anvendte arkivering data, når håndtere store filstørrelser. En større deduplication del udgave af 512 K bruges, når denne indstilling er aktiveret til at fremskynde dataoverførsel til skyen.

#### <a name="volume-format"></a>Lydstyrke-format

Når du opretter StorSimple enheder på din iSCSI-server, skal du initialiseret, tilslutte og formatere enhederne. Handlingen er udført på værten for forbindelse til din StorSimple enhed. Bedste fremgangsmåder anbefales, når tilslutte og formatering enheder på værten StorSimple.

-   Udføre en hurtig formatering på alle StorSimple enheder.

-   Når du formaterer en StorSimple lydstyrken, bruge en allokering enhedsstørrelse (AUS) på 64 KB (standard er 4 KB). 64 KB AUS er baseret på tests udført internt til almindelige StorSimple arbejdsmængder og andre arbejdsmængder.

-   Når du bruger StorSimple virtuelle matrixen konfigureret som en iSCSI-server, du skal ikke bruge samlet enheder eller dynamiske diske som disse enheder eller diske understøttes ikke af StorSimple.

#### <a name="share-access"></a>Dele access

Følg disse retningslinjer, når du opretter aktier på din virtuelle matrix filserver:

-   Når du opretter en del, kan du tildele en gruppe som administrator af en del i stedet for en enkelt bruger.

-   Du kan administrere NTFS-tilladelserne, efter del, der er oprettet ved at redigere aktier via Windows Stifinder.

#### <a name="volume-access"></a>Lydstyrken access

Når du konfigurerer iSCSI-enhederne til din StorSimple virtuelle matrix, er det vigtigt at kontrollere adgangen, hvor det er nødvendigt. For at bestemme, hvilke værtsservere kan få adgang til enheder skal du oprette og knytte access kontrolelement poster (ACRs) til StorSimple enheder.

Brug følgende anbefalede fremgangsmåder, når du konfigurerer ACRs for StorSimple enheder:

-   Knytte altid mindst én ACR til en disk.

-   Definere flere ACRs kun i et klyngebaseret miljø.

-   Når du tildeler mere end én ACR til en disk, kan du sikre dig, lydstyrken ikke vises på en måde, hvor den kan samtidig åbnes af mere end én ikke-grupperede vært. Hvis du har tildelt flere ACRs til et drev, vises en advarsel for at gennemgå din konfiguration.

### <a name="data-security-and-encryption"></a>Datasikkerhed og kryptering

Din StorSimple virtuelle matrix har funktioner for sikkerhed og kryptering, beskytte fortroligheden og integriteten af dine data. Når du bruger disse funktioner, anbefales det at følge disse bedste fremgangsmåder: 

-   Definere en cloud storage krypteringsnøgle for at generere AES 256 kryptering, inden data, der sendes fra dit virtuelle matrix til skyen. Der kræves ikke denne tast, hvis dine data er krypteret til at begynde med. Tasten kan oprettes og bevares sikre ved hjælp af et vigtige administrationssystem som [Azure vigtige samling](../key-vault/key-vault-whatis.md).

-   Når du konfigurerer kontoen lagerplads via tjenesten StorSimple Manager, Sørg for, at du aktiverer tilstanden SSL til at oprette en sikker kanal af netværkskommunikation mellem enheden StorSimple og skyen.

-   Genoprette taster til kontiene lagerplads (ved at få adgang til tjenesten Azure-lager) med jævne mellemrum at tage højde for eventuelle ændringer til at få adgang til baseret på den ændrede listen over administratorer.

-   Data i din virtuelle matrix er komprimeret og deduplicated før den er sendt til Azure. Vi anbefaler ikke ved hjælp af tjenesten Data Deduplication rolle på din Windows Server-vært.


## <a name="operational-best-practices"></a>Bedste fremgangsmåder ved drift

De bedste fremgangsmåder ved drift er retningslinjer, der skal følges under den daglige administration eller gendannelse af den virtuelle matrix. Disse fremgangsmåder dækker bestemte administrationsopgaver som tager sikkerhedskopier, gendanne fra et ekstra sæt udføre en failover, deaktivere og slette den matrix, overvågning system statistik- og tilstandsoplysninger og kører virus scanner på din virtuelle matrix.

### <a name="backups"></a>Sikkerhedskopier

Dataene på din virtuelle matrix er sikkerhedskopieret i skyen på to måder, standard automatiserede daglige sikkerhedskopiering af hele enheden starter på 22:30 eller via en sikkerhedskopi af manuelt efter behov. Som standard opretter enheden automatisk daglige skyen snapshot af alle dataene på den. Gå til at [sikkerhedskopiere dine StorSimple virtuelle matrix](storsimple-ova-backup.md)kan finde flere oplysninger.

Frekvens og opbevaring, der er knyttet til standard sikkerhedskopier kan ikke ændres, men du kan konfigurere det tidspunkt, hvor de daglige sikkerhedskopier er startet hver dag. Når du konfigurerer starttidspunktet for automatisk sikkerhedskopiering, anbefaler vi, at:

-   Planlægge dine sikkerhedskopier til belastet. Sikkerhedskopiering starttidspunkt skal ikke falder sammen med flere host EY.

-   Starte en sikkerhedskopi af manuelt efter behov, når du planlægger at udføre en enhed failover eller forud for vinduet reparation til at beskytte data på din virtuelle matrix.

### <a name="restore"></a>Gendanne

Du kan gendanne fra en sikkerhedskopi, der er angivet på to måder: gendanne til en anden enhed eller del eller udføre en på elementniveau gendannelse (fås kun med en virtuel matrix, der er konfigureret som en filserver). På elementniveau gendannelse kan du gøre en detaljeret gendannelse af filer og mapper fra en skyen sikkerhedskopi af alle aktierne på StorSimple enhed. Få mere at vide ved at gå til [gendanne fra en sikkerhedskopi](storsimple-ova-restore.md).

Når du udfører en gendannelse, kan du huske følgende retningslinjer på:

-   Din StorSimple virtuelle matrix understøtter ikke direkte gendannelse. Det kan dog være let opnås ved at gøre to ting: tilføje plads på den virtuelle matrix og derefter gendanner til en anden lydstyrken/del.

-   Når gendanne fra et lokalt drev, skal du huske bliver indstillingen Gendan en længerevarende handling. Selvom lydstyrken kan hurtigt kommer online, stadig dataene være hydrated i baggrunden.

-   Typen beholdning forbliver uændret under gendannelsen. En lagdelte lydstyrken genoprettes til en anden lagdelte enhed og et lokalt fastgjorte lydstyrken til en anden lokalt fastgjort lydstyrken.

-   Når du forsøger at gendanne en masse eller en del fra en sikkerhedskopi angive, hvis Gendannelsesjobbet mislykkes, skal en målliste lydstyrken, eller del kan stadig oprettes i portalen. Det er vigtigt, at du sletter denne ubrugte target lydstyrken eller dele i portalen for at minimere de fremtidige problemer, der følger af dette element.

### <a name="failover-and-disaster-recovery"></a>Failover og genoprettelse efter nedbrud

En enhed failover gør det muligt at overføre dine data fra en *kilde* enhed i datacenteret til en anden *target* enhed, der findes i den samme eller en anden geografiske placering. Enhed sekundære gælder for hele enheden. Under failover ændres skyen dataene til kilde-enhed ejerskab til, på målenheden.

For din StorSimple virtuelle matrix, kan du kun mislykkes til en anden virtuelle matrix, der administreres af den samme StorSimple Manager-tjeneste. En failover til en 8000 serie enhed eller en matrix, der administreres af en anden StorSimple Manager-tjeneste (end, til kilde-enhed) er ikke tilladt. Hvis du vil vide mere om failover overvejelser skal du gå til [forudsætninger for sekundære enhed](storsimple-ova-failover-dr.md).

Når du udfører en fejl for din virtuelle matrix, du huske følgende:

-   Til en planlagt failover er det anbefalede bedste fremgangsmåde er at tage alle enheder/aktier offline før start af sekundære. Følg vejledningen til at tage enheder/aktier offline på værtsplaceringen for første og derefter tage dem offline på enheden virtuelle operativsystem-specifikke.

-   Til en fil server nedbrud (DR) anbefaler vi, at du deltager target enheden til det samme domæne som kilde, så del tilladelser er automatisk løst. Kun sekundære til en destination enhed i det samme domæne understøttes i denne udgave.

-   Når DR er gennemført, slettes automatisk kilde-enhed. Selvom enheden er ikke længere tilgængelig, er den virtuelle maskine, som du har klargjort på host systemet stadig forbrug ressourcer. Vi anbefaler, at du sletter denne virtuelt fra systemet host for at forhindre, at eventuelle gebyrer periodisering af.

-   Bemærk, selvom sekundære er mislykkes, **dataene er altid sikkert i skyen**. Overvej følgende tre scenarier, hvor sekundære ikke blev fuldført:

    -   Der opstod en fejl i de indledende faser af sekundære som når DR foreløbig Kontroller udføres. Målenheden kan stadig bruges i så fald. Du kan prøve sekundære på den samme målenhed.

    -   Der opstod en fejl under processen faktisk failover. I dette tilfælde er den destinationsadresse-enhed markeret ubrugelig. Du skal klargøre og konfigurere en anden target virtuelle matrix og bruger, til failover.

    -   Sekundære blev fuldført følge som kildeenheden er blevet slettet, men den destinationsadresse-enhed har problemer, og du kan ikke få adgang til data. Dataene er stadig sikkert i skyen og nemt kan hentes ved at oprette en anden virtuelle matrix og derefter bruge den som en enhed med destination for DR.

### <a name="deactivate"></a>Deaktivere

Når du har deaktiveret en StorSimple virtuelle matrix, bryde forbindelsen mellem enheden og tilsvarende StorSimple Manager-tjenesten. Deaktivering af er en **permanent** handling og kan ikke fortrydes. En deaktiveret enhed kan ikke registreres med tjenesten StorSimple Manager igen. Gå til at [deaktivere og slette dine StorSimple virtuelle matrix](storsimple-deactivate-and-delete-device.md)kan finde flere oplysninger.

Husk følgende anbefalede fremgangsmåder, når deaktivere din virtuelle matrix:

-   Tage et skyen øjebliksbillede af alle dataene, før du deaktivere en virtuel enhed. Når du har deaktiveret en virtuel matrix, mistes alle lokal enhed dataene. Tage et øjebliksbillede i skyen, så du kan gendanne data på et senere tidspunkt.

-   Før du har deaktiveret en StorSimple virtuelle matrix, skal du sørge for at stoppe eller slette klienter og værter, der er afhænger af enheden.

-   Slette en deaktiveret enhed, hvis du ikke længere bruger, så det ikke periodiseres gebyrer.

### <a name="monitoring"></a>Overvågning

For at sikre, at din StorSimple virtuelle matrix er i en sammenhængende sund tilstand, skal du overvåge matrixen, og Sørg for, at du modtager oplysninger fra systemet herunder beskeder. Hvis du vil overvåge overordnede tilstanden for den virtuelle matrix, implementere følgende anbefalede fremgangsmåder:

- Konfigurere overvågning for at registrere diskplads brugen af harddisken virtuelle matrix data samt OS disken. Hvis kører Hyper-V, kan du bruge en kombination af System Center Virtual Machine Manager (SCVMM) og System Center Operations Manager (SCOM) til at overvåge dine virtualization hosts.   

- Konfigurere besked via mail på din virtuelle matrix til at sende beskeder på visse brugen niveauer.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Indekssøgning og virus scanne programmer

En StorSimple virtuelle matrix kan automatisk klasse data fra det lokale niveau til Microsoft Azure skyen. Når et program som en indekssøgning eller en virusscanning bruges til at scanne de data, der er gemt på StorSimple, skal du sørge for, at dataene skyen ikke få adgang til og trukket tilbage til det lokale niveau.

Vi anbefaler, at du implementerer følgende anbefalede fremgangsmåder, når du konfigurerer indeks Søg eller virus scanningen til din virtuelle matrix:

-   Deaktivere automatisk konfigureret fuld scanning handlinger.

-   Konfigurere indeks Søg eller virus scanning programmet til at udføre en trinvis scanning for lagdelte enheder. Dette vil gennemse kun de nye data sandsynligvis er placeret på det lokale niveau. De data, er lagdelt til skyen er ikke adgang til under en trinvis handling.

-   Kontrollér de korrekte søgefiltre og indstillinger er konfigureret, så kun de tilsigtede filtyper få scannet. For eksempel billedfiler (JPEG, GIF og TIFF) og tekniske tegninger skal ikke scannes under trinvis eller fuld indeks Genopbyg.

Hvis du bruger Windows indeksering proces, skal du følge disse retningslinjer:

-   Brug ikke Windows indekseringsprogram for lagdelte enheder, som den tilbagekaldelsen store datamængder (TBs) fra skyen, hvis indekset skal genopbygges ofte. Genopbygge indekset vil hente alle filtyper for at indeksere deres indhold.

-   Brug Windows indeksering proces for lokalt fastgjorte enheder, som dette vil kun adgang til data på de lokale niveauer til at bygge indekset (i skyen ikke kan få adgang til data).

### <a name="byte-range-locking"></a>Byte område låsning

Programmer kan låse et bestemt område af byte i filerne. Hvis byte område låsning er aktiveret på de programmer, der skriver til din StorSimple, virker derefter overgang switchmiljø ikke på din virtuelle matrix. For den overgang switchmiljø for at arbejde, skal alle områder for de filer, åbnes ikke låses op. Byte område låsning understøttes ikke med lagdelte enheder på din virtuelle matrix.

Anbefalede foranstaltninger til at afhjælpe byte område låsning omfatter:

-   Deaktiver byte område låsning i dit programlogik.

-   Brug lokalt fastgjort enheder (i stedet for lagdelt) for de data, der er knyttet til dette program. Lokalt fastgjorte enheder ikke klasse i skyen.

-   Når ved hjælp af lokalt fastgjort enheder med byte område låsning aktiveret, kan lydstyrken er online, før gendannelsen er fuldført. I disse tilfælde skal du vente gendannelsen skal være fuldført.

## <a name="multiple-arrays"></a>Flere matrixer

Flere virtuelle matrixer muligvis installeres på kontoen for en voksende arbejdssæt med data, som kan spilder til skyen dermed påvirke ydeevnen for enheden. Det er bedst at skala enheder, som arbejdssættet vokser i disse tilfælde. Dette kræver en eller flere enheder skal tilføjes i et lokalt datacenter. Når du tilføjer enhederne, kan du:

-   Opdele det aktuelle sæt af data.
-   Installer nye arbejdsbelastninger på nye enheder.
-   Hvis du anvender flere virtuelle matrixer, anbefaler vi, at fra justering af belastning perspektiv, distribuere matrixen på tværs af forskellige hypervisor hosts.

-  Flere virtuelle matrixer (når konfigureret som en filserver eller en iSCSI-server) kan installeres i en delt fil System Namespace. Detaljeret vejledning, skal du gå til [Distribueret fil System Namespace løsning med installationsvejledning til Hybrid skyen lagerplads](https://www.microsoft.com/download/details.aspx?id=45507). Distribueret fil System gentagelse anbefales i øjeblikket ikke til brug sammen med den virtuelle matrix. 


## <a name="see-also"></a>Se også
Lær, hvordan du kan [administrere dine StorSimple virtuelle matrix](storsimple-ova-manager-service-administration.md) via tjenesten StorSimple Manager.
