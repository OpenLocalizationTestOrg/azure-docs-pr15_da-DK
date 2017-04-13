<properties
    pageTitle="Konfigurere slutpunkterne på en klassisk Windows VM | Microsoft Azure"
    description="Lær at oprette slutpunkter til en Windows-VM i portalen Azure klassisk tillade kommunikation med en Windows virtuel maskine i Azure."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Hvordan du konfigurerer slutpunkterne på en klassisk Windows virtuel maskine i Azure


Alle vinduer virtuelle maskiner, som du opretter i Azure ved hjælp af den klassiske implementeringsmodel automatisk kan kommunikere via en privat netværk kanal med andre virtuelle maskiner i den samme skytjeneste eller virtuelt netværk. Computere på internettet eller andre virtuelle netværk kræver dog slutpunkter til at dirigere indgående netværkstrafikken til en virtuel maskine. I denne artikel findes også for [Linux virtuelle computere](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]I implementeringsmodel **Ressourcestyring** er slutpunkter konfigureret ved hjælp af **Netværk sikkerhedsgrupper (NSGs)**. Få mere at vide under [Tillad ekstern adgang til din VM ved hjælp af portalen Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Når du opretter en Windows virtuel maskine i portalen Azure klassisk, oprettes almindelige slutpunkter som dem for Fjernskrivebord og Windows PowerShell Remoting typisk for dig automatisk. Du kan konfigurere yderligere slutpunkter, mens du opretter den virtuelle maskine eller senere efter behov.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Næste trin

* For at bruge en Azure PowerShell-cmdlet til at konfigurere et VM slutpunkt skal du se [Tilføj AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* For at bruge en Azure PowerShell-cmdlet til at administrere en ACL på et slutpunkt skal du se [administrere adgangskontrol lister (ACLs) for slutpunkter ved hjælp af PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Hvis du har oprettet en virtuel maskine i implementeringsmodel Ressourcestyring, kan du bruge Azure PowerShell til at [oprette netværk sikkerhedsgrupper](../virtual-network/virtual-networks-create-nsg-arm-ps.md) til kontrolelementet trafik til VM.
