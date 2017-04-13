<properties 
   pageTitle="Accelereret netværk for en virtuel maskine - PowerShell | Microsoft Azure"
   description="Lær, hvordan du konfigurerer accelereret netværk til en Azure virtuelt ved hjælp af PowerShell."
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
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Accelereret netværk for en virtuel maskine

> [AZURE.SELECTOR]
- [Azure-portalen](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Accelereret netværk gør det muligt for enkelt roden i/o-Virtualization (SR-IOV) til en virtuel maskine (VM) gør det meget forbedring af ydeevnen netværk. Denne høj ydeevne sti tilsidesætte værten fra datapath reducere ventetid, formindskelse og CPU-forbrug til brug sammen med tunge netværk arbejdsbelastningen på understøttede VM datatyper. I denne artikel forklares, hvordan du bruger Azure PowerShell til at konfigurere accelereret netværk i implementeringsmodel Azure ressourcestyring. Du kan også oprette en VM med accelereret netværk ved hjælp af portalen Azure. Til at få mere at vide hvordan, klik på feltet Azure Portal øverst i denne artikel.

Følgende billede viser kommunikation mellem to virtuelle maskiner (VM) med og uden accelereret netværk:

![Sammenligning](./media/virtual-network-accelerated-networking-powershell/image1.png)

Alle netværk trafik og VM skal gennemgang af værten og parameteren virtuelle uden accelereret netværk. Virtuel Skift indeholder alle politikhåndhævelse, som netværk sikkerhedsgrupper, adgangskontrollister, isolationsniveauet og andre virtualiseret netværkstjenester til netværkstrafik. Hvis du vil vide mere, du læse artiklen [Hyper-V netværk Virtualization og Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Med accelereret netværk, netværkstrafik ankommer til netværkskort (NIC) og derefter videresendes til VM. Alle netværkspolitikker, der gælder parameteren virtuelle uden accelereret netværk er lagt over og anvendt i hardware. Anvende politik i hardware, der gør det muligt for NIC for at videresende netværkstrafik direkte til VM, springe værten og parameteren virtuelle, samtidig med at alle politikken anvendes den i værten.

Fordelene ved accelereret netværk gælder kun for den VM, som det er aktiveret på. De bedste resultater er den perfekt at aktivere denne funktion på mindst to FOS forbindelse til den samme VNet.  Når du kommunikerer på tværs af VNets eller forbindende lokalt, har denne funktion en minimale betydning for ventetid på overordnet.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Fordele

- **Kortere ventetid / højere pakker hver anden (pps):** Fjerne parameteren virtuelle fra datapath fjerner den tid, pakker bruger i at være vært for behandling af politik og øge antallet af pakker, der kan behandles i VM.
- **Reduceret formindskelse:** Virtuel Skift behandling afhænger af mængden politik, der skal anvendes og arbejdsbelastningen af CPU'EN, der gør behandlingen. Overføre politik håndhævelsen til hardware fjerner denne variation ved at levere pakker direkte til VM, fjerne værten VM kommunikation og alle software afbrydelser og kontekst parametre.
- **Mindre CPU-forbrug:** Springe parameteren virtuelle på værten fører til mindre CPU-forbrug til at behandle netværkstrafik.

## <a name="limitations"></a>Begrænsninger

Der findes følgende begrænsninger, når du bruger denne egenskab:
 
- **Netværk oprettelse af brugergrænsefladen:** Accelereret netværk kan kun aktiveres for en ny netværksgrænseflade.  Det kan ikke aktiveres på en eksisterende netværksgrænseflade.
- **VM oprettelse:** Grænsefladen et netværk med accelereret netværk aktiveret kan kun knyttes til en VM, når VM oprettes. Grænsefladen for det netværk, kan ikke knyttes til en eksisterende VM.
- **Områder:** Tilbydes i Vest Central os og Vest Europe Azure områder kun. Sættet af områder udvides i fremtiden.
- **Understøttede operativsystem:** Microsoft Windows Server 2012 R2 og Windows Server 2016 Technical Preview 5. Understøttelse af Linux og Windows Server 2012, føjes snart.
- **VM størrelse:** Standard_D15_v2 og Standard_DS15_v2 er den eneste understøttede VM forekomst størrelser. Få mere at vide artiklen [Windows VM størrelser](../virtual-machines/virtual-machines-windows-sizes.md) . Sæt af understøttede VM forekomst størrelser udvides i fremtiden.

Ændringer af disse begrænsninger bliver offentliggjort via siden [Azure Virtual Networking opdateringer](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Oprette en Windows VM med accelereret netværk

1. Åbn en kommandoprompt PowerShell og udføre de resterende trin i dette afsnit i en enkelt PowerShell-session. Hvis du ikke allerede har PowerShell installeret og konfigureret, kan du udføre trinnene i artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) .
2. For at registrere for eksempel skal du sende en mail til [Accelereret netværk abonnementer](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) med dit abonnement-ID og beregnet brug. Ikke fuldført de resterende trin indtil, når du modtager en mail med besked om, at du har antaget i eksemplet.
3. Registrere muligheden med dit abonnement ved at angive følgende kommandoer:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Erstat *westcentralus* med navnet på en anden placering, der understøttes af denne egenskab, der er angivet i afsnittet [begrænsninger](#limitations) i denne artikel (hvis det er nødvendigt). Skriv følgende kommando for at angive en variabel til placeringen:

        $locName = "westcentralus"

5. Erstat *RG1* med et navn til ressourcegruppen, der indeholder den nye brugergrænseflade for netværk og skrive følgende kommandoer til at oprette den:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Ændre *VM1 NIC1* til hvad du vil navngive grænsefladen for det netværk, og angiv derefter følgende kommando:

        $NICName = "VM1-NIC1"

7. Netværksgrænsefladen skal have forbindelse til et undernet inden for et eksisterende virtuelle Azure-netværk (VNet) i den samme placering og [abonnement](../azure-glossary-cloud-terminology.md#subscription) som netværksgrænsefladen. Lær mere om VNets ved at læse artiklen [Oversigt over virtuelle netværk](virtual-networks-overview.md) , hvis du ikke kender dem. For at oprette en VNet skal du udføre trinnene i artiklen [oprette et VNet](virtual-networks-create-vnet-arm-ps.md) . Ændre "værdierne" i den følgende $Variables til navnet på VNet og undernet, du vil forbinde netværksgrænsefladen til.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Hvis du ikke kender navnet på en eksisterende VNet på den placering, du valgte i trin 3, kan du angive følgende kommandoer:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Hvis listen returneres er tom, skal du oprette en VNet på placeringen. For at oprette en VNet skal du udføre trinnene i artiklen [oprette et virtuelt netværk](virtual-networks-create-vnet-arm-ps.md) .

    Skriv følgende kommandoer for at få navnet på undernet i VNet, og Erstat *Subnet1* over med navnet på et undernet:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Skriv følgende kommandoer for at hente de VNet og undernet og tildele dem til variabler.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identificere en eksisterende offentlige IP-adresseressource, som kan knyttes til grænsefladen for det netværk, så du kan oprette forbindelse til den via internettet. Hvis du ikke vil have adgang til VM med netværksgrænsefladen via internettet, kan du springe dette trin. Uden et offentlige IP-adresse, skal du oprette forbindelse til VM fra en anden VM forbindelse til den samme VNet. 

    Ændre *PIP1* til navnet på en eksisterende offentlige IP-adresseressource, som findes i den placering, du opretter netværksgrænsefladen i og, der ikke er knyttet til en anden netværksgrænseflade i øjeblikket. Hvis det er nødvendigt, kan du ændre $rgName til navnet på den ressourcegruppe den offentlige IP-adresseressource findes i, og Skriv følgende kommando:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Angiv følgende kommandoer, hvis du ikke kender navnet på en eksisterende offentlige IP-adresseressource:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Hvis kolonnen **IPConfiguration** har nogen værdi i den returnerede output, den offentlige IP-adresseressource er ikke knyttet til en eksisterende netværksgrænseflade og kan bruges. Hvis listen er tom, eller der er ingen tilgængelig offentlige IP-adresse ressourcer, kan du oprette én ved hjælp af kommandoen ny AzureRmPublicIPAddress.

    >[AZURE.NOTE] Offentlige IP-adresser har et nominel gebyr. Lær mere om priser ved at læse siden [priser IP-adresse](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Hvis du vælger ikke at føje en offentlige IP-adresseressource til brugergrænsefladen, fjerne *- PublicIPAddress $PIP1* i slutningen af den kommando, der følger efter. Oprette netværksgrænsefladen med accelereret netværk ved at skrive følgende kommando:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Tildele netværksgrænsefladen til en VM, når du opretter VM ved at følge instruktionerne i trin 3 og 6 i artiklen [oprette et VM](../virtual-machines/virtual-machines-windows-ps-create.md) . Erstat *Standard_A1* i trin 6-2 med en af de VM størrelser, der er angivet i afsnittet [begrænsninger](#limitations) i denne artikel.

    >[AZURE.NOTE] Hvis du har ændret *navnet* på den $locName, $rgName eller $nic variabler i denne artikel, mislykkes trin 6 i oprette en artikel til VM. Du kan dog ændre *værdierne* i variablerne.

12. Når VM er oprettet, kan du hente [accelereret netværk driver](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), oprette forbindelse til VM og køre driver installer i VM.

13. Højreklik på Windows-knappen, og klik på **Enhedshåndtering**. Kontrollere, at **Mellanox ConnectX-3 virtuelle funktionen Ethernet-netværkskort** vises under indstillingen **netværk** , når det er udvidet, som vist i billedet nedenfor:

    ![Enhedshåndtering](./media/virtual-network-accelerated-networking-powershell/image2.png)