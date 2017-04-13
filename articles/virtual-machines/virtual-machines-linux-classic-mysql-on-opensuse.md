<properties
    pageTitle="Installere MySQL på en OpenSUSE VM | Microsoft Azure"
    description="Lær at installere MySQL på en OpenSUSE Linux VMirtual maskine i Azure."
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
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installere MySQL på en virtuel maskine kører OpenSUSE Linux i Azure

[MySQL] [ MySQL] er en populære og open source-SQL-database. Dette selvstudium viser, hvordan du opretter en virtuel maskine kører OpenSUSE Linux og derefter installere MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Oprette en virtuel maskine kører OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installere og køre MySQL på den virtuelle maskine

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Næste trin
Få mere at vide om MySQL se [MySQL dokumentation][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

