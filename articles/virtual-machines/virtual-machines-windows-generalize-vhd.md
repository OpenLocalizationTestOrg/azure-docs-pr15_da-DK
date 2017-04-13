<properties
    pageTitle="Generaliser Windows Virtuelle | Microsoft Azure"
    description="Lær at bruge Sysprep til generalize en Windows-VM til brug med implementeringsmodel ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalize en Windows virtuel maskine, ved hjælp af Sysprep

Dette afsnit viser, hvordan du generalize din Windows virtuelle maskine til brug som et billede. Sysprep fjerner alle dine personlige oplysninger, blandt andet og laver computer der skal bruges som et billede. Få mere at vide om Sysprep se [Sådan Brug Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

Kontrollér, at de serverroller, der kører på computeren, der understøttes af Sysprep. Du kan finde flere oplysninger, se [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Hvis du kører Sysprep før du overfører din Virtuelle til Azure for første gang, skal du kontrollere, at du har [forberedt din VM](virtual-machines-windows-prepare-for-upload-vhd-image.md) før du kører Sysprep. 

1. Log på Windows-virtuelt.

2. Åbn kommandopromptvinduet som administrator. Ændre mappen til **%windir%\system32\sysprep**og derefter køre `sysprep.exe`.

3. Vælg **Angiv System Out of Box Experience (OOBE)**i dialogboksen **Værktøj til forberedelse af System** , og Sørg for, at afkrydsningsfeltet **generalisere** er markeret.

4. Vælg **Luk computeren**i **Indstillinger for lukning**.

5. Klik på **OK**.

    ![Start Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Når Sysprep er fuldført, lukkes den den virtuelle maskine. 

## <a name="next-steps"></a>Næste trin

- Hvis VM er i det lokale miljø, kan du nu [overføre Virtuelle til Azure](virtual-machines-windows-upload-image.md).
- Hvis VM allerede er i Azure, kan du nu [oprette et billede fra generalized VM](virtual-machines-windows-capture-image.md).