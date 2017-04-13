<properties
    pageTitle="Sikkerhedskopiere og gendanne til SQL Server | Microsoft Azure"
    description="I denne artikel beskrives sikkerhedskopierings- og overvejelser i forbindelse med SQL Server-databaser, der kører på virtuelle Azure-computere."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sikkerhedskopiere og gendanne til SQL Server på Azure virtuelle computere

## <a name="overview"></a>Oversigt

Sikkerhedskopiere data i SQL Server-databaser er en vigtig del af strategien i beskyttelse mod datatab på grund af programmet eller bruger fejl. Dette er lige sand for SQL Server, der kører på Azure virtuelle maskiner (VM'er).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Til SQL Server, der kører i Azure FOS, kan du bruge oprindelige sikkerhedskopiering og gendanne teknikker med tilsluttede diske til destinationen for sikkerhedskopifilerne. Der er dog en grænse for antallet af diske, du kan vedhæfte til en Azure virtuelt, baseret på [størrelsen af den virtuelle maskine](virtual-machines-linux-sizes.md). Der er også spild af Diskadministration skal du tænke på.

Begynder med SQL Server-2014, kan du sikkerhedskopiere og gendanne til Microsoft Azure Blob-lager. SQL Server 2016 giver også forbedringer til denne indstilling. Desuden for databasefiler, der er gemt i Microsoft Azure Blob-lager, indeholder SQL Server 2016 en indstilling til næsten omgående sikkerhedskopier og hurtig gendanner ved hjælp af Azure snapshots. I denne artikel indeholder en oversigt over disse indstillinger, og du kan finde flere oplysninger på [SQL serversikkerhedskopiering og gendannelse med Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Du kan finde en beskrivelse af indstillingerne for sikkerhedskopiering af meget store databaser, [Flere TB SQL Server Database sikkerhedskopi strategier for virtuelle Azure-computere](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

I nedenstående afsnit indeholder specifikke oplysninger om de forskellige versioner af SQL Server, som understøttes i en Azure virtuelt.

## <a name="sql-server-virtual-machines"></a>SQL Server virtuelle maskiner

Når din SQL Server-forekomst kører på en Azure Virtual Machine, findes databasefilerne allerede på data diske i Azure. Disketterne direkte i Azure Blob-lager. Så tage grunde til at sikkerhedskopiere din database og fremgangsmåder du Skift en smule. Overvej følgende. 

- Du ikke længere skal bruge til at udføre databasesikkerhedskopier for at give beskyttelse mod hardware eller medier, fordi Microsoft Azure leverer adgangskodebeskyttelsen som en del af tjenesten Microsoft Azure.

- Du stadig nødt til at udføre databasesikkerhedskopier for at give beskyttelse mod brugerfejl eller til arkivering, lovmæssige årsager eller administrative formål.

- Du kan gemme den sikkerhedskopifil direkte i Azure. Du kan finde yderligere oplysninger i de følgende afsnit, som giver vejledning til de forskellige versioner af SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 understøtter funktioner til [Sikkerhedskopiering og gendannelse med Azure BLOB](https://msdn.microsoft.com/library/jj919148.aspx) findes i SQL Server-2014. Men det indeholder også følgende forbedringer:

| 2016 udvidede               | Detaljer                          |
|---------------------|-------------------------------|
| **Striping**              | Når du sikkerhedskopierer til Microsoft Azure blob-lager, understøtter SQL Server 2016 sikkerhedskopiering op til flere BLOB til at aktivere sikkerhedskopiering af store databaser, op til maksimalt 12,8 TB.      |
| **Øjebliksbillede sikkerhedskopi**                | Ved hjælp af Azure snapshots indeholder sikkerhedskopiering af SQL Server-fil øjebliksbillede næsten omgående sikkerhedskopier og hurtig gendanner for databasefiler gemt ved hjælp af tjenesten Azure Blob-lager. Denne funktion gør det muligt at forenkle dine sikkerhedskopiering og gendannelse politikker. Fil-snapshot sikkerhedskopi understøtter også punkt i tiden gendannelsen. Du kan finde yderligere oplysninger finder [Øjebliksbillede sikkerhedskopier for databasefiler i Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Administrerede sikkerhedskopiering planlægning**            | SQL Server administrerede Backup til Azure understøtter nu brugerdefinerede tidsplaner. Du kan finde yderligere oplysninger finder [SQL Server administrerede Backup til Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Se et selvstudium af funktionerne i SQL Server 2016, når du bruger Azure Blob-lager, [Selvstudium: Brug af tjenesten Microsoft Azure Blob storage med SQL Server 2016 databaser](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 omfatter følgende forbedringer:

1. **Sikkerhedskopiere og gendanne til Azure**:

 - *SQL Server Backup til URL-adresse* har nu support i SQL Server Management Studio. Muligheden for at sikkerhedskopiere til Azure er nu tilgængelig, når du bruger sikkerhedskopiering eller gendannelse opgave eller vedligeholdelse plan søgning i SQL Server Management Studio. Du kan finde yderligere oplysninger finder [Sikkerhedskopiering af SQL Server til URL-adresse](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server administrerede sikkerhedskopiering til Azure* har nye funktioner, der gør det muligt for automatisk sikkerhedskopiering administration. Dette er især nyttig til automatisering sikkerhedskopiering administration for SQL Server 2014 forekomster, der kører på en Azure-computer. Du kan finde flere oplysninger [SQL Server administrerede Backup til Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Automatisk sikkerhedskopiering* giver yderligere automatisering for at aktivere automatisk *SQL Server administrerede Backup til Azure* på alle eksisterende og nye databaser til en SQL Server VM i Azure. Du kan finde yderligere oplysninger finder [Automatisk sikkerhedskopiering til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-automated-backup.md).
 - Se en oversigt over alle indstillingerne for sikkerhedskopiering af SQL Server 2014 til Azure [SQL serversikkerhedskopiering og gendannelse med Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Kryptering**: SQL Server 2014 understøtter kryptering af data, når du opretter en sikkerhedskopi. Den understøtter flere krypteringsalgoritmer og brug osf et certifikat eller asymmetrisk nøgle. Du kan finde yderligere oplysninger finder [Sikkerhedskopi kryptering](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Se [sikkerhedskopiere og gendanne af SQL Server-databaser (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)kan finde detaljerede oplysninger om SQL serversikkerhedskopiering og gendannelse i SQL Server 2012.

Starter i SQL Server 2012 SP1 akkumuleret opdatering 2, kan du sikkerhedskopiere til og gendanne fra tjenesten Azure Blob-lager. Denne indstilling kan bruges til at sikkerhedskopiere SQL Server-databaser på en SQL Server, der kører på en Azure virtuel computer eller en lokal forekomst. Se [SQL serversikkerhedskopiering og gendannelse med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)kan finde flere oplysninger.

Nogle af fordelene ved at bruge tjenesten Azure Blob storage omfatter muligheden for at tilsidesætte 16 disk grænsen for vedhæftede disk brugervenlighed management, direkte tilgængeligheden af den sikkerhedskopifil til en anden forekomst af SQL Server-forekomst, der kører på en Azure virtuel maskine eller en lokal forekomst til overførsel eller nedbrud gendannelse formål. En komplet liste over fordele ved at bruge en Azure blob storage tjeneste for sikkerhedskopiering af SQL Server, i afsnittet *fordele* i [SQL serversikkerhedskopiering og gendannelse med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Se [sikkerhedskopiere og gendanne bedste fremgangsmåder (Azure Blob Storage Service)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)til anbefalinger til de bedste fremgangsmåder og oplysninger om fejlfinding.

## <a name="sql-server-2008"></a>SQL Server 2008

Se til SQL serversikkerhedskopiering og gendannelse i SQL Server 2008 R2, [sikkerhedskopiere og gendanne databaser i SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Se til SQL serversikkerhedskopiering og gendannelse i SQL Server 2008, [sikkerhedskopiere og gendanne databaser i SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Næste trin

Hvis du planlægger installationen af SQL Server på en Azure VM, kan du finde klargøring vejledning i de følgende Selvstudium: [klargøring af en SQL Server virtuel maskine på Azure med Azure ressourcestyring](virtual-machines-windows-portal-sql-server-provision.md).

Selvom sikkerhedskopierings- og kan bruges til at overføre dine data, der potentielt nemmere data overførsel stier til SQL Server på en Azure VM. Fuld finde en beskrivelse af indstillinger for overførsel og anbefalinger, [overføre en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

Gennemse andre [ressourcer til kørsel af SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
