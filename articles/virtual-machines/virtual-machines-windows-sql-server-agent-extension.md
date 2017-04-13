<properties
    pageTitle="SQL Server Agent udvidelse til SQL Server FOS (ressourcestyring) | Microsoft Azure"
    description="Dette emne beskrives, hvordan du administrerer SQL Server agent udvidelse, som automatiserer bestemte SQL Server-administrationsopgaver. Dette omfatter automatisk sikkerhedskopiering, automatisk rettelse og Azure-tasten samling Integration. Dette emne bruges af Ressourcestyring installation tilstand."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>SQL Server Agent udvidelse til SQL Server FOS (ressourcestyring)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-server-agent-extension.md)
- [Klassisk](virtual-machines-windows-classic-sql-server-agent-extension.md)

I SQL Server IaaS Agent udvidelse (SQLIaaSExtension) kører på Azure virtuelle maskiner til at automatisere administrationsopgaver. Dette emne indeholder en oversigt over de tjenester, der understøttes af lokalnummer samt instruktioner til installation, status og fjernelse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel. Se [SQL Server Agent udvidelse til SQL Server FOS klassisk](virtual-machines-windows-classic-sql-server-agent-extension.md)for at få vist den klassiske version af denne artikel.

## <a name="supported-services"></a>Understøttede tjenester

Filtypenavnet SQL Server IaaS Agent understøtter følgende administrationsopgaver:

| Administration af funktion | Beskrivelse |
|---------------------|-------------------------------|
| **SQL automatisk Backup** | Automatisere planlægningen af sikkerhedskopier for alle databaser standardforekomst af SQL Server i VM. Du kan finde yderligere oplysninger finder [automatiseret sikkerhedskopi til SQL Server på virtuelle Azure-computere (ressourcestyring)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatisk rettelse** | Konfigurerer et vedligeholdelsesvindue, hvor opdateringer til din VM kan ske, så du kan undgå opdateringer under spidsbelastning for din arbejdsbyrde. Du kan finde flere oplysninger, se [automatiseret rettelse til SQL Server på virtuelle Azure-computere (ressourcestyring)](virtual-machines-windows-sql-automated-patching.md).|
| **Azure vigtige samling Integration** | Gør det muligt at installere og konfigurere Azure-tasten samling på din SQL Server VM automatisk. Se [Konfigurere Azure nøgle samling Integration til SQL Server på Azure FOS (ressourcestyring)](virtual-machines-windows-ps-sql-keyvault.md)kan finde flere oplysninger.|

## <a name="prerequisites"></a>Forudsætninger

Krav til brug i SQL Server IaaS Agent udvidelse på din VM:

**Operativsystem**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server-versioner**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Hente og konfigurere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md)

## <a name="installation"></a>Installation

Filtypenavnet SQL Server IaaS Agent installeres automatisk, når du klargør et af SQL Server virtuelt galleriet billederne.

Hvis du opretter et virtuelt OS kun Windows Server, kan du installere filtypenavnet manuelt ved hjælp af **Sæt AzureVMSqlServerExtension** PowerShell-cmdlet. For eksempel følgende kommando installerer filtypenavnet på en OS kun Windows Server VM og giver den "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Hvis du opdaterer til den nyeste version af filtypenavnet SQL IaaS Agent, skal du genstarte computeren virtuelle efter opdatering af filtypenavnet.

>[AZURE.NOTE] Hvis du installerer SQL Server IaaS Agent lokalnummer manuelt på en Windows Server VM, skal du bruge og administrere de tilgængelige funktioner, ved hjælp af PowerShell-kommandoer. Grænsefladen portalen er kun tilgængelig for SQL Server-galleriet billeder.

## <a name="status"></a>Status

En metode til at kontrollere, at filtypenavnet er installeret, er at få vist agent status på portalen Azure. Vælg **alle indstillinger** i bladet virtuelt, og klik derefter på i **filtypenavne**. Du bør se filtypenavnet **SQLIaaSExtension** vises.

![IaaS Agent udvidelsen SQL Server Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Du kan også bruge **Get-AzureVMSqlServerExtension** Azure Powershell-cmdlet.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Den forrige kommando bekræfter agenten er installeret og giver oplysninger om overordnede status. Du kan også finde for bestemte statusoplysninger om automatisk sikkerhedskopiering og rettelses med følgende kommandoer.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Fjern   

Du kan fjerne filtypenavnet ved at klikke på ellipsen på bladet **udvidelser** af virtuelt egenskaber i Azure-portalen. Klik derefter på **Slet**.

![Fjerne udvidelsen til SQL Server IaaS Agent Azure-portalen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Du kan også bruge **Fjern AzureRmVMSqlServerExtension** Powershell-cmdlet.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Næste trin

Begynde at bruge en af de tjenester, der understøttes af filtypenavnet. Yderligere oplysninger finder du de emner, der refereres til i sektionen [understøttes tjenester](#supported-services) i denne artikel.

Du kan finde flere oplysninger om kører SQL Server på virtuelle Azure-computere, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
