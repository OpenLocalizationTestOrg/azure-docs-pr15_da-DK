<properties 
   pageTitle="Sådan angives en statiske privat IP-Adresser i ARM tilstand ved hjælp af portalen Azure | Microsoft Azure"
   description="Forstå privat IP'er (fald), og hvordan du administrerer dem i ARM tilstand ved hjælp af portalen Azure"
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

# <a name="how-to-set-a-static-private-ip-address-in-the-azure-portal"></a>Sådan angives en statisk privat IP-adresse i portalen Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [administrere statisk privat IP-adresse i modellen, klassisk installation](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Følgende eksempel fremgangsmåde forventer et enkelt miljø, som allerede har oprettet. Hvis du vil køre trinnene, som de vises i dette dokument, skal først opbygge testmiljø, der er beskrevet i [oprette en vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Sådan oprettes en VM til test statisk private IP-adresser

Du kan ikke angive en statisk privat IP-adresse under oprettelse af en VM i tilstanden ressourcestyring installation ved hjælp af portalen Azure. Skal du først oprette VM, skal du derefter angive dens privat IP-adresse til at være statisk.

Følg nedenstående trin for at oprette en navngivet *DNS01* i *front end* -undernet af en VNet med navnet *TestVNet*VM.

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Ny** > **beregne** > **Windows Server 2012 R2 Datacenter**, kan du se, på listen **Vælg en implementeringsmodel** allerede viser **Ressourcestyring**, og klik derefter på **Opret**, som det fremgår figuren nedenfor.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. Skriv navnet på VM skal have oprettet (*DNS01* i vores scenarie), i bladet **grundlæggende** den lokale administratorkonto og adgangskode, som det fremgår figuren nedenfor.

    ![Grundlæggende om blade](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Sørg for, at den **placering** , der er markeret er *Centrale USA*, og derefter klikke på **Vælg eksisterende** under **ressourcegruppe**, og klik derefter på **ressourcegruppe** igen, og derefter skal du klikke på *TestRG*, og klik derefter på **OK**.

    ![Grundlæggende om blade](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. Vælg **A1 Standard**i bladet **Vælg en størrelse** , og klik derefter på **Vælg**.

    ![Vælg en størrelse blade](./media/virtual-networks-static-ip-arm-pportal/figure04.png) 

6. Kontroller, at følgende egenskaber er angivet i bladet **Indstillinger** er konfigureret med værdierne nedenfor, og klik derefter på **OK**.

    -**Lagerplads konto**: *vnetstorage*
    - **Netværk**: *TestVNet*
    - **Undernet**: *front end*

    ![Vælg en størrelse blade](./media/virtual-networks-static-ip-arm-pportal/figure05.png)  

7. Klik på **OK**i bladet **Oversigt** . Bemærk feltet nedenfor vises i dit dashboard.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hvordan du kan hente statiske privat IP-adresseoplysninger for en VM

Udfør nedenstående trin for at få vist statisk privat IP-adresseoplysningerne for det VM, der er oprettet i ovenstående trin.

1. Klik på **Gennemse alt**fra portalen Azure Azure > **virtuelle maskiner** > **DNS01** > **alle indstillinger** > **netværksgrænseflader** og klik derefter på netværksgrænsefladen kun vises.

    ![Implementere VM felt](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. Klik på **alle indstillinger**i bladet **netværkskort**  > **IP-adresser** og Bemærk værdierne **tildelings** - og **IP-adresse** .

    ![Implementere VM felt](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Sådan føjes en statisk privat IP-adresse til en eksisterende VM
Hvis du vil tilføje en statisk privat IP-adresse til VM oprettet ved hjælp af ovenstående trin, skal du følge nedenstående trin:

1. Klik på **statisk** under **tildeling**fra ovenstående bladet **IP-adresser** .
2. Skriv *192.168.1.101* for **IP-adresse**, og klik derefter på **Gem**.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Hvis du efter at klikke på **Gem** du bemærke, at opgaven stadig er indstillet til **dynamiske**, betyder det, at den angivne IP-adresse er allerede i brug. Kan du prøve en anden IP-adresse.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Sådan fjernes en statisk privat IP-adresse fra en VM
Følg nedenstående trin for at fjerne den statiske private IP-adresse fra VM oprettet ovenfor.
    
1. Klik på **dynamisk** under **tildeling**ovenstående bladet **IP-adresser** , og klik derefter på **Gem**.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [reserveret offentlige IP-](virtual-networks-reserved-public-ip.md) adresser.
- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) adresser.
- Se [reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx).