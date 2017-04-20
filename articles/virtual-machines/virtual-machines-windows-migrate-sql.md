<properties
    pageTitle="Overføre en SQL Server-database til SQL Server på en VM | Microsoft Azure"
    description="Lær, hvordan du overfører en lokal brugerdatabase til SQL Server i en Azure virtual machine."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Overføre en SQL Server-database til SQL Server i en Azure-VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Resource Manager-model.


Der er et antal metoder for at overflytte en bruger lokale SQL Server-database til SQL Server i en Azure-VM. Denne artikel vil kort diskutere forskellige metoder, anbefaler den bedste metode til forskellige scenarier og omfatter et [selvstudium](#azure-vm-deployment-wizard-tutorial) fører dig ved hjælp af guiden **installation af SQL Server-Database til et Microsoft Azure VM** . 

Den metode, ved hjælp af guiden **installation af SQL Server-Database til et Microsoft Azure VM** , der er beskrevet i [selvstudiet](#azure-vm-deployment-wizard-tutorial) er for modellen Klassisk installation. 

## <a name="what-are-the-primary-migration-methods"></a>Hvad er de primære overflytningsfil metoder?

De primære overflytningsfil metoder er:

- Du kan bruge en SQL Server-Database til Microsoft VM Azure-guiden Installation
- Udføre lokal sikkerhedskopiering ved hjælp af komprimering og manuelt kopiere sikkerhedskopien til Azure virtuel maskine
- Foretag en sikkerhedskopiering til URL-adressen og gendannelse i Azure virtual machine fra URL-adressen
- Fjerne og derefter kopiere filerne data- og logfiler til Azure blob storage og derefter vedhæfte til SQL Server i Azure VM fra URL-adresse
- Konvertere på stedet fysisk maskine til Hyper-V VHD, overføre til Azure Blob storage og derefter installere som ny VM ved hjælp af overført virtuel harddisk
- Levere harddisken ved hjælp af Windows Importer/eksporter Service
- Hvis du har en AlwaysOn installation på stedet, skal du bruge [Tilføj Azure replika guiden](virtual-machines-windows-classic-sql-onprem-availability.md) til at oprette en replika i Azure og failover, pege på Azure databaseforekomsten brugere
- SQL Server- [transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) kan bruge til at konfigurere Azure SQL Server-forekomst som en abonnent og derefter deaktivere replikering, pege på Azure databaseforekomsten brugere



## <a name="choosing-your-migration-method"></a>Hvis du vælger en overflytningsmetode

For optimal ydeevne ved dataoverførsel er overflytning af databasefilerne til en komprimeret sikkerhedskopifil Azure VM som regel den bedste metode. Dette er den metode, der bruger [en SQL Server-Database til et Microsoft VM Azure guiden Installation](#azure-vm-deployment-wizard-tutorial) . Denne guide er den anbefalede metode til at overflytte et lokale bruger-database, der kører på SQL Server 2005 eller til større til SQL Server-2014 eller større, når komprimeret database sikkerhedskopi-filen er mindre end 1 TB.

For at minimere nedetid under overflytningsprocessen database, skal du bruge enten den AlwaysOn eller indstillingen transaktionsreplikering.

Hvis det ikke er muligt at bruge de ovennævnte metoder, manuelt flytte databasen. Hvis du bruger denne metode, du normalt starter med en sikkerhedskopi af databasen, efterfulgt af en kopi af den databasen sikkerhedskopiering i Azure og derefter udføre en databasegendannelse af. Du kan også kopiere databasefiler sig til Azure og derefter vedhæfte dem. Der flere metoder, som du kan udføre manuel processen for overflytning af en database til en Azure-VM.

> [AZURE.NOTE] Når du opgraderer til SQL Server-2014 eller SQL Server-2016 fra ældre versioner af SQL Server, bør du overveje, om det er nødvendigt at foretage ændringer. Vi anbefaler, at du håndterer alle afhængigheder af funktioner, der ikke understøttes af den nye version af SQL Server som en del af dit migreringsprojekt. Yderligere oplysninger om understøttede versioner og scenarier, du [opgradere til SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Følgende tabel viser en oversigt over hver af de primære overflytningsfil metoder og beskriver når brugen af hver metode er mest passende.

| Metode  | Databaseversionen kilde  |  Destinationsversionen af database | Source database backup størrelse begrænsning  | Noter  |
|---|---|---|---|---|
| [Du kan bruge en SQL Server-Database til Microsoft VM Azure-guiden Installation](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 eller nyere | SQL Server 2014 eller nyere | < 1 TB  | Hurtigste og nemmeste metode bruges, når det er muligt at overflytte til en ny eller eksisterende SQL Server-forekomst i en Azure virtual machine | 
| [Brug af Azure replika guiden Tilføj](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 eller nyere | SQL Server 2012 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Minimerer nedetid, skal bruges, når du har en AlwaysOn lokale installation |
| [Brug SQL Server-transaktionsreplikering](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 eller nyere | SQL Server 2005 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Bruges, når du har brug at minimere nedetid og ikke har en AlwaysOn lokale installation |
| [Udføre lokal sikkerhedskopiering ved hjælp af komprimering og manuelt kopiere sikkerhedskopien til Azure virtuel maskine](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 eller nyere | SQL Server 2005 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Brug kun når du kan ikke bruge guiden, f.eks når databaseversionen destination er mindre end SQL Server 2012 SP1 CU2 eller database backup er større end 1 TB (Terabyte 12,8 med SQL Server-2016) |
| [Foretag en sikkerhedskopiering til URL-adressen og gendannelse i Azure virtual machine fra URL-adressen](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 eller større | SQL Server 2012 SP1 CU2 eller større | < 12,8 TB til SQL Server-2016, ellers < 1 TB | Generelt ved hjælp af [Sikkerhedskopiering til URL-adressen](https://msdn.microsoft.com/library/dn435916.aspx) er ækvivalente i ydeevnen ved hjælp af guiden og ikke helt så let |
| [Fjerne og derefter kopiere filerne data- og logfiler til Azure blob storage og derefter vedhæfte til SQL Server i Azure virtual machine fra URL-adresse](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 eller nyere | SQL Server 2014 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Brug denne metode, når du har planer om at [gemme filerne ved hjælp af tjenesten Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx) og knytte dem til SQL Server, der kører i en Azure VM, især med meget store databaser |
| [Konvertere lokal maskine til Hyper-V virtuelle harddiske, overføre til Azure Blob storage og derefter installere en ny virtuel maskine, der bruger Virtuelle overførte](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 eller nyere | SQL Server 2005 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Bruges, når du [bringer din egen SQL Server-licens](../sql-database/sql-database-paas-vs-sql-server-iaas.md), når du overfører en database, som du vil køre på en ældre version af SQL Server, eller når du overfører systemet og bruger databaser sammen som en del af overførslen af databasen, der er afhængige af andre brugerdatabaser og/eller systemdatabaser. |
| [Levere harddisken ved hjælp af Windows Importer/eksporter Service](#ship-hard-drive) | SQL Server 2005 eller nyere | SQL Server 2005 eller nyere | [Lagergrænsen for Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Bruge [Windows Importer/eksporter Service](../storage/storage-import-export-service.md) , når manuel kopieringsmetode går for langsomt, som med meget store databaser |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Guide til installation af Azure VM selvstudium

Brug guiden **Installer SQL Server-Database til et Microsoft Azure VM** i Microsoft SQL Server Management Studio til at overføre en SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server-2014 eller SQL Server-2016 lokale bruger-database (op til 1 TB) til SQL Server-2014 eller SQL Server-2016 i en Azure virtual machine. Du kan bruge denne guide til at overflytte en brugerdatabase til en eksisterende Azure virtuel maskine eller til en Azure VM med SQL Server, der er oprettet af guiden under overflytningen. Når du overfører en database til en nyere version af SQL Server, opgraderet databasen automatisk under processen.

Metoden er den klassiske installation model. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Hent seneste Version af installation en SQL Server-Database til en Microsoft VM Azure-guide

Brug den nyeste version af Microsoft SQL Server Management Studio til SQL Server til at sikre, at du har den nyeste version af guiden **installation af SQL Server-Database til et Microsoft Azure VM** . Den nyeste version af denne guide indeholder de seneste opdateringer til portalen Azure klassiske og understøtter de nyeste Azure VM-billeder i galleriet (ældre versioner af guiden virker ikke). Hent den nyeste version af Microsoft SQL Server Management Studio til SQL Server, [hente den](http://go.microsoft.com/fwlink/?LinkId=616025) og installere den på en klientcomputer med forbindelse til den database, du planlægger at overføre og internettet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Konfigurer den eksisterende Azure virtuelle maskine og SQL Server-forekomsten (hvis relevant)

Hvis du overfører til en eksisterende VM Azure, skal følgende konfigurationstrin, der kræves:

- Konfigurere VM Azure og SQL Server-forekomsten for at aktivere forbindelser fra en anden computer ved at følge trinnene til at [oprette forbindelse til forekomsten af SQL Server VM fra SSMS på en anden computer](virtual-machines-windows-sql-connect.md). 2014 til SQL Server og SQL Server 2016 billederne i galleriet understøttes, hvis du overflytter ved hjælp af guiden.
- Konfigurere et slutpunkt, der er åbne for tjenesten SQL Server-netværkskort med sky på Microsoft Azure-gatewayen med private port af 11435. Denne port er oprettet som en del af SQL Server-2014 eller SQL Server-2016 klargøring af en Microsoft Azure VM. Sky adapteren opretter også en Windows Firewall-reglen, så dens indgående TCP-forbindelser på standardport 11435. Dette slutpunkt kan guiden for at udnytte tjenesten Cloud adapter for at kopiere sikkerhedskopier af filer fra den lokale forekomst til VM Azure. Yderligere oplysninger finder du under [Sky Adapter til SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Oprette sky Adapter slutpunkt](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Kør Brug Installer SQL Server-Database til en Microsoft VM Azure-guide

1. Åbn Microsoft SQL Server Management Studio til Microsoft SQL Server-2016 og oprette forbindelse til den SQL Server-forekomst, der indeholder brugerdatabasen, som du vil overflytte til en Azure-VM.
2. Højreklik på den database, du overfører, peg på opgaver, og klik derefter på Installer til en Microsoft Azure VM.

    ![Start guiden](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Klik på Næste på siden Introduktion.
4. Oprette forbindelse til den SQL Server-forekomst, der indeholder den database, du vil overføre til en Azure VM på siden Indstillinger for kilde.
5. Angiv en midlertidig placering til sikkerhedskopierne. Hvis du opretter forbindelse til en fjernserver, skal du angive et netværksdrev.

    ![Indstillinger for kilde](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Klik på Næste.
7. Klik på Log på og logge på Azure-konto på siden Microsoft Azure-logon.
8. Vælg det abonnement, du vil bruge, og klik på Næste.

    ![Azure-logon](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. På siden Indstillinger for installation, kan du angive en ny eller en eksisterende skybaseret tjeneste navnet og Virtual Machine:

 - Angiv et nyt navn for sky tjenestenavnet og Virtual Machine til at oprette en ny Cloud-tjeneste med en ny Azure virtuel maskine ved hjælp af en SQL Server-2014 eller 2016 Galleri for SQL Server-afbildning.

     - Hvis du angiver et nyt navn til Cloud-tjeneste, kan du angive den konto i lagerplads, du vil bruge.

     - Hvis du angiver navnet på en eksisterende skybaseret tjeneste, hentes storage-konto og du har angivet.

 - Angiv en eksisterende skybaseret tjeneste navn og nye virtuelle maskinnavn til at oprette en ny Azure virtuel maskine i en eksisterende skybaseret tjeneste. Kun angive en SQL Server-2014 eller SQL Server-2016 galleri billede.
 - Angiv en eksisterende skybaseret tjeneste navnet og Virtual Machine til at bruge en eksisterende Azure virtuel maskine. Det skal et billede, der er oprettet ved hjælp af en SQL Server-2014 eller SQL Server-2016 galleri billede.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Klik på Indstillinger
  - Hvis du har angivet en eksisterende skybaseret tjeneste navnet og Virtual Machine, bliver du bedt om at angive brugernavn og adgangskode.

        ![Azure computerindstillinger.](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Hvis du har angivet et nyt navn til Virtual Machine, bliver du bedt om at vælge et billede fra listen over billeder, galleri og angive følgende oplysninger:
      - Image – Vælg SQL Server-2014 eller SQL Server-2016
        - Brugernavn
        - Ny adgangskode
        - Bekræft adgangskode
        - Placering
        - Størrelse.
    - Desuden Klik for at acceptere selv oprettede certifikatet for denne nye Microsoft Azure Virtual Machine, og klik derefter på OK.

    ![Azure nye maskinindstillinger](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Angiv databasenavnet mål, hvis det er forskelligt fra navnet på kildedatabasen. Hvis der allerede findes i destinationsdatabasen, systemet vil automatisk forøges databasenavn i stedet for overskrive den eksisterende database.
12. Klik på Næste, og klik derefter på Udfør.

    ![Resultater](./media/virtual-machines-windows-migrate-sql/results.png)

13. Når guiden er fuldført, kan du oprette forbindelse til din virtuelle maskine og Kontroller, at databasen er blevet overflyttet.
14. Hvis du har oprettet en ny virtuel maskine, kan du konfigurere virtual machine til Azure og SQL Server-forekomsten ved at følge trinnene til at [oprette forbindelse til forekomsten af SQL Server VM fra SSMS på en anden computer](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Sikkerhedskopiering af filer og kopier til VM og gendannelse

Brug denne metode, når du ikke bruger en SQL Server-Database til et Microsoft VM Azure guiden Installation enten fordi du overfører til en version af SQL Server, inden SQL Server-2014 eller sikkerhedskopien er større end 1 TB. Hvis sikkerhedskopien er større end 1 TB, skal du fordeler den, fordi den maksimale størrelse på en VM-disk 1 TB. Brug følgende generelle trin til at overføre en brugerdatabase, ved hjælp af denne måde:

1.  Udføre en fuld sikkerhedskopi til en lokal placering.
2.  Opret eller overføre en virtuel maskine med versionen af SQL Server, der ønskes.
3.  Opsætning af tilslutning baseret på dine krav. Se [oprette forbindelse til en SQL Server virtuel maskine på Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
4.  Kopier filerne backup til dine VM via Fjernskrivebord, Windows Stifinder eller kopieringskommandoen fra en kommandoprompt.

## <a name="backup-to-url-and-restore"></a>Sikkerhedskopiering og gendannelse af URL-adresse

Bruge metoden [backup til URL-adresse](https://msdn.microsoft.com/library/dn435916.aspx) , når du ikke bruge en SQL Server-Database til et Microsoft VM Azure guiden Installation, fordi sikkerhedskopien er større end 1 TB, og du overfører fra og til SQL Server-2016. Til mindre end 1 TB eller kører en version af SQL Server inden 2016 til SQL Server-databaserne, anbefales brug af guiden. Med SQL Server 2016 der spredte sikkerhedskopisæt understøttes, anbefales til ydeevne og skal være større end maksimale størrelse pr. blob. Brug af [Windows Importer/eksporter Service](../storage/storage-import-export-service.md) anbefales til meget store databaser.

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Fjerne og kopiere til URL-adressen og knytte fra URL-adresse

Brug denne metode, når du har planer om at [gemme filerne ved hjælp af tjenesten Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx) og knytte dem til SQL Server, der kører i en Azure VM, især med meget store databaser. Brug følgende generelle trin til at overføre en brugerdatabase, ved hjælp af denne måde:

1.  Fjerne tilknytningen af databasefiler fra den lokale databaseforekomst.
2.  Kopier filerne frakoblet database til Azure blob storage ved hjælp af [kommandolinjefunktionen for AZCopy](../storage/storage-use-azcopy.md).
3.  Vedhæfte databasefilerne fra Azure URL-adressen til SQL Server-forekomsten på Azure-VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konverter til VM og overføre til URL-adresse og installere som nye VM

Brug denne metode til at overflytte alle system- og databaser i en lokal SQL Server-forekomst til Azure virtuel maskine. Brug følgende generelle trin til at overflytte en hel forekomst af SQL Server ved hjælp af denne manuelle metode:

1.  Konvertere fysiske og virtuelle maskiner til Hyper-V virtuelle harddiske ved at bruge [Konverteringsprogrammet til Microsoft Virtual Machine](http://technet.microsoft.com/library/dn873998.aspx).
2.  Overføre Virtuelle harddiskfiler ved hjælp af [Tilføj-AzureVHD-cmdlet](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)til Azure Storage.
3.  Implementer en ny virtuel maskine ved hjælp af den overførte virtuelle harddisk.

> [AZURE.NOTE] Hvis du vil overføre hele programmet, kan du bruge [Azure gendannelse af websteder](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Skibet harddisk

Brug [Windows Importer/eksporter en metode](../storage/storage-import-export-service.md) til at overføre store mængder data fra en fil til Azure Blob storage i situationer, hvor overførslen via netværket er uoverkommeligt dyre eller ikke muligt. Med denne tjeneste kan sende du en eller flere harddiske, der indeholder dataene til en Azure datacenter, hvor dine data vil blive overført til din konto i lagerplads.

## <a name="next-steps"></a>Næste trin

Se [SQL Server på Azure virtuelle maskiner oversigt over](virtual-machines-windows-sql-server-iaas-overview.md)yderligere oplysninger om at køre SQL Server på Azure virtuelle maskiner.

Vejledning i oprettelse af en Azure SQL Server Virtual Machine fra indfangede billede, kan du se [tip og Tricks på 'kloning' SQL Azure virtuelle maskiner fra hentede billeder](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) på bloggen CSS SQL Server-teknikere.
