<properties
    pageTitle="Fejlfinding i forbindelse med sletter Azure lagerplads firmaer, objektbeholdere eller virtuelle harddiske i en klassisk installation | Microsoft Azure"
    description="Fejlfinding i forbindelse med sletter Azure lagerplads firmaer, objektbeholdere eller virtuelle harddiske i en klassisk installation"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Fejlfinding i forbindelse med sletter Azure lagerplads firmaer, objektbeholdere eller virtuelle harddiske i en klassisk installation

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Du kan modtage fejl, når du forsøger at slette Azure lagerplads konto, objektbeholder eller Virtuelle i [Azure portal](https://portal.azure.com/) eller [Azure klassisk portal](https://manage.windowsazure.com/). Problemerne, kan det skyldes følgende omstændigheder:

-   Når du sletter en VM, slettes disken og Virtuelle automatisk ikke. Der kan være årsagen til fejlen på lagerplads konto sletningen. Vi Slet ikke disken, så du kan bruge disken til at oprette forbindelse til en anden VM.

-   Der er stadig en rettighed på en disk eller blob, der er knyttet til disken.

Hvis der ikke er adresseret Azure problemet i denne artikel, kan du besøge Azure-fora på [MSDN og stakoverløb](https://azure.microsoft.com/support/forums/). Du kan sende dit problem på disse fora, eller @AzureSupport på Twitter. Du kan også sende en Azure supportanmodning ved at vælge **få support** på webstedet [Azure understøtter](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Symptomer

Følgende afsnit indeholder almindelige fejl, som du muligvis får, når du forsøger at slette de Azure lagerplads firmaer, objektbeholdere eller virtuelle harddiske.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scenarie 1: Kan ikke slette en lagerplads konto

Når du navigerer til kontoen lagerplads i [Azure portal](https://portal.azure.com/) eller [Azure klassisk portal](https://manage.windowsazure.com/) og vælg **Slet**, kan du muligvis få vist følgende fejlmeddelelse:

*Lagerplads konto StorageAccountName indeholder VM billeder. Kontroller disse VM billeder er fjernet, før du kan slette denne lagerplads konto.*

Du kan også få vist denne fejl:

**På Azure-portalen**:

*Blev ikke slettet lagerplads konto < vm-lager-kontonavn >. Kan ikke slette lagerplads konto < vm-lager-kontonavn >: ' lagerplads konto < vm-lager-kontonavn > har nogle aktive billede(r) og/eller disken(e). Sikre, at disse billede(r) og/eller disken(e) fjernes, før du kan slette denne konto lagerplads.'.*

**Klassisk på Azure-portalen**:

*Lagerplads konto < vm-lager-kontonavn > har nogle aktive billede(r) og/eller disken(e), f.eks. xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Kontrollér, at disse billede(r) og/eller disken(e) fjernes, før du kan slette denne lagerplads konto.*

Eller

**På Azure-portalen**:

*Lagerplads konto < vm-lager-kontonavn > har 1 beholdere til opbevaring, har et aktivt billede og/eller disk elementer. Kontroller disse elementer er fjernet fra billede-lager, før du kan slette denne lagerplads konto*.

**Klassisk på Azure-portalen**:

*Sende mislykkedes lagerplads konto < vm-lager-kontonavn > har 1 beholdere til opbevaring, har et aktivt billede og/eller disk elementer. Kontroller disse elementer er fjernet fra billede-lager, før du kan slette denne lagerplads konto. Når du forsøger at slette en lagerplads-konto, og der er stadig er aktiv diske, der er knyttet til den, du får vist en meddelelse om der er aktive diske, der skal slettes*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scenarie 2: Kan ikke slette en objektbeholder

Når du forsøger at slette objektbeholderen til lagring af, kan du muligvis få vist følgende fejlmeddelelse:

*Lykkedes ikke at slette objektbeholder til lagring <container name>. Fejl: "der findes i øjeblikket en rettighed på objektbeholderen og blev angivet noget bortlease-ID i anmodningen*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scenarie 3: Kan ikke slette en virtuel harddisk

Når du sletter en VM og prøv derefter at slette BLOB tilknyttede virtuelle, modtager du muligvis vist følgende meddelelse:

*Det lykkedes ikke at slette blob ' path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Fejl: "der findes i øjeblikket en rettighed på blob og blev angivet noget bortlease-ID i anmodningen.*

## <a name="solution"></a>Løsning
Du kan løse de mest almindelige problemer, kan du prøve følgende metode:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Trin 1: Slet eventuelle OS diske, der forhindrer sletning af lagerplads konto, objektbeholder eller Virtuelle

1. Skifte til [Azure klassisk portal](https://manage.windowsazure.com/).
2. Vælg **VIRTUELT** > **DISKE**.

    ![Billede af diske på virtuelle maskiner på Azure klassisk portal.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Find de diske, der er knyttet til lager konto, objektbeholder eller Virtuelle, du vil slette. Når du markerer placeringen af disken, kan du finde den tilknyttede lagerplads konto, beholderen eller Virtuelle.

    ![Billede, der viser placeringsoplysninger om en disk på Azure klassisk portal](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Bekræft, ingen VM ikke vises på feltet **vedhæftet** diskene, og slet derefter diskene.

    > [AZURE.NOTE] Hvis en disk er knyttet til en VM, kan du ikke kan du slette den. Diske er fjernet fra en slettede VM asynkront. Det kan tage et par minutter, efter VM er blevet slettet for dette felt for at rydde.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Trin 2: Slette en hvilken som helst VM billeder, der forhindrer sletning af det lagerplads firma eller en objektbeholder

1. Skifte til [Azure klassisk portal](https://manage.windowsazure.com/).
2. Vælg **VIRTUELT** > **billeder**, og slet derefter de billeder, der er knyttet til lager konto, objektbeholder eller Virtuelle.

    Herefter du forsøge at slette lagerplads konto, objektbeholder eller Virtuelle igen.

> [AZURE.WARNING] Sørg for at sikkerhedskopiere noget, du vil gemme, før du sletter kontoen. Det er ikke muligt at gendanne en slettet lagerplads konto eller hente det indhold, som den indeholdt før sletning. Dette også gælder for alle ressourcer i kontoen: Når du sletter en Virtuelle, blob, tabel, kø eller filer, slettes den permanent. Sørg for, at ressourcen, der ikke er i brug.

## <a name="about-the-stopped-deallocated-status"></a>Om stoppet (deallokeres) status

VM'er, der er oprettet i den klassiske implementeringsmodel og, som er bevaret har **stoppet (deallokeres)** status på [Azure portal](https://portal.azure.com/) eller [Azure klassisk portal](https://manage.windowsazure.com/).

**Azure klassisk portalen**:

![Stoppet (Deallocated) status for FOS på Azure-portalen.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure-portalen**:

![Stoppet (deallokeres) status for FOS på Azure klassisk portal.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Statussen "Stoppet (deallokeres)" versioner computerressourcer, som CPU, hukommelse og netværk. Diskene dog bevares stadig, så du kan hurtigt oprette igen VM Hvis det er nødvendigt. Disse er oprettet oven på virtuelle harddiske, som understøttes af Azure-lager. Kontoen lagerplads har disse virtuelle harddiske og diskene har rettigheder på disse virtuelle harddiske.

## <a name="next-steps"></a>Næste trin

- [Slette en lagerplads konto](storage-create-storage-account.md#delete-a-storage-account)
- [Sådan skiftes låste lease af blob-lager i Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
