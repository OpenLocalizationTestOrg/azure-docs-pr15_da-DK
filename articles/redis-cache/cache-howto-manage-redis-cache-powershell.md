<properties
    pageTitle="Administrere Azure Redis cachen med Azure PowerShell | Microsoft Azure"
    description="Lær at udføre administrative opgaver til Azure Redis Cache ved hjælp af Azure PowerShell."
    services="redis-cache"
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Administrere Azure Redis cachen med Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Dette emne viser, hvordan til at udføre almindelige opgaver såsom opretter, opdatere, og skalere dit Azure Redis Cache forekomster, sådan genoprette hurtigtaster og hvordan du kan få vist oplysninger om din cache. Du kan finde en komplet liste over Azure Redis Cache PowerShell-cmdlet'er [Azure Redis Cache-cmdletter](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk model](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Forudsætninger

Hvis du allerede har installeret Azure PowerShell, skal du have Azure PowerShell version 1.0.0 eller nyere. Du kan se versionen af Azure PowerShell, som du har installeret med denne kommando i kommandoprompten Azure PowerShell.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Først skal du logge Azure med denne kommando.

    Login-AzureRmAccount

Angiv mailadressen til din Azure-konto og adgangskoden i dialogboksen Microsoft Azure-logon.

Næste, hvis du har flere Azure abonnementer, skal du angive dit Azure-abonnement. Hvis du vil se en liste over dine aktuelle abonnementer, du Kør denne kommando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Hvis du vil angive abonnementet, skal du køre følgende kommando. I følgende eksempel på abonnementets navn er `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Før du kan bruge Windows PowerShell med Azure Ressourcestyring, skal du følgende:

- Windows PowerShell, Version 3.0 eller 4.0. Hvis du vil finde versionen af Windows PowerShell, skal du skrive:`$PSVersionTable` og kontrollere værdien af `PSVersion` er 3.0 eller 4.0. For at installere en kompatibel version skal du se [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Brug cmdlet'en Get-Help for at få detaljeret hjælp til en hvilken som helst cmdlet, der vises i dette selvstudium.

    Get-Help <cmdlet-name> -Detailed

For at få hjælp til de `New-AzureRmRedisCache` cmdlet, type:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Sådan oprettes forbindelse til Azure Government Cloud eller Azure Kina skyen

Miljø er som standard på Azure `AzureCloud`, som repræsenterer den globale Azure sky forekomst. Hvis du vil oprette forbindelse til en anden forekomst, du bruger den `Add-AzureRmAccount` kommando med den `-Environment` eller -`EnvironmentName` kommandolinjeparameteren med det ønskede miljø eller miljø navn.

For at se listen over tilgængelige miljøer skal køre den `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Oprette forbindelse til Azure Government skyen

For at oprette forbindelse til Azure Government skyen, skal du bruge en af følgende kommandoer.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

eller

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

For at oprette en cache i Azure Government skyen skal du bruge en af følgende steder.

-   USGov Virginia
-   Krigsskibe i USGov Iowa

Se [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) og [Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md)kan finde flere oplysninger om Azure Government skyen.

### <a name="to-connect-to-the-azure-china-cloud"></a>Oprette forbindelse til Azure Kina skyen

For at oprette forbindelse til Azure Kina skyen, skal du bruge en af følgende kommandoer.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

eller

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

For at oprette en cache i Azure Kina skyen, skal du bruge en af følgende steder.

-   Kina øst
-   Kina nord

Du kan finde flere oplysninger om Azure Kina skyen, [AzureChinaCloud til Azure drevet af 21Vianet i Kina](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Egenskaber, der bruges til Azure Redis Cache PowerShell

Den følgende tabel indeholder egenskaber og beskrivelser for ofte anvendte parametre, når oprettelse og administration af din Azure Redis Cache forekomster ved hjælp af Azure PowerShell.

| Parameter          | Beskrivelse                                                                                                                                                                                                        | Standard  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Navn               | Navnet på cachen                                                                                                                                                                                                  |          |
| Placering           | Placeringen af cachen                                                                                                                                                                                              |          |
| ResourceGroupName  | Ressource gruppenavn, hvor du kan oprette cachen                                                                                                                                                                   |          |
| Størrelse               | Størrelsen af cachen. Gyldige værdier er: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB                                                                     | 1GB      |
| ShardCount         | Antallet af shards til at oprette, når du opretter en premium cache med klynge aktiveret. Gyldige værdier er: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| SKU                | Angiver SKU cachen. Gyldige værdier er: grundlæggende, Standard, Premium                                                                                                                                         | Standard |
| RedisConfiguration | Angiver Redis konfigurationsindstillinger. Du kan finde oplysninger om hver indstilling tabellen nedenfor [RedisConfiguration egenskaber](#redisconfiguration-properties) . |          |
| EnableNonSslPort   | Angiver, om ikke-SSL-porten er aktiveret.                                                                                                                                                                     | FALSK    |
| MaxMemoryPolicy    | Denne parameter frarådes - du kan bruge RedisConfiguration i stedet.                                                                                                                                              |          |
| StaticIP           | Når vært for din cache i en VNET, du angiver en entydig IP-adresse i undernet til cachen. Hvis du ikke er angivet, vælges en for dig fra undernettet.                                                                                                                     |          |
| Undernet             | Når vært for din cache i en VNET, du angiver navnet på det undernet, hvor du kan installere cachen.                                                                                                                  |          |
| VirtualNetwork     | Når vært for din cache i en VNET, du angiver ressource-ID for VNET hvor du kan installere cachen.                                                                                                             |          |
| KeyType            | Angiver, hvilke adgang for at genoprette når forny access-taster. Gyldige værdier er: primær, sekundær |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>RedisConfiguration egenskaber

| Egenskaben                      | Beskrivelse                                                                                                          | Priser niveauer       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| RDB sikkerhedskopiering aktiveret            | Om [Redis data brugerdata](cache-how-to-premium-persistence.md) er aktiveret                                     | Kun Premium        |
| RDB-lager-forbindelse-streng | Forbindelsesstrengen på lagerplads kontoen for [Redis bevarelse af data](cache-how-to-premium-persistence.md)       | Kun Premium        |
| RDB-sikkerhedskopi-frekvens          | Sikkerhedskopiering hyppigheden for [Redis bevarelse af data](cache-how-to-premium-persistence.md)                               | Kun Premium        |
| reserveret maxmemory            | Konfigurerer [hukommelse reserveret](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) til ikke-cache processer | Standard og Premium |
| maxmemory-politik              | Konfigurerer [eviction politik](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) for cachen           | Alle priser niveauer   |
| besked-keyspace-begivenheder        | Konfigurerer [keyspace beskeder](cache-configure.md#keyspace-notifications-advanced-settings)                     | Standard og Premium |
| hash-Maks-ziplist-poster      | Konfigurerer [hukommelsesoptimering](http://redis.io/topics/memory-optimization) til små Sammenlæg datatypen          | Standard og Premium |
| hash-Maks-ziplist-værdi        | Konfigurerer [hukommelsesoptimering](http://redis.io/topics/memory-optimization) til små Sammenlæg datatypen          | Standard og Premium |
| sæt-Maks-intset-poster        | Konfigurerer [hukommelsesoptimering](http://redis.io/topics/memory-optimization) til små Sammenlæg datatypen          | Standard og Premium |
| zset-Maks-ziplist-poster      | Konfigurerer [hukommelsesoptimering](http://redis.io/topics/memory-optimization) til små Sammenlæg datatypen          | Standard og Premium |
| zset-Maks-ziplist-værdi        | Konfigurerer [hukommelsesoptimering](http://redis.io/topics/memory-optimization) til små Sammenlæg datatypen          | Standard og Premium |
| databaser                     | Konfigurerer antallet af databaser. Denne egenskab kan konfigureres kun på oprettelse af cache.                          | Standard og Premium |

## <a name="to-create-a-redis-cache"></a>Oprette en Redis Cache

Nye Azure Redis Cache forekomster er oprettet ved hjælp af [Ny AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet.

>[AZURE.IMPORTANT] Første gang du opretter en Redis cache i et abonnement, ved hjælp af portalen Azure portalen registrerer de `Microsoft.Cache` navneområde til dette abonnement. Hvis du forsøger at oprette den første Redis cache i et abonnement, ved hjælp af PowerShell, skal du først registrere pågældende navneområde, ved hjælp af kommandoen følgende. Ellers cmdletter som `New-AzureRmRedisCache` og `Get-AzureRmRedisCache` mislykkes.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Se en liste over tilgængelige parametre og beskrivelserne til `New-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Hvis du vil oprette en cache med standardparametre, skal du køre følgende kommando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, og `Location` er obligatoriske parametre, men resten er valgfrie og har en standardværdi. Køre den forrige kommando opretter en forekomst af Standard SKU Azure Redis Cache med det angivne navn, placering og ressourcegruppe, der er 1 GB i størrelse med ikke-SSL-porten deaktiveret.

Angive en størrelse på P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), for at oprette en premium cache P3 (26 GB - 260 GB), eller P4 (53 GB - 530 GB). For at aktivere klynge skal du angive en shard Tæl ved hjælp af den `ShardCount` parameter. I følgende eksempel oprettes en P1 premium cache med 3 shards. En P1 premium cache er 6 GB i størrelse, og da vi angivet tre shards den samlede størrelse er 18 GB (Gigabyte 3 x 6).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Til at angive værdier for den `RedisConfiguration` parameter, skal du omslutte værdierne i `{}` som nøgleværdi/par som `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. I følgende eksempel oprettes en standard 1 GB cache med `allkeys-random` maxmemory-politik og keyspace beskeder konfigureret med `KEA`. Du kan finde flere oplysninger i [Keyspace meddelelser (avancerede indstillinger)](cache-configure.md#keyspace-notifications-advanced-settings) og [Maxmemory-politik og maxmemory reserveret](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Konfigurere indstillingen databaser under oprettelse af cache

Den `databases` indstilling kan konfigureres kun under oprettelse af cache. I følgende eksempel oprettes en premium P3 (26 GB) cache med 48 databaser ved hjælp af [Ny AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Yderligere oplysninger om den `databases` egenskab, se [standard Azure Redis Cache serverkonfiguration](cache-configure.md#default-redis-server-configuration). Du kan finde flere oplysninger om oprettelse af en cache ved hjælp af [Ny AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) cmdlet afsnittet tidligere [til at oprette en Redis Cache](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Opdatere en Redis cache

Azure Redis Cache forekomster opdateres ved hjælp af cmdlet'en [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) til.

Se en liste over tilgængelige parametre og beskrivelserne til `Set-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Den `Set-AzureRmRedisCache` cmdlet kan bruges til at opdatere egenskaber såsom `Size`, `Sku`, `EnableNonSslPort`, og den `RedisConfiguration` værdier. 

Følgende kommando opdaterer maxmemory-politik for Redis cachen med navnet myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Skalere et Redis cache

`Set-AzureRmRedisCache`kan bruges til at skalere en Azure Redis cache forekomst hvornår den `Size`, `Sku`, eller `ShardCount` egenskaber kan ændres. 

>[AZURE.NOTE]Skalering en cache ved hjælp af PowerShell er underlagt de samme grænser og retningslinjer som skalering en cache fra Azure-portalen. Du kan skalere til et andet priser trin med følgende begrænsninger.
>
>-  Du kan skalere fra et højere priser trin til et lavere priser trin.
>    -    Du kan skalere fra en **Premium** cache ned til et **almindeligt** eller en **grundlæggende** cache.
>    -    Du kan skalere fra en **Standard** cache ned til en **grundlæggende** cache.
>-  Du kan skalere fra en **grundlæggende** cache til en **Standard** -cachen, men du kan ikke ændre størrelsen på samme tid. Hvis du har brug for en anden størrelse, kan du gøre en efterfølgende skalering operation til den ønskede størrelse.
>-  Du kan skalere fra en **grundlæggende** cache direkte til en **Premium** cache. Du skal skalere fra **grundlæggende** til **Standard** i én skalering handling og derefter fra **Standard** til **Premium** i en efterfølgende skalering handling.
>-  Du kan skalere fra en større ned til den **C0 (250 MB)** størrelse.
>
>Du kan finde flere oplysninger, se, [hvordan du skala Azure Redis Cache](cache-how-to-scale.md).

Følgende eksempel viser, hvordan du kan tilpasse en cache med navnet `myCache` til 2,5 GB cachen. Bemærk, at denne kommando fungerer for både en grundlæggende eller en Standard cache.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Når denne kommando er udstedt, returneres status for cachen (ligner opkald `Get-AzureRmRedisCache`). Bemærk, at den `ProvisioningState` er `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Når handlingen skalering er fuldført, den `ProvisioningState` ændres til `Succeeded`. Hvis du vil foretage en efterfølgende skalering handling, som ændrer fra grundlæggende til Standard og derefter ændre størrelsen, skal du vente, indtil den forrige handling er fuldført, eller du får vist en fejl, der ligner følgende.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>At få oplysninger om en Redis cache

Du kan hente oplysninger om en cache ved hjælp af cmdlet'en [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) til.

Se en liste over tilgængelige parametre og beskrivelserne til `Get-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Hvis du vil returnere oplysninger om alle cache i det aktuelle abonnement, skal du køre `Get-AzureRmRedisCache` uden parametre.

    Get-AzureRmRedisCache

Hvis du vil returnere oplysninger om alle cache i en bestemt ressourcegruppe, skal du køre `Get-AzureRmRedisCache` med den `ResourceGroupName` parameter.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

For at returnere oplysninger om en bestemt cache, skal du køre `Get-AzureRmRedisCache` med den `Name` parameter, der indeholder navnet på cachen, og den `ResourceGroupName` parameteren med ressourcegruppen, der indeholder cachen.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Til at hente hurtigtasterne til en Redis cache

Du kan bruge cmdlet'en [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) til for at hente hurtigtasterne til din cache.

Se en liste over tilgængelige parametre og beskrivelserne til `Get-AzureRmRedisCacheKey`, køre følgende kommando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

For at hente taster til din cache skal ringe på `Get-AzureRmRedisCacheKey` cmdlet og overføre navnet på din cache på navnet på den ressourcegruppe, der indeholder cachen.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>At genoprette hurtigtaster til din Redis cache

Du kan bruge cmdlet'en [Ny AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) til for at genoprette hurtigtasterne til din cache.

Se en liste over tilgængelige parametre og beskrivelserne til `New-AzureRmRedisCacheKey`, køre følgende kommando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Ringe til for at genoprette nøglen primær eller sekundær for din cache, den `New-AzureRmRedisCacheKey` cmdlet og overfører i navnet, ressourcegruppe, og Angiv enten `Primary` eller `Secondary` for den `KeyType` parameter. I eksemplet nedenfor gendannes tasten sekundær adgang til en cache.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Slette en Redis cache

Brug cmdlet'en [Fjern AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) , hvis du vil slette en Redis cache.

Se en liste over tilgængelige parametre og beskrivelserne til `Remove-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

I eksemplet nedenfor cachen med navnet `myCache` fjernes.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Importere en Redis cache

Du kan importere data til en Azure Redis Cache-forekomst ved hjælp af den `Import-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importér/Eksportér er kun tilgængelig for [premium niveau](cache-premium-tier-intro.md) cache. Se [importere og eksportere data i Azure Redis Cache](cache-how-to-import-export-data.md)kan finde flere oplysninger om Importér/Eksportér.

Se en liste over tilgængelige parametre og beskrivelserne til `Import-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

Følgende kommando importerer data fra den blob, der er angivet af SAS uri til Azure Redis Cache.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Eksportere en Redis cache

Du kan eksportere data fra en Azure Redis Cache-forekomst ved hjælp af den `Export-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importér/Eksportér er kun tilgængelig for [premium niveau](cache-premium-tier-intro.md) cache. Se [importere og eksportere data i Azure Redis Cache](cache-how-to-import-export-data.md)kan finde flere oplysninger om Importér/Eksportér.

Se en liste over tilgængelige parametre og beskrivelserne til `Export-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Følgende kommando eksporterer data fra en Azure Redis Cache forekomst i objektbeholderen angivet af SAS uri.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Genstarte en Redis cache

Du kan genstarte din Azure Redis Cache-forekomst ved hjælp af den `Reset-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Genstart er kun tilgængelig for [premium niveau](cache-premium-tier-intro.md) cachelagre. Se [Cache administration - genstart](cache-administration.md#reboot)kan finde flere oplysninger om at du har genstartet din cache.

Se en liste over tilgængelige parametre og beskrivelserne til `Reset-AzureRmRedisCache`, køre følgende kommando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

Følgende kommando genstarter begge noder af den angivne cache.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om at bruge Windows PowerShell med Azure, skal du se følgende ressourcer:

- [Azure Redis Cache cmdlet dokumentation på MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Azure ressourcestyring cmdletter](http://go.microsoft.com/fwlink/?LinkID=394765): Lær at bruge-cmdletter i modulet AzureResourceManager.
- [Anvender ressource grupper til at administrere dine Azure ressourcer](../resource-group-template-deploy-portal.md): Lær at oprette og administrere grupper i portalen Azure.
- [Azure-blog](http://blogs.msdn.com/windowsazure): Få mere at vide om nye funktioner i Azure.
- [Windows PowerShell-blog](http://blogs.msdn.com/powershell): Få mere at vide om nye funktioner i Windows PowerShell.
- [", Scripting-ekspert!" Blog](http://blogs.technet.com/b/heyscriptingguy/): få reale tip og tricks til Windows PowerShell-community.
