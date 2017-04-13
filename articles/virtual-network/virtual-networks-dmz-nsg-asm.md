<properties
   pageTitle="Eksempel på DMZ – oprette en simpel DMZ med NSGs | Microsoft Azure"
   description="Oprette en DMZ med netværk sikkerhedsgrupper (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>Eksempel 1 – oprette en simpel DMZ med NSGs

[Gå tilbage til sikkerhed rammen bedste fremgangsmåder side][HOME]

I dette eksempel opretter en simpel DMZ med fire windows-servere og netværk sikkerhedsgrupper. Den fører også med hver af de relevante kommandoer, der giver en bedre forståelse af hvert enkelt trin. Der er også en trafik scenarie afsnit for at give en detaljeret trinvise hvordan trafik forløber gennem lag af beskyttelse i DMZ. Til sidst i referencer er sektion fuldført kode og instruktionerne til at oprette dette miljø for at teste og eksperimentere med forskellige scenarier. 

![Indgående DMZ med NSG][1]

## <a name="environment-description"></a>Beskrivelse af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- To cloud services: "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk og "CorpNetwork", med to undernet "FrontEnd" og "Back end"
- En sikkerhedsgruppe til netværk, der anvendes til begge undernet
- En Windows-Server, der repræsenterer en programserver web ("IIS01")
- To vinduer servere, der repræsenterer program tilbage afslutte servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

I afsnittet referencer under er der en PowerShell-script, der bygger mest muligt ud af det miljø, der er beskrevet ovenfor. Opbygning af FOS og virtuelle netværk, selvom udføres af eksempel på et scriptet, der ikke er beskrevet mere detaljeret i dette dokument. 

At opbygge miljøet

  1.    Gemme den netværk config XML-fil, der er inkluderet i afsnittet referencer (opdateret med navne, placering og IP-adresser, så det svarer til det angivne scenario)
  2.    Opdatere Brugervariabler i scriptet til at matche miljøet er scriptet til at blive kørt mod (abonnementer, tjenestenavne osv.)
  3.    Udførelse af script i PowerShell

**Bemærk**: det område, der er angivet i PowerShell-script skal passer til det område, der er angivet i netværk konfiguration XML-fil.

Når scriptet kører korrekt yderligere valgfrie trin kan blive ført, i afsnittet referencer er to scripts til at konfigurere webserver og app-server med et enkelt webprogram til at tillade test med denne DMZ-konfiguration.

De følgende afsnit give en detaljeret beskrivelse af netværk sikkerhedsgrupper, og hvordan de fungerer i dette eksempel ved gennemgang af vigtige linjer af PowerShell-script.

## <a name="network-security-groups-nsg"></a>Netværk sikkerhedsgrupper (NSG)
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med seks regler. 

>[AZURE.TIP] Generelt, skal du oprette bestemte "Tillad" regler først og derefter mere generisk "Afvis" reglerne sidst. De tildelte prioritet bestemmer, hvilke regler, der er evalueret første. Når trafik er fundet skal gælde for en bestemt regel, skal evalueres ingen yderligere regler. NSG regler kan anvende i enten i den indgående eller udgående retning (fra perspektivet undernettet).

Med en erklæring, er der opbygget følgende regler for indgående trafik:

1.  Interne DNS-trafik (port 53) er tilladt
2.  RDP trafik (port 3389) fra internettet til en hvilken som helst VM er tilladt
3.  HTTP-trafik (port 80) fra internettet til webserver (IIS01) er tilladt
4.  Al trafik (alle porte) fra IIS01 til AppVM1 er tilladt
5.  Al trafik (alle porte) fra internettet til hele VNet (begge undernet) nægtet
6.  Al trafik (alle porte) fra front end-undernet til back end-undernet er nægtet

Med disse regler, der er bundet til hvert undernet, hvis en HTTP-anmodningen blev indgående fra internettet til webserveren, begge regler 3 (Tillad) og 5 (Afvis) vil anvende, men fordi regel 3 har højere prioritet, kun den vil anvende og regel 5 vil ikke kommer ind i Afspil. HTTP-anmodningen vil herefter til webserveren. Hvis den samme trafik blev prøver at nå DNS01 serveren, regel 5 (Afvis), vil være først anvende trafikken vil ikke kunne overføre til serveren. Regel 6 (Afvis) blokerer front end-undernet fra taler til back end-undernet (med undtagelse af tilladte trafik i regler 1 og 4), dette beskytter back end-netværket i tilfælde en hacker kompromiser webprogrammet on Frontend, hackeren ville har begrænset adgang til back end-"beskyttet" netværket (kun til ressourcer, der vises på AppVM01 serveren).

Der findes en udgående regel, der tillader trafik ud på internettet. I dette eksempel skal er vi at tillade udgående trafik og ikke at ændre alle udgående regler. Hvis du vil låse trafik i begge retninger, er brugerdefineret Routing påkrævet, dette er set i "Eksempel 3" nedenfor.

Hver regel er beskrevet mere detaljeret således (**Note**: alle elementer i den nedenstående liste i, der begynder med et dollartegn (f.eks.: $NSGName) er en brugerdefineret variabel fra scriptet i afsnittet reference i dette dokument):

1. Først skal der oprettes en sikkerhedsgruppe netværk for at holde reglerne:

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  Den første regel i dette eksempel, så DNS-trafik mellem alle interne netværk til DNS-server på back end-undernet. Der er nogle vigtige parametre for reglen:
  - "Skriv" Angiver, i hvilken retning af trafik denne regel træder i kraft; Dette er fra perspektivet, undernet eller virtuelt (afhængigt af hvor denne NSG er bundet). Dermed Hvis Type er "Indgående" og trafik er at angive undernettet, reglen skal gælde og trafik fra undernettet berøres ikke af denne regel.
  - "Prioritet" Angiver den rækkefølge, hvori en trafikken skal evalueres. Jo lavere det nummer jo højere prioritet. Så snart en regel gælder for en bestemt trafikken, behandles ingen yderligere regler. Dermed Hvis en regel med prioritet 1 tillader trafik, og en regel med prioritet 2 afviser trafik, og begge regler gælder for trafik derefter trafikken ville have tilladelse til at flow (da regel 1 havde en højere prioritet det tog effekt og ingen yderligere regler blev anvendt).
  - "Handling" Angiver, hvis trafik, der påvirkes af denne regel er blokerede eller tilladte.

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  Denne regel, så RDP-trafik til flyde fra internettet til RDP porten på en hvilken som helst server på enten undernet i VNET. Denne regel benytter to specielle typer adressepræfikser; "VIRTUAL_NETWORK" og "INTERNET". Dette er en nem måde at adresse til en større kategori af adressepræfikser.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  Denne regel kan indgående internettrafik at ramme webserveren. Dette ændrer ikke routing funktionsmåden; Det kan kun trafik tiltænkt IIS01 til at videregive. Dermed Hvis trafik fra internettet havde webserveren som destinationen for denne regel vil tillade, at det og standse behandlingen af flere regler. (I reglen på prioritet 140 alle andre indgående internettrafik er blokeret). Hvis du kun behandler HTTP-trafik, kan være yderligere begrænset denne regel, at kun tillade Destination Port 80.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  Denne regel tillader trafik til at overføre fra serveren IIS01 til AppVM01 serveren, en nyere regel blokerer andre front end til back end-trafik. Til at forbedre denne regel, hvis porten kendes, der skal tilføjes. Eksempelvis hvis IIS-serveren er nå kun SQL Server på AppVM01, Port destinationsområde skal ændres fra "*" (alle) til 1433 (SQL-port), således at en mindre indgående angreb overflade på AppVM01 skal webprogrammet nogensinde være kompromitteret.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  Denne regel afviser trafik fra internettet til en hvilken som helst servere på netværket. Sammen med reglen på prioritet 110 og 120, giver mulighed for kun indgående internettrafik til firewallen og RDP porte til andre servere og dokumentkomponenter alt det andet. 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  Den endelige regel afviser trafik fra front end-undernet til back end-undernet. Da dette er en indgående kun regel, tillades modsat trafik (fra backend-version til Frontend).

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>Trafik scenarier
#### <a name="allowed-web-to-web-server"></a>(*Tilladt*) Web til webserver
1.  Internet bruger HTTP siden serviceanmodninger fra FrontEnd001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Skybaseret tjeneste gennemløb trafik via Åbn slutpunkt på port 80 mod IIS01 (webserver)
3.  Front end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet til IIS01) gælder, trafik er tilladt, standse regel behandling
4.  Trafik rammer interne IP-adressen på webserveren IIS01 (10.0.1.5)
5.  IIS01 lytter efter webtrafik, modtager mødeindkaldelsen og starter behandlingen af anmodningen
6.  IIS01 beder SQL Server på AppVM01 oplysninger
7.  Ingen udgående regler på front end-undernet trafik er tilladt
8.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet firewall) ikke anvende, flytte til næste regel
  4.    NSG regel 4 (IIS01 til AppVM01) gælder, trafik er tilladt, skal du stoppe regel behandling
9.  AppVM01 modtager SQL-forespørgslen og reagerer
10. Da der ikke er nogen udgående regler på back end-undernet er svaret tilladt
11. Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt.
12. IIS-serveren modtager SQL-svar og afsluttes HTTP-svaret, og der sendes til person
13. Fordi der er ingen udgående regler på front end-undernet svaret er tilladt, og brugeren Internet modtager den webside, der anmodes om.

#### <a name="allowed-rdp-to-backend"></a>(*Tilladt*) RDP til backend-version
1.  Server administrator på internettet, som RDP-session til AppVM01 på BackEnd001.CloudApp.Net:xxxxx, hvor xxxxx er vilkårligt tildelt portnummeret for RDP til AppVM01 (den tilknyttede port kan findes på Azure-portalen eller via PowerShell)
2.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) gælder, trafik er tilladt, standse regel behandling
3.  Standardregler i kraft med nogen udgående regler og returnerede trafik er tilladt
4.  RDP-session er aktiveret
5.  AppVM01 beder om brugernavn adgangskode

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(*Tilladt*) Web DNS-Server opslag på DNS-server
1.  Web Server, IIS01, behov et datafeed på www.data.gov, men skal løse adressen.
2.  Netværkskonfigurationen for listerne VNet DNS01 (10.0.2.4 på back end-undernet) som den primære DNS-server, IIS01 sender DNS-anmodningen til DNS01
3.  Ingen udgående regler på front end-undernet trafik er tilladt
4.  Back end-undernet begynder indgående regel behandling:
  1.     NSG regel 1 (DNS) gælder, trafik er tilladt, standse regel behandling
5.  DNS-server modtager anmodningen
6.  DNS-server har ikke den adresse, der er cachelagret og beder om en rod DNS-server på internettet
7.  Ingen udgående regler på back end-undernet trafik er tilladt
8.  Internet DNS-server reagerer, da denne session er startet internt, er svaret tilladt
9.  DNS-server cachelagres svaret og reagerer på den oprindelige anmodning tilbage til IIS01
10. Ingen udgående regler på back end-undernet trafik er tilladt
11. Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt
12. IIS01 modtager svaret fra DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Tilladt*) Web access serverfilen på AppVM01
1.  IIS01 beder om en fil på AppVM01
2.  Ingen udgående regler på front end-undernet trafik er tilladt
3.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet til IIS01) ikke anvende, flytte til næste regel
  4.    NSG regel 4 (IIS01 til AppVM01) gælder, trafik er tilladt, skal du stoppe regel behandling
4.  AppVM01 modtager anmodningen og reagerer med fil (hvis det er godkendt adgang)
5.  Da der ikke er nogen udgående regler på back end-undernet er svaret tilladt
6.  Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt.
7.  IIS-serveren modtager filen

#### <a name="denied-web-to-backend-server"></a>(*Nægtet*) Web til back end-serveren
1.  Internet-bruger forsøger at få adgang til en fil på AppVM01 via tjenesten BackEnd001.CloudApp.Net
2.  Da der ikke er nogen slutpunkter åben for filshare, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, blokerer NSG regel 5 (Internet til VNet) denne trafik

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(*Nægtet*) Web DNS-opslag på DNS-server
1.  Internet-bruger forsøger at slå en interne DNS-post på DNS01 i BackEnd001.CloudApp.Net-tjenesten
2.  Da der ikke er nogen slutpunkter åben for DNS, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, NSG regel 5 (Internet til VNet) blokerer denne trafik (Bemærk: denne regel 1 (DNS) vil ikke anvende af to årsager, først kilde-adresse er internettet, denne regel gælder kun for den lokale VNet som kilde, også dette er en regel for Tillad, så den aldrig vil nægte trafik)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Nægtet*) Web Access SQL gennem Firewall
1.  Internetbrugeren anmoder om SQL-data fra FrontEnd001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Da der ikke er nogen slutpunkter åben for SQL, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til firewallen
3.  Hvis slutpunkter var åbne eller anden grund, begynder front end-undernet indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet til IIS01) gælder, trafik er tilladt, standse regel behandling
4.  Trafik rammer interne IP-adressen på IIS01 (10.0.1.5)
5.  IIS01 lytte ikke på port 1433, så ingen svar på anmodningen

## <a name="conclusion"></a>Konklusion
Dette er en relativt simple og direkte videresende metode til at isolere back-end-undernet fra indgående trafik.

Du kan finde flere eksempler og en oversigt over netværk sikkerhedsgrænser [her][HOME].

## <a name="references"></a>Referencer
### <a name="main-script-and-network-config"></a>Primære Script og netværk Config
Gemme fuld scriptet i en PowerShell-script-fil. Du kan gemme Config netværk i en fil med navnet "NetworkConf1.xml".
Ændre de brugerdefinerede variabler efter behov. Køre scriptet og derefter følge instruktionerne til at Firewall regel konfiguration indeholdt i afsnittet Eksempel 1 ovenfor.

#### <a name="full-script"></a>Fuld Script
Dette script bliver, baseret på brugerdefinerede variabler;

1.  Oprette forbindelse til et Azure-abonnement
2.  Oprette en ny firmapost lagerplads
3.  Oprette en ny VNet og to undernet som defineret i netværk konfigurationsfil
4.  Opbygge 4 windows server FOS
5.  Konfigurer NSG, herunder:
  - Oprette en NSG
  - Udfylde med regler
  - Binder på NSG til de relevante undernet

Denne PowerShell-script skal køres lokalt på en internet forbindelse PC eller server.

>[AZURE.IMPORTANT] Når dette script køres, kan der være advarsler eller andre oplysninger, der pop i PowerShell. Kun fejlmeddelelser i rød er anledning.


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>Netværk konfigurationsfil
Gem denne XML-fil med opdaterede placering og tilføje linket til denne fil til $NetworkConfigFile variabel i scriptet ovenfor.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Eksempel på program-Scripts
Hvis du vil installere en Northwind til dette og andre DMZ eksempler, en har fået på følgende link: [Eksempel programmet Script][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Indgående DMZ med NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

