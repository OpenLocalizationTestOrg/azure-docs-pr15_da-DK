<properties
    pageTitle="Oprette forbindelse til en SQL Server virtuel maskine (ressourcestyring) | Microsoft Azure"
    description="Lær at oprette forbindelse til SQL Server, der kører på en virtuel maskine i Azure. Dette emne bruges den klassiske implementeringsmodel. Scenarier er forskellige afhængigt af konfigurationen af netværk og placeringen af klienten."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Oprette forbindelse til en SQL Server virtuel maskine på Azure (ressourcestyring)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-connect.md)
- [Klassisk](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du opretter forbindelse til din SQL Server-forekomst, der kører på en Azure virtuelt. Det dækker nogle [generelle connectivity scenarier](#connection-scenarios) og derefter indeholder [detaljeret vejledning til konfiguration af SQL Server-forbindelse i en Azure VM](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel. For at få vist den klassiske version af denne artikel, skal du se [forbinde til en SQL Server virtuel maskine på Azure klassisk](virtual-machines-windows-classic-sql-connect.md).

Hvis du hellere vil have en komplet gennemgang af både klargøring og forbindelse, kan du se [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Forbindelse scenarier

Den måde, en klient, der opretter forbindelse til SQL Server, der kører på en virtuel maskine varierer afhængigt af placeringen af klienten og maskine/netværk konfigurationen. Disse scenarier omfatter:

- [Oprette forbindelse til SQL Server via internettet](#connect-to-sql-server-over-the-internet)
- [Oprette forbindelse til SQL Server i det samme virtuelle netværk](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Oprette forbindelse til SQL Server via internettet

Hvis du vil oprette forbindelse til din SQL Server-database engine fra internettet, der er flere trin, der kræves som konfiguration af firewall, så i SQL-godkendelse og konfigurere dit netværk sikkerhedsgruppe, som du skal have et netværk sikkerhedsgruppe regel, der tillader TCP-trafik på port 1433.

Hvis du bruger på portalen til at klargøre en SQL Server virtuelt billede med Ressourcestyring, udført disse trin for dig, når du vælger **offentlige** SQL connectivity indstillingen:

![Offentlige SQL connectivity indstilling under klargøring](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Hvis dette ikke er en under klargøringen, kan derefter du manuelt konfigurere SQL Server og virtuelle maskiner ved at følge [trinnene i denne artikel for at konfigurere forbindelsen manuelt](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] Et virtuelt billede for SQL Server Express edition aktiverer ikke automatisk TCP/IP-protokollen. For Express edition, skal du bruge SQL Server Configuration Manager [manuelt](#configure-sql-server-to-listen-on-the-tcp-protocol) aktivere TCP/IP-protokollen, når du har oprettet VM.

Når dette er færdig, kan enhver klient med internetadgang oprette forbindelse til SQL Server-forekomsten ved at angive enten den offentlige IP-adresse på den virtuelle maskine eller DNS-etiket, der er tildelt til den pågældende IP-adresse. Hvis SQL Server-port 1433, behøver du ikke angive det i forbindelsesstrengen.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Selvom den måde kan connectivity for klienter via internettet, betyder det ikke, at alle kan oprette forbindelse til SQL Server. Udefrakommende har klienter til det rigtige brugernavn og adgangskode. Du kan undgå den velkendte port 1433 for ekstra sikkerhed. Eksempelvis hvis du har konfigureret SQL Server for at lytte på port 1500 og etableret stort firewall og netværk sikkerhed gruppe regler, kan du forbinde ved at føje portnummeret til navnet på den Server som i følgende eksempel:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Det er vigtigt at bemærke, at når du bruger denne metode til at kommunikere med SQL Server, alle udgående data fra Azure datacenter er underlagt normal [priser på udgående dataoverførsler](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Oprette forbindelse til SQL Server i det samme virtuelle netværk

[Virtuelt netværk](../virtual-network/virtual-networks-overview.md) gør det muligt for flere scenarier. Du kan oprette forbindelse FOS i det samme virtuelle netværk, selvom FOS findes i forskellige grupper. Og med en [VPN til-websted](../vpn-gateway/vpn-gateway-site-to-site-create.md), kan du oprette en hybrid arkitektur, der forbinder FOS med lokale netværk og computere.

Virtuelt Network gør det også muligt at deltage i dit Azure FOS til et domæne. Dette er den eneste måde at bruge Windows-godkendelse til SQL Server. De andre forbindelse scenarier kræver SQL-godkendelse med brugernavne og adgangskoder.

Hvis du bruger på portalen til at klargøre en SQL Server virtuelt billede med Ressourcestyring, er stort firewallregler for kommunikation på det virtuelle netværk konfiguration, når du vælger **privat** for indstillingen SQL connectivity. Hvis dette ikke er en under klargøringen, kan derefter du manuelt konfigurere SQL Server og virtuelle maskiner ved at følge [trinnene i denne artikel for at konfigurere forbindelsen manuelt](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Men hvis du planlægger at konfigurere en domænemiljø og Windows-godkendelse, behøver du ikke følge trinnene i denne artikel til at konfigurere SQL-godkendelse og logon. Du også har ikke brug for at konfigurere netværk sikkerhedsgruppe regler for adgang via internettet.

>[AZURE.NOTE] Et virtuelt billede for SQL Server Express edition aktiverer ikke automatisk TCP/IP-protokollen. For Express edition, skal du bruge SQL Server Configuration Manager [manuelt](#configure-sql-server-to-listen-on-the-tcp-protocol) aktivere TCP/IP-protokollen, når du har oprettet VM.

Hvis resultatet af, at du har konfigureret DNS i netværket virtuel, kan du oprette forbindelse til SQL Server-forekomst ved at angive navnet på SQL Server VM computer i forbindelsesstrengen. I følgende eksempel antages det, som også er blevet konfigureret Windows-godkendelse og, at brugeren har fået adgang til SQL Server-forekomsten.

    "Server=mysqlvm;Integrated Security=true"

Bemærk, at i dette scenarie skal du kan også angive VM IP-adresse.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Trin til manuel konfiguration af SQL Server-forbindelse i en Azure VM

Følgende trin viser, hvordan du manuelt installere forbindelse til SQL Server-forekomsten og derefter du kan også oprette forbindelse via internettet ved hjælp af SQL Server Management Studio (SSMS). Bemærk, at mange af disse trin er gjort for dig, når du vælger de relevante SQL Server connectivity indstillinger på portalen.

Før du kan oprette forbindelse til forekomsten af SQL Server fra en anden VM eller på internettet, skal du udføre følgende opgaver, som beskrevet i følgende afsnit:

- [Åbn TCP-porte i Windows firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Konfiguration af SQL Server for at lytte til TCP-protokollen](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Konfiguration af SQL Server til blandet tilstand-godkendelse](#configure-sql-server-for-mixed-mode-authentication)
- [Oprette SQL Server-godkendelse logon](#create-sql-server-authentication-logins)
- [Konfigurere en indgående regel netværk sikkerhedsgruppe](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Konfigurere en DNS-etiket til den offentlige IP-adresse](#configure-a-dns-label-for-the-public-ip-address)
- [Oprette forbindelse til Database Engine fra en anden computer](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Næste trin

Klargøring af vejledningen sammen med følgende connectivity, finder du [Klargør en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Gennemse læringssti](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) til SQL Server på Azure virtuelle computere.

Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
