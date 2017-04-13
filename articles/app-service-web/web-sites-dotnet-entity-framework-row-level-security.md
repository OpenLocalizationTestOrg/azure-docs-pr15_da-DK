<properties
    pageTitle="Selvstudium: WebApp med en med flere lejer database ved hjælp af enhed Framework og sikkerhed på brugerniveau række"
    description="Lær, hvordan du udvikler en ASP.NET MVC 5 WebApp med en med flere lejer SQL Database backent, ved hjælp af enhed Framework og sikkerhed på brugerniveau række."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="04/25/2016"
    ms.author="thmullan"/>

# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Selvstudium: WebApp med en med flere lejer database ved hjælp af enhed Framework og sikkerhed på brugerniveau række

Dette selvstudium viser, hvordan du opretter en med flere lejer WebApp med en "[delte database, delte skema](https://msdn.microsoft.com/library/aa479086.aspx)" arkitekturer model, ved hjælp af enhed Framework og [Sikkerhed på brugerniveau række](https://msdn.microsoft.com/library/dn765131.aspx). I denne tabel, en enkelt database indeholder data for mange lejere, og hver række i hver tabel er knyttet til et "lejer id" Række-niveau sikkerhed sikkerhed, en ny funktion til Azure SQL-Database, der bruges til at forhindre lejere i at få adgang til hinandens data. Det kræver blot en enkelt, lille ændring til programmet. Ved at samle lejer access logik i selve databasen, RLS forenkler programkoden og reducerer risikoen for uautoriseret videregivelse utilsigtede data mellem lejere.

Lad os starte med programmet enkel Contact Manager fra [oprette en ASP.NET MVP app med auth og SQL DB og installere til Azure App-tjenesten](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Højre nu programmet giver mulighed for alle brugere (lejere) for at få vist alle kontakter:

![Contact Manager-program før RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Med blot nogle få små ændringer føjer vi understøttelse af flere arkitekturer, så brugerne kan se kun de kontaktpersoner, der hører til dem.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Trin 1: Tilføje en Interceptor klasse i programmet på computeren til at angive SESSION_CONTEXT

Der er en ændring af ansøgning vi brug for at gøre. Da alle brugere af programmet opretter forbindelse til den database, bruger den samme forbindelsesstreng (det vil sige samme SQL-logon), findes der i øjeblikket ingen måde for en RLS politik at vide, hvilke bruger den skal filtrere efter. Denne metode er meget almindeligt i webprogrammer, fordi det giver mulighed for effektiv gruppering af forbindelse, men det betyder, at vi brug for en anden måde at identificere den aktuelle program bruger i databasen. Løsningen er at have programmet, angive et par nøgle-værdi for den aktuelle bruger-id i [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) umiddelbart efter du har åbnet en forbindelse, før den udfører alle forespørgsler. SESSION_CONTEXT er en session-fastsat nøgle-værdi store, og vores politik for RLS anvender det bruger-id gemt i den til at identificere den aktuelle bruger.

Vi tilføjer en [interceptor](https://msdn.microsoft.com/data/dn469464.aspx) (især, [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), en ny funktion i enhed Framework (ELEMENTÆRFILEN) 6, der automatisk skal angives den aktuelle bruger-id i SESSION_CONTEXT ved at udføre en T-SQL-sætning, når ELEMENTÆRFILEN åbner en forbindelse.

1.  Åbn ContactManager projektet i Visual Studio.
2.  Højreklik på mappen modeller i Solution Explorer, og vælg Tilføj > klasse.
3.  Navngiv den nye klasse "SessionContextInterceptor.cs", og klik på Tilføj.
4.  Erstat indholdet af SessionContextInterceptor.cs med følgende kode.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Det er den eneste program ændring er nødvendig. Gå videre og opbygge og udgive programmet.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Trin 2: Føje en bruger-id-kolonne til databaseskemaet

Dernæst skal vi brug at føje en kolonne med bruger-id til kontaktpersoner tabellen for at knytte hver række med en bruger (lejer). Vi vil ændre skemaet direkte i databasen, så vi ikke behøver at medtage dette felt i vores ELEMENTÆRFILEN datamodel.

Oprette forbindelse til databasen direkte ved hjælp af SQL Server Management Studio eller Visual Studio, og udfør derefter følgende T-SQL:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Dette tilføjer en bruger-id-kolonne i tabellen kontakter. Vi bruger datatypen nvarchar(128) til at matche det bruger-id'er gemmes i tabellen AspNetUsers, og vi oprette en standardbegrænsning, der angives automatisk bruger-id for nyligt indsatte rækker skal være det bruger-id i øjeblikket er gemt i SESSION_CONTEXT.

Tabellen ser nu sådan ud:

![SSMS kontaktpersoner tabellen](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Når der oprettes nye kontakter, skal de automatisk tildeles de korrekte bruger-id. Til demonstrationsformål, men lad os tildele nogle af disse eksisterende kontakter til en eksisterende bruger.

Hvis du har oprettet nogle få brugere i programmet allerede (f.eks., ved hjælp af lokale, Google eller Facebook konti), kan du se dem i tabellen AspNetUsers. I skærmbilledet nedenfor er der kun én bruger hidtil.

![SSMS AspNetUsers tabel](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Kopiere Id for user1@contoso.com, og sætte det ind i T-SQL-sætningen nedenfor. Udfør denne sætning for at knytte tre af kontakterne til denne bruger-id.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Trin 3: Opret en politik for række-sikkerhed på brugerniveau i databasen

Det sidste trin er at oprette en sikkerhedspolitik, der bruger bruger-id i SESSION_CONTEXT til at filtrere resultaterne returneres af forespørgsler automatisk.

Mens du stadig har forbindelse til databasen, kan du udføre følgende T-SQL:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Denne kode understøtter tre ting. Først skal oprettes et nyt skema som en bedste praksis for centralisering og begrænse adgangen til RLS objekter. Derefter opretter en prædikat funktion, der vil returnere '1', når bruger-id i en række svarer til bruger-id i SESSION_CONTEXT. Til sidst skal opretter det en sikkerhedspolitik, der tilføjer denne funktion som både et filter og Bloker prædikatet på tabellen. Prædikatet filter får forespørgsler til at returnere kun rækker, der hører til den aktuelle bruger, og prædikatet Bloker fungerer som en beskytter at forhindre, at programmet fra nogensinde kommer til at indsætte en række for den bruger, der er galt.

Nu køre programmet, og log på som user1@contoso.com. Denne bruger kan nu se de kontakter, som vi er tildelt til denne bruger-id tidligere:

![Contact Manager-program før RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Hvis du vil validere dette yderligere, kan du prøve at registrere en ny bruger. De vises ingen kontaktpersoner, fordi ingen har fået tildelt. Hvis de opretter en ny kontakt, tildeles til dem, og kun de skal kunne se den.

## <a name="next-steps"></a>Næste trin

Det var det! Enkel Contact Manager WebApp er blevet konverteret til en med flere lejer et sted, hvor hver enkelt bruger har sin egen liste over kontakter. Ved hjælp af række-sikkerhed på brugerniveau, har vi undgås kompleksitet sikres lejer access logik i vores programkode. Denne gennemsigtighed kan programmet til at fokusere på reelle business problemet ved hånden, og det også reducerer risikoen for ved et uheld lækker data, som programmet er codebase vokser.

Dette selvstudium har kun ridset overfladen af hvad der er muligt med RLS. For eksempel det er muligt at har mere avancerede eller findelt access logik, og det er muligt at gemme mere end bare den aktuelle bruger-id i SESSION_CONTEXT. Det er også muligt at [integrere RLS med elastiske database værktøjer klientbiblioteker](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) til at understøtte flere lejer shards i ét skala ud data lag.

Ud over disse muligheder, også arbejder på at foretage RLS endnu bedre. Hvis du har spørgsmål, ideer eller ting, du gerne vil se, at fortælle os i feltet Bemærkninger. Vi sætter stor pris på din feedback!
