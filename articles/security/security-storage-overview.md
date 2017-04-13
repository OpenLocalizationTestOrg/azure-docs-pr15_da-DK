<properties
   pageTitle="Oversigt over Azure lagerplads sikkerhed | Microsoft Azure"
   description=" Azure-lager er cloud storageløsning til moderne programmer, der er afhænger af holdbarhed, tilgængelighed og skalerbarhed til imødekommer behovet hos deres kunder. I denne artikel giver et overblik over grundlæggende Azure sikkerhedsfunktioner, der kan bruges med Azure-lager. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Oversigt over sikkerhed Azure-lager

Azure-lager er cloud storageløsning til moderne programmer, der er afhænger af holdbarhed, tilgængelighed og skalerbarhed til imødekommer behovet hos deres kunder. Azure-lager indeholder et omfattende sæt af sikkerhedsfunktioner:

- Kontoen lagerplads kan være fastgjort med rollebaseret adgangskontrol og Azure Active Directory.
- Data kan være fastgjort undervejs mellem et program og Azure ved hjælp af kryptering på klientsiden, HTTPS eller 3.0 små og mellemstore virksomheder.
- Data kan indstilles til krypteres automatisk, når skrevet til Azure-lager ved hjælp af lagerplads Service kryptering.
- OS og Data diske, der bruges af virtuelle maskiner kan indstilles til krypteres med Azure Disk kryptering.
- Delegeret adgang til dataobjekter i Azure-lager kan tildeles ved hjælp af delt Access signaturer.
- Den anvendte godkendelsesmetode, som bruges af en person, når de åbner lagerplads kan spores ved hjælp af lagerplads analyser.

Du kan finde en mere detaljeret Kig på sikkerhed i Azure-lager, [Azure-lager sikkerhedsvejledning](../storage/storage-security-guide.md). Denne vejledning indeholder en undersøgelse sikkerhedsfunktioner Azure-lager som lager konto taster datakryptering undervejs og resten og lagerplads analyser.

I denne artikel giver et overblik over Azure sikkerhedsfunktioner, der kan bruges med Azure-lager. Links er leveret til artikler, som giver oplysninger om hver funktion, så du kan få mere at vide.

Her er de grundlæggende funktioner skal gennemgås i denne artikel:

- Rollebaseret adgangskontrol
- Delegeret adgang til lagerplads objekter
- Kryptering undervejs
- Kryptering på resten/lagerplads Service kryptering
- Azure Disk kryptering
- Azure vigtige samling

## <a name="role-based-access-control-rbac"></a>Rollebaseret adgangskontrol (RBAC)

Du kan sikre kontoen lagerplads med rollebaseret adgangskontrol (RBAC). Begrænse adgang, der er baseret på de [har brug at vide](https://en.wikipedia.org/wiki/Need_to_know) og [minimale privilegier](https://en.wikipedia.org/wiki/Principle_of_least_privilege) sikkerhedsprincipper er afgørende organisationer, hvor du vil gennemtvinge sikkerhedspolitikker for dataadgang. Disse rettigheder tildeles ved at tildele den relevante RBAC rolle til grupper og programmer på et bestemt område. Du kan bruge [indbyggede RBAC roller](../active-directory/role-based-access-built-in-roles.md), som lager konto bidragyder, tildele tilladelser til brugere.

Lær mere:

- [Azure Active Directory rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Delegeret adgang til lagerplads objekter

En delt adgang signatur (SAS) giver delegeret adgang til ressourcer i kontoen lagerplads. Sikkerhedstilknytningerne betyder, at du kan give en klient begrænsede tilladelser til objekter på din lagerplads konto i en angivet periode tid og med et bestemt sæt tilladelser. Du kan give disse begrænsede tilladelser uden at dele din konto access-taster. Sikkerhedstilknytningerne er en URI, der omfatter alle de oplysninger, der er nødvendige for godkendt adgang til en ressource, lagerplads i dets forespørgselsparametre. For at få adgang til lagerplads ressourcer med Sikkerhedstilknytningerne skal klienten kun sende i Sikkerhedstilknytningerne til den relevante parametre eller metode.

Lær mere:

- [Om SAS datamodellen](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Oprette og bruge en SAS med Blob-lager](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Kryptering undervejs
Kryptering undervejs er en metode til at beskytte data, når de er sendt på tværs af netværk. Du kan sikre data ved hjælp af med Azure-lager:

- [Transport-niveau kryptering](../storage/storage-security-guide.md#encryption-in-transit), som HTTPS, når du overfører data ind eller ud af Azure-lager.
- [Tråd kryptering](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), som små og mellemstore virksomheder 3.0 kryptering til Azure filshares.
- [Klientsiden kryptering](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), til at kryptere dataene, før det overføres til lager og dekryptere dataene, når det er overført tør for lagerplads.

Lær mere om klientsiden kryptering:

- [Klientsiden kryptering til Microsoft Azure-lager](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Skyen sikkerhed styrer serie: kryptere Data undervejs](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Kryptering på resten

[Kryptering af data på resten](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) er for mange organisationer, et obligatorisk trin mod data beskyttelse af personlige oplysninger, overholdelse af regler og datasuverænitet. Der findes tre Azure funktioner, som giver kryptering af data, der er "ved resten":

- [Lagerplads Service kryptering](../storage/storage-security-guide.md#encryption-at-rest) kan du anmode om, at tjenesten storage automatisk krypterer data, når du skriver til Azure-lager.
- [Klientsiden kryptering](../storage/storage-security-guide.md#client-side-encryption) indeholder også funktionen af kryptering på resten.
- [Azure Disk kryptering](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) gør det muligt at kryptere den OS og data diske bruges af en IaaS virtuelt.

Lær mere om lagerplads Service kryptering:

- [Azure lagerplads Service kryptering](https://azure.microsoft.com/services/storage/) er tilgængelig for [Azure Blob-lager](https://azure.microsoft.com/services/storage/blobs/). Få at vide om andre lagertyper Azure-, skal du se [filer](https://azure.microsoft.com/services/storage/files/), [Disk (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [tabel](https://azure.microsoft.com/services/storage/tables/)og [kø](https://azure.microsoft.com/services/storage/queues/).
- [Azure-lager Service kryptering efter Data på resten](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk kryptering

Azure Disk kryptering for virtuelle maskiner (VM'er) hjælper dig med at adresse organisatoriske sikkerhed og overholdelse af krav ved at kryptere disken VM (herunder start-og data) med nøgler og politikker, du styrer i [Azure nøgle samling](https://azure.microsoft.com/services/key-vault/).

Disken kryptering for FOS fungerer for Linux og Windows-operativsystemer. Tasten samling bruges også kan hjælpe dig med at beskytte, administrere og Overvåg anvendelse af dine disk krypteringsnøgler. Alle data i VM disken er krypteret på resten ved hjælp af branchestandard krypteringsteknologi i kontiene Azure-lager. Disken kryptering løsningen til Windows er baseret på [Microsoft sikkerhedshardware](https://technet.microsoft.com/library/cc732774.aspx)og Linux-løsning er baseret på [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lær mere:

- [Azure Disk kryptering til Windows og Linux IaaS virtuelle maskiner](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure vigtige samling

Azure Disk kryptering bruger [Azure nøgle samling](https://azure.microsoft.com/services/key-vault/) til at hjælpe dig med at kontrollere og administrere disk krypteringsnøgler og hemmeligheder i abonnementet vigtige samling og sikre, at alle data i virtuelt diskene er krypteret på resten i Azure-lager. Overvåge nøgler og politik brugen skal du bruge tasten samling.

Lær mere:

- [Hvad er Azure-tasten samling?](../key-vault/key-vault-whatis.md)
- [Introduktion til Azure-tasten samling](../key-vault/key-vault-get-started.md)
