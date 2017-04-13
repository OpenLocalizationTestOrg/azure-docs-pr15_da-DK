<properties
    pageTitle="Automatisk Backup til SQL Server virtuelle maskiner (klassisk) | Microsoft Azure"
    description="Beskriver funktionen automatisk sikkerhedskopiering for SQL Server, der kører i virtuelle Azure-computere ved hjælp af Ressourcestyring. "
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

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatiseret Backup til SQL Server på Azure virtuelle maskiner (klassisk)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-automated-backup.md)
- [Klassisk](virtual-machines-windows-classic-sql-automated-backup.md)

Automatiseret Backup konfigureres automatisk [Administrerede sikkerhedskopi til Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) for alle eksisterende og nye databaser på en Azure VM, der kører SQL Server 2014 Standard eller Enterprise. Dette gør det muligt at konfigurere almindelig databasesikkerhedskopier, der anvender robust Azure blob-lager. Automatiseret Backup afhænger af [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se [Automatisk sikkerhedskopiering til SQL Server i Azure virtuelle maskiner ressourcestyring](virtual-machines-windows-sql-automated-backup.md)for at få vist ressourcestyring version af denne artikel.

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge automatiserede sikkerhedskopi, skal du overveje følgende forudsætninger:

**Operativsystem**:

- Windows Server 2012
- Windows Server 2012 R2

**Version/udgave af SQL Server**:

- SQL Server 2014 som Standard
- SQL Server 2014 Enterprise

>[AZURE.NOTE] SQL Server 2016 understøttes ikke endnu for automatisk sikkerhedskopiering.

**Konfiguration af database**:

- Target databaser skal bruge modellen Fuldstændig gendannelse.

**Azure PowerShell**:

- [Installere de seneste Azure PowerShell-kommandoer](../powershell-install-configure.md).

**SQL Server IaaS filtypenavn**:

- [Installere udvidelsen IaaS til SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Indstillinger

I følgende tabel beskrives de indstillinger, der kan konfigureres til automatisk sikkerhedskopiering. Klassisk FOS, skal du bruge PowerShell til at konfigurere disse indstillinger.

|Indstilling|Område (standard)|Beskrivelse|
|---|---|---|
|**Automatisk sikkerhedskopiering**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer automatisk sikkerhedskopiering for en Azure VM, der kører SQL Server 2014 Standard eller Enterprise.|
|**Opbevaringsperiode**|1-30 dage (30 dage)|Antallet af dage til at bevare en sikkerhedskopi.|
|**Lagerplads konto**|Azure-lager konto (lagerplads-konto, der er oprettet for den angivne VM)|En Azure-lager-konto, der skal bruges til lagring af filer automatisk sikkerhedskopiering i blob-lager. En objektbeholder oprettes på denne placering til at gemme alle sikkerhedskopifilerne. Sikkerhedskopifil navnekonventionen indeholder den dato, klokkeslæt og computernavn.|
|**Kryptering**|Aktivere/deaktivere (deaktiveret)|Aktiverer eller deaktiverer kryptering. Når kryptering er aktiveret, kan de certifikater, der bruges til at gendanne sikkerhedskopien findes i den angivne lagerplads konto i samme automaticbackup beholder ved hjælp af samme navnekonventionen. Hvis adgangskoden ændres, skal der oprettes et nyt certifikat med adgangskoden, men det gamle certifikat, der er tilbage for at gendanne tidligere sikkerhedskopier.|
|**Adgangskode**|Adgangskode tekst (ingen)|En adgangskode til kryptering taster. Denne indstilling er kun påkrævet Hvis kryptering er aktiveret. Hvis du vil gendanne en krypteret sikkerhedskopi, skal du have den korrekt adgangskode og relaterede certifikat, der blev brugt på det tidspunkt, som sikkerhedskopien er taget.|

## <a name="configuration-with-powershell"></a>Konfiguration med PowerShell

I eksemplet nedenfor PowerShell er automatisk sikkerhedskopiering konfigureret til en eksisterende SQL Server 2014 VM. Kommandoen **Ny AzureVMSqlServerAutoBackupConfig** konfigurerer indstillingerne for automatisk sikkerhedskopiering for at gemme sikkerhedskopier i den angivne ved variablen $storageaccount Azure-lager-konto. Disse sikkerhedskopier bevares for 10 dage. Kommandoen **Sæt AzureVMSqlServerExtension** opdaterer den angivne Azure VM med disse indstillinger.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Det kan tage flere minutter at installere og konfigurere SQL Server IaaS Agent.

For at aktivere kryptering skal du ændre det forrige script for at overføre parameteren EnableEncryption sammen med en adgangskode (secure streng) for parameteren CertificatePassword. Følgende script aktiverer indstillingerne for automatisk sikkerhedskopiering i det forrige eksempel og tilføjer kryptering.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

For at deaktivere automatisk sikkerhedskopiering skal køre scriptet samme uden at de **-aktivere** -parameter til **Ny AzureVMSqlServerAutoBackupConfig**. Det kan tage flere minutter at deaktivere automatisk sikkerhedskopiering som med installationen.

>[AZURE.NOTE] Deaktivere og fjernelse af SQL Server IaaS Agent fjerner ikke er konfigureret tidligere administrerede sikkerhedskopi indstillingerne. Før du deaktivere eller fjerne SQL Server IaaS Agent skal du deaktivere automatisk sikkerhedskopiering.

## <a name="next-steps"></a>Næste trin

Automatiseret Backup konfigurerer administrerede sikkerhedskopi på Azure FOS. Så er det vigtigt at [Gennemgå i dokumentationen til administrerede sikkerhedskopi](https://msdn.microsoft.com/library/dn449496.aspx) at forstå opførsel og konsekvenserne.

Du kan finde yderligere sikkerhedskopi og gendanne vejledning til SQL Server på Azure FOS i følgende emne: [sikkerhedskopiere og gendanne til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md).

Du kan finde oplysninger om andre tilgængelige automatiseringsopgaver, [SQL Server IaaS Agent filtypenavn](virtual-machines-windows-classic-sql-server-agent-extension.md).

Du kan finde flere oplysninger om kører SQL Server på Azure FOS, [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).
