<properties 
   pageTitle="Netværk grænseflader | Microsoft Azure"
   description="Få mere at vide om Azure netværksgrænseflader i Azure ressourcestyring."
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

# <a name="network-interfaces"></a>Netværksgrænseflader

Netværkskort (NIC) er sammenhængen mellem en Virtual Machine (VM) og det underliggende software netværk. I denne artikel forklares det, hvad et netværksgrænsefladen er, og hvordan de bruges i implementeringsmodel Azure ressourcestyring.

Microsoft anbefaler at implementere nye ressourcer ved hjælp af Ressourcestyring implementeringsmodel, men du kan også distribuere FOS med netværksforbindelsen i [Klassisk](virtual-network-ip-addresses-overview-classic.md) implementeringsmodel. Hvis du kender til klassisk modellen, er der vigtige forskelle i VM netværk i implementeringsmodel ressourcestyring. Lær mere om forskellene ved at læse artiklen [virtuelt netværk - klassisk](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

I Azure, et netværk interface:

1. Er en ressource, der kan oprettes, slettes, og har sine egne konfigurerbar indstillinger.
2. Skal have forbindelse til ét undernet i ét Azure virtuelle netværk (VNet), når den er oprettet. Hvis du ikke kender VNets, skal du vide mere om dem ved at læse artiklen [Oversigt over virtuelle netværk](virtual-networks-overview.md) . NIC skal være forbundet med en VNet, som findes i den samme Azure [placering](https://azure.microsoft.com/regions) og [abonnement](../azure-glossary-cloud-terminology.md#subscription) som på NIC. Når en NIC er oprettet, kan du ændre det undernet, den er tilsluttet, men du kan ikke ændre VNet den er tilsluttet.
3. Har et navn, der er tildelt til den, der ikke kan ændres, når NIC er blevet oprettet. Navnet skal være entydigt i en Azure [ressourcegruppe](../azure-resource-manager/resource-group-overview.md#resource-groups), men skal ikke være entydige i abonnementet, Azure placeringen den oprettes i eller VNet den er tilsluttet. Flere netværkskort oprettes typisk inden for et Azure-abonnement. Det anbefales, at du udtænke en navngivningsregel, der gør administration af flere netværkskort nemmere end ved hjælp af standardnavne. Se artiklen [anbefalede navngivningskonventioner for Azure ressourcer](../guidance/guidance-naming-conventions.md) for forslag.
4. Kan knyttes til en VM, men kan kun knyttes til en enkelt VM, som findes i den samme placering, som NIC.
5. Har en MAC-adresse, som er bevaret med NIC til, så længe den er knyttet til en VM. MAC-adressen er bevaret om VM genstartes (fra i operativsystemet) eller ikke længere (deaktivere tildelte) og ved hjælp af portalen Azure, Azure PowerShell eller kommandolinjen Azure i gang. Hvis det er fjernet fra en VM og knyttet til en anden VM, modtager NIC, en anden MAC-adresse. Hvis NIC slettes, tildeles MAC-adressen til andre netværkskort.
6. Have skal en primær **private** *IPv4* fast eller dynamisk IP-adresse tildelt den.
8. Måske har en offentlige IP-adresseressource er knyttet til den.
9. Understøtter accelereret netværk med enkelt roden i/o-virtualization (SR-IOV) til bestemte VM størrelser, der kører bestemte versioner af Microsoft Windows Server-operativsystemet. Hvis du vil vide mere om denne PREVIEW-funktion skal du læse artiklen [accelereret netværk for en virtuel maskine](virtual-network-accelerated-networking-powershell.md) .
10. Kan modtage trafik, der ikke er stilet til private IP-adresser, der er tildelt til den, hvis IP-videresendelse er aktiveret for NIC. Hvis en VM kører firewall-software for eksempel, dirigerer pakker, der ikke er stilet til sin egen IP-adresser. VM skal stadig køre software, der kan rute eller videresende trafik, men for at gøre det, skal være aktiveret for en NIC. IP-videresendelse
11. Ofte oprettes i samme ressourcegruppe som den er tilsluttet VM eller den samme VNet, som den er tilsluttet, selvom den ikke skal være.

## <a name="vms-with-multiple-network-interfaces"></a>FOS med flere netværkgrænseflader

Flere netværkskort kan knyttes til en VM, men når at gøre det, skal du være opmærksom på følgende:  

- VM størrelse skal understøtte flere netværkskort. Læs artiklerne om [Windows Server VM størrelser](../virtual-machines/virtual-machines-windows-sizes.md) eller [Linux VM størrelser](../virtual-machines/virtual-machines-linux-sizes.md) for at få mere for at vide om, hvilke VM størrelser understøtter flere netværkskort.   
- VM skal oprettes med mindst to netværkskort. Hvis VM er oprettet med kun én NIC, selvom VM størrelsen understøtter mere end én, kan du ikke vedhæfte flere netværkskort til VM, når den er oprettet. Så længe VM er blevet oprettet med mindst to netværkskort, kan du vedhæfte flere netværkskort til VM efter den er oprettet, så længe VM størrelsen understøtter mere end to netværkskort.  
- Du kan frigøre sekundær netværkskort (NIC primære ikke frakoblet) fra en VM, hvis VM har mindst tre netværkskort, der er knyttet til den. Du kan ikke fjerne netværkskort, hvis VM har to eller mindre netværkskort knyttet til den.  
- Rækkefølgen af netværkskort fra i VM tilfældigt og kan også ændre på tværs af Azure infrastruktur opdateringer. Dog IP-adresser, og det tilsvarende ethernet MAC-adresser, forbliver de samme. Antag for eksempel operativsystemet identificerer Azure NIC1 som Eth1. Eth1 har IP-adresse 10.1.0.100 og MAC-adressen 00-0D-3A-B0-39-0D. Når en Azure infrastruktur opdaterer og genstarte, operativsystemet kan nu identificere Azure NIC1 som Eth2, men IP- og MAC-adresserne vil være den samme som de var, da operativsystemet identificeret Azure NIC1 som Eth1. Når en genstart er kunden-definerede, forbliver NIC rækkefølgen den samme i operativsystemet.  
- Hvis VM er medlem af en [tilgængelighed angivet](../azure-glossary-cloud-terminology.md#availability-set), skal alle FOS i sættet tilgængelighed have enten en enkelt NIC eller flere NIC. Hvis FOS har flere netværkskort, det nummer de havde ikke er påkrævet for at være den samme, som hver VM har mindst to netværkskort.

## <a name="next-steps"></a>Næste trin

- Få mere at vide, hvordan du opretter en VM med et enkelt NIC ved at læse artiklen [Opret en VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Få mere at vide, hvordan du opretter en VM med flere netværkskort ved at læse artiklen [Implementer en VM med flere NIC](virtual-network-deploy-multinic-arm-ps.md) .
- Få mere at vide, hvordan du opretter en NIC med flere IP-konfigurationer ved at læse [flere IP-adresser til Azure virtuelle maskiner](virtual-network-multiple-ip-addresses-powershell.md) artiklen.
