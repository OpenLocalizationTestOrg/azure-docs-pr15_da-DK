<properties 
    pageTitle="LOB programmet testmiljø | Microsoft Azure" 
    description="Lær at oprette en webbaseret, linje med forretningsprogram i et hybridt skybaseret miljø til IT pro- eller udvikling test." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Konfigurere en webbaseret LOB program i en hybrid sky til test

Dette emne vejleder dig gennem oprettelsen af et simuleret skyen hybridmiljø til test en webbaseret linje med (LOB) forretningsprogram Microsoft Azure som vært. Her er den resulterende konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Denne konfiguration består af:

- En simuleret lokale netværk Azure (TestLab VNet) som vært.
- Et kryds lokale virtuelt netværk Azure (TestVNET) som vært.
- En VNet-VNet VPN-forbindelse.
- En webbaseret LOB server, SQL server og sekundær domænenavn fra domænecontrolleren i det virtuelle TestVNET netværk.

Denne konfiguration indeholder en basis og almindelige udgangspunktet, kan du:

- Udvikle og teste LOB-programmer, der er hostet på Internet Information Services (IIS) med en SQL Server 2014 database back-end i Azure.
- Udføre tests af denne simuleret hybrid skybaseret IT-arbejdsbyrde.

Der er tre store faser til konfiguration af hybrid skyen testmiljø:

1.  Konfigurere skyen simuleret hybridmiljø.
2.  Konfigurere SQL server-computeren (SQL1).
3.  Konfigurere LOB serveren (LOB1).

Denne arbejdsbelastningen kræver et Azure-abonnement. Hvis du har et abonnement til MSDN eller Visual Studio, skal du se [månedlige Azure kredit for Visual Studio abonnenter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Du kan finde et eksempel på en fremstilling LOB program som vært Azure, **Line of business-applikationer** arkitektur grundrids på [Microsoft Software netværksarkitekturdiagrammer og perspektivplaner](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Konfigurere skyen simuleret hybridmiljø

Oprette [simuleret hybrid skyen testmiljø](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Da denne testmiljø ikke kræver tilstedeværelsen af APP1 serveren på Corpnet undernet, kan du lukke det nu.

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Konfigurere SQL server-computeren (SQL1)

Start DC2 computeren fra Azure-portalen, hvis det er nødvendigt.

Opret derefter en virtuel maskine til SQL1 med disse kommandoer på en Azure PowerShell kommandoprompten på din lokale computer. Før du kører disse kommandoer, Udfyld de variable værdier, og fjern den < og > tegn.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Bruge Azure portal til at oprette forbindelse til SQL1 ved hjælp af den lokale administratorkonto af SQL1.

Konfigurer Windows firewallregler for at give test af grundlæggende forbindelser og SQL Server-trafik. Kør følgende kommandoer fra en administrator niveau Windows PowerShell kommandoprompten på SQL1.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Kommandoen ping bør medføre fire vellykket svar fra IP-adresse 192.168.0.4.

Dernæst skal du tilføje ekstra data disken på SQL1 som en ny enhed med bogstavet F:.

1.  Klik på **filer og lagerplads tjenester**i venstre rude af Server Manager, og klik derefter på **diske**.
2.  Klik på **disk 2** (med **Partition** indstillet til **Ukendt**) i indholdsruden i gruppen **diske** .
3.  Klik på **opgaver**, og klik derefter på **Ny lydstyrken**.
4.  I afsnittet før du begynder side i guiden Ny lydstyrken, klik på **Næste**.
5.  Klik på **Disk 2**, på siden Vælg server og disk, og klik derefter på **Næste**. Når du bliver bedt om det, skal du klikke på **OK**.
6.  Klik på **Næste**på siden Angiv størrelsen på siden lydstyrken.
7.  Klik på **Næste**på siden Tildel til en side, drev bogstav eller en mappe.
8.  Klik på **Næste**på siden Vælg arkiv system indstillinger.
9.  Klik på **Opret**på siden Bekræft valg.
10. Klik på **Luk**, når du er færdig.

Kør følgende kommandoer i kommandoprompten Windows PowerShell på SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Dernæst skal deltage i SQL1 CORP Windows Server Active Directory-domænet med disse kommandoer på Windows PowerShell-prompt på SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Anvend kontoen CORP\User1, når du bliver bedt om at angive domæne kontolegitimationsoplysninger for kommandoen **Tilføj-Computer** .

Genstart computeren, kan du bruge Azure-portalen skal oprettes forbindelse til SQL1 *med den lokale administratorkonto af SQL1*.

Dernæst skal konfigurere SQL Server-2014 for at bruge F: drev for nye databaser og til konto brugertilladelser.

1.  Skriv **SQL Server Management**fra startskærmen, og klik derefter på **SQL Server 2014 Management Studio**.
2.  Klik på **Opret forbindelse**i **Opret forbindelse til serveren**.
3.  I ruden med træstrukturen Object Explorer, højreklik på **SQL1**, og klik derefter på **Egenskaber**.
4.  Klik på **Indstillinger for**i vinduet **Egenskaber for Server** .
5.  Find **Database standardplaceringer** og angive disse værdier: 
    - **Data**skal du skrive stien **f:\Data**.
    - Skriv stien **f:\Log**for **logfiler**.
    - Skriv stien **f:\Backup**til **sikkerhedskopi**.
    - Bemærk: Kun nye databaser bruge disse placeringer.
6.  Klik på **OK** for at lukke vinduet.
7.  Åbn **sikkerhed**i ruden med træstrukturen **Object Explorer** .
8.  Højreklik på **logon** , og klik derefter på **Nyt logon**.
9.  Skriv **CORP\User1**i **Login-navn**.
10. Klik på **systemadministrator**på siden **Serverroller** , og klik derefter på **OK**.
11. Luk Microsoft SQL Server Management Studio.

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Konfigurere LOB server (LOB1)

Opret først et virtuelt for LOB1 med disse kommandoer Azure PowerShell kommandoprompten på din lokale computer.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Derefter Brug portalen Azure til at oprette forbindelse til LOB1 med legitimationsoplysningerne for den lokale administratorkonto af LOB1.

Konfigurer en Windows Firewall regel for at tillade trafik til grundlæggende forbindelser test. Kør følgende kommandoer fra en administrator niveau Windows PowerShell kommandoprompten på LOB1.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

Kommandoen ping bør medføre fire vellykket svar fra IP-adresse 192.168.0.4.

Dernæst skal deltage i LOB1 CORP Active Directory-domænet med disse kommandoer på Windows PowerShell-prompt.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Anvend kontoen CORP\User1, når du bliver bedt om at angive domæne kontolegitimationsoplysninger for kommandoen **Tilføj-Computer** .

Genstart computeren, kan du bruge Azure-portalen til at oprette forbindelse til LOB1 med CORP\User1 konto og adgangskode.

Derefter konfigurerer LOB1 til IIS og teste adgangen fra KLIENT1.

1.  Fra Server Manager, skal du klikke på **Tilføj roller og funktioner**.
2.  Klik på **Næste**på siden **Oversigt** .
3.  Klik på **Næste**på siden **Vælg installationstype** .
4.  Klik på **Næste**på siden **Vælg destinations-serveren** .
5.  Klik på **Web Server (IIS)** på listen over **roller**på siden **serverroller** .
6.  Når du bliver bedt om det, skal du klikke på **Tilføj funktioner**, og klik derefter på **Næste**.
7.  Klik på **Næste**på siden **Vælg funktioner** .
8.  Klik på **Næste**på siden **Web Server (IIS)** .
9.  Markér eller fjern markeringen i afkrydsningsfelterne for de tjenester, du har brug for til test dit LOB program, og klik derefter på **Næste**på siden **Vælg rolletjenester** .
10. Klik på **Installer**på siden **Bekræft installationsindstillinger** .
11. Vent, indtil installationen af komponenter er afsluttet, og klik derefter på **Luk**.
12. Oprette forbindelse til KLIENT1 computeren med kontolegitimationsoplysninger CORP\User1 fra portalen Azure, og start derefter Internet Explorer.
13. Skriv **http://lob1/** i adresselinjen, og tryk derefter på ENTER. Du bør se websiden standard IIS 8.

Dette er den aktuelle konfiguration.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Dette miljø er nu klar til at implementere din webbaseret program på LOB1 og teste funktionalitet fra KLIENT1 på Corpnet undernet.

## <a name="next-step"></a>Næste trin

- Tilføje en ny virtuel maskine ved hjælp af [Azure-portalen](virtual-machines-windows-hero-tutorial.md).
