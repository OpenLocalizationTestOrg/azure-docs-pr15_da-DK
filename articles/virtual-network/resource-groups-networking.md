<properties
   pageTitle="Netværk udbyder Ressourceoversigt | Microsoft Azure"
   description="Få mere at vide om den nye netværk ressource udbyder i Azure ressourcestyring."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Udbyderen af netværk ressource
Brug af en understøttelse i dags business succes er muligheden for at oprette og administrere omfattende netværk opmærksom på programmer i en fleksible, fleksibel, sikker og gentaget måde. Azure ressource Manager (ARM) gør det muligt at oprette disse programmer, som en enkelt samling af ressourcer i grupper. Disse ressourcer administreres via forskellige ressource udbydere under ARM.

Azure ressourcestyring er afhængig af anden ressource udbydere giver adgang til dine ressourcer. Der er tre primære ressource udbydere: netværk, opbevaring og Beregn. I dette dokument beskrives de egenskaber og fordele i udbyderen af ressource det netværk, herunder:

- **Metadata** – du kan føje oplysninger til ressourcer ved hjælp af mærker. Disse koder kan bruges til at spore ressource anvendelsen på tværs af grupper og abonnementer.
- **Større kontrol over netværket** – ressourcer er løst forbundne netværk, og du kan styre dem på en mere detaljeret måde. Det betyder, at du har større fleksibilitet i administrationen netværk ressourcer.
- **Hurtigere konfiguration** – fordi netværksressourcer er løst forbundne, du kan oprette og dirigerer netværksressourcer parallelt. Dette er kraftigt reduceret konfiguration tid.
- **Rolle baseret adgangskontrol** – RBAC indeholder standardroller med bestemte sikkerhed omfang, ud over at tillade oprettelse af brugerdefinerede roller for sikker administration.
- **Nemmere administration og installation** - det er nemmere at installere og administrere programmer, da kan kan du oprette en hele programmet stak som en enkelt samling af ressourcer i en ressourcegruppe. Og hurtigere at installere, da du kan installere ved blot at angive en skabelon JSON data.
- **Hurtig tilpasning** - du kan bruge deklarativ typografi skabeloner til at aktivere gentaget og hurtig tilpasning af installationer.
- **Gentaget tilpasning** - du kan bruge deklarativ typografi skabeloner til at aktivere gentaget og hurtig tilpasning af installationer.
- **Administration af grænseflader** - du kan bruge en af følgende grænseflader til at administrere dine ressourcer:
    - RESTEN baseret API
    - PowerShell
    - .NET SDK
    - Node.JS SDK
    - Java SDK
    - Azure CLI
    - Eksempel-portalen
    - ARM skabelonsprog

## <a name="network-resources"></a>Netværksressourcer
Du kan nu administrere netværksressourcer uafhængigt af hinanden, i stedet for få dem alle administreres via en enkelt Beregn ressource (en virtuel maskine). Dette sikrer, at en højere grad af fleksibilitet og fleksibilitet i ved at oprette en kompleks og store skala infrastruktur i en ressourcegruppe.

En begrebsmæssig visning af en stikprøve-installation, der involverer et program, der er flere niveauer vises nedenfor. Hver ressource, du kan se, som netværkskort, offentlige IP-adresser og FOS, kan administreres uafhængigt af hinanden.

![Netværk ressource model](./media/resource-groups-networking/Figure2.png)

Hver ressource indeholder en fælles gruppe egenskaber, og deres individuelle egenskaben indstillet. De fælles egenskaber er:

|Egenskaben|Beskrivelse|Eksempelværdier|
|---|---|---|
|**Navn**|Entydige ressourcenavn. Hver ressourcetype har sin egen naming begrænsninger.|PIP01, VM01, NIC01|
|**placering**|Azure område, hvor ressourcen er placeret|westus, eastus|
|**id**|Entydigt id for URI baseret|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Du kan se de individuelle egenskaber for ressourcer i afsnittene herunder.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Af administrationsgrænseflader
Du kan administrere dine Azure netværk ressourcer ved hjælp af forskellige grænseflader. I dette dokument vil vi fokusere på to af disse grænseflader: REST-API og skabeloner.

### <a name="rest-api"></a>REST-API
Som tidligere nævnt kan netværksressourcer administreres via en række forskellige grænseflader, herunder REST-API, .NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure Portal og skabeloner.

Rest-API svarer til HTTP 1.1 protocol-specifikationen. Den generelle URI struktur af API vises herunder:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Og parametre i klammeparenteser repræsenterer følgende elementer:

- **abonnement-id** - dit Azure abonnement-id.
- **ressource-udbyder-navneområde** - navneområdet for den udbyder, der bruges. Værdien for udbyderen af det netværk ressource er *Microsoft.Network*.
- **område-name** - områdenavnet Azure

Fremgangsmåderne HTTP understøttes, når du foretager opkald til REST-API:

- **PLACERE** - bruges til at oprette en ressource af en bestemt type redigere en ressourceegenskab for eller ændre en tilknytning mellem ressourcer.
- **Få** - bruges til at hente oplysninger for en klargjort ressource.
- **Slette** - bruges til at slette en eksisterende ressource.

Både anmodning og svar er i overensstemmelse med formatet JSON data. Du kan finde flere detaljer, [Azure ressource Management API'er](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>ARM skabelonsprog
Ud over administration af ressourcer imperatively (via API'er eller SDK), kan du også bruge en deklarativ programmering typografi til at oprette og administrere netværksressourcer ved hjælp af ARM skabelonsprog.

Et eksempel repræsentation af en skabelon leveres nedenfor-

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Skabelonen er primært en JSON beskrivelse af ressourcerne og forekomst værdierne tilføjet via parametre. I eksemplet nedenfor kan bruges til at oprette et virtuelt netværk med 2 undernet.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Du har mulighed for at give parameterværdierne manuelt, når du bruger en skabelon, eller du kan bruge en parameterfil. Eksemplet herunder viser et mulige sæt parameterværdier skal bruges sammen med skabelonen ovenfor:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


De vigtigste fordele ved brug af skabeloner er:

- Du kan oprette en kompleks infrastruktur i en ressourcegruppe i en deklarativ typografi. Organiseringen af oprettelse af ressourcer, herunder administration af afhængighed håndteres af ARM.
- Infrastrukturen kan oprettes i en gentaget måde på tværs af forskellige områder og i et område ved blot at ændre parametre.
- Typografien deklarativ fører til kortere overlappende tid i opbygning af skabelonerne og distribution af infrastrukturen.

Du kan finde eksempelskabeloner, [Azure Hurtig start skabeloner](https://github.com/Azure/azure-quickstart-templates).

Du kan finde flere oplysninger om ARM skabelonsprog, [Azure ressourcestyring skabelon sprog](../resource-group-authoring-templates.md).

Skabelonen eksempel bruges virtuelt netværk og undernet ressourcer. Der er andre netværksressourcer, du kan bruge som angivet nedenfor:

### <a name="using-a-template"></a>Ved hjælp af en skabelon

Du kan installere tjenester til Azure fra en skabelon, ved hjælp af PowerShell, AzureCLI, eller ved at udføre en Klik her for at installere fra GitHub. For at installere tjenester fra en skabelon i GitHub, skal du udføre følgende trin:

1. Åbn filen template3 fra GitHub. Åbn [virtuelle netværk med to undernet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)som et eksempel.
2. Klik på **Implementer til Azure**, og derefter logge på til Azure-portalen med dine legitimationsoplysninger.
3. Bekræfte skabelonen, og klik derefter på **Gem**.
4. Klik på **Rediger parametre** , og vælg en placering, som *Vest USA*, til vnet og undernet.
5. Hvis det er nødvendigt, ændre parametrene **ADDRESSPREFIX** og **SUBNETPREFIX** , og klik derefter på **OK**.
6. Klik på **Vælg en ressourcegruppe** , og klik derefter på den ressourcegruppe, du vil tilføje vnet og undernet til. Alternativt kan du oprette en ny ressourcegruppe ved at klikke på **eller oprette nye**.
3. Klik på **Opret**. Bemærk feltet viser **installation af klargøring af skabelon**. Når installationen er færdig, får du vist et skærmbillede, der ligner en nedenfor.

![Eksempel på skabelon installation](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Næste trin

[Azure ressourcestyring skabelon sprog](../resource-group-authoring-templates.md)

[Azure netværk – ofte anvendte skabeloner](https://github.com/Azure/azure-quickstart-templates)

[Beregne ressource udbyder](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)
