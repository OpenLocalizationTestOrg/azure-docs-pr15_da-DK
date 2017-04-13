<properties
    pageTitle="Administrere Azure SQL-databaser ved hjælp af Azure automatisering | Microsoft Azure"
    description="Få mere at vide om, hvordan Azure Automation service kan bruges til at administrere Azure SQL-databaser på skala."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Administrere Azure SQL-databaser ved hjælp af Azure automatiske

Denne vejledning introducerer dig til Azure Automation service, og hvordan det kan bruges til at forenkle administration af din Azure SQL-databaser.


## <a name="what-is-azure-automation"></a>Hvad er Azure automatisering?

[Azure automatisering](https://azure.microsoft.com/services/automation/) er en Azure service for at forenkle skyen administration ved hjælp af automatisering af områdeprocesser for. Brug af Azure automatisering, kan længerevarende, manuel, fejlbehæftede og ofte gentagne opgaver automatisere henblik på øget pålidelighed, effektivitet og klokkeslæt til værdi for din organisation.

Azure automatisering giver en meget pålidelig og meget tilgængelig arbejdsproces udførelse af program, der skaleres til at opfylde dine behov, som organisationen vokser. I Azure Automation kan processer sættes manuelt ved at 3 tredjepart systemer eller med planlagte intervaller så opgaver sker nøjagtigt, når det er nødvendigt.

Sænke funktionsdygtige omkostninger og frigøre IT / DevOps personale til at fokusere på arbejde, der tilføjer business værdien ved at flytte dine skyen administrationsopgaver skal køres automatisk med Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hvordan kan Azure automatiske med at administrere Azure SQL-databaser?

Azure SQL-Database kan administreres i Azure automatiske ved hjælp af [Azure SQL Database PowerShell-cmdlet'er](https://msdn.microsoft.com/library/dn546723.aspx) , der er tilgængelige i [Azure PowerShell værktøjer](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure automatiske har disse Azure SQL Database PowerShell-cmdletter tilgængelige af feltet, så du kan udføre alle administrationsopgaver dine SQL DB i tjenesten. Du kan også par disse cmdlet'er i Azure Automation med cmdletter for andre Azure tjenester til at automatisere komplekse opgaver på tværs af Azure tjenester og -3 part systemer.

Azure automatiske har også muligheden for at kommunikere med SQL-servere direkte, ved at udstede SQL-kommandoer ved hjælp af PowerShell.

[Azure automatisering runbook galleriet](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) indeholder en række forskellige produkt team og community runbooks at komme i gang automatisere styring af Azure SQL-databaser, andre Azure tjenester og 3 part systemer. Galleriet runbooks omfatter:

 * [Kør SQL-forespørgsler i forhold til en SQL Server-database](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Lodret skalere (op eller ned) Azure SQL-Database på en tidsplan](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Afkort en SQL-tabel, hvis dens database nærmer sig den maksimale størrelse](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Indeksere tabeller i en Azure SQL-Database, hvis de er meget fragmenteret](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Azure automatisering, og hvordan det kan bruges til at administrere Azure SQL-databaser, skal du følge disse links til yderligere oplysninger om Azure automatisering.

- [Azure automatisering oversigt](../automation/automation-intro.md)
- [Min første runbook](../automation/automation-first-runbook-graphical.md)
- [Azure automatisering learning kort](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Azure automatisering: Agent for din SQL i skyen](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
