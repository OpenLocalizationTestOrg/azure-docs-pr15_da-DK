<properties
    pageTitle="Oprette forbindelse til en SQL Server virtuel maskine (klassisk) | Microsoft Azure"
    description="Lær at oprette forbindelse til SQL Server, der kører på en virtuel maskine i Azure. Dette emne bruges den klassiske implementeringsmodel. Scenarier er forskellige afhængigt af konfigurationen af netværk og placeringen af klienten."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Oprette forbindelse til en SQL Server virtuel maskine på Azure (klassisk installation)

> [AZURE.SELECTOR]
- [Ressourcestyring](virtual-machines-windows-sql-connect.md)
- [Klassisk](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du opretter forbindelse til din SQL Server-forekomst, der kører på en Azure virtuelt. Det dækker nogle [generelle connectivity scenarier](#connection-scenarios) og derefter indeholder [detaljeret vejledning til konfiguration af SQL Server-forbindelse i en Azure VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Hvis du bruger ressourcestyring FOS skal du se [forbinde til en SQL Server virtuel maskine på Azure ved hjælp af Ressourcestyring](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Forbindelse scenarier

Den måde, en klient, der opretter forbindelse til SQL Server, der kører på en virtuel maskine varierer afhængigt af placeringen af klienten og maskine/netværk konfigurationen. Disse scenarier omfatter:

- [Oprette forbindelse til SQL Server i det samme skytjeneste](#connect-to-sql-server-in-the-same-cloud-service)
- [Oprette forbindelse til SQL Server via internettet](#connect-to-sql-server-over-the-internet)
- [Oprette forbindelse til SQL Server i det samme virtuelle netværk](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Før du opretter forbindelse med en af disse metoder, skal du følge [trinnene i denne artikel for at konfigurere forbindelsen](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Oprette forbindelse til SQL Server i det samme skytjeneste

Flere virtuelle maskiner kan oprettes i den samme skybaseret tjeneste. For at forstå dette scenario virtuelle maskiner, se, [hvordan du opretter forbindelse virtuelle maskiner med en virtuel netværk eller skybaseret tjeneste](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Dette scenario er, når en klient på et virtuelt forsøger at oprette forbindelse til SQL Server, der kører på en anden virtuel maskine i den samme skybaseret tjeneste.

I dette scenario, kan du oprette forbindelse ved hjælp af VM **navn** (også vist som **Computernavn** eller **hostname** i portalen). Dette er det navn, du har angivet for VM under oprettelse. Hvis du navngivet dine SQL VM **mysqlvm**, kan en klient VM i samme skytjenesten for eksempel bruge følgende forbindelsesstreng til at oprette forbindelse:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Oprette forbindelse til SQL Server via internettet

Hvis du vil oprette forbindelse til din SQL Server-database engine fra internettet, skal du oprette et virtuelt slutpunkt for indgående TCP-kommunikation. Dette trin i Azure konfigurationen, omdirigerer indgående TCP-porttrafik til en TCP-port, der er tilgængeligt til den virtuelle maskine.

Hvis du vil oprette forbindelse via internettet, skal du bruge den VM DNS-navn og VM slutpunkt portnummeret (konfigureret senere i denne artikel). Gå til portalen Azure for at finde DNS-navnet, og vælg **virtuelle maskiner (klassisk)**. Vælg din virtuelle maskine. **DNS-navn** vises i sektionen **Overview** .

For eksempel bør du overveje en klassisk virtuel maskine, der hedder **mysqlvm** med en DNS-navnet **mysqlvm7777.cloudapp.net** og en **57500**VM slutpunkt. Hvis en konfigureret forbindelse, kan følgende forbindelsesstreng bruges til at få adgang til den virtuelle maskine fra et vilkårligt sted på internettet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Selvom den måde kan connectivity for klienter via internettet, betyder det ikke, at alle kan oprette forbindelse til SQL Server. Udefrakommende har klienter til det rigtige brugernavn og adgangskode. Ikke brug for ekstra sikkerhed, den velkendte port 1433 for det offentlige virtuelt slutpunkt. Og hvis det er muligt, kan du overveje at tilføje en ACL på din slutpunkt at begrænse trafik kun til klienterne, du giver tilladelse. Du kan finde vejledning til brug af ACLs med slutpunkter, [Administrer ACL på et slutpunkt](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] Det er vigtigt at bemærke, at når du bruger denne metode til at kommunikere med SQL Server, alle udgående data fra Azure datacenter er underlagt normal [priser på udgående dataoverførsler](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Oprette forbindelse til SQL Server i det samme virtuelle netværk

[Virtuelt netværk](../virtual-network/virtual-networks-overview.md) gør det muligt for flere scenarier. Du kan oprette forbindelse FOS i det samme virtuelle netværk, selvom FOS findes i forskellige skytjenester. Og med en [VPN til-websted](../vpn-gateway/vpn-gateway-site-to-site-create.md), kan du oprette en hybrid arkitektur, der forbinder FOS med lokale netværk og computere.

Virtuelt Network gør det også muligt at deltage i dit Azure FOS til et domæne. Dette er den eneste måde at bruge Windows-godkendelse til SQL Server. De andre forbindelse scenarier kræver SQL-godkendelse med brugernavne og adgangskoder.

Hvis du vil konfigurere en domænemiljø og Windows-godkendelse, behøver du ikke følge trinnene i denne artikel til at konfigurere det offentlige slutpunkt eller SQL-godkendelse og logon. I dette scenarie skal du oprette forbindelse til din SQL Server-forekomst ved at angive navnet på SQL Server VM i forbindelsesstrengen. I følgende eksempel antages det, som også er blevet konfigureret Windows-godkendelse og, at brugeren har fået adgang til SQL Server-forekomsten.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Trin til konfiguration af SQL Server-forbindelse i en Azure VM

Følgende trin viser, hvordan du opretter forbindelse til SQL Server-forekomsten via internettet ved hjælp af SQL Server Management Studio (SSMS). Dog gælder de samme trin for at gøre din SQL Server virtuelle maskine tilgængelige for dine programmer, der kører både lokalt og i Azure.

Før du kan oprette forbindelse til forekomsten af SQL Server fra en anden VM eller på internettet, skal du udføre følgende opgaver, som beskrevet i følgende afsnit:

- [Oprette et TCP slutpunkt for den virtuelle maskine](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Åbn TCP-porte i Windows firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Konfiguration af SQL Server for at lytte til TCP-protokollen](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Konfiguration af SQL Server til blandet tilstand-godkendelse](#configure-sql-server-for-mixed-mode-authentication)
- [Oprette SQL Server-godkendelse logon](#create-sql-server-authentication-logins)
- [Finde DNS-navnet på den virtuelle maskine](#determine-the-dns-name-of-the-virtual-machine)
- [Oprette forbindelse til Database Engine fra en anden computer](#connect-to-the-database-engine-from-another-computer)

Forbindelsesstien er opsummeret ved i det følgende diagram:

![Oprette forbindelse til en SQL Server virtuel maskine](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Næste trin

Hvis du også planlægger at bruge AlwaysOn tilgængelighed grupper til høj tilgængelighed og nedbrud, bør du overveje at implementere en lytter. Database klienter opretter forbindelse til lytteren i stedet for direkte til en af forekomsterne af SQL Server. Lytteren dirigerer klienter til den primære replika i gruppen tilgængelighed. Se [konfigurere en ILB lytteren for AlwaysOn tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-int-listener.md)kan finde flere oplysninger.

Det er vigtigt at gennemse alle de bedste fremgangsmåder til sikkerhed for SQL Server, der kører på en Azure virtuel maskine. Du kan finde yderligere oplysninger finder [Overvejelser om sikkerheden for SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-security.md).

[Gennemse læringssti](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) til SQL Server på Azure virtuelle computere. 

Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
