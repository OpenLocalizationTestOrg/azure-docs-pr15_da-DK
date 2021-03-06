<properties
    pageTitle="Oprette forbindelse Windows FOS i en skybaseret tjeneste | Microsoft Azure"
    description="Oprette forbindelse Windows virtuelle maskiner, der er oprettet med den klassiske implementeringsmodel til en Azure skybaseret tjeneste eller virtuelt netværk."
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

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Oprette forbindelse Windows virtuelle maskiner, der er oprettet med klassisk installation modellen med en virtuel netværk eller skybaseret tjeneste

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Windows virtuelle maskiner, der er oprettet med den klassiske implementeringsmodel placeres altid i en skybaseret tjeneste. Skytjenesten fungerer som en beholder og giver et entydigt offentlige DNS-navn, en offentlig IP-adresse og et sæt af slutpunkter for at få adgang til den virtuelle maskine via internettet. Skytjenesten kan være i et virtuelt netværk, men det er ikke et krav. Du kan også [oprette forbindelse Linux virtuelle maskiner med en virtuel netværk eller skybaseret tjeneste](virtual-machines-linux-classic-connect-vms.md).

Hvis der ikke er en skybaseret tjeneste i et virtuelt netværk, skal kaldes den en *enkeltstående* skybaseret tjeneste. Virtuelle maskiner i en enkeltstående skybaseret tjeneste kun kan kommunikere med andre virtuelle maskiner ved hjælp af de andre virtuelle maskiner offentlige DNS-navne, og trafikken sendes via internettet. Hvis en skybaseret tjeneste er i et virtuelt netværk og virtuelle maskiner, i den valgte skybaseret tjeneste kan kommunikere med alle andre virtuelle maskiner i det virtuelle netværk uden at sende en hvilken som helst trafik via internettet.

Hvis du markerer virtuelle maskiner i samme enkeltstående skybaseret tjeneste, kan du stadig bruge justering af belastning og tilgængelighed sæt. Yderligere oplysninger finder du [belastning virtuelle maskiner](virtual-machines-windows-load-balance.md) og [administrere tilgængeligheden af virtuelle maskiner](virtual-machines-windows-manage-availability.md). Du kan ikke organisere virtuelle maskiner på undernet eller oprette en enkeltstående skybaseret tjeneste forbindelse til dit lokale netværk. Her er et eksempel:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Næste trin

Når du opretter en virtuel maskine, er det en god ide at [føje data disken](virtual-machines-windows-classic-attach-disk.md) , så dine tjenester og arbejdsbelastninger, som har en placering til lagring af data. 