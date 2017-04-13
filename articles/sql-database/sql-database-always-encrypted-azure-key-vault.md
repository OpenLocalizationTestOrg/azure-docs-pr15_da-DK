<properties
    pageTitle="Altid krypteret: Beskytte følsomme oplysninger i Azure SQL Database med databasekryptering | Microsoft Azure"
    description="Beskytte følsomme oplysninger i dine SQL-database i minutter."
    keywords="kryptering af data, krypteringsnøgle, skyen kryptering"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Altid krypteret: Beskytte følsomme oplysninger i SQL-Database og gemme dine krypteringsnøgler i Azure-tasten samling

> [AZURE.SELECTOR]
- [Azure vigtige samling](sql-database-always-encrypted-azure-key-vault.md)
- [Windows lager](sql-database-always-encrypted.md)


I denne artikel beskrives, hvordan til at sikre følsomme oplysninger i en SQL-database med kryptering af data ved hjælp af [Altid krypteret guiden](https://msdn.microsoft.com/library/mt459280.aspx) i [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Den indeholder også oplysninger, der viser dig, hvordan at lagre hver krypteringsnøgle i Azure-tasten samling.

Krypteret er altid en ny krypteringsteknologi for data i Azure SQL-Database og SQL Server, der hjælper med at beskytte følsomme data på resten på serveren, under bevægelse mellem klienten og serveren, og når dataene er i brug. Altid sikrer krypteret, at følsomme data aldrig vises som almindelig tekst i databasesystemet. Når du konfigurerer kryptering af data, kun klientprogrammer eller app-servere, der har adgang til de pågældende taster kan få adgang til data i almindelig tekst. Oplysninger, skal du se [Altid krypteret (databaseprogram)](https://msdn.microsoft.com/library/mt163865.aspx).


Når du konfigurerer database for at bruge altid krypteret, opretter du en klientprogrammet i C# med Visual Studio til at arbejde med den krypterede data.

Følg trinnene i denne artikel, og Lær, hvordan du konfigurerer altid krypteret til Azure SQL-database. I denne artikel lærer du at udføre følgende opgaver:

- Bruge guiden altid krypteret i SSMS til at oprette [altid krypteret taster](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Oprette en [kolonne master nøgle (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Oprette en [krypteringsnøgle kolonne (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Oprette en databasetabel og kryptere kolonner.
- Oprette et program, der indsætter, markerer og viser data fra kolonnerne krypteret.


## <a name="prerequisites"></a>Forudsætninger

I dette selvstudium skal du:

- En Azure-konto og abonnement. Hvis du ikke har en tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 eller nyere.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) eller nyere (på klientcomputeren).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](../powershell-install-configure.md), version 1.0 eller nyere. Skriv **(Get-modul azure - ListAvailable). Version** at se, hvilken version af PowerShell du kører.



## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Aktivere klientprogrammet og få adgang til tjenesten SQL-Database

Du skal aktivere klientprogrammet og få adgang til tjenesten SQL-Database ved at oprette den nødvendige godkendelse og opbygning af *ClientId* og *hemmeligt* , du skal bruge til at godkende dit program i den følgende kode.

1. Åbn [Azure klassisk portal](http://manage.windowsazure.com).
2. Vælg **Active Directory** , og klik på den Active Directory-forekomst, der anvender dit program.
3. Klik på **programmer**, og klik derefter på **Tilføj**.
4. Skriv et navn til dit program (f.eks.: *myClientApp*), Vælg **WEBPROGRAM**, og klik på pilen for at fortsætte.
5. Du kan skrive en gyldig URL-adresse (for eksempel *http://myClientApp*) og fortsætte til **SIGN-ON URL-adresse** og **APP ID URI** .
6. Klik på **KONFIGURER**.
7. Kopier din **klient-ID**. (Du skal bruge denne værdi i din kode senere.)
8. Vælg **1 år** fra rullelisten **Vælg varighed** i sektionen **taster** . (Du kan kopiere nøglen, når du har gemt i trin 14.)
11. Rul ned, og klik på **Tilføj program**.
12. Lad være **Vis** angivet til **Microsoft Apps** , og vælg **Microsoft Azure Service Management**. Klik på markering for at fortsætte.
13. Vælg **Access Azure Service Management** rullelisten **Delegerede tilladelser** .
14. Klik på **Gem**.
15. Når Gem afsluttes, skal du kopiere den nøgleværdi i sektionen **taster** . (Du skal bruge denne værdi i din kode senere.)



## <a name="create-a-key-vault-to-store-your-keys"></a>Oprette en vigtige samling af legitimationsoplysninger for at gemme dine nøgler

Nu, hvor din klient app er konfigureret, og du har din klient-ID, er det tid til at oprette en vigtige samling af legitimationsoplysninger og konfigurere dens access-politik, så du og dit program kan få adgang til den samling hemmeligheder (altid krypteret tasterne). *Oprette*, *få*, *liste*, *Log*, *bekræfte*, *wrapKey*og *unwrapKey* tilladelser der kræves til oprettelse af en ny kolonne master nøgle og konfiguration af kryptering med SQL Server Management Studio.

Du kan hurtigt oprette en vigtige samling ved at køre følgende script. Se [Introduktion til Azure nøgle samling](../Key-Vault/key-vault-get-started.md)for en detaljeret beskrivelse af disse cmdlet'er og flere oplysninger om oprettelse og konfiguration af en vigtige samling af legitimationsoplysninger.



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Oprette en tom SQL-database
1. Log på [Azure-portalen](https://portal.azure.com/).
2. Gå til **nye** > **Data + lagerplads** > **SQL-Database**.
3. Oprette en **tom** database med navnet **Øvelse** på en ny eller eksisterende server. Du kan finde detaljerede anvisninger om, hvordan du opretter en database i portalen Azure, [oprette en SQL-database i minutter](sql-database-get-started.md).

    ![Oprette en tom database](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Du skal bruge forbindelsen streng senere i selvstudiet, så når du har oprettet databasen, gå til den nye øvelse database og kopiere forbindelsesstrengen. Du kan få vist forbindelsesstrengen når som helst, men det er nemt at kopiere den i portalen Azure.

1. Gå til **SQL-databaser** > **Øvelse** > **Vis database forbindelsesstrenge**.
2. Kopiere forbindelsesstrengen til **ADO.NET**.

    ![Kopiér forbindelsesstrengen](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Oprette forbindelse til databasen med SSMS

Åbn SSMS og oprette forbindelse til serveren med øvelse databasen.


1. Åbn SSMS. (Gå til **forbinde** > **Database Engine** at åbne vinduet **Opret forbindelse til Server** , hvis det ikke er åbent.)
2. Angiv dit servernavn og legitimationsoplysninger. Du kan finde navnet på serveren på bladet SQL-database, og i forbindelsesstrengen du kopierede tidligere. Skriv navnet på fuldført server, herunder *database.windows.net*.

    ![Kopiér forbindelsesstrengen](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Hvis vinduet **Ny regel for Firewall** åbnes, skal du logge på Azure og lade SSMS oprette en ny firewallregel for dig.


## <a name="create-a-table"></a>Oprette en tabel

I dette afsnit, skal oprette du en tabel for at holde patienter data. Det er ikke indledningsvis krypteret – kan du konfigurere kryptering i næste afsnit.

1. Udvid **databaser**.
1. Højreklik på den **Øvelse** database, og klik på **Ny forespørgsel**.
2. Indsæt følgende Transact-SQL (T-SQL) i den nye forespørgselsvinduet, og **Udfør** den.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Kryptere kolonner (Konfigurer altid krypteret)

SSMS indeholder en guide, der hjælper dig med at konfigurere nemt altid krypteret ved hjælp af indstillingen kolonne master nøgle, kolonne krypteringsnøgle og krypterede kolonner for dig.

1. Udvid **databaser** > **Øvelse** > **tabeller**.
2. Højreklik på tabellen **patienter** , og vælg **Kryptere kolonner** for at åbne guiden altid krypteret:

    ![Kryptere kolonner](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Guiden altid krypteret indeholder i de følgende afsnit: **Kolonnemarkeringen**, **Master Key konfiguration**, **Validering**og **Oversigt**.

### <a name="column-selection"></a>Valg af kolonne##

Klik på **Næste** på siden **Introduktion** til at åbne siden **Valg af kolonne** . På denne side, du vælger, hvilke kolonner, du vil kryptere, [typen kryptering, og hvilke kolonne krypteringsnøgle (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) til brug.

Kryptere **CPR-NUMMER** og **fødselsdato** oplysninger for hver tålmodig. Kolonnen CPR-NUMMER, der anvender deterministisk kryptering, der understøtter lighed opslag, joinforbindelser og Gruppér efter. Kolonnen fødselsdato anvender randomiseret kryptering, der ikke understøtter handlinger.

Angiv den **Type kryptering** for kolonnen CPR-NUMMER til **Deterministic** og kolonnen fødselsdato til **Randomized**. Klik på **Næste**.

![Kryptere kolonner](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Master Key konfiguration###

**Master Key** konfigurationssiden er sted, hvor du konfigurere din CMK og vælge udbyderen, vigtige store, hvor CMK skal gemmes. I øjeblikket, kan du gemme en CMK i Windows certifikat store, Azure-tasten samling eller et hardware sikkerhed-modul (HSM).

Dette selvstudium viser, hvordan du gemmer dine nøgler i Azure-tasten samling.

1.     Vælg **Azure vigtige samling**.
1.     Vælg den ønskede vigtige samling af legitimationsoplysninger på rullelisten.
1.     Klik på **Næste**.

![Master key konfiguration](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### <a name="validation"></a>Datavalidering###

Du kan kryptere kolonnerne nu eller gemme en PowerShell-script til at køre senere. Vælg **Fortsæt for at afslutte nu** dette selvstudium, og klik på **Næste**.

### <a name="summary"></a>Oversigt ###

Kontrollere, at indstillingerne for alle rette og klikke på **Udfør** for at fuldføre konfigurationen til altid krypteret.


![Oversigt](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### <a name="verify-the-wizards-actions"></a>Bekræfte guidens handlinger

Når guiden er færdig, er databasen konfigureret til altid krypteret. Guiden udføres følgende handlinger:

- Oprettet nøgle på master for en kolonne og gemmes det i Azure-tasten samling.
- Oprettet en kolonne krypteringsnøgle og gemmes det i Azure-tasten samling.
- Konfigureret de markerede kolonner til kryptering. Tabellen patienter i øjeblikket indeholder ikke data, men eventuelle eksisterende data i de markerede kolonner er nu krypteret.

Du kan kontrollere oprettelse af tasterne i SSMS ved at udvide **Øvelse** > **sikkerhed** > **Altid krypteret taster**.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Oprette en klientprogrammet, der fungerer sammen med den krypterede data

Nu, hvor altid krypteret er konfigureret, kan du oprette et program, der udfører *indsætter* og *markerer* på de kolonner, der er krypteret.  

> [AZURE.IMPORTANT] Dit program skal bruge [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objekter, når der passerer data i almindelig tekst til serveren med altid krypteret kolonner. Sende ordret værdier uden at bruge SqlParameter objekter medfører en undtagelse.

1. Åbne Visual Studio, og Opret en ny C# console-program. Sørg for, at dit projekt er indstillet til **.NET Framework 4.6** eller nyere.
2. Navngiv projektet **AlwaysEncryptedConsoleAKVApp** , og klik på **OK**.
![Nyt console-program](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. Installere følgende NuGet-pakker ved at gå til **værktøjer** > **NuGet Package Manager** > **Pakke Manager-konsollen**.

Køre disse to kodelinjer i pakke Manager-konsollen.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ændre din forbindelsesstreng for at aktivere altid krypteret

I dette afsnittet forklares, hvordan du aktiverer altid krypteret i din databaseforbindelsesstreng.


Hvis du vil aktivere altid krypteret, skal du føje nøgleordet **Kolonne krypteringsindstilling** til din forbindelsesstreng og angive den til **aktiveret**.

Du kan angive dette direkte i forbindelsesstrengen, eller du kan angive den ved hjælp af [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Eksempelprogrammet i næste afsnit viser, hvordan du bruger **SqlConnectionStringBuilder**.



### <a name="enable-always-encrypted-in-the-connection-string"></a>Aktivér altid krypteret i forbindelsesstrengen

Tilføj følgende nøgleord til din forbindelsesstreng.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Aktivér altid er krypteret med SqlConnectionStringBuilder

Følgende kode viser, hvordan du aktiverer altid krypteret ved at angive [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) til [aktiveret](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Registrere provideren Azure-tasten samling

Følgende kode viser, hvordan til at registrere provideren Azure-tasten samling med ADO.NET-driver.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Altid krypteret console Northwind

Dette eksempel demonstrerer Sådan:

- Ændre din forbindelsesstreng for at aktivere altid krypteret.
- Registrere Azure-tasten samling som programmets vigtige store udbyder.  
- Indsætte data i kolonnerne krypteret.
- Vælg en post ved at filtrere efter en bestemt værdi i en krypteret kolonne.

Erstat indholdet af **Program.cs** med følgende kode. Erstat forbindelsesstrengen for den globale connectionString variabel i den linje, der direkte står foran metoden Main med din gyldig forbindelsesstreng fra Azure-portalen. Dette er den eneste ændring, du skal træffe til denne kode.

Køre appen for at se altid krypteret i aktion.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Kontrollér, at dataene er krypteret

Du kan hurtigt kontrollere, at de faktiske data på serveren er krypteret ved at forespørge patienter dataene med SSMS (ved hjælp af din aktuelle forbindelse hvor **Kolonne krypteringsindstilling** endnu ikke er aktiveret).

Køre følgende forespørgsel på øvelse databasen.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Du kan se, at de krypterede kolonner ikke indeholder alle data i almindelig tekst.

   ![Nyt console-program](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Hvis du vil bruge SSMS til at få adgang til dataene i almindelig tekst, kan du tilføje den *kolonne krypteringsindstilling = aktiveret* -parameter til forbindelsen.

1. Højreklik på din server i **Object Explorer** i SSMS, og vælg **Afbryd forbindelse**.
2. Klik på **Opret forbindelse** > **Database Engine** til at åbne vinduet **Opret forbindelse til serveren** , og klik på **Indstillinger**.
3. Klik på **Flere parametrene for dataforbindelse** , og skriv **kolonne krypteringsindstilling = aktiveret**.

    ![Nyt console-program](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Køre følgende forespørgsel på øvelse databasen.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Du kan nu se data i kolonnerne krypterede almindelig tekst.


    ![Nyt console-program](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Næste trin
Når du opretter en database, der bruger altid krypteret, vil du måske at gøre følgende:

- [Roter og rydde op i dine nøgler](https://msdn.microsoft.com/library/mt607048.aspx).
- [Overførsel af data, der allerede er krypteret med altid krypteret](https://msdn.microsoft.com/library/mt621539.aspx).


## <a name="related-information"></a>Relaterede oplysninger

- [Altid krypteret (klient udvikling)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Kryptering af gennemsigtig data](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server-kryptering](https://msdn.microsoft.com/library/bb510663.aspx)
- [Altid krypteret guiden](https://msdn.microsoft.com/library/mt459280.aspx)
- [Altid krypteret blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
