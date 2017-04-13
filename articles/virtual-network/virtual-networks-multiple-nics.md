<properties
   pageTitle="Oprette en VM med flere netværkskort"
   description="Lær at oprette og konfigurere FOS med flere netværkskort"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Oprette en VM med flere netværkskort

Du kan oprette virtuelle maskiner (VM'er) i Azure og vedhæfte flere netværkgrænseflader (NIC) til hver af dine FOS. Multi NIC er et krav for mange netværk virtuelle udstyr, som programmet levering og WAN optimeringsløsninger. Multi NIC indeholder også flere netværk trafik management funktioner, herunder isolationsniveauet af trafik mellem en forside afslutte NIC og igen end NIC(s) eller adskillelse af data plan trafik fra management plan trafik.

![Multi NIC for VM](./media/virtual-networks-multiple-nics/IC757773.png)

Skærmbilledet ovenfor viser en VM med tre netværkskort hver forbindelse til et andet undernet.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- Internettet VIP (klassisk installationer) understøttes kun på "standard" NIC. Der er kun én VIP til IP-adresse af standard NIC.
- Forekomst niveau offentlige IP-adresse (LPIP) adresser (klassisk installationer) understøttes ikke på nuværende tidspunkt for flere NIC FOS.
- Rækkefølgen af netværkskort fra i VM tilfældigt og kan også ændre på tværs af Azure infrastruktur opdateringer. Dog IP-adresser, og det tilsvarende ethernet MAC-adresser er forbliver de samme. Antag f.eks., **Eth1** har IP-adresse 10.1.0.100 og MAC-adressen 00-0D-3A-B0-39-0D; Når du har en Azure infrastruktur Opdater og genstarte, den kan ændres til **Eth2**, men IP- og MAC parring forbliver de samme. Når en genstart er kunden-definerede, forbliver NIC rækkefølgen på samme måde.
- Adressen for hver NIC på hver VM skal være placeret i et undernet, flere netværkskort på en enkelt VM kan hver tildeles adresser, der findes i det samme undernet.
- VM størrelsen bestemmer antallet af Netværkskort, som du kan oprette for en VM. Reference på [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) og [Linux](../virtual-machines/virtual-machines-linux-sizes.md) VM størrelser artikler for at finde ud af, hvor mange Netværkskort understøtter hver VM størrelse. 

## <a name="network-security-groups-nsgs"></a>Netværk sikkerhedsgrupper (NSGs)
I en ressourcestyring installation en hvilken som helst NIC på en VM muligvis tilknyttede med et netværk sikkerhed gruppe (NSG), herunder en hvilken som helst netværkskort på en VM, der har flere netværkskort aktiveret. Hvis en NIC er tildelt en adresse i et undernet, hvor undernettet er knyttet til en NSG, klik derefter på regler i det undernet NSG gælder også for pågældende NIC. Ud over at tilknytte undernet med NSGs, kan du også knytte et NIC med en NSG.

Hvis et undernet er knyttet til en NSG, og en NIC i pågældende undernet er individuelt knyttet til en NSG, anvendes de tilknyttede NSG regler i **flow rækkefølge** efter retningen af den trafik, der overføres ind eller ud af NIC:

- **Indgående trafik** Hvis destinationen er den pågældende NIC flyder først gennem undernet, udløser det undernet NSG regler, før du går til NIC, og klik derefter udløser NIC NSG regler.
- **Udgående trafik** Hvis kilden er den pågældende NIC flyder først ud fra NIC, udløser NIC NSG regler, før der passerer gennem undernettet, og klik derefter udløser det undernet NSG regler.

Få mere at vide om [Netværk sikkerhedsgrupper](virtual-networks-nsg.md) , og hvordan de anvendes baseret på tilknytninger til undernet, FOS og netværkskort..

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Sådan konfigureres en flere NIC VM i en klassisk installation

Vejledningen nedenfor kan du oprette flere NIC VM med 3 netværkskort: standard NIC og to ekstra netværkskort. Konfigurationstrinnene opretter en VM, der skal konfigureres ifølge nedenfor service konfiguration filfragment:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Du skal bruge følgende forudsætninger før du forsøger at køre PowerShell-kommandoerne i eksemplet.

- Et Azure-abonnement.
- Et konfigureret virtuelt netværk. Se [Oversigt over virtuelle netværk](virtual-networks-overview.md) for at få flere oplysninger om VNets.
- Den seneste version af Azure PowerShell har downloadet og installeret. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

Hvis du vil oprette en VM med flere netværkskort, skal du følge nedenstående trin:

1. Vælge et billede af VM fra Azure VM Billedgalleri. Bemærk, at billeder, ændres regelmæssigt og er tilgængelige efter område. Det billede, der er angivet i eksemplet nedenfor kan ændre eller muligvis ikke i dit område, så sørg for at angive det billede, du har brug for.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Opret en VM konfiguration.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Oprette standard administrator login.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Føje yderligere netværkskort til VM konfigurationen.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Angiv undernet og IP-adresse for standard NIC.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Oprette VM i netværket virtuel.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] Den VNet, du angiver her, skal allerede findes (som nævnt i forudsætningerne). Eksemplet herunder angiver et virtuelt netværk med navnet **MultiNIC VNet**.

## <a name="limitations"></a>Begrænsninger

Følgende begrænsninger gælder, når du bruger flere NIC-funktion:

- Flere NIC FOS skal oprettes i Azure virtuelle netværk (VNets). Ikke-VNet FOS ikke kan konfigureres med flere netværkskort.
- Alle FOS i et sæt tilgængelighed for at bruge enten multi NIC eller enkelt NIC. Der må ikke være en blanding af flere NIC FOS og enkelt NIC FOS inden for en tilgængelighed sæt. Samme regler gælder for FOS i en skybaseret tjeneste.
- En VM med enkelt NIC ikke kan konfigureres med flere netværkskort (og omvendt) når den er installeret, uden at slette og opretter den igen.


## <a name="secondary-nics-access-to-other-subnets"></a>Sekundær netværkskort adgang til andre undernet

Som standard konfigureret sekundære netværkskort ikke med en standardgateway, på grund af som trafikken på de sekundære netværkskort begrænses for at være i samme undernet. Hvis brugerne vil aktivere sekundær netværkskort at tale uden for deres egne undernet, skal de til at tilføje en post i tabellen routing til at konfigurere gatewayen, som beskrevet nedenfor.

>[AZURE.NOTE] FOS blev oprettet, inden juli 2015 muligvis en standardgateway, der er konfigureret til alle netværkskort. Standardgatewayen for sekundær netværkskort fjernes ikke, når disse FOS er blevet genstartet. Forbindelse til internettet kan bryde i operativsystemer, der bruger svage host routing modellen, som Linux, hvis indgangs- og udgangspunkt trafik bruge forskellige netværkskort.

### <a name="configure-windows-vms"></a>Konfigurere Windows FOS

Antag, at du har en Windows-VM med to netværkskort på følgende måde:

- Primære NIC IP-adresse: 192.168.1.4
- Sekundær NIC IP-adresse: 192.168.2.5

Tabellen IPv4 rute for denne VM ville se således ud:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Bemærk, at standardruten (0.0.0.0) er kun tilgængelig for den primære NIC. Du kan ikke få adgang til ressourcer uden for undernettet for den sekundære NIC, som vist nedenfor:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Følg nedenstående trin for at tilføje en standardrute på den sekundære NIC skal:

1. Kør kommandoen nedenfor til at identificere nummeret for den sekundære NIC fra en kommandoprompt:

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Bemærk den anden post i tabellen med et indeks over 27 (i dette eksempel).
3. Kør kommandoen **route add** fra kommandoprompten, som vist nedenfor. I dette eksempel angiver du 192.168.2.1 som standard-gatewayen til den sekundære NIC:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Gå tilbage til kommandoprompten for at teste forbindelsen, og prøv at pinge et andet undernet fra den sekundære NIC som vist int eksemplet nedenfor:

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Du kan også kontrollere ruten tabellen for at kontrollere den tilføjede rute, som vist nedenfor:

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Konfigurere Linux FOS

For Linux FOS, da standardfunktionsmåden bruger svage host routing, anbefaler vi, at de sekundære netværkskort er begrænset til trafik flyder kun inden for det samme undernet. Men hvis connectivity uden for undernettet har behov for visse scenarier, brugere skal aktivere politik baseret routing for at sikre, at indgangs- og udgangspunkt trafik bruger den samme NIC.

## <a name="next-steps"></a>Næste trin

- Installer [MultiNIC FOS i et scenarie med 2-niveau programmet i en ressourcestyring installation](virtual-network-deploy-multinic-arm-template.md).
- Installer [MultiNIC FOS i et scenarie med 2-niveau programmet i en klassisk installation](virtual-network-deploy-multinic-classic-ps.md).
