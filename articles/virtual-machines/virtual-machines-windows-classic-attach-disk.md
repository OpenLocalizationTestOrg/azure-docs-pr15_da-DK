<properties
    pageTitle="Vedhæfte en disk til en VM | Microsoft Azure"
    description="Vedhæfte en datadisk til en Windows virtuel maskine, der er oprettet med den klassiske implementeringsmodel og starte den."
    services="virtual-machines-windows, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Vedhæfte en datadisk til en Windows virtuel maskine, der er oprettet med den klassiske implementeringsmodel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Hvis du vil bruge den nye portal, skal du se, [hvordan du vedhæfter en datadisk til en Windows-VM i portalen Azure](virtual-machines-windows-attach-disk-portal.md).

Hvis du har brug for en yderligere datadisk, kan du vedhæfte en tom disk eller en eksisterende disk med data til en VM. I begge tilfælde er diskene .vhd filer, der er placeret på en Azure-lager-konto. Hvis det er en ny disk, når du vedhæfte disken, skal også skal du starte den, så det er klar til brug af en Windows-VM.

Se [om og virtuelle harddiske til virtuelle computere](virtual-machines-windows-about-disks-vhds.md)kan finde flere oplysninger om diske.


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Initialisering på disk

1. Oprette forbindelse til den virtuelle maskine. Finde en vejledning, se, [hvordan du logger på en virtuel maskine, der kører Windows Server][logon].

2. Når du logger den virtuelle maskine, Åbn **Server Manager**. Vælg **filen og lagerplads tjenester**i venstre rude.

    ![Åbn Server Manager](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Udvide menuen, og vælg **diske**.

4. Sektionen **diske** viser diskene. I de fleste tilfælde får den disk 0, disk 1 og disk 2. Disk 0 er operativsystemet disk, disk 1 er den midlertidige disk og disk 2 er den datadisk, du lige har knyttet til VM. Den nye datadisk vises partitionen som **Ukendt**. Højreklik på disken, og vælg **initialiseret**.

5.  Du får du besked om, at alle data vil blive slettet, når disken er initialiseret. Klik på **Ja** for at Bekræft advarselsmeddelelsen og initialisering på disk. Når det er fuldført, vises Partion som **GPT**. Højreklik på disken igen, og vælg **Ny lydstyrken**.

6.  Fuldføre guiden ved hjælp af standardværdierne. Når guiden er færdig, vises sektionen **enheder** nye lydstyrken. Disken er nu online og klar til at gemme data.

    ![Lydstyrken initialiseret](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] Størrelsen af VM bestemmer, hvor mange diske du kan vedhæfte til den. Yderligere oplysninger finder du [størrelser til virtuelle computere](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Yderligere ressourcer

[Hvordan du kan afbryde forbindelsen til en disk fra en Windows virtuel maskine](virtual-machines-windows-classic-detach-disk.md)

[Om og virtuelle harddiske til virtuelle maskiner](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
