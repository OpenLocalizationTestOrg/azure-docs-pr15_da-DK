<properties
   pageTitle="Adgang og sikkerhed i Azure ressourcestyring skabeloner | Microsoft Azure" 
   description="Azure virtuelt DotNet Core selvstudium"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Adgang og sikkerhed i Azure ressourcestyring skabeloner

Programmer, der er hostet i Azure sandsynligvis skal være adgang via internettet eller en VPN-/ Express distribuere forbindelse med Azure. Med eksemplet musik Store-program er på webstedet gjort tilgængelig på internettet med en offentlig IP-adresse. Med access etableret skal forbindelser til programmet og adgang til virtuelt ressourcerne selv være fastgjort. Denne access-sikkerhed leveres med en netværk sikkerhedsgruppe. 

Dette dokument indeholder oplysninger om hvordan programmet musik Store sikres i skabelonen eksempel Azure ressourcestyring. Alle afhængigheder og unikke konfigurationer er fremhævet. Implementere en forekomst af løsningen til Azure-abonnement og arbejde sammen med skabelonen Azure Ressourcestyring for den bedste oplevelse. Skabelonen fuldført kan findes her – [Musik Store installation i Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).


## <a name="public-ip-address"></a>Offentlige IP-adresse

For at give offentlig adgang til en Azure ressource, kan en offentlige IP-adresseressource bruges. Offentlige IP-adresse kan være konfigureret med en fast eller dynamisk IP-adresse. Hvis en dynamisk adresse bruges, og den virtuelle maskine er ikke længere og deallokeres, fjernes adresserne. Når computeren startes igen, kan blive udpeget, en anden offentlige IP-adresse. For at forhindre en IP-adresse, kan bruges en reserverede IP-adresse. 

En offentlig IP-adresse kan tilføjes til en Azure ressourcestyring skabelon ved hjælp af Visual Studio guiden Tilføj ny ressource eller ved at indsætte gyldige JSON i en skabelon. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Offentlige IP-adresse](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

En offentlig IP-adresse kan være knyttet til et virtuelt netværkskort eller belastningsjustering. I dette eksempel, fordi webstedet musik Store er på tværs af flere virtuelle maskiner, er den offentlige IP-adresse knyttet til den belastning.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [offentlige IP-adresse tilknytning til justering af belastning](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Den offentlige IP-adresse som vist fra Azure-portalen. Bemærk, at den offentlige IP-adresse er knyttet til en justering af belastning og ikke en virtuel maskine. Netværk Indlæs balancere gennemgås i næste dokumentet i denne serie.

![Offentlige IP-adresse](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

Du kan finde flere oplysninger om Azure offentlige IP-adresser, [IP-adresser i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Netværk sikkerhedsgruppe

Når access er fastsat til Azure ressourcer, begrænses denne adgang. Til Azure virtuelle maskiner opnås sikker adgang ved hjælp af en sikkerhedsgruppe netværk. Med eksemplet musik Store programmet alle adgang til den virtuelle maskine er begrænset til med undtagelse af over port 80 til HTTP-adgang, og port 3389 for RDP adgang. En sikkerhedsgruppe netværk kan tilføjes til en Azure ressourcestyring skabelon ved hjælp af Visual Studio guiden Tilføj ny ressource eller ved at indsætte gyldige JSON i en skabelon.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Netværk sikkerhedsgruppe](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57).

```none
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

I dette eksempel er sikkerhedsgruppen netværk Tilknyt med undernetobjektet erklæret i virtuelt netværk ressourcen. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [netværk sikkerhedsgruppe tilknytning til virtuelt netværk](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143).


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

Her er, hvordan sikkerhedsgruppen netværk ser ud fra Azure-portalen. Bemærk, at en NSG kan være tilknyttes en undernet og / eller netværk grænseflade. I dette tilfælde er NSG knyttet til et undernet. I denne konfiguration gælder indgående regler for alle virtuelle maskiner, der er forbundet til undernettet.

![Netværk sikkerhedsgruppe](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

Se [Hvad er en netværk sikkerhedsgruppe]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)til dybdegående oplysninger om netværk sikkerhedsgrupper.

## <a name="next-step"></a>Næste trin

<hr>

[Trin 3 - tilgængelighed og skalering i Azure ressourcestyring skabeloner](./virtual-machines-windows-dotnet-core-4-availability-scale.md)
