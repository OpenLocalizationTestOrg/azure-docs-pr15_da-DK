<properties
    pageTitle="Installere MongoDB på en Windows-VM | Microsoft Azure"
    description="Lær, hvordan du installerer MongoDB på en Azure VM, der er oprettet med den klassiske implementeringsmodel, der kører Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Installere MongoDB på en Windows-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se [denne artikel](virtual-machines-windows-classic-install-mongodb.md)for at installere og konfigurere MongoDB ved hjælp af Ressourcestyring implementeringsmodel.

[MongoDB] [ MongoDB] er en populære open source-, høj ydeevne NoSQL database. I denne artikel hjælper dig med at oprette en virtuel maskine til Windows Server (VM), ved hjælp af [Azure klassisk portal][AzurePortal]. Du kan derefter opretter og vedhæfte en datadisk til VM før installation og konfiguration af MongoDB. Hvis du har en eksisterende VM i Azure, du vil bruge, kan du hoppe direkte til [installation og konfiguration af MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Oprette en virtuel maskine, der kører Windows Server

Følg disse instruktioner til at oprette en virtuel maskine.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Du kan tilføje et slutpunkt for MongoDB, mens du opretter den virtuelle maskine, og Konfigurer den på følgende måde: kald den som **Mongo**, bruge **TCP** som protokollen, og Angiv begge de offentlige og private porte til **27017**.

## <a name="attach-a-data-disk"></a>Vedhæfte en datadisk
Vedhæfte en datadisk for at give lager til den virtuelle maskine, og derefter starte den, så Windows kan bruge den. Hvis du allerede har en datadisk, du kan vedhæfte eksisterende disken, eller du kan vedhæfte en tom disk.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Vejledning til, under initialisering af disken, se "Sådan: Initialisering af en ny datadisk i Windows Server" i [hvordan du vedhæfter en datadisk til en Windows virtuel maskine](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installere og køre MongoDB på den virtuelle maskine

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Oversigt
I dette selvstudium, du har lært hvordan du opretter en virtuel maskine, der kører Windows Server, oprette forbindelse til den fra en fjernplacering og vedhæfte en datadisk.  Du kan også lært, hvordan du installerer og konfigurerer MongoDB på en Windows-baseret virtuel maskine. Du kan nu få adgang til MongoDB på virtuelle Windows-baseret computer ved at følge de avancerede emner i [MongoDB dokumentation][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
