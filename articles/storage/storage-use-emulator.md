<properties 
    pageTitle="Bruge emulatoren Azure-lager til udvikling og afprøvning | Microsoft Azure" 
    description="Azure-lager emulatoren indeholder et gratis lokale udviklingsmiljø til udvikling og test af mod Azure-lager. Få mere at vide om lagring emulatoren, herunder hvordan godkendes anmodninger om, hvordan du kan oprette forbindelse til emulatoren fra dit program, og hvordan du bruger værktøjet kommandolinjen." 
    services="storage" 
    documentationCenter="" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>
<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="tamram"/>

# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Bruge emulatoren Azure-lager til udvikling og afprøvning

## <a name="overview"></a>Oversigt

Microsoft Azure-lager emulatoren giver et lokalt miljø, der emulerer Azure Blob, kø og tabel tjenesterne til udviklingsformål. Med lagerplads emulatoren, kan du teste dit program mod lagerplads tjenesterne lokalt, uden at oprette et Azure-abonnement eller foretage eventuelle omkostninger. Når du er tilfreds med, hvordan dit program virker i emulatoren, kan du skifte til ved hjælp af en Azure-lager-konto i skyen.

> [AZURE.NOTE] Emulatoren lagerplads fås som en del af [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). Du kan også installere lagerplads emulatoren ved hjælp af [enkeltstående installer](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). For at konfigurere emulatoren lagerplads, skal du have administratorrettigheder på computeren.
> 
> Lagerplads emulatoren kører i øjeblikket kun på Windows.
>  
> Bemærk, at data, der er oprettet i én version af lagerplads emulatoren ingen er garanti for at kunne åbnes, når du bruger en anden version. Hvis du vil bevare dine data til på lang sigt, anbefales det at gemme dataene i en Azure-lager-konto i stedet for i emulatoren lagerplads.

## <a name="how-the-storage-emulator-works"></a>Hvordan fungerer lagerplads emulatoren
 
Lagerplads emulatoren bruger en lokal Microsoft SQL Server-forekomst og det lokale filsystem til at emulere Azure lagerplads tjenesterne. Som standard bruger lagerplads emulatoren en database i Microsoft SQL Server 2012 Express LocalDB.  Du kan vælge at konfigurere lagerplads emulatoren for at få adgang til en lokal forekomst af SQL Server i stedet for forekomsten LocalDB. Se [Start- og initialisering lagerplads emulatoren](#start-and-initialize-the-storage-emulator) under for flere oplysninger.

Du kan installere SQL Server Management Studio Express for at administrere din LocalDB installation. Lagerplads emulatoren opretter forbindelse til SQL Server eller LocalDB ved hjælp af Windows-godkendelse. 

Nogle forskelle i funktionalitet findes mellem lagerplads emulator og Azure lagerplads services. Se [forskelle mellem lagerplads emulator og Azure-lager](#differences-between-the-storage-emulator-and-azure-storage)kan finde flere oplysninger om disse forskelle.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Godkendelse af anmodninger om mod lagerplads emulatoren

Ligesom i Azure-lager i skyen, skal hver anmodning, du foretager mod lagerplads emulatoren godkendes, medmindre en anonym anmodning. Du kan godkende anmodninger mod lagerplads emulatoren ved hjælp af delte nøgle godkendelse eller med en delt adgang signatur (SAS).

### <a name="authentication-with-shared-key-credentials"></a>Godkendelse med delt nøgle legitimationsoplysninger

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Du kan finde flere oplysninger om forbindelsesstrenge, [Konfigurere Azure lagerplads forbindelsesstrenge](storage-configure-connection-string.md). 

### <a name="authentication-with-a-shared-access-signature"></a>Godkendelse med en delt adgang signatur 

Nogle Azure storage klient-biblioteker, som biblioteket Xamarin understøtter kun godkendelse med en delt adgangstoken signatur (SAS). Du skal oprette dette SAS token ved hjælp af et værktøj eller program, der understøtter delt nøgle-godkendelse. Der er en nem måde at oprette SAS token via Azure PowerShell:

1. Installer Azure PowerShell, hvis du ikke allerede har. Det anbefales at bruge den nyeste version af Azure PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md#Install) installationsvejledning.

2. Åbn Azure PowerShell og Kør følgende kommandoer. Husk at erstatte *ACCOUNT_NAME* og *ACCOUNT_KEY ==* med dine egne legitimationsoplysninger. Erstat *CONTAINER_NAME* med et navn efter eget valg.

        $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
        
        New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
        
        $now = Get-Date 
        
        New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

Den resulterende delte access-signatur URI for objektbeholderen nye skal ligne følgende:

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

Delt adgang signaturen er oprettet i dette eksempel er gyldig for én dag. Signaturen giver fuld adgang (det vil sige Læs, Skriv, Slet og listen) til BLOB inde i beholderen.

Du kan finde flere oplysninger om delt adgang signaturer, [Ved hjælp af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md).


## <a name="start-and-initialize-the-storage-emulator"></a>Starte og initialiseret lagerplads emulatoren

Vælg knappen Start for at starte emulatoren Azure-lager, eller tryk på på Windows-tasten. Begynd at skrive **Azure lagerplads Emulator**, og vælg emulatoren fra listen over programmer. 

Når emulatoren kører, får du vist et ikon i meddelelsesområdet i Windows-proceslinjen.

Hvis lagerplads emulatoren starter, vises der et kommandolinjen vindue. Du kan bruge kommandolinjen vinduet til at starte og stoppe lagerplads emulatoren samt rydde data, få aktuelle status og initialiseret emulatoren. Se [Lagerplads Emulator kommandolinjeparametre værktøjet Reference](#storage-emulator-command-line-tool-reference)kan finde flere oplysninger.

Når vinduet kommandolinjen er lukket, fortsat kører emulatoren lagerplads. Følg trinnene ovenfor for at få vist kommandolinjen igen, som om starte lagerplads emulatoren.

Første gang du kører emulatoren lagerplads er det lokale lagermiljø initialiseret for dig. Initialiseringen opretter en database i LocalDB og forbeholder sig HTTP porte for hver tjeneste lokale lager. 

Lagerplads emulatoren installeres som standard til mappen C:\Program Files (x86) \Microsoft SDKs\Azure\Storage Emulator. 

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Initialiseret lagerplads emulatoren for at bruge en anden SQL-database

Du kan bruge værktøjet lagerplads emulator kommandolinjen initialiseret lagerplads emulatoren til at pege på en forekomst af SQL-database end LocalDB standardforekomst. Bemærk, at du skal køre værktøjet kommandolinjen med administratorrettigheder initialiseret back-end-databasen for lagerplads emulatoren:

1. Klik på knappen **Start** , eller tryk på **Windows** -tasten. Begynd at skrive `Azure Storage Emulator` og vælge den, når det vises, for at få vist værktøjet lagerplads emulator kommandolinjen.
2. I vinduet Kommandoprompt skal du skrive følgende kommando, hvor `<SQLServerInstance>` er navnet på SQL Server-forekomsten. Du kan bruge LocalDb, angive `(localdb)\v11.0` som SQL Server-forekomsten.

        AzureStorageEmulator init /server <SQLServerInstance> 
    
    Du kan også bruge følgende kommando, som omdirigerer emulatoren bruge standard SQL Server-forekomsten:

        AzureStorageEmulator init /server .\\ 

    Eller du kan bruge følgende kommando, som starter databasen til LocalDB standardforekomst igen:

        AzureStorageEmulator init /forceCreate 

Se [Lagerplads Emulator kommandolinjeparametre værktøjet Reference](#storage-emulator-command-line-tool-reference)kan finde flere oplysninger om disse kommandoer.

## <a name="addressing-resources-in-the-storage-emulator"></a>Vælge en adresse til ressourcer i lagerplads emulatoren

Tjenesten slutpunkter for lagerplads emulatoren er forskellige fra dem på en Azure-lager-konto. Forskellen er skyldes, at den lokale computer ikke udfører domæne navneoversættelse, så lagerplads emulator slutpunkterne kræver en lokal adresse i stedet for et domænenavn.

Når du tale om en ressource på en Azure-lager-konto, kan du bruge følgende skema, hvor kontonavnet er en del af værtsnavnet URI, og den ressource, der adresseret er en del af URI stien:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Den følgende URI er for eksempel en gyldig adresse til en blob på en Azure-lager-konto:

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Da den lokale computer ikke udfører domæne navneoversættelse er kontonavnet i emulatoren lagerplads en del af URI stien i stedet for værtsnavnet. Du kan bruge følgende skema for en ressource, der kører i lagerplads emulatoren:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Følgende adresse kan for eksempel bruges til at få adgang til en blob i lagerplads emulatoren:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Tjenesten slutpunkter for lagerplads emulatoren er:

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Vælge en adresse til kontoen sekundær med RA-GRS

Begynder med version 3.1, understøtter lagerplads emulator konto læseadgang geografisk overflødige gentagelse (RA-GRS). For de ressourcer, lagerplads både i skyen og i det lokale emulator, kan du få adgang til sekundær placeringen ved tilføjelse af-sekundær til kontonavnet. Følgende adresse kan for eksempel bruges til at få adgang til en blob ved hjælp af skrivebeskyttet sekundær i lagerplads emulatoren:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Bruge biblioteket lagerplads klient til .NET version 3.2 eller nyere til programmeringsmæssig adgang til sekundært med emulatoren lagerplads. Se [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) få mere at vide.

## <a name="storage-emulator-command-line-tool-reference"></a>Lagerplads emulator værktøj på kommandolinjen reference

Start i version 3.0, når du starter lagerplads emulatoren, og får du vist et kommandolinjen vindue pop op. Bruge vinduet kommandolinjen til at starte og stoppe emulatoren samt som for at forespørge efter status og udføre andre handlinger.

> [AZURE.NOTE] Hvis du har den Microsoft Azure beregne emulator installeret, vises en proceslinje-ikonet, når du starter emulatoren lagerplads. Højreklik på ikonet for at få vist en menu, der indeholder en grafisk måde at starte og stoppe emulatoren lagerplads.

### <a name="command-line-syntax"></a>Syntaksen for kommandolinjen

    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>Indstillinger

For at få vist listen over indstillinger, Skriv `/help` ved kommandoprompten.

| Indstillingen | Beskrivelse                                                    | Kommandoen                                                                                                 | Argumenter                                                                                                         |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Start**  | Starter emulatoren lagerplads.                                | `AzureStorageEmulator start [-inprocess]`                                                                    | *-inprocess*: starte emulatoren i den aktuelle proces i stedet for at oprette en ny proces.                          |
| **Stop**   | Stopper emulatoren lagerplads.                                    | `AzureStorageEmulator stop`                                                                                  |                                                                                                                   |
| **Status** | Udskriver status for lagerplads emulatoren.                     | `AzureStorageEmulator status`                                                                                |                                                                                                                   |
| **Ryd**  | Rydder dataene i alle tjenester, der er angivet på kommandolinjen. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *BLOB*: rydder blob data. <br/>*kø*: rydder kø data. <br/>*tabel*: rydder tabeldata. <br/>*alle*: fjerner alle data i alle tjenester. |
| **Initialisering**   | Udfører enkeltstående initialisering til konfiguration af emulatoren.       | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server Servernavn\Forekomstnavn*: Angiver den server, der er vært for SQL-forekomsten. <br/>*navn på-sqlinstance forekomst*: Angiver navnet på SQL-forekomsten, der skal bruges i standard server-forekomsten. <br/>*-forcecreate*: Fremtvinger oprettelse af SQL-databasen, selvom de allerede forefindes. <br/>*-inprocess*: udfører initialisering i den aktuelle proces i stedet for forgrening en ny proces. Du skal starte den aktuelle proces med administratorrettigheder for at udføre initialisering.          |
                                                                                                                  
## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Forskelle mellem lagerplads emulator og Azure-lager

Da lagerplads emulatoren er en emuleret miljøet kører i en lokal SQL-forekomst, er der nogle forskelle i funktionalitet mellem emulatoren og en Azure-lager-konto i skyen:

- Lagerplads emulatoren understøtter kun en enkelt fast konto og en velkendt godkendelsesnøgle.

- Lagerplads emulatoren er ikke en SVG-lagringstjeneste og understøtter ikke et stort antal samtidige klienter.

- Som beskrevet i [Adressering ressourcer i lagerplads emulatoren](#addressing-resources-in-the-storage-emulator), behandles ressourcer, der er anderledes i lagerplads emulatoren kontra en Azure-lager-konto. Denne forskel er skyldes, at domænet navneoversættelse er tilgængelig i skyen, men ikke på den lokale computer.

- Begynder med version 3.1, understøtter lagerplads emulator konto læseadgang geografisk overflødige gentagelse (RA-GRS). Alle konti har RA-GRS aktiveret i emulatoren, og der er aldrig en mellemliggende tid mellem de primære og sekundære replikaer. Handlingerne få Blob Service statistik, få kø Service statistik og få tabel Service Statistik understøttes på den sekundære konto og altid returnerer værdien af den `LastSyncTime` svarelement som det aktuelle klokkeslæt efter den underliggende SQL-database.

    Bruge biblioteket lagerplads klient til .NET version 3.2 eller nyere til programmeringsmæssig adgang til sekundært med emulatoren lagerplads. Se [Microsoft Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) få mere at vide.

- Tjenesten fil og slutpunkter for små og mellemstore virksomheder protocol tjeneste understøttes ikke i øjeblikket i emulatoren lagerplads.

- Lagerplads emulatoren returnerer fejlen VersionNotSupportedByEmulator (http-statuskode 400 - forkert anmodning), hvis du bruger en version af tjenesterne lagerplads, der ikke er endnu er understøttet af versionen af emulatoren du bruger.

### <a name="differences-for-blob-storage"></a>Forskelle til Blob-lager 

Følgende forskelle gælder for Blob-lager i emulatoren:

- Lagerplads emulator kun understøtter blob størrelse op til 2 GB.

- Handlingen placere Blob lykkes det muligvis mod en blob, der findes i lagerplads emulatoren og har et aktivt bortlease, selvom bortlease ID ikke er angivet som en del af anmodningen. 

- Føje handlinger ikke understøttes af emulatoren Blob. Forsøger at udføre en handling på en Føj blob returnerer fejlen FeatureNotSupportedByEmulator (http-statuskode 400 - forkert anmodning).

### <a name="differences-for-table-storage"></a>Forskelle til Table storage 

Følgende forskelle gælder for Table storage i emulatoren:

- Egenskaber for dato i tjenesten tabel i lagerplads emulatoren understøtter kun det område, der understøttes af SQL Server 2005 (*dvs.*, de er påkrævet for at være senere end 1 januar 1753). Alle datoer før 1 januar 1753 ændres til denne værdi. Præcision af datoer, der er begrænset til nøjagtigheden af SQL Server 2005, hvilket betyder, at datoerne er præcis til 1/300th af en anden.

- Lagerplads emulatoren understøtter partition nøgle og række vigtige egenskabsværdier for mindre end 512 byte hver. Desuden må ikke den samlede størrelse af kontonavn, tabelnavn og vigtige egenskabsnavne sammen overstige 900 byte.

- Den samlede størrelse af en række i en tabel i lagerplads emulatoren er begrænset til mindre end 1 MB.

- Skriv med egenskaber for data i emulatoren lagerplads `Edm.Guid` eller `Edm.Binary` understøtter kun den `Equal (eq)` og `NotEqual (ne)` sammenligningsoperatorer i forespørgsel filtrere strenge.

### <a name="differences-for-queue-storage"></a>Forskelle for kø-lagerplads

Der er ingen forskelle specifikt for kø lageret i emulatoren.

## <a name="storage-emulator-release-notes"></a>Lagerplads emulator produktbemærkninger

### <a name="version-45"></a>Version 4.5

- Fast en fejl, der forårsagede initialisering og installation af lagerplads emulatoren mislykkes, når databasen sikkerhedskopiering blevet omdøbt.

### <a name="version-44"></a>Version 4.4

- Lagerplads emulatoren understøtter nu version 2015-12-11 af lagerplads tjenesterne på Blob, kø og tabel slutpunkter for tjenesten.

- Lagerplads emulatoren Spildopsamling af blob-data er nu mere effektivt, når håndtere stort antal BLOB.

- Fast en fejl, der forårsagede objektbeholder ACL XML skal bekræftes en smule anderledes fra hvordan tjenesten storage ved den.

- Fast en fejl, der nogle gange forårsagede Maks og min DateTime-værdier, der skal rapporteres i den forkerte tidszone.

### <a name="version-43"></a>Version 4,3

- Lagerplads emulatoren understøtter nu version 2015-07-08 lagerplads tjenesterne på Blob, kø og tabel slutpunkter for tjenesten.

### <a name="version-42"></a>Version 4.2

- Lagerplads emulatoren understøtter nu version 2015-04-05 af lagerplads tjenesterne på Blob, kø og tabel slutpunkter for tjenesten.

### <a name="version-41"></a>Version 4.1

- Lagerplads emulatoren understøtter nu version 2015-02-21 lagerplads tjenesterne på Blob, kø og tabel service slutpunkter, med undtagelse af de nye funktioner i føje Blob. 

- Lagerplads emulatoren returnerer nu en sigende fejlmeddelelse, hvis du bruger en version af tjenesterne lagerplads, der ikke er endnu er understøttet i denne version af emulatoren. Vi anbefaler, at du bruger den nyeste version af emulatoren. Hvis du støder på fejlen VersionNotSupportedByEmulator (http-statuskode 400 - forkert anmodning), skal du hente den nyeste version af emulatoren lagerplads.

- Data med fast en fejl hvori en Kør betingelse, der er forårsaget tabel enhed for at være forkerte under samtidige Flet handlinger.

### <a name="version-40"></a>Version 4.0

- Lagerplads emulatoren eksekverbare er blevet omdøbt til *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Version 3.2

- Lagerplads emulatoren understøtter nu version 2014-02-14 af lagerplads tjenesterne på Blob, kø og tabel slutpunkter for tjenesten. Bemærk, at filen service slutpunkter ikke understøttes i øjeblikket i emulatoren lagerplads. Du kan finde oplysninger om version 2014-02-14 i [versionsstyring for Azure lagerplads Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) .

### <a name="version-31"></a>Version 3.1

- Læseadgang geografisk overflødige lagerplads (RA-GRS) understøttes nu i emulatoren lagerplads. Få Blob Service statistik og få kø Service statistik på få tabel Service statistik API'er understøttes for kontoen sekundær og altid returnerer værdien af elementet LastSyncTime svar som det aktuelle klokkeslæt efter den underliggende SQL-database. Bruge biblioteket lagerplads klient til .NET version 3.2 eller nyere til programmeringsmæssig adgang til sekundært med emulatoren lagerplads. Se Microsoft Azure-lager klient biblioteket til .NET Reference til detaljer.

### <a name="version-30"></a>Version 3.0

- Azure-lager emulatoren leveres ikke længere i den samme pakke som Beregn emulatoren.

- Lagerplads emulator grafiske brugergrænseflade frarådes tilsidesættes til fordel for en kommandolinjeflade. Få at vide om at brugergrænsefladen med kommandolinjen, se lagerplads Emulator kommandolinjeparametre værktøjet Reference. Den grafiske brugergrænseflade fortsætter med at være til stede i version 3.0, men det er kun tilgængeligt, når emulatoren beregne er installeret ved at højreklikke på ikonet system bakke og vælge Vis lagerplads Emulator brugergrænseflade.

- Version 2013-08-15 Azure lagerplads tjenesterne understøttes fuldt ud nu. (Tidligere denne version kun er understøttet af lagerplads Emulator version 2.2.1 Preview.)
