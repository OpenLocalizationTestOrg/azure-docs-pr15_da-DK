<properties
   pageTitle="Godkendelse til Azure SQL datawarehouse | Microsoft Azure"
   description="Azure Active Directory (AAD) og SQL Server-godkendelse til Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Godkendelse til Azure SQL datawarehouse

> [AZURE.SELECTOR]
- [Oversigt over sikkerhed](sql-data-warehouse-overview-manage-security.md)
- [Godkendelse](sql-data-warehouse-authentication.md)
- [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
- [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Hvis du vil oprette forbindelse til SQL Data Warehouse, skal du gå i sikkerhedslegitimationsoplysninger forbindelse med godkendelse. Ved oprettelse af en forbindelse, er visse indstillinger for forbindelse konfigureret som en del af om oprettelse af din forespørgsel session.  

Du kan finde flere oplysninger om sikkerhed og hvordan du aktiverer forbindelser til dit datawarehouse, [Secure en database i SQL Data Warehouse][].

## <a name="sql-authentication"></a>SQL-godkendelse
Hvis du vil oprette forbindelse til SQL Data Warehouse, skal du angive følgende oplysninger:

- Fuldstændigt servernavn
- Angive SQL-godkendelse
- Brugernavn
- Adgangskode
- Standarddatabase (valgfrit)

Som standard er din forbindelse opretter forbindelse til *master* databasen og ikke brugerdatabasen. Hvis du vil oprette forbindelse til brugerdatabasen, kan du vælge at benytte en af to ting:

- Angiv standarddatabasen,, når du registrerer din server med SQL Server Object Explorer i SSDT, SSMS, eller i dit program forbindelsesstreng. For eksempel indeholde parameteren InitialCatalog for en ODBC-forbindelse.
- Fremhæve brugerdatabasen før du opretter en session i SSDT.

> [AZURE.NOTE] Transact-SQL-sætningen **Brug mindatabase;** understøttes ikke til at ændre databasen for en forbindelse. Vejledning i at oprette forbindelse til SQL Data Warehouse med SSDT, referere til [forespørgsel med Visual Studio][] -artiklen.

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD)-godkendelse

[Azure Active Directory] [ What is Azure Active Directory] godkendelse er en funktion for at oprette forbindelse til Microsoft Azure SQL Data Warehouse ved hjælp af identiteter i Azure Active Directory (Azure AD). Med Azure Active Directory-godkendelse, kan du centralt administrerer identiteter for brugere og andre Microsoft-tjenester på en central placering. Central administration af ID kan samle for at administrere SQL Data Warehouse brugere og forenkler administrationen af tilladelser. 

### <a name="benefits"></a>Fordele

Azure Active Directory-fordele omfatter:

- Indeholder et alternativ til SQL Server-godkendelse.
- Hjælper med at stoppe udbredelsen af bruger-id'er på tværs af databaseservere.
- Gør det muligt for adgangskode rotation på ét sted
- Administrere brugertilladelser til databasen ved hjælp af eksterne (AAD) grupper.
- Fjerner lagring af adgangskoder ved at aktivere integreret Windows-godkendelse og andre former for godkendelse, der understøttes af Azure Active Directory.
- Bruger indeholdt databasebrugere til at godkende identiteter på niveauet for databasen.
- Understøtter token-baseret godkendelse for at oprette forbindelse til SQL Data Warehouse-programmer.
- Understøtter Multi-Factor authentication gennem Active Directory Universal godkendelse til SQL Server Management Studio. Finde en beskrivelse af Multi-Factor Authentication, [SSMS support til Azure AD MFA med SQL-Database og SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory er stadig relativt ny og har nogle begrænsninger. For at sikre, at Azure Active Directory er passer til dit miljø, skal du se [Azure AD-funktioner og begrænsninger][], specifikt yderligere overvejelser.

### <a name="configuration-steps"></a>Trin til konfiguration

Følg disse trin for at konfigurere godkendelse af Azure Active Directory.

1. Oprette og udfylde en Azure Active Directory
2. Valgfrit: Knytte eller ændre active directory, der er knyttet til abonnementet Azure
3. Oprette en Azure Active Directory-administrator for Azure SQL Data Warehouse.
4. Konfigurere din klientcomputere
5. Oprette indeholdte databasebrugere i din database, der er knyttet til Azure AD identiteter
6. Oprette forbindelse til dit datawarehouse ved hjælp af Azure AD identiteter

Azure Active Directory-brugere vises i øjeblikket ikke i SSDT Object Explorer. Få vist brugerne i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)som en løsning.
  
### <a name="find-the-details"></a>Finde oplysninger
- Udføre den detaljerede vejledning. Den detaljerede vejledning til at konfigurere og bruge Azure Active Directory-godkendelse er næsten identiske til Azure SQL-Database og Azure SQL Data Warehouse. Følg den detaljerede vejledning i emnet [tilslutning til SQL-Database eller SQL Data Warehouse ved brug af Azure Active Directory-godkendelse](../sql-database/sql-database-aad-authentication.md).
- Oprette brugerdefinerede databaseroller og føje brugere til rollerne. Giv derefter detaljerede tilladelser til rollerne. Se [Introduktion til Database Engine tilladelser](https://msdn.microsoft.com/library/mt667986.aspx)kan finde flere oplysninger.

## <a name="next-steps"></a>Næste trin

For at starte forespørgsler dit datawarehouse til Visual Studio og andre programmer, skal du se [forespørgsel med Visual Studio][].

<!-- Article references -->
[Sikre en database i SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Forespørgsel med Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD-funktioner og begrænsninger]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
