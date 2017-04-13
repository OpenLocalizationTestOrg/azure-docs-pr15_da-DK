<properties
    pageTitle="Oversigt over SQL Server på Azure virtuelle maskiner | Microsoft Azure"
    description="Få mere at vide om at køre komplette versioner af SQL Server på virtuelle Azure-computere. Få direkte links til alle SQL Server VM billeder og relateret indhold."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Oversigt over SQL Server på Azure virtuelle maskiner

Dette emne beskrives indstillingerne for kører SQL Server på Azure virtuelle maskiner (VM'er), samt [links til portalen billeder](#option-1-create-a-sql-vm-with-per-minute-licensing) og en oversigt over [almindelige opgaver](#manage-your-sql-vm).

>[AZURE.NOTE] Hvis du allerede kender SQL Server og blot ønsker at se, hvordan du installerer en SQL Server VM, kan du se [klargøring en SQL Server virtuel maskine i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Oversigt
Hvis du er databaseadministrator af en eller en udvikler, give Azure FOS en metode til at flytte dine lokale SQL Server arbejdsmængder og programmer i skyen. Den følgende video giver en teknisk oversigt over SQL Server Azure FOS.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

Videoen omhandler følgende områder:

|Tid|Område|
|---|---|
| 00:21 | Hvad er Azure FOS? |
| 01:45 | Sikkerhed |
| 02:50 | Forbindelse |
| 03:30 | Lagerplads pålideligheden og ydeevnen |
| 05:20 | VM størrelser |
| 05:54 | Høj tilgængelighed og SERVICENIVEAUAFTALE |
| 07:30 | Understøttelse af konfiguration |
| 08:00 | Overvågning |
| 08:32 | Demo: Oprette en SQL Server 2016 VM |

>[AZURE.NOTE] Videoen fokuserer på SQL Server 2016, men Azure leverer VM billeder i tidligere versioner af SQL Server, herunder 2008, 2012, 2014 og 2016. 

## <a name="scenarios"></a>Scenarier
Der er mange grunde, som du kan vælge at være vært for dine data i Azure. Hvis dit program flyttes til Azure, forbedrer ydeevnen du kan også flytte dataene. Men der er andre fordele. Du har automatisk adgang til flere datacentre for en global tilstedeværelse og genoprettelse efter nedbrud. Data, der er også meget sikret og robust.

SQL Server, der kører på Azure FOS er en af mulighederne for lagring af din relationsdata i Azure. Det er velegnet til flere scenarier. Du vil muligvis konfigurere Azure VM som på samme måde som muligt til en lokal SQL Server-computer. Eller du måske køre ekstra programmer og tjenester på den samme database-server. Der er to primære ressourcer, der kan hjælpe dig med at gennemgå endnu flere scenarier og overvejelser i forbindelse med:

 - [SQL Server på Azure virtuelle maskiner](https://azure.microsoft.com/services/virtual-machines/sql-server/) giver et overblik over de bedste scenarier for brug af SQL Server på Azure FOS. 
 - [Vælg en skybaseret SQL Server option: Azure SQL (PaaS)-Database eller SQL Server på Azure FOS (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) indeholder en detaljeret sammenligning mellem SQL-Database og SQL Server, der kører på en VM.

## <a name="create-a-new-sql-vm"></a>Oprette en ny SQL VM
De følgende afsnit indeholder direkte links til portalen Azure til SQL Server virtuelt galleriet billeder. Afhængigt af det valgte billede kan du enten løn til SQL Server-licensering omkostninger på grundlag-minutters, eller du kan hente din egen licens (BYOL).

Finde en trinvis vejledning til denne proces i dette selvstudium skal [klargøring en SQL Server virtuel maskine i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md). Du skal også gennemgå [ydeevne bedste fremgangsmåder til SQL Server FOS](virtual-machines-windows-sql-performance.md), som forklarer, hvordan du vælger den relevante maskine størrelse og andre funktioner, der er tilgængelige under klargøringen.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Mulighed 1: Oprette en SQL VM med per minutters licenser
Den følgende tabel indeholder en matrix af tilgængelige SQL Server-billeder i galleriet virtuelt. Klik på et link til begynder at oprette en ny SQL VM med angivne version, edition og operativsystem.

|Version|Operativsystem|Edition|
|---|---|---|
|**SQL-2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2) [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2) [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Udviklingscenter](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), og [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL-2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Mulighed 2: Oprette en SQL VM med en eksisterende licens
Du kan også tage dine egne licens (BYOL). I dette scenarie skal betale du kun for VM uden eventuelle yderligere gebyrer for SQL Server-licenser. Hvis du vil bruge din egen licens, kan bruge matrixen af SQL Server-versioner, versioner og operativsystemer nedenfor. I portalen, er disse billednavne præfikset **{BYOL}**.

|Version|Operativsystem|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Hvis du vil bruge BYOL VM billeder, du skal have og Enterprise Agreement med [Licens mobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Du kan også bruge en gyldig licens til version udgaven af SQL Server, du vil bruge. Du skal [angive de nødvendige BYOL oplysninger til Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) om **10** dage efter klargøring af din VM.

## <a name="manage-your-sql-vm"></a>Administrere din SQL VM
Efter klargøring af din SQL Server VM, er der flere valgfrit administrationsopgaver. I mange aspekter du konfigurere og administrere SQL Server præcis, som du vil administrere en lokal SQL Server-forekomst. Nogle opgaver er specifikke for Azure. De følgende afsnit fremhæves nogle af disse områder med links til flere oplysninger.

### <a name="connect-to-the-vm"></a>Oprette forbindelse til VM
En af de mest grundlæggende management trin er at oprette forbindelse til din SQL Server VM gennem værktøjer, såsom SQL Server Management Studio (SSMS). Du kan finde oplysninger om, hvordan du opretter forbindelse til din nye SQL Server VM, [Opret forbindelse til en SQL Server virtuel maskine på Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Overføre dine data
Hvis du har en eksisterende database, skal du vil flytte, til den klargjorte SQL VM. En liste over indstillinger for overførsel og vejledning, kan du se [overføre en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Konfigurere høj tilgængelighed
Hvis du kræver høj tilgængelighed, kan du overveje at konfiguration af SQL Server tilgængelighed grupper. Dette omfatter flere Azure FOS i et virtuelt netværk. Portalen Azure har en skabelon, der konfigurerer denne konfiguration for dig. Se [konfigurere en AlwaysOn tilgængelighed gruppe på Azure ressourcestyring virtuelle computere](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)kan finde flere oplysninger. Hvis du vil konfigurere din tilgængelighed gruppe og tilknyttede lytteren manuelt, skal du se [Konfigurere AlwaysOn tilgængelighed grupper i Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Se andre høj tilgængelighed forhold til [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Sikkerhedskopiere dine data
Azure FOS kan drage fordel af [Automatisk sikkerhedskopiering](virtual-machines-windows-sql-automated-backup.md), der jævnligt opretter sikkerhedskopier af databasen til blob-lager. Du kan bruge denne teknik også manuelt. Du kan finde flere oplysninger i afsnittet [Brug Azure lageret til SQL serversikkerhedskopiering og gendannelse](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Se en oversigt over alle indstillinger for sikkerhedskopiering og gendannelse, [Sikkerhedskopiering og gendannelse til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatisere opdateringer
Azure FOS kan bruge [Automatisk rettelse](virtual-machines-windows-sql-automated-patching.md) til at planlægge et vedligeholdelsesvindue til at installere vigtige windows og SQL Server opdateres automatisk.

### <a name="customer-experience-improvement-program-ceip"></a>Program til forbedring af brugeroplevelsen (CEIP)
Kunde programmet til forbedring af kundeoplevelsen (CEIP) er aktiveret som standard. Dette sender med jævne mellemrum rapporter til Microsoft for at forbedre SQL Server. Der findes ingen styring opgave, der er påkrævet med CEIP, medmindre du vil deaktivere efter klargøring af. Du kan tilpasse eller deaktivere CEIP ved at oprette forbindelse til VM med Fjernskrivebord. Derefter Kør værktøjet **SQL Server-fejl og rapportering om anvendelse** . Følg vejledningen for at deaktivere rapportering. 

Du kan finde flere oplysninger i afsnittet CEIP i emnet [Acceptér licensvilkårene](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Næste trin
[Gennemse læringssti](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) til SQL Server på Azure virtuelle computere.

Flere spørgsmål? Først skal du se [SQL Server på Azure virtuelle maskiner ofte stillede spørgsmål](virtual-machines-windows-sql-server-iaas-faq.md). Men også tilføje din spørgsmål eller kommentarer til bunden af enhver SQL VM emner til at interagere med Microsoft og gruppen.
