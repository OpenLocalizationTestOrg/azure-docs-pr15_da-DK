<properties
    pageTitle="Azure Government dokumentation | Microsoft Azure"
    description="Dette giver en sammenligning af funktioner og vejledning om udvikling af programmer til Azure Government"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Azure Government databaser

##  <a name="sql-database"></a>SQL-Database

Se i<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Microsoft Security Center til SQL Database Engine</a> og [Offentlige dokumentationen til Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) for at få yderligere vejledning af metadata synlighed konfiguration og bedste praksis for beskyttelse.

### <a name="variations"></a>Variationer

SQL-version 12 Database er alment tilgængelig i Azure Government.

Adressen for SQL Azure-servere i Azure Government er anderledes:

Tjenestetype|Azure offentlige|Azure Government
---|---|---
SQL-Database|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Overvejelser i forbindelse med

Følgende oplysninger identificerer i Azure Government rammen til Azure SQL:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alle dataene opbevares og behandles i Microsoft Azure SQL kan indeholde fastsat af Azure Government data. Du skal bruge Databaseværktøjer til dataoverførsel fastsat af Azure Government data. | Azure SQL-metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Disse metadata omfatter alle konfigurationsdata, der er angivet, da at oprette og vedligeholde produktet lagerplads.  Angiv ikke omfattet/kontrolleres data i følgende felter: Database navn, Abonnementsnavnet, grupper, servernavn, Server administrator logon, installation navne, ressourcenavne, ressource-mærker

## <a name="azure-redis-cache"></a>Azure Redis Cache

Få mere at vide om denne tjeneste, og hvordan det skal bruges i dokumentationen [Azure Redis Cache offentlige](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variationer

URL-adresser til at få adgang til og administration af Azure Redis cachen i Azure Government er forskellige:

Tjenestetype|Azure offentlige|Azure Government
---|---|---
Cache slutpunkt|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Azure-portalen|https://Portal.Azure.com|https://Portal.Azure.us

>[AZURE.NOTE] Alle dine scripts og kode skal tage højde for den relevante slutpunkter og miljøer. Se, [hvordan du opretter forbindelse til Azure Government Cloud](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)kan finde flere oplysninger.


### <a name="considerations"></a>Overvejelser i forbindelse med

Følgende oplysninger identificerer i Azure Government rammen til Azure Redis Cache:

| Omfattet/kontrolleres data, der er tilladt | Omfattet/kontrolleres data, der er ikke tilladt |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alle dataene opbevares og behandles i Azure Redis Cache kan indeholde fastsat af Azure Government data. | Azure Redis Cache metadata er ikke tilladt til at indeholde kontrolleres Eksporter data. Angiv ikke omfattet/kontrolleres data i følgende felter: Cache navn, VNET navn, Abonnementsnavnet, grupper, ressource mærker, Redis egenskaber.  

##  <a name="next-steps"></a>Næste trin

Til supplerende oplysninger og opdateringer abonnere på den <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government Blog.</a>
