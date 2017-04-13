<properties
   pageTitle="ExpressRoute kunde router konfiguration eksempler | Microsoft Azure"
   description="Denne side indeholder router config eksempler til Cisco og Juniper routere."
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

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Router konfiguration prøver at konfigurere og administrere routing

Denne side indeholder brugergrænseflade og routing konfiguration eksempler til Cisco IOS-XE og Juniper MX-serie routere. Disse er beregnet til at være eksempler for at få vejledning kun og må ikke bruges, som den er. Du kan arbejde med din leverandør af at komme frem til rette konfigurationer til dit netværk. 

>[AZURE.IMPORTANT] Eksempler på denne side er beregnet til at være rent for at få vejledning. Du skal arbejde med din leverandør salg / tekniske team og teamet netværk for at komme frem til relevante konfigurationer til dine behov. Microsoft understøtter ikke problemer i forbindelse med konfigurationer, der er angivet på denne side. Du skal kontakte din leverandør af enhed for support til problemer.

Router konfiguration eksempler nedenfor gælder for alle peerings. Gennemse [ExpressRoute peerings](expressroute-circuit-peerings.md) og [ExpressRoute routing krav](expressroute-routing.md) til flere oplysninger om routing.

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE baseret routere

Eksemplerne i dette afsnit gælder for en hvilken som helst router, der kører på IOS-XE OS familie.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. konfiguration af grænseflader og underordnede grænseflader

Du kræver en sub grænseflade per peering i hver router, du opretter forbindelse til Microsoft. En sub-grænseflade kan blive identificeret med et VLAN-ID eller et stablet par VLAN-id'er og en IP-adresse.

#### <a name="dot1q-interface-definition"></a>Dot1Q interfacedefinition

Dette eksempel indeholder underordnede interfacedefinitionen for en underordnet grænseflade med en enkelt VLAN-ID. VLAN ID'ET er entydige for hver peering. Den sidste oktet af afsenderadressen IPv4 være altid et ulige tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>QinQ interfacedefinition

Dette eksempel indeholder underordnede interfacedefinitionen for en underordnet grænseflade med to VLAN-id'er. Det ydre VLAN-ID (s-mærke), hvis brugt forbliver de samme på tværs af alle peerings. Det indre VLAN-ID (c-tags) er entydige for hver peering. Den sidste oktet af afsenderadressen IPv4 være altid et ulige tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. konfiguration af eBGP sessioner

Du skal konfigurere en session med BGP med Microsoft for hver peering. Eksemplet nedenfor kan du konfigurere en session med BGP med Microsoft. Hvis du har brugt til din sub-grænseflade IPv4-adressen var a.b.c.d, bliver IP-adressen på BGP tilstødende (Microsoft) a.b.c.d+1. Den sidste oktet for den BGP tilstødende IPv4-adresse være altid et lige tal.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. konfigurere præfikser til at vises over BGP sessionen

Du kan konfigurere din router for at annoncere Vælg præfikser til Microsoft. Du kan gøre det ved hjælp af eksemplet nedenfor.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. rute kort

Du kan bruge rute kort og præfiks lister til filter præfikser, der er blevet overført til dit netværk. Du kan bruge eksemplet nedenfor til at udføre opgaven. Sørg for, at du har relevante præfiks lister konfiguration.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX serie routere 

Eksemplerne i dette afsnit gælder for en hvilken som helst Juniper MX-serie routere.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. konfiguration af grænseflader og underordnede grænseflader

#### <a name="dot1q-interface-definition"></a>Dot1Q interfacedefinition

Dette eksempel indeholder underordnede interfacedefinitionen for en underordnet grænseflade med en enkelt VLAN-ID. VLAN ID'ET er entydige for hver peering. Den sidste oktet af afsenderadressen IPv4 være altid et ulige tal.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>QinQ interfacedefinition

Dette eksempel indeholder underordnede interfacedefinitionen for en underordnet grænseflade med to VLAN-id'er. Det ydre VLAN-ID (s-mærke), hvis brugt forbliver de samme på tværs af alle peerings. Det indre VLAN-ID (c-tags) er entydige for hver peering. Den sidste oktet af afsenderadressen IPv4 være altid et ulige tal.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. konfiguration af eBGP sessioner

Du skal konfigurere en session med BGP med Microsoft for hver peering. Eksemplet nedenfor kan du konfigurere en session med BGP med Microsoft. Hvis du har brugt til din sub-grænseflade IPv4-adressen var a.b.c.d, bliver IP-adressen på BGP tilstødende (Microsoft) a.b.c.d+1. Den sidste oktet for den BGP tilstødende IPv4-adresse være altid et lige tal.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. konfigurere præfikser til at vises over BGP sessionen

Du kan konfigurere din router for at annoncere Vælg præfikser til Microsoft. Du kan gøre det ved hjælp af eksemplet nedenfor.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. rute kort

Du kan bruge rute kort og præfiks lister til filter præfikser, der er blevet overført til dit netværk. Du kan bruge eksemplet nedenfor til at udføre opgaven. Sørg for, at du har relevante præfiks lister konfiguration.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Næste trin

Se [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md) få mere at vide.
