<properties
    pageTitle="SQL Server Agent udvidelse til SQL Server FOS (klassisk) | Microsoft Azure"
    description="Dette emne beskrives, hvordan du administrerer SQL Server agent udvidelse, som automatiserer bestemte SQL Server-administrationsopgaver. Dette omfatter automatisk sikkerhedskopiering, automatisk rettelse og Azure-tasten samling Integration. Dette emne bruges tilstanden Klassisk installation."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>SQL Server Agent udvidelse til SQL Server FOS (klassisk)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-server-agent-extension.md)
- [Klassisk](virtual-machines-windows-classic-sql-server-agent-extension.md)

I SQL Server IaaS Agent udvidelse (SQLIaaSAgent) kører på Azure virtuelle maskiner til at automatisere administrationsopgaver. Dette emne indeholder en oversigt over de tjenester, der understøttes af lokalnummer samt instruktioner til installation, status og fjernelse.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se [SQL Server Agent lokalnummer til SQL Server FOS Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)for at få vist ressourcestyring version af denne artikel.

## <a name="supported-services"></a>Understøttede tjenester

Filtypenavnet SQL Server IaaS Agent understøtter følgende administrationsopgaver:

| Administration af funktion | Beskrivelse |
|---------------------|-------------------------------|
| **SQL automatisk Backup** | Automatisere planlægningen af sikkerhedskopier for alle databaser standardforekomst af SQL Server i VM. Du kan finde flere oplysninger, se [automatisk backup til SQL Server på virtuelle Azure-computere (klassisk)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatisk rettelse** | Konfigurerer et vedligeholdelsesvindue, hvor opdateringer til din VM kan ske, så du kan undgå opdateringer under spidsbelastning for din arbejdsbyrde. Du kan finde flere oplysninger, se [automatisk rettelse til SQL Server på virtuelle Azure-computere (klassisk)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Azure vigtige samling Integration** | Gør det muligt at installere og konfigurere Azure-tasten samling på din SQL Server VM automatisk. Se [Konfigurere Azure nøgle samling Integration til SQL Server på Azure FOS (klassisk)](virtual-machines-windows-classic-ps-sql-keyvault.md)kan finde flere oplysninger.|

## <a name="prerequisites"></a>Forudsætninger

Krav til brug i SQL Server IaaS Agent udvidelse på din VM:

### <a name="operating-system"></a>Operativsystem:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>SQL Server-version:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[Hente og konfigurere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md).

Start Windows PowerShell, og forbinde den til abonnementet Azure med kommandoen **Tilføj AzureAccount** .

    Add-AzureAccount

Hvis du har flere abonnementer, kan du bruge **Vælg AzureSubscription** til at markere det abonnement, der indeholder det relevante mål klassisk VM.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

På dette tidspunkt, kan du få en liste over klassisk virtuelle maskiner og deres tilknyttede tjenestenavne med kommandoen **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installation

Klassisk FOS, skal du bruge PowerShell til at installere SQL Server IaaS Agent udvidelsen og konfigurere deres tilknyttede tjenester. Brug **Sæt AzureVMSqlServerExtension** PowerShell-cmdlet til at installere filtypenavnet. For eksempel følgende kommando installerer filtypenavnet på en Windows Server VM (klassisk) og giver den "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Hvis du opdaterer til den nyeste version af filtypenavnet SQL IaaS Agent, skal du genstarte computeren virtuelle efter opdatering af filtypenavnet.

>[AZURE.NOTE] Klassisk virtuelle maskiner har ikke mulighed for at installere og konfigurere filtypenavnet SQL IaaS Agent via portalen.

## <a name="status"></a>Status

En metode til at kontrollere, at filtypenavnet er installeret, er at få vist agent status på portalen Azure. Vælg **alle indstillinger** i bladet virtuelt, og klik derefter på i **filtypenavne**. Du bør se filtypenavnet **SQLIaaSAgent** vises.

![IaaS Agent udvidelsen SQL Server Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Du kan også bruge **Get-AzureVMSqlServerExtension** Azure Powershell-cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Fjern   

Du kan fjerne filtypenavnet ved at klikke på ellipsen på bladet **udvidelser** af virtuelt egenskaber i Azure-portalen. Klik derefter på **Slet**.

![Fjerne udvidelsen til SQL Server IaaS Agent Azure-portalen](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Du kan også bruge **Fjern AzureVMSqlServerExtension** Powershell-cmdlet.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Næste trin

Begynde at bruge en af de tjenester, der understøttes af filtypenavnet. Yderligere oplysninger finder du de emner, der refereres til i sektionen [understøttes tjenester](#supported-services) i denne artikel.

Du kan finde flere oplysninger om kører SQL Server på virtuelle Azure-computere, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
