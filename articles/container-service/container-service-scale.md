<properties
   pageTitle="Tilpasse din ACS klynge med Azure CLI | Microsoft Azure"
   description="Sådan skalere dit Azure objektbeholder tjenesten klynge, ved hjælp af Azure CLI."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, objektbeholdere, Micro-tjenester, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Skalere et Azure objektbeholder tjenesten

Du kan skalere ud antallet af knuder din Azure objektbeholder tjenesten (ACS) har ved hjælp af værktøjet Azure CLI. Når du bruger Azure CLI skalere, returnerer værktøjet en ny konfigurationsfil, som repræsenterer den ændring, der er anvendt på objektbeholderen.

## <a name="about-the-command"></a>Om kommandoen

Azure CLI skal være i Azure ressourcestyring tilstand for dig at interagere med Azure beholdere. Du kan skifte til Ressourcestyring tilstand ved at ringe til `azure config mode arm`. Den `acs` kommandoen har en underordnet-kommando med navnet `scale` , der indeholder alle skala handlinger for en objektbeholder tjenesten. Du kan få hjælp til de forskellige parametre, bruges i kommandoen skala ved at køre `azure acs scale --help`, som udskriver noget nogenlunde sådan ud:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Bruge kommandoen til at skalere

Hvis du vil skalere en objektbeholder tjenesten, skal du først kende **ressourcegruppe** og **Azure objektbeholder tjenesten (ACS) navn**, og også angive nye antallet af supportmedarbejdere. Ved hjælp af et mindre eller større beløb, kan du tilpasse ned eller op henholdsvis.

Du muligvis gerne vide det aktuelle antallet af supportmedarbejdere til en objektbeholder tjenesten, før du skalere. Brug den `azure acs show <resource group> <ACS name>` kommandoen til at returnere ACS config. Bemærk <mark>Tæl</mark> resultatet.

#### <a name="see-current-count"></a>Se aktuelle Tæl

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Skalere til nye tæller

Som det er sikkert allerede umiddelbart indlysende, kan du tilpasse objektbeholder tjenesten ved at ringe til `azure acs scale` og angivelse af **ressourcegruppe**, **ACS navn**og **agent Tæl**. Når du skalere en objektbeholder tjenesten, returnerer Azure CLI JSON streng, der repræsenterer den nye konfiguration af objektbeholder tjenesten, herunder nye agent antallet.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Næste trin

- [Installere en klynge](container-service-deployment.md)