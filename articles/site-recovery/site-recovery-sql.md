<properties 
    pageTitle="Beskytte SQL Server med SQL Server nedbrud og Azure websted nedbrud | Microsoft Azure" 
    description="I denne artikel beskrives, hvordan du overfører SQL Server ved hjælp af muligheder for genoprettelse efter Azure websted gendannelse af SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Beskytte SQL Server med SQL Server nedbrud og Azure gendannelse af websteder 


Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md).

 I denne artikel beskrives, hvordan du beskytter SQL Server back-end i et program ved hjælp af en kombination af SQL Server BCDR-teknologier og Azure gendannelse af websteder. Du skal have en god forståelse af SQL Server genoprettelse efter nedbrud (failoverklynge, AlwaysOn tilgængelighed grupper Webdatabase spejling, logge levering) og Azure gendannelse af websteder, inden du installerer scenarier, der er beskrevet i denne artikel.



## <a name="overview"></a>Oversigt

Mange arbejdsbelastninger bruge SQL Server som en foundation. Programmer som SharePoint, Dynamics og SAP bruger SQL Server til at implementere datatjenester.  Programmer installere SQL Server i en række forskellige måder:

- **Enkeltstående SQL Server**: SQL Server og alle databaser hostes på en enkelt maskine (fysiske eller et virtuelt). Når virtualiseret, bruges host klynge til lokal høj tilgængelighed. Ingen gæst niveau høj tilgængelighed er implementeret.
- **SQL Server Failover klynge forekomster (altid på FCI)**: to eller flere noder af forekomster af SQL server med delte diske er konfigureret i en Windows sekundær klynge. Hvis et af de klynge forekomster er ned, kan klyngen mislykkedes over SQL Server til en anden forekomst. Denne konfiguration bruges typisk til HA på et primære websted. Den beskytte ikke mod fejl eller afbrydelse i det delte lagerplads lag. Delt disk kan implementeres bruger ISCSI, Fiber kanalen eller delt VHDx.
- **SQL altid på tilgængelighed grupper**: I denne konfiguration er to noder installationen er i en delt noget klynge med SQL Server-databaser, der er konfigureret i en tilgængelighed gruppe med synkron gentagelse og automatisk belastningsjustering.

I Enterprise Edition indeholder SQL Server også oprindelige nedbrud gendannelse teknologier til gendanne databaser til et eksternt websted. I denne artikel vil vi udnytte og integrere med disse oprindelige SQL nedbrud gendannelse teknologier: 

- SQL altid på tilgængelighed grupper for nedbrud for SQL Server 2012 eller 2014 Enterprise Edition.
- SQL databasespejling i høj sikkerhed tilstand for SQL Server Standard edition (alle versioner) eller SQL Server 2008 R2.


Gendannelse af websteder kan beskytte SQL Server, som opsummeret i tabellen.

 |**Lokalt til en lokal installation** | **Lokalt til Azure** 
---|---|---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja 
**Fysisk server** | Ja | Ja


## <a name="support-and-integration"></a>Support og integration

Disse SQL Server-version understøttes af scenarier i denne artikel:


- SQL Server 2014 Enterprise og Standard
- SQL Server 2012 Enterprise og Standard
- SQL Server 2008 R2 Enterprise og Standard


Gendannelse af websteder kan integreres med oprindelige SQL Server BCDR teknologier opsummeret i tabellen nedenfor for at give en løsning til genoprettelse efter genoprettelse.

**Funktion** |**Detaljer** | **SQL Server-version** 
---|---|---
**AlwaysOn tilgængelighed gruppe** | Flere enkeltstående forekomster af SQL Server køre i en sekundær klynge, der indeholder flere noder.<br/><br/>Databaser kan grupperes i failover grupper, der kan kopieres (spejlet) på forekomster af SQL Server så uden delte lager er det er nødvendigt.<br/><br/>Indeholder nedbrud mellem et primære niveau og en eller flere sekundære websteder. To noder kan konfigureres i en delt noget klynge med SQL Server-databaser, der er konfigureret i en tilgængelighed gruppe med synkron replikering og automatisk belastningsjustering. | SQL Server 2014 & 2012 Enterprise edition
**Failoverklynge (AlwaysOn FCI)** | SQL Server bruger Windows failoverklynge for høj tilgængelighed af lokal SQL Server arbejdsmængder.<br/><br/>Noder, der kører forekomster af SQL Server med delte diske er konfigureret i en sekundær klynge. Hvis en forekomst er nede skifter på klynge til andet tema.<br/><br/>Klyngen beskytte ikke mod fejl eller udfald i delt storage. Den delte disk kan implementeres med iSCSI, fiber kanal, eller en delt VHDXs. | SQL Server Enterprise-versioner<br/><br/>SQL Server Standard edition (begrænset til kun to noder)
**Database spejling (høj sikkerhed tilstand)** | Beskytter en enkelt database til en enkelt sekundær kopi. Tilgængelige i begge høj sikkerhed (synkron) og høj ydeevne (asynkron) gentagelse. Kræver ikke en sekundær klynge. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise alle udgaver
**Enkeltstående SQL Server** | SQL Server og database er placeret på en enkelt server (fysisk eller virtuel). Host klynge bruges til høj tilgængelighed, hvis serveren er virtuelle. Ingen gæst niveau høj tilgængelighed. | Enterprise- eller Standard edition

## <a name="deployment-recommendations"></a>Anbefalinger for implementering


I denne tabel kan du se vores anbefalinger til integration af SQL Server BCDR teknologier med gendannelse af websteder.

**Version** |**Edition** | **Installation** | **Lokalt til lokalt** | **Lokalt til Azure** 
---|---|---|---|---
SQL Server 2014 eller 2012 | Enterprise | Failoverklyngeforekomsten | AlwaysOn tilgængelighed grupper | AlwaysOn tilgængelighed grupper
 | Enterprise | AlwaysOn tilgængelighed grupper for høj tilgængelighed | AlwaysOn tilgængelighed grupper | AlwaysOn tilgængelighed grupper
 | Standard | Failoverklyngeforekomsten (FCI) | Gendannelse replikering med lokale spejling for websted | Gendannelse replikering med lokale spejling for websted
 | Enterprise- eller Standard | Enkeltstående | Websted gendannelse gentagelse | Websted gendannelse gentagelse
SQL Server 2008 R2 | Enterprise- eller Standard | Failoverklyngeforekomsten (FCI) | Gendannelse replikering med lokale spejling for websted | Gendannelse replikering med lokale spejling for websted
 | Enterprise- eller Standard | Enkeltstående | Websted gendannelse gentagelse | Websted gendannelse gentagelse
SQL Server (alle versioner) | Enterprise- eller Standard | Failoverklyngeforekomsten - DTC program | Websted gendannelse gentagelse | Ikke understøttet


## <a name="deployment-prerequisites"></a>Forudsætninger for installation

Her er, hvad du skal have inden du starter:

- En lokal SQL Server-installation med en understøttede SQL Server-version. Typisk skal du også kontakte en Active Directory til SQL server.
- Forudsætninger for dette scenario, du vil installere. Forudsætninger, der kan findes i hver installation artikel. Links til disse er angivet i [Oversigt over websted gendannelse](site-recovery-overview.md).
- Hvis du vil konfigurere Genoprettelse i Azure, skal du køre værktøjet [Vurdering af Azure virtuelt parathed](http://www.microsoft.com/download/details.aspx?id=40898) på din SQL Server virtuelle computere at sikre, at de er kompatible med Azure og gendannelse af websteder.


## <a name="set-up-active-directory"></a>Konfigurere Active Directory

Du skal bruge Active Directory på webstedet sekundær gendannelse til SQL Server kan køre korrekt. der er flere muligheder:

- **Lille virksomhed**– Hvis du har et lille antal programmer og en enkelt domænecontrolleren for webstedet i det lokale miljø, og du vil skifte over hele webstedet, anbefaler vi, at du bruger gendannelse af websteder repication for at gentage domænecontrolleren til det sekundære datacenter eller til Azure.

- **Mellemstore til store virksomheder**– Hvis du har et stort antal program, du kører en Active Directory-område, og du vil mislykkes forfra ved at programmet eller arbejdsbelastningen, anbefaler vi du konfigurerer et ekstra domænecontroller i det sekundære datacenter eller i Azure. Vær opmærksom på, hvis du bruger AlwaysOn tilgængelighed grupper til at gendanne til et eksternt websted vi anbefaler, at du har konfigureret en anden ekstra domænecontroller på sekundær websted eller Azure, skal bruges til gendannet SQL Server-forekomsten.

Vejledningen i dette dokument antages, at en domænecontrolleren er tilgængelig i den sekundære placering. [Læs mere](site-recovery-active-directory.md) om beskyttelse af Active Directory med gendannelse af websteder.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Integrere beskyttelse med SQL Server Always-On (lokale til Azure)


Gendannelse af websteder understøtter oprindeligt SQL AlwaysOn. Hvis du har oprettet en SQL tilgængelighed gruppe med en Azure virtuelt konfigurere som 'Sekundær' derefter kan du bruge gendannelse af websteder til at administrere sekundære af grupperne tilgængelighed. 

>[AZURE.NOTE] Denne funktion er i øjeblikket i Vis udskrift og tilgængelige når Hyper-V værtsservere i det primære datacenter administreres i VMM skyer, og når VMware konfiguration administreres af en [Server Configuration](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Nu er denne funktion ikke findes i den nye Azure portal.

#### <a name="prerequisites"></a>Forudsætninger

Her er, hvad du vil integrere SQL AlwaysOn med gendannelse af websteder:

- En lokal SQL Server (enkeltstående server eller en sekundær klynge).
- En eller flere Azure virtuelle maskiner med SQL Server er installeret
- En SQL tilgængelighed gruppe, der er konfigureret mellem en lokal SQL Server og SQL Server, der kører i Azure
- PowerShell remoting skal være aktiveret på computeren med SQL Server i det lokale miljø. VMM serveren eller Server Configuration skal kunne gøre remote PowerShell-opkald til SQL Server.
- En brugerkonto skal tilføjes på lokal SQL Server på disse SQL brugergrupper med mindst disse tilladelser:
    - ÆNDRE TILGÆNGELIGHEDEN: gruppetilladelser [her](https://msdn.microsoft.com/library/hh231018.aspx), og [her](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - tilladelser[her](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- En RunAs konto skal der oprettes på VMM Server eller en konto skal der oprettes på serveren konfiguration ved hjælp af CSPSConfigtool.exe for den bruger, der er nævnt i det forrige trin 
- Modulet SQL PS skal installeres SQL-servere, der kører i det lokale miljø, og af Azure virtuelle maskiner
- VM Agent skal være installeret virtuelle maskiner, der kører på Azure
- NTAUTHORITY\System skal have følgende tilladelser på SQL Server på virtuelle maskiner i Azure:
    - ÆNDRE tilgængelighed gruppe - tilladelser [her](https://msdn.microsoft.com/library/hh231018.aspx), og [her](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - ALTER DATABASE - tilladelser [her](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Trin 1: Tilføje en SQL Server


1. Klik på **Tilføj SQL** for at tilføje en ny SQL Server. 

    ![Tilføje SQL](./media/site-recovery-sql/add-sql.png)

2. I **Konfigurere indstillinger for SQL** > **navn** indeholder et fuldt navn til at referere til SQL Server.
3. **I SQL Server (fulde Domænenavn)** angive det fuldstændige Domænenavn for kilden SQL Server, som du vil tilføje. I tilfælde af SQL Server er installeret på en sekundær klynge, Giv dernæst fulde Domænenavn til klyngen og ikke til nogen af klyngenoderne.  
4. Vælg standardforekomsten i **SQL Server-forekomsten** eller angive navnet på den brugerdefinerede forekomst.
5. Vælg en VMM server eller konfiguration af Server, der er registreret i samling af legitimationsoplysninger gendannelse af websteder i **Management Server** . Gendannelse af websteder bruger denne indholdsstyringsserver til at kommunikere med SQL Server.
6. Angiv navnet på en RunAs-konto, der er oprettet på den angivne VMM server eller den konto, der er oprettet på serveren Configuraaaon **køre som konto** . Denne konto bruges til at få adgang til SQL Server og bør have læse-og Failover på tilgængelighed grupper på computeren med SQL Server.

    ![SQL-dialogboksen Tilføj](./media/site-recovery-sql/add-sql-dialog.png)

Når du har tilføjet den SQL Server vises den under fanen **SQL-servere** . 

![SQL Server-liste](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Trin 2: Tilføje en SQL tilgængelighed gruppe

1. Når SQL Server tilføjet maskine er næste trin er at føje grupperne tilgængelighed til gendannelse af websteder. Analysere ned i den SQL Server, der er tilføjet i forrige trin for at gøre det, og klik på Tilføj SQL tilgængelighed gruppe. 

    ![Tilføje SQL AG](./media/site-recovery-sql/add-sqlag.png)

2. SQL tilgængelighed gruppe kan replikeres til en eller flere virtuelle maskiner i Azure. Når du tilføjer gruppen sql tilgængelighed, du skal angive navnet og abonnement på den Azure virtuelle maskine, hvor du vil gruppen tilgængelighed for at kunne være ikke ved gendannelse af websteder.

    ![Tilføje SQL AG dialogboksen](./media/site-recovery-sql/add-sqlag-dialog.png)

3. I eksemplet ovenfor tilgængelighed gruppe Saldo 1-AG bliver primære på virtuelt SQLAGVM2 kører i abonnement DevTesting2 på en failover. 

>[AZURE.NOTE] Kun de tilgængelighed grupper, der er primære på den SQL Server, der er tilføjet i ovenstående trin findes der skal føjes til gendannelse af websteder. Hvis du har oprettet en tilgængelighed gruppe primære på SQL Server, eller hvis du har tilføjet flere tilgængelighed grupper på SQL Server, når den er blevet tilføjet, skal du opdatere den ved hjælp af indstillingen Opdater tilgængelig på SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Trin 3: Opret en Plan for gendannelse

Næste trin er at oprette en gendannelse plan ved hjælp af både virtuelle maskiner og grupperne tilgængelighed. Vælg den samme VMM Server eller konfiguration af Server, du har brugt i trin 1 som kilde- og Microsoft Azure som destination.

![Oprette en Plan](./media/site-recovery-sql/create-rp1.png)

![Oprette en Plan](./media/site-recovery-sql/create-rp2.png)

I eksemplet med består Sharepoint-programmet af 3 virtuelle maskiner, bruger en SQL tilgængelighed gruppe som dens backend-version. I denne gendannelse plan vi kunne vælge begge tilgængelighed gruppen samt den virtuelle maskine, der udgør programmet. 

Du kan yderligere tilpasse gendannelse planen ved at flytte virtuelle maskiner til forskellige failover grupper til at definere rækkefølgen rækkefølgen ydelse. Tilgængelighed gruppe er altid mislykkedes over første, som det skal bruges som en back-end for alle programmer. 

![Tilpasse en Plan](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Trin 4: Mislykkes over

Forskellige failover indstillinger er tilgængelige, når en tilgængelighed gruppe er blevet føjet til at planlægge en gendannelse.

Failover | Detaljer
--- | ---
**Planlagte failover** | Planlagte Failover betyder, at ingen datatab failover. For at opnå SQL tilgængelighed gruppens tilgængelighed tilstand først er indstillet til synkron og derefter en failover udløses for at gøre gruppen tilgængelighed primær til den virtuelle maskine, der er angivet under tilføjelse af gruppen tilgængelighed til gendannelse af websteder. Tilgængelighed tilstanden er indstillet til den samme værdi, når sekundære er fuldført, som den var, før den planlagte sekundære blev udløst.
**Ikke-planlagt failover** | Ikke-planlagt Failover kan resultere i tab af data. Mens udløser ikke-planlagt failover tilstanden tilgængelighed af gruppen tilgængelighed ændres ikke, og it foretages primære til den virtuelle maskine, der er angivet under tilføjelse af gruppen tilgængelighed til gendannelse af websteder. Når der ikke-planlagt failover er fuldført, og den lokale server, der kører SQL Server er tilgængelig igen, skal omvendt replikering udløses i gruppen tilgængelighed. Bemærk, at denne handling er ikke tilgængelig under gendannelse plan og kan blive ført på SQL tilgængelighed gruppe under fanen SQL-servere
**Test failover** | Test sekundær server til SQL tilgængelighed gruppe understøttes ikke. Hvis du udløse Test Failover af en gendannelse planlægge, der indeholder SQL tilgængelighed gruppe, vil blive ignoreret failover for tilgængelighed gruppe.


Overvej disse failover-indstillinger.

Indstillingen | Detaljer
--- | ---
**Valgmulighed 1** | 1. udføre en test failover af programmet og front end niveauer.<br/><br/>2. opdatere programmet niveau for at få adgang til replika kopi i skrivebeskyttet tilstand, og Udfør en skrivebeskyttet test af programmet.
**Mulighed 2** | 1. oprette en kopi af virtuelt forekomsten af replika SQL Server (ved hjælp af VMM Klon til Azure sikkerhedskopiering eller websted til websted) og finde den frem i en test-netværk<br/><br/> 2. Udfør den test sekundære bruger gendannelse plan.

Trin 5: Mislykkes tilbage

Hvis du vil gøre gruppen tilgængelighed igen primære på den lokale SQL Server kan du gøre det ved udløser planlagt Failover på gendannelse planlægge og vælge retningen fra Microsoft Azure til lokale VMM Server.

>[AZURE.NOTE] Når en ikke-planlagt failover har modsat gentagelse skal udløses i gruppen tilgængelighed til at genoptage replikering. Replikering forbliver afbrudt, indtil dette sker.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Beskytte maskiner uden en VMM Server eller en Server Configuration

Til miljøer, der ikke administreres af en VMM Server eller en Server Configuration, kan Azure automatiske Runbooks bruges til at konfigurere en script failover af SQL tilgængelighed grupper. Nedenfor finder du disse trin for at konfigurere, som:

1.  Oprette en lokal fil, som scriptet mislykkes over en tilgængelighed gruppe. Dette eksempelscript angiver en sti til gruppen tilgængelighed på Azure replikaen og skifter den til forekomsten replika. Dette script vil blive kørt på SQL Server replika virtual machine ved at overføre er med filtypenavnet brugerdefineret script.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Overføre scriptet til en blob på en Azure-lager-konto. Brug dette eksempel:

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Oprette en Azure automatiske runbook for at aktivere scripts på SQL Server replika virtuel maskine i Azure. Brug dette eksempelscript til at gøre dette. Du kan [Få mere at vide](site-recovery-runbook-automation.md) om brug af automatiske runbooks i gendannelse planer. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Når du opretter en gendannelse plan for programmet tilføje et "allerede gruppere 1 Start" script trin, der aktiverer automatisering runbook mislykkes over tilgængeligheden af grupper.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Integrere beskyttelse med SQL AlwaysOn (lokale til en lokal installation)

Hvis SQL Server bruger tilgængelighed grupper for høj tilgængelighed eller en failoverklyngeforekomsten, anbefaler vi, at du bruger tilgængelighed grupper på webstedet gendannelse. Bemærk, at denne vejledning til programmer, der ikke bruger distribuerede transaktioner.

1. [Konfigurer databaser](https://msdn.microsoft.com/library/hh213078.aspx) i tilgængelighed grupper.
2. Opret et nyt virtuelt netværk på sekundær websted.
3. Konfigurere et websted til websted VPN mellem det nye virtuelle netværk og det primære websted.
4. Oprette en virtuel maskine på webstedet gendannelse og installere SQL Server på den.
5. Udvide de eksisterende AlwaysOn tilgængelighed grupper til den nye SQL Server virtuelle maskine. Konfigurere denne forekomst af SQL Server som en asynkron replika kopi.
6. Oprette en tilgængelighed gruppe lytteren eller opdatere eksisterende lytteren at medtage virtuelt asynkron replika.
7. Sørg for, at programmet farmen er installation ved hjælp af lytteren. Hvis det er sat op ved hjælp af navnet på databaseserveren, skal du opdatere den for at bruge lytteren, så du ikke behøver at omkonfigurere efter sekundære.

Programmer, som bruger distribuerede transaktioner vi anbefaling du bruge [Gendannelse af websteder med SAN gentagelse](site-recovery-vmm-san.md) eller [VMWare/fysisk server til websted gentagelse](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Overvejelser i forbindelse med gendannelse plan

1. Tilføje dette eksempelscript til biblioteket VMM primære og sekundære-websteder.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Når du opretter en gendannelse plan for programmet tilføje et "allerede gruppere 1 Start" script trin, der aktiverer scriptet mislykkes over tilgængeligheden af grupper.



## <a name="protect-a-standalone-sql-server"></a>Beskytte en enkeltstående SQL Server

I denne konfiguration anbefales det, du bruger replikering gendannelse af websteder til at beskytte SQL Server-computeren. De nøjagtige trin, afhænger af, om SQL Server er konfigureret som et virtuelt eller fysisk server, og om du vil gentage til Azure eller et sekundært lokale websted. Få en vejledning alle installationer i [Oversigt over websted gendannelse](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Beskytte en SQL Server-klynge (Standard eller 2008 R2)

For en klynge, der kører SQL Server Standard edition eller SQL Server 2008 R2 anbefaler vi, du bruger replikering gendannelse af websteder til at beskytte SQL Server.

### <a name="on-premises-to-on-premises"></a>Lokalt til en lokal installation

- Hvis programmet bruger distribuerede transaktioner anbefaler vi du installerer [Gendannelse af websteder med SAN gentagelse](site-recovery-vmm-san.md) til en Hyper-V-miljø og [VMware/fysisk server til VMware](site-recovery-vmware-to-vmware.md) for VMware-miljø.

- Udnytte den ovenstående fremgangsmåde for at gendanne klyngen som en separat server ved at udnytte en lokal høj sikkerhed DB spejling til ikke-DTC programmer.

### <a name="on-premises-to-azure"></a>Lokalt til Azure

Gendannelse af websteder understøtter ikke gæst klynge support, når replikeres til Azure. SQL Server indeholder også ikke en løsning til lave omkostninger nedbrud genoprettelse til Standard edition. Vi anbefaler, at du har beskyttet lokal SQL Server-klynge til en separat SQL Server og gendanne den igen i Azure.


1. Konfigurere en ekstra enkeltstående SQL Server-forekomst på webstedet i det lokale miljø.
2. Konfigurere denne forekomst skal fungere som en spejling til de databaser, der har brug for beskyttelse. Konfigurere spejling tilstanden høj sikkerhed.
3.  Konfigurere gendannelse af websteder på det lokale websted baseret på miljøet ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) eller [VMware/fysisk server](site-recovery-vmware-to-azure-classic.md).
4.  Brug gentagelse gendannelse af websteder kan replikeres nye SQL Server-forekomsten til Azure. Det er en høj sikkerhed spejlvendt kopi og så skal synkroniseres med den primære klynge, men den kan replikeres til Azure ved hjælp af replikering gendannelse af websteder.

Følgende grafik viser denne konfiguration.

![Standard klynge](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Failback overvejelser i forbindelse med

SQL standard klynger, vil failback efter en ikke-planlagt failover kræver en SQL-sikkerhedskopi og gendanne fra den spejlede forekomst til den oprindelige klynge og genoprette spejlingen.

## <a name="next-steps"></a>Næste trin
Du kan [Få mere at vide](site-recovery-best-practices.md) om at være klar til at udrulle gendannelse af websteder.










 