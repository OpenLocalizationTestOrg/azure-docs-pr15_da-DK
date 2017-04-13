<properties 
   pageTitle="Foretage fejlfinding af netværk sikkerhedsgrupper - PowerShell | Microsoft Azure"
   description="Lær, hvordan du udfører fejlfinding af netværk sikkerhedsgrupper i Azure ressourcestyring installation modellen med Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
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
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Foretage fejlfinding af netværk sikkerhedsgrupper ved hjælp af Azure PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Hvis du har konfigureret netværk sikkerhedsgrupper (NSGs) på din virtuelle maskine (VM) og oplever problemer med serverforbindelsen VM, indeholder denne artikel en oversigt over diagnosticering egenskaber for NSGs til hjælp til fejlfinding af yderligere.

NSGs gør det muligt at kontrollere typerne trafik pågældende flow ind og ud af din virtuelle maskiner (VM'er). NSGs kan anvendes på undernet i et virtuelt Azure-netværk (VNet), netværksgrænseflader (NIC) eller begge dele. De effektive regler, der er anvendt på en NIC er en sammenlægning af de regler, der findes i de NSGs, der er anvendt på en NIC og det undernet, den er tilsluttet. Regler på tværs af disse NSGs kan nogle gange er i konflikt med hinanden og påvirke en VM netværksforbindelse.  

Du kan få vist alle de effektive sikkerhedsregler fra din NSGs, som anvendes på din VM netværkskort. I denne artikel viser, hvordan du udfører fejlfinding af VM forbindelsesproblemer ved hjælp af disse regler i implementeringsmodel Azure ressourcestyring. Hvis du ikke kender VNet og NSG begreber, Læs artiklerne om [virtuelle netværks](virtual-networks-overview.md) - og [netværk sikkerhedsgrupper](virtual-networks-nsg.md) oversigt.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Bruge effektive sikkerhedsregler til at foretage fejlfinding af VM trafikken

Dette scenario, der følger efter er et eksempel på et almindeligt forbindelsesproblem:

En VM med navnet *VM1* er en del af et undernet med navnet *Subnet1* i en VNet med navnet *WestUS VNet1*. Et forsøg på at oprette forbindelse til VM ved hjælp af RDP over TCP-port 3389 mislykkes. NSGs anvendes på både NIC *VM1 NIC1* og undernet *Subnet1*. Trafik til TCP-port 3389 er tilladt i den NSG, der er knyttet til netværksgrænsefladen *VM1 NIC1*, men TCP ping til VM1's port 3389 opstår.

Mens i dette eksempel bruges TCP-port 3389, kan følgende trin bruges til at bestemme mislykkede forsøg på indgående og udgående forbindelse via en hvilken som helst port.

## <a name="detailed-troubleshooting-steps"></a>Detaljerede fejlfindingstrin
Benytte følgende fremgangsmåde for at foretage fejlfinding af NSGs for en VM:

1. Starte en session med Azure PowerShell og logge på Azure. Hvis du ikke er vant til at bruge Azure PowerShell, kan du læse [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) -artiklen.

2. Skriv følgende kommando for at returnere alle NSG regler, der er anvendt på en NIC med navnet *VM1 NIC1* i ressourcegruppen *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Hvis du ikke kender navnet på et NIC, kan du skrive følgende kommando for at hente navnene på alle netværkskort i en ressourcegruppe: 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    Følgende tekst er et eksempel på effektive regler output returneres til *VM1 NIC1* NIC:

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Bemærk følgende oplysninger i output:

    - Der er to **NetworkSecurityGroup** sektioner: en er knyttet til et undernet (*Subnet1*) og en er knyttet til en NIC (*VM1 NIC1*). I dette eksempel, der er anvendt en NSG til hver enkelt.
    - **Tilknytning** viser ressourcen (undernet eller NIC) er tilknyttet en given NSG. Hvis NSG ressourcen er flyttet/tilknytning fjernet umiddelbart før du kører denne kommando, du muligvis vente et par sekunder, før ændringen til at afspejle i kommando-output. 
    - Regel navnene, der er med *defaultSecurityRules*foranstillet:, når en NSG der oprettes flere standard sikkerhedsregler er oprettet i modellen. Standardregler ikke kan fjernes, men de kan tilsidesættes med højere prioriteringsregler.
     Læs [NSG oversigt](virtual-networks-nsg.md#default-rules) -artikel for at få flere oplysninger om NSG standard sikkerhedsregler.
    - **ExpandedAddressPrefix** udvider adressepræfikser for NSG standardmærker. Mærker repræsenterer flere adressepræfikser. Udvidelse af koderne kan være praktiske, når fejlfinding i forbindelse med VM connectivity til/fra specifikke adressepræfikser. For eksempel med VNET peering udvides VIRTUAL_NETWORK mærke til at vise peered VNet præfikser i det forrige resultat.

        >[AZURE.NOTE] Kommandoen kun viser effektive reglerne Hvis en NSG er knyttet til et undernet, en NIC, eller begge dele. En VM muligvis flere netværkskort med forskellige NSGs, der er anvendt. Når fejlfinding, skal du køre kommandoen for hver NIC.
        
3. Skriv følgende kommandoer til yderligere fejlfinding for at lette filtrering over større antal NSG regler: 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Et filter til RDP trafik (TCP-port 3389), er anvendt til gittervisningen, som vist i billedet nedenfor:

    ![Liste til dokumentregler](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Som du kan se i gittervisningen, der er begge Tillad og Afvis regler for RDP. Output fra trin 2 viser, at reglen *DenyRDP* er i NSG anvendt til undernettet. For indgående regler skal behandles NSGs anvendt til undernettet først. Hvis der findes en værdi, behandles ikke NSG anvendt på netværksgrænsefladen. I dette tilfælde blokerer *DenyRDP* reglen fra undernettet RDP til VM (**VM1**).

    >[AZURE.NOTE] En VM muligvis flere netværkskort, der er knyttet til den. Hver kan have forbindelse til et andet undernet. Da kommandoer i de forrige trin, som køres mod en NIC, er det vigtigt at sikre, at du angiver den NIC, du har connectivity manglende. Hvis du ikke er sikker på, kan du altid køre kommandoerne mod hver NIC, der er knyttet til VM.

5. Ændre reglen *Nægte RDP (3389)* til at *Tillade RDP(3389)* i **Subnet1 NSG** NSG til RDP til VM1. Bekræft, at TCP-port 3389 er åbent ved at åbne en RDP-forbindelse til VM eller ved hjælp af værktøjet PsPing. Du kan få mere at vide om PsPing ved at læse den [PsPing overførselssiden](https://technet.microsoft.com/sysinternals/psping.aspx)

    Du kan eller fjerne regler fra en NSG ved hjælp af oplysningerne i output fra følgende kommando:

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende punkter, når fejlfinding af problemer:

- Standard NSG regler blokerer indgående adgang fra internettet og kun tillade VNet indgående trafik. Regler skal tilføjes eksplicit for at tillade indgående adgang fra internettet, efter behov.
- Hvis der er ingen NSG sikkerhedsregler, der forårsager en VM netværksforbindelsen mislykkes, være problemet skyldes:
    - Firewallsoftware, der kører i den VM operativsystem
    - Omdirigerer konfigureret til virtuelle anvendelser eller lokalt trafik. Internettrafik kan blive omdirigeret til en lokal installation via tvunget-tunnelføring. En RDP/SSH forbindelse fra internettet til dine VM fungerer muligvis ikke med denne indstilling, afhængigt af hvordan netværkshardwaren lokale håndterer denne trafik. Læs [Fejlfinding omdirigerer](virtual-network-routes-troubleshoot-powershell.md) artikel for at lære at diagnosticere distribuere problemer, der kan være at forhindre strømmen af trafik og VM. 
- Hvis du har peered VNets, som standard, udvide mærket VIRTUAL_NETWORK automatisk for at medtage præfikser for peered VNets. Du kan få vist disse præfikser på listen **ExpandedAddressPrefix** at foretage fejlfinding af problemer med at VNet peering connectivity. 
- Effektive sikkerhedsregler vises kun, hvis der er en NSG, der er knyttet til VM NIC og eller undernet. 
- Hvis der er ingen NSGs, der er knyttet til NIC eller undernet, og du har en offentlig IP-adresse, der er tildelt til din VM, vil alle porte være åben for indgående og udgående adgang. Hvis VM har en offentlig IP-adresse, anbefales anvende NSGs på NIC eller undernet.  
