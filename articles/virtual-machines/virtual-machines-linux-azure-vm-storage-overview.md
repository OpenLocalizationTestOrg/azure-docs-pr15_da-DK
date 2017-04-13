<properties
  pageTitle="Azure og Linux VM lager | Microsoft Azure"
  description="I denne artikel beskrives Azure Standard og Premium lager med Linux virtuelle maskiner."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Azure og Linux VM lagerplads

Azure-lager er cloud storageløsning til moderne programmer, der er afhænger af holdbarhed, tilgængelighed og skalerbarhed til imødekommer behovet hos deres kunder.  Ud over gør det muligt for udviklere til at oprette store programmer til at understøtte nye scenarier, indeholder Azure-lager også lagerplads grundlaget for virtuelle Azure-computere.

## <a name="azure-storage-standard-and-premium"></a>Azure-lager: Standard og Premium

Azure VM kan være bygget på standard lagerplads diske eller premium lagerplads diske.  Når ved hjælp af portalen til at vælge din VM skal du vælge en rulleliste på grundlæggende skærmen for at få vist både standard og premium.  Skærmbilledet nedenfor fremhæver/fra-knappen menuen.  Når du skifter til SSD, kun premium lagerplads aktiveret FOS der vises i alle sikkerhedskopier af SSD drev.  Når du skifter til harddisk, aktiveret standard lagerplads FOS sikkerhedskopierede rundt om diskdrev, der skal vises sammen med premium lagerplads FOS sikkerhedskopien af SSD.

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Når du opretter en VM fra den `azure-cli` du kan vælge mellem standard- og premium, når du vælger VM størrelsen via den `-z` eller `--vm-size` cli flag.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Oprette en VM med standard lagerplads VM på cli

Flaget cli `-z` vælger Standard_A1 med A1 der en standard-lager baseret Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Oprette en VM med premium lagerplads på cli

Flaget cli `-z` vælger Standard_DS1 med DS1 der en premium lagerplads baseret Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Standard lagerplads

Azure Standard lagerplads er standardtypen lagerplads.  Standard lagerplads er rentable samtidig med at performant.  

## <a name="premium-storage"></a>Premium lagerplads

Azure Premium lagerplads tilbyder høj ydeevne og lav ventetid disk understøttelse af virtuelle maskiner, der kører jeg/O-intensivt arbejdsmængder. Virtual machine (VM) diske, der bruger Premium lagerplads gemmes data på massiv tilstand SSD'er (). Du kan overføre dit program VM diske til Azure Premium lager til Udnyt hastigheden og ydeevnen af disketterne.

Premium storage-funktioner:

- Premium lagerplads diske: Azure Premium lagerplads understøtter VM diske, som kan knyttes til DS, DSv2 eller GS serie Azure FOS.

- Premium siden Blob: Premium lagerplads understøtter Azure siden blob, som bruges til at holde fast diske til Azure virtuelle maskiner (VM'er).

- Premium lokalt overflødige lager: En Premium lagerplads konto kun understøtter lokalt overflødige lagerplads (LRS) som indstillingen gentagelse og bevarer tre kopier af dataene i et enkelt område.

- [Premium lagerplads](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Premium lagerplads understøttes FOS

Premium lagerplads understøtter DS-serien, DSv2-serien, GS række og Fs serie Azure virtuelle maskiner (VM'er). Du kan bruge både Standard og Premium lagerplads diske med Premium lagerplads, der understøttes af FOS. Men du kan ikke bruge Premium lagerplads diske med VM serie, som ikke er kompatible Premium-lager.

Følgende er Linux fordelingerne, som vi godkendt med Premium lagerplads.

| Fordeling | Version                 | Understøttede kernen    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7.x, 8.x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| Centos       | 6.5 6.6 6,7, 7.0, og 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Lagring af filer

Azure fillagring indeholder filshares i skyen ved hjælp af standard små og mellemstore virksomheder-protokollen. Du kan overføre enterprise-programmer, der er afhænger af filservere at Azure med Azure filer. Programmer, der kører i Azure kan nemt tilslutte filshares fra Azure virtuelle maskiner, der kører Linux. Og med den nyeste version af fillagring, du kan også oprette forbindelse til et filshare fra et lokalt program, der understøtter små og mellemstore virksomheder 3.0.  Da filshares er små og mellemstore virksomheder aktier, kan du få adgang til dem via standard filsystemet API'er.

Lagring af filer er oprettet på den samme teknologi som Blob tabel og kø lagerplads, så fillagring tilbyder tilgængelighed, holdbarhed, skalerbarhed og geografisk-redundans, der er indbygget i Azure lagerplads platform. Flere oplysninger om fil lagerplads ydeevne mål og begrænsninger for, i Azure lagerplads skalerbarhed og ydeevne destinationer.

- [Sådan bruges Azure fillagring med Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Hurtigtast lagerplads

Azure varmt lagerplads niveau er optimeret til lagring af data, der åbnes ofte.  Hurtigtast lagerplads er lagerplads standardtypen for blob butikker.

## <a name="cool-storage"></a>Smarte lagerplads

Azure smarte lagerplads niveau er optimeret til lagring af data, der er sjældent adgang til og langlivet. Eksempel på Brug sager for smarte lagerplads omfatter sikkerhedskopier, medieindhold, videnskabelige data, overholdelse af regler og arkivering data. Generelt er data, der sjældent bruges en perfekt kandidat til smarte lagerplads.

|                             | Hurtigtast lagerplads niveau      | Smarte lagerplads niveau     |
|:----------------------------|:---------------------:|:---------------------:|
| Tilgængelighed                | 99,9%                 | 99%                   |
| Tilgængelighed (RA-GRS læser) | 99,99%                | 99,9%                 |
| Brugsgebyrer               | Større lagerplads omkostninger  | Reducere omkostningerne til lager   |
|                             | Nederste access          | Højere access         |
|                             | og omkostninger | og omkostninger |


## <a name="redundancy"></a>Redundans

Dataene i din Microsoft Azure-lager konto replikeres altid for at sikre holdbarhed og høj tilgængelighed, møde Azure lagerplads SLA selv trods midlertidige hardware mislykkede forsøg.

Når du opretter en konto med lagerplads, skal du vælge en af følgende indstillinger for gentagelse:

- Lokalt overflødige lagerplads (LRS)
- Zone overflødige lagerplads (ZRS)
- Geografisk overflødige lagerplads (GRS)
- Læseadgang geografisk overflødige lagerplads (RA-GRS)

### <a name="locally-redundant-storage"></a>Lokalt overflødige lagerplads

Lokalt overflødige lagerplads (LRS) kopieres dataene i det område, hvor du har oprettet kontoen lagerplads. For at maksimere holdbarhed, replikeres alle anmodninger mod data i din lagerplads konto tre gange. Disse tre replikaer hver er placeret separat fejl domæner og opgradering domæner.  En anmodning om returnerer korrekt kun, når den er skrevet til alle tre kopier.

### <a name="zone-redundant-storage"></a>Zone overflødige lagerplads

Zone overflødige lagerplads (ZRS) kopieres dine data på tværs af to eller tre faciliteter, i et enkelt område eller på tværs af to områder, give højere holdbarhed end LRS. Hvis kontoen lagerplads har ZRS aktiveret, derefter er dine data robust selv i tilfælde af fejl på en af faciliteterne.

### <a name="geo-redundant-storage"></a>Geografisk overflødige lagerplads

Geografisk overflødige lagerplads (GRS) kopieres dataene til en sekundær område, der er hundredvis af sømil væk fra det primære område. Hvis kontoen lagerplads har GRS aktiveret, derefter er dine data robust selv i tilfælde af en komplet internationale afbrydelse eller nedbrud, ikke er det primære område gendannes.

### <a name="read-access-geo-redundant-storage"></a>Læseadgang geografisk overflødige lagerplads

Læseadgang geografisk overflødige lagerplads (RA-GRS) maksimerer tilgængelighed til kontoen lager ved at give skrivebeskyttet adgang til dataene i den sekundære placering, ud over gentagelse på tværs af to områder, der leveres af GRS. Hvis data bliver utilgængeligt i det primære område, kan dit program læse data fra den sekundære område.

Til en undersøgelse Azure lagerplads redundans se:

- [Azure lagerplads gentagelse](../storage/storage-redundancy.md)

## <a name="scalability"></a>Skalerbarhed

Azure-lager er stort omfang SVG, så du kan gemme og proces hundredvis af TB data, der understøtter stor data scenarier, der kræves af videnskabelig, finansielle analyse og medieprogrammer. Eller du kan gemme de lille mængde data, der kræves til en small business-websted. Det sted, hvor dine behov falde, betaler du kun for de data, du har gemt. Azure-lager i øjeblikket gemmer mange trillions entydige kunde objekter, og håndterer millioner af anmodninger om sekundet i gennemsnit.

For standard lagerplads konti: en standard-lagerplads konto har en anmodning om maksimale samlede rente 20.000 IOP'ER. Den samlede IOP'ER på tværs af alle diskoverfladerne virtuelt med en konto med standard lagerplads må ikke overstige denne grænse.

Premium lagerplads-konto: en premium lagerplads konto har en maksimale samlede overførselshastighed på 50 Gbps. Det samlede gennemløb på tværs af alle VM disken må ikke overstige denne grænse.

## <a name="availability"></a>Tilgængelighed

Vi garantere, mindst 99,99% (99,9% for smarte Access niveau) af tid, vi korrekt behandler anmodninger om at læse data fra læseadgang geografisk overflødige lagerplads (RA-GRS) konti, forudsat at mislykkede forsøg på at læse data fra det primære område er forsøgt igen på den sekundære område.

Vi garantere, mindst 99,9% (99% for smarte Access niveau) af tid, vi korrekt behandler anmodninger om at læse data fra lokalt overflødige lagerplads (LRS), Zone overflødige lagerplads (ZRS) og geografisk overflødige lagerplads (GRS) konti.

Vi garantere, mindst 99,9% (99% for smarte Access niveau) af tid, vi korrekt behandler anmodninger om at skrive data til lokalt overflødige lagerplads (LRS), Zone overflødige lagerplads (ZRS), og geografisk overflødige lagerplads (GRS) konti og læseadgang geografisk overflødige lagerplads (RA-GRS) konti.

- [Azure SERVICENIVEAUAFTALE for lagerplads](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Områder

Azure er alment tilgængelig i 30 områder i hele verden, og har offentliggjort-planer til 4 flere områder. Geografiske udvidelse er vigtig for Azure, fordi det giver mulighed for vores kunder for at opnå højere ydeevne og den understøtter deres krav og præferencer med hensyn til dataplacering.  Azures seneste område til start er i Tyskland.

Microsoft Cloud-Tyskland giver en opdelte mulighed for at de Microsoft Cloud-tjenester, der allerede findes i Europa, oprette øgede muligheder for innovation og økonomiske vækst for meget organiseret partnere og kunder i Tyskland, EU (EU) og det europæiske tilknytning (EFTA).

Kundedata i disse nye datacentre i Magdeburg og Frankfurt, administreres styres af en person med data, T-Systems International, en uafhængig tysk virksomhed og datterselskaber af Deutsche Telekom. Microsofts kommercielle skytjenester i disse datacentre overholde tysk data håndtering af regler og give kunder flere muligheder for at af hvordan og hvor dataene behandles.


- [Azure områder kort](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sikkerhed

Azure-lager indeholder et omfattende sæt af sikkerhedsfunktioner hvorved sammen udviklere til at oprette sikker programmer. Kontoen lagerplads selve kan være fastgjort ved hjælp af rollebaseret adgangskontrol og Azure Active Directory. Data kan være fastgjort undervejs mellem et program og Azure ved hjælp af kryptering på klientsiden, HTTPS eller små og mellemstore virksomheder 3.0. Data kan indstilles til krypteres automatisk, når skrevet til Azure-lager ved hjælp af lagerplads Service kryptering (SSE). OS og Data diske, der bruges af virtuelle maskiner kan indstilles til krypteres med Azure Disk kryptering. Delegeret adgang til dataobjekter i Azure-lager kan tildeles ved hjælp af delt Access signaturer.

### <a name="management-plane-security"></a>Administration af plan sikkerhed

Administration af plan består af de ressourcer, der bruges til at administrere din lagerplads konto. I dette afsnit gennemgår vi om implementeringsmodel Azure ressourcestyring og hvordan du bruger rollebaseret adgangskontrol (RBAC) til at styre adgangen til dine konti på lagerplads. Vi taler også om administration af dine lagerplads konto nøgler og hvordan du kan gendanne dem.

### <a name="data-plane-security"></a>Plan datasikkerhed

I dette afsnit, vil vi se på tillade adgang til de faktiske data-objekter i din lagerplads-konto, som blob, filer, køer og tabeller, ved hjælp af delt Access signaturer og gemt Access politikker. Vi kommer ind på både service niveau SAS og konto niveau SAS. Vi ser også, hvordan du kan begrænse adgangen til et bestemt IP-adresse (eller område af IP-adresser), hvordan du kan begrænse den protokol, der bruges til HTTPS, og hvordan du tilbagekalde en delt Access signatur uden at vente på at udløbe.

## <a name="encryption-in-transit"></a>Kryptering undervejs

Dette afsnit beskrives, hvordan du sikrer data, når du overfører ind eller ud af Azure-lager. Gennemgår vi om anbefalede brug af HTTPS og den kryptering som små og mellemstore virksomheder 3.0 til Azure filshares. Vi kan også se nærmere på klientsiden kryptering, der gør det muligt at kryptere data, før det overføres til lager i en klientprogrammet og dekryptere dataene, når det er overført tør for lagerplads.

## <a name="encryption-at-rest"></a>Kryptering på resten

Vi vil tale om lagerplads Service-kryptering (SSE), og hvordan du kan aktivere den til en lagerplads-konto, hvilket resulterer i din Bloker blob, siden BLOB og tilføje BLOB krypteret der automatisk, når skrevet til Azure-lager. Vi ser også på hvordan du kan bruge Azure Disk kryptering og udforske grundlæggende forskellene og tilfælde af Disk kryptering kontra SSE kontra klientsiden kryptering. Vi ser kortvarigt på FIPS overholdelse af angivne standarder for amerikanske computere.

- [Azure lagerplads sikkerhedsvejledning](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Omkostning

- [Lagerplads omkostninger](https://azure.microsoft.com/pricing/details/storage/)

- [Lommeregner til lagerplads omkostninger](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Lagergrænser

- [Tjenesten lagergrænser](../azure-subscription-service-limits.md#storage-limits)
