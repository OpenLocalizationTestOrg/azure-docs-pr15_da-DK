<properties
   pageTitle="Flere IP-adresser for virtuelle maskiner - PowerShell | Microsoft Azure"
   description="Lær at tildele flere IP-adresser til en virtuel maskine, ved hjælp af Azure PowerShell."
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
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Tildele flere IP-adresser til virtuelle maskiner

Azure Virtual Machine (VM) kan have en eller flere netværksgrænseflader (NIC) til. En hvilken som helst NIC kan have en eller flere offentlig eller privat IP-adresser tildelt. Hvis du ikke kender IP-adresser i Azure, skal du læse artiklen [IP-adresser i Azure](virtual-network-ip-addresses-overview-arm.md) mere at vide om dem. I denne artikel forklares, hvordan du bruger Azure PowerShell til at tildele flere IP-adresser til en VM i implementeringsmodel Azure ressourcestyring.

Tildele flere IP-adresser til en VM gør det muligt for følgende funktioner:

- Vært for flere websteder eller tjenester med forskellige IP-adresser og SSL-certifikater på en enkelt server.
- Fungere som et netværk virtuelle produktet som en firewall eller justering af belastning.
- Muligheden for at føje nogen af private IP-adresser på grund af netværkskortene til en Azure justering af belastning back end-puljen. Kun den primære IP-adresse for den primære NIC kunne tidligere føjes til en back end-puljen.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

For at registrere for eksempel skal du sende en mail til [Flere IP'er](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) med dit abonnement-ID og beregnet brug.

## <a name="scenario"></a>Scenarie

I denne artikel kan du knytte tre IP-konfigurationer til en netværksgrænseflade.
Følgende eksempel konfigurationer bliver oprettet og tildelt et NIC, der har tre private IP-adresser og én offentlige IP-adresse tildelt:

- IPConfig-1: Et dynamisk privat IP-adresse (standard) og en offentlige IP-fra den offentlige IP-adresseressource med navnet PIP1.
- IPConfig-2: En statisk privat IP-adresse og ingen offentlige IP-adresse.
- IPConfig-3: En dynamisk privat IP-adresse og ingen offentlige IP-adresse.

    ![Af alternativ billedtekst](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Dette scenarie antager, at du har en ressourcegruppe med navnet *RG1* , hvor der er en VNet kaldet *VNet1* og et undernet, kaldet *Subnet1*. Det antages videre, du har en VM kaldet *VM1*, et netværk brugergrænseflade kaldet *VM1 NIC1* , der er knyttet til den og en offentlig IP-adresse kaldet *PIP1*.

[I denne artikel](./virtual-machines/virtual-machines-windows-ps-create.md ) indeholder en gennemgang hvordan du opretter de ressourcer, der er nævnt ovenfor, i tilfælde af, at du ikke har oprettet dem før.

## <a name = "create"></a>Oprette en VM med flere IP-adresser

1. Åbn en kommandoprompt PowerShell og udføre de resterende trin i dette afsnit i en enkelt PowerShell-session. Hvis du ikke allerede har PowerShell installeret og konfigureret, kan du udføre trinnene i artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) .

2. Ændre "værdierne" i den følgende $Variables til Azure [placering](https://azure.microsoft.com/regions) netværket virtuel er placeret, navnet på den [ressourcegruppe](../azure-resource-manager/resource-group-overview.md#resource-groups), VNet i ressourcegruppen, det undernet, du vil forbinde NIC til og navnet på NIC. Fuldfør trinnene for at tilføje flere IP-adresser til en hvilken som helst NIC, der er knyttet til en VM, som du har brug for.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Hvis du ikke kender navnet på en eksisterende Azure placering eller ressourcegruppe, Skriv følgende kommandoer:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>NIC skal have forbindelse til et undernet inden for et eksisterende virtuelle Azure-netværk (VNet). De tre komponenter: NIC, undernet og VNet, skal alle findes i den samme område og [abonnement](../azure-glossary-cloud-terminology.md#subscription).  Hvis du ikke kender VNets, skal du læse artiklen [Oversigt over virtuelle netværk](virtual-networks-overview.md) for at få mere at vide om dem eller læse artiklen [oprette et VNet](virtual-networks-create-vnet-arm-ps.md) for at lære at oprette en.

    Hvis du ikke kender navnet på en eksisterende VNet, Skriv følgende kommando og erstatte *VNet1* i den forrige variabel med navnet på en VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Hvis listen returneres er tom, skal du oprette en VNet. For at lære, hvordan du læse artiklen [oprette et virtuelt netværk](virtual-networks-create-vnet-arm-ps.md) .

    Skriv følgende kommandoer finder du navnet på de eksisterende undernet i VNet og erstatte *Subnet1* over med navnet på et undernet:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Skriv følgende kommando for at hente undernettet og tildele den til en variabel.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definer de IP-konfigurationer, du vil tildele til NIC. Hver konfiguration kan have en fast eller dynamisk privat IP-adresse og en tilhørende offentlige IP-adresseressource med en fast eller dynamisk adresse.

    Tilføje eller fjerne et vilkårligt antal de konfigurationer, som følger afhængigt af hvor mange IP-adresser, du vil knytte til NIC og indstillinger, du vil konfigurere.

    **IPConfig-1**

    Ændrer værdien *PIP1* til navnet på en eksisterende offentlige IP-adresseressource, som findes i den placering, du opretter på NIC i og, der ikke er i øjeblikket er knyttet til en anden NIC. Ændre *RG1* til navnet på ressourcegruppen den offentlige IP-adresseressource findes i. Ændre *IPConfig-1* til det navn, du vil give til den første IP-konfiguration. Skriv følgende kommandoer:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Bemærk de *-primære* skifte. Når du tildeler en NIC flere IP-konfigurationer, skal have tildelt én konfiguration som *primært*. Hvis du ikke kender navnet på en eksisterende offentlige IP-adresseressource, Skriv følgende kommando: Get-AzureRMPublicIPAddress | Formatér-tabelnavn, placering, IP-adresse, IpConfiguration

    Hvis kolonnen **IPConfiguration** har nogen værdi i outputtet returneres, er den offentlige IP-adresseressource er ikke knyttet til en eksisterende NIC og kan bruges. Hvis listen er tom, eller der er ingen tilgængelig offentlige IP-adresse ressourcer, kan du oprette én ved hjælp af kommandoen **Ny AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Offentlige IP-adresser har et nominel gebyr. Hvis du vil vide mere om IP-adresse priser, Læs siden [IP-adresse priser](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Ændre *IPConfig-2* til det navn, du vil give til den anden IP-konfiguration og ændre *10.0.0.5* til en ubrugt gyldig IP-adresse for det undernet, du tildeler NIC til. Skriv følgende kommandoer:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Skriv følgende kommando, hvis du ikke kender IP-adresse adresse område, der er tildelt til undernettet:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Ændre *IPConfig-3* til det navn, du vil give til den tredje IP-konfiguration, og Skriv følgende kommandoer:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Du kan tildele op til 250 privat IP-adresse til en NIC. Er der en grænse for antallet af offentlige IP-adresser, der kan bruges i et abonnement. Hvis du vil vide mere, kan du læse artiklen [Azure begrænser](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Oprette NIC ved hjælp af de IP-konfigurationer, der er defineret i det forrige trin.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Vedhæft NIC, når du opretter en VM ved at følge trinnene i artiklen [oprette et VM](../virtual-machines/virtual-machines-windows-ps-create.md) . Selvom artiklen opretter en VM, der kører Windows Server, er det samme for en Linux VM, end at vælge et andet operativsystem trinnene. Gentag trin 1-3 i denne artikel. Gå videre til trin 4 og 5 og derefter udfører trin 6 i oprette en artikel til VM.

    >[AZURE.WARNING] Trin 6 i oprette en artikel til VM mislykkes, hvis du har ændret variablen med navnet $nic til noget andet i trin 6 i denne artikel, eller ikke har fuldført de forrige trin i denne artikel.

8. Få vist privat IP-adresser, Azure DHCP, der er tildelt til NIC og den offentlige IP-adresseressource, der er tildelt NIC ved at skrive følgende kommando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Tilføje alle de private IP-adresser (herunder primært) i TCP/IP-konfigurationen i operativsystemet manuelt. 

**Windows**

1. Fra en kommandoprompt, Skriv *ipconfig/all*.  Du kan kun se de *primære* privat IP-adresse (gennem DHCP).
2. Skriv *ncpa.cpl* næste i kommandopromptvinduet. Derved åbnes et nyt vindue.
3. Åbn egenskaberne for **LAN-forbindelse**.
4. Dobbeltklik på Internet Protocol version 4 (IPv4)
5. Vælg **Brug følgende IP-adresse** , og Angiv følgende værdier:
    - **IP-adresse**: angive den *primære* private IP-adresse
    - **Undernetmaske**: sæt baseret på dit undernet. For eksempel, hvis undernettet er en /24 undernet derefter undernetmasken er 255.255.255.0.
    - **Standard gateway**: den første IP-adresse i undernettet. Hvis dit undernet er 10.0.0.0/24, er gateway IP-adressen 10.0.0.1.
    - Klik på **Brug følgende DNS-serveradresser** , og Angiv følgende værdier:
        - **Foretrukken DNS-server:** Angiv 168.63.129.16, hvis du ikke bruger dine egne DNS-server.  Hvis du er, kan du angive IP-adressen for din DNS-server.
    - Klik på knappen **Avanceret** , og Tilføj yderligere IP-adresser. Tilføje alle de sekundære privat IP-adresser er angivet i trin 8 til NIC med det samme undernet, der er angivet for den primære IP-adresse.
    - Klik på **OK** for at lukke ud af TCP/IP-indstillinger og derefter på **OK** igen for at lukke indstillingerne for kortet. Dette vil derefter genoprette forbindelsen RDP.

6. Fra en kommandoprompt, Skriv *ipconfig/all*. Vises alle IP-adresser, du har tilføjet, og DHCP er slået fra.


**Linux (Ubuntu)**

1. Åbne et terminal vindue.
2. Kontrollér, at du bruger rod. Hvis du ikke er det, kan du gøre dette ved hjælp af følgende kommando:

            sudo -i
3. Opdatering af konfigurationsfil på netværksgrænsefladen (Hvis 'eth0').
    - Bevar den eksisterende post til dhcp. Dette vil konfigurere den primære IP-adresse, som det plejer at være tidligere.
    - Tilføje en konfiguration for en ekstra statiske IP-adresse med følgende kommandoer:

                cd /etc/network/interfaces.d/
                ls

        Du bør se filen all .cfg.
4. Åbn filen: vi *filnavn*.

    Du burde se følgende linjer i slutningen af filen:

            auto eth0
            iface eth0 inet dhcp
5. Tilføj følgende linjer efter de linjer, der findes i denne fil:

            iface eth0 inet static
            address <your private IP address here>
6. Gem filen ved hjælp af følgende kommando:

            :wq
7.  Nulstil netværksgrænsefladen med følgende kommando:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Køre både ifdown og ifup i den samme linje, hvis ved hjælp af en ekstern forbindelse.
8. Kontrollere IP-adressen er føjet til netværksgrænsefladen med følgende kommando:

            ip addr list eth0

    Du bør se den IP-adresse, du har tilføjet som en del af listen.

**Linux (Redhat, CentOS og andre)**

1. Åbne et terminal vindue.
2. Kontrollér, at du bruger rod. Hvis du ikke er det, kan du gøre dette ved hjælp af følgende kommando:

            sudo -i
3. Angiv din adgangskode, og følg vejledningen, som du bliver bedt om. Når du bruger den rod, skal du gå til mappen netværk scripts med følgende kommando:

            cd /etc/sysconfig/network-scripts
4. Liste over relaterede ifcfg filerne med følgende kommando:

            ls ifcfg-*

    Du bør se *ifcfg eth0* som en af filerne.
5. Kopiér *ifcfg eth0* -filen, og kald den *ifcfg-eth0:0* med følgende kommando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Redigere den *ifcfg-eth0:0* filen med følgende kommando:

            vi ifcfg-eth1
7. Ændre enheden til det relevante navn i filen. *eth0:0* i dette tilfælde med følgende kommando:

            DEVICE=eth0:0
8. Ændre den *IPADDR = YourPrivateIPAddress* linje for at afspejle IP-adressen.
9. Gem filen med følgende kommando:

            :wq
10. Genstarte tjenesterne, netværk, og Sørg for, at ændringerne, der er gået igennem ved at køre følgende kommandoer:

            /etc/init.d/network restart
            Ipconfig

    Du bør se den IP-adresse, du har tilføjet, *eth0:0*, på listen, returneres.

## <a name="add"></a>Tilføje IP-adresser til en eksisterende VM

Benytte følgende fremgangsmåde for at tilføje yderligere IP-adresser til en eksisterende NIC:

1. Åbn en kommandoprompt PowerShell og udføre de resterende trin i dette afsnit i en enkelt PowerShell-session. Hvis du ikke allerede har PowerShell installeret og konfigureret, kan du udføre trinnene i artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) .

2. Ændre "værdierne" i den følgende $Variables til navnet på den NIC, du vil tilføje IP-adresser til og ressourcegruppe og placering på NIC findes i:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Hvis du ikke kender navnet på den NIC, du vil ændre, skal du angive følgende kommandoer, skal du ændre værdierne i de forrige variabler:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Oprette en variabel og angive den til den eksisterende NIC ved at skrive følgende kommando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Hente undernet-ID NIC er forbundet til ved at udføre [trin 3](#subnet) i Opret en VM med flere IP-adresser sektion i denne artikel.

5. Opret de IP-konfigurationer, du vil føje til netværket ved at følge instruktionerne i [trin 5](#ipconfigs) i afkrydsningsfeltet Opret en VM med flere IP-adresser afsnit i denne artikel.

6. Ændre *$IPConfigName4* til navnet på den IP-konfiguration, du har oprettet i ovenstående trin. Skriv følgende kommando for at tilføje konfigurationen skal:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Hvis du vil angive NIC med IP-konfigurationen, Skriv følgende kommando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Tilføj de IP-adresser, du har føjet til NIC til operativsystemet VM ved at følge instruktionerne i [trin 9](#os) i oprette en VM med flere IP-adresser afsnit i denne artikel.
