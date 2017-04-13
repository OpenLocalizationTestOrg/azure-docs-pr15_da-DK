<properties
    pageTitle="Flytte databaser mellem servere, mellem-abonnementer og og Azure."
    description="Hurtige trin til at kopiere, flytte, og overføre data og databaser i Azure SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Flytte databaser mellem servere, mellem-abonnementer og og Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Flytte en database til en anden server i samme-abonnement
- [Azure-portalen](https://portal.azure.com), klik på **SQL-databaser**, Vælg en database på listen og klik derefter på **Kopiér**. Se [kopiere en Azure SQL-database](sql-database-copy.md) for at få flere detaljer.

## <a name="to-move-a-database-between-subscriptions"></a>Flytte en database mellem abonnementer på
- Klik på **SQL-servere** i [Azure-portalen](https://portal.azure.com), og vælg derefter den server, hvor databasen på listen. Klik på **Flyt**, og vælg derefter ressourcer til at flytte og abonnementet til at flytte til.

## <a name="to-migrate-a-sql-database-into-azure"></a>Du kan overføre en SQL-database til Azure
- Find ud af databasen kompatibelt og derefter vælge den rigtige overførselsmetode, der er baseret på dine behov. Følg retningslinjerne og indstillinger i [overføre en SQL Server-database](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Oprette en kopi af en database til brug uden for Azure
- [Eksportere en BACPAC fil.](sql-database-export.md)
