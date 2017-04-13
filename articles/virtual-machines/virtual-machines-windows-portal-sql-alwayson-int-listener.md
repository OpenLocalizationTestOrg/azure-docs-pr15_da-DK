<properties
   pageTitle="Oprette lytteren for AlwaysOn availabilty gruppe til SQL Server på virtuelle Azure-computere"
   description="Trinvise instruktioner til oprettelse af en lytter for en AlwaysOn availabilty gruppe til SQL Server på virtuelle Azure-computere"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Konfigurere en intern justering af belastning for en AlwaysOn tilgængelighed gruppe i Azure

Dette emne forklares det, hvordan du opretter en intern justering af belastning for en SQL Server AlwaysOn tilgængelighed gruppe i Azure virtuelle maskiner, der kører i ressource manager model. En AlwaysOn tilgængelighed gruppe kræver en belastningsjustering, når forekomsterne af SQL Server er på Azure virtuelle maskiner. Justering af belastning gemmer IP-adressen for tilgængelighed gruppe lytteren. Hvis en tilgængelighed gruppe strækker sig over flere områder, skal hvert område for belastningsjustering.

For at udføre denne opgave, skal du have en SQL Server AlwaysOn tilgængelighed gruppe, der er installeret på Azure virtuelle maskiner i ressource manager model. Begge SQL Server virtuelle maskiner skal være medlem af det samme sæt tilgængelighed. Du kan bruge den [Microsoft-skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) til automatisk at oprette gruppen AlwaysOn tilgængelighed i Azure ressourcestyring. Denne skabelon opretter automatisk den interne justering af belastning for dig. 

Hvis du foretrækker, kan du [konfigurere en AlwaysOn tilgængelighed gruppe](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Dette emne kræver, at dine tilgængelighed grupper er allerede konfigureret.  

Relaterede emner omfatter:

 - [Konfigurere AlwaysOn tilgængelighed grupper i Azure VM (grafiske)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Konfigurere en VNet-VNet forbindelse ved hjælp af Azure ressourcestyring og PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Trin

Du kan oprette og konfigurere belastningsjustering i portalen Azure ved gennemgang af dette dokument. Når, der er fuldført, skal konfigurere du klynge for at bruge IP-adressen fra justering af belastning til AlwaysOn tilgængelighed gruppe lytteren.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Oprette og konfigurere belastning på portalen Azure

I denne del på opgaven, vil du udføre følgende trin på portalen Azure:

1. Oprette justering af belastning og konfigurere IP-adresse

1. Konfigurere back end-puljen

1. Oprette efterprøvning af af 

1. Angive regler for belastning

>[AZURE.NOTE] Hvis de SQL-servere i forskellige grupper og områder, vil du gøre alle disse trin to gange, én gang i hver ressourcegruppe.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. oprette justering af belastning og konfigurere IP-adresse

Det første trin er at oprette justering af belastning. Åbn den ressourcegruppe, der indeholder de SQL Server virtuelle maskiner i Azure-portalen. Klik på **Tilføj**i ressourcegruppen.

- Søge efter **justering af belastning**. Vælg **Justering af belastning**, som er blevet publiceret af **Microsoft**i søgeresultaterne.

- Klik på **Opret**på bladet **Justering af belastning** .

- Under **Opret justering af belastning**, konfigurere, på justering af belastning på følgende måde:

| Indstilling | Værdi |
| ----- | ----- |
| **Navn** | Et tekstnavn, der repræsenterer justering af belastning. For eksempel, **sqlLB**. |
| **Skema** | **Interne** |
| **Virtuelt netværk** | Vælg det virtuelle netværk, som de SQL-servere er i.   |
| **Undernet**  | Vælg det undernet, som de SQL-servere er i. |
| **Abonnement** | Hvis du har flere abonnementer, kan blive vist dette felt. Markere det abonnement, du vil knytte til denne ressource. Det er normalt den samme subcription som alle ressourcer til gruppen tilgængelighed.  |
| **Ressourcegruppe** | Vælg den ressourcegruppe, som de SQL-servere er i. | 
| **Placering** | Vælg den Azure placering, som de SQL-servere er i. |

- Klik på **Opret**. 

Azure opretter den justering af belastning, som du har konfigureret ovenfor. Justering af belastning hører til en bestemt netværk, undernet, ressourcegruppe og placering. Når Azure er fuldført, skal du kontrollere Indlæs belastningsjusteringstjenesten indstillinger i Azure. 

Nu, konfigurere Indlæs belastningsjusteringstjenesten IP-adresse.  

- Bladet Indlæs belastningsjusteringstjenesten **Indstillinger** , klik på **IP-adresse**. Bladet **IP-adresse** viser, at dette er en privat justering af belastning på det samme virtuelle netværk som din SQL-servere. 

- Angiv følgende indstillinger: 

| Indstilling | Værdi |
| ----- | ----- |
| **Undernet** | Vælg det undernet, som de SQL-servere er i. |
| **Tildeling** | **Statisk** |
| **IP-adresse** | Skriv en ubrugt virtuelle IP-adresse fra undernettet.  |

- Gemme indstillingerne.

Justering af belastning har nu en IP-adresse. Registrere denne IP-adresse. Du skal bruge denne IP-adresse, når du opretter en lytter på klyngen. I en PowerShell-script senere i denne artikel kan du bruge denne adresse for den `$ILBIP` variabel.



## <a name="2-configure-the-backend-pool"></a>2. Konfigurer back end-puljen

Næste trin er at oprette en back end-adresse samling. Azure kalder back end-adresse puljen *back end-puljen*. I dette tilfælde er back end-puljen adresserne på de to SQL-servere i din tilgængelighed gruppe. 

- Klik på den justering af belastning du har oprettet i din ressourcegruppe. 

- **Indstillinger**, klik på **back end-grupper**.

- Klik på **Tilføj** for at oprette en back end-adresse pulje **back end-adresse grupper**. 

- **Tilføj back end-puljen** under **navn**, Skriv et navn til back end-puljen.

- Klik på **+ Tilføj en virtuel maskine**under **virtuelle computere** . 

- Klik på **Angiv en tilgængelighed** under **Vælg virtuelle maskiner** og angive tilgængelighed SQL Server virtuelle maskiner tilhører.

- Når du har valgt sættet tilgængelighed, kan du klikke på **Vælg virtuelle maskiner**. Klik på de to virtuelle maskiner, der hoster forekomster af SQL Server i gruppen tilgængelighed. Klik på **Vælg**. 

- Klik på **OK** for at lukke blade for **Vælg virtuelle maskiner**, og **Tilføj back end-puljen**. 

Azure opdaterer indstillingerne for adresse back end-puljen. Angive din tilgængelighed har nu en samling af to SQL-servere.

## <a name="3-create-a-probe"></a>3. Opret en efterprøvning af af

Næste trin er at oprette en efterprøvning af af. Efterprøvning af af definerer, hvordan Azure kontrollere hvilken af de SQL-servere aktuelt ejer tilgængelighed gruppe lytteren. Azure vil sende forespørgsler baseret på IP-adresse på en port, som du definerer, når du opretter efterprøvning af af tjenesten.

- Klik på **sonder**bladet Indlæs belastningsjusteringstjenesten **Indstillinger** . 

- Klik på **Tilføj**på bladet **sonder** .

- Konfigurere efterprøvning af af på bladet **Tilføj efterprøvning af af** . Brug følgende værdier til at konfigurere efterprøvning af af:

| Indstilling | Værdi |
| ----- | ----- |
| **Navn** | Et tekstnavn, der repræsenterer efterprøvning af af. For eksempel, **SQLAlwaysOnEndPointProbe**. |
| **Protokol** | **TCP** |
| **Port** | Du kan bruge enhver tilgængelig port. For eksempel, *59999*.    |
| **Interval**  | *5* | 
| **Beskadiget grænseværdi**  | *2* | 

- Klik på **OK**. 

>[AZURE.NOTE] Sørg for, at du angiver porten er åben på firewallen på begge SQL-servere. Begge servere kræver en indgående regel for TCP-port, du bruger. Se [tilføje eller redigere firewallregel](http://technet.microsoft.com/library/cc753558.aspx) kan finde flere oplysninger. 

Azure opretter efterprøvning af af. Azure anvender efterprøvning af af til at teste hvilken SQL Server har lytteren for gruppen tilgængelighed.

## <a name="4-set-the-load-balancing-rules"></a>4. Angiv belastningsjustering regler

Angive regler for belastning. Justering af belastning reglerne konfigurere, hvordan justering af belastning dirigerer trafik til de SQL-servere. Du skal aktivere direkte server vende tilbage til denne justering af belastning fordi kun én af de to SQL-servere nogensinde ejer tilgængelighed gruppe lytteren ressourcen ad gangen.

- Klik på **Indlæs regler for justering af belastning**på bladet Indlæs belastningsjusteringstjenesten **Indstillinger** . 

- Klik på **Tilføj**på bladet **belastning regler** .

- Brug bladet **Tilføj indlæse justering af belastning regler** til at konfigurere belastningsjustering regel. Brug følgende indstillinger: 

| Indstilling | Værdi |
| ----- | ----- |
| **Navn** | Et tekstnavn, der repræsenterer belastningsjustering regler. For eksempel, **SQLAlwaysOnEndPointListener**. |
| **Protokol** | **TCP** |
| **Port** | *1433*   |
| **Back end-Port** | *1433*. Bemærk, at dette, deaktiveres, fordi denne regel bruger **Flydende IP-(returnerede direkte server)**.   |
| **Sende forespørgsler** | Brug navnet på den efterprøvning af af, du har oprettet for denne justering af belastning. |
| **Session vedvarenhed**  | **Ingen** | 
| **Inaktiv timeout (minutter)**  | *4* | 
| **Flydende IP-adresse (returnerede direkte server)**  | **Aktiveret** | 

 >[AZURE.NOTE] Du skal muligvis rulle ned på bladet at få vist alle indstillinger.

- Klik på **OK**. 

- Azure konfigurerer belastningsjustering regel. Nu er justering af belastning konfigureret til at dirigere trafik til SQL Server, der er vært lytteren for gruppen tilgængelighed. 

På dette tidspunkt har ressourcegruppen belastningsjustering, oprette forbindelse til begge SQL Server-computere. Justering af belastning indeholder også en IP-adresse for SQL Server AlwaysOn tilgængelighed gruppe lytteren, så enten maskine kan svare på anmodninger om grupperne tilgængelighed.

>[AZURE.NOTE] Hvis din SQL-servere er i to separate områder, skal du gentage trinnene i andet området. Hvert område kræver en justering af belastning. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurere klynge for at bruge Indlæs belastningsjusteringstjenesten IP-adresse 

Næste trin er at konfigurere lytteren på klyngen, og få lytteren online. For at opnå dette, skal du gøre følgende: 

1. Oprette tilgængelighed gruppe lytteren på sekundær klynge 

1. Få lytteren online

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. oprette tilgængelighed gruppe lytteren på sekundær klynge

I dette trin skal oprette du manuelt tilgængelighed gruppe lytteren i failoverklyngestyring og SQL Server Management Studio (SSMS).

- Bruge RDP til at oprette forbindelse til den Azure virtuelle maskine, der er vært den primære replika. 

- Åbn failoverklyngestyring.

- Vælg noden **netværk** , og noter netværksnavn klynge. Dette navn, der skal bruges i de `$ClusterNetworkName` variabelt i PowerShell-script.

- Udvid klyngenavnet, og klik derefter på **roller**.

- I ruden **roller** , skal du højreklikke på tilgængelighed gruppenavn og derefter vælge **Tilføj ressource** > **Klient adgangspunkt**.

- Oprette et navn til denne nye lytteren, i feltet **navn** og derefter to gange på **Næste** , og klik derefter på **Udfør**. Du skal ikke bringe ressourcen til online eller lytter på dette tidspunkt.

 >[AZURE.NOTE] Navnet på den nye lytteren er navnet på det netværk, der anvender programmer til at oprette forbindelse til databaser i gruppen SQL Server tilgængelighed.

- Klik på fanen **ressourcer** , og derefter udvide adgangspunkt klient, du lige har oprettet. Højreklik på IP-ressourcen, og klik på Egenskaber. Notér navnet på IP-adressen. Du vil bruge dette navn i den `$IPResourceName` variable i PowerShell-script.

- Klik på **Statiske IP-adresse** , og Angiv den statiske IP-adresse til den samme adresse, du brugte, da du angiver Indlæs belastningsjusteringstjenesten IP-adressen på portalen Azure under **IP-adresse** . Aktivere NetBIOS til denne adresse, og klik på **OK**. Gentag dette trin for hver IP-ressource, hvis din løsning strækker sig over flere Azure VNets. 

- Åbn en øgede PowerShell ISE på Klyngenoden, der er i øjeblikket vært den primære replika, og Indsæt følgende kommandoer i et nyt script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Opdater variablerne og køre PowerShell-scriptet for at konfigurere den IP-adresse og port til den nye lytter.

 >[AZURE.NOTE] Hvis dine SQL-servere er i separate områder, skal du køre PowerShell-scriptet to gange. Første gang bruger klynge netværksnavn klynge IP-ressourcenavnet, og Indlæs belastningsjusteringstjenesten IP-adresse fra den første ressourcegruppe. For anden gang bruge klynge netværksnavn klynge IP-ressourcenavnet, og Indlæs belastningsjusteringstjenesten IP-adresse fra den anden ressourcegruppe.

Klyngen har nu en tilgængelighed gruppe lytteren ressource.

## <a name="2-bring-the-listener-online"></a>2. sætter lytteren online

Med tilgængelighed gruppe lytteren ressourcen konfigureret, kan du tage lytteren online så programmer kan oprette forbindelse til databaser i gruppen tilgængelighed med lytteren.

- Gå tilbage til failoverklyngestyring. Udvid **roller** og derefter fremhæve din tilgængelighed gruppe. Højreklik på navnet for lytteren, og klik på **Egenskaber**under fanen **ressourcer** .

- Klik på fanen **afhængigheder** . Hvis der er flere ressourcer, der vises, skal du bekræfte, IP-adresser har eller ej og, afhængigheder. Klik på **OK**.

- Højreklik på navnet for lytteren, og klik på **Få Online**.


- Når lytteren er online, under fanen **ressourcer** , skal du højreklikke på gruppen tilgængelighed, og klik på **Egenskaber**.

- Oprette en afhængighed af lytteren navn ressource (ikke IP-adresse ressourcenavnet). Klik på **OK**.


- Start SQL Server Management Studio og oprette forbindelse til den primære replika.


- Gå til **AlwaysOn høj tilgængelighed** | **tilgængelighed grupper** | **tilgængelighed gruppe lyttere**. 


- Du bør nu se lytteren navnet, du har oprettet i failoverklyngestyring. Højreklik på navnet for lytteren, og klik på **Egenskaber**.


- Angiv portnummeret for tilgængelighed gruppe lytteren ved hjælp af $EndpointPort du tidligere har brugt i boksen **Port** (1433 var standard), klik derefter på **OK**.

Du har nu en SQL Server AlwaysOn tilgængelighed gruppe i Azure virtuelle maskiner, der kører i ressource manager tilstand. 

## <a name="test-the-connection-to-the-listener"></a>Teste forbindelsen til lytteren

At teste forbindelsen:

1. RDP til en SQL Server, der er i det samme virtuelle netværk, men ikke ejer replikaen. Det kan være andre SQL Server i klyngen.

1. Bruge **sqlcmd** til at teste forbindelsen. For eksempel opretter følgende script en **sqlcmd** forbindelse til den primære replika gennem lytteren med Windows-godkendelse:

        sqlcmd -S <listenerName> -E

SQLCMD forbindelsen Opret automatisk forbindelse til den primære replika vært for forekomsten af SQL Server. 

## <a name="guidelines-and-limitations"></a>Retningslinjer og begrænsninger

Bemærk følgende retningslinjer på tilgængelighed gruppe lytteren i Azure ved hjælp af interne justering af belastning:

- Kun én interne tilgængelighed gruppe lytteren understøttes per skybaseret tjeneste, fordi lytteren er konfigureret til at justering af belastning, og der er kun én interne justering af belastning. Men det er muligt at oprette multipe eksterne lyttere. 

- Justering af belastning, du kun åbne lytteren fra i det samme virtuelle netværk med en intern.
 
