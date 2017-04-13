<properties
    pageTitle="Konfigurere slutpunkterne på en klassisk Linux VM | Microsoft Azure"
    description="Lær, hvordan du konfigureret slutpunkter for en Linux VM i portalen Azure klassisk til at tillade kommunikation med en Linux virtuel maskine i Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Hvordan du konfigurerer slutpunkterne på en Linux klassisk virtuel maskine i Azure

Alle Linux virtuelle maskiner, som du opretter i Azure ved hjælp af den klassiske implementeringsmodel kan automatisk kommunikere via en privat netværk kanal med andre virtuelle maskiner i den samme skytjeneste eller virtuelt netværk. Computere på internettet eller andre virtuelle netværk kræver dog slutpunkter til at dirigere indgående netværkstrafikken til en virtuel maskine. I denne artikel findes også for [Windows virtuelle computere](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]I implementeringsmodel **Ressourcestyring** er slutpunkter konfigureret ved hjælp af **Netværk sikkerhedsgrupper (NSGs)**. Få mere at vide under [åbning af porte og slutpunkter] (virtuelle-maskiner-linux-nsg-quickstart.md).

Når du opretter en Linux virtuel maskine i portalen Azure klassisk, oprettes et slutpunkt for Secure Shell (SSH) er typisk for dig automatisk. Du kan konfigurere yderligere slutpunkter, mens du opretter den virtuelle maskine eller senere efter behov.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Næste trin

* Du kan også oprette et VM slutpunkt ved hjælp af [Azure kommandolinjen](../virtual-machines-command-line-tools.md). Kør kommandoen **azure vm slutpunkt oprette** .

* Hvis du har oprettet en virtuel maskine i implementeringsmodel Ressourcestyring, kan du bruge Azure CLI i ressourcestyring tilstand til at [oprette netværk sikkerhedsgrupper](../virtual-network/virtual-networks-create-nsg-arm-cli.md) til kontrolelementet trafik til VM.