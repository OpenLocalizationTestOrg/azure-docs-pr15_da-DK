<properties
   pageTitle="Azure datakatalog understøttede datakilder | Microsoft Azure"
   description="Specifikation af de aktuelt understøttede datakilder."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-supported-data-sources"></a>Azure datakatalog understøttede datakilder

Brugere af Azure datakatalog kan publicere metadata ved hjælp af en offentlige API, et Klik-én gang registrering værktøjet eller ved manuelt at angive oplysninger direkte til datakatalog web portal. I følgende gitter opsummerer alle datakilder, der understøttes af kataloget i dag og funktionerne til publicering for hver.  Vises også er de eksterne data-værktøjer, som hver kilde kan starte fra vores portal "Åbn i" oplevelse. Det andet gitter i artiklen har flere tekniske specifikation af forbindelsesegenskaber hver datakilder.


## <a name="list-of-supported-data-sources"></a>Liste over understøttede datakilder

<table>

    <tr>
       <td><b>Datakildeobjektet</b></td>
       <td><b>API</b></td>
       <td><b>Manuel indtastning</b></td>
       <td><b>Værktøjet til registrering af</b></td>
       <td><b>Åbn i værktøjer</b></td>
       <td><b>Noter</b></td>
    </tr>

    <tr>
      <td>Azure datalager sø Directory</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Data sø Store filer</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure-lager Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure-lager Directory</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure-lager tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS Directory</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS fil</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL-tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL-visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle-databasetabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle databasevisning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Andre (generisk aktiv)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL-lageret datatabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse-visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services Dimension</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services mål</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services-tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services-rapport</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Kun oprindelig tilstand-servere. SharePoint-tilstand understøttes ikke.</font></td>
    </tr>

    <tr>
      <td>SQL Server-tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server-visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata-tabel</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata-visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana visning</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Beregning og analytiske visninger. Attributten visninger understøttes ikke.</font></td>
    </tr>

    <tr>
      <td>Db2-tabel</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 visning</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Fil systemfil</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>FTP-mappe</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>FTP-fil</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-rapport</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-slutpunkt</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HTTP-fil</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Sæt af OData-enhed</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>OData-funktionen</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql-tabel</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql-visning</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana visning</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce-objekter</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint-liste </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Hvis du har brug for flere kilder, du sende en funktion anmodning, ved hjælp af [Azure datakatalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Specifikation af datakilde reference
> [AZURE.NOTE] "DSL struktur" kolonne i de følgende tabel kun lister forbindelsesegenskaber for "-adresse" egenskaben pose, der bruges af Azure datakatalog (det vil sige "-adresse" egenskaben pose kan indeholde andre forbindelsesegenskaber af datakilden som Azure datakatalog fortsætter, men som ikke anvender.)
<table>
    <tr>
       <td><b>Kildetype</b></td>
       <td><b>Aktivtype</b></td>
       <td><b>Objekttyper</b></td>
       <td><b>DSL struktur<b></td>
    </tr>
    <tr>
      <td>Azure sø datalager</td>
      <td>Objektbeholder</td>
      <td>Data sø</td>
      <td>
        <font size=2>protokol: webhdfs <br>godkendelse: {grundlæggende, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Azure sø datalager</td>
      <td>Tabel</td>
      <td>Mappe, fil</td>
      <td>
        <font size=2>protokol: webhdfs <br>godkendelse: {grundlæggende, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Azure-lager</td>
      <td>Objektbeholder</td>
      <td>Objektbeholder</td>
      <td>
        <font size=2>protokol: azure BLOB <br>godkendelse: {azure-access-nøgle} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domæne <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektbeholder</font>
      </td>
    </tr>
    <tr>
      <td>Azure-lager</td>
      <td>Tabel</td>
      <td>BLOB, mappe</td>
      <td>
        <font size=2>protokol: azure BLOB <br>godkendelse: {azure-access-nøgle} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domæne <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektbeholder <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Navn</font>
      </td>
    </tr>
    <tr>
      <td>Azure-lager</td>
      <td>Objektbeholder</td>
      <td>Objektbeholder</td>
      <td>
        <font size=2>protokol: azure-tabeller <br>godkendelse: {azure-access-nøgle} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domæne <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto</font>
      </td>
    </tr>
    <tr>
      <td>Azure-lager</td>
      <td>Tabel</td>
      <td>Tabel</td>
      <td>
        <font size=2>protokol: azure-tabeller <br>godkendelse: {azure-access-nøgle} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domæne <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Navn</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Objektbeholder</td>
      <td>Virtuel klynge</td>
      <td>
        <font size=2>protokol: cosmos <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabel</td>
      <td>Streame Stream sæt visning</td>
      <td>
        <font size=2>protokol: cosmos <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Objektbeholder</td>
      <td>Websted</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Rapport</td>
      <td>Dashboard-rapport</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: db2 <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: db2 <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema</font>
      </td>
    </tr>
    <tr>
      <td>Filsystemet</td>
      <td>Tabel</td>
      <td>Fil</td>
      <td>
        <font size=2>protokol: fil <br>godkendelse: {ingen, grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sti</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabel</td>
      <td>Mappe, fil</td>
      <td>
        <font size=2>protokol: ftp <br>godkendelse: {ingen, grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop-distribueret filsystem</td>
      <td>Objektbeholder</td>
      <td>Klynge</td>
      <td>
        <font size=2>protokol: webhdfs <br>godkendelse: {grundlæggende, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop-distribueret filsystem</td>
      <td>Tabel</td>
      <td>Mappe, fil</td>
      <td>
        <font size=2>protokol: webhdfs <br>godkendelse: {grundlæggende, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: hive <br>godkendelse: {hdinsight, grundlæggende, brugernavn, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: hive <br>godkendelse: {hdinsight, grundlæggende, brugernavn, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Objektbeholder</td>
      <td>Websted</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Rapport</td>
      <td>Dashboard-rapport</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Tabel</td>
      <td>Slutpunkt, fil</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: mysql <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: mysql <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Objektbeholder</td>
      <td>Enhed objektbeholder</td>
      <td>
        <font size=2>protokol: odata <br>godkendelse: {ingen, grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabel</td>
      <td>Enhed sæt, funktionen</td>
      <td>
        <font size=2>protokol: odata <br>godkendelse: {ingen, grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ressource</font>
      </td>
    </tr>
    <tr>
      <td>Oracle-Database</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: oracle <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Oracle-Database</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: oracle <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: postgresql <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: postgresql <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Objektbeholder</td>
      <td>Websted</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Rapport</td>
      <td>Dashboard-rapport</td>
      <td>
        <font size=2>protokol: http <br>godkendelse: {ingen, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Power-forespørgsel</td>
      <td>Tabel</td>
      <td>Miks for data</td>
      <td>
        <font size=2>protokol: power-forespørgsel <br>godkendelse: {oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabel</td>
      <td>Objekt</td>
      <td>
        <font size=2>protokol: salesforce-com <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;klasse <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Elementnavn</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Objektbeholder</td>
      <td>Server</td>
      <td>
        <font size=2>protokol: sap-hana-sql <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Tabel</td>
      <td>Få vist</td>
      <td>
        <font size=2>protokol: sap-hana-sql <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabel</td>
      <td>Liste</td>
      <td>
        <font size=2>protokol: sharepoint-liste <br>godkendelse: {grundlæggende, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse</font>
      </td>
    </tr>
    <tr>
      <td>SQL datawarehouse</td>
      <td>Kommandoen</td>
      <td>Lagret Procedure</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL datawarehouse</td>
      <td>TableValuedFunction</td>
      <td>Tabelfunktion</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL datawarehouse</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>SQL datawarehouse</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Kommandoen</td>
      <td>Lagret Procedure</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Tabelfunktion</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: tds <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;skema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionelle</td>
      <td>Objektbeholder</td>
      <td>Model</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionelle</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionelle</td>
      <td>Mål</td>
      <td>Mål</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {måling}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensionelle</td>
      <td>Tabel</td>
      <td>Dimension</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {Dimension}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Objektbeholder</td>
      <td>Model</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Mål</td>
      <td>Mål</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {måling}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Tabel</td>
      <td>Tabel</td>
      <td>
        <font size=2>protokol: analysis services <br>godkendelse: {windows, grundlæggende, anonym, ingen} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objekttype: {tabel}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Objektbeholder</td>
      <td>Server</td>
      <td>
        <font size=2>protokol: reporting services <br>godkendelse: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Rapport</td>
      <td>Rapport</td>
      <td>
        <font size=2>protokol: reporting services <br>godkendelse: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sti <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Objektbeholder</td>
      <td>Database</td>
      <td>
        <font size=2>protokol: teradata <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabel</td>
      <td>Tabel, visning</td>
      <td>
        <font size=2>protokol: teradata <br>godkendelse: {protokol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objekt</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Objektbeholder</td>
      <td>Model</td>
      <td>
        <font size="2">protokol: mssql mds <br>godkendelse: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tabel</td>
      <td>Enhed</td>
      <td>
        <font size="2">protokol: mssql mds <br>godkendelse: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-adresse <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;enhed</font>
      </td>
    </tr>
    <tr>
      <td>Andre (ikke en af ovenstående)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>protokol: standard aktiv <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;emneid</font>
      </td>
    </tr>
</table>
