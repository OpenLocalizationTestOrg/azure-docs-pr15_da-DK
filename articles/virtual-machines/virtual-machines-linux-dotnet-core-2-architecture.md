<properties
   pageTitle="Implementere beregne ressourcer med Azure ressourcestyring skabeloner | Microsoft Azure"
   description="Azure virtuelt DotNet Core selvstudium"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Programmet arkitektur med Azure ressourcestyring skabeloner

Når du udvikler en Azure ressourcestyring installation, kan du beregne krav skal knyttes til Azure ressourcer og -tjenester. Hvis et program består af flere http slutpunkter, en database og datatypen cachelagring service, Azure ressourcer, der er vært hver af disse komponenter skal være rationalized. For eksempel indeholder programmet eksempel musik Store et webprogram, der er placeret på en virtuel maskine, og en SQL-database, som er hostet i Azure SQL-database. 

Dette dokument indeholder oplysninger om hvordan musik Store Beregn ressourcer er konfigureret i skabelonen eksempel Azure ressourcestyring. Alle afhængigheder og unikke konfigurationer er fremhævet. Implementere en forekomst af løsningen til Azure-abonnement og arbejde sammen med skabelonen Azure Ressourcestyring for den bedste oplevelse. Skabelonen fuldført kan findes her – [Musik Store installation på Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="virtual-machine"></a>Virtuelt

Programmet musik Store omfatter et webprogram, hvor kunder kan finde og købe musik. Selvom der findes flere Azure tjenester, der kan være vært for webprogrammer i dette eksempel bruges en virtuel maskine. Ved hjælp af skabelonen eksempel musik Store, et virtuelt installeres, installere en webserver, og webstedet musik Store installeret og konfigureret. Af hensyn til denne artikel, er kun virtuelt installationen detaljeret. Konfiguration af webserveren og programmet gennemgås i en artikel til senere.

En virtuel maskine kan tilføjes til en skabelon, ved hjælp af guiden Visual Studio Tilføj ny ressource eller ved at indsætte gyldige JSON i skabelonen installation. Når du installerer en virtuel maskine, skal du også bruge flere relaterede ressourcer. Hvis du bruger Visual Studio til at oprette skabelonen, oprettes disse ressourcer for dig. Hvis manuelt bygning af skabelonen, skal disse ressourcer indsættes og konfigureret.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Virtuelt JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Når installeret, kan du se egenskaberne virtuelt i portalen Azure.

![Virtuelt](./media/virtual-machines-linux-dotnet-core/vm.png)

## <a name="storage-account"></a>Lagerplads konto

Lagerplads konti har mange indstillinger for lagring af og funktioner. For konteksten af Azure virtuelle maskiner indeholder en lagerplads konto de virtuelle harddiske af den virtuelle maskine og eventuelle yderligere data diske. Eksemplet musik Store omfatter én lagerplads konto for at holde virtuelle harddisken på hver virtuelle maskine i installationen. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Lagerplads konto](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

En lagerplads konto er Tilknyt med en virtuel maskine i ressourcestyring skabelon erklæring af den virtuelle maskine. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [virtuelt og lagerplads konto sammenslutning](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Efter installation, kan kontoen lagerplads ses i portalen Azure.

![Lagerplads konto](./media/virtual-machines-linux-dotnet-core/storacct.png)

Hvis du klikker på konto blob objektbeholderen til lagring af, kan filen virtuelle harddisk for hver virtuelt, der er installeret med skabelonen ses.

![Virtuelle harddiske](./media/virtual-machines-linux-dotnet-core/vhd.png)

Du kan finde flere oplysninger om Azure-lager, dokumentationen [Azure-lager](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Virtuelt netværk

Hvis en virtuel maskine kræver interne netværk som muligheden for at kommunikere med andre virtuelle maskiner og Azure ressourcer, er et virtuelt Azure-netværk påkrævet.  Et virtuelt netværk gør ikke den virtuelle maskine tilgængelige via internettet. Forbindelse til offentlige kræver en offentlige IP-adresse, som er detaljeret senere i denne serie.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [virtuelt netværk og undernet](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
  }
}
```

Fra Azure-portalen, det virtuelle netværk, der ser ud som følgende billede. Bemærk, at alle virtuelle maskiner, der installeres med skabelonen er knyttet til det virtuelle netværk.

![Virtuelt netværk](./media/virtual-machines-linux-dotnet-core/vnet.png)

## <a name="network-interface"></a>Netværksgrænsefladen

 Et virtuelt grænsefladen et netværk opretter forbindelse til et virtuelt netværk og mere specifikt til et undernet, der er defineret i det virtuelle netværk. 
 
 Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring- [Grænsefladen netværk](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Hver virtuelt ressource omfatter en profil for netværk. Netværksgrænsefladen er knyttet til den virtuelle maskine i denne profil.  

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Virtuelt netværksprofil](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

Fra portalen Azure grænsefladen for det netværk, der ser ud som følgende billede. Den interne IP-adresse og virtuelt tilknytningen kan ses af netværk interface ressource.

![Netværksgrænsefladen](./media/virtual-machines-linux-dotnet-core/nic.png)

Finde flere oplysninger om Azure virtuelle netværk, dokumentationen [Azure virtuelt netværk](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Azure SQL-Database

Ud over en virtuel maskine, der er vært for webstedet musik Store, installeres Azure SQL-Database for at placere musik Store-database. Fordelen ved at bruge Azure SQL-Database her er, at et andet sæt af virtuelle maskiner er ikke påkrævet, og skalering og tilgængelighed er indbygget i tjenesten.

Du kan tilføje en Azure SQL-database ved hjælp af Visual Studio tilføje nye ressourcen guide, eller ved at indsætte gyldige JSON i en skabelon. SQL Server ressourcen indeholder et brugernavn og adgangskode, der er tildelt administratorrettigheder på SQL-forekomsten. Desuden tilføjes en SQL firewall ressource. Programmer, der er hostet i Azure er som standard kunne oprette forbindelse til SQL-forekomst. Hvis du vil tillade eksterne programmet skal sådanne en SQL Server Management studio til at oprette forbindelse til SQL-forekomsten, firewallen konfigureres. Af hensyn til demo musik Store er standardkonfigurationen fint. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

En visning af SQL server og MusicStore database som det fremgår Azure portalen.

![SQL Server](./media/virtual-machines-linux-dotnet-core/sql.png)

Du kan finde flere oplysninger om installation af Azure SQL-Database, dokumentationen [Azure SQL-Database](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Næste trin

<hr>

[Trin 2 - adgang og sikkerhed i Azure ressourcestyring skabeloner](./virtual-machines-linux-dotnet-core-3-access-security.md)
