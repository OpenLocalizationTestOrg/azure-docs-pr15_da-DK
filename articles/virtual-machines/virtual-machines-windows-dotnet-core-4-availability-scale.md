<properties
   pageTitle="Tilgængelighed og skalering i Azure ressourcestyring skabeloner | Microsoft Azure"
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

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Tilgængelighed og skalering i Azure ressourcestyring skabeloner

Tilgængelighed og skalering referere til oppetid og muligheden for at imødekomme behov. Hvis et program skal være en 99,9% af tid, skal det have en arkitektur, som gør det muligt for flere samtidige Beregn ressourcer. For eksempel i stedet for at få et enkelt websted, omfatter en konfiguration med et højere niveau over tilgængeligheden af flere forekomster af det samme sted, med justering af teknologi foran dem. I denne konfiguration kan én forekomst af programmet hentes til vedligeholdelse, mens de resterende vil fungere. Skalaen henviser på den anden side til en programmer mulighed for at kunne levere efter behov. Med en belastning kan ikke-opgjorte programmet, tilføje eller fjerne forekomster fra puljen et program skalere for at imødekomme behov.

Dette dokument indeholder oplysninger om hvordan musik Store eksempel installation er konfigureret for tilgængelighed og skalering. Alle afhængigheder og unikke konfigurationer er fremhævet. Implementere en forekomst af løsningen til Azure-abonnement og arbejde sammen med skabelonen Azure Ressourcestyring for den bedste oplevelse. Skabelonen fuldført kan findes her – [Musik Store installation i Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="availability-set"></a>Tilgængelighed sæt

Angive en tilgængelighed fylder logisk virtuelle Azure-computere fysisk værter og andre infrastructural komponenter som power forsyninger og hardware til fysisk netværk. Tilgængelighed sæt Sørg for, at der sker ikke alle virtuelle maskiner under vedligeholdelse, enhedsfejl eller andet nedetid. Angive en tilgængelighed kan tilføjes til en Azure ressourcestyring skabelon ved hjælp af Visual Studio guiden Tilføj ny ressource eller indsætte gyldige JSON i en skabelon.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Tilgængelighed angive](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L368).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "availability-set"
  },
  "properties": {
  }
}
```

Angive en tilgængelighed er erklæret som en egenskab for et virtuelt ressource. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [tilgængelighed angive tilknytning til virtuelt](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L302).

```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Tilgængeligheden angive som set fra Azure-portalen. Hver virtuelt og detaljer om konfigurationen detaljerede her.

![Tilgængelighed sæt](./media/virtual-machines-windows-dotnet-core/ase-win.png)

Du kan finde detaljerede oplysninger om tilgængelighed sæt, [Administrer tilgængeligheden af virtuelle maskiner](./virtual-machines-windows-manage-availability.md). 

## <a name="network-load-balancer"></a>Netværk justering af belastning

Mens en tilgængelighed sæt indeholder programmet fejltolerance, gør belastningsjustering mange forekomster af programmet tilgængelig på en enkelt netværksadresse. Flere forekomster af et program kan bruges på mange virtuelle maskiner, hver enkelt forbindelse til en justering af belastning. Som programmet, der opnås adgang, dirigerer belastning den indgående anmodning på tværs af de vedhæftede medlemmer. Belastningsjustering kan tilføjes ved hjælp af Visual Studio guiden Tilføj ny ressource, eller ved at indsætte korrekt formateret JSON ressource til Azure ressourcestyring skabelon.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Netværk justering af belastning](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L198).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer"
  },
  ........<truncated>
}
```

Fordi eksempelprogrammet er være tilgængelig på internettet med en offentlig IP-adresse, er denne adresse er knyttet til justering af belastning. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [netværk justering af belastning tilknytning til offentlige IP-adresse](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211).

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

Fra Azure-portalen viser netværk Indlæs belastningsjusteringstjenesten oversigt tilknytningen til den offentlige IP-adresse.

![Netværk justering af belastning](./media/virtual-machines-windows-dotnet-core/nlb-win.png)

## <a name="load-balancer-rule"></a>Indlæse belastningsjusteringstjenesten regel

Når du bruger en belastningsjustering, konfigureres regler, der bestemmer, hvordan trafik fordeles på tværs af de tilsigtede ressourcer. Med programmet eksempel musik Store trafik modtages på port 80 af den offentlige IP-adresse og er fordelt over port 80 af alle virtuelle maskiner. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Indlæs belastningsjusteringstjenesten regel](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L226).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

En visning af netværk Indlæs belastningsjusteringstjenesten regel fra portalen.

![Netværk Indlæs belastningsjusteringstjenesten regel](./media/virtual-machines-windows-dotnet-core/lbrule-win.png)

## <a name="load-balancer-probe"></a>Efterprøvning af justering af belastning

Justering af belastning skal også have til at overvåge hver virtuel maskine, så anmodninger sendes kun til computere. Denne overvågning finder sted ved konstant test af en foruddefineret port. Musik Store installationen er konfigureret til at sende forespørgsler port 80 på alle inkluderet virtuelle computere. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Indlæs belastningsjusteringstjenesten forespørgsler](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L247).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Indlæs belastningsjusteringstjenesten efterprøvning af af set fra Azure-portalen.

![Netværk Indlæs belastningsjusteringstjenesten efterprøvning af af](./media/virtual-machines-windows-dotnet-core/lbprobe-win.png)

## <a name="inbound-nat-rules"></a>Indgående NAT regler

Når du bruger en belastning, skal lægges til det ønskede sted, der giver ikke belastning ikke-opgjorte adgang til hver virtuelt regler. Når du opretter en RDP-forbindelse med hver virtuelt, denne trafik må ikke være Indlæs afstemmes for eksempel, hellere en forudbestemt sti skal konfigureres. Foruddefinerede stier er konfigureret ved hjælp af en indgående NAT regel ressource. Ved hjælp af denne ressource kan indgående kommunikation knyttes til individuelle virtuelle computere. 

Med musik Store-program, er en port fra og med 5000 tilknyttet port 3389 på hver virtuel maskine til RDP-adgang. Den `copyindex()` bruges funktionen til at øge den indgående port, så den anden virtuelle maskine modtager en indgående port af 5001, de tredje 5002 og så videre.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Indgående NAT regler](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L260). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'RDP-VM', copyIndex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 3389,
    "enableFloatingIP": false
  }
}
```

Et eksempel indgående NAT-regel, som det fremgår Azure portalen. Der oprettes en RDP NAT reglen for hver virtuelt i installationen.

![Indgående NAT regel](./media/virtual-machines-windows-dotnet-core/natrule-win.png)

Se [belastning til Azure-infrastrukturtjenester](./virtual-machines-windows-load-balance.md)til dybdegående oplysninger om den Azure netværk justering af belastning.

## <a name="deploy-multiple-vms"></a>Installere flere FOS

Til sidst skal for et angive tilgængelighed eller justering af belastning kan fungere effektivt, er flere virtuelle maskiner påkrævet. Flere FOS kan installeres ved hjælp af funktionen Azure ressourcestyring skabelon kopi. Brug af kopieringsfunktionen, det er ikke nødvendigt at definere et begrænset antal virtuelle maskiner, hellere denne værdi kan dynamisk leveres på tidspunktet for installation. Kopieringsfunktionen forbruger antallet af forekomster, der skal oprettes og håndtag anvender det korrekte antal virtuelle maskiner og de tilknyttede ressourcer.

En parameter er defineret, der henter en forekomst Tæl i skabelonen musik Store eksempel. Dette nummer bruges i hele skabelonen, når du opretter virtuelle computere og relaterede ressourcer.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 2,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

Kopiér løkke gives på ressourcen virtuelt et navn og antallet af forekomster parameter bruges til at styre antallet resulterende kopier.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Virtuelt kopieringsfunktionen](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L290). 


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  }
```

Du kan få adgang til den aktuelle gentagelse af kopieringsfunktionen med den `copyIndex()` funktionen. Værdien af funktionen kopi indeks kan bruges til at navngive virtuelle computere og andre ressourcer. Eksempelvis hvis to forekomster af et virtuelt er installeret, skal de forskellige navne. Den `copyIndex()` funktionen kan bruges som en del af navnet på den virtuelle computer til at oprette et entydigt navn. Et eksempel på den `copyindex()` funktion, som bruges til navngivning kan ses i virtuelt ressourcen. Her, navnet på computer er en sammenkædning af den `vmName` parameter, og den `copyIndex()` funktionen. 

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [Kopiér indeks-funktionen](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L309). 


```none
"osProfile": {
  "computerName": "[concat(variables('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "adminPassword": "[parameters('adminPassword')]"
}
```

Den `copyIndex` funktionen bruges flere gange i skabelonen musik Store eksempel. Ressourcer og -funktioner ved hjælp af `copyIndex` omfatter noget, der er specifikke for en enkelt forekomst af den virtuelle maskine sådanne og netværksgrænsefladen, regler for indlæsning af belastningsjusteringstjenesten, og en afhænger af funktioner. 

Se [oprette flere forekomster af ressourcer i Azure ressourcestyring](../resource-group-create-multiple.md)kan finde flere oplysninger om kopieringsfunktionen.

## <a name="next-step"></a>Næste trin

<hr>

[Trin 4 - program-installation med Azure ressourcestyring skabeloner](./virtual-machines-windows-dotnet-core-5-app-deployment.md)