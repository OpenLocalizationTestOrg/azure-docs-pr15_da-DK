<properties
   pageTitle="Oprette forbindelse til Azure SQL datawarehouse | Microsoft Azure"
   description="Sådan finder du serveren streng navn og forbindelse til din til Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Oprette forbindelse til Azure SQL datawarehouse

I denne artikel hjælper dig med at oprette forbindelse til SQL Data Warehouse for første gang.

## <a name="find-your-server-name"></a>Finde navnet på din server

Det første trin til at oprette forbindelse til SQL Data Warehouse er at vide, hvordan du kan finde dit servernavn.  For eksempel er servernavnet i følgende eksempel sample.database.windows.net. Sådan finder du navnet på den fuldstændige server:

1. Gå til [Azure-portalen][].
2. Klik på **SQL-databaser** 
3. Klik på den database, du vil oprette forbindelse til.
4. Find den fulde servernavn.

    ![Fuld servernavn][1]

## <a name="supported-drivers-and-connection-strings"></a>Understøttede drivere og forbindelsesstrenge af

Azure SQL Data Warehouse understøtter [ADO.NET][], [ODBC][], [PHP][]og [JDBC][]. Klik på en af de foregående drivere til at finde den nyeste version og dokumentation. For at generere automatisk forbindelsesstrengen for den driver, som du bruger fra Azure-portalen, kan du klikke på **Vis database forbindelsesstrenge** fra det foregående eksempel.  Følgende er også nogle eksempler på, hvordan en forbindelsesstreng ser ud for hver driver.

> [AZURE.NOTE] Overvej at angive forbindelsestimeout til 300 sekunder at tillade forbindelsen til klare korte perioder utilgængelighed.

### <a name="adonet-connection-string-example"></a>Eksempel på ADO.NET connection streng

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Eksempel på ODBC connection streng

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Eksempel på PHP connection streng

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Eksempel på JDBC connection streng

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Indstillinger for forbindelse

SQL Data Warehouse standardiseres visse indstillinger under forbindelse og oprettelse af objekt. Disse indstillinger kan ikke tilsidesættes og indeholder:

| Databaseindstilling       | Værdi                        |
| :--------------------- | :--------------------------- |
| [BRUGES ANSI_NULLS][]         | PÅ                           |
| [QUOTED_IDENTIFIERS][] | PÅ                           |
| [DATOFORMAT][]         | MDÅ                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Næste trin

For at oprette forbindelse og forespørgsler med Visual Studio skal du se [forespørgsel med Visual Studio][]. Hvis du vil vide mere om indstillinger for godkendelse, skal du se [godkendelse til Azure SQL Data Warehouse][].

<!--Articles-->
[Forespørgsel med Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Godkendelse til Azure SQL datawarehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[BRUGES ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATOFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure-portalen]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


