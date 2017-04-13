<properties
    pageTitle="Hvordan du gør administratoropgaver fx Nulstil administratoradgangskode | Microsoft Azure"
    description="Beskriver, hvordan til at udføre almindelige administrative opgaver i SQL-Database. For eksempel nulstilling af administratoradgangskode, give og fjerne adgang."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="Nulstil administratoradgangskode"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Hvordan du kan udføre almindelige administrative opgaver såsom nulstilling af administratoradgangskode i Azure SQL-Database
Brug dette emne til hurtige trin til at tildele og fjerne adgangen til Azure SQL-database. Du kan finde mere omfattende oplysninger i:

- [Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md)
- [Sikring af SQL-database](sql-database-security.md)
- [Sikkerhedscenter til SQL Server-Database Engine og Azure SQL-Database](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Nulstille administratoradgangskode for en logisk server

- [Azure-portalen](https://portal.azure.com) skal du klikke på **SQL-servere**, vælge serveren på listen og derefter klikke på **Nulstil adgangskode**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>For at gøre sikker kun autoriserede IP er-adresser tilladt at få adgang til serveren
- Se [Sådan: konfigurere firewall-indstillinger på SQL-Database](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Til at oprette indeholdte databasebrugere i databasen
- Bruge sætningen [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) og se [Indeholdt databasebrugere - gøre din Database Portable](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Til at godkende indeholdte databasebrugere ved hjælp af din Azure Active Directory
- Se [oprettelse af forbindelse til SQL-Database ved hjælp af Azure Active Directory-godkendelse](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>At oprette ekstra logon til høj rettigheder brugere i den virtuelle overordnede database
- Bruge sætningen [Oprette logon](https://msdn.microsoft.com/library/ms189751.aspx) , og se afsnittet administrere logon i [administrere databaser og logon i Azure SQL-Database](sql-database-manage-logins.md) for at få flere detaljer.
