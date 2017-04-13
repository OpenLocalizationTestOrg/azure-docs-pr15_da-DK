
<properties
   pageTitle="Azure vejledning | mønstre og fremgangsmåder | Microsoft Azure"
   description="Azure Reference arkitekturer"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="christb"/>

# <a name="azure-reference-architectures"></a>Azure Reference arkitekturer

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

_Dette indhold er i active udvikling. Det er praktisk så vi gør den tilgængelig for udskrift i dag. Vi vil meget gerne din feedback._

Vores reference arkitekturer er arrangeret efter scenarie med flere relaterede arkitekturer, der er grupperet.
Hver enkelt arkitektur indeholder anbefalede fremgangsmåder og præskriptive trin samt en eksekverbare komponent, der indeholder anbefalingerne.
Mange af arkitekturer er gradvist; opbygning af oven på foregående arkitekturer, der har færre krav.

## <a name="designing-your-infrastructure-for-resiliency"></a>Designe infrastrukturen af tolerance

Denne serie begynder med anbefalede fremgangsmåder til optimal VM konfiguration og culminates i et område med flere installation med failover.

- [Kører et Windows VM på Azure](guidance-compute-single-vm.md)
- [Kører en Linux VM på Azure](guidance-compute-single-vm-linux.md)
- [Kører flere FOS for skalerbarhed og tilgængelighed](guidance-compute-multi-vm.md)
- [Køre Windows FOS til en N-delt-arkitektur](guidance-compute-n-tier-vm.md)
- [Køre Linux FOS til en N-delt-arkitektur](guidance-compute-n-tier-vm-linux.md)
- [Køre Windows FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters.md)
- [Køre Linux FOS i flere områder til høj tilgængelighed](guidance-compute-multiple-datacenters-linux.md)

## <a name="connecting-your-on-premises-network-to-azure"></a>Oprette forbindelse dit lokale netværk til Azure

Denne serie starter ved, de måder at oprette forbindelse til din eksisterende netværk til Azure. Derefter udvides den til at omfatte krav til tilgængelighed og sikkerhed.

- [Implementere en hybrid netværksarkitektur med Azure og lokale VPN](guidance-hybrid-network-vpn.md)
- [Implementere en hybrid netværksarkitektur med Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
- [Implementere en meget tilgængelige hybrid netværksarkitektur](guidance-hybrid-network-expressroute-vpn-failover.md)

## <a name="securing-your-hybrid-network"></a>Sikring af netværket hybrid

Denne serie dækker de metoder til oprettelse af DMZ i Azure til sikre forbindelser, der kommer fra dit lokale datacenter, og på internettet.

- [Implementere en DMZ mellem Azure og dit lokale datacenter](guidance-iaas-ra-secure-vnet-hybrid.md)
- [Implementere en DMZ mellem Azure og internettet](guidance-iaas-ra-secure-vnet-dmz.md)

## <a name="providing-identity-services"></a>Give identitet tjenester

Denne serie starter ved, hvordan du bruger Azure Active Directory til at levere brugergodkendelse i Azure. Derefter udvides den til at omfatte komplekse scenarier, hvis du vil udvide infrastrukturen ADDERER til Azure og bruge ADFS til delegering.

- [Implementering af Azure Active Directory](./guidance-identity-aad.md)
- [Hvis du vil udvide katalogtjenester Active Directory (tilføjer) til Azure](./guidance-identity-adds-extend-domain.md)
- [Oprette en Active Directory Directory Services (tilføjer) ressourceskov i Azure](./guidance-identity-adds-resource-forest.md)
- [Implementering af Active Directory Federation Services (ADFS) i Azure](./guidance-identity-adfs.md)

## <a name="architecting-scalable-web-application-using-azure-paas"></a>Planlagde arkitektur SVG webprogrammet ved hjælp af Azure PaaS

Denne serie dækker anbefalinger til bygning af SVG og fleksibelt webapps. 

- [Grundlæggende webprogram](guidance-web-apps-basic.md)
- [Forbedring af skalerbarhed i et webprogram](guidance-web-apps-scalability.md)
- [Webprogram med høj tilgængelighed](guidance-web-apps-multi-region.md)
