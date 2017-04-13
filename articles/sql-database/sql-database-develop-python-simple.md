<properties
    pageTitle="Oprette forbindelse til SQL-Database ved hjælp af Python | Microsoft Azure"
    description="Viser et eksempel på Python kode du kan bruge til at oprette forbindelse til Azure SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Oprette forbindelse til SQL-Database ved hjælp af Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Dette emne viser, hvordan til at oprette forbindelse og forespørge Azure SQL-Database ved hjælp af Python. Du kan køre dette eksempel fra Windows, Ubuntu Linux eller Mac-platforme.


## <a name="step-1-create-a-sql-database"></a>Trin 1: Oprette en SQL-database

Se på [siden Introduktion](sql-database-get-started.md) til at se, hvordan du opretter en eksempeldatabasen.  Det er vigtigt du følge vejledningen for at oprette en **AdventureWorks databaseskabelon**. Eksemplerne nedenfor fungerer kun med **AdventureWorks skemaet**. Når du har oprettet databasen foretager sikker på, at aktiverer du adgang til din IP-adresse ved at aktivere firewallreglerne, som beskrevet i på [siden Introduktion](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Trin 2: Konfigurer udviklingsmiljø

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Installere de nødvendige moduler
Åbn din terminal og installere

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Åbn din terminal, og gå til en mappe, hvor du har planer om at oprette din python script. Skriv følgende kommandoer for at installere **FreeTDS** og **pymssql**. pymssql bruger FreeTDS til at oprette forbindelse til SQL-databaser.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Du kan installere pymssql fra [**her**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Kontrollér, at du vælger den korrekte whl-fil. For eksempel: Hvis du bruger Python 2.7 på en 64-bit maskine vælge: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Når du har hentet Placer filen .whl den i mappen C:/Python27.

Nu installere pymssql driveren ved hjælp af pip fra kommandolinjen. cd i C:/Python27, og Kør følgende
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Du kan finde instruktioner til at aktivere Brug pip [her](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Trin 3: Køre eksempelkode

Oprette en fil kaldet **sql_sample.py** , og Indsæt følgende kode indeni. Du kan udføre dette fra kommandolinjen ved hjælp af:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Oprette forbindelse til din SQL-Database

Funktionen [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) bruges til at oprette forbindelse til SQL-Database.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Udføre en SQL SELECT-sætning

Funktionen [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) kan bruges til at hente et resultatsæt fra en forespørgsel med SQL-Database. Denne funktion grundlæggende accepterer en forespørgsel, og returnerer et resultatsæt, der kan være nævnt med brug af [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Indsætte en række, overføre parametre og hente den oprettede primære nøgle

I SQL-Database kan egenskaben [identitet](https://msdn.microsoft.com/library/ms186775.aspx) og objektet [SEKVENS](https://msdn.microsoft.com/library/ff878058.aspx) bruges til at generere [primær nøgle](https://msdn.microsoft.com/library/ms179610.aspx) -værdier. 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transaktioner


Denne kodeeksempel demonstrerer brugen af transaktioner, hvor du:

* Starte en transaktion
* Indsætte en række med data
* Rollback din transaktion at fortryde Indsæt 

Indsæt følgende kode i sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Næste trin

* Gennemgå [SQL Database udvikling oversigt](sql-database-develop-overview.md)
* Flere oplysninger om [Microsoft Python Driver til SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Besøg [Python Developer Center](/develop/python/).

## <a name="additional-resources"></a>Yderligere ressourcer 

* [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Udforske alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/)
