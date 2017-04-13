<properties 
   pageTitle="Administrere NSGs ved hjælp af portalen preview i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du administrerer exising NSGs ved hjælp af portalen preview i ressourcestyring."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Administrere NSGs ved hjælp af portalen preview

> [AZURE.SELECTOR]
- [Portal](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Hente oplysninger

Du kan se din eksisterende NSGs, hente regler til en eksisterende NSG og finde ud af, hvilke ressourcer en NSG er knyttet til den.

### <a name="view-existing-nsgs"></a>Få vist eksisterende NSGs
Følg nedenstående trin for at få vist alle eksisterende NSGs i et abonnement.

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Gennemse >** > **netværk sikkerhedsgrupper**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Se listen over NSGs i bladet **netværk sikkerhedsgrupper** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Følg nedenstående trin for at få vist listen over NSGs i gruppen **Indbyggede RG NSG** ressource. 

1. Klik på **ressourcegrupper >** > **Indbyggede RG NSG** > **...**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Søge efter elementer, der viser ikonet NSG, som vist i bladet **ressourcer** på listen over ressourcer.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Liste over alle regler for en NSG

Følg nedenstående trin for at få vist regler for en NSG med navnet **NSG FrontEnd**. 

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Klik på **indgående sikkerhedsregler**under fanen **Indstillinger** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. Bladet **indgående sikkerhedsregler** vises som vist nedenfor.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Klik på **sikkerhedsregler for udgående** for at få vist udgående regler under fanen **Indstillinger** .

>[AZURE.NOTE] Klik på ikonet **standardregler** øverst i bladet, der viser reglerne for at få vist standardregler.

### <a name="view-nsgs-associations"></a>Få vist NSGs tilknytninger

Følg nedenstående trin for at se, hvad ressourcer **NSG FrontEnd** NSG er tilknyttes.

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Klik på **undernet** for at få vist, hvilke undernet er knyttet til NSG i fanen **Indstillinger** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Klik på **netværksgrænseflader** for at få vist hvad netværkskort er knyttet til NSG i fanen **Indstillinger** .

## <a name="manage-rules"></a>Administrere regler

Du kan føje regler til en eksisterende NSG, redigere eksisterende regler og fjerne regler.

### <a name="add-a-rule"></a>Tilføje en regel

Følg nedenstående trin for at tilføje en regel for at tillade **indgående** trafik til port **443** fra en hvilken som helst computer til **NSG FrontEnd** NSG skal.

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Klik på **indgående sikkerhedsregler**under fanen **Indstillinger** .
3. Klik på **Tilføj**i bladet **indgående sikkerhedsregler for** . Udfylde værdierne, som vist nedenfor i bladet **Tilføj indgående sikkerhedsregel** , og klik derefter på **OK**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Bemærk den nye regel i bladet **indgående sikkerhedsregler** efter et par sekunder.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Ændre en regel

Følg nedenstående trin for at ændre den regel, der er oprettet ovenfor til at tillade indgående trafik fra **internettet** kun skal.

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Klik på den regel, der er oprettet ovenfor, under fanen **Indstillinger** .
3. Ændre egenskaben **postkilde** , som vist nedenfor i bladet **Tillad https** , og klik derefter på **Gem**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Slette en regel

Følg nedenstående trin for at slette den regel, der er oprettet ovenfor.

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Klik på den regel, der er oprettet ovenfor, under fanen **Indstillinger** .
3. Klik på **Slet**i bladet **Tillad https** , og klik derefter på **Ja**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Administrere tilknytninger

Du kan knytte en NSG til undernet og netværkskort. Du kan også fjerne forbindelsen mellem en NSG fra alle de ressourcer, der er knyttet til.

### <a name="associate-an-nsg-to-a-nic"></a>Knytte en NSG til et NIC

Følg nedenstående trin for at knytte **NSG FrontEnd** NSG til **TestNICWeb1** NIC.

1. Klik på **NSG FrontEnd**bladet **netværk sikkerhedsgrupper** , eller bladet **ressourcer** ovenstående.
2. Fanen **Indstillinger** , skal du klikke på **netværksgrænseflader** > **knytte** > **TestNICWeb1**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Fjerne forbindelsen mellem en NSG fra et NIC

Følg nedenstående trin for at fjerne tilknytningen **NSG FrontEnd** NSG fra **TestNICWeb1** NIC.

1. Fra Azure-portalen, skal du klikke på **ressourcegrupper >** > **Indbyggede RG NSG** > **...**  >  **TestNICWeb1**.
2. Klik på **Skift sikkerhed** i bladet **TestNICWeb1**  > **None**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Du kan også bruge denne blade knytte NIC til en eksisterende NSG.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Fjerne forbindelsen mellem en NSG fra et undernet

Følg nedenstående trin for at fjerne tilknytningen **NSG FrontEnd** NSG fra **front end** -undernet.

1. Fra Azure-portalen, skal du klikke på **ressourcegrupper >** > **Indbyggede RG NSG** > **...**  >  **TestVNet**.
2. Klik på **undernet**i bladet **Indstillinger**  > **front end** > **netværk sikkerhedsgruppe** > **ingen**.

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Klik på **Gem**i bladet **front end** .

![Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Knytte en NSG til et undernet

Hvis du vil knytte **NSG FrontEnd** NSG til **FronEnd** undernet igen, skal du følge nedenstående trin.

1. Fra Azure-portalen, skal du klikke på **ressourcegrupper >** > **Indbyggede RG NSG** > **...**  >  **TestVNet**.
2. Klik på **undernet**i bladet **Indstillinger**  > **front end** > **netværk sikkerhedsgruppe** > **NSG FrontEnd**.
3. Klik på **Gem**i bladet **front end** .

>[AZURE.NOTE] Du kan også knytte en NSG til et undernet fra thh NSG **Indstillinger** blade.

## <a name="delete-an-nsg"></a>Slette en NSG

Du kan kun slette en NSG, hvis det ikke er knyttet til en ressource. Følg nedenstående trin for at slette en NSG.

1. Fra Azure-portalen, skal du klikke på **ressourcegrupper >** > **Indbyggede RG NSG** > **...**  >  **NSG FrontEnd**.
2. Klik på **netværksgrænseflader**i bladet **Indstillinger** .
3. Hvis der er en hvilken som helst netværkskort, der er angivet, skal du klikke på NIC, og Følg trin 2 i [Dissociate en NSG fra et NIC](#Dissociate-an-NSG-from-a-NIC).
4. Gentag trin 3 for hver NIC.
5. Klik på **undernet**i bladet **Indstillinger** .
6. Hvis der ikke er en hvilken som helst undernet, der er angivet, skal du klikke på undernettet og følge trin 2 og 3 i [Dissociate en NSG fra et undernet](#Dissociate-an-NSG-from-a-subnet).
7. Rul tilbage til bladet **NSG FrontEnd** klik derefter på **Slet** > **Ja**.

[Azure portal - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Næste trin

- [Aktivere logføring af](virtual-network-nsg-manage-log.md) NSGs.
