##<a name="tcp-settings-for-azure-vms"></a>TCP-indstillinger for Azure FOS

Azure FOS kommunikere med det offentlige Internet ved hjælp af [NAT] [ nat] (NAT). NAT-enheder tildele en offentlige IP-adresse og port til en Azure VM, så, VM til at oprette en socket til kommunikation med andre enheder. Hvis pakker stopper, der flyder gennem socket efter et bestemt tidspunkt, NAT-enheden afbryder tilknytningen, og Socketen er gratis der skal bruges i andre FOS.

Dette er almindelige NAT-funktioner, som kan medføre problemer med kommunikation på TCP-baserede programmer, der forventer en socket skal bevares ud over en timeoutperiode. Der er to inaktiv timeoutindstillinger skal du tænke på, for sessioner i tilstanden *oprettet forbindelse* :

- **indgående** via [Azure justering af belastning][azure-lb-timeout]. Denne timeout 4 minutter som standard, og kan ændres op til 30 minutter.
- **udgående** ved hjælp af [SNAT] [ snat] (kilde NAT). Denne timeout er indstillet til 4 minutter, og kan ikke justeres.

For at sikre forbindelser ikke går tabt ud over timeoutgrænsen på, skal du sikre dig, enten dit program bevarer sessionen Live, eller du kan konfigurere det underliggende operativsystem for at gøre dette. Indstillingerne, der skal bruges er forskellige for Linux og Windows-systemer, som vist nedenfor.

For [Linux][linux], skal du ændre kernen variablerne nedenfor.
NET.IPv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8
 
Til [Windows][windows], skal du ændre værdierne i registreringsdatabasen nedenfor.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Indstillingerne ovenfor sikre en Behold Live pakke er sendt, efter 2 minutter (120 sekunder) Inaktivitetstid, og derefter sendes hvert 30. Og hvis 8 af disse pakker mislykkes, sessionen afbrydes.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md