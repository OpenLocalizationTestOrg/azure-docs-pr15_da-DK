<properties
    pageTitle="Høj tilgængelighed og nedbrud til SQL Server | Microsoft Azure"
    description="En beskrivelse af de forskellige typer HADR strategier for SQL Server, der kører i virtuelle Azure-computere."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Høj tilgængelighed og genoprettelse efter nedbrud til SQL Server på virtuelle Azure-computere

## <a name="overview"></a>Oversigt

Microsoft Azure virtuelle maskiner (VM'er) med SQL Server kan hjælpe dig med at reducere omkostningerne ved en høj tilgængelighed og genoprettelse efter genoprettelse (HADR) databaseløsning. De fleste SQL Server HADR løsninger, der understøttes i Azure virtuelle maskiner, både som kun Azure og som hybrid løsninger. Hele HADR systemet kører i en kun Azure-løsning i Azure. I en hybridkonfiguration kører en del af løsningen i Azure og anden del kører lokalt i organisationen. Fleksibilitet af Azure miljøet gør det muligt at flytte helt eller delvist til Azure at opfylde de budget og HADR af SQL Server-database-systemer.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Forstå behovet for en HADR løsning

Det er op til dig for at sikre, at databasesystemet i besiddelse af de HADR funktioner, der kræver serviceniveauaftale (SLA). Fakultet, Azure indeholder høj tilgængelighed funktioner, som tjenesten reparation til skytjenester og registrering af fejl på gendannelse til de virtuelle maskiner, garanterer ikke selv kan du opfylder de ønskede SERVICENIVEAUAFTALE. Disse mekanismer Beskyt høj tilgængeligheden af FOS, men ikke høj tilgængeligheden af SQL Server, der kører i FOS. Det er muligt for SQL Server-forekomsten mislykkes, mens VM er online og sund. Desuden lige høj tilgængeligheden fra Azure gør det muligt for nedetid af FOS på grund af begivenheder som gendannelse fra software eller mislykkede forsøg på hardware og opgradering af operativsystemet.

Desuden geografisk overflødige lagerplads (GRS) i Azure, som er implementeret med en funktion, der hedder geografisk gentagelse, muligvis ikke en løsning til tilstrækkelig genoprettelse efter genoprettelse for dine databaser. Seneste opdateringer kan gå tabt i tilfælde af nedbrud, fordi geografisk gentagelse sender data asynkront. Flere oplysninger om begrænsninger for geografisk replikering er dækket i sektionen [geografisk replikering understøttes ikke for data og logfiler på separate diske](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>HADR installation arkitekturer

SQL Server HADR teknologier, der understøttes i Azure omfatter:

- [Altid på tilgængelighed grupper](https://technet.microsoft.com/library/hh510230.aspx)
- [Samme database](https://technet.microsoft.com/library/ms189852.aspx)
- [Log levering](https://technet.microsoft.com/library/ms187103.aspx)
- [Sikkerhedskopiere og gendanne med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx)
- [Altid på sekundær klynge forekomster](https://technet.microsoft.com/library/ms189134.aspx)

Det er muligt at kombinere teknologier sammen for at implementere en SQL Server-løsning, der indeholder både høj tilgængelighed og muligheder for genoprettelse efter genoprettelse. Afhængigt af den teknologi, du bruger, kan en hybridinstallation kræver en VPN-tunnel med Azure virtuelt netværk. Afsnittene herunder viser nogle af eksempel installation arkitekturer.

## <a name="azure-only-high-availability-solutions"></a>Kun Azure: høj tilgængelighed løsninger

Du kan have en høj tilgængelighed løsning til SQL Server-databaser i Azure ved hjælp af altid på tilgængelighed-grupper eller Webdatabase spejling.

| Teknologi                               | Eksempel arkitekturer                    |
| ---------------------------------------- | ---------------------------------------- |
| **Altid på tilgængelighed grupper**        | Alle tilgængelighed Kopier køre i Azure FOS til høj tilgængelighed i det samme område. Du skal konfigurere et domænenavn fra domænecontrolleren VM, fordi Windows Server Failover klynge (WSFC) kræver en Active Directory-domæne.<br/> ![Altid på tilgængelighed grupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Yderligere oplysninger finder du se [Konfigurere altid på tilgængelighed grupper i Azure (grafiske)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Altid på sekundær klynge forekomster** | Failover klynge forekomster (FCI), som kræver delt storage, kan oprettes på 2 forskellige måder.<br/><br/>1. en FCI på en to noder WSFC kører i Azure FOS med lagerplads, der understøttes af en tredjepartsudbyder af clusterløsning. Du kan finde en bestemt eksempel, der bruger SIOS DataKeeper, [høj tilgængelighed til et filshare, ved hjælp af WSFC og 3 part software SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. en FCI på en to noder WSFC kører i Azure FOS med remote iSCSI Target delt blokstorage via ExpressRoute. For eksempel Fremviser NetApp privat lagerplads (NPS) et iSCSI-mål via ExpressRoute med Equinix til Azure FOS.<br/><br/>For tredjeparts delt storage og data gentagelse løsninger, skal du kontakte forhandleren til problemer med at få adgang til data på failover.<br/><br/>Bemærk, at brug FCI oven på [Azure fillagring](https://azure.microsoft.com/services/storage/files/) ikke understøttes endnu, fordi denne løsning ikke anvende Premium lagerplads. Vi arbejder på at understøtte dette snart. |

## <a name="azure-only-disaster-recovery-solutions"></a>Kun Azure: løsninger til genoprettelse efter genoprettelse

Du kan har en løsning til genoprettelse efter genoprettelse i dine SQL Server-databaser i Azure ved hjælp af altid på tilgængelighed-grupper, database spejling eller sikkerhedskopi og gendanne med BLOB storage.

| Teknologi                               | Eksempel arkitekturer                    |
| ---------------------------------------- | ---------------------------------------- |
| **Altid på tilgængelighed grupper**        | Tilgængelighed replikaer kører på tværs af flere datacentre i Azure FOS for nedbrud. Denne løsning i tværs område beskytter mod komplet websted afbrydelse. <br/> ![Altid på tilgængelighed grupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>I et område skal alle kopier inden for samme skytjenesten og den samme VNet. Da hvert område skal have en separat VNet, kræver disse løsninger VNet til VNet connectivity. Se [konfigurere et websted til websted VPN i portalen Azure klassisk](../vpn-gateway/vpn-gateway-site-to-site-create.md)kan finde flere oplysninger. |
| **Samme database**                   | Hovedstolen og spejling og servere, der kører i forskellige datacentre for nedbrud. Du skal installere ved hjælp af servercertifikater, fordi en active directory-domæne ikke kan strækker sig over flere datacentre.<br/>![Samme database](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Sikkerhedskopiere og gendanne med Azure Blob Storage Service** | Fremstilling databaser, der er sikkerhedskopieret direkte til blob-lager i en anden datacenter for nedbrud.<br/>![Sikkerhedskopiering og gendannelse](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Se [Sikkerhedskopiering og gendannelse til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md)kan finde flere oplysninger. |

## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: Løsninger genoprettelse efter genoprettelse

Du kan have en løsning til genoprettelse efter genoprettelse til dine SQL Server-databaser i en hybrid-IT-miljø ved hjælp af altid på tilgængelighed-grupper, databasespejling, log forsendelses og sikkerhedskopiering og gendanne med Azure blog lagerplads.

| Teknologi                               | Eksempel arkitekturer                    |
| ---------------------------------------- | ---------------------------------------- |
| **Altid på tilgængelighed grupper**        | Nogle tilgængelighed replikaer, der kører i Azure FOS og andre replikaer kører lokalt for nedbrud på tværs af websteder. Webstedet fremstilling kan være enten lokalt eller i en Azure datacenter.<br/>![Altid på tilgængelighed grupper](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Da alle tilgængelighed kopier skal være i samme WSFC klynge, skal WSFC klynge dække begge netværk (en med flere undernet WSFC klynge). Denne konfiguration kræver en VPN-forbindelse mellem Azure og lokale netværk.<br/><br/>Til vellykket nedbrud i dine databaser, skal du også installere en replikeringsdomænecontroller på webstedet nedbrud gendannelse.<br/><br/>Det er muligt at bruge guiden Tilføj replika i SSMS til at tilføje en Azure kopi til en eksisterende altid på tilgængelighed gruppe. Kan finde flere oplysninger i Selvstudium: udvide din altid på tilgængelighed gruppe til Azure. |
| **Samme database**                   | En partner, der kører i en Azure VM og de andre, der kører på det lokale miljø til på tværs af websteder nedbrud ved hjælp af servercertifikater. Partnere behøver ikke at være i samme Active Directory-domæne, og ingen VPN-forbindelse er påkrævet.<br/>![Samme database](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>En anden database samme scenario omfatter en partner, der kører i en Azure VM og de andre, der kører lokalt i det samme Active Directory-domæne til nedbrud på tværs af websteder. Der kræves en [VPN-forbindelse mellem Azure virtuelt netværk og lokale netværk](../vpn-gateway/vpn-gateway-site-to-site-create.md) .<br/><br/>Til vellykket nedbrud i dine databaser, skal du også installere en replikeringsdomænecontroller på webstedet nedbrud gendannelse. |
| **Log levering**                         | En server, der kører i en Azure VM og de andre, der kører på det lokale miljø til nedbrud på tværs af websteder. Log levering afhænger af Windows fildeling, så der kræves en VPN-forbindelse mellem Azure virtuelt netværk og lokale netværk.<br/>![Log levering](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Til vellykket nedbrud i dine databaser, skal du også installere en replikeringsdomænecontroller på webstedet nedbrud gendannelse. |
| **Sikkerhedskopiere og gendanne med Azure Blob Storage Service** | Lokale fremstilling databaser sikkerhedskopieret direkte til Azure blob-lager til nedbrud.<br/>![Sikkerhedskopiering og gendannelse](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Se [Sikkerhedskopiering og gendannelse til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-backup-recovery.md)kan finde flere oplysninger. |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Vigtige overvejelser i forbindelse med SQL Server HADR i Azure

Azure FOS, storage og netværk har forskellige funktionsdygtige egenskaber end et lokalt, ikke virtualiseret IT-infrastruktur. En vellykket implementering af en HADR SQL Server-løsning i Azure kræver, at du forstå forskellene og designe din løsning til at medtage dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Høj tilgængelighed knuderne i et sæt tilgængelighed

Tilgængelighed sæt i Azure gør det muligt at placere noderne høj tilgængelighed i separate fejl domæner (FDs), og Opdater domæner (UDs). Azure FOS skal placeres i det samme sæt tilgængelighed, skal du installere dem i den samme skybaseret tjeneste. Kun knuder på samme skytjenesten kan deltage i det samme sæt tilgængelighed. Se [administrere tilgængelighed af virtuelle maskiner](virtual-machines-windows-manage-availability.md)kan finde flere oplysninger.

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>WSFC klynge funktionsmåde i Azure netværk

Ikke-RFC-kompatibel DHCP-tjenesten i Azure kan medføre oprettelse af visse WSFC klyngekonfigurationer mislykkes på grund af netværksnavn klynge, tildeles en dublerede IP-adresse, som den samme IP-adresse som et af klyngenoderne. Dette er et problem, når du implementerer altid på tilgængelighed grupper, som afhænger af funktionen WSFC.

Overvej dette scenario, når en klynge med to noder oprettes og kommer online:

1. Klyngen er online, og derefter NODE1 anmoder om en dynamisk tildelt IP-adresse til netværksnavn klynge.

2. Ingen IP-adresse end NODE1's egen IP-adressen er angivet af tjenesten DHCP, da tjenesten DHCP genkender, anmodningen kommer fra NODE1 sig selv.

3. Windows registrerer, tildeles en dublerede adresse både NODE1 og klynge netværksnavn, og klynge standardgruppen ikke er online.

4. Klynge standardgruppen flytter til NODE2, der behandler NODE1's IP-adresse, som den klynge IP-adresse og kombinerer klynge standardgruppen online.

5. Når NODE2 forsøger at oprette forbindelse med NODE1, Lad pakker rettet mod NODE1 aldrig NODE2, da det løser NODE1's IP-adresse til sig selv. NODE2 kan ikke oprette forbindelse med NODE1 derefter mister quorum og lukker klyngen.

6. I mellemtiden NODE1 kan sende pakker til NODE2, men kan ikke svare, NODE2. NODE1 mister quorum og lukker klyngen.

Dette scenarie kan undgås ved at tildele en ubrugt statiske IP-adresse, som et link-local IP-adresse som 169.254.1.1, netværksnavn klynge for at få klynge netværksnavn online. Se [Konfigurere Windows sekundær klynge i Azure for altid på tilgængelighed-grupper](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)for at forenkle denne proces.

Yderligere oplysninger finder du se [Konfigurere altid på tilgængelighed grupper i Azure (grafiske)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Understøttelse af tilgængelighed gruppe lytteren

Tilgængelighed gruppe lyttere understøttes på Azure VM'er, der kører Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 og Windows Server 2016. Denne understøttelse er gjort muligt ved hjælp af netværksbelastningen slutpunkter aktiveret på den Azure VM'er, der er tilgængelighed gruppenoder. Du skal følge speciel konfigurationstrinnene for lyttere til at arbejde for begge klientprogrammer, der kører i Azure, såvel som dem, der kører i det lokale miljø.

Der er to primære indstillinger til konfiguration af din lytteren: ekstern (offentlig) eller interne. Eksterne (offentlige) lytteren bruger en via justering af belastning internettet og er knyttet til en offentlig virtuelle IP-(VIP), der er tilgængeligt via internettet. En intern lytteren bruger en intern justering af belastning og understøtter kun klienter i det samme virtuelle netværk. For enten at indlæse belastningsjusteringstjenesten type, skal du aktivere direkte serveren returnere. 

Hvis gruppen tilgængelighed strækker sig over flere Azure undernet (som en installation, der krydser Azure områder), forbindelsesstrengen klienten skal indeholde "**MultisubnetFailover = True**". Dette resulterer i parallelle forbindelse forsøg replikaer i de forskellige undernet. Få oplysninger om konfiguration af en lytter, gå

- [Konfigurer en ILB lytteren for altid på tilgængelighed grupper i Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Konfigurer en ekstern lytter for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Du kan stadig oprette forbindelse til hver tilgængelighed replika separat ved at oprette forbindelse direkte til forekomsten af tjenesten. Da altid på tilgængelighed-grupper er bagudkompatible med database spejling klienter, kan du også oprette forbindelse til tilgængelighed replikaer som database spejling partnere, som replikaerne er konfigureret ligner databasespejling:

- En primær replika og en sekundær replika

- Den sekundære replika er konfigureret som ikke kan læses (**Læsevenligt sekundær** indstilling indstillet til **Nej**)

Et eksempel klient forbindelsesstreng, der svarer til denne database spejling lignende konfiguration, ved hjælp af ADO.NET eller SQL Server Native Client er nedenfor:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Du kan finde flere oplysninger om klientforbindelse, i:

- [Brug af forbindelse streng nøgleord med SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Tilslutte klienter til en Database, spejling Session (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Oprette forbindelse til tilgængelighed gruppe lytteren i hybride IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Tilgængelighed gruppe lyttere, klientforbindelse og programmet sekundære (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Brug af Database-spejling strenge med tilgængelighed grupper](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Netværksventetid i hybride IT

Du skal installere HADR løsningen med den antagelse, at der kan være perioder med høj netværksventetid mellem dit lokale netværk og Azure. Når du installerer replikaer på Azure, skal du bruge asynkron Anvend i stedet for synkron bekræftelse for synkroniseringstilstand. Når implementerer database spejling servere både lokalt og i Azure, bruge tilstanden høj ydeevne i stedet for tilstanden høj sikkerhed.

### <a name="geo-replication-support"></a>Understøttelse af geografisk gentagelse

Geografisk-gentagelse i Azure diske understøtter ikke-datafil og logfil over den samme database gemmes på separate diske. GRS kopieres ændringer på hver enkelt disk, uafhængigt af hinanden og asynkront. Denne funktion garanterer Skriv rækkefølgen i en enkelt disk i den geografisk replikerede kopi, men ikke på tværs af geografisk replikerede kopier af flere diske. Hvis du konfigurerer en database for at gemme datafilen og logfilen på separate diske, kan de gendannede diske efter nedbrud kan indeholde en opdateret kopi af datafilen end logfil, som sideskift Skriv baseret log in SQL Server og SURT egenskaberne for transaktioner. Hvis du ikke har mulighed for at deaktivere geografisk replikering på kontoen lagerplads, skal du holde alle data, og log filer for en given database på samme disk. Hvis du skal bruge mere end én disk på grund af størrelsen på databasen, skal du installere en af de nedbrud gendannelse løsninger, der er nævnt ovenfor til at sikre overflødige data.

## <a name="next-steps"></a>Næste trin

Hvis du har brug at oprette en Azure virtuelt med SQL Server, kan du se [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md).

For at få den bedste ydeevne fra SQL Server, der kører på en Azure VM skal du se vejledningen i [Ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md).

Du kan finde andre emner, der er relateret til kører SQL Server i Azure FOS, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Andre ressourcer

- [Installere en ny Active Directory-område i Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Oprette WSFC klynge i altid på tilgængelighed grupper i Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
