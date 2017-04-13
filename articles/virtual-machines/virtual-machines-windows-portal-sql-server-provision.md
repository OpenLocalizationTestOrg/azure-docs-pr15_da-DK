<properties
    pageTitle="Klargøre en SQL Server virtuel maskine | Microsoft Azure"
    description="Oprette og oprette forbindelse til en SQL Server virtuel maskine i Azure ved hjælp af portalen. Dette selvstudium bruger tilstanden ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Klargøre en SQL Server virtuel maskine i portalen Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Selvstudiet til slut viser, hvordan du bruger portalen Azure til at klargøre en virtuel maskine, der kører SQL Server.

Galleriet Azure VM (virtual machine) indeholder flere billeder, der indeholder Microsoft SQL Server. Du kan vælge et af billederne SQL VM fra galleriet og klargøre det i dit miljø, Azure med nogle få klik.

I dette selvstudium skal du:

- [Vælge et billede af SQL VM fra galleriet](#select-a-sql-vm-image-from-the-gallery)
- [Konfigurere og oprette VM](#configure-the-vm)
- [Åbn VM med Fjernskrivebord](#open-the-vm-with-remote-desktop)
- [Oprette forbindelse til SQL Server fra en fjernplacering](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Vælge et billede af SQL VM fra galleriet

1. Log på [Azure-portalen](https://portal.azure.com) ved hjælp af din konto.

    >[AZURE.NOTE] Hvis du ikke har en Azure-konto, kan du besøge [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

1. Klik på **Ny**på Azure-portalen. På portalen åbnes bladet **Ny** . SQL Server VM ressourcer findes i gruppen **virtuelle maskiner** på Marketplace.

1. Klik på **virtuelle maskiner**i bladet **Ny** .

1. Hvis du vil se alle de tilgængelige billeder, skal du klikke på **se alle** på bladet **virtuelle maskiner** .

    ![Azure virtuelle maskiner Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Klik på **SQL Server**under **Database-servere**. Du skal muligvis rulle ned for at finde **Database-servere**. Gennemse de tilgængelige skabeloner i SQL Server.

    ![Galleri for virtuel maskine SQL billeder](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Hver skabelon identificerer en SQL Server-version og et operativsystem. Vælg en af disse billeder på listen. Gennemgå bladet oplysninger, der indeholder en beskrivelse af billedet, virtuelt.

    >[AZURE.NOTE] SQL VM billeder indeholder licenser omkostningerne til SQL Server til hver minutters priser VM du opretter. Der findes en anden indstilling for at få dine-ejer-licenser (BYOL) og betale kun for VM. Navnene billede er præfikset {BYOL}. Yderligere oplysninger om denne indstilling, kan du se [Introduktion til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).

1. Kontrollér, **Ressourceleder, der** er markeret, under **Vælg en implementeringsmodel**. Ressourcestyring er det anbefalede implementeringsmodel for nye virtuelle maskiner. Klik på **Opret**.

    ![Oprette SQL VM med ressourcestyring](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Konfigurere VM
Der findes fem blade til konfiguration af en SQL Server virtuel maskine.

| Trin               | Beskrivelse                          |
|---------------------|-------------------------------|
| **Grundlæggende funktioner**              | [Konfigurere grundlæggende indstillinger](#1-configure-basic-settings)      |
| **Størrelse**                | [Vælg virtuelt størrelse](#2-choose-virtual-machine-size)   |
| **Indstillinger**            | [Konfigurere valgfrie funktioner](#3-configure-optional-features)   |
| **Indstillinger for SQL Server** | [Konfigurere indstillinger for SQL server](#4-configure-sql-server-settings) |
| **Oversigt**             | [Gennemse oversigten](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. Konfigurer grundlæggende indstillinger
Angiv følgende oplysninger på bladet **grundlæggende** :

* Angiv et entydigt virtuelt **navn**.
* Angiv et **brugernavn** for den lokale administratorkonto på VM. Kontoen tilføjes også rollen SQL Server **systemadministrator** fast server.
* Angive en stærk **adgangskode**.
* Hvis du har flere abonnementer, skal du kontrollere, at abonnementet er korrekte for den nye VM.
* Skriv et navn til en ny ressourcegruppe i feltet **ressourcegruppe** . Du kan også hvis du vil bruge en eksisterende ressource gruppe skal du klikke på **Vælg eksisterende**. En ressourcegruppe er en samling af relaterede ressourcer i Azure (virtuelle maskiner, lager konti, virtuelt Network osv.).

    >[AZURE.NOTE] Ved hjælp af en ny ressourcegruppe er nyttigt, hvis du kun test eller lære om SQL Server-installationer i Azure. Når du er færdig med din test, kan du slette ressourcegruppe for automatisk at slette VM og alle de ressourcer, der er knyttet til ressourcegruppen. Se [Oversigt over Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md), du kan finde flere oplysninger om ressourcegrupper.

* Vælg en **placering** til denne installation.
* Klik på **OK** for at gemme indstillingerne.

    ![SQL grundlæggende Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Vælg virtuelt størrelse
Vælg et virtuelt størrelse i bladet **Vælg en størrelse** på trinnet **størrelse** . Bladet viser indledningsvis anbefalede maskine størrelser baseret på den skabelon, du har valgt. Det også beregner den månedlige pris for at køre VM.

![Indstillinger for SQL VM størrelse](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

For fremstilling arbejdsbelastninger anbefaler vi at vælge et virtuelt størrelse, der understøtter [Premium lagerplads](../storage/storage-premium-storage.md). Hvis du ikke kræver pågældende præstationsniveau, kan du bruge knappen **Vis alle** , som viser alle maskine størrelsesindstillinger. Du kan for eksempel bruge mindre maskine til en udvikling eller testmiljø.

>[AZURE.NOTE] Finde flere oplysninger om virtuelt størrelser, [størrelser til virtuelle computere](virtual-machines-windows-sizes.md). Du kan finde overvejelser i forbindelse med SQL Server VM størrelser, [ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md).

Vælg størrelsen på din computer, og klik derefter på **Vælg**.

## <a name="3-configure-optional-features"></a>3. Konfigurer valgfrie funktioner
Konfigurere Azure storage, netværk og overvågning til den virtuelle maskine på bladet **Indstillinger** .

- Angiv en **Disk Skriv** Standard eller Premium (SSD) under **lagerplads**. Premium lagerplads anbefales til fremstilling arbejdsmængder.

>[AZURE.NOTE] Hvis du vælger Premium (SSD) for en maskine størrelse, der ikke understøtter Premium lagerplads, ændres størrelsen af din computer automatisk.  

- Du kan acceptere kontonavn automatisk klargjort lager under **lagerplads konto**. Du kan også klikke på **lagerplads konto** skal du vælge en eksisterende konto og Konfigurer kontotypen lagerplads. Som standard opretter Azure en ny lagerplads konto med lokalt overflødige lagerplads. Du kan finde flere oplysninger om indstillinger for lagring af, [Azure-lager gentagelse](../storage/storage-redundancy.md).

- Under **netværk**skal acceptere du de automatisk på forhånd værdier. Du kan også klikke på hver funktion til at konfigurere den **virtuelle netværk**, **undernet**, **offentlige IP-adresse**og **Netværk sikkerhedsgruppe**manuelt. Bevar standardværdierne med henblik på dette selvstudium.

- Azure aktiverer **overvågnings** som standard med den samme lagerplads-konto, der er angivet af VM. Du kan ændre disse indstillinger her.

- Under **angive tilgængelighed**, skal du angive en tilgængelighed sæt. I forbindelse med dette selvstudium, kan du vælge **ingen**. Hvis du vil konfigurere SQL AlwaysOn tilgængelighed grupper, kan du konfigurere tilgængeligheden for at undgå at genskabe den virtuelle maskine.  Se [administrere tilgængelighed af virtuelle maskiner](virtual-machines-windows-manage-availability.md)kan finde flere oplysninger.

Når du er færdig konfiguration af disse indstillinger, klik på **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Konfigurer SQL server-indstillinger
Konfigurere specifikke indstillinger og optimeringer til SQL Server på bladet **SQL Server-indstillingerne** . De indstillinger, du kan konfigurere til SQL Server omfatter følgende.

| Indstilling               |
|---------------------|
| [Forbindelse](#connectivity)              |
| [Godkendelse](#authentication)                |
| [Konfigurationsproblemer med datalagring](#storage-configuration)            |
| [Automatisk rettelse](#automated-patching) |
| [Automatisk sikkerhedskopiering](#automated-backup)             |
| [Azure vigtige samling Integration](#azure-key-vault-integration)             |
| [R tjenester](#r-services) |

### <a name="connectivity"></a>Forbindelse
Angiv typen adgang, du vil SQL Server-forekomsten på denne VM under **SQL connectivity**. Vælge **offentlige (internet)** til at tillade forbindelser til SQL Server-computere eller tjenester på internettet med henblik på dette selvstudium. Med denne indstilling er valgt, konfigurerer Azure automatisk firewallen og sikkerhedsgruppen netværk, der tillader trafik på port 1433.  

![Indstillinger for SQL-forbindelse](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Hvis du vil oprette forbindelse til SQL Server via internettet, skal også du aktivere SQL Server-godkendelse, der er beskrevet i næste afsnit.

>[AZURE.NOTE] Det er muligt at føje flere begrænsninger for netværkskommunikationen til din SQL Server VM. Du kan gøre dette ved at redigere sikkerhedsgruppen netværk, når VM er blevet oprettet. Du kan finde flere oplysninger, se [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)

Hvis du foretrækker at ikke aktiverer forbindelser til Database Engine via internettet, kan du vælge en af følgende indstillinger:

- **Lokal (i kun VM)** til at tillade forbindelser til SQL Server kun fra VM.
- **Privat (inden for Virtual Network)** til at tillade forbindelser til SQL Server-computere eller tjenester i det samme virtuelle netværk.

>[AZURE.NOTE] Et virtuelt billede for SQL Server Express edition aktiverer ikke automatisk TCP/IP-protokollen. Dette gælder også for indstillingerne offentlige og Private forbindelse. For Express edition, skal du bruge SQL Server Configuration Manager [manuelt](#configure-sql-server-to-listen-on-the-tcp-protocol) aktivere TCP/IP-protokollen, når du har oprettet VM.

Generelt kan forbedre sikkerheden ved at vælge den mest restriktiv forbindelse, der giver mulighed for det pågældende scenarie. Men alle indstillingerne er der kan sikres gennem netværk sikkerhedsgruppe regler og SQL/Windows-godkendelse.

Som standard **port** 1433. Du kan angive et andet portnummer.
Du kan finde flere oplysninger, se [Opret forbindelse til en SQL Server virtuel maskine (ressourcestyring) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Godkendelse
Hvis du kræver SQL Server-godkendelse, skal du klikke på **Aktivér** under **SQL-godkendelse**.

![SQL Server-godkendelse](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Hvis du planlægger at få adgang til SQL Server via internettet (det vil sige indstillingen forbindelse til offentlige), skal du aktivere SQL-godkendelse her. Offentlig adgang til SQL Server kræver brug af SQL-godkendelse.

Hvis du aktiverer SQL Server-godkendelse, angive et **Login-navn** og en **adgangskode**. Dette brugernavn er konfigureret som en SQL Server-godkendelse logon og medlem af **systemadministrator** fast serverrolle. Se [vælge en anden godkendelsestilstand](http://msdn.microsoft.com/library/ms144284.aspx) finde flere oplysninger om godkendelse funktionsmåder.

Hvis du ikke aktiverer SQL Server-godkendelse, kan du bruge den lokale administratorkonto på VM at oprette forbindelse til SQL Server-forekomsten.

### <a name="storage-configuration"></a>Konfigurationsproblemer med datalagring
Klik på **lagerplads konfiguration** for at angive lagerplads kravene.

![Konfiguration af SQL-lager](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Hvis du vælger Standard lagerplads, er denne indstilling er ikke tilgængelig. Automatisk lagring optimering er kun tilgængelig for Premium lagerplads.

Du kan angive krav som input/output-operationer sekundet (IOP'er) overførselshastighed i MB/s og samlede lagerstørrelse. Konfigurere disse værdier ved hjælp af de glidende ubalance. På portalen beregner automatisk antallet af diske, der er baseret på disse krav.

Som standard optimerer Azure opbevaring til 5000 IOP'er, 200 MB og 1 TB lagerplads. Du kan ændre disse indstillinger for lagring af baseret på arbejdsbelastningen. Vælg en af følgende indstillinger under **lagerplads optimeret til**:

- **Generelt** er standardindstillingen og understøtter arbejdsbelastninger, som de fleste.
- **Transaktion** behandling optimerer lagerplads på traditionelle database OLTP arbejdsmængder.
- **Datalagring** optimerer lagerpladsen for arbejdsbelastninger, som analytisk og rapportering.

>[AZURE.NOTE] De øvre grænser på skyderne varierer afhængigt af størrelsen på dine valgte virtuelt.

### <a name="automated-patching"></a>Automatisk rettelse
**Automatisk rettelse** er aktiveret som standard. Automatiske rettelser kan Azure til automatisk programrettelse SQL Server og operativsystemet. Angiv en dag i ugen, tidspunkt og varighed for et vedligeholdelsesvindue. Azure udfører rettelse i vinduet vedligeholdelse. Vedligeholdelse vinduet tidsplanen bruger VM landestandard for tid. Hvis du ikke vil Azure til automatisk programrettelse SQL Server og operativsystemet, skal du klikke på **Deaktiver**.  

![SQL automatisk rettelse](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Du kan finde flere oplysninger, se [Automatisk rettelse til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Automatisk sikkerhedskopiering
Aktivere automatiske sikkerhedskopier af databasen for alle databaser under **automatisk backup**. Automatisk sikkerhedskopiering er aktiveret som standard.

Når du aktiverer SQL automatiseret backup, kan du konfigurere følgende:

- Opbevaringsperiode (dage) for sikkerhedskopiering
- Lagerplads konto der skal bruges for sikkerhedskopiering
- Krypteringsindstilling for og adgangskode for sikkerhedskopiering

Hvis du vil kryptere sikkerhedskopien, skal du klikke på **Aktivér**. Angiv **adgangskode**. Azure opretter et certifikat for at kryptere sikkerhedskopierne og bruger den angivne adgangskode til at beskytte dette certifikat.

![SQL automatisk Backup](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Du kan finde yderligere oplysninger finder [Automatisk sikkerhedskopiering til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Azure-tasten samling-integration
Klik på **Azure vigtige samling integration** for at gemme sikkerhed hemmeligheder i Azure til kryptering, og klik på **Aktivér**.

![Integration af SQL Azure vigtige samling](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

I følgende tabel vises de parametre, der kræves for at konfigurere Azure-tasten samling Integration.

|PARAMETER|BESKRIVELSE|EKSEMPEL|
|----------|----------|-------|
|**Vigtige samling URL-adresse** |Placeringen af den vigtigste samling af legitimationsoplysninger.|https://contosokeyvault.Vault.Azure.NET/ |
|**Hovednavn** |Azure Active Directory tjenestens hovednavn. Dette navn er også kaldes klient-ID.  |fde2b411 - 33d 5-4e11-af04eb07b669ccf2|
| **Vigtigste hemmeligt**|Azure Active Directory-tjenesten vigtigste hemmeligt. Denne hemmeligt kaldes også klient hemmeligheden. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Legitimationsoplysninger navn**|**Legitimationsoplysninger for navn**: AKV Integration opretter en legitimationsoplysninger i SQL Server, så VM skal have adgang til den vigtige samling af legitimationsoplysninger. Vælg et navn for denne legitimationsoplysninger.| mycred1|

Se [Konfigurere Azure nøgle samling Integration til SQL Server på Azure FOS](virtual-machines-windows-ps-sql-keyvault.md)kan finde flere oplysninger.

Klik på **OK**, når du er færdig med at konfigurere SQL Server-indstillinger.

### <a name="r-services"></a>R tjenester
SQL Server 2016 Enterprise edition har du mulighed for at aktivere [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx). Dette gør det muligt at bruge avancerede analytics med SQL Server 2016. Klik på **Aktivér** i bladet **SQL Server-indstillinger** .

![Aktivere SQL Server R Services](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Mulighed for at aktivere R Services er deaktiveret for SQL Server-billeder, der ikke er 2016 Enterprise edition.

## <a name="5-review-the-summary"></a>5. Gennemse oversigten
Gennemse oversigten bladet **Oversigt** , og klik på **OK** for at oprette SQL Server, ressourcegruppe og ressourcer, der er angivet for denne VM.

Du kan overvåge installationen fra azure-portalen. Knappen **beskeder** øverst på skærmen viser grundlæggende status for installationen.

>[AZURE.NOTE] For at give dig en ide på installation gange, implementeret jeg en SQL VM til indtastning af østasiatiske USA område med standardindstillinger. Denne test installation tog 26 minutter at gennemføre alt. Men du kan opleve en hurtigere eller langsommere installation tid baseret på dit område og valgt indstillinger.

## <a name="open-the-vm-with-remote-desktop"></a>Åbn VM med Fjernskrivebord

Brug følgende trin til at oprette forbindelse til den virtuelle maskine med Fjernskrivebord:

1. Når Azure VM er oprettet, vises ikonet for VM i dashboardet Azure. Du kan også finde det ved at gennemse dine eksisterende virtuelle computere. Klik på din nye SQL virtuelle maskine. Et **virtuelt** blade vises virtuel maskine få.
1. Klik på **Forbind**øverst i bladet **virtuelt** .
1. Browseren henter en RDP-fil til VM. Åbn filen RDP.
    ![Remote Desktop til SQL VM](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. Forbindelse til Fjernskrivebord giver dig besked, ikke kan blive identificeret udgiver af denne remote forbindelse. Klik på **Opret forbindelse** til at fortsætte.
1. Klik på **Brug en anden konto**i dialogboksen **Windows sikkerhed** .
1. **Brugernavn** type ** \<brugernavn >**, hvor <user name> er det brugernavn, du angav, da du konfigurerede VM. Du skal føje en indledende omvendt skråstreg foran navnet.
1. Skriv den **adgangskode** , du tidligere har konfigureret for denne VM, og klik derefter på **OK** for at oprette forbindelse.
1. Hvis en anden **Fjernskrivebord** dialogboks beder dig om at oprette forbindelse, skal du klikke på **Ja**.

Når du opretter forbindelse til den SQL Server virtuelle maskine, kan du Start SQL Server Management Studio og oprette forbindelse til Windows-godkendelse ved hjælp af din lokale administratorrettigheder. Hvis du har aktiveret SQL Server-godkendelse, kan du også oprette forbindelse med SQL-godkendelse med SQL-logon og adgangskode, du har konfigureret under klargøring.

Adgang til maskinen gør det muligt at ændre direkte maskine og SQL Server-indstillingerne efter behov. For eksempel kan du konfigurere indstillingerne for firewallen eller ændre indstillingerne for SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Oprette forbindelse til SQL Server fra en fjernplacering

I dette selvstudium udvalgte vi **offentlige** adgangen til virtuelt og **SQL Server-godkendelse**. Disse indstillinger konfigureres automatisk den virtuelle maskine for at tillade SQL Server-forbindelser fra enhver klient via internettet (hvis de har den korrekte SQL-logon).

>[AZURE.NOTE] Hvis du ikke har valgt offentlige under klargøringen, kræves ekstra trin for at få adgang til dine SQL Server-forekomst via internettet. Yderligere oplysninger finder du se [forbinde til en SQL Server virtuel maskine](virtual-machines-windows-sql-connect.md).

De følgende afsnit viser, hvordan du opretter forbindelse til din SQL Server-forekomst på din VM fra en anden computer via internettet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Næste trin
Se andre oplysninger om brug af SQL Server i Azure [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md) og [Ofte stillede spørgsmål](virtual-machines-windows-sql-server-iaas-faq.md).

En video oversigt over SQL Server på virtuelle Azure-computere, se [Azure VM er den bedste platform til SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Gennemse læringssti](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) til SQL Server på Azure virtuelle computere.
