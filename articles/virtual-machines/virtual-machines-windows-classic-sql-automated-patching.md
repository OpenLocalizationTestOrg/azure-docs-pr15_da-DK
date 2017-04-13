<properties
    pageTitle="Automatisk rettelse til SQL Server FOS (klassisk) | Microsoft Azure"
    description="Beskriver funktionen automatisk rettelse til SQL Server virtuelle maskiner, der kører i Azure ved hjælp af tilstanden Klassisk installation."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisk rettelse til SQL Server på Azure virtuelle maskiner (klassisk)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-automated-patching.md)
- [Klassisk](virtual-machines-windows-classic-sql-automated-patching.md)

Automatiseret rettelses opretter et vedligeholdelsesvindue til en Azure Virtual Machine, kører SQL Server. Automatiske opdateringer kan kun installeres i dette tidsrum. Dette sikrer, at systemopdateringer og alle tilknyttede genstarter opstå på det bedste mulige tidspunkt for databasen til SQL Server. Automatiseret rettelses afhænger af [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]For at få vist ressourcestyring version af denne artikel, se [Automatisk rettelse til SQL Server i Azure virtuelle maskiner ressourcestyring](virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge automatisk rettelse, skal du overveje følgende forudsætninger:

**Operativsystem**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server-version**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Installere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md).

**SQL Server IaaS filtypenavn**:

- [Installere udvidelsen IaaS til SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Indstillinger

I følgende tabel beskrives de indstillinger, der kan konfigureres automatisk rettelse. Klassisk FOS, skal du bruge PowerShell til at konfigurere disse indstillinger.

|Indstilling|Mulige værdier|Beskrivelse|
|---|---|---|
|**Automatisk rettelse**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer automatisk rettelse til en Azure virtual machine.|
|**Tidsplan for vedligeholdelse**|Hver dag, mandag, tirsdag, onsdag, torsdag, fredag, lørdag, søndag|Planen for at hente og installere opdateringer til Windows, SQL Server og Microsoft til din virtuelle maskine.|
|**Vedligeholdelse start time**|0-24|Lokale starttidspunktet til at opdatere den virtuelle maskine.|
|**Vedligeholdelse vinduet varighed**|30-180|Antallet af minutter tilladelse til at fuldføre overførslen og installation af opdateringer.|
|**Programrettelse kategori**|Vigtige|Kategorien opdateringer hentes og installeres.|

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell

I det følgende eksempel bruges PowerShell til at konfigurere automatisk rettelse på en eksisterende SQL Server VM. Kommandoen **Ny AzureVMSqlServerAutoPatchingConfig** konfigurerer et nyt vedligeholdelsesvindue for Automatiske opdateringer.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Baseret på dette eksempel, beskrives i følgende tabel de praktiske indvirkning på destinationen Azure VM:

|Parameter|Effekt|
|---|---|
|**DayOfWeek**|Hver torsdag installeret.|
|**MaintenanceWindowStartingHour**|Start opdateringer på 11:00 am.|
|**MaintenanceWindowsDuration**|Rettelser skal være installeret i 120 minutter. Baseret på starttidspunktet, skal de fuldføre ved 1:00 pm.|
|**PatchCategory**|Den eneste indstilling for denne parameter er "Vigtigt".|

Det kan tage flere minutter at installere og konfigurere SQL Server IaaS Agent.

For at deaktivere automatisk rettelse skal køre den samme scripts uden parameteren - Aktivér til ny-AzureVMSqlServerAutoPatchingConfig. Det kan tage flere minutter at deaktivere automatisk rettelse, som med installationen.

## <a name="next-steps"></a>Næste trin

Du kan finde oplysninger om andre tilgængelige automatiseringsopgaver, [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-classic-sql-server-agent-extension.md).

Du kan finde flere oplysninger om kører SQL Server på Azure FOS, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
