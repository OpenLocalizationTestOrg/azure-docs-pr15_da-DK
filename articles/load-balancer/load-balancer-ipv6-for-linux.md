<properties
    pageTitle="Konfiguration af DHCPv6 til Linux FOS | Microsoft Azure"
    description="Sådan konfigureres DHCPv6 til Linux FOS."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6-azure justering af belastning dobbelt stak, offentlige IP-adresse, oprindelige ipv6, mobile, og iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Konfiguration af DHCPv6 til Linux FOS

Nogle af Linux virtuelt billederne i Azure Marketplace har ikke DHCPv6 som standard konfigureret. Hvis du vil understøtter IPv6, skal DHCPv6 konfigureres i i Linux OS fordelingen, som du bruger. Forskellige Linux fordelingerne har forskellige måder at konfigurere DHCPv6, fordi de bruger forskellige pakker.

>[AZURE.NOTE] Seneste SUSE Linux og CoreOS billeder i Azure Marketplace har været forudkonfigurerede med DHCPv6. Der kræves ingen ekstra ændringer, når du bruger disse billeder.

Dette dokument beskrives, hvordan du aktiverer DHCPv6, så din Linux virtuelle maskine henter en IPv6-adresse.

>[AZURE.WARNING] Forkert redigering af netværk konfigurationsfiler kan du risikere at miste netværksadgang til din VM. Vi anbefaler, at du tester konfigurationsændringerne på ikke-produktiv systemer. Vejledningen i denne artikel er testet på de nyeste versioner af Linux billederne i Azure Marketplace. Se i dokumentationen til din specifikke version af Linux kan finde mere detaljerede oplysninger.

## <a name="ubuntu"></a>Ubuntu

1. Redigere filen `/etc/dhcp/dhclient6.conf` og tilføje følgende linje:

        timeout 10;

2. Redigere netværkskonfiguration for grænsefladen eth0 med følgende konfiguration:

    * På **Ubuntu 12.04 og 14.04**, skal du redigere filen`/etc/network/interfaces.d/eth0.cfg`
    * Rediger filen på **Ubuntu 16.04**`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Forny IPv6-adresse:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Redigere filen `/etc/dhcp/dhclient6.conf` og tilføje følgende linje:

        timeout 10;

2. Redigere filen `/etc/network/interfaces` og tilføje følgende konfiguration:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Forny IPv6-adresse:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Redigere filen `/etc/sysconfig/network` og tilføje følgende parameter:

        NETWORKING_IPV6=yes

2. Redigere filen `/etc/sysconfig/network-scripts/ifcfg-eth0` og tilføje følgende to parametre:

        IPV6INIT=yes
        DHCPV6C=yes

3. Forny IPv6-adresse:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Seneste SLES og openSUSE billeder i Azure har været forudkonfigurerede med DHCPv6. Der kræves ingen ekstra ændringer, når du bruger disse billeder. Hvis du har en VM, der er baseret på en ældre eller brugerdefineret SUSE billede, skal du følge nedenstående trin:

1. Installere på `dhcp-client` pakke, hvis det er nødvendigt:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Redigere filen `/etc/sysconfig/network/ifcfg-eth0` og tilføje følgende parameter:

        DHCLIENT6_MODE='managed'

3. Forny IPv6-adresse:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 og openSUSE fremskridt

Seneste SLES og openSUSE billeder i Azure har været forudkonfigurerede med DHCPv6. Der kræves ingen ekstra ændringer, når du bruger disse billeder. Hvis du har en VM, der er baseret på en ældre eller brugerdefineret SUSE billede, skal du følge nedenstående trin:

1. Redigere filen `/etc/sysconfig/network/ifcfg-eth0` og erstatte denne parameter

        #BOOTPROTO='dhcp4'

    med følgende værdi:

        BOOTPROTO='dhcp'

2. Føj følgende parameter til `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Forny IPv6-adresse:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Seneste CoreOS billeder i Azure har været forudkonfigurerede med DHCPv6. Der kræves ingen ekstra ændringer, når du bruger disse billeder. Hvis du har en VM, der er baseret på en ældre eller brugerdefineret CoreOS billede, skal du følge nedenstående trin:

1. Redigere filen`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Forny IPv6-adresse:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
