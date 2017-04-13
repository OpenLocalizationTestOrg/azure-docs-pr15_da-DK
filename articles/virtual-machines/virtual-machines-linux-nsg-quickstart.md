<properties
   pageTitle="Åbne porte og slutpunkter til en Linux VM | Microsoft Azure"
   description="Lær, hvordan du åbner en port / oprette et slutpunkt til din Linux VM ved hjælp af Azure ressource manager implementeringsmodel og Azure CLI"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Åbne porte og slutpunkter til en Linux VM i Azure
Du åbner en port eller oprette et slutpunkt til en virtuel maskine (VM) i Azure ved at oprette et netværk filter på en undernet eller VM netværk interface. Du placerer disse filtre, som styrer både indgående og udgående trafik, på et netværk sikkerhedsgruppe, der er knyttet til den ressource, der modtager trafikken. Lad os bruge et almindeligt eksempel på webtrafik på port 80.

## <a name="quick-commands"></a>Hurtig kommandoer
Hvis du vil oprette en sikkerhedsgruppe netværk og regler, du har brug for [Azure CLI](../xplat-cli-install.md) installeret og bruger ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `myNetworkSecurityGroup`, og `myVnet`.

Oprette dit netværk sikkerhedsgruppe, angive dine egne navne og placering korrekt. I følgende eksempel oprettes en netværk sikkerhedsgruppe med navnet `myNetworkSecurityGroup` i den `WestUS` placering:

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Tilføje en regel for at tillade HTTP-trafik til dit webserver (eller Juster til dit eget scenarie, som SSH access eller database connectivity). I følgende eksempel oprettes en regel med navnet `myNetworkSecurityGroupRule` tillade TCP-trafik på port 80:

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Knytte sikkerhedsgruppen netværk til din VM netværkskort (NIC). I følgende eksempel knyttes en eksisterende NIC med navnet `myNic` med sikkerhedsgruppen netværk med navnet `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Du kan også knytte sikkerhedsgruppe dit netværk, med et virtuelt netværksundernet i stedet for blot at netværksgrænsefladen på en enkelt VM. I følgende eksempel knyttes en eksisterende undernet med navnet `mySubnet` i den `myVnet` virtuelt netværk med sikkerhedsgruppen netværk med navnet `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Flere oplysninger om netværk sikkerhedsgrupper
Hurtig kommandoerne her kan du kommer i gang med trafik der flyder til din VM. Netværk sikkerhedsgrupper giver mange fantastiske funktioner og granularitet for at styre adgang til dine ressourcer. Du kan læse mere om [oprettelse af en sikkerhedsgruppe netværk og ACL regler her](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Du kan definere netværk sikkerhedsgrupper og ACL regler som en del af Azure ressourcestyring skabeloner. Læs mere om [oprettelse af netværk sikkerhedsgrupper med skabeloner](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Hvis du skal bruge port-videresendelse for at tilknytte en entydig eksterne port til en intern port på din VM, bruge regler for oversættelse (NAT) og belastningsjustering. Du vil muligvis få vist TCP-port 8080 eksternt og har trafik til TCP port 80 på en VM. Du kan få mere at vide om at [oprette en forbindelse til internettet justering af belastning](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Næste trin
Du har oprettet en simpel regel for at tillade HTTP-trafik i dette eksempel. Du kan finde oplysninger om at oprette mere detaljerede miljøer i følgende artikler:

- [Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)
- [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Azure ressourcestyring oversigt til Indlæs balancere](../load-balancer2    /load-balancer-arm.md)