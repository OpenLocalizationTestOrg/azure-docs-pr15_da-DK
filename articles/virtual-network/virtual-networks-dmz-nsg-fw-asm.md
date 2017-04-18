<properties
   pageTitle="Eksempel på DMZ – oprette en DMZ at beskytte programmer med en Firewall og NSGs | Microsoft Azure"
   description="Oprette en DMZ med en Firewall og netværk sikkerhedsgrupper (NSG)"
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

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Eksempel 2 – oprette en DMZ at beskytte programmer med en Firewall og NSGs

[Gå tilbage til sikkerhed rammen bedste fremgangsmåder side][HOME]

I dette eksempel opretter en DMZ med en firewall, fire windows-servere og netværk sikkerhedsgrupper. Den fører også med hver af de relevante kommandoer, der giver en bedre forståelse af hvert enkelt trin. Der er også en trafik scenarie afsnit for at give en detaljeret trinvise hvordan trafik forløber gennem lag af beskyttelse i DMZ. Til sidst i referencer er sektion fuldført kode og instruktionerne til at oprette dette miljø for at teste og eksperimentere med forskellige scenarier. 

![Indgående DMZ med for og NSG][1]

## <a name="environment-description"></a>Beskrivelse af miljø
I dette eksempel er der et abonnement, der indeholder følgende:

- To cloud services: "FrontEnd001" og "BackEnd001"
- Et virtuelt netværk "CorpNetwork", med to undernet: "FrontEnd" og "Back end"
- En enkelt netværk sikkerhedsgruppe, der anvendes til begge undernet
- Et netværk virtuelle produktet i dette eksempel Barracuda NextGen Firewall, tilsluttet til front end-undernet
- En Windows-Server, der repræsenterer en programserver web ("IIS01")
- To vinduer servere, der repræsenterer program igen afslutte servere ("AppVM01", "AppVM02")
- En Windows-server, der repræsenterer en DNS-server ("DNS01")

>[AZURE.NOTE] Selvom i dette eksempel bruges en Barracuda NextGen Firewall, kan mange af de forskellige netværk virtuelle hjælpemidler bruges i dette eksempel.

I afsnittet referencer under er der en PowerShell-script, der bygger mest muligt ud af det miljø, der er beskrevet ovenfor. Opbygning af FOS og virtuelle netværk, selvom udføres af eksempel på et scriptet, der ikke er beskrevet mere detaljeret i dette dokument.
 
Sådan oprettes miljøet:

  1.    Gemme den netværk config XML-fil, der er inkluderet i afsnittet referencer (opdateret med navne, placering og IP-adresser, så det svarer til det angivne scenario)
  2.    Opdatere Brugervariabler i scriptet til at matche miljøet er scriptet til at blive kørt mod (abonnementer, tjenestenavne osv.)
  3.    Udførelse af script i PowerShell

**Bemærk**: det område, der er angivet i PowerShell-script skal passer til det område, der er angivet i netværk konfiguration XML-fil.

Når scriptet kører tages efter script følgende trin:

1.  Konfigurere firewallreglerne, er dette dækket i afsnittet nedenfor: firewallregler.
2.  Du kan også er to scripts til at konfigurere webserver og app-server med et enkelt webprogram til at tillade test med denne DMZ konfiguration i sektionen referencer.

I næste afsnit forklares de fleste af sætningerne scripts i forhold til netværk sikkerhedsgrupper.

## <a name="network-security-groups-nsg"></a>Netværk sikkerhedsgrupper (NSG)
I dette eksempel er en NSG gruppe indbygget og derefter indlæst med seks regler. 

>[AZURE.TIP] Generelt, skal du oprette bestemte "Tillad" regler først og derefter mere generisk "Afvis" reglerne sidst. De tildelte prioritet bestemmer, hvilke regler, der er evalueret første. Når trafik er fundet skal gælde for en bestemt regel, skal evalueres ingen yderligere regler. NSG regler kan anvende i enten i den indgående eller udgående retning (fra perspektivet undernettet).

Med en erklæring, er der opbygget følgende regler for indgående trafik:

1.  Interne DNS-trafik (port 53) er tilladt
2.  RDP trafik (port 3389) fra internettet til en hvilken som helst VM er tilladt
3.  HTTP-trafik (port 80) fra internettet til for (firewall) er tilladt
4.  Al trafik (alle porte) fra IIS01 til AppVM1 er tilladt
5.  Al trafik (alle porte) fra internettet til hele VNet (begge undernet) nægtet
6.  Al trafik (alle porte) fra front end-undernet til back end-undernet er nægtet

Med disse regler, der er bundet til hvert undernet, hvis en HTTP-anmodningen blev indgående fra internettet til webserveren, begge regler 3 (Tillad) og 5 (Afvis) vil anvende, men fordi regel 3 har højere prioritet, kun den vil anvende og regel 5 vil ikke kommer ind i Afspil. HTTP-anmodningen vil herefter firewallen. Hvis den samme trafik blev prøver at nå DNS01 serveren, regel 5 (Afvis), vil være først anvende trafikken vil ikke kunne overføre til serveren. Regel 6 (Afvis) blokerer front end-undernet fra taler til back end-undernet (med undtagelse af tilladte trafik i regler 1 og 4), dette beskytter back end-netværket i tilfælde en hacker kompromiser webprogrammet on Frontend, hackeren ville har begrænset adgang til back end-"beskyttet" netværket (kun til ressourcer, der vises på AppVM01 serveren).

Der findes en udgående regel, der tillader trafik ud på internettet. I dette eksempel skal er vi at tillade udgående trafik og ikke at ændre alle udgående regler. Du låser trafik i begge retninger, brugerdefineret distributionen er påkrævet, dette er set i et andet eksempel, der kan findes i det [primære sikkerhed rammen dokument][HOME].

Ovenstående behandles NSG reglerne ligner meget de NSG regler i [Eksempel 1 – oprette en simpel DMZ med NSGs][Example1]. Gennemgå NSG beskrivelsen i dette dokument til et nærmere Kig på hver NSG regel og det er attributter.

## <a name="firewall-rules"></a>Firewallregler
En management-klienten skal være installeret på en PC til at administrere firewallen og oprette de konfigurationer, det er nødvendigt. Se dokumentationen fra din firewall (eller andre for) leverandør om til styring af enheden. Resten af dette afsnit beskriver konfigurationen af sig selv firewallen gennem leverandører management-klienten (det vil sige ikke Azure portal eller PowerShell).

Vejledning til klienten download og oprette forbindelse til Barracuda i dette eksempel kan findes her: [Barracuda viser administrator](https://techlib.barracuda.com/NG61/NGAdmin)

På firewallen skal regler for videresendelse oprettes. Da dette eksempel omdirigerer kun internettrafik i bundet til firewallen og derefter til webserveren, bruges kun én videresendelse NAT-regel. Barracuda NextGen Firewall i dette eksempel bruges til skal reglen være en Destination NAT regel ("sommertid NAT") til at videregive denne trafik.

For at oprette følgende regel (eller bekræfte eksisterende standardregler), begyndende fra Barracuda viser klient administratordashboard, gå til fanen konfiguration, afsnit Klik på regelsæt i funktionel konfiguration. Et gitter der hedder "Main regler" viser de eksisterende aktive og deaktiverede regler på firewallen. I øverste højre hjørne af dette gitter er en lille, grøn "+" knappen, Klik her for at oprette en ny regel (Bemærk: din firewall muligvis "låst" for ændringer, hvis du får vist en knap, der er markeret "Låse", og du kan ikke oprette eller redigere regler, skal du klikke på denne knap for at "låse" på regelsæt og Tillad redigering). Hvis du vil redigere en eksisterende regel, vælg reglen, højreklik, og vælg Rediger regel.

Oprette en ny regel, og Angiv et navn, som "WebTraffic". 

Ikonet Destination NAT regel ser sådan ud: ![Destination NAT-ikon][2]

Selve reglen ser nogenlunde således ud:

![Firewallregel][3]

Her en indgående adresse, som rammer firewallen forsøger at få HTTP (port 80 eller 443 til HTTPS) sendes ud af firewallen "DHCP1 lokal IP-" grænseflade og omdirigeret til webserveren med 10.0.1.5 IP-adresse. Da trafikken kommer på port 80 og gå til webserveren på port 80 blev der kræves ingen ændring af port. Dog kunne mållisten har været 10.0.1.5:8080 Hvis vores webserver lyttet på port 8080 dermed oversætte den indgående port 80 firewallen til indgående port 8080 på webserveren.

En forbindelsesmetode bør også være angivet, til Destination reglen fra internettet, "Dynamisk SNAT" er mest passende. 

Det er vigtigt, at dens prioriteten er indstillet korrekt, selvom kun én regel er blevet oprettet. Hvis i gitteret i alle regler for firewall til denne nye regel er nederst (under "BLOCKALL" reglen) kommer det aldrig på Afspil. Sikre, at nyoprettede reglen for webtrafik er over BLOCKALL reglen.

Når reglen oprettes, den skal overføres til firewallen og derefter aktiveret, hvis dette ikke er gjort regel ændringen træder først i kraft. Processen opslagsnål og aktivering er beskrevet i næste afsnit.

## <a name="rule-activation"></a>Regel aktivering
Med den regelsæt, der er ændret for at tilføje denne regel på regelsæt overført til firewallen og aktiveret.

![Firewall regel aktivering][4]

Er en klynge af knapperne i øverste højre hjørne af management-klienten. Klik på knappen "Send ændringer" for at sende de ændrede regler til firewallen, og derefter klikke på knappen "Aktiver".

Dette eksempel miljø build er fuldført med aktivering af firewall regelsæt. Du kan også indlæg build scripts i afsnittet referencer kan køre for at føje et program til dette miljø til at teste den under trafik scenarier.

>[AZURE.IMPORTANT] Det er vigtigt at opdager, at du ikke vil rammer webserveren direkte. Når en browser anmoder om en HTTP-side fra FrontEnd001.CloudApp.Net, sender HTTP slutpunktet (port 80) denne trafik til firewallen, ikke på webserveren. Firewallen derefter i henhold til reglen oprettet ovenfor, NAT-enheder, der anmoder om til webserveren.

## <a name="traffic-scenarios"></a>Trafik scenarier

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Tilladt) Web til webserver, der passerer gennem Firewall
1.  Internet bruger HTTP siden serviceanmodninger fra FrontEnd001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Skybaseret tjeneste gennemløb trafik via Åbn slutpunkt på port 80 til firewall lokale grænseflade på 10.0.1.4:80
3.  Front end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet firewall) gælder, trafik er tilladt, standse regel behandling
4.  Trafik rammer interne IP-adresse i firewall (10.0.1.4)
5.  Firewall videresendelsesregel se dette er port 80 trafik, omdirigerer til webserveren IIS01
6.  IIS01 lytter efter webtrafik, modtager mødeindkaldelsen og starter behandlingen af anmodningen
7.  IIS01 beder SQL Server på AppVM01 oplysninger
8.  Ingen udgående regler på front end-undernet trafik er tilladt
9.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet firewall) ikke anvende, flytte til næste regel
  4.    NSG regel 4 (IIS01 til AppVM01) gælder, trafik er tilladt, skal du stoppe regel behandling
10. AppVM01 modtager SQL-forespørgslen og reagerer
11. Da der ikke er nogen udgående regler på back end-undernet er svaret tilladt
12. Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt.
13. IIS-serveren modtager SQL-svar og afsluttes HTTP-svaret, og der sendes til person
14. Da dette er en NAT-session fra firewallen, er svar destinationen (oprindeligt) for firewallen
15. Firewallen modtager svaret fra Web-serveren og videresender tilbage til den bruger, Internet
16. Fordi der er ingen udgående regler på front end-undernet svaret er tilladt, og brugeren Internet modtager den webside, der anmodes om.

#### <a name="allowed-rdp-to-backend"></a>(Tilladt) RDP til backend-version
1.  Server administrator på internettet, som RDP-session til AppVM01 på BackEnd001.CloudApp.Net:xxxxx, hvor xxxxx er vilkårligt tildelt portnummeret for RDP til AppVM01 (den tilknyttede port kan findes på Azure-portalen eller via PowerShell)
2.  Da firewallen lytter kun på adressen, FrontEnd001.CloudApp.Net, er det ikke forbundet med denne trafikken
3.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) gælder, trafik er tilladt, standse regel behandling
4.  Standardregler i kraft med nogen udgående regler og returnerede trafik er tilladt
5.  RDP-session er aktiveret
6.  AppVM01 beder om brugernavn adgangskode

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tilladt) Web DNS-Server opslag på DNS-server
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Tilladt) Web access serverfilen på AppVM01
1.  IIS01 beder om en fil på AppVM01
2.  Ingen udgående regler på front end-undernet trafik er tilladt
3.  Back end-undernet begynder indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 3 (Internet firewall) ikke anvende, flytte til næste regel
  4.    NSG regel 4 (IIS01 til AppVM01) gælder, trafik er tilladt, skal du stoppe regel behandling
4.  AppVM01 modtager anmodningen og reagerer med fil (hvis det er godkendt adgang)
5.  Da der ikke er nogen udgående regler på back end-undernet er svaret tilladt
6.  Front end-undernet begynder indgående regel behandling:
  1.    Der er ingen NSG regel, der gælder for indgående trafik fra back end-undernet til front end-undernet, så ingen af NSG regler gælder
  2.    Standard system reglen at tillade trafik mellem undernet vil tillade denne trafik, så trafikken er tilladt.
7.  IIS-serveren modtager filen

#### <a name="denied-web-direct-to-web-server"></a>(Nægtet) Web direkte til webserver
Fordi webserveren, IIS01 og firewallen er i samme Skytjenesten dele de samme offentlige modstående IP-adresse. En HTTP-trafik ville dermed dirigeret til firewallen. Mens anmodningen ville være served, den kan ikke gå direkte til den webserver, den passerede, efter hensigten, via firewallen først. Se det første Scenario i denne sektion til trafikken.

#### <a name="denied-web-to-backend-server"></a>(Nægtet) Web til back end-serveren
1.  Internet-bruger forsøger at få adgang til en fil på AppVM01 via tjenesten BackEnd001.CloudApp.Net
2.  Da der ikke er nogen slutpunkter åben for filshare, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, blokerer NSG regel 5 (Internet til VNet) denne trafik

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Nægtet) Web DNS-opslag på DNS-server
1.  Internet-bruger forsøger at slå en interne DNS-post på DNS01 i BackEnd001.CloudApp.Net-tjenesten
2.  Da der ikke er nogen slutpunkter åben for DNS, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til serveren
3.  Hvis slutpunkterne var åbne eller anden grund, NSG regel 5 (Internet til VNet) blokerer denne trafik (Bemærk: denne regel 1 (DNS) vil ikke anvende af to årsager, først kilde-adresse er internettet, denne regel gælder kun for den lokale VNet som kilde, også dette er en regel for Tillad, så den aldrig vil nægte trafik)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Nægtet) Web Access SQL gennem Firewall
1.  Internetbrugeren anmoder om SQL-data fra FrontEnd001.CloudApp.Net (Internet modstående skybaseret tjeneste)
2.  Da der ikke er nogen slutpunkter åben for SQL, dette vil ikke bestået Skytjenesten og vil ikke kunne oprette forbindelse til firewallen
3.  Hvis slutpunkter var åbne eller anden grund, begynder front end-undernet indgående regel behandling:
  1.    NSG regel 1 (DNS) ikke anvende, flytte til næste regel
  2.    NSG regel 2 (RDP) ikke anvende, flytte til næste regel
  3.    NSG regel 2 (Internet firewall) gælder, trafik er tilladt, standse regel behandling
4.  Trafik rammer interne IP-adresse i firewall (10.0.1.4)
5.  Firewall har ingen regler for videresendelse for SQL og udelader trafikken

## <a name="conclusion"></a>Konklusion
Dette er en relativt direkte videresende metode til at beskytte dit program med en firewall, og isolerer back-end-undernettet fra indgående trafik.

Du kan finde flere eksempler og en oversigt over netværk sikkerhedsgrænser [her][HOME].

## <a name="references"></a>Referencer
### <a name="main-script-and-network-config"></a>Primære Script og netværk Config
Gemme fuld scriptet i en PowerShell-script-fil. Du kan gemme Config netværk i en fil med navnet "NetworkConf2.xml".
Ændre de brugerdefinerede variabler efter behov. Køre scriptet og derefter følge instruktionerne til at Firewall regel konfiguration ovenfor.

#### <a name="full-script"></a>Fuld Script
Dette script skal, baseret på brugerdefinerede variabler:

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
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Indgående DMZ med NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Destination NAT-ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Firewallregel"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Firewall regel aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
