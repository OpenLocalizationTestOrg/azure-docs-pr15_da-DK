<properties
    pageTitle="SQL Server på Azure virtuelle maskiner ofte stillede spørgsmål om | Microsoft Azure"
    description="I denne artikel indeholder svar på ofte stillede spørgsmål om kører SQL Server på Azure FOS."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server på Azure virtuelle maskiner ofte stillede spørgsmål

Dette emne indeholder svar på nogle af de mest almindelige spørgsmål om at køre [SQL Server på virtuelle Azure-computere](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

1. **Hvordan opretter jeg en Azure virtuelt med SQL Server?**

    Der er to måder at gøre dette. Den nemmeste løsning er at oprette en virtuel maskine, der indeholder SQL Server. Du kan finde en selvstudium på tilmelding til Azure og oprette en SQL VM fra portalen [klargøring en SQL Server virtuel maskine i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md). Du har også mulighed for at installere SQL Server på en VM og genbruge en lokal licens med [Licens mobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/)manuelt.

1. **Hvad er forskellen mellem SQL FOS og SQL-Database-tjenesten?**

    Objekter kører SQL Server på en Azure virtuelt er ikke det adskiller sig fra kører SQL Server i en ekstern datacenter. [SQL-Database](../sql-database/sql-database-technical-overview.md) indeholder derimod database som en tjeneste. Med SQL-Database har du ikke adgang til de computere, der hoster dine databaser. Finde en fuld sammenligning [Vælg en skybaseret SQL Server option: Azure SQL (PaaS)-Database eller SQL Server på Azure FOS (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Hvordan kan jeg overføre mit lokal SQL Server-database til skyen?**

    Opret først en Azure virtuelt med en forekomst af SQL Server. Derefter overføre dine lokale databaser til den pågældende forekomst. Du kan finde data overførsel strategier kan [overføre en SQL Server-database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

2. **Kan jeg ændre de installerede funktioner eller installere en anden forekomst af SQL Server på den samme VM?**

    Ja. Installationsmedierne til SQL Server er placeret i en mappe på **C** -drevet. Kør **Setup.exe** fra den pågældende placering til at tilføje nye forekomster af SQL Server eller ændre andre installerede funktioner i SQL Server på computeren.

3. **Hvordan opgraderer jeg til en ny version/udgave af SQL Server på en Azure VM?**

    Der er i øjeblikket ingen direkte opgradering til SQL Server, der kører i en Azure VM. Oprette en ny Azure virtuel maskine med den ønskede version/udgave af SQL Server, og derefter overføre dine databaser til den nye server ved hjælp af standard [data overførsel teknikker](virtual-machines-windows-migrate-sql.md).

4. **Hvordan kan jeg installere min licenseret kopi af SQL Server på en Azure VM?**

    Kopiér installationsmedierne til SQL Server til Windows Server VM, og derefter installere SQL Server på VM. Licensering årsager, skal du have [Licens mobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Har du betale SQL-omkostningerne ved en VM, hvis det er kun bruges til standby/failover?**

    Hvis du opretter den SQL VM via galleriet, skal du have en separat licens til standby SQL VM, og de priser er den samme. Hvis du installerer SQL Server manuelt på en virtuel maskine med [Licens mobilitet](https://azure.microsoft.com/pricing/license-mobility/), har du mulighed for at have en gratis passive SQL forekomst for failover. Teksten gennemse kravene og begrænsningerne.

6. **Hvordan opdateringer og servicepakker anvendes på en SQL Server VM?**

    Virtuelle maskiner giver dig kontrol over værtsmaskinen, herunder hvornår og hvordan du anvender opdateringer. Du kan anvende windows-opdateringer manuelt for operativsystemet, eller du kan aktivere en planlægning tjeneste kaldet [Automatisk rettelse](virtual-machines-windows-classic-sql-automated-patching.md). Automatisk rettelses installationer eventuelle opdateringer, der er markeret vigtigt, herunder SQL Server-opdateringer i den pågældende kategori. Andre valgfrie opdateringer til SQL Server skal være installeret manuelt.

7. **Er det muligt at oprette konfigurationer, der ikke vises i galleriet virtuelt (For eksempel Windows 2008 R2 + SQL Server 2012)?**

    Nej. For virtuelt galleriet billeder, der omfatter SQL Server, skal du vælge en af de medfølgende billeder.

9. **Hvordan installerer jeg SQL Dataværktøjer på min Azure VM?**

    Hent og Installer værktøjerne SQL Data fra [Microsoft SQL Server Data Tools – Business Intelligence til Visual Studio-2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Ressourcer

Se den video, [Azure VM er den bedste platform til SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)for en oversigt over SQL Server på virtuelle Azure-computere. Du kan også få en god introduktion i emnet [SQL Server på virtuelle Azure-computere oversigt](virtual-machines-windows-sql-server-iaas-overview.md).

Andre ressourcer omfatter:

- [Klargøre en SQL Server virtuel maskine i portalen Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Overflytte en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md)
- [Høj tilgængelighed og nedbrud for SQL Server på Azure virtuelle computere](virtual-machines-windows-sql-high-availability-dr.md)
- [Ydeevnen bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md)
- [Programmet mønstre og udviklingsstrategier til SQL Server på Azure virtuelle computere](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
