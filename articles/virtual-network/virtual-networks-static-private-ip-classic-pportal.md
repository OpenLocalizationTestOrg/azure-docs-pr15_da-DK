<properties 
   pageTitle="Sådan angives en statiske privat IP-Adresser i tilstanden Klassisk ved hjælp af portalen Azure | Microsoft Azure"
   description="Forstå statisk privat IP'er, og hvordan du administrerer dem i tilstanden Klassisk ved hjælp af portalen Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-the-azure-portal"></a>Sådan angives en statisk privat IP-adresse (klassisk) i portalen Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [administrere en statisk privat IP-adresse i implementeringsmodel ressourcestyring](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Følgende eksempel fremgangsmåde forventer et enkelt miljø, som allerede har oprettet. Hvis du vil køre trinnene, som de vises i dette dokument, skal først opbygge testmiljø, der er beskrevet i [oprette en vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sådan angives en statisk privat IP-adresse, når du opretter en VM
Følg nedenstående trin for at oprette en navngivet *DNS01* i *front end* -undernet af en VNet med navnet *TestVNet* med en statisk privat IP af *192.168.1.101*VM skal:

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. Klik på **Ny** > **beregne** > **Windows Server 2012 R2 Datacenter**, kan du se, på listen **Vælg en implementeringsmodel** allerede viser **Klassisk**, og klik derefter på **Opret**.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. Skriv navnet på VM skal have oprettet (*DNS01* i vores scenarie), i bladet **Oprette VM** den lokale administratorkonto og adgangskode.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Klik på **Valgfri konfiguration** > **netværk** > **Virtuelt netværk**, og klik derefter på **TestVNet**. Hvis **TestVNet** ikke er tilgængelig, skal du kontrollere, du bruger den *Centrale USA* placering og har oprettet den testmiljø, der er beskrevet i starten af denne artikel.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. I bladet **netværk** Sørg for, at det markerede undernet er *front end*, og derefter klikke på **IP-adresser**, under **tildeling af IP-adresser** skal du klikke på **statisk**og angiv derefter *192.168.1.101* for **IP-adresse** , som set under.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure04.png)   

6. Klik på **OK** i bladet **IP-adresser** og derefter klikke på **OK** i bladet **netværk** , og klik på **OK** i bladet **valgfrit config** .
7. Klik på **Opret**i bladet **Oprette VM** . Bemærk feltet nedenfor vises i dit dashboard.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hvordan du kan hente statiske privat IP-adresseoplysninger for en VM

Udfør nedenstående trin for at få vist statisk privat IP-adresseoplysningerne for det VM, der er oprettet i ovenstående trin.

1. Klik på **Gennemse alt**fra portalen Azure Azure > **virtuelle maskiner (klassisk)** > **DNS01** > **alle indstillinger** > **IP-adresser** og meddelelse om tildeling af IP-adresser og IP-adresse, som vist nedenfor.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Sådan fjernes en statisk privat IP-adresse fra en VM
Følg nedenstående trin for at fjerne den statiske private IP-adresse fra VM oprettet ovenfor.
    
1. Klik på **dynamisk** til højre for **tildeling af IP-adresser**, fra ovenstående bladet **IP-adresser** , og derefter klikke på **Gem**, og klik derefter på **Ja**.

    ![Oprette VM Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Sådan føjes en statisk privat IP-adresse til en eksisterende VM
Hvis du vil tilføje en statisk privat IP-adresse til VM oprettet ved hjælp af ovenstående trin, skal du følge nedenstående trin:

1. Klik på **statisk** til højre for **tildeling af IP-adresser**fra ovenstående bladet **IP-adresser** .
2. Skriv *192.168.1.101* for **IP-adresse**, og derefter klikke på **Gem**, og klik derefter på **Ja**.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [reserveret offentlige IP-](virtual-networks-reserved-public-ip.md) adresser.
- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) adresser.
- Se [reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx).