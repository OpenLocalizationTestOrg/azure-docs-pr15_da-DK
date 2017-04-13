<properties
    pageTitle="SQL Server Business Intelligence | Microsoft Azure"
    description="Dette emne anvender ressourcer, der er oprettet med den klassiske implementeringsmodel og beskriver de Business Intelligence (BI)-funktioner, der er tilgængelige for SQL Server, der kører på Azure virtuelle maskiner (VM'er)."
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

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence i Azure virtuelle maskiner

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Galleriet Microsoft Azure Virtual Machine indeholder billeder, der indeholder SQL Server-installationer. SQL Server-udgaver, der understøttes i galleriet billederne er de samme installationsfiler, kan du installere til lokale computere og virtuelle computere. Dette emne indeholder en oversigt over de SQL Server Business Intelligence (BI)-funktioner, der er installeret på billederne og trinnene til konfiguration af påkrævet efter en virtuel maskine er klargjort. Dette emne beskrives også understøttet installation topologier til BI-funktioner og bedste fremgangsmåder.

## <a name="license-considerations"></a>Overvejelser i forbindelse med licens

Der er to måder at licens SQL Server på Microsoft Azure virtuelle maskiner:

1. Licens mobilitet fordele, der er en del af Software Assurance. Du kan finde flere oplysninger [Licens mobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Betale hver time rente af Azure virtuelle maskiner med SQL Server er installeret. Se afsnittet "SQL Server" i [Virtuelle maskiner priser](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Du kan finde flere oplysninger om licenser og aktuelle satser, [Virtuelle maskiner licensering ofte stillede spørgsmål om](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server billeder, der findes i Azure Galleri for virtuel maskine

Galleriet Microsoft Azure Virtual Machine indeholder flere billeder, der indeholder Microsoft SQL Server. Det er installeret på virtuelt billederne varierer afhængigt af versionen af operativsystemet og versionen af SQL Server. På listen over tilgængelige billeder i galleriet Azure virtuelt ændres ofte.

![SQL-billede i azure VM galleri](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Følgende PowerShell-script returnerer listen over Azure billeder, der indeholder "SQL-Server" på procesnavn:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Flere oplysninger om udgaver og funktioner, der understøttes af SQL Server, se følgende:

- [Udgaver af SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Funktioner, der understøttes af udgaverne af SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI-funktioner er installeret på SQL Server Galleri for virtuel maskine billeder

Den følgende tabel opsummerer de Business Intelligence-funktioner, der er installeret på almindelige Microsoft Azure Virtual Machine galleriet billederne til SQL Server"

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 Standard

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 Standard

|SQL Server BI funktion|Installeret på galleriet Hurtige|Noter|
|---|---|---|
|**Reporting Services-tilstand**|Ja|Installeret, men kræver konfiguration, herunder URL-adressen til manager. I afsnittet [Konfigurere Reporting Services](#configure-reporting-services).|
|**Reporting Services SharePoint-tilstand**|Nej|Microsoft Azure Virtual Machine galleriet Hurtige ikke inkluderer SharePoint- eller SharePoint-installationsfiler. <sup>1</sup>|
|**Multidimensionelle Analysis Services og Data mining (OLAP)**|Ja|Installeret og konfigureret som standard Analysis Services-forekomst|
|**Analysis Services Tabular**|Nej|Understøttes i SQL Server 2012, er 2014 og 2016 billeder, men det ikke installeret som standard. Installere en anden forekomst af Analysis Services. Se afsnittet installere andre SQL Server Services og funktioner i dette emne.|
|**Analysis Services Powerpivot til SharePoint**|Nej|Microsoft Azure Virtual Machine galleriet Hurtige ikke inkluderer SharePoint- eller SharePoint-installationsfiler. <sup>1</sup>|

<sup>1</sup> for flere oplysninger om SharePoint og Azure virtuelle maskiner skal du se [Microsoft Azure arkitekturer til SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) og [SharePoint-installationen på Microsoft Azure virtuelle maskiner](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Kør følgende PowerShell-kommando for at få en liste over installerede tjenester, der indeholder "SQL" i navnet på tjenesten.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Generelle anbefalinger og bedste praksis

- Den mindste anbefalede størrelse for en virtuel maskine er **A3** , når du bruger SQL Server Enterprise Edition. **A4** virtuelt størrelse anbefales til SQL Server BI installationer af Analysis Services og Reporting Services.

    Du kan finde oplysninger om de aktuelle VM størrelser, [Virtuelt størrelser til Azure](virtual-machines-linux-sizes.md).

- Den bedste fremgangsmåde til at administrere disk er at gemme data, log og sikkerhedskopifilerne på drev end **C**: og **D**:. For eksempel oprette data **E**: og **F**:.

    - Det drev, cachelagring politik for standard drev **C**: der ikke er optimal til at arbejde med data.

    - **D**: drev er et midlertidigt drev, der bruges primært til sidefilen. **D**: drev bevares ikke og gemmes ikke i blob-lager. Administration af opgaver, som en ændring af størrelsen virtuelt nulstille **D**: drev. Det anbefales **ikke** at bruge **D**: drev for databasefiler, herunder tempdb.

    Du kan finde flere oplysninger om oprettelse og vedhæfte diske, se, [hvordan du vedhæfte en Data Disk til en virtuel maskine](virtual-machines-windows-classic-attach-disk.md).

- Stoppe eller fjerne tjenester, du ikke vil bruge. For eksempel hvis den virtuelle maskine bruges kun til Reporting Services, stoppe eller fjerne Analysis Services og SQL Server Integration Services. Følgende billede er et eksempel på de tjenester, der er i gang som standard.

    ![SQL Server-tjenesterne](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] SQL Server-database engine er påkrævet i de understøttede BI-scenarier. I en enkelt server VM topologi skal af-databaseprogram køre på den samme VM.

    Se følgende kan finde flere oplysninger: [Fjerne Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) og [Fjern en forekomst af Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Kontrollere **Windows Update** nye 'vigtige opdateringer'. Microsoft Azure Virtual Machine billederne opdateres ofte; vigtige opdateringer kan dog bliver tilgængelig fra **Windows Update** , når billedet VM sidst blev opdateret.

## <a name="example-deployment-topologies"></a>Eksempel på installation topologier

Følgende er eksempel-installationer, der bruger virtuelle Microsoft Azure-computere. Topologier i disse diagrammer er kun nogle af de mulige topologier, du kan bruge med SQL Server BI-funktioner og virtuelle Microsoft Azure-computere.

### <a name="single-virtual-machine"></a>Enkelt virtuelt

Analysis Services, Reporting Services, SQL Server-Database Engine og datakilder på en enkelt virtuel maskine.

![BI iass scenarie med 1 virtuelt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>To virtuelle maskiner

- Analysis Services, Reporting Services og SQL Server-Database Engine på en enkelt virtuel maskine. Denne installation omfatter rapport server-databaser.

- Datakilder i en anden VM. Den anden VM omfatter SQL Server-Database Engine som datakilde.

![BI iaas scenarie med 2 virtuelle maskiner](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Blandede Azure-data på Azure SQL-database

- Analysis Services, Reporting Services og SQL Server-Database Engine på en enkelt virtuel maskine. Denne installation omfatter rapport server-databaser.

- Datakilden er Azure SQL-database.

![BI iaas scenarier vm og AzureSQL som datakilde](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrid – lokale data

- I dette eksempel-installation Analysis Services kører Reporting Services og SQL Server-Database Engine på en enkelt virtuel maskine. Den virtuelle maskine vært for rapport server-databaser. Den virtuelle maskine er føjet til en lokal domæne via Azure Virtual Networking eller nogle andre VPN-tunnelføring løsning.

- Datakilden er i det lokale miljø.

![BI iaas scenarier vm og på lokale datakilder](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfiguration af Reporting Services-tilstand

Virtuelt galleriet Hurtige til SQL Server indeholder Reporting Services-tilstand, der er installeret, men rapportserveren ikke er konfigureret. Trinnene i dette afsnit konfigurere Reporting Services-rapport-serveren. Du kan finde mere detaljerede oplysninger om konfiguration af Reporting Services-tilstand skal [Installere Reporting Services oprindelige tilstand rapport Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Du kan finde lignende indhold, der bruger Windows PowerShell-scripts til at konfigurere rapportserveren, [Bruge PowerShell til at oprette en Azure VM med et oprindelige tilstand rapportserver](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Oprette forbindelse til den virtuelle maskine og starte Reporting Services Configuration Manager

Der er to almindelige arbejdsprocesser til at oprette forbindelse til en Azure Virtual Machine:

- Oprette forbindelse i ved at klikke på navnet på den virtuelle maskine, og klik derefter på **Opret forbindelse**. En forbindelse til Fjernskrivebord åbnes, og navnet på computer, der automatisk udfyldes.

    ![oprette forbindelse til azure virtuelt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Oprette forbindelse til den virtuelle maskine med Windows Fjernskrivebord. I brugergrænsefladen på det Fjernskrivebord:

    1. Skriv **navnet til skyen tjenesten** som navnet på computeren.

    1. Skriv kolon (:) og offentlige portnummeret, der er konfigureret til TCP remote desktop slutpunkt.

        Myservice.cloudapp.NET:63133

        Du kan finde flere oplysninger, se [Hvad er en skybaseret tjeneste?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Start Reporting Services Configuration Manager.**

1. I **Windows Server 2012**:

1. Skriv **Reporting Services** for at få vist en liste over Apps fra skærmbilledet **Start** .

1. Højreklik på **Reporting Services Configuration Manager** , og klik på **Kør som Administrator**.

1. I **Windows Server 2008 R2**:

1. Klik på **Start**, og klik derefter på **Alle programmer**.

1. Klik på **Microsoft SQL Server 2016**.

1. Klik på **konfigurationsværktøjer**.

1. Højreklik på **Reporting Services Configuration Manager** , og klik på **Kør som Administrator**.

Eller

1. Klik på **Start**.

1. Skriv **reporting services**, i dialogboksen **Søg efter programmer og filer** . Hvis VM kører Windows Server 2012, skal du skrive **reporting services** på startskærmen i Windows Server 2012.

1. Højreklik på **Reporting Services Configuration Manager** , og klik på **Kør som Administrator**.

    ![søge efter ssrs Konfigurationsstyring](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurere Reporting Services

**Tjenestekonto og web-URL-adresse:**

1. Kontrollere at **Servernavnet** er navnet på den lokale server, og klik på **Opret forbindelse**.

1. Bemærk tom **Rapport servernavn, Database**. Databasen er oprettet, når konfigurationen er fuldført.

1. Bekræft **Rapport serverens Status** er **startet**. Hvis du vil kontrollere tjenesten i Windows Server Manager, er tjenesten **SQL Server Reporting Services** Windows-tjenesten.

1. Klik på **Servicekonto** og ændre kontoen, efter behov. Hvis den virtuelle maskine bruges i et ikke-joinforbundne domænemiljø, er den indbyggede **ReportServer** konto tilstrækkelige. Du kan finde flere oplysninger om servicekonto, [Tjenestekonto](https://msdn.microsoft.com/library/ms189964.aspx).

1. Klik på **Web URL-adressen** i venstre rude.

1. Klik på **Anvend** for at konfigurere standardværdierne.

1. Bemærk **rapport Server Web Service URL-adresser**. Bemærk, at TCP standardporten er 80 og er en del af URL-adressen. På et senere tidspunkt, kan du oprette et Microsoft Azure virtuelt slutpunkt for port.

1. Kontrollér de handlinger, der er fuldført, i ruden **resultater** .

**Database:**

1. Klik på **Database** i venstre rude.

1. Klik på **Skift Database**.

1. Kontrollér **Opret en ny rapport server-database** er markeret, og klik derefter på Næste.

1. Bekræft **Servernavn** , og klik på **Test forbindelse**.

1. Hvis resultatet er **testforbindelsen lykkedes**, klik på **OK** , og klik derefter på **Næste**.

1. Bemærk databasenavnet er **ReportServer** og **rapportserver tilstand** er **oprindelige** og klik derefter på **Næste**.

1. Klik på **Næste** på siden **legitimationsoplysninger** .

1. Klik på **Næste** på siden **Oversigt** .

1. Klik på **Næste** på siden **status, og Afslut** .

**Web portalen URL-adresse eller Webadresse på Rapportstyring til 2012 og 2014:**

1. Klik på **URL-adressen til portalen**eller **Rapportstyring URL-adressen** for 2014 og 2012 i venstre rude.

1. Klik på **Anvend**.

1. Kontrollér de handlinger, der er fuldført, i ruden **resultater** .

1. Klik på **Afslut**.

Du kan finde oplysninger om rapport servertilladelser, [Give tilladelser til en oprindelige tilstand rapportserver](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Gå til den lokale Rapportstyring

For at kontrollere konfigurationen, skal du gå til Rapportstyring på VM.

1. Start Internet Explorer på VM, med administratorrettigheder.

1. Gå til http://localhost/reports på VM.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>At oprette forbindelse til Remote webportal eller Rapportstyring til 2014 og 2012

Hvis du vil oprette forbindelse til internettet portal eller Rapportstyring til 2014 og 2012 og på den virtuelle maskine fra en fjerncomputer, kan du oprette en ny virtuel maskine TCP-slutpunkt. Som standard lytter rapportserveren efter HTTP-anmodninger på **port 80**. Hvis du konfigurerer rapport server URL-adresserne for at bruge en anden port, skal du angive portnummeret i de følgende instruktioner.

1. Oprette et slutpunkt for den virtuelle maskine af TCP-Port 80. Finde flere oplysninger i afsnittet [virtuelt slutpunkter og Firewall-porte](#virtual-machine-endpoints-and-firewall-ports) i dette dokument.

1. Åbn port 80 i den virtuelle maskine firewall.

1. Gå til den webportalen eller Rapportstyring, med Azure virtuelt **DNS-navn** som servernavnet i URL-adressen. Eksempel:

    **Rapportserver**: http://uebi.cloudapp.net/reportserver  **webportal**: http://uebi.cloudapp.net/reports

    [Konfigurere en Firewall til rapport serveradgang](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>At oprette og publicere rapporter Azure virtuelt

I følgende tabel opsummeres nogle af de indstillinger, der er tilgængelig til at udgive eksisterende rapporter fra en lokal computer til rapportserveren hostet på Microsoft Azure Virtual Machine:

- **Report Builder**: den virtuelle maskine omfatter Klik-én gang version af Microsoft SQL Server Report Builder for SQL-2014 og 2012. Sådan startes Report builder først gang på den virtuelle maskine med SQL-2016:

    1. Start din browser med administratorrettigheder.

    1. Gå til webportalen på en virtuel maskine, og vælg **Download** -ikonet i øverste højre hjørne.
    
    1. Vælg **Report Builder**.

    Du kan finde flere oplysninger [Start Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).

- **SQL Server Data Tools**: VM: SQL Server Data Tools er installeret på den virtuelle maskine og kan bruges til at oprette **Rapport Server projekter** og rapporter på den virtuelle maskine. SQL Server Data Tools kan udgive rapporterne til rapportserveren på den virtuelle maskine.

- **SQL Server Data Tools: Remote**: Opret en Reporting Services-projekt i SQL Server Data Tools, der indeholder Reporting Services-rapporter på din lokale computer. Konfigurere project til at oprette forbindelse til tjenesten URL-adresse.

    ![SSDT Projektegenskaber for SSRS projekt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Oprette en. Virtuelle harddisk, der indeholder rapporter og derefter overføre og vedhæfte drevet.

    1. Oprette en. Virtuelle harddisk på din lokale computer, der indeholder dine rapporter.

    1. Oprette og installere et management-certifikat.

    1. Overføre filen Virtuelle til Azure ved hjælp af Tilføj AzureVHD cmdlet [Opret og Overfør en virtuel harddisk Windows Server til Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Vedhæfte disken til den virtuelle maskine.

## <a name="install-other-sql-server-services-and-features"></a>Installere andre SQL Server Services og funktioner

Køre installationsguiden af SQL server for at installere yderligere SQL Server-tjenester, som Analysis Services i tabelformat tilstand. Konfiguration af filerne er på den virtuelle maskine lokale disk.

1. Klik på **Start** , og klik derefter på **Alle programmer**.

1. Klik på **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** eller **Microsoft SQL Server 2012** , og klik derefter på **Konfigurationsværktøjer**.

1. Klik på **SQL Server Installation Center**.

Eller afspille C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe eller C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] Første gang du kører installationsprogrammet til SQL Server, flere installationsfiler kan hentes og kræver en genstart af den virtuelle maskine og genstart af installationen af SQL Server.
>
>Hvis du vil tilpasse det billede, der er valgt fra Microsoft Azure Virtual Machine flere gange, kan du overveje at oprette din egen SQL Server-billede. Analysis Services SysPrep funktionalitet er aktiveret med SQL Server 2012 SP1 CU2. Se [overvejelser i forbindelse med installation af SQL Server ved hjælp af SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) og [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)kan finde flere oplysninger.

### <a name="to-install-analysis-services-tabular-mode"></a>Hvis du vil installere Analysis Services Tabular tilstand

Trinnene i dette afsnit **opsummere** installationen af Analysis Services tabular-tilstand. Yderligere oplysninger finder du se følgende:

- [Installere Analysis Services i tabelformat tilstand](https://msdn.microsoft.com/library/hh231722.aspx)

- [Tabelformat modellering (Adventure Works-selvstudium)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Sådan installeres Analysis Services Tabular tilstand:**

1. Klik på **Installation** i venstre rude i guiden til installation af SQL Server, og klik derefter på **enkeltstående installation af nye SQL server eller føje funktioner til en eksisterende installation**.

    - Hvis du ser den **Angiv en mappe**, gå til c:\SQLServer_13.0_full, c:\SQLServer_12.0_full eller c:\SQLServer_11.0_full, og klik derefter på **Ok**.

1. Klik på **Næste** på siden produkt opdateringer.

1. Vælg **Udfør en ny installation af SQL Server** , og klik på **Næste**på siden **Installationstype** .

1. Klik på **Installation af SQL Server-funktioner**på siden **Konfiguration af rollen** .

1. Klik på **Analysis Services**på siden **Valg af funktion** .

1. Skriv et beskrivende navn, som **tabelformat** tekstbokse **Med navnet forekomst** og **Forekomst-Id** på siden **Konfiguration af forekomst** .

1. Vælg **Tabelformat tilstand**på siden **Konfiguration af Analysis Services** . Tilføj den aktuelle bruger til listen administrative tilladelser.

1. Udfør, og Luk installationsguiden af SQL Server.

## <a name="analysis-services-configuration"></a>Konfiguration af Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Fjernadgang til Analysis Services-serveren

Analysis Services-serveren understøtter kun windows-godkendelse. For at få adgang til Analysis Services fra en fjernplacering fra klientprogrammer som SQL Server Management Studio eller SQL Server Data Tools, skal den virtuelle maskine være knyttet til dit lokale domæne, ved hjælp af Azure Virtual Networking. Flere oplysninger under [Azure virtuelt netværk](../virtual-network/virtual-networks-overview.md).

En **standardforekomst** af Analysis Services lytter på TCP port **2383**. Åbn porten i virtuelle maskiner firewallen. En grupperet navngivet forekomst af Analysis Services også lytter på port **2383**.

For en **navngivet forekomst** af Analysis Services kræves tjenesten SQL Server-browseren til at administrere port adgang. Konfigurationen af SQL Server-browseren standard er port **2382**.

Åbn port **2382** i firewallen virtuelle maskiner, og Opret en statisk Analysis Services med navnet forekomst port.

1. Hvis du vil kontrollere porte, der allerede findes i brug på VM og hvilken proces anvender portene, skal du køre følgende kommando med administratorrettigheder:

        netstat /ao

1. Brug SQL Server Management Studio til at oprette en statisk Analysis Services navngivne forekomst port ved at opdatere 'Port' værdi i tabelformat AS forekomst generelle egenskaber. Du kan finde flere oplysninger, se afsnittet "bruge en fast port til standard eller med navnet forekomst" i [konfigurere Windows Firewall for at tillade Analysis Services Access](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Genstart tabelformat forekomsten af Analysis Services-tjeneste.

Finde flere oplysninger i afsnittet **virtuelt slutpunkter og Firewall-porte** i dette dokument.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtuelt slutpunkter og Firewall-porte

Dette afsnit indeholder en oversigt over Microsoft Azure virtuelt slutpunkter til at oprette og porte der skal åbnes i virtuelt firewalls. Den følgende tabel opsummerer de **TCP** -porte til at oprette slutpunkter for og porte til at åbne i virtuelle maskiner firewallen.

- Hvis du bruger en enkelt VM og følgende to elementer er opfyldt, du behøver ikke at oprette VM slutpunkter, og du behøver ikke at åbne portene i firewallen på VM.

    - Fra en fjernplacering forbindelse ikke til SQL Server-funktioner på VM. Om oprettelse af en forbindelse til Fjernskrivebord til VM og få adgang til SQL Server-funktioner på VM lokalt betragtes ikke som en remote forbindelse til SQL Server-funktioner.

    - Du Deltag ikke VM til et lokalt domæne via Azure Virtual Networking eller en anden VPN-tunnelføring løsning.

- Hvis den virtuelle maskine ikke er tilsluttet et domæne, men du vil fra en fjernplacering opretter du forbindelse til SQL Server-funktioner på VM:

    - Åbn porte i firewallen på VM.

    - Oprette virtuelle maskine slutpunkter for de relevante porte (*).

- Hvis den virtuelle maskine er tilsluttet et domæne, ved hjælp af en VPN-tunnel som Azure Virtual Networking, derefter er slutpunkterne ikke påkrævet. Åbn dog porte i firewallen på VM.

  	|Port|Type|Beskrivelse|
|---|---|---|
|**80**|TCP|Rapportserver fjernadgang (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|SQL Server-browseren. Det er nødvendigt, når VM i tilsluttet et domæne.|
|**2382**|TCP|SQL Server-browseren.|
|**2383**|TCP|SQL Server Analysis Services standardforekomst og grupperet navngivne forekomster.|
|**Brugerdefinerede**|TCP|Oprette en statisk Analysis Services navnet forekomst port for et portnummer, du vælger, og derefter fjerne blokeringen af portnummeret i firewallen.|

Du kan finde flere oplysninger om oprettelse af slutpunkter, se følgende:

- Oprette slutpunkter:[hvordan du konfigurerer slutpunkter til en virtuel maskine](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server: Se afsnittet "Komplette konfiguration af trin til at oprette forbindelse til den virtuelle maskine ved hjælp af SQL Server Management Studio" i [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md).

I følgende diagram vises portene, der skal åbnes i VM firewall til at tillade fjernadgang til funktioner og -komponenter på VM.

![porte åbne til bi-programmer i Azure FOS](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Ressourcer

- Gennemse supportpolitikken for Microsoft-serversoftware bruges i Azure virtuelt miljø. I følgende emne indeholder en oversigt over understøttelse af funktioner som BitLocker, Failoverklyngedannelse og netværksbelastning. [Microsoft-serversoftware understøtter for virtuelle Azure-computere](http://support.microsoft.com/kb/2721672).

- [SQL Server på Azure virtuelle maskiner oversigt](virtual-machines-windows-sql-server-iaas-overview.md)

- [Virtuelle maskiner](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Hvordan du vedhæfter en datadisk til en virtuel maskine](virtual-machines-windows-classic-attach-disk.md)

- [Overflytte en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md)

- [Bestemme denne servertilstand af en Analysis Services-forekomst](https://msdn.microsoft.com/library/gg471594.aspx)

- [Multidimensionelle modellering (Adventure Works-selvstudium)](https://technet.microsoft.com/library/ms170208.aspx)

- [Azure dokumentation Center](https://azure.microsoft.com/documentation/)

- [Brug af Power BI i et hybridmiljø](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Sende feedback og kontaktoplysninger via Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Community-indhold

- [Azure SQL Database-styring med PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
