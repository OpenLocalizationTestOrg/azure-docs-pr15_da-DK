<properties
    pageTitle="Log på en klassisk Azure VM | Microsoft Azure"
    description="Bruge klassiske Azure portal til at logge på en Windows-VM, der er oprettet med den klassiske implementeringsmodel."
    services="virtual-machines-windows"
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
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Log på en Windows-VM ved hjælp af den klassiske Azure portal

I den klassiske Azure portal, kan du bruge knappen **Opret forbindelse** til at starte en Fjernskrivebord-session og logge på en Windows-VM.

Vil du oprette forbindelse til en Linux-VM? Se, [hvordan du logger på en virtuel maskine, der kører Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Lær, hvordan du [udfører disse trin ved hjælp af nye Azure portal](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Video gennemgang

Her er en video gennemgang af trinnene i dette selvstudium. Den dækker også slutpunkter og offentlige og private porte, der bruges til at oprette forbindelse til en Windows-VM i Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Oprette forbindelse til den virtuelle maskine

1. Log på Azure klassiske portalen.

2. Klik på **virtuelle maskiner**, og vælg derefter den virtuelle maskine.

3. Klik på **Tilslut**på kommandolinjen i bunden af siden.

    ![Log på den virtuelle maskine](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Hvis knappen **Opret forbindelse** er ikke tilgængelig, kan du se tip til fejlfinding i slutningen af denne artikel.

## <a name="log-on-to-the-virtual-machine"></a>Log på den virtuelle maskine

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Næste trin

-   Hvis knappen **Opret forbindelse** er inaktivt, eller du oplever andre problemer med forbindelsen til fjernskrivebordet, kan du prøve at nulstille konfigurationen. Klik på **Nulstil fjernkonfiguration**under **Hurtig Glance**, fra dashboardet virtuel maskine.
-   Problemer med din adgangskode, kan du prøve at nulstille den. Klik på **Nulstil adgangskode**under **Hurtig Glance**, fra dashboardet virtuel maskine.

Hvis disse tip virker ikke eller ikke, hvad du skal bruge, kan du se [fejlfinding i forbindelse med Fjernskrivebord-forbindelser til en Windows-baseret Azure virtuel maskine](virtual-machines-windows-troubleshoot-rdp-connection.md). Denne artikel hjælper dig med at diagnosticere og løse almindelige problemer.


