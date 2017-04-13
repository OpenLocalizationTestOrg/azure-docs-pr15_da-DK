<properties
   pageTitle="ExpressRoute kunde router konfiguration eksempler | Microsoft Azure"
   description="Denne side indeholder router konfiguration eksempler til Cisco og Juniper routere."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>Router konfiguration prøver at konfigurere og administrere NAT

Denne side indeholder NAT konfiguration eksempler til Cisco ASA og Juniper SRX serie routere. Disse er beregnet til at være eksempler for at få vejledning kun og må ikke bruges, som den er. Du kan arbejde med din leverandør af at komme frem til rette konfigurationer til dit netværk. 

>[AZURE.IMPORTANT] Eksempler på denne side er beregnet til at være rent for at få vejledning. Du skal arbejde med din leverandør salg / tekniske team og teamet netværk for at komme frem til relevante konfigurationer til dine behov. Microsoft understøtter ikke problemer i forbindelse med konfigurationer, der er angivet på denne side. Du skal kontakte din leverandør af enhed for support til problemer.

Router konfiguration eksempler nedenfor vedrører Azure offentlige og Microsoft peerings. Du skal ikke konfigurere NAT til Azure private peering. Gennemse [ExpressRoute peerings](expressroute-circuit-peerings.md) og [ExpressRoute NAT krav](expressroute-nat.md) kan finde flere oplysninger.

**Note:** Du skal bruge separat NAT IP-grupper for forbindelse til internettet og ExpressRoute. Bruger den samme NAT IP-pulje på tværs af internettet og ExpressRoute resulterer i asymmetrisk routing og tab af forbindelse.

## <a name="cisco-asa-firewalls"></a>Cisco ASA firewalls

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAULA konfiguration for trafik fra kundenetværket til Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PAULA konfiguration for trafik fra Microsoft til kundenetværk

#### <a name="interfaces-and-direction"></a>Grænseflader og retning:
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>Konfiguration af:
NAT puljen:

    object network outbound-PAT
        host <NAT-IP>

Target Server:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objekt gruppe, som kunden IP-adresser

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT kommandoer:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX serie routere 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Opret overflødige Ethernet-grænseflader for-klyngen

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Opret to sikkerhedszoner

 - Hav tillid til Zone for interne netværk og Untrust Zone for eksterne netværk modstående kant routere
 - Tildele relevante grænseflader til zonerne
 - Tillad services om grænseflader


    sikkerhed {zoner {sikkerhed zone tillid {host indgående-trafik {system-tjenester {ping;                  } protokoller {bgp;                  grænseflader}} {reth0.100;              }} sikkerhed zone Untrust {host indgående-trafik {system-tjenester {ping;                  } protokoller {bgp;                  grænseflader}} {reth1.100;              }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3. Opret sikkerhedspolitikker mellem Zoner
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Konfigurer NAT politikker
 - Opret to NAT grupper. En bruges til at NAT-trafik udgående til Microsoft og andre fra Microsoft til kunden.
 - Oprette regler til NAT respektive trafikken

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurer BGP for at annoncere selektiv præfikser i hver retning

Se eksemplerne i eksempelsiden [Routing konfiguration](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. oprette politikker

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Næste trin

Se [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md) få mere at vide.
