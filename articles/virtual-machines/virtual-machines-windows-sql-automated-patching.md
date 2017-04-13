<properties
    pageTitle="Automatisk rettelse til SQL Server FOS (ressourcestyring) | Microsoft Azure"
    description="Beskriver funktionen automatisk rettelse til SQL Server virtuelle maskiner, der kører i Azure ved hjælp af Ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk rettelse til SQL Server på Azure virtuelle maskiner (ressourcestyring)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-automated-patching.md)
- [Klassisk](virtual-machines-windows-classic-sql-automated-patching.md)

Automatiseret rettelses opretter et vedligeholdelsesvindue til en Azure Virtual Machine, kører SQL Server. Automatiske opdateringer kan kun installeres i dette tidsrum. Denne rescriction sikrer, at systemopdateringer og alle tilknyttede genstarter opstå på det bedste mulige tidspunkt for databasen til SQL Server. Automatiseret rettelses afhænger af [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel. For at få vist den klassiske version af denne artikel, se [Automatisk rettelse til SQL Server på Azure virtuelle maskiner klassisk](virtual-machines-windows-classic-sql-automated-patching.md).

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

- [Installere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md) , hvis du vil konfigurere automatisk rettelse med PowerShell.

>[AZURE.NOTE] Automatiseret rettelses er afhængig af SQL Server IaaS Agent filtypenavn. Aktuelle SQL virtuelt galleriet billeder tilføje dette lokalnummer som standard. Du kan finde yderligere oplysninger finder [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Indstillinger

I følgende tabel beskrives de indstillinger, der kan konfigureres automatisk rettelse. Faktisk konfigurationstrinnene varierer afhængigt af om du bruger Azure portal eller Azure Windows PowerShell-kommandoer.

|Indstilling|Mulige værdier|Beskrivelse|
|---|---|---|
|**Automatisk rettelse**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer automatisk rettelse til en Azure virtual machine.|
|**Tidsplan for vedligeholdelse**|Hver dag, mandag, tirsdag, onsdag, torsdag, fredag, lørdag, søndag|Hente og installere opdateringer til Windows, SQL Server og Microsoft til din virtuelle maskine tidsplanen.|
|**Vedligeholdelse start time**|0-24|Lokale starttidspunktet til at opdatere den virtuelle maskine.|
|**Vedligeholdelse vinduet varighed**|30-180|Antallet af minutter tilladelse til at fuldføre overførslen og installation af opdateringer.|
|**Programrettelse kategori**|Vigtige|Kategorien opdateringer hentes og installeres.|

## <a name="configuration-in-the-portal"></a>Konfiguration af i portalen
Du kan bruge Azure portalen skal du konfigurere automatisk rettelse under klargøringen eller til eksisterende FOS.

### <a name="new-vms"></a>Ny FOS
Brug portalen Azure til at konfigurere automatisk rettelse, når du opretter en ny SQL Server virtuel maskine i implementeringsmodel ressourcestyring.

Vælg **automatiske rettelser**i bladet **SQL Server-indstillingerne** . Følgende Azure portalen skærmbillede viser bladet **SQL automatisk rettelse** .

![Automatisk rettelse SQL Azure-portalen](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Se emnet fuldført på [klargøring af en SQL Server virtuel maskine i Azure](virtual-machines-windows-portal-sql-server-provision.md)for kontekst.

### <a name="existing-vms"></a>Eksisterende FOS
Vælg din SQL Server virtuelle maskine til eksisterende SQL Server virtuelle maskiner. Vælg derefter **SQL Server-konfiguration** del af bladet **Indstillinger** .

![SQL automatisk rettelse til eksisterende FOS](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Klik på knappen **Rediger** i den automatiske rettelser sektion i bladet **konfiguration af SQL Server** .

![Konfigurere SQL automatisk rettelse til eksisterende FOS](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Klik på knappen **OK** nederst på bladet **SQL Server-konfiguration** for at gemme ændringerne, når du er færdig.

Hvis du ønsker at aktivere automatisk rettelse for første gang, konfigurerer Azure SQL Server IaaS Agent i baggrunden. I denne periode vises portalen Azure muligvis ikke, at automatisk rettelse er konfigureret. Vent nogle minutter til agent skal være installeret, konfigureret. Herefter portalen Azure afspejler de nye indstillinger.

>[AZURE.NOTE] Du kan også konfigurere automatisk rettelse ved hjælp af en skabelon. Du kan finde yderligere oplysninger finder [Azure Hurtig start skabelon til automatisk rettelse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell

Efter klargøring af din SQL VM kan du bruge PowerShell til at konfigurere automatisk rettelse.

I det følgende eksempel bruges PowerShell til at konfigurere automatisk rettelse på en eksisterende SQL Server VM. Kommandoen **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** konfigurerer et nyt vedligeholdelsesvindue for Automatiske opdateringer.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Baseret på dette eksempel, beskrives i følgende tabel de praktiske indvirkning på destinationen Azure VM:

|Parameter|Effekt|
|---|---|
|**DayOfWeek**|Hver torsdag installeret.|
|**MaintenanceWindowStartingHour**|Start opdateringer på 11:00 am.|
|**MaintenanceWindowsDuration**|Rettelser skal være installeret i 120 minutter. Baseret på starttidspunktet, skal de fuldføre ved 1:00 pm.|
|**PatchCategory**|Den eneste indstilling for denne parameter er **vigtigt**.|

Det kan tage flere minutter at installere og konfigurere SQL Server IaaS Agent.

For at deaktivere automatisk rettelse skal køre scriptet samme uden at de **-aktivere** -parameter til **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. Ikke findes på **-aktivere** parameter signaler kommandoen til at deaktivere funktionen.

## <a name="next-steps"></a>Næste trin

Du kan finde oplysninger om andre tilgængelige automatiseringsopgaver, [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

Du kan finde flere oplysninger om kører SQL Server på Azure FOS, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
