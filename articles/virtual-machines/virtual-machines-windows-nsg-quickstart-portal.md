<properties
   pageTitle="Åbn porte til en VM ved hjælp af portalen Azure | Microsoft Azure"
   description="Lær, hvordan du åbner en port / oprette et slutpunkt til din Windows-VM ved hjælp af ressource manager implementeringsmodel i portalen Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Åbning af porte til en VM i Azure ved hjælp af portalen Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Hurtig kommandoer
Du kan også [udføre disse trin, ved hjælp af Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

Opret først sikkerhedsgruppe dit netværk. Vælg en ressourcegruppe i portalen, klik på **Tilføj**, og derefter søge efter og vælge 'Netværk sikkerhedsgruppe':

![Tilføje en sikkerhedsgruppe netværk](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Angiv et navn til dit netværk sikkerhedsgruppe, Vælg eller Opret en ressourcegruppe, og vælg en placering. Klik på **Opret** , når du er færdig:

![Oprette en sikkerhedsgruppe netværk](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Vælg den nye netværk sikkerhed gruppe. Vælg 'indgående sikkerhedsregler', og derefter klikke på knappen **Tilføj** for at oprette en regel:

![Tilføje en indgående regel](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Angiv et navn til den nye regel. Port 80 er allerede indsat som standard. Denne blade er, hvor du vil ændre den kilde, protocol og destination når du føjer flere regler til dit netværk sikkerhedsgruppe. Klik på **OK** for at oprette reglen:

![Oprette en indgående regel](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

Det sidste trin er at knytte sikkerhedsgruppe dit netværk til et undernet eller en bestemt netværk-grænseflade. Lad os knytte sikkerhedsgruppen netværk til et undernet. Vælg 'Undernet' og derefter klikke på **knytte**:

![Knytte en sikkerhedsgruppe netværk til et undernet](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Vælg dit virtuelle netværk, og vælg derefter det rigtige undernet:

![Knytte en netværk sikkerhedsgruppe til virtuelt netværk](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Du har nu oprettet en sikkerhedsgruppe netværk, oprettes en indgående regel, der tillader trafik på port 80 og tilknyttet et undernet. En hvilken som helst FOS, du opretter forbindelse til pågældende undernet er tilgængelig på port 80.


## <a name="more-information-on-network-security-groups"></a>Flere oplysninger om netværk sikkerhedsgrupper
Hurtig kommandoerne her kan du kommer i gang med trafik der flyder til din VM. Netværk sikkerhedsgrupper giver mange fantastiske funktioner og granularitet for at styre adgang til dine ressourcer. Du kan læse mere om [oprettelse af en sikkerhedsgruppe netværk og ACL regler her](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Du kan definere netværk sikkerhedsgrupper og ACL regler som en del af Azure ressourcestyring skabeloner. Læs mere om [oprettelse af netværk sikkerhedsgrupper med skabeloner](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Hvis du skal bruge port-videresendelse for at tilknytte en entydig eksterne port til en intern port på din VM, bruge regler for oversættelse (NAT) og belastningsjustering. Du vil muligvis få vist TCP-port 8080 eksternt og har trafik til TCP port 80 på en VM. Du kan få mere at vide om at [oprette en forbindelse til internettet justering af belastning](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Næste trin
Du har oprettet en simpel regel for at tillade HTTP-trafik i dette eksempel. Du kan finde oplysninger om at oprette mere detaljerede miljøer i følgende artikler:

- [Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md)
- [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Azure ressourcestyring oversigt til Indlæs balancere](../load-balancer/load-balancer-arm.md)
