<properties
   pageTitle="SSMS support til Azure AD MFA med SQL-Database og SQL Data Warehouse | Microsoft Azure"
   description="Bruge flere tages godkendelse med SSMS for SQL-Database og SQL datawarehouse."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>SSMS support til Azure AD MFA med SQL-Database og SQL Data Warehouse

Azure SQL-Database og Azure SQL Data Warehouse understøtter nu forbindelser fra SQL Server Management Studio (SSMS) ved hjælp af *Active Directory Universal godkendelse*. Active Directory Universal godkendelse er en interaktiv arbejdsgang, der understøtter *Azure Multi-Factor Authentication* (MFA). Azure MFA hjælper med at beskytter adgang til data og programmer under overholdelse af brugernes behov for en enkelt logonprocessen. Det leverer stærk godkendelse med et udvalg af nemt bekræftelse – telefonopkald, SMS-besked, chipkort med pinkode eller mobilapp meddelelse – så brugerne kan vælge metoden de foretrækker. Du kan finde en beskrivelse af Multi-Factor Authentication [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).

SSMS understøtter nu:

- Interaktive MFA med Azure AD med mulighed for pop op-dialogboksen feltvalidering.
- Ikke-interaktiv Active Directory-adgangskode og Active Directory integreret godkendelse metoder, der kan bruges i mange forskellige programmer (ADO.NET, JDBC, ODBC- osv.). Disse to metoder resulterer aldrig i pop op-dialogbokse.

Når kontoen er konfigureret til MFA kræver arbejdsgang de interaktive godkendelse yderligere brugerhandlinger via pop op-dialogbokse, chipkortbrug osv. Når kontoen er konfigureret til MFA, skal brugeren vælge Azure Universal godkendelse til at oprette forbindelse. Hvis kontoen ikke kræver MFA, kan brugeren stadig bruge de andre to Azure Active Directory Authentication indstillinger.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Universal godkendelse begrænsninger for SQL-Database og SQL Data Warehouse

- SSMS er det eneste værktøj, der er i øjeblikket er aktiveret til MFA gennem Active Directory Universal godkendelse.
- Kun en enkelt Azure Active Directory-konto kan logge på for en forekomst af SSMS ved hjælp af Universal godkendelse. For at logge på som en anden Azure AD-konto, skal du bruge en anden forekomst af SSMS. (Denne begrænsning er begrænset til Active Directory Universal godkendelse, og du kan logge på forskellige servere, der bruger Active Directory Password Authentication, Active Directory integreret godkendelse eller SQL Server-godkendelse).
- SSMS understøtter Active Directory Universal godkendelse for Object Explorer, Query Editor og forespørgsel Store visualisering.
- Hverken DacFx eller skema Designer understøtter Universal godkendelse.
- MSA konti understøttes ikke til Active Directory Universal godkendelse.
- Active Directory Universal godkendelse understøttes ikke i SSMS for brugere, der er importeret til den aktuelle Active Directory fra andre Azure Active mapper. Disse brugere understøttes ikke, da det kræver en lejer-ID'ET for at validere kontiene, og der er ingen metode til at levere.
- Der er ingen yderligere softwarekrav til Active Directory Universal godkendelse, bortset fra at du skal bruge en understøttet version af SSMS.

## <a name="configuration-steps"></a>Trin til konfiguration

Implementering af Multi-Factor Authentication kræver fire grundlæggende trin.

1. **Konfigurer en Azure Active Directory** – du kan finde flere oplysninger, se [integrere dine lokale identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [tilføje dit eget domænenavn til Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure nu understøtter sammenslutning med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrere adresselisten Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)og [Administrer Azure AD ved hjælp af Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Konfigurere MFA** -trinvis vejledning i [Konfigurere Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Konfigurere SQL-Database eller SQL Data Warehouse til Azure AD-godkendelse** – en trinvis vejledning i [tilslutning til SQL-Database eller SQL Data Warehouse ved brug af Azure Active Directory-godkendelse](sql-database-aad-authentication.md).

4. **Hente SSMS** – download de nyeste SSMS på klientcomputeren (mindst August 2016), fra [Hente SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Oprette forbindelse ved hjælp af Universal godkendelse med SSMS

Følgende trin viser, hvordan du opretter forbindelse til SQL-Database eller SQL Data Warehouse ved hjælp af den seneste SSMS.

1. For at oprette forbindelse ved hjælp af Universal godkendelse, i dialogboksen **Opret forbindelse til Server** , skal du vælge **Active Directory Universal godkendelse**.
![1mfa-universal-forbindelse][1]

2. Som normalt for SQL-Database og SQL Data Warehouse skal du klikke på **Indstillinger** og Angiv databasen, i dialogboksen **Indstillinger** . Klik derefter på **Opret forbindelse**.
3. Når dialogboksen **Log på din konto** vises, give konto og adgangskoden til din Azure Active Directory-identitet.
![2mfa-logon][2]

    > [AZURE.NOTE] Til Universal godkendelse med en konto, som ikke kræver MFA, du opretter forbindelse på dette tidspunkt. Fortsæt med følgende trin for brugere, der kræver MFA.
 
4. To MFA konfiguration dialogbokse vises muligvis. Dette én gang handlingen afhænger af MFA administratoren konfiguration, og som derfor er valgfrit. Dette trin er nogle gange foruddefinerede for et domæne, der MFA aktiveret (for eksempel domænet kræver brugere at bruge et chipkort og pinkode).  
![3mfa-konfiguration][3]

5. Den anden mulige én gang i dialogboksen kan du vælge oplysningerne om din godkendelsesmetode. De mulige indstillinger er konfigureret af administratoren.
![4mfa-Bekræft-1][4]
 
6. Azure Active Directory sender bekræftende oplysninger for dig. Når du modtager verifikationskoden, angive den i feltet **Enter verifikationskoden** , og klik på **Log på**.
![5mfa-Bekræft-2][5]

Når bekræftelsen er gennemført, forbinder SSMS normalt forudsat gyldige legitimationsoplysninger og firewall adgang.

##<a name="next-steps"></a>Næste trin  

Give andre adgang til databasen: [SQL Database-godkendelse og godkendelse: give adgang](sql-database-manage-logins.md)  
Sørg for, at andre kan oprette forbindelse via firewallen: [konfigurere en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

