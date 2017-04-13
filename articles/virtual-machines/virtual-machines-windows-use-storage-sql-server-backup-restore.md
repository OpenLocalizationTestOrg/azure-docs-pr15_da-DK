<properties
    pageTitle="At bruge Azure lagerplads til SQL Server-sikkerhedskopiering og gendannelse | Microsoft Azure"
    description="Lær at sikkerhedskopiere SQL Server til Azure-lager. Beskriver fordelene ved at sikkerhedskopiere SQL-databaser til Azure-lager."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Bruge Azure lagerplads til SQL serversikkerhedskopiering og gendannelse

## <a name="overview"></a>Oversigt

Start med SQL Server 2012 SP1 CU2, og kan du nu skrive sikkerhedskopiering af SQL Server direkte til tjenesten Azure Blob storage. Du kan bruge denne funktion til at sikkerhedskopiere til og gendanne fra tjenesten Azure Blob med en lokal SQL Server-database eller en SQL Server-database på en Azure virtuel computer. Sikkerhedskopi til skyen indeholder fordelene ved tilgængelighed, ubegrænset geografisk replikerede lager og nemmere at overførsel af data til og fra skyen. Du kan udstede SIKKERHEDSKOPIERING eller gendannelse sætninger ved hjælp af Transact-SQL eller SMO.

SQL Server 2016 introducerer nye funktioner Du kan bruge [fil øjebliksbillede sikkerhedskopi](http://msdn.microsoft.com/library/mt169363.aspx) til at udføre næsten omgående sikkerhedskopier og meget hurtig gendanner.

Dette emne forklares det, hvorfor du kan vælge at bruge Azure-lager til SQL sikkerhedskopier og derefter beskrives de involverede komponenter. Du kan bruge de ressourcer, der er angivet i slutningen af artiklen til at få adgang til yderligere oplysninger om at begynde at bruge denne tjeneste med dine SQL Server-sikkerhedskopier og gennemgange af.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Fordelene ved hjælp af tjenesten Azure Blob til SQL Server sikkerhedskopier

Der findes flere udfordringer, der kan opstå, når sikkerhedskopiering af SQL Server. Disse udfordringer omfatter Lagerstyring for risikoen for fejl ved lagring, adgang til lager og hardwarekonfiguration. Mange af disse udfordringer behandles ved hjælp af tjenesten Azure Blob-lager til sikkerhedskopiering af SQL Server. Overvej følgende fordele:

- **Brugervenligheden**: gemme dine sikkerhedskopier i Azure BLOB kan være et praktisk, fleksibel og nem adgang til indstilling. Oprette lagerplads på din SQL Server-sikkerhedskopier kan være lige så nemt som at ændre din eksisterende scripts/sager bruge syntaksen **Sikkerhedskopi URL-adresse** . Lagerplads skal typisk tilstrækkeligt langt fra fremstilling databaseplaceringen for at forhindre enkelt nedbrud, der kan påvirke begge og fremstilling database placeringerne. Hvis du vælger at [geografisk replikeres din Azure BLOB](../storage/storage-redundancy.md), har du et ekstra lag af beskyttelse i tilfælde af nedbrud, kan det påvirke det hele område.
- **Sikkerhedskopi arkiv**: feltet Azure Blob-lager service giver et bedre alternativ til ofte anvendte bånd mulighed for at arkivere sikkerhedskopier. Bånd lagerplads kan kræve fysisk transport til en facilitet og mål for at beskytte medier. Gemme dine sikkerhedskopier i Azure Blob-lager indeholder et øjeblik høj tilgængelighed og en varige arkivering indstilling.
- **Administrerede hardware**: der er ingen omkostninger for administration af hardware med Azure-tjenester. Azure services administrere hardwaren og give geografisk gentagelse til redundans og beskyttelse mod hardware mislykkede forsøg.
- **Ubegrænset lagerplads**: Når du aktiverer en direkte sikkerhedskopi til Azure blob, du har adgang til næsten ubegrænsede lagerplads. Du kan også har sikkerhedskopierer til en Azure virtuelt disk begrænsninger, der er baseret på maskine størrelse. Er der en grænse for antallet af diske, du kan vedhæfte til en Azure virtuelt efter sikkerhedskopier. Denne grænse er 16 diske for en meget stor forekomst og færre for mindre forekomster.
- **Sikkerhedskopi tilgængelighed**: sikkerhedskopier, der er gemt i Azure BLOB er tilgængelige som helst og når som helst og kan nemt åbnes for gendanner til en lokal SQL Server eller en anden SQL Server kører i en Azure virtuel maskine, uden at skulle database vedhæfte/fjerne eller hente og vedhæfte den virtuelle harddisk.
- **Omkostninger**: betale kun for den tjeneste, der bruges. Kan være økonomisk som en og sikkerhedskopiering arkiv indstilling. Se [Azure priser Lommeregner](http://go.microsoft.com/fwlink/?LinkId=277060 "Priser Lommeregner")og [Azure priser artikel](http://go.microsoft.com/fwlink/?LinkId=277059 "priser artikel") kan finde flere oplysninger.
- **Lagerplads snapshots**: Når databasefiler er gemt i en Azure blob, og du bruger SQL Server 2016, kan du bruge [fil øjebliksbillede sikkerhedskopi](http://msdn.microsoft.com/library/mt169363.aspx) til at udføre næsten omgående sikkerhedskopier og meget hurtig gendanner.

Se [SQL serversikkerhedskopiering og gendannelse med Azure Blob Storage Service](http://go.microsoft.com/fwlink/?LinkId=271617)kan finde flere oplysninger.

I følgende to afsnit introduceres tjenesten Azure Blob-lager, herunder de påkrævede SQL Server-komponenter. Det er vigtigt at forstå komponenterne og deres interaktion korrekt brug sikkerhedskopi og gendanne fra tjenesten Azure Blob storage.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage servicekomponenter

Følgende Azure komponenter anvendes, når sikkerhedskopiere til tjenesten Azure Blob storage.

| Komponent               | Beskrivelse                          |
|---------------------|-------------------------------|
| **Lagerplads konto** | Kontoen lagerplads er udgangspunktet for alle lagerplads tjenester. For at få adgang til en tjeneste til Azure Blob-lager, først oprette en Azure-lager-konto. Du kan finde flere oplysninger om Azure Blob storage service, se, [hvordan du bruger tjenesten Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Objektbeholder** | En objektbeholder giver en gruppering af et sæt af BLOB og kan gemme et ubegrænset antal BLOB. Hvis du vil skrive en SQL Server sikkerhedskopiering til en Azure Blob-tjeneste, du skal mindst have rodobjektbeholderen oprettet. |
| **BLOB** | En fil på en hvilken som helst type og størrelse. BLOB er tilgængelige i følgende URL-format: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Du kan finde flere oplysninger om siden BLOB se [forstå Bloker og siden BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-komponenter

De følgende SQL Server-komponenter bruges, når sikkerhedskopiere til tjenesten Azure Blob storage.

| Komponent               | Beskrivelse                          |
|---------------------|-------------------------------|
| **URL-ADRESSE** | En URL-adresse angiver en URI Uniform Resource Identifier () til en entydig sikkerhedskopifil. URL-adressen bruges til at angive placeringen af og navnet på den sikkerhedskopifil for SQL Server. URL-adressen skal pege på en faktisk blob, ikke kun en beholder. Hvis blob ikke findes, oprettes den. Hvis en eksisterende blob er angivet, sikkerhedskopi mislykkes, medmindre den > FORMATÉR med indstillingen er angivet. Følgende er et eksempel på den URL-adresse, du vil angive i kommandoen BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS anbefales, men ikke påkrævet. |
| **Legitimationsoplysninger** | De oplysninger, der kræves for at oprette forbindelse og godkende til Azure Blob storage tjenesten er gemt som legitimationsoplysninger.  I rækkefølge for SQL Server til at skrive sikkerhedskopier i en Azure Blob eller gendanne fra det, skal der oprettes en SQL Server-legitimationsoplysninger. Du kan finde flere oplysninger [SQL Server-legitimationsoplysninger](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Hvis du vil kopiere, og Overfør en sikkerhedskopifil til tjenesten Azure Blob storage, skal du bruge typen side blob som din datalager, hvis du planlægger at bruge denne fil til gendannelse. GENDANNE fra en blok blob type mislykkes med en fejl.

## <a name="next-steps"></a>Næste trin

1. Oprette en Azure-konto, hvis du ikke allerede har en. Hvis du evaluerer Azure, kan du overveje at [gratis prøveversion](https://azure.microsoft.com/free/).

1. Derefter går gennem et af følgende selvstudier, der hjælper dig med at oprette en konto, lager og udføre en gendannelse.

    - **SQL Server 2014**: [Selvstudium: SQL Server 2014 sikkerhedskopiering og gendannelse til Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [Selvstudium: Brug af tjenesten Microsoft Azure Blob storage med SQL Server 2016 databaser](https://msdn.microsoft.com/library/dn466438.aspx)

1. Gennemse yderligere dokumentation, der starter med [SQL serversikkerhedskopiering og gendannelse med Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Hvis du støder på problemer, kan du gennemse emnet [Sikkerhedskopiering af SQL Server til URL-adressen bedste praksis og fejlfinding](https://msdn.microsoft.com/library/jj919149.aspx).

Andre SQL Server sikkerhedskopiere og gendanne indstillinger, skal du se [Sikkerhedskopiering og gendannelse til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
