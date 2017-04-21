
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Sammenligne forbindelsesstrengen


Følgende tabel sammenligner de forbindelsesstrenge, som dit C#-program skal bruge til at oprette forbindelse til din lokale SQL Server kontra Azure SQL-Database i skyen. Forskellen er med fed skrift.


| Forbindelsesstreng til<br/>Azure SQL-Database | Forbindelsesstreng til<br/>Microsoft SQL Server |
| :-- | :-- |
| Server =**tcp:**{your_serverName_here}**. database.windows.net,1433**;<br/>Bruger-ID = {your_loginName_here}**@{your_serverName_here}**;<br/>Adgangskode = {your_password_here};<br/>**Database = {your_databaseName_here};**<br/>**Forbindelsen afbrydes efter = 30**;<br/>**Kryptere = True**;<br/>**TrustServerCertificate = falsk**; | Server = {your_serverName_here};<br/>Bruger-ID = {your_loginName_here};<br/>Adgangskode = {your_password_here}; |


Den **Database =** er valgfrit for SQL Server, men er påkrævet for SQL-Database.


[.NET ADO SqlConnectionStringBuilder egenskaber](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - i denne artikel beskrives alle parametre i detaljer.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
