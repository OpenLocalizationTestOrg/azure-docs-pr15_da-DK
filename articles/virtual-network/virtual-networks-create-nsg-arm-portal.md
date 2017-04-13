<properties 
   pageTitle="Sådan oprettes NSGs i ARM tilstand ved hjælp af portalen Azure | Microsoft Azure"
   description="Lær, hvordan du opretter og installerer NSGs i ARM ved hjælp af portalen Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Sådan administreres NSGs ved hjælp af portalen Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [oprette NSGs i modellen Klassisk installation](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Eksempel PowerShell kommandoer nedenfor forventer et enkelt miljø, som allerede har oprettet på basis af ovenstående scenarie. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først oprette testmiljøet ved at installere [denne skabelon](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), klik på **Implementer til Azure**, erstatte Standardparameterværdier, hvis det er nødvendigt og følge instruktionerne i portalen. Nedenstående trin Brug **De indbyggede RG NSG** som navnet på skabelonen, der er blevet installeret ressourcegruppen.

## <a name="create-the-nsg-frontend-nsg"></a>Oprette NSG FrontEnd NSG

Følg nedenstående trin for at oprette **NSG FrontEnd** NSG, som vist i ovenstående scenarie.

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Gennemse >** > **netværk sikkerhedsgrupper**.

    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. Klik på **Tilføj**i bladet **netværk sikkerhedsgrupper** .
  
    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Oprette en navngivet *NSG FrontEnd* i den *Indbyggede RG NSG* ressourcegruppe NSG i bladet **Opret netværk sikkerhedsgruppe** , og klik derefter på **Opret**.

    ![Azure portal - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Oprette regler i en eksisterende NSG

Følg nedenstående trin for at oprette regler i en eksisterende NSG fra Azure-portalen.

2. Klik på **Gennemse >** > **netværk sikkerhedsgrupper**.

3. Klik på **NSG FrontEnd**på listen over NSGs > **indgående sikkerhedsregler**

    ![Azure portal - NSG FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Klik på **Tilføj**på listen over **indgående sikkerhedsregler**.

    ![Azure portal - Tilføj regel](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Oprette en regel med navnet *web-regel* med prioriteten af *200* tillade adgang via *TCP* til port *80* til en hvilken som helst VM fra alle kilder i bladet **Tilføj indgående sikkerhedsregel** , og klik derefter på **OK**. Bemærk, at de fleste af disse indstillinger er standardværdier allerede.

    ![Azure portal - regelindstillinger](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Du får vist den nye regel i NSG efter et par sekunder.

    ![Azure portal - ny regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Gentag trin til 6 for at oprette en indgående regel med navnet *rdp-regel* med en prioritet på *250* tillade adgang via *TCP* til port *3389* til en hvilken som helst VM fra alle kilder.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Knytte NSG til front end-undernet

1. Klik på **Gennemse >** > **ressourcegrupper** > **Indbyggede RG NSG**.
2. Klik på **…** i bladet **Indbyggede RG NSG**  >  **TestVNet**.

    ![Azure portal - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. Klik på **undernet**i bladet **Indstillinger**  > **front end** > **netværk sikkerhedsgruppe** > **NSG FrontEnd**.

    ![Azure portal - undernet indstillinger](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Klik på **Gem**i bladet **front end** .

    ![Azure portal - undernet indstillinger](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Oprette back end-NSG NSG

Følg nedenstående trin for at oprette **Back-end NSG** NSG og knytte den til **back-end** -undernettet.

1. Gentage trinnene i [oprette NSG FrontEnd NSG](#Create-the-NSG-FrontEnd-NSG) til at oprette en NSG med navnet *NSG-backend-version*
2. Gentage trinnene i [oprette regler i en eksisterende NSG](#Create-rules-in-an-existing-NSG) til at oprette de **indgående** regler i tabellen nedenfor.

  	|Indgående regel|Udgående regel|
  	|---|---|
  	|![Azure portal - indgående regel](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Azure portal - udgående regel](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Gentage trinnene i [knytte NSG til front end-undernet](#Associate-the-NSG-to-the-FrontEnd-subnet) knytte **Back-end NSG** NSG til **back-end** -undernet.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [administrerer eksisterende NSGs](virtual-network-manage-nsg-arm-portal.md)
- [Aktivere logføring af](virtual-network-nsg-manage-log.md) NSGs.