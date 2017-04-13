<properties 
    pageTitle="Hvordan du kan oprette og administrere Azure Redis Cache bruger kommandolinjen Azure (Azure CLI) | Microsoft Azure" 
    description="Lær, hvordan du installerer Azure CLI på en hvilken som helst platform, hvordan du kan bruge det til at oprette forbindelse til kontoen Azure og hvordan du kan oprette og administrere en Redis cache fra Azure CLI." 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Hvordan du kan oprette og administrere Azure Redis Cache bruger kommandolinjen Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Azure CLI er en god metode til at administrere din Azure-infrastruktur fra en hvilken som helst platform. I denne artikel beskrives, hvordan til at oprette og administrere din Azure Redis Cache forekomster, ved hjælp af Azure CLI.

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil oprette og administrere Azure Redis Cache forekomster ved hjælp af Azure CLI, skal du udføre følgende trin.

-   Du skal have en Azure-konto. Hvis du ikke har en, kan du oprette en [gratis konto](https://azure.microsoft.com/pricing/free-trial/) i et par minutter.
-   [Installer Azure CLI](../xplat-cli-install.md).
-   Forbinde installationen af Azure CLI med en personlig Azure-konto eller med en arbejde eller skole Azure-konto, og log på fra Azure CLI ved hjælp af den `azure login` kommandoen. For at forstå forskellene og vælge skal du se [oprette forbindelse til et Azure-abonnement fra Azure kommandolinjen (Azure CLI)](../xplat-cli-connect.md).
-   Før du kører en af følgende kommandoer, skifte Azure CLI i ressourcestyring tilstand ved at køre den `azure config mode arm` kommandoen. Se [angive tilstanden Azure ressourcestyring](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode)kan finde flere oplysninger.

## <a name="redis-cache-properties"></a>Redis Cache egenskaber

Følgende egenskaber anvendes, når du opretter og opdatere Redis Cache forekomster.

| Egenskaben            | Skift                                  | Beskrivelse                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Navn                | -n, -navn                              | Navnet på Redis cachen.                                                                                                                                                                                                                             |
| ressourcegruppe      | -g, –-ressourcegruppe                    | Navnet på den ressourcegruppe.                                                                                                                                                                                                                          |
| placering            | -l, – placering                          | Placering til at oprette cache.                                                                                                                                                                                                                            |
| størrelse                | -z, -størrelse                              | Størrelsen af Redis cachen. Gyldige værdier: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]                                                                                                                                                                  |
| SKU                 | -x, – sku                               | Redis SKU. Skal være en af: [grundlæggende, Standard, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | -e, – Aktivér-ikke-ssl-port               | EnableNonSslPort egenskab for den Redis Cache. Tilføje dette flag, hvis du vil aktivere ikke SSL-porten for din cache                                                                                                                                    |
| Redis konfiguration | -c,--redis-konfiguration               | Redis konfiguration. Angiv en JSON formateret streng med konfigurationsnøgler og værdier her. Format: "{" ":"";" ":" "}"                                                                                                                                     |
| Redis konfiguration | -f, – redis konfigurationsfil          | Redis konfiguration. Angiv stien til en fil, der indeholder konfigurationsnøgler og værdier her. Format for posten fil: {"": "";"": ""}                                                                                                                |
| Shard antal         | -r, – shard Tæl                       | Antallet af Shards til at oprette på en Premium klynge Cache med klynge.                                                                                                                                                                               |
| Virtuelt netværk     | -v, – virtuelt netværk                   | Når vært for din cache i en VNET, du angiver den nøjagtige ARM ressource-ID i det virtuelle netværk for at installere redis cachen i. Eksempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| vigtige type            | -t, – nøgle-type                          | Type nøgle til at forny. Gyldige værdier: [primær, sekundær]                                                                                                                                                                                             |
| StaticIP            | -p, – statiske ip-< statiske ip->             | Når vært for din cache i en VNET, du angiver en entydig IP-adresse i undernet til cachen. Hvis du ikke er angivet, vælges en for dig fra undernettet.                                                                                                |
| Undernet              | t, – undernet<subnet>                    | Når vært for din cache i en VNET, du angiver navnet på det undernet, hvor du kan installere cachen.                                                                                                                                                    |
| VirtualNetwork      | -v, – virtuelt netværk < virtuelt-netværk > | Når vært for din cache i en VNET, du angiver den nøjagtige ARM ressource-ID i det virtuelle netværk for at installere redis cachen i. Eksempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Abonnement        | -s, -abonnement                      | Abonnement-id.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Se alle Redis Cache-kommandoer

Hvis du vil se alle Redis Cache kommandoer og deres parametre, skal du bruge den `azure rediscache -h` kommandoen.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Oprette en Redis Cache

Hvis du vil oprette en Redis Cache, skal du bruge følgende kommando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache create -h` kommandoen.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Slette en eksisterende Redis Cache

Hvis du vil slette en Redis Cache, skal du bruge følgende kommando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache delete -h` kommandoen.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Liste over alle Redis cachelagre inden for dit abonnement eller ressourcegruppe

Du kan få vist alle Redis cachelagre inden for dit abonnement eller ressourcegruppe, kan du bruge følgende kommando:

    azure rediscache list [options]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache list -h` kommandoen.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Vise egenskaberne for en eksisterende Redis Cache

For at vise egenskaberne for en eksisterende Redis Cache skal du bruge følgende kommando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache show -h` kommandoen.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Skift indstillinger for en eksisterende Redis Cache

Hvis du vil ændre indstillingerne for en eksisterende Redis Cache, skal du bruge følgende kommando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache set -h` kommandoen.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Forny tasten godkendelse til en eksisterende Redis Cache

For at forny tasten godkendelse til en eksisterende Redis Cache, skal du bruge følgende kommando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Angive `Primary` eller `Secondary` for `key-type`.

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache renew-key -h` kommandoen.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Listen primær og sekundær taster på en eksisterende Redis Cache

På listen primær og sekundær funktionstaster af en eksisterende Redis Cache, skal du bruge følgende kommando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Du kan finde flere oplysninger om denne kommando køre den `azure rediscache list-keys -h` kommandoen.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
