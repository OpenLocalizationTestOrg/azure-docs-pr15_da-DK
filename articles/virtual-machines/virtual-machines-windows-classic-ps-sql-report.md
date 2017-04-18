<properties 
    pageTitle="Bruge PowerShell til at oprette en VM med en oprindelig tilstand rapportserver | Microsoft Azure"
    description="Dette emne beskrives og vejleder dig gennem installation og konfiguration af en SQL Server Reporting Services-tilstand rapportserver i en Azure Virtual Machine. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Bruge PowerShell til at oprette en Azure VM med en rapportserver-tilstand

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Dette emne beskrives og vejleder dig gennem installation og konfiguration af en SQL Server Reporting Services-tilstand rapportserver i en Azure Virtual Machine. Trinnene i dette dokument bruge en kombination af manuelle trin til at oprette den virtuelle maskine og et Windows PowerShell-script til at konfigurere Reporting Services på VM. Scriptet til konfiguration omfatter en firewallport åbnes for HTTP eller HTTPs.

>[AZURE.NOTE] Hvis du ikke har brug **HTTPS** på rapportserver, **springe trin 2**.
>
>Når du har oprettet VM i trin 1, gå til afsnittet Brug script til at konfigurere rapportserver og HTTP. Når du kører scriptet, er rapportserveren klar til brug.

## <a name="prerequisites-and-assumptions"></a>Forudsætninger og antagelser

- **Azure-abonnement**: bekræfte antallet kerner, der er tilgængelige i dit Azure-abonnement. Hvis du har oprettet den anbefalede VM størrelse af **A3**, skal du **4** tilgængelig kerner. Hvis du bruger en VM størrelse på **A2**, skal du **2** tilgængelige kerner.
    
    - For at bekræfte core grænsen for dit abonnement på Azure klassisk portalen, skal du klikke på indstillinger i venstre rude og derefter klikke på brugen i den øverste menu.
    
    - Du kan øge kvoten core ved at kontakte [Azure Support](https://azure.microsoft.com/support/options/). Du kan finde VM størrelsesoplysninger [Virtuelt størrelser til Azure](virtual-machines-linux-sizes.md).

- **Windows PowerShell-Scripting**: emnet forudsætter, at du har et grundlæggende kendskab til Windows PowerShell. Du kan finde flere oplysninger om brug af Windows PowerShell, se følgende:

    - [Starte Windows PowerShell på Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Komme i gang med Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Trin 1: Klargøre en Azure virtuelt

1. Gå til portalen Azure klassisk.

1. Klik på **virtuelle maskiner** i venstre rude.

    ![virtuelle Microsoft azure-computere](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Klik på **Ny**.

    ![knappen Ny](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Klik på **fra galleriet**.

    ![nye vm fra galleriet](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Klik på **SQL Server 2014 RTM Standard – Windows Server 2012 R2** , og klik derefter på pilen for at fortsætte.

    ![Næste](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Hvis du har brug for Reporting Services datadrevne abonnementer funktion, kan du vælge **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Flere oplysninger om SQL Server-udgaver og understøttelse af funktioner, kan du se [Funktioner understøttes af udgaverne af SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Redigere følgende felter på siden **virtuelt konfiguration** :
                                    
    - Hvis der er mere end én **VERSION UDGIVELSESDATO**, skal du vælge den nyeste version.
    
    - **Virtuelt navn**: navnet på computer også bruges på konfigurationssiden af næste som skyen tjenesten DNS standardnavnet. DNS-navnet skal være entydig på tværs af tjenesten Azure. Overvej at konfigurere VM med navnet på en computer, der beskriver, hvad VM skal bruges til. For eksempel ssrsnativecloud.
    
    - **Niveau**: Standard
    
    - **Størrelse: A3** er den anbefalede VM størrelse for SQL Server arbejdsmængder. Hvis en VM bruges kun som en rapportserver, er en VM størrelse på A2 tilstrækkelige, medmindre rapportserveren har en stor arbejdsbyrde. Se [Virtuelle maskiner priser](https://azure.microsoft.com/pricing/details/virtual-machines/)for VM prisoplysninger.
    
    - **Nye brugernavn**: det navn, du angiver oprettes som administrator på VM.
    
    - **Ny adgangskode** og **Bekræft**. Denne adgangskode bruges til den nye administratorkonto og anbefales du bruger en stærk adgangskode.
    
    - Klik på **Næste**. ![Næste](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. På den næste side skal du redigere følgende felter:

    - **Skybaseret tjeneste**: Vælg **Opret en ny tjeneste i skyen**.
    
    - **Skyen tjenesten DNS-navn**: Dette er Skytjenesten, der er knyttet til VM offentlige DNS-navn. Standardnavnet er det navn, du har skrevet i for VM navnet. Hvis i senere trin i emnet, du opretter et SSL-certifikat, der er tillid til, og derefter DNS-navnet bruges til værdien af den "**udstedt til**" certifikatets.
    
    - **Område/forbindelse gruppe/virtuelle netværk**: Vælg det område, der er tættest på dine slutbrugere.
    
    - **Lagerplads konto**: Brug en automatisk oprettede lagerplads konto.
    
    - **Angive tilgængelighed**: ingen.
    
    - **SLUTPUNKTER** Holde **Fjernskrivebord** og **PowerShell** slutpunkterne, og Tilføj derefter enten en HTTP eller HTTPS slutpunkt, afhængigt af dit miljø.

        - **HTTP**: standard offentlige og private port er **80**. Bemærk, at hvis du bruger en privat port end 80, ændre **$HTTPport = 80** i http-script.

        - **HTTPS**: standard offentlige og private port er **443**. Sikkerhedsmæssige årsager er at ændre den private port og konfigurere din firewall og rapportserveren til at bruge den private port. Se, [hvordan du angive kommunikation med en virtuel maskine](virtual-machines-windows-classic-setup-endpoints.md)kan finde flere oplysninger om slutpunkter. Bemærk, at hvis du bruger en port end 443, ændre parameteren **$HTTPsport = 443** i HTTPS-script.
    
    - Klik på Næste. ![Næste](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Under den sidste side i guiden skal du gemme standard **installere VM agent** markeret. Trinnene i dette emne anvender ikke VM agent, men hvis du vil beholde denne VM, VM agent og filtypenavne, kan du forbedre han CM.  Se [VM Agent og udvidelser – del 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/)kan finde flere oplysninger om VM agent. En af standard extensions installeret ad kører er filtypenavnet "BGINFO", der vises på VM skrivebord, Systemoplysninger som interne IP- og ledig diskplads.

1. Klik på udført. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. **Status** for VM viser som **Start (artikel Provisioning)** under processen klargøring og derefter vises som **kører** , når VM klargjort og er klar til brug.

## <a name="step-2-create-a-server-certificate"></a>Trin 2: Oprette et servercertifikat

>[AZURE.NOTE] Hvis du ikke kræver HTTPS på rapportserveren, kan du **springe trin 2** , og gå til afsnittet **Brug script til at konfigurere rapportserver og HTTP**. Brug HTTP-script til hurtigt for at konfigurere rapportserveren og rapportserveren vil være klar til brug.

Hvis du vil bruge HTTPS på VM, skal du et SSL-certifikat, der er tillid til. Afhængigt af scenariet, kan du bruge en af følgende to metoder:

- Et gyldigt SSL-certifikat udstedt af et certificering nøglecenter (CA), og der er tillid til af Microsoft. Rodcertifikater CA er påkrævet for at skal distribueres via Microsoft Root Certificate Program. Kan finde flere oplysninger om dette program i [Windows og Windows Phone 8 SSL Root Certificate Program (medlem nøglecentre)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) og [Introduktion til Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Et selvsigneret certifikat. Selvsignerede certifikater anbefales ikke til fremstilling miljøer.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Bruge et certifikat, der er oprettet af en der er tillid til certifikatet nøglecenter (CA)

1. **Anmode om et servercertifikat for det offentlige websted fra et nøglecenter**. 

    Du kan bruge guiden certifikat til at generere en anmodning om certifikatfil (Certreq.txt), du sender til et nøglecenter, eller til at oprette en anmodning om en online nøglecenter. For eksempel Microsoft Certificate Services i Windows Server 2012. Afhængigt af niveauet for identifikation assurance, som dit servercertifikat, er det flere dage til flere måneder for nøglecenter til at godkende anmodningen og sender dig en certifikatfil. 

    Du kan finde flere oplysninger om anmoder om se en servercertifikater, følgende: 
    
    - Brug [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Sikkerhedsværktøjer til at administrere Windows Server 2012.

    [Sikkerhedsværktøjer til at administrere Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] Feltet **udstedt til** af det der er tillid til SSL-certifikat skal være den samme som **Skyen tjenesten DNS-navn** , du har brugt til den nye VM.

1. **Installere servercertifikat på webserveren**. Webserveren i dette tilfælde er den VM, der er vært rapportserveren og webstedet er oprettet i efterfølgende trin, når du konfigurerer Reporting Services. Se [installere et servercertifikat](https://technet.microsoft.com/library/cc740068)kan finde flere oplysninger om installation af servercertifikatet på webserveren ved hjælp af certifikat MMC-snap-in.
    
    Hvis du vil bruge scriptet inkluderet i dette emne, hvis du vil konfigurere rapportserver, værdien af certifikater **miniature** er påkrævet som en parameter for scriptet. Se næste afsnit for at få oplysninger om, hvordan du får fat i miniature for certifikatet.

1. Tildele servercertifikatet til rapportserveren. Opgaven er afsluttet i næste afsnit, når du konfigurerer rapportserveren.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Hvis du vil bruge de virtuelle maskiner selvsigneret certifikat

Et selvsigneret certifikat blev oprettet på VM, når VM blev klargjort. Certifikatet, der har samme navn som VM DNS-navn. For at undgå certifikatfejl, det er nødvendigt, at certifikatet, der er tillid til, på selve VM og også ved alle brugere af webstedet.

1. Føje certifikatet til **Rodnøglecentre**at have tillid til certifikatet på den lokale VM rod CA. Følgende er en oversigt over de nødvendige trin. Detaljeret vejledning til, hvordan du har tillid til Nøglecenteret, skal du se [installere et servercertifikat](https://technet.microsoft.com/library/cc740068).

    1. Vælg VM fra Azure klassisk-portalen, og klik på Opret forbindelse. Afhængigt af browserkonfigurationen af, kan du bedt om at gemme en RDP-fil til at oprette forbindelse til VM.
    
        ![oprette forbindelse til azure virtuelt](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Brug VM brugernavn-brugernavn og adgangskode, du har konfigureret, da du oprettede VM. 
    
        For eksempel i det følgende billede er navnet på VM **ssrsnativecloud** , og brugernavnet er **testuser**.
        
        ![indeholder vm logonnavn](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Køre mmc.exe. Du kan finde flere oplysninger, se [Sådan: få vist certifikater med MMC-Snap-in](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. I menuen console program **fil** tilføje snap-in-programmet **certifikater** , Vælg **Computerkonto** , når du bliver bedt om og klik derefter på **Næste**.
    
    1. Vælg **Lokalcomputer** til at administrere, og klik derefter på **Udfør**.
    
    1. Klik på **Ok** , og udvid derefter den **certifikater - personlige** noder, og klik derefter på **certifikater**. Certifikatet, der hedder efter DNS-navnet på VM og slutter med **cloudapp.net**. Højreklik på navnet på certifikatet, og klik på **Kopiér**.
    
    1. Udvid noden **Rodnøglecentre** og højreklik derefter på **certifikater** , og klik derefter på **Indsæt**.
    
    1. Hvis du vil validere, dobbeltklik på navnet på certifikatet under **Rodnøglecentre** og kontrollere, at der ikke er nogen fejl, og du kan se dit certifikat. Hvis du vil bruge HTTPS scriptet inkluderet i dette emne, hvis du vil konfigurere rapportserver, værdien af certifikater **miniature** er påkrævet som en parameter for scriptet. **Miniatureværdien skal hentes**, skal du udføre følgende. Der er også et eksempel på PowerShell til at hente miniature i afsnittet [Brug script til at konfigurere rapportserver og HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Dobbeltklik på navnet på certifikatet, for eksempel ssrsnativecloud.cloudapp.net.
        
        1. Klik på fanen **Detaljer** .
        
        1. Klik på **miniature**. Værdien af miniature vises i feltet detaljer, for eksempel a6 08 3c f f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
        
        1. Kopiere miniature og gemme værdien til senere eller rediger scriptet nu.
        
        1. (*) Før du kører scriptet, skal du fjerne mellemrum mellem værdipar. For eksempel vil miniature noteret før nu være a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Tildele servercertifikatet til rapportserveren. Opgaven er afsluttet i næste afsnit, når du konfigurerer rapportserveren.

Hvis du bruger et selvsigneret SSL-certifikat, navnet på certifikatet, der allerede svarer til hostname VM. Derfor DNS på computeren, der allerede er registreret globalt og kan åbnes fra en klient.

## <a name="step-3-configure-the-report-server"></a>Trin 3: Konfigurere rapportserveren

Dette afsnit vejleder dig gennem konfigurationen af VM som en Reporting Services-tilstand rapportserver. Du kan bruge en af følgende metoder til at konfigurere rapportserveren:

- Brug scriptet til at konfigurere rapportserveren

- Brug Konfigurationsstyring til at konfigurere rapportserveren.

Mere detaljeret vejledning, skal du se afsnittet [oprette forbindelse til den virtuelle maskine og starte Reporting Services Configuration Manager](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Godkendelse Note:** Windows-godkendelse er den anbefalede godkendelsesmetode, og den er standard Reporting Services-godkendelse. Kun brugere, der er konfigureret på VM kan få adgang til Reporting Services og tildelt Reporting Services-roller.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Brug script til at konfigurere rapportserveren og HTTP

For at bruge Windows PowerShell-script til at konfigurere rapportserveren skal du udføre følgende trin. Konfigurationen omfatter HTTP, ikke HTTPS:

1. Vælg VM fra Azure klassisk-portalen, og klik på Opret forbindelse. Afhængigt af browserkonfigurationen af, kan du bedt om at gemme en RDP-fil til at oprette forbindelse til VM.

    ![oprette forbindelse til azure virtuelt](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Brug VM brugernavn-brugernavn og adgangskode, du har konfigureret, da du oprettede VM. 

    For eksempel i det følgende billede er navnet på VM **ssrsnativecloud** , og brugernavnet er **testuser**.
    
    ![indeholder vm logonnavn](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Åbn **Windows PowerShell ISE** på VM, med administratorrettigheder. PowerShell ISE er som standard på Windows server 2012 installeret. Det anbefales, at du bruger ISE i stedet for et almindeligt Windows PowerShell-vindue, så du kan indsætte scriptet i ISE, redigere scriptet og derefter køre scriptet.

1. Klik på menuen **Vis** i Windows PowerShell ISE, og klik derefter på **Vis Script ruden**.

1. Kopiere følgende script, og Indsæt scriptet i ruden Windows PowerShell ISE script.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Hvis du har oprettet VM med en HTTP-port end 80, ændre parameteren $HTTPport = 80.

1. Scriptet er konfigureret til Reporting Services. Hvis du vil køre scriptet til Reporting Services, skal du ændre version del af stien til navneområdet til "v11" på sætningen Get-WmiObject.

1. Køre scriptet.

**Validering**: for at bekræfte, at funktionen grundlæggende rapport server fungerer, skal du se afsnittet [Bekræft konfigurationen](#verify-the-configuration) senere i dette emne.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Brug script til at konfigurere rapportserveren og HTTPS

Hvis du vil bruge Windows PowerShell til at konfigurere rapportserveren, skal du benytte følgende fremgangsmåde. Konfigurationen omfatter HTTPS, ikke HTTP.

1. Vælg VM fra Azure klassisk-portalen, og klik på Opret forbindelse. Afhængigt af browserkonfigurationen af, kan du bedt om at gemme en RDP-fil til at oprette forbindelse til VM.

    ![oprette forbindelse til azure virtuelt](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Brug VM brugernavn-brugernavn og adgangskode, du har konfigureret, da du oprettede VM. 

    For eksempel i det følgende billede er navnet på VM **ssrsnativecloud** , og brugernavnet er **testuser**.

    ![indeholder vm logonnavn](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Åbn **Windows PowerShell ISE** på VM, med administratorrettigheder. PowerShell ISE er som standard på Windows server 2012 installeret. Det anbefales, at du bruger ISE i stedet for et almindeligt Windows PowerShell-vindue, så du kan indsætte scriptet i ISE, redigere scriptet og derefter køre scriptet.

1. For at aktivere, der kører scripts skal du køre følgende Windows PowerShell-kommandoen:

        Set-ExecutionPolicy RemoteSigned

    Du kan derefter køre følgende for at bekræfte politikken:

        Get-ExecutionPolicy

1. Klik på menuen **Vis** i **Windows PowerShell ISE**, og klik derefter på **Vis Script ruden**.

1. Kopiere følgende script, og sæt dem ind i ruden Windows PowerShell ISE script.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Ændre parameteren **$certificatehash** i scriptet:

    - Dette er en **påkrævet** parameter. Hvis du ikke har gemt værdien certifikat fra de forrige trin, kan du bruge en af følgende metoder til at kopiere hashværdi certifikat fra certifikater miniature.:

        Åbn Windows PowerShell ISE på VM, og køre følgende kommando:

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        Output ligner følgende. Hvis scriptet returnerer en tom linje, hvor VM ikke har et certifikat, der er konfigureret for eksempel, i afsnittet [Brug virtuelle maskiner Self-signed certifikat](#to-use-the-virtual-machines-self-signed-certificate).
    
    ELLER
    
    - Kør mmc.exe på VM og derefter tilføje snap-in-programmet **certifikater** .
    
    - Dobbeltklik på certifikatnavnet på din under noden **Der er tillid til rodnøglecentre** . Hvis du bruger et selvsigneret certifikat af VM, certifikatet, der hedder efter DNS-navnet på VM og slutter med **cloudapp.net**.
    
    - Klik på fanen **Detaljer** .
    
    - Klik på **miniature**. Værdien af miniature vises i feltet detaljer, for eksempel af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
    
    - **Før du kører scriptet**, fjerne mellemrum mellem værdipar. For eksempel af1160b64b288d890a8212ff6ba9c3664f319048

1. Ændre parameteren **$httpsport** : 

    - Hvis du har brugt port 443 for HTTPS-slutpunkt, derefter behøver du ikke at opdatere denne parameter i scriptet. Ellers skal du bruge Portværdien, du valgte, da du konfigurerede HTTPS privat slutpunktet på VM.

1. Ændre parameteren **$DNSName** : 

    - Scriptet er konfigureret til et jokertegn certifikat $DNSName = "+". Hvis du ikke gør ingen vil konfigurere et jokertegn certifikat indbinding, skrive en kommentar ud $DNSName ="+"og aktivere følgende linje, fuld $DNSNAme reference, ## $DNSName="$server.cloudapp.net".

        Ændre værdien $DNSName, hvis du ikke vil bruge den virtuelle maskine DNS-navn for Reporting Services. Hvis du bruger parameteren, certifikatet skal også bruge dette navn, og du registrere navn globalt på en DNS-server.

1. Scriptet er konfigureret til Reporting Services. Hvis du vil køre scriptet til Reporting Services, skal du ændre version del af stien til navneområdet til "v11" på sætningen Get-WmiObject.

1. Køre scriptet.

**Validering**: for at bekræfte, at funktionen grundlæggende rapport server fungerer, skal du se afsnittet [Bekræft konfigurationen](#verify-the-connection) senere i dette emne. For at bekræfte certifikatet binding Åbn en kommandoprompt med administratorrettigheder, og derefter køre følgende kommando:

    netsh http show sslcert

Resultatet omfatter følgende:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Brug Configuration Manager til at konfigurere rapportserveren

Hvis du ikke vil køre PowerShell-scriptet for at konfigurere rapportserveren, skal du følge trinnene i dette afsnit for at bruge Konfigurationsstyring til Reporting Services-tilstand til at konfigurere rapportserveren.

1. Vælg VM fra Azure klassisk-portalen, og klik på Opret forbindelse. Brug det brugernavn og adgangskode, du har konfigureret, da du oprettede VM.

    ![oprette forbindelse til azure virtuelt](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Kør Windows update og installere opdateringer til VM. Hvis en genstart af VM er påkrævet, genstart VM og genoprette forbindelse til VM fra Azure klassisk portal.

1. Skriv **Reporting Services** , og Åbn **Reporting Services Configuration Manager**fra menuen Start på VM.

1. Lad standardværdierne for **Servernavnet** og **Rapport Server-forekomst**. Klik på **Opret forbindelse**.

1. Klik på **Web URL-adressen**i venstre rude.

1. RS er som standard konfigureret til HTTP-port 80 med IP-adresse "Alle tildelt". Sådan tilføjer HTTPS:

    1. I **SSL-certifikat**: Vælg det certifikat, du vil bruge, for eksempel [VM navn]. cloudapp.net. Hvis ingen certifikater ikke er vist, i afsnittet **trin 2: oprette et servercertifikat** oplysninger om, hvordan du installerer og har tillid til certifikatet på VM.
    
    1. Under **SSL-Port**: Vælg 443. Hvis du har konfigureret HTTPS privat slutpunktet i VM med en anden privat port, kan du bruge denne værdi her.
    
    1. Klik på **Anvend** , og vent på handlingen er fuldført.

1. Klik på **Database**i venstre rude.

    1. Klik på **Skift Databas**e.
    
    1. Klik på **Opret en ny rapport server-database** , og klik derefter på **Næste**.
    
    1. Lad standarden **Servernavn**: som VM navn og lade standard **Godkendelsestype** som **Aktuelle bruger** – **Integreret sikkerhed**. Klik på **Næste**.
    
    1. Lad standarden **Databasenavnet** som **ReportServer** , og klik på **Næste**.
    
    1. Lad standarden **Godkendelsestype** som **Service legitimationsoplysningerne** , og klik på **Næste**.
    
    1. Klik på **Næste** på siden **Oversigt** .
    
    1. Klik på **Udfør**, når konfigurationen er fuldført.

1. Klik på **Rapportstyring URL-adressen**i venstre rude. Lad være standard **Virtuel mappe** som **rapporter** , og klik på **Anvend**.

1. Klik på **Afslut** for at lukke Reporting Services Configuration Manager.

## <a name="step-4-open-windows-firewall-port"></a>Trin 4: Åbn Windows Firewall Port

>[AZURE.NOTE] Hvis du har brugt en af scriptene til at konfigurere rapportserveren, kan du springe dette afsnit. Scriptet medtages et trin for at åbne firewall-porten. Standard blev port 80 til HTTP og port 443 til HTTPS.

Hvis du vil oprette forbindelse fra en fjernplacering til Rapportstyring eller rapportserveren på den virtuelle maskine, kræves et TCP-slutpunkt på VM. Det er påkrævet til at åbne den samme port i den VM firewall. Slutpunktet blev oprettet, da VM blev klargjort.

Dette afsnit indeholder grundlæggende oplysninger om, hvordan du åbner firewall porten. Yderligere oplysninger finder du se [konfigurere en Firewall for rapport serveradgang](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Hvis du har brugt scriptet til at konfigurere rapportserveren, kan du springe dette afsnit. Scriptet medtages et trin for at åbne firewall-porten.

Hvis du har konfigureret en privat port til HTTPS end 443, kan du ændre følgende script korrekt. For at åbne port **443** på Windows Firewall skal du udføre følgende:

1. Åbne et Windows PowerShell-vindue med administratorrettigheder.

1. Hvis du brugte en port end 443, da du konfigurerede HTTPS slutpunktet på VM, opdateres porten i følgende kommando og derefter køre kommandoen:

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Når kommandoen er fuldført, **Ok** vises i kommandoprompten.

Åbne et vindue i Windows PowerShell for at bekræfte, at port åbnes, og køre følgende kommando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificer konfigurationen

Åbn browseren med administratorrettigheder for at bekræfte, at funktionen grundlæggende rapport server nu fungerer, og derefter gå til følgende server ad rapport Rapportstyring URL-ADRESSER:

- Gå til URL-adressen til server på VM:

        http://localhost/reportserver

- Gå til URL-adressen til Manager på VM:

        http://localhost/Reports

- Gå til rapporten **remote** Manager på VM fra din lokale computer. Opdatere DNS-navnet i følgende eksempel efter behov. Når du bliver bedt om en adgangskode, kan du bruge administratorrettigheder, du oprettede, da VM blev klargjort. Brugernavnet er i [domæne]\[brugernavn] format, hvor domænet er VM navnet på computer, for eksempel ssrsnativecloud\testuser. Hvis du ikke bruger HTTP**S**, kan du fjerne **s** i URL-adressen. Se næste afsnit for at få oplysninger om at oprette flere brugere på VM.

        https://ssrsnativecloud.cloudapp.net/Reports

- Gå til remote Rapportserveren fra din lokale computer. Opdatere DNS-navnet i følgende eksempel efter behov. Hvis du ikke bruger HTTPS, kan du fjerne s i URL-adressen.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Oprette brugere og tildele roller

Når du konfigurerer og bekræfte rapportserveren, er et almindelige administrative opgaver til at oprette en eller flere brugere og tildele brugere til Reporting Services-roller. Yderligere oplysninger finder du se følgende:

- [Oprette en lokal brugerkonto](https://technet.microsoft.com/library/cc770642.aspx)

- [Giv brugeren adgang til en rapportserver (Rapportstyring)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Oprette og administrere rolletildelinger](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>At oprette og publicere rapporter Azure virtuelt

I følgende tabel opsummeres nogle af de indstillinger, der er tilgængelig til at udgive eksisterende rapporter fra en lokal computer til rapportserveren hostet på Microsoft Azure Virtual Machine:

- **RS.exe script**: Brug RS.exe script til at kopiere rapportelementer fra og eksisterende rapportserver til din Microsoft Azure virtuelle maskine. Yderligere oplysninger finder du i afsnittet "-tilstand til oprindelig tilstand – Microsoft Azure virtuel maskine" i [eksempel Reporting Services rs.exe Script til at overføre indhold mellem rapportservere](https://msdn.microsoft.com/library/dn531017.aspx).

- **Report Builder**: den virtuelle maskine omfatter Klik-én gang version af Microsoft SQL Server Report Builder. Sådan startes Report builder først gang på den virtuelle maskine:

    1. Start din browser med administratorrettigheder.
    
    1. Gå til Rapportstyring på den virtuelle maskine, og klik på **Report Builder** på båndet.

    Se [installation, afinstallation af, og understøttende Report Builder](https://technet.microsoft.com/library/dd207038.aspx)kan finde flere oplysninger.

- **SQL Server Data Tools: VM**: Hvis du har oprettet VM med SQL Server 2012 og derefter SQL Server Data Tools er installeret på den virtuelle maskine og kan bruges til at oprette **Rapport Server projekter** og rapporter på den virtuelle maskine. SQL Server Data Tools kan udgive rapporterne til rapportserveren på den virtuelle maskine.
    
    Hvis du har oprettet VM med SQL server 2014, kan du installere SQL Server Data Tools – BI til visual Studio. Yderligere oplysninger finder du se følgende:

    - [Microsoft SQL Server Data Tools – Business Intelligence til Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Microsoft SQL Server Data Tools – Business Intelligence til Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [SQL Server Data Tools og SQL Server BI (Business Intelligence SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **SQL Server Data Tools: Remote**: Opret en Reporting Services-projekt i SQL Server Data Tools, der indeholder Reporting Services-rapporter på din lokale computer. Konfigurere project til at oprette forbindelse til tjenesten URL-adresse.

    ![SSDT Projektegenskaber for SSRS projekt](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Brug script**: Brug script til at kopiere rapport serverindhold. Se [eksempel Reporting Services rs.exe Script til at overføre indhold mellem rapportservere](https://msdn.microsoft.com/library/dn531017.aspx)kan finde flere oplysninger.

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimere omkostninger, hvis du ikke bruger VM

>[AZURE.NOTE] Luk VM fra Azure klassisk portal for at minimere gebyrer for din virtuelle Azure-computere når den ikke er i brug. Hvis du bruger Windows power indstillinger i en VM for at lukke VM, er du stadig betale samme beløb for VM. Hvis du vil reducere gebyrer, skal du lukke VM i portalen Azure klassisk. Hvis du ikke længere har brug for VM, huske at slette VM og de tilknyttede .vhd filer for at undgå lagerplads gebyrer. Se afsnittet ofte stillede spørgsmål i [Virtuelle maskiner priser detaljer](https://azure.microsoft.com/pricing/details/virtual-machines/)kan finde flere oplysninger.

## <a name="more-information"></a>Få mere at vide

### <a name="resources"></a>Ressourcer

- Du kan finde lignende indhold, der er relateret til en enkelt serverinstallation af SQL Server Business Intelligence og SharePoint 2013, [Bruge Windows PowerShell til at oprette en Azure VM med SQL Server BI og SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Du kan finde lignende indhold, der er relateret til en med flere server installation af SQL Server Business Intelligence og SharePoint 2013, [Installere SQL Server Business Intelligence i virtuelle Azure-computere](https://msdn.microsoft.com/library/dn321998.aspx).

- Du kan finde generelle oplysninger i forbindelse med installation af SQL Server Business Intelligence i Azure virtuelle maskiner, [SQL Server Business Intelligence i virtuelle Azure-computere](virtual-machines-windows-classic-ps-sql-bi.md).

- Flere oplysninger om omkostninger af Azure beregne gebyrer, skal du se fanen virtuelle maskiner i [Azure priser Lommeregner](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Community-indhold

- Du kan finde trinvise instruktioner til, hvordan du opretter en Reporting Services oprindelige tilstand rapportserver uden brug af script, [Vært for SQL Reporting Service på Azure Virtual Machine](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Links til andre ressourcer til SQL Server på Azure FOS

[SQL Server på Azure virtuelle maskiner oversigt](virtual-machines-windows-sql-server-iaas-overview.md)
