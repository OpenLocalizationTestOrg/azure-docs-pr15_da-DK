<properties
    pageTitle="Hvad er nyt i SQL-Database version 12 | Microsoft Azure"
    description="Beskriver, hvorfor business systemer, der bruger Azure SQL-Database i skyen drage fordel ved at opgradere til version version 12 nu."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>Hvad er nyt i SQL-Database version 12


Dette emne beskrives de mange fordele, der indeholder den nye version 12-version af Azure SQL-Database over version V11.


Vi fortsætte med at føje funktioner til version 12. Så opfordrer vi dig at besøge vores Tjenesteopdateringerne webside til Azure og for at bruge dets filtre:


- Filtreret til [tjenesten SQL-Database](https://azure.microsoft.com/updates/?service=sql-database).
- Filtreret til generelt tilgængelig [(GA) meddelelser](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) om funktioner for SQL-Database funktioner.


De seneste oplysninger om begrænsninger for ressourcen for SQL-Database er beskrevet på:<br/>[Azure SQL Database ressource begrænsninger](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Øget programmet kompatibilitet med SQL Server


Et vigtigt mål for SQL-Database version 12 blev til at forbedre kompatibilitet med Microsoft SQL Server 2014 og til at bevare kompatibiliteten, som er udgivet nye versioner af SQL Server. Blandt andre områder opnås version 12 fungerer ensartet med SQL Server i området vigtige programmering. Eksempel:

- [Indbygget JSON-understøttelse](https://msdn.microsoft.com/library/dn921897.aspx)

- [Vinduet funktioner](http://msdn.microsoft.com/library/ms189798.aspx)med [OVER](http://msdn.microsoft.com/library/ms189461.aspx)

- [XML-indeks](http://msdn.microsoft.com/library/bb934097.aspx) og [selektiv XML-indeks](http://msdn.microsoft.com/library/jj670104.aspx)

- [Registrering af ændringer](http://msdn.microsoft.com/library/bb933875.aspx)

- [VÆLG... I](http://msdn.microsoft.com/library/ms188029.aspx)

- [Søgning i hele teksten](http://msdn.microsoft.com/library/ms142571.aspx)

- [ÆNDRE databasen fastsat konfiguration (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Se [her](sql-database-transact-sql-information.md) for at få det lille sæt af funktioner, der endnu ikke understøttes i SQL-Database.


### <a name="compatibility-level-130"></a>Kompatibilitetsniveau 130


> [AZURE.IMPORTANT] Starter i **juni 2016**, har *nyligt* oprettet databaser på Azure SQL-Database version 12 deres kompatibilitetsniveau begynde på 130, som svarer til Microsoft SQL Server 2016 GA.
> 
> Du kan bruge `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` Hvis du foretrækker.
> 
> Databaser, der er oprettet før juni 2016 har ikke deres kompatibilitetsniveau ændret af denne ændring af standard. Er niveauet i en database, der er ændret ved at opgradere fra V11 til version 12 er heller ikke.



En forklaring af hvordan du kan sammenligne dine vigtigste forespørgsler mellem senest kontra forrige kompatibilitetsniveau, skal du se:

- [Forbedret udførelse af forespørgsler med kompatibilitetsniveau 130 i Azure SQL-Database](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Mere premium ydeevne, nye ydeevneniveauer


I version 12 øges vi Database transaktion enheder (DTUs) tildeles alle Premium ydeevneniveauer med 25% gratis. Endnu bedre ydeevne kan der opnås med nye funktioner som:


- Understøttelse af i hukommelsen [columnstore indeks](http://msdn.microsoft.com/library/gg492153.aspx).
- [Tabel partitionering efter rækker](http://msdn.microsoft.com/library/ms187802.aspx) med relaterede forbedringer til [AFKORTE tabel](http://msdn.microsoft.com/library/ms177570.aspx).
- Tilgængeligheden af dynamiske management visninger [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) til at overvåge og finjustere ydeevnen.


### <a name="reliable-performance"></a>Pålidelig ydeevne


Hvis klientprogrammet opretter forbindelse til SQL-Database version 12, mens din klient kører på en Azure VM (virtual machine), skal du åbne følgende port intervaller på VM:

- 11000 11999
- 14000 14999


Klik [her](sql-database-develop-direct-route-ports-adonet-v12.md) for at få mere at vide om portene version 12 SQL-Database. Portene, der er nødvendige for forbedret ydeevne i SQL-Database version 12.


## <a name="better-support-for-cloud-saas-vendors"></a>Bedre support til skyen SaaS leverandører


Kun i version 12, har vi udsendt et nyt niveau af ydeevneniveau S3 og offentlige eksemplet på [elastiske database grupper](sql-database-elastic-pool.md). Elastiske database grupper er en løsning, der er udviklet til skyen SaaS leverandører.  Med elastiske database grupper kan du:


- Dele DTUs mellem databaser for at reducere omkostningerne til stort antal databaser.
- Udføre [elastiske database jobs](sql-database-elastic-jobs-overview.md) for at administrere databaser skaleres til brug.


## <a name="security-enhancements"></a>Forbedringer af sikkerheden


Sikkerhed har højeste prioritet for alle, der kører deres virksomhed i skyen. De seneste sikkerhedsfunktioner udgivet i version 12 omfatter:


- [Række-sikkerhed på brugerniveau](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Dynamiske Data Maskering](sql-database-dynamic-data-masking-get-started.md)
- [Indeholdte databaser](http://msdn.microsoft.com/library/ff929188.aspx)
- [Programmet roller](http://msdn.microsoft.com/library/ms190998.aspx) administrerede med Giv, AFVIS, OPHÆVE
- [Kryptering af gennemsigtig Data](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Oprette forbindelse til SQL-Database ved hjælp af Azure Active Directory-godkendelse](sql-database-aad-authentication.md)
 - SQL-Database understøtter nu Azure Active Directory-godkendelse ved hjælp af oprettelse af forbindelse til SQL-Database ved hjælp af identiteter i Azure Active Directory (Azure AD). Du kan administrere identiteter for brugere og andre Microsoft-tjenester på en central placering centralt med Azure Active Directory-godkendelse.
- [Altid krypteret](https://msdn.microsoft.com/library/mt163865.aspx) (i eksemplet) gør kryptering gennemsigtig til programmer og gør det klienter til at kryptere følsomme data i klientprogrammer uden at dele tasterne kryptering med SQL-Database.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Øget Forretningskontinuitet, når der er brug for gendannelse


Version 12 tilbyder forbedret gendannelse punkt målsætninger (frigivne produktionsordrer) og anslåede gendannelse tiden (ERTs):


| Funktionen Business løbende | Tidligere version | VERSION 12 |
| :-- | :-- | :-- |
| Geografisk-Gendan | • Frigivne Produktionsordre < 24 timer.<br/>• Indsæt < 12 timer. | • Frigivne Produktionsordre < 1 time.<br/>• Indsæt < 12 timer. |
| Aktive geografisk-gentagelse | • Frigivne Produktionsordre < 5 minutter.<br/>• Indsæt < 1 time. | • Frigivne Produktionsordre < 5 sekunder.<br/>• Indsæt < 30 sekunder. |


Du kan få flere oplysninger i [SQL-Database Forretningskontinuitet](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Flere årsager til at opgradere nu


Der er mange gode grunde hvorfor kunderne skal opgradere nu til version 12 Azure SQL-Database fra V11:


- SQL-Database version 12 har en lang liste over funktioner ud over funktionerne i V11.
- Vi fortsætte med at tilføje nye funktioner til version 12, men ingen nye funktioner, føjes til V11.
- De fleste nye funktioner er udgivet på SQL-Database version 12, før de er frigivet til Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Bruger du version 12 allerede?


En nem måde at se, om du har en database eller logiske server, der kører på en tidligere version af tjenesten SQL-Database er at gøre følgende:


1. Gå til [Azure-portalen](https://portal.azure.com/).
2. Klik på **Gennemse**.
3. Klik på **SQL-servere**.
4. Ikonet ud for din server eller database fortæller tekstenheden:
 - ![Ikon for en version 12 server](./media/sql-database-v12-whats-new/v12_icon.png) **version 12 logiske server**
 - ![Ikon for tidligere version af server](./media/sql-database-v12-whats-new/earlier_icon.png) **tidligere version logiske server**


En anden metode til at fastslå versionen er at køre den `SELECT @@version;` -sætning i databasen, og få vist de samme resultater:


- **12**.0.2000.10 &nbsp; *(version version 12)*
- **11**.0.9228.18 &nbsp; *(version V11)*


En version 12-database kan bruges på en logisk version 12-server. Og en version 12 server kan være vært for kun version 12 databaser.


Hvis du ikke kører endnu på version 12, kan du opgradere din logiske server ved at følge trinnene i [opgradere til SQL Database version 12 på plads](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Generelle tilgængelighed områder


- Ved 31 juli 2015 havde alle områder opgraderet til generelle tilgængelighed (GA).
- Version 12 blev udgivet i December 2014, men kun på status for Preview.

[Supplerende vilkår for anvendelse af Microsoft Azure eksempler](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
