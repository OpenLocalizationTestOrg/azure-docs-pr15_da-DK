
<properties
   pageTitle="Oprette et komplet Linux-miljø ved hjælp af Azure CLI | Microsoft Azure"
   description="Oprette lagerplads, en Linux VM, et virtuelt netværk og undernet, belastningsjustering, en NIC, en offentlige IP- og en netværk sikkerhedsgruppe, alle fra bunden op ved hjælp af Azure CLI."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Oprette et komplet Linux-miljø ved hjælp af Azure CLI

I denne artikel oprette vi et simpelt netværk med belastningsjustering og et par VM'er, der er nyttige til udvikling og enkel databehandling. Vi fører gennem processen kommandoen ved kommandoen, indtil du har to FOS med arbejdstid, sikker Linux, du kan oprette forbindelse fra et vilkårligt sted på internettet. Derefter kan du gå videre til mere komplekse netværk og miljøer.

Langs måde, du få mere at vide om hierarkiet afhængighed, implementeringsmodel ressourceleder, der giver dig, og om, hvor meget power den. Når du kan se, hvordan systemet er oprettet, kan du genopbygger det meget hurtigere ved hjælp af [Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). Også, når du lære, hvordan delene af dit miljø passer sammen, oprette skabeloner for at automatisere dem bliver nemmere.

Miljøet indeholder:

- To FOS i et sæt tilgængelighed.
- Belastningsjustering med en regel for justering af belastning på port 80.
- Netværk sikkerhed gruppe (NSG) regler til at beskytte din VM mod uønskede trafik.

![Oversigt over grundlæggende miljø](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Hvis du vil oprette dette brugerdefinerede miljø, du har brug for den seneste [Azure CLI](../xplat-cli-install.md) i ressourcestyring tilstand (`azure config mode arm`). Du skal også en JSON parsing af værktøjet. I dette eksempel bruges [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Hurtig kommandoer
Hvis du har brug for til hurtigt at udføre opgaven, følgende oplysninger i afsnittet grundtallet kommandoer til at overføre en VM til Azure. Mere detaljerede oplysninger og kontekst for hvert trin i resten af dokumentet, Start [her](#detailed-walkthrough).

Sørg for, at du har [Azure CLI](../xplat-cli-install.md) logget ind, og brug af Ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne omfatter `myResourceGroup`, `mystorageaccount`, og `myVM`.

Oprette ressourcegruppen. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `westeurope` placering:

```bash
azure group create -n myResourceGroup -l westeurope
```

Kontrollere ressourcegruppen ved hjælp af JSON-parseren:

```bash
azure group show myResourceGroup --json | jq '.'
```

Oprette kontoen lagerplads. I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount` (kontonavn lager skal være entydige, så du skal angive dine egne entydigt navn):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Bekræfte lager konto ved hjælp af JSON-parseren:

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Oprette det virtuelle netværk. I følgende eksempel oprettes et virtuelt netværk med navnet `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Oprette et undernet. I følgende eksempel oprettes et undernet med navnet `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Kontrollér virtuelt netværk og undernet ved hjælp af JSON-parseren:


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Oprette en offentlige IP-adresse. I følgende eksempel oprettes en offentlige IP-adresse med navnet `myPublicIP` med DNS-navnet på `mypublicdns` (DNS-navnet skal være entydige, så du skal angive dine egne entydigt navn):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Oprette justering af belastning. I følgende eksempel oprettes en justering af belastning med navnet `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Oprette en front-end-IP-samling for justering af belastning og knytte den offentlige IP-adresse. I følgende eksempel oprettes en front end IP-gruppe med navnet `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Oprette IP-back end-puljen for justering af belastning. I følgende eksempel oprettes en back end-IP-gruppe, der hedder `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Opret SSH indgående netværk adresse oversættelse (NAT) regler for justering af belastning. I følgende eksempel oprettes to Indlæs belastningsjusteringstjenesten regler, `myLoadBalancerRuleSSH1` og `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Oprette webstedet indgående NAT regler for justering af belastning. I følgende eksempel oprettes en Indlæs belastningsjusteringstjenesten regel, der hedder`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Oprette efterprøvning af belastning belastningsjusteringstjenesten sundhed af. I følgende eksempel oprettes en TCP-test med navnet `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Kontrollere justering af belastning, IP-grupper og NAT regler ved hjælp af JSON-parseren:

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Oprette den første netværkskort (NIC). Erstatte den `#####-###-###` sektioner med dine egne Azure abonnement-ID. Dit abonnement ID er noteret i output fra `jq` når du undersøger de ressourcer, du opretter. Du kan også få vist dit abonnement-ID med `azure account list`. 

I følgende eksempel oprettes en NIC med navnet `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Oprette den anden NIC. I følgende eksempel oprettes en NIC med navnet `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Kontrollere de to netværkskort ved hjælp af JSON-parseren:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Oprette sikkerhedsgruppen netværk. I følgende eksempel oprettes en netværk sikkerhedsgruppe med navnet `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Tilføj to indgående regler for sikkerhedsgruppe netværk. I følgende eksempel oprettes to regler `myNetworkSecurityGroupRuleSSH` og `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Du kan kontrollere netværk sikkerhedsgruppe og indgående regler ved hjælp af JSON-parseren:

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Binde sikkerhedsgruppen netværk til de to netværkskort:

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Oprette sættet tilgængelighed. I følgende eksempel oprettes et sæt med navnet tilgængelighed `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Oprette den første Linux VM. I følgende eksempel oprettes en VM med navnet `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Oprette den anden Linux VM. I følgende eksempel oprettes en VM med navnet `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Brug JSON-parseren til at kontrollere, at alt, der er bygget:

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Eksportere det nye miljø til en skabelon til hurtigt at oprette nye forekomster:

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Detaljeret gennemgang
De detaljerede efterfølgende trin beskriver, hvad hver kommando, der gør, efterhånden som du øger ud af dit miljø. Disse begreber er nyttige, når du opretter dine egne brugerdefinerede miljøer til udvikling som f.eks.

Sørg for, at du har [Azure CLI](../xplat-cli-install.md) logget ind, og brug af Ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne omfatter `myResourceGroup`, `mystorageaccount`, og `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Oprette ressourcegrupper, og vælg installation placeringer

Azure ressourcegrupper er logiske installation enheder, der indeholder oplysninger om konfiguration og metadata til at aktivere for logiske administration af ressource installationer. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `westeurope` placering:

```bash
azure group create --name myResourceGroup --location westeurope
```

Output:

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

Du har brug for lagerplads konti VM disken og om en eventuelle yderligere data disk, du vil tilføje. Du kan oprette lagerplads konti næsten samme, når du opretter grupper.

Her vi bruger den `azure storage account create` kommando, der passerer placeringen af kontoen, ressourcegruppen, der bestemmer den, og typen lagerplads support, du vil. I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Output:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Undersøge vores ressourcegruppe ved hjælp af den `azure group show` kommandoen, vi bruge værktøjet [jq](https://stedolan.github.io/jq/) sammen med den `--json` Azure CLI indstilling. (Du kan bruge **jsawk** eller et sprog-bibliotek, du foretrækker at fortolke JSON.)

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Hvis du vil undersøge kontoen lagerplads ved hjælp af CLI, skal du først angive kontonavne og taster. Erstat navnet på kontoen, lagerplads i følgende eksempel med et navn, som du vælger:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Derefter kan du se lagerplads oplysninger nemt:

```bash
azure storage container list
```

Output:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Oprette et virtuelt netværk og undernet

Du skal næste skal oprette et virtuelt netværk, der kører i Azure og et undernet, hvor du kan oprette din FOS. I følgende eksempel oprettes et virtuelt netværk med navnet `myVnet` med den `192.168.0.0/16` adressepræfiks:

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Output:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Igen skal vi bruge indstillingen – json af `azure group show` og **jq** at se, hvordan vi opretter vores ressourcer. Vi har nu en `storageAccounts` ressource og en `virtualNetworks` ressource.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Nu Lad os oprette et undernet i den `myVnet` virtuelt netværk, hvor FOS er installeret. Vi bruger den `azure network vnet subnet create` kommando sammen med de ressourcer, som vi allerede har oprettet: den `myResourceGroup` ressourcegruppe og `myVnet` virtuelt netværk. I eksemplet nedenfor vi tilføje det med navnet undernet `mySubnet` sammen med præfikset undernet adresse af `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Output:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Da undernettet er logisk i det virtuelle netværk, er vi se undernet oplysninger med en lidt anden kommando. Kommandoen vi bruge er `azure network vnet show`, men vi fortsætte med at undersøge JSON output ved hjælp af **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Output:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Oprette en offentlig IP-adresse (PIP)

Nu Lad os oprette den offentlige IP-adresse (PIP), som vi tildeler til din justering af belastning. Det gør det muligt at oprette forbindelse til din FOS fra internettet ved hjælp af den `azure network public-ip create` kommandoen. Da standardadressen er dynamisk, vi oprette en navngivet DNS-post i **cloudapp.azure.com** domæne ved hjælp af den `--domain-name-label` indstilling. I følgende eksempel oprettes en offentlige IP-adresse med navnet `myPublicIP` med DNS-navnet på `mypublicdns`. Angiv din egen entydigt DNS-navn, som DNS-navnet skal være entydig:

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Output:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Den offentlige IP-adresse er også en ressource, som på øverste niveau, så du kan se den med `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Du kan undersøge flere ressourcedetaljer, herunder det fuldstændige domænenavn (fulde Domænenavn) af underdomænet, ved hjælp af den komplette `azure network public-ip show` kommandoen. Den offentlige IP-adresseressource har fået tildelt logisk, men en bestemt adresse ikke er blevet tildelt endnu. For at hente en IP-adresse, skal du bruge en justering af belastning, som vi endnu ikke har oprettet.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Output:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Oprette en justering af belastning og IP-grupper
Når du opretter en belastningsjustering, kan du distribuere trafik på tværs af flere FOS. Den indeholder også redundans i dit program ved at køre flere FOS, besvare anmodninger tale vedligeholdelse eller stor belastning. I følgende eksempel oprettes en justering af belastning med navnet `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Output:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Vores justering af belastning er ret tomt, så lad os oprette nogle IP-grupper. Vi vil oprette to IP-grupper til vores justering af belastning - én til front end og én til back-end. IP-front end-puljen er offentligt synlige. Det er også den placering, hvor vi kan tildele PIP, vi oprettede tidligere. Derefter bruger vi back end-puljen som en placering til vores FOS at oprette forbindelse til. På denne måde trafikken kan bevæge sig gennem justering af belastning til FOS.

Først skal Lad os oprette vores front end IP-puljen. I følgende eksempel oprettes en front end-puljen med navnet `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Output:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Bemærk, hvor vi har brugt den `--public-ip-name` skifte til at videregive i den `myPublicIP` , vi oprettede tidligere. Tildeling af den offentlige IP-adresse til justering af belastning gør det muligt at nå dine FOS på tværs af internettet.

Næste, Lad os oprette vores anden IP-puljen, denne gang til vores back end-trafik. I følgende eksempel oprettes en back end-puljen med navnet `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Output:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Vi kan se, hvordan vores justering af belastning sig ved at se med `azure network lb show` og undersøge, om JSON output:

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Oprette justering af belastning NAT regler
Hvis du vil have trafik der flyder gennem vores justering af belastning, skal vi oprette netværksadresse oversættelse (NAT) regler, der angiver indgående eller udgående handlinger. Du kan angive protokollen til brug af og derefter knytte eksterne porte til interne porte efter behov. Lad os oprette nogle regler, der tillader SSH gennem vores justering af belastning til vores FOS for vores miljø. Vi konfigurere TCP-portnumre 4222 og 4223 til at dirigere til TCP-port 22 på vores FOS (som vi opretter senere). I følgende eksempel oprettes en regel med navnet `myLoadBalancerRuleSSH1` skal tilknyttes port 22 TCP-port 4222:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Output:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Gentag proceduren for anden NAT reglen for SSH. I følgende eksempel oprettes en regel med navnet `myLoadBalancerRuleSSH2` skal tilknyttes port 22 TCP-port 4223:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Lad os også gå videre og oprette en NAT regel for TCP-port 80 til webtrafik, tilslutte reglen op til vores IP-grupper. Hvis vi Tilslut reglen til IP-puljen, i stedet for at tilslutte reglen til vores FOS enkeltvis, kan vi tilføje eller fjerne FOS fra IP-puljen. Justering af belastning justerer automatisk strømmen af trafik. I følgende eksempel oprettes en regel med navnet `myLoadBalancerRuleWeb` skal tilknyttes port 80 TCP-port 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Output:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Oprette efterprøvning af af et Indlæs belastningsjusteringstjenesten tilstand

En tilstand forespørgsler med jævne mellemrum Kontroller af VM'er, der er bag vores justering af belastning at sikre, at de operativsystem og besvare mødeindkaldelser, som defineret. Hvis ikke, de er fjernet fra handling til at sikre, at brugere ikke blive bedt om at dem. Du kan definere brugerdefinerede Kontroller til tilstand efterprøvning af af, sammen med intervaller og timeoutværdier. Se [justering af belastning sonder](../load-balancer/load-balancer-custom-probe-overview.md)kan finde flere oplysninger om sundhed sonder. I følgende eksempel oprettes en TCP sundhed undersøges navngivne `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Output:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Vi angivet her, et interval på 15 sekunder til vores tilstandskontrol. Vi kan gå glip af op til fire sonder (et minut) før justering af belastning mener, at værten fungerer ikke længere.

## <a name="verify-the-load-balancer"></a>Bekræfte justering af belastning
Nu er Indlæs belastningsjusteringstjenesten konfigurationen fuldført. Her er de trin, du oprindeligt brugte for:

1. Først du har oprettet en justering af belastning.
2. Derefter du har oprettet en front end-puljen IP- og tildelt en offentlige IP-adresse.
3. Næste, du har oprettet en back end-IP-pulje, der FOS kan oprette forbindelse til.
4. Du har oprettet NAT regler, der tillader SSH til FOS til at administrere, sammen med en regel, der gør det muligt for TCP-port 80 til vores online herefter.
5. Til sidst du har tilføjet en efterprøvning af sundhed af for at kontrollere FOS med jævne mellemrum. I denne tilstand efterprøvning af af sikrer, at brugerne ikke forsøger at få adgang til en VM, der ikke længere fungerer eller fungerer indhold.

Lad os gennemgå dine justering af belastning ligner nu:

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Oprette en NIC til brug med Linux VM

Netværkskort er tilgængelige fra et program, da du kan anvende regler på deres brug. Du kan også har mere end én. I følgende `azure network nic create` kommando, du logge på NIC for at indlæse back end-IP-puljen, og knytte den til NAT reglen til at tillade SSH trafik.
 
Erstatte den `#####-###-###` sektioner med dine egne Azure abonnement-ID. Dit abonnement ID er noteret i output fra `jq` når du undersøger de ressourcer, du opretter. Du kan også få vist dit abonnement-ID med `azure account list`. 

I følgende eksempel oprettes en NIC med navnet `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Output:

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Du kan se detaljerne ved at undersøge ressourcen direkte. Du undersøge ressourcen ved hjælp af den `azure network nic show` kommandoen:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Nu skal vi oprette den anden NIC, tilslutte i vores back end-IP-puljen igen. Denne gang andet NAT regel tillader SSH trafik. I følgende eksempel oprettes en NIC med navnet `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Oprette en sikkerhedsgruppe netværk og regler

Nu skal vi oprette en sikkerhedsgruppe netværk og de indgående regler, der styrer adgang til på NIC. En sikkerhedsgruppe netværk kan anvendes til en NIC eller undernet. Du angive regler for at styre strømmen af trafik og din FOS. I følgende eksempel oprettes en netværk sikkerhedsgruppe med navnet `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Lad os tilføje indgående reglen for NSG til at tillade indgående forbindelser på port 22 (til at understøtte SSH). I følgende eksempel oprettes en regel med navnet `myNetworkSecurityGroupRuleSSH` tillade TCP-port 22:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Nu Lad os tilføje indgående reglen for NSG til at tillade indgående forbindelser på port 80 (til at understøtte webtrafik). I følgende eksempel oprettes en regel med navnet `myNetworkSecurityGroupRuleHTTP` tillade TCP-port 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] Indgående reglen er et filter for indgående netværksforbindelser. I dette eksempel skal binde vi NSG til den virtuelle FOS-NIC, hvilket betyder, at en anmodning til port 22 er passerede gennem til NIC på vores VM. Denne indgående regel er om en netværksforbindelse, og ikke om et slutpunkt, som er hvad det ville være om i klassisk installationer. Hvis du vil åbne en port, skal du lade den `--source-port-range` indstillet til '\*' (standardværdien) til at acceptere indgående anmodninger fra **en hvilken som helst** anmode om port. Port er typisk dynamisk.

## <a name="bind-to-the-nic"></a>Bindes til NIC

Binde NSG til netværkskortene. Vi har brug at oprette forbindelse vores netværkskort med vores netværk sikkerhedsgruppe. Køre begge kommandoer til at logge på begge af vores netværkskort:

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Oprette et sæt tilgængelighed
Tilgængelighed angiver Hjælp opslag din FOS på tværs af et domæner og opgradering domæner. Lad os oprette en tilgængelighed, der er angivet for din FOS. I følgende eksempel oprettes et sæt med navnet tilgængelighed `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Fejl domæner definere en gruppering af virtuelle maskiner, der deler en fælles power kilde- og netværk parameter. Som standard er de virtuelle maskiner, der er konfigureret i din tilgængelighed sæt adskilt på tværs af op til tre fejl domæner. Formålet er, at et hardwareproblem på en af disse fejl domæner ikke påvirker alle VM, der kører din app. Azure distribuerer automatisk FOS domæner fejl, når du indsætter dem i et sæt tilgængelighed.

Opgradering domæner angiver grupper af virtuelle maskiner og underliggende fysisk hardware, der kan genstartes på samme tid. Den rækkefølge, hvori opgradering domæner er genstartet kan ikke være fortløbende under planlagt vedligeholdelse, men kun én opgraderingen genstartes ad gangen. Igen, Azure automatisk distribuerer dine FOS på tværs af opgradering domæner når du indsætter dem i et websted med tilgængelighed.

Få mere at vide om [Administration af tilgængeligheden af FOS](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Oprette Linux FOS

Du har oprettet i lager og netværk ressourcer for at understøtte Internet-tilgængelige FOS. Nu Lad os oprette disse FOS og sikre dem med en SSH nøgle, der ikke har en adgangskode. I dette tilfælde skal vi oprette en Ubuntu VM baseret på den seneste LTS. Vi finde disse billedoplysninger ved hjælp af `azure vm image list`, som beskrevet i [finde Azure VM billeder](virtual-machines-linux-cli-ps-findimage.md).

Vi valgte et billede ved hjælp af kommandoen `azure vm image list westeurope canonical | grep LTS`. I dette tilfælde skal vi bruge `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Til det sidste felt vi overfører `latest` så fremover vi altid få det seneste build. (Den streng, vi bruger er `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Dette næste trin er velkendt for alle, der allerede har oprettet en ssh rsa-offentlige og private nøgler par på Linux eller Mac ved hjælp af **ssh keygen-t rsa -b 2048**. Hvis du ikke har nogen vigtige par certifikat din `~/.ssh` directory, kan du oprette dem:

- Automatisk, ved hjælp af den `azure vm create --generate-ssh-keys` indstilling.
- Manuelt ved hjælp af [vejledningen for at oprette dem til dig selv](virtual-machines-linux-mac-create-ssh-keys.md).

Du kan også bruge den `--admin-password` metode til at godkende dine SSH forbindelser, når VM er blevet oprettet. Denne metode er typisk mindre sikker.

Vi oprette VM ved at tage alle vores ressourcer og oplysninger sammen med den `azure vm create` kommandoen:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Output:

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Du kan oprette forbindelse til din VM med det samme ved hjælp af din standard SSH taster. Sørg for, at du angiver den korrekte port, da vi der passerer gennem justering af belastning. (For vores første VM, vi konfigurere NAT reglen for at videresende port 4222 til vores VM):

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Output:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Gå videre og oprette din anden VM på samme måde:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Og du kan nu bruge den `azure vm show myResourceGroup myVM1` kommandoen til at undersøge, hvad du har oprettet. På dette tidspunkt, kører du din Ubuntu FOS bag en belastningsjustering i Azure, som du kan logge på kun med dit SSH vigtige par (fordi adgangskoder er deaktiveret). Du kan installere nginx eller httpd, installere en WebApp og se trafikken forløb gennem justering af belastning både FOS.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Output:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Eksportere miljøet som en skabelon
Nu hvor du har oprettet af dette miljø, hvad nu, hvis du vil oprette et ekstra udviklingsmiljø med de samme parametre eller et produktionsmiljø, der svarer til det? Ressourceleder, der bruger JSON-skabeloner, der definerer alle parametre for dit miljø. Du udvidelse hele miljøer ved at referere til denne JSON-skabelon. Du kan [oprette JSON skabeloner manuelt](../resource-group-authoring-templates.md) eller eksportere et eksisterende miljø for at oprette skabelonen JSON for dig:

```bash
azure group export --name myResourceGroup
```

Denne kommando opretter den `myResourceGroup.json` fil i din aktuelle arbejdsmappe. Når du opretter et miljø fra denne skabelon, kan du bliver bedt om alle de ressourcenavne, herunder navne for justering af belastning, netværksgrænseflader eller FOS. Du kan udfylde disse navne i skabelonfilen ved at tilføje den `-p` eller `--includeParameterDefaultValue` -parameter til den `azure group export` kommandoen, der tidligere blev vist. Redigere din JSON-skabelon til at angive ressourcenavne eller [oprette en parameters.json-fil](../resource-group-authoring-templates.md#parameters) , der angiver ressourcenavnene.

Sådan oprettes et miljø fra din skabelon:

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Du måske at læse [mere om, hvordan du installerer fra skabeloner](../resource-group-template-deploy-cli.md). Få mere at vide om, hvordan trinvist opdatere miljøer, bruge parameterfilen, og få adgang til skabeloner fra en enkelt lagerplacering.

## <a name="next-steps"></a>Næste trin

Nu er du klar til at begynde at arbejde med flere netværk komponenter og FOS. Du kan bruge dette eksempel miljø udvidelse dit program ved hjælp af de vigtigste komponenter introduceret her.
