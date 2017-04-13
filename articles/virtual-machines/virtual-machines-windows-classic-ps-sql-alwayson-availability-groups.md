<properties
    pageTitle="Konfigurere altid på tilgængelighed gruppe i Azure VM med PowerShell"
    description="Dette selvstudium bruger ressourcer, der er oprettet med den klassiske implementeringsmodel og bruger PowerShell til at oprette en altid på tilgængelighed gruppe i Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Konfigurere altid på tilgængelighed gruppe i Azure VM med PowerShell

> [AZURE.SELECTOR]
- [Ressourcestyring: skabelon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Ressourcestyring: Manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Klassisk: Brugergrænsefladen](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Klassisk: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure virtuelle maskiner (VM'er) kan hjælpe databaseadministratorer af til at implementere lavere omkostninger for en høj tilgængelighed SQL Server-systemet. Dette selvstudium viser, hvordan du implementere en tilgængelighed gruppe ved hjælp af SQL Server altid på til en komplet i et Azure-miljø. I slutningen af selvstudiet, vil din SQL Server altid på løsning i Azure består af følgende elementer:

- Et virtuelt netværk, der indeholder flere undernet, herunder en front-end- og et back end-undernet

- Et domænenavn fra domænecontrolleren et domæne, Active Directory (AD)

- To SQL Server FOS udrulles til back end-undernet og tilsluttet AD-domæne

- En 3-node WSFC klynge med Node størstedelen quorum modellen

- En tilgængelighed gruppe med to synkron Anvend replikaer af en tilgængelighed-database

Dette scenarie vælges efter dens enkel, ikke til dens omkostningseffektivitet eller andre faktorer på Azure. For eksempel kan du minimere antallet af FOS for en gruppe med to gengivne tilgængelighed for at gemme på Beregn timer i Azure ved hjælp af domænecontrolleren som quorum fil del bekræftelse i en 2-node WSFC klynge. Denne metode reducerer VM antallet af en fra ovenstående konfigurationen.

Dette selvstudium er beregnet til at vise dig de trin, der kræves til at konfigurere løsningen beskrevet ovenfor uden udarbejdelse på detaljerne for hvert enkelt trin. I stedet for viser dig trinnene til konfiguration af grafiske brugergrænseflade, bruges derfor PowerShell scripting for at fører dig gennem hvert trin hurtigt. Antages det følgende:

- Du har allerede en Azure-konto til virtuelt abonnementet.

- Du har installeret [Azure PowerShell-cmdlet'er](../powershell-install-configure.md).

- Du har allerede en dækkende forståelse af altid på tilgængelighed-grupper for lokal løsninger. Du kan finde yderligere oplysninger finder [Altid på tilgængelighed grupper (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Oprette forbindelse til dit Azure-abonnement og oprette det virtuelle netværk

1. I et PowerShell-vindue på din lokale computer, importere Azure modulet, hente en publicering indstillingsfil til din computer og forbinde sessionen PowerShell til dit Azure abonnement ved at importere de hentede udgivelsesindstillinger.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Kommandoen **Get-AzurePublishgSettingsFile** genererer automatisk en management certifikat med Azure henter den til din computer. En browser åbnes automatisk, og du bliver bedt om at angive Microsoft-kontolegitimationsoplysninger for abonnementet Azure. Hentede **.publishsettings** filen indeholder alle de oplysninger, du skal bruge til at administrere din Azure abonnement. Når du har gemt denne fil til en lokal mappe, du importere den ved hjælp af kommandoen **Importér AzurePublishSettingsFile** .

    >[AZURE.NOTE] Filen publishsettings indeholder dine legitimationsoplysninger (ikke-kodede spidsbåndbredde), der bruges til at administrere dine Azure abonnementer og tjenester. Af sikkerhedsmæssige årsager til denne fil er at gemme den midlertidigt uden for din datakilde kataloger (for eksempel i mappen Libraries\Documents), og derefter slette den, når importen er færdig. En ondsindet bruger kan få adgang til filen publishsettings kan redigere, oprette og slette dine Azure tjenester.

1. Angive en serie af variabler, du vil bruge til at oprette din skyen IT-infrastruktur.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Vær opmærksom på følgende fremgangsmåder for at sikre, at dine kommandoer lykkes senere:

    - Variabler **$storageAccountName** og **$dcServiceName** skal være entydig, fordi de bruges til at identificere cloud storage konto og skyen serverens henholdsvis på internettet.

    - Navne, der er angivet for variabler **$affinityGroupName** og **$virtualNetworkName** er konfigureret i virtuelt netværk konfiguration dokumentet, du vil bruge senere.

    - **$sqlImageName** angiver opdaterede navnet på det VM billede, der indeholder SQL Server 2012 Service Pack 1 Enterprise Edition.

    - For enkel, **Contoso! 000** er den samme adgangskode, der bruges på hele det hele selvstudium.

1. Oprette en forbindelse gruppe.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Oprette et virtuelt netværk ved at importere en konfigurationsfil.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Af konfigurationsfilen indeholder følgende XML-dokument. Kort sagt den angiver et virtuelt netværk kaldet **ContosoNET** i gruppen forbindelse kaldet **ContosoAG**, og det har adresse mellemrum **10.10.0.0/16** og har to undernet, **10.10.1.0/24** og **10.10.2.0/24**, som er det forside undernet og igen undernet, henholdsvis. Forside undernet er, hvor du kan placere klientprogrammer som Microsoft SharePoint, og det tilbage undernet er, hvor du vil placere SQL Server FOS. Hvis du ændrer **$affinityGroupName** og **$virtualNetworkName** variablerne tidligere, skal du også ændre de tilsvarende navne nedenfor.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Oprette en lagerplads-konto, der er tilknyttet gruppen forbindelse, du har oprettet og angive den som kontoen for den aktuelle lagerplads i dit abonnement.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Oprette DC serveren i nye cloud-tjenesten og tilgængelighed sæt.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Denne serie af via kommandoer gøre følgende:

    - **Ny AzureVMConfig** opretter en VM konfiguration.

    - **Tilføj AzureProvisioningConfig** giver konfigurationsparametrene for en enkeltstående Windows server.

    - **Tilføj AzureDataDisk** tilføjer disken data, du vil bruge til lagring af Active Directory-data med cachelagring indstilling, der er indstillet til ingen.

    - **Ny AzureVM** opretter en ny tjeneste i skyen og opretter den nye Azure VM i den nye tjeneste i skyen.

1. Vent på den nye VM klargøres fuldt og hente remote desktop filen til din arbejdsmappe. Da den nye Azure VM tager lang tid at blive klargjort, mens gentage fortsat afstemning den nye VM, indtil den er klar til brug.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

DC serveren er nu klargjort korrekt. Derefter skal konfigurere du Active Directory-domænet på denne DC-server. Lad vinduet PowerShell åben på din lokale computer. Du skal bruge det igen senere til at oprette to SQL Server FOS.

## <a name="configure-the-domain-controller"></a>Konfigurere domænecontrolleren

1. Oprette forbindelse til DC serveren ved at åbne filen remote desktop. Bruge maskine administratorens brugernavn AzureAdmin og din adgangskode **Contoso! 000**, som du har angivet, når du opretter den nye VM.

1. Åbne et PowerShell-vindue i administratortilstand.

1. Kør følgende **DCPROMO. EXE** kommando for at konfigurere **corp.contoso.com** domænet, med data mapper på drev M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Når kommandoen er fuldført, genstarter VM automatisk.

1. Oprette forbindelse til DC serveren igen ved at åbne filen remote desktop. Denne gang skal logge på som **CORP\Administrator**.

1. Åbne et PowerShell-vindue i administratortilstand og importere Active Directory-PowerShell-modulet ved hjælp af følgende kommando:

        Import-Module ActiveDirectory

1. Kør følgende kommandoer for at tilføje tre brugere til domænet.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** bruges til at konfigurere alt i forbindelse med forekomster af tjenesten SQL Server, WSFC klynge og gruppen tilgængelighed. **CORP\SQLSvc1** og **CORP\SQLSvc2** bruges som SQL Server-tjenestekonti til to SQL Server FOS.

1. Derefter skal du køre følgende kommandoer for at give **CORP\Install** tilladelser til at oprette computerobjekter i domænet.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    GUID angivet ovenfor er GUID for objekttype computer. Kontoen **CORP\Install** skal have tilladelsen **Læse alle egenskaber** og **Oprette computerobjekter** for at oprette de aktive direkte objekter for WSFC klynge. Tilladelse til at **Læse alle egenskaber** er allerede får til CORP\Install som standard, så du ikke behøver at give den eksplicit. Finde flere oplysninger om tilladelser, der bruges til at oprette WSFC klynge [Failover klynge trinvis vejledning: konfigurere konti i Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Nu hvor du er færdig med konfiguration af Active Directory og user-objekter, du opretter to SQL Server FOS og deltage i dem til dette domæne.

## <a name="create-the-sql-server-vms"></a>Oprette SQL Server FOS

1. Fortsætte med at bruge PowerShell-vinduet, der er åben på din lokale computer. Definere følgende yderligere variabler:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP-adresse **10.10.0.4** tildeles typisk den første VM, du opretter i **10.10.0.0/16** undernettet af netværket Azure virtuelle. Du skal kontrollere dette er adressen på din DC server ved at køre **IPCONFIG**.

1. Kør følgende via kommandoer til at oprette den første VM i WSFC klynge, med navnet **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Bemærk følgende vedrørende kommandoen ovenfor:

    - **Ny AzureVMConfig** opretter en VM konfiguration med navnet på det ønskede tilgængelighed. Efterfølgende FOS oprettes med det samme tilgængelighed sætnavn, så de er sammenføjet til de samme tilgængelighed.

    - **Tilføj AzureProvisioningConfig** sammenkæder VM til det Active Directory-domæne, du har oprettet.

    - **Angiv AzureSubnet** placerer VM i Back-undernet.

    - **Ny AzureVM** opretter en ny tjeneste i skyen og opretter den nye Azure VM i den nye tjeneste i skyen. Parameteren **DnsSettings** angiver, at DNS-serveren for servere i den nye tjeneste i skyen har den IP-adresse **10.10.0.4**, som er IP-adressen på serveren, DC. Denne parameter er nødvendig for at aktivere de nye FOS i skytjenesten til at tilslutte til Active Directory-domænet korrekt. Uden denne parameter, skal du manuelt angive IPv4-indstillinger i din VM bruge DC server som den primære DNS-server, efter VM er klargjort og derefter Deltag VM til Active Directory-domæne.

1. Kør følgende via kommandoer til at oprette SQL Server FOS, med navnet **ContosoSQL1** og **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Bemærk følgende vedrørende kommandoerne ovenfor:

    - **Ny AzureVMConfig** bruger den samme tilgængelighed sætnavn som DC-serveren og bruger SQL Server 2012 Service Pack 1 Enterprise Edition billedet i galleriet virtuelt. Angives også operativsystem disken til læse-cachelagring kun (ingen cachelagring). Det anbefales, at du overfører databasefilerne til en separat datadisk, du vedhæfte til VM og konfigurere med ikke læse eller cachelagring. Der er dog næsten lige så godt at fjerne cachelagring på disken operativsystem, da du ikke kan fjerne læst cachelagring på disken operativsystem.

    - **Tilføj AzureProvisioningConfig** sammenkæder VM til det Active Directory-domæne, du har oprettet.

    - **Angiv AzureSubnet** placerer VM i Back-undernet.

    - **Tilføj AzureEndpoint** tilføjer access slutpunkter, så klientprogrammer kan få adgang til disse tjenester forekomster af SQL Server på internettet. Forskellige porte gives til ContosoSQL1 og ContosoSQL2.

    - **Ny AzureVM** opretter den nye SQL Server VM i samme skytjenesten som ContosoQuorum. Du skal markere FOS i samme skytjenesten Hvis du vil have dem til at være i det samme sæt tilgængelighed.

1. Vent på hver VM klargøres fuldt og hente dens remote desktop filer til din arbejdsmappe. Den til en løkke F4 gennem tre nye FOS og udfører kommandoer i de øverste krøllede kantede parenteser for hver af dem.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    SQL Server FOS er nu klargjort og køre, men de er installeret med SQL Server med standardindstillingerne.

## <a name="initialize-the-wsfc-cluster-vms"></a>Initialiseret WSFC klynge FOS

I dette afsnit skal du redigere de tre servere, du oftest bruger i WSFC klynge og installationen af SQL Server. Mere specifikt:

- (Alle servere) Du skal installere funktionen **Failoverklyngedannelse** .

- (Alle servere) Du skal tilføje **CORP\Install** som maskine **administrator**.

- (ContosoSQL1 og ContosoSQL2 kun) Du skal tilføje **CORP\Install** som en **systemadministrator** rolle i standarddatabasen.

- (ContosoSQL1 og ContosoSQL2 kun) Du skal tilføje **NT AUTHORITY\System** som et logon med følgende tilladelser:

    - Ændre en tilgængelighed gruppe

    - Oprette forbindelse SQL

    - Få vist server state

- (ContosoSQL1 og ContosoSQL2 kun) Af **TCP-** protokollen er allerede aktiveret på SQL Server VM. Men du stadig nødt til at åbne firewall til fjernadgang til SQL Server.

Nu er du klar til at starte. Begynder med **ContosoQuorum**, skal du følge nedenstående trin:

1. Oprette forbindelse til **ContosoQuorum** ved at starte remote desktop filerne. Bruge maskine administratorens brugernavn **AzureAdmin** og din adgangskode **Contoso! 000**, som du har angivet, når du opretter FOS.

1. Kontrollér, at computerne, der er blevet sluttet til **corp.contoso.com**.

1. Vent installationen af SQL Server til har kørt automatiseret initialisering opgaver før du fortsætter.

1. Åbne et PowerShell-vindue i administratortilstand.

1. Installere funktionen Windows Failoverklyngedannelse.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Tilføje **CORP\Install** som lokal administrator.

        net localgroup administrators "CORP\Install" /Add

1. Log af ContosoQuorum. Du er færdig med denne server nu.

        logoff.exe

Dernæst skal startes **ContosoSQL1** og **ContosoSQL2**. Følg nedenstående trin, som er identiske i begge af SQL Server FOS.

1. Oprette forbindelse til to SQL Server FOS ved at starte remote desktop filerne. Bruge maskine administratorens brugernavn **AzureAdmin** og din adgangskode **Contoso! 000**, som du har angivet, når du opretter FOS.

1. Kontrollér, at computerne, der er blevet sluttet til **corp.contoso.com**.

1. Vent installationen af SQL Server til har kørt automatiseret initialisering opgaver før du fortsætter.

1. Åbne et PowerShell-vindue i administratortilstand.

1. Installere funktionen Windows Failoverklyngedannelse.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Tilføje **CORP\Install** som lokal administrator

        net localgroup administrators "CORP\Install" /Add

1. Importere den SQL Server PowerShell-udbyder.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Tilføje **CORP\Install** som rollen systemadministrator til standard SQL Server-forekomsten.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Tilføje **NT AUTHORITY\System** som et logon med tre tilladelser, der er beskrevet ovenfor.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Åbn firewallen for fjernadgang til SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Log af begge FOS.

        logoff.exe

Til sidst skal er du klar til at konfigurere gruppen tilgængelighed. Du skal bruge SQL Server PowerShell-Provider til at udføre alle arbejdet på **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurere gruppen tilgængelighed

1. Oprette forbindelse til **ContosoSQL1** igen ved at starte remote desktop filerne. Log på med **CORP\Install**i stedet for at logge på ved hjælp af computerkontoen.

1. Åbne et PowerShell-vindue i administratortilstand.

1. Definere følgende variabler:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importere SQL Server PowerShell Provider.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Ændre SQL Server-servicekonto for ContosoSQL1 til CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Ændre SQL Server-servicekonto for ContosoSQL2 til CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Hente **CreateAzureFailoverCluster.ps1** fra [Oprette WSFC klynge for altid på tilgængelighed grupper i Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) til den lokale arbejdsmappe. Du skal bruge denne script til at oprette en funktionelle WSFC klynge. Vigtige oplysninger om, hvordan WSFC arbejde sammen med Azure netværket, under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-high-availability-dr.md).

1. Skifte til din arbejdsmappe og oprette WSFC klynge med hentede scriptet.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Aktivér altid på tilgængelighed-grupper til SQL Server standard forekomster på **ContosoSQL1** og **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Oprette en mappe til sikkerhedskopier og tildele tilladelser til SQL Server-tjenestekonti. Du skal bruge denne mappe til at forberede tilgængelighed databasen på den sekundære replika.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Oprette en database på **ContosoSQL1** kaldet **MyDB1**, tage både en fuld sikkerhedskopiering og sikkerhedskopiering af en og gendanne dem på **ContosoSQL2** med indstillingen **Med ingen genoprettelse** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Oprette tilgængeligheden gruppe slutpunkter på SQL Server FOS og angive de nødvendige tilladelser på slutpunkterne.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Oprette tilgængelighed replikaer.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Til sidst skal oprette gruppen tilgængelighed og deltage i den sekundære replika til gruppen tilgængelighed.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Næste trin
Du har nu korrekt implementeret SQL Server altid i ved at oprette en gruppe for tilgængelighed i Azure. For at konfigurere en lytter for denne tilgængelighed gruppe skal du se [konfigurere en ILB lytter for altid på tilgængelighed grupper i Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Du kan finde andre oplysninger om brug af SQL Server i Azure [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
