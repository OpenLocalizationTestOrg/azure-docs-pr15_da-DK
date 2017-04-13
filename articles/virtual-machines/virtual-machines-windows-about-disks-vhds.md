<properties
    pageTitle="Om og virtuelle harddiske for Windows FOS | Microsoft Azure"
    description="Få mere at vide om grundlæggende diske og virtuelle harddiske til Windows virtuelle maskiner i Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Om og virtuelle harddiske til Azure virtuelle maskiner

Ligesom enhver anden computer Brug virtuelle maskiner i Azure diske som et sted for at gemme et operativsystem, programmer og data. Alle Azure virtuelle maskiner har mindst to diske – en Windows-operativsystemet disk og en midlertidig disk. Operativsystem disken er oprettet ud fra et billede, og både operativsystem disken og billedet er virtuelle harddiske (virtuelle harddiske) gemt i en Azure-lager-konto. Virtuelle maskiner kan også få en eller flere datadisce, gemmes også som virtuelle harddiske. I denne artikel findes også for [Linux virtuelle computere](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## <a name="operating-system-disk"></a>Operativsystem disk

Hver virtuelt har en vedhæftet operativsystem disk. Det er registreret som en SATA drev og mærket C:-drevet som standard. Denne disk har en maksimale kapacitet for 1023 gigabyte (GB). 

##<a name="temporary-disk"></a>Midlertidige disk

Midlertidige disken oprettes automatisk for dig. Midlertidige disken er mærket D: drev som standard, og den bruges til at gemme pagefile.sys. 

Størrelsen af den midlertidige disk varierer, afhængigt af størrelsen på den virtuelle maskine. Du kan finde yderligere oplysninger finder [størrelser til Windows virtuelle computere](virtual-machines-windows-sizes.md).

>[AZURE.WARNING] Gem ikke data på den midlertidige disk. Det indeholder midlertidige til programmer og processer og er beregnet til kun at gemme data som side eller Udskift filer. Hvis du vil knytte denne disk til et andet bogstav, kan du se [ændre bogstavet for Windows midlertidige disken](virtual-machines-windows-classic-change-drive-letter.md).

Yderligere oplysninger om hvordan Azure bruger den midlertidige disk, kan du se [om midlertidigt drev på Microsoft Azure virtuelle maskiner](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Datadisk

En datadisk er en virtuel harddisk, der er knyttet til en virtuel maskine til at gemme programmet data eller andre data, du vil bevare. Datadisce er registreret som SCSI-drev og er mærket med et bogstav, du vælger.  Hver datadisk har en maksimale kapacitet på 1023 GB. Størrelsen af den virtuelle maskine bestemmer, hvor mange datadisce du kan vedhæfte til den og typen af lagerplads, du kan bruge til at hoste diskene.

>[AZURE.NOTE] Du kan finde flere oplysninger om virtuelle maskiner kapacitet, [størrelser til Windows virtuelle computere](virtual-machines-windows-sizes.md).

Azure opretter en operativsystem disk, når du opretter en virtuel maskine fra et billede. Hvis du bruger et billede, der indeholder datadisce, opretter Azure også diskene data, når programmet opretter den virtuelle maskine. Ellers skal tilføje du datadisce når du har oprettet den virtuelle maskine.

Du kan føje datadisce til en virtuel maskine når som helst ved at **vedhæfte** disken til den virtuelle maskine. Du kan bruge en virtuel harddisk, du har overført eller kopieret til kontoen lagerplads, eller som Azure opretter for dig. Vedhæfte en datadisk tilknyttet filen Virtuelle med VM ved at placere en rettighed på den virtuelle harddisk, så den ikke kan slettes fra lagerplads, mens den stadig er tilsluttet.

## <a name="about-vhds"></a>Om virtuelle harddiske

De virtuelle harddiske bruges i Azure er .vhd-filer, der er gemt som siden BLOB med en konto med standard eller førsteklasses lagerplads i Azure. Se [forstå Bloker BLOB og siden BLOB](https://msdn.microsoft.com/library/ee691964.aspx)kan finde flere oplysninger om siden BLOB. Finde flere oplysninger om premium lagerplads [Premium lagerplads: High-performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).

Azure understøtter harddiskdrev Virtuelle-formatet. Fast formatet arrangerer den logiske disk lineært i filen, så disk forskydningen X er gemt på blob forskydning X. En lille sidefod i slutningen af blob beskrives egenskaberne for den virtuelle harddisk. Fast format affald plads ofte, fordi de fleste diske har store ubrugte områder i dem. Azure gemmer dog .vhd filer i formatet sparse, så du modtager fordelene ved begge fast og dynamiske diske på samme tid. Du kan finde flere oplysninger i [Introduktion til virtuelle harddiske](https://technet.microsoft.com/library/dd979539.aspx).

Alle .vhd-filer i Azure, du vil bruge som en kilde til at oprette diske eller billeder er skrivebeskyttet. Når du opretter en disk eller et billede, gør Azure kopier af .vhd-filer. Disse kopier kan være skrivebeskyttet eller læse-og-og skriveadgang, afhængigt af hvordan du bruger den virtuelle harddisk.

Når du opretter en virtuel maskine fra et billede, opretter Azure en disk til den virtuelle maskine, der er en kopi af .vhd kildefilen. For at beskytte mod utilsigtet sletning, placerer Azure en rettighed på en hvilken som helst .vhd kildefilen, der bruges til at oprette et billede, en disk operativsystem eller en datadisk.

Før du kan slette en kilde .vhd-fil, skal du fjerne bortlease ved at slette disken eller billede. Du kan slette den virtuelle maskine, disk operativsystem og .vhd kildefilen på én gang ved at slette den virtuelle maskine og slette alle tilknyttede diske. Slette en .vhd-fil, der er en kilde til en datadisk kræver dog flere trin i et sæt rækkefølge. Først fjerne disken fra den virtuelle maskine, derefter slette disken, og slet derefter filen .vhd.

>[AZURE.WARNING] Hvis du sletter en .vhd kildefil fra lagerplads eller slette kontoen lagerplads, gendanne ikke Microsoft dataene for dig.



## <a name="next-steps"></a>Næste trin
-  [Vedhæft en disk](virtual-machines-windows-attach-disk-portal.md) tilføje yderligere lagerplads til dit VM.
-  [Overføre en Windows VM billede til Azure](virtual-machines-windows-upload-image.md) skal bruges, når du opretter en ny VM.
-  [Ændre bogstavet for Windows midlertidige disken](virtual-machines-windows-classic-change-drive-letter.md) , så programmet kan bruge D: drev til data.
