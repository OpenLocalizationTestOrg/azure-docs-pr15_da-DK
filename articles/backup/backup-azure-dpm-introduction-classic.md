<properties
    pageTitle="Introduktion til Azure DPM sikkerhedskopi | Microsoft Azure"
    description="En introduktion til sikkerhedskopiering af DPM servere ved hjælp af tjenesten Azure sikkerhedskopi"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="System Center Data Protection Manager, beskyttelse datastyring, dpm sikkerhedskopi"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Forberede at sikkerhedskopiere arbejdsbelastninger til Azure med DPM

> [AZURE.SELECTOR]
- [Server til Azure sikkerhedskopiering](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup Server (klassisk)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (klassisk)](backup-azure-dpm-introduction-classic.md)


Denne artikel indeholder en introduktion til brug af Microsoft Azure Backup til at beskytte dine System Center Data Protection Manager (DPM) servere og arbejdsmængder. Ved at læse den, skal du forstår:

- Hvordan fungerer Azure DPM server sikkerhedskopi
- Forudsætninger for at opnå en jævn sikkerhedskopiering oplevelse
- Typisk fejl og hvordan du kan løse dem.
- Understøttede scenarier

System Center DPM sikkerhedskopierer filer og programmet data. Data, der er sikkerhedskopieret til DPM kan være gemt på båndet, på disk, eller sikkerhedskopieret til Azure med Microsoft Azure sikkerhedskopiering. DPM skal arbejde sammen med Azure sikkerhedskopiering på følgende måde:

- **DPM installeres som en fysisk server eller en lokal virtuel maskine** – Hvis DPM installeres som en fysisk server eller en lokal Hyper-V virtuelt, kan du sikkerhedskopiere dataene til en Azure sikkerhedskopiering samling ud over disk og bånd sikkerhedskopiering.
- **DPM installeres som en Azure virtuelt** – fra System Center 2012 R2 med Update 3 DPM kan installeres som en Azure virtuelt. Hvis DPM installeres som en Azure virtuelt, kan du sikkerhedskopiere dataene til Azure diske knyttet til den DPM Azure virtuelle maskine, eller du kan aflaste lagring af data ved at kopiere den op til en samling af legitimationsoplysninger Azure sikkerhedskopi.

## <a name="why-backup-your-dpm-servers"></a>Hvorfor Sikkerhedskopiér serverne DPM?

Business fordelene ved at bruge Azure sikkerhedskopi til at sikkerhedskopiere DPM servere omfatter:

- Du kan bruge Azure sikkerhedskopi som et alternativ til langsigtede installation på båndet for lokal DPM installation.
- Til DPM installationer i Azure, Azure sikkerhedskopien kan du offload lagerplads fra Azure disken, så du kan skalere op ved at gemme ældre data i Azure sikkerhedskopierings- og nye data på disken.

## <a name="how-does-dpm-server-backup-work"></a>Hvordan fungerer DPM server sikkerhedskopi?
Hvis du vil sikkerhedskopiere en virtuel maskine, først et punkt-in-time øjebliksbillede af dataene, der skal bruges. Tjenesten Azure sikkerhedskopiering starter sikkerhedskopieringen på det planlagte tidspunkt og udløser filtypenavnet sikkerhedskopiering for at tage et øjebliksbillede. Filtypenavnet sikkerhedskopiering i overensstemmelse med tjenesten i gæst VSS at opnå konsistens og aktiverer API'EN blob øjebliksbillede af tjenesten Azure-lager, når konsistens er nået. Dette er gjort for at få et ensartet øjebliksbillede af diske på den virtuelle maskine, uden at lukke den.

Når snapshot er blevet tildelt, overføres dataene af tjenesten Azure sikkerhedskopien til den ekstra samling af legitimationsoplysninger. Tjenesten sørger for at identificere og overføre kun de dokumentkomponenter, der er ændret fra den seneste sikkerhedskopi, hvilket gør sikkerhedskopier lager og netværk effektiv. Når dataoverførslen er fuldført, snapshot er fjernet, og der oprettes et gendannelsespunkt. Denne gendannelsespunkt kan ses i portalen Azure klassisk.

>[AZURE.NOTE] Til Linux virtuelle maskiner kan kun fil ensartet sikkerhedskopi.

## <a name="prerequisites"></a>Forudsætninger
Forberede Azure sikkerhedskopi til at sikkerhedskopiere DPM data på følgende måde:

1. **Opret en sikkerhedskopi samling** – oprette en samling af legitimationsoplysninger i konsollen Azure sikkerhedskopi.
2. **Hent samling legitimationsoplysninger** – i Azure sikkerhedskopi, skal du overføre det management-certifikat, du har oprettet til samling af legitimationsoplysninger.
3. **Installere på Azure Backup Agent og registrere serveren** – fra Azure sikkerhedskopiering og installere agenten på hver DPM server og registrere DPM-serveren i den ekstra samling af legitimationsoplysninger.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Krav (og begrænsninger)

- DPM kan køre som en fysisk server eller en Hyper-V virtuel maskine, der er installeret på System Center 2012 SP1 eller System Center 2012 R2. Det kan også køre som en Azure virtuelt kører på System Center 2012 R2 med mindst DPM 2012 R2 opdateringspakke 3 eller en Windows virtuel maskine i VMWare, der kører på System Center 2012 R2 med mindst opdateringspakke 5.
- Hvis du kører DPM med System Center 2012 SP1, skal du installere opdateringspakke 2 til System Center Data Protection Manager SP1. Dette er påkrævet, før du kan installere Azure Backup Agent.
- DPM-serveren skal have Windows PowerShell og .net Framework 4.5 installeret.
- DPM kan sikkerhedskopiere arbejdsbelastninger, som de fleste til Azure sikkerhedskopi. En komplet liste over hvad er understøttet, se understøtter Azure sikkerhedskopien elementerne nedenfor.
- Data gemt i Azure sikkerhedskopi kan ikke gendannes med indstillingen "kopi på bånd".
- Du skal bruge en Azure-konto med funktionen Azure sikkerhedskopi er aktiveret. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Læs mere om [Azure sikkerhedskopi priser](https://azure.microsoft.com/pricing/details/backup/).
- Azure sikkerhedskopiering kræver Azure Backup Agent skal være installeret på de servere, du vil sikkerhedskopiere. Hver server skal have mindst 10% af størrelsen på de data, der sikkerhedskopieres, tilgængelig som lokale gratis lagerplads. For eksempel kræver sikkerhedskopiere 100 GB af data mindst 10 GB ledig plads på arbejdsområ placering. Mens minimum er 10%, anbefales 15% af gratis lokale lagerplads skal bruges til placeringen af cachen.
- Data gemmes i Azure samling lagerplads. Der er ingen grænse for mængden data, du kan tilbage til en sikkerhedskopi af Azure vault, men størrelsen på en datakilde (for eksempel en virtuel computer eller database) må overstige 54,400 GB.

Disse filtyper understøttes til tilbage til Azure:

- Krypteret (kun fuld sikkerhedskopier)
- Komprimeret (trinvise sikkerhedskopier understøttes)
- Sparse (trinvise sikkerhedskopier understøttes)
- Komprimeret og sparse (behandlet som Sparse)

Og det er ikke-understøttede:

- Servere på store og små bogstaver filsystemet understøttes ikke.
- Hårde links (sprunget over)
- Genfortolkningspunkter punkter (sprunget over)
- Krypteret og komprimeret (sprunget over)
- Krypteret og sparse (ignoreret)
- Komprimeret værdistrøm
- Sparse værdistrøm

>[AZURE.NOTE] Fra i System Center 2012 DPM med SP1, kan du sikkerhedskopiere op arbejdsbelastninger, som er beskyttet af DPM til Azure ved hjælp af Microsoft Azure sikkerhedskopiering.
