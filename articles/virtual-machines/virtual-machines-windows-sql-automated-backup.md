<properties
    pageTitle="Automatisk Backup til SQL Server virtuelle maskiner (ressourcestyring) | Microsoft Azure"
    description="Beskriver funktionen automatisk sikkerhedskopiering for SQL Server, der kører i virtuelle Azure-computere ved hjælp af Ressourcestyring. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisk Backup til SQL Server på Azure virtuelle maskiner (ressourcestyring)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-automated-backup.md)
- [Klassisk](virtual-machines-windows-classic-sql-automated-backup.md)

Automatiseret Backup konfigureres automatisk [Administrerede sikkerhedskopi til Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) for alle eksisterende og nye databaser på en Azure VM, der kører SQL Server 2014 Standard eller Enterprise. Dette gør det muligt at konfigurere almindelig databasesikkerhedskopier, der anvender robust Azure blob-lager. Automatiseret Backup afhænger af [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel. Se [Automatisk sikkerhedskopiering for SQL Server på Azure virtuelle maskiner klassisk](virtual-machines-windows-classic-sql-automated-backup.md)for at få vist den klassiske version af denne artikel.

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge automatiserede sikkerhedskopi, skal du overveje følgende forudsætninger:

**Operativsystem**:

- Windows Server 2012
- Windows Server 2012 R2

**Version/udgave af SQL Server**:

- SQL Server 2014 som Standard
- SQL Server 2014 Enterprise

**Konfiguration af database**:

- Target databaser skal bruge modellen Fuldstændig gendannelse

**Azure PowerShell**:

- [Installere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md) , hvis du vil konfigurere automatisk sikkerhedskopiering med PowerShell.

>[AZURE.NOTE] Automatiseret Backup er afhængig af SQL Server IaaS Agent filtypenavn. Aktuelle SQL virtuelt galleriet billeder tilføje dette lokalnummer som standard. Du kan finde yderligere oplysninger finder [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Indstillinger

I følgende tabel beskrives de indstillinger, der kan konfigureres til automatisk sikkerhedskopiering. Faktisk konfigurationstrinnene varierer afhængigt af om du bruger Azure portal eller Azure Windows PowerShell-kommandoer.

|Indstilling|Område (standard)|Beskrivelse|
|---|---|---|
|**Automatisk sikkerhedskopiering**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer automatisk sikkerhedskopiering for en Azure VM, der kører SQL Server 2014 Standard eller Enterprise.|
|**Opbevaringsperiode**|1-30 dage (30 dage)|Antallet af dage til at bevare en sikkerhedskopi.|
|**Lagerplads konto**|Azure-lager konto (lagerplads-konto, der er oprettet for den angivne VM)|En Azure-lager-konto, der skal bruges til lagring af filer automatisk sikkerhedskopiering i blob-lager. En objektbeholder oprettes på denne placering til at gemme alle sikkerhedskopifilerne. Sikkerhedskopifil navnekonventionen indeholder den dato, klokkeslæt og computernavn.|
|**Kryptering**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer kryptering. Når kryptering er aktiveret, kan de certifikater, der bruges til at gendanne sikkerhedskopien findes i den angivne lagerplads konto i samme automaticbackup beholder ved hjælp af samme navnekonventionen. Hvis adgangskoden ændres, skal der oprettes et nyt certifikat med adgangskoden, men det gamle certifikat, der er tilbage for at gendanne tidligere sikkerhedskopier.|
|**Adgangskode**|Adgangskode tekst (ingen)|En adgangskode til kryptering taster. Denne indstilling er kun påkrævet Hvis kryptering er aktiveret. Hvis du vil gendanne en krypteret sikkerhedskopi, skal du have den korrekt adgangskode og relaterede certifikat, der blev brugt på det tidspunkt, som sikkerhedskopien er taget.|

## <a name="configuration-in-the-portal"></a>Konfiguration af i portalen
Du kan bruge portalen Azure til at konfigurere automatisk sikkerhedskopiering under klargøringen eller til eksisterende FOS.

### <a name="new-vms"></a>Ny FOS
Brug portalen Azure til at konfigurere automatisk sikkerhedskopiering, når du opretter en ny SQL Server 2014 virtuel maskine i implementeringsmodel ressourcestyring.

Vælg **automatisk sikkerhedskopiering**i bladet **SQL Server-indstillingerne** . Følgende Azure portalen skærmbillede viser bladet **SQL automatisk sikkerhedskopiering** .

![Konfiguration af automatisk sikkerhedskopiering af SQL Azure-portalen](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Se emnet fuldført på [klargøring af en SQL Server virtuel maskine i Azure](virtual-machines-windows-portal-sql-server-provision.md)for kontekst.

### <a name="existing-vms"></a>Eksisterende FOS
Vælg din SQL Server virtuelle maskine til eksisterende SQL Server virtuelle maskiner. Vælg derefter **SQL Server-konfiguration** del af bladet **Indstillinger** .

![SQL automatisk sikkerhedskopiering for eksisterende FOS](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Klik på knappen **Rediger** i sektionen automatiseret sikkerhedskopiering i bladet **konfiguration af SQL Server** .

![Konfigurere automatisk sikkerhedskopiering af SQL til eksisterende FOS](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Klik på knappen **OK** nederst på bladet **SQL Server-konfiguration** for at gemme ændringerne, når du er færdig.

Hvis du ønsker at aktivere automatisk sikkerhedskopiering for første gang, konfigurerer Azure SQL Server IaaS Agent i baggrunden. I denne periode vises portalen Azure muligvis ikke, at automatisk sikkerhedskopiering er konfigureret. Vent nogle minutter til agent skal være installeret, konfigureret. Herefter vil portalen Azure afspejle de nye indstillinger.

>[AZURE.NOTE] Du kan også konfigurere automatisk sikkerhedskopiering ved hjælp af en skabelon. Du kan finde yderligere oplysninger finder [Azure Hurtig start skabelon til automatisk sikkerhedskopiering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell

Efter klargøring af din SQL VM kan du bruge PowerShell til at konfigurere automatisk sikkerhedskopiering.

I eksemplet nedenfor PowerShell er automatisk sikkerhedskopiering konfigureret til en eksisterende SQL Server 2014 VM. Kommandoen **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** konfigurerer indstillingerne for automatisk sikkerhedskopiering for at gemme sikkerhedskopier i kontoen Azure-lager, der er knyttet til den virtuelle maskine. Disse sikkerhedskopier bevares for 10 dage. Kommandoen **Sæt AzureRmVMSqlServerExtension** opdaterer den angivne Azure VM med disse indstillinger.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Det kan tage flere minutter at installere og konfigurere SQL Server IaaS Agent.

Ændre det forrige script for at overføre parameteren **EnableEncryption** sammen med en adgangskode (secure streng) for parameteren **CertificatePassword** for at aktivere kryptering. Følgende script aktiverer indstillingerne for automatisk sikkerhedskopiering i det forrige eksempel og tilføjer kryptering.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

For at deaktivere automatisk sikkerhedskopiering skal køre scriptet samme uden at de **-aktivere** -parameter til kommandoen **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** . Ikke findes på **-aktivere** parameter signaler kommandoen til at deaktivere funktionen. Det kan tage flere minutter at deaktivere automatisk sikkerhedskopiering som med installationen.

>[AZURE.NOTE] Fjerne SQL Server IaaS Agent fjerner ikke er konfigureret tidligere automatisk sikkerhedskopiering indstillingerne. Før du deaktivere eller fjerne SQL Server IaaS Agent skal du deaktivere automatisk sikkerhedskopiering.

## <a name="next-steps"></a>Næste trin

Automatiseret Backup konfigurerer administrerede sikkerhedskopi på Azure FOS. Så er det vigtigt at [Gennemgå i dokumentationen til administrerede sikkerhedskopi](https://msdn.microsoft.com/library/dn449496.aspx) at forstå opførsel og konsekvenserne.

Du kan finde yderligere sikkerhedskopi og gendanne vejledning til SQL Server på Azure FOS i følgende emne: [sikkerhedskopiere og gendanne til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md).

Du kan finde oplysninger om andre tilgængelige automatiseringsopgaver, [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-sql-server-agent-extension.md).

Du kan finde flere oplysninger om kører SQL Server på Azure FOS, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
