<properties
    pageTitle="Oprette en VM i portalen klassisk | Microsoft Azure"
    description="Oprette en Windows virtuel maskine i portalen Azure klassisk."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Oprette en virtuel maskine, der kører Windows Azure klassisk-portalen

> [AZURE.SELECTOR]
- [Azure klassisk portal](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Klassisk installation](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring implementeringsmodel](virtual-machines-windows-hero-tutorial.md) ved hjælp af **nye Azure portal**. 

Dette selvstudium viser, hvordan du opretter en Azure virtuelt (VM), der kører Windows Azure klassisk portalen. Vi bruger en afbildning af Windows Server som et eksempel, men det er kun én af de mange billeder Azure tilbyder. Bemærk, at dit billede valg afhænger af dit abonnement. Windows desktop billeder kan for eksempel være tilgængelige for MSDN-abonnenter.

Dette afsnit beskrives, hvordan du bruger indstillingen **Fra galleriet** i portalen Azure klassisk til at oprette den virtuelle maskine. Denne indstilling giver flere konfigurationsmuligheder end indstillingen **Hurtig oprettelse** . Eksempelvis hvis du vil deltage i et virtuelt til et virtuelt netværk, skal du bruge indstillingen **Fra galleriet** .

Du kan også oprette FOS ved hjælp af [dine egne billeder](virtual-machines-windows-classic-createupload-vhd.md). Hvis du vil vide mere om dette og andre metoder, du se [forskellige måder at oprette en Windows virtuel maskine](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Video gennemgang

Her er en gennemgang af dette selvstudium.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Oprette den virtuelle maskine

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [opretter en VM ved hjælp af Ressourcestyring implementeringsmodel](virtual-machines-windows-hero-tutorial.md) i den nye Azure portal. 

- Log på den virtuelle maskine. Yderligere oplysninger finder du [logge på en virtuel maskine, der kører Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Vedhæfte en disk for at gemme data. Du kan vedhæfte både tomme og diske, der indeholder data. Flere oplysninger under [vedhæfte en datadisk til en Windows virtuel maskine, der er oprettet med den klassiske implementeringsmodel](virtual-machines-windows-classic-attach-disk.md).
