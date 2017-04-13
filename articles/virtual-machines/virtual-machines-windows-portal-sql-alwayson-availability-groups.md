<properties
    pageTitle="Konfigurere altid på tilgængelighed gruppe i Azure VM automatisk – ressourcestyring"
    description="Opret en altid på tilgængelighed gruppe med Azure virtuelle maskiner i Azure ressourcestyring tilstand. Dette selvstudium bruger primært brugergrænsefladen til automatisk for at oprette hele løsningen."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Konfigurere altid på tilgængelighed gruppe i Azure VM automatisk – ressourcestyring

> [AZURE.SELECTOR]
- [Ressourcestyring: skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Ressourcestyring: Manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Klassisk: Brugergrænsefladen](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Klassisk: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Selvstudiet til slut viser, hvordan du opretter en SQL Server tilgængelighed gruppe med Azure ressourcestyring virtuelle computere. Dette selvstudium bruger Azure blade til at konfigurere en skabelon. Du kan gennemse standardindstillingerne, Skriv nødvendige indstillinger, og Opdater blade i portalen, som du gennemgå dette selvstudium.

I slutningen af selvstudiet består SQL Server tilgængelighed gruppe-løsningen i Azure af følgende elementer:

- Et virtuelt netværk, der indeholder flere undernet, herunder en front-end- og et back end-undernet

- To domænenavn fra domænecontrolleren et domæne, Active Directory (AD)

- To SQL Server FOS udrulles til back end-undernet og tilsluttet AD-domæne

- En 3-node WSFC klynge med Node størstedelen quorum modellen

- En tilgængelighed gruppe med to synkron Anvend replikaer af en tilgængelighed-database

Figuren nedenfor er en grafisk repræsentation af løsningen.

![Teste øvelse arkitektur for AG i Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alle ressourcerne i denne løsning tilhører en enkelt ressourcegruppe.

Dette selvstudium antages det følgende:

- Du har allerede en Azure-konto. Hvis du ikke har en [tilmelde dig en prøveversion konto](http://azure.microsoft.com/pricing/free-trial/).

- Du ved allerede, hvordan du klargør en SQL Server VM fra galleriet virtuelt ved hjælp af den grafiske brugergrænseflade. Du kan finde flere oplysninger, se [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Du har allerede en dækkende forståelse af tilgængelighed grupper. Du kan finde yderligere oplysninger finder [altid på tilgængelighed grupper (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Hvis du er interesseret i at bruge tilgængelighed grupper med SharePoint, skal du også se [konfigurere SQL Server 2012 altid på tilgængelighed grupper til SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

I dette selvstudium skal du bruge Azure portalen for at:

- Vælg den altid på skabelon fra portalen

- Gennemse skabelonindstillinger og opdatere nogle konfigurationsindstillinger for dit miljø

- Overvåge Azure, som programmet opretter hele miljøet

- Oprette forbindelse til en af domænecontrollerne, og klik derefter på en af de SQL-servere

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Klargøre klynge fra galleriet

Azure indeholder et galleribillede af til hele løsningen. For at finde skabelonen:

1.  Log på portalen Azure ved hjælp af din konto.
1.  Klikke på Azure portalen på **+ Ny.** På portalen åbnes bladet nye.
1.  På siden ny blade Søg efter **AlwaysOn**.
![Finde AlwaysOn skabelon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Find **SQL Server AlwaysOn klynge**i søgeresultaterne.
![AlwaysOn skabelon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  **Vælg en implementeringsmodel** Vælg **Ressourcestyring**.

### <a name="basics"></a>Grundlæggende funktioner

Klik på **grundlæggende** og konfigurere følgende:

- **Administratorbrugernavn** er en brugerkonto med administratorrettigheder domæne og et medlem af rollen SQL Server systemadministrator faste serverroller på begge forekomster af SQL Server. Brug **domæneadministratorbrugernavn**af dette selvstudium.

- **Adgangskoden** er adgangskoden til den domæneadministratorkonto. Brug en kompleks adgangskode. Bekræft adgangskoden.

- **Abonnementet** er det abonnement, kan faktureres Azure til at køre alle de ressourcer, der er installeret for gruppen tilgængelighed. Hvis din konto har flere abonnementer, kan du angive et andet abonnement.

- **Ressourcegruppe** er navnet til gruppen, at alle de ressourcer, der Azure oprettet af dette selvstudium skal tilhøre. Brug **SQL-HA-indbyggede RG**af dette selvstudium. Du kan finde flere oplysninger, se (Azure ressourcestyring oversigt) [ressource-gruppe-overview.md / # ressource-grupper].

- **Placering** er det Azure område, hvor ressourcerne til dette selvstudium skal oprettes. Vælg en Azure område til at hoste infrastrukturen.

Nedenfor finder du hvad bladet **grundlæggende** ser sådan ud:

![Grundlæggende funktioner](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Klik på **OK**.

### <a name="domain-and-network-settings"></a>Indstillinger for domæne og netværk

Denne skabelon til Azure galleriet opretter et nyt domæne med nye domænecontrollere. Det opretter også et nyt netværk og to undernet. Skabelonen aktiverer ikke oprettelse af servere i et eksisterende domæne eller virtuelt netværk. Næste trin er at konfigurere indstillingerne for domæne og netværk.

Gennemse de forudindstillede værdier for indstillingerne for domæne og netværk på **domæne og netværksindstillinger** blade:

- **Skov rod domænenavn** er det domænenavn, der skal bruges til det AD-domæne, der er vært for klyngen. Brug **contoso.com**i dette selvstudium.

- **Virtuel netværksnavn** er netværksnavn til Azure virtuelt netværk. Brug **autohaVNET**til dette selvstudium.

- **Domænecontrolleren undernet navn** er navnet på en del af det virtuelle netværk, der er vært domænecontrolleren. I dette selvstudium Brug **undernet-1**. Dette undernet, der anvender adresse præfiks **10.0.0.0/24**.

- **Navnet på SQL Server undernet** er navnet på en del af det virtuelle netværk, hosts de SQL-servere og filen dele til bekræftelse. I dette selvstudium Brug **undernet-2**. Dette undernet, der anvender adresse præfiks **10.0.1.0/26**.

Vil vide mere om virtuelle netværk i [Azure se oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md).  

**Domæne og netværksindstillinger** ser sådan ud:

![Indstillinger for domæne og netværk](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Hvis det er nødvendigt, kan du ændre disse værdier. I dette selvstudium bruge vi foruddefinerede værdier.

- Gennemse indstillingerne, og klik på **OK**.

###<a name="availability-group-settings"></a>indstillinger for tilgængelighed

Gennemse de foruddefinerede værdier for gruppen tilgængelighed og lytteren på **tilgængelighed gruppeindstillinger** .

- **Tilgængelighed gruppenavn** er grupperet ressourcenavnet for gruppen tilgængelighed. Brug **Contoso-ag**af dette selvstudium.

- **tilgængelighed gruppenavn lytteren** bruges af klyngen og den interne justering af belastning. Klienter opretter forbindelse til SQL Server kan bruge dette navn til at oprette forbindelse til de relevante kopi af databasen. Brug **Contoso-lytteren**til dette selvstudium.

-  **tilgængelighed gruppe lytteren port** Angiver TCP-porten SQL Server lytteren bruger. Dette selvstudium for brug standardporten, **1433**.

Hvis det er nødvendigt, kan du ændre disse værdier. Bruge foruddefinerede værdier for dette selvstudium.  

![indstillinger for tilgængelighed](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Klik på **OK**.

###<a name="vm-size-storage-settings"></a>Indstillinger for lagring af VM størrelse

Vælg en SQL Server virtuelt størrelse på **VM størrelse, indstillinger for lagring af** og gennemse de andre indstillinger.

- **SQL Server virtuelt størrelse** er størrelsen på Azure virtuelt til begge SQL-servere. Vælg en passende for din arbejdsbyrde virtuelt størrelse. Hvis du bygger Brug denne miljø til selvstudiet **DS2**. Til fremstilling arbejdsbelastninger, skal du vælge et virtuelt størrelse, der kan understøtte arbejdsbelastningen. Mange fremstilling arbejdsbelastninger kræver **DS4** eller større. Skabelonen skal oprette to virtuelle maskiner af denne størrelse og installere SQL Server på hver enkelt. Du kan finde yderligere oplysninger finder [størrelser til virtuelle computere](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure installeres Enterprise Edition af SQL Server. Omkostninger, afhænger af udgaven og virtuelt størrelse. Se [virtuelle maskiner priser](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql)kan finde detaljerede oplysninger om aktuelle omkostninger.

- **Domænecontroller virtuelt størrelse** er virtuelt størrelse ved domæne enheder. Dette selvstudium for brug **D2**.

- **Dele filer til bekræftelse virtuelt størrelse** er størrelsen på virtuelt til fil del bekræftelse. Dette selvstudium for brug **A1**.

- **SQL-lagerplads konto** er navnet på kontoen lagerplads til SQL Server-data og operativsystem diske. Brug **alwaysonsql01**til dette selvstudium.

- **DC lagerplads konto** er navnet på kontoen lagerplads ved domæne enheder. Brug **alwaysondc01**til dette selvstudium.

- **SQL Server-data disk størrelse** i TB er størrelsen af SQL Server data disken i TB. Angiv et tal fra 1 til 4. Dette er størrelsen på disken data, der knyttes til hver SQL Server. Dette selvstudium brug af **1**.

- **Optimering af lagerplads** angiver bestemte lagerplads konfigurationsindstillinger for SQL Server virtuelle maskiner baseret på typen arbejdsbyrde. Alle SQL-servere i dette scenarie bruge premium lagerplads med Azure disk host cache angivet som skrivebeskyttet. Du kan desuden optimere SQL Server-indstillingerne for arbejdsbelastningen ved at vælge en af disse tre indstillinger:

    - **Generelle arbejdsbelastningen** angiver ingen bestemte konfigurationsindstillinger

    - **Behandling af transaktion** angiver sporingsflag 1117 og 1118

    - **Datalagring** angiver sporingsflag 1117 og 610

Brug **generelle arbejdsbelastningen**for dette selvstudium.

![Indstillinger for lagring af VM størrelse](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Gennemse indstillingerne, og klik på **OK**.

####<a name="a-note-about-storage"></a>En bemærkning om lagerplads

Yderligere optimeringer, afhænger af størrelsen på SQL Server data diskene. For hver terabyte data disken tilføjer Azure en ekstra 1 TB premium lagerplads (SSD). Når en server kræver 2 TB eller mere, oprettes skabelonen en lagerpulje på hver SQL Server. En lagerpulje er en slags lagerplads virtualization, hvor flere cd'er er konfigureret til at levere højere kapacitet, fleksibilitet og ydeevne.  Skabelonen derefter opretter en lagerplads på lagerpulje og viser det som en enkelt data til operativsystemet. Skabelonen angiver disken disken data til SQL Server. Skabelonen justerer lagerpulje til SQL Server med følgende indstillinger:

- Stripestørrelse er indstillingen interleave for den virtuelle disk. Transaktions arbejdsbelastninger er dette indstillet til 64 KB. Til data opbevaring arbejdsbelastninger, som er indstillingen 256 KB.

- Det er let at fleksibilitet (ingen fleksibilitet).

>[AZURE.NOTE] Azure premium lager er lokalt overflødige og bevarer tre kopier af dataene i et enkelt område, så yderligere fleksibilitet på lagerpulje ikke er påkrævet.

- AntalKolonner er lig med antallet af diske i lagerpulje.

Yderligere oplysninger om lagerplads i: mellemrum og lager grupper

- [Oversigt over lagerplads mellemrum](http://technet.microsoft.com/library/hh831739.aspx).

- [Windows Server-sikkerhedskopiering og lagerplads grupper](http://technet.microsoft.com/library/dn390929.aspx)

Du kan finde flere oplysninger om bedste fremgangsmåder for SQL Server configuration i [ydeevne bedste fremgangsmåder til SQL Server på Azure virtuelle computere](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Indstillinger for SQL Server

Gennemse og ændre præfikset SQL Server VM navn, SQL Server-version, SQL Server service-konto og adgangskode og SQL automatisk rettelse af tidsplan for vedligeholdelse på **SQL Server-indstillingerne** .

- **SQL Server navn præfiks** bruges til at oprette et navn for hver SQL Server. Brug **Contoso-ag**af dette selvstudium. Navnene på SQL Server kan *Contoso-ag-0* og *Contoso-ag-1*.

- **SQL Server-version** er versionen af SQL Server. Dette selvstudium for brug af **SQL Server-2014**. Du kan også vælge **SQL Server 2012** eller **SQL Server 2016**.

- **SQL Server service kontobrugernavn** er domænenavnet for tjenesten SQL Server-konto. Brug **sqlservice**til dette selvstudium.

- **Adgangskoden** er den til SQL Server service-kontoen.  Brug en kompleks adgangskode. Bekræft adgangskoden.

- **SQL automatisk rettelse tidsplan for vedligeholdelse** identificerer den ugedag, Azure automatisk programrettelse de SQL-servere. Skriv **søndag**for dette selvstudium.

- Tidspunktet på dagen for det område, Azure er **SQL automatisk rettelse af vedligeholdelse start time** , når automatisk rettelse begynder.

>[AZURE.NOTE]Vinduet rettelse for hver VM er forskudt ved en time. Kun én virtuelt er installeret for at udbedre ad gangen for at undgå afbrydelser af tjenesterne.

![Indstillinger for SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Gennemse indstillingerne, og klik på **OK**.

###<a name="summary"></a>Oversigt

På siden Oversigt kontrollerer Azure indstillingerne. Du kan også hente skabelonen. Gennemse oversigten. Klik på **OK**.

###<a name="buy"></a>Køb

Denne endelige blade indeholder **vilkår for anvendelse**og **beskyttelse af personlige oplysninger**. Gennemse oplysningerne. Når du er klar til at starte oprettelsen af virtuelle maskiner, og alle de andre påkrævede ressourcer til gruppen tilgængelighed Azure, kan du klikke på **Opret**.

Portalen Azure opretter ressourcegruppen og alle ressourcerne.

##<a name="monitor-deployment"></a>Overvåge installation

Overvåge forløbet installation fra Azure-portalen. Et ikon, der repræsenterer installationen er automatisk fastgjort til Azure portalen dashboard.

![Azure Dashboard](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Oprette forbindelse til SQL Server

De nye forekomster af SQL Server, der kører på virtuelle maskiner, der ikke har forbindelse til internettet. Domænecontrollerne har dog en via forbindelse Internettet. For at oprette forbindelse til SQL-serverne med Fjernskrivebord, første RDP til en af domænecontrollerne. Åbn en anden RDP til SQL Server fra domænecontrolleren.

Til RDP til primary domain controller, skal du følge disse trin:

1.  Fra Azure portalen dashboardet for meget, at installationen er fuldført.

1.  Klik på **ressourcer**.

1.  Klik på **ad-primær-dc** , som er navnet på den virtuelle maskine til primære domænenavn fra domænecontrolleren i bladet **ressourcer** .

1.  Klik på **Opret forbindelse**bladet for **ad-primær-dc** . Din browser spørger, om du vil åbne eller gemme remote connection-objektet. Klik på **Åbn**.
![Oprette forbindelse til DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Forbindelse til Fjernskrivebord** kan advare dig, at udgiveren af denne remote forbindelse ikke kan identificeres. Klik på **Opret forbindelse**.

1.  Windows-sikkerhed beder dig om at angive dine logonoplysninger for at oprette forbindelse til IP-adressen på den primære domænenavn fra domænecontrolleren. Klik på **Brug en anden konto**. Skriv **contoso\DomainAdmin**for **brugernavn** . Dette er den konto, du har valgt til administratorbrugernavn. Brug den komplekse adgangskode, du valgte, da du konfigurerede skabelonen.

1.  **Fjernskrivebord** kan advare dig, at fjerncomputeren ikke kunne godkendes på grund af problemer med dens sikkerhedscertifikat. Det viser dig navnet på sikkerhed certifikatet. Hvis du har fulgt selvstudiet bliver navnet **ad-primær-dc.contoso.com**. Klik på **Ja**.

Du har nu forbindelse til den primære domænenavn fra domænecontrolleren. Til RDP til SQL Server, skal du følge disse trin:

1.  Åbn **Fjernskrivebord**på domænecontrolleren.

1.  **Computer**, skal du skrive navnet på en af de SQL-servere. Dette selvstudium skal du skrive **SQL Server-0**.

1.  Brug den samme brugerkonto og den adgangskode, du brugte til at RDP domænecontrolleren.

Du har nu forbindelse til RDP til SQL Server. Du kan åbne SQL Server management studio, oprette forbindelse til standardforekomsten af SQL Server og kontrollere gruppen availabilty er konfigureret.


