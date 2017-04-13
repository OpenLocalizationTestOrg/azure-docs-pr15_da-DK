<properties 
   pageTitle="Installere en VM med en statiske offentlige IP-Adresser ved hjælp af portalen Azure i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du installerer FOS med en statiske offentlige IP-Adresser ved hjælp af portalen zure i ressourcestyring."
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
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Installere en VM med en statiske offentlige IP-Adresser ved hjælp af portalen Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Oprette en VM med en statiske offentlige IP-Adresser 

Følg nedenstående trin for at oprette en VM med en statisk offentlige IP-adresse i portalen Azure.

1. I en browser, gå til [Azure-portalen](https://portal.azure.com) og, hvis det er nødvendigt, logge på med din Azure-konto.
2. Klik på **Ny**på det øverste venstre hjørne af portalen skal>>**beregne**>**Windows Server 2012 R2 Datacenter**.
3. Vælg **Ressourcestyring** på listen **Vælg en implementeringsmodel** , og klik på **Opret**.
4. Angiv VM oplysninger, som vist nedenfor i bladet **grundlæggende** , og klik derefter på **OK**.

    ![Azure portal - grundlæggende](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Klik på **A1 Standard** , som vist nedenfor i bladet **Vælg en størrelse** , og klik derefter på **Vælg**.

    ![Azure portal - Vælg en størrelse](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Klik på **offentlige IP-adresse**i bladet **Indstillinger** og derefter i bladet **Opret offentlige IP-adresse** under **tildeling**, skal du klikke på **statisk** som vist nedenfor. Og klik derefter på **OK**.

    ![Azure portal - oprette offentlige IP-adresse](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Klik på **OK**i bladet **Indstillinger** .
8. Gennemse bladet **Oversigt** , som vist nedenfor, og klik derefter på **OK**.

    ![Azure portal - oprette offentlige IP-adresse](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Læg mærke til det nye felt i dit dashboard.

    ![Azure portal - oprette offentlige IP-adresse](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Når VM er oprettet, vises bladet **Indstillinger** som vist nedenfor

    ![Azure portal - oprette offentlige IP-adresse](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)