<properties 
    pageTitle="SQL Server gemt Procedure aktivitet" 
    description="Få mere at vide, hvordan du kan bruge den SQL Server gemt Procedure aktivitet til at starte en lagret procedure i en Azure SQL-Database eller Azure SQL Data Warehouse fra en Data Factory rørledning." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="spelluru"/>

# <a name="sql-server-stored-procedure-activity"></a>SQL Server gemt Procedure aktivitet
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

Du kan bruge SQL Server en lagret Procedure aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) til at kalde en lagret procedure i en af de følgende data butikker: 


- Azure SQL-Database 
- Azure SQL datawarehouse  
- SQL Server-Database i virksomheden eller en Azure VM. Du skal installere Datastyringsgateway på den samme computer, der er vært databasen eller på en anden computer for at undgå konkurrere for ressourcer med databasen. Datastyringsgateway er en software, der forbinder lokale datakilder kilder/data skabe flaskehals i Azure FOS til skytjenester på en sikker og administrerede måde. Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway. 

I denne artikel bygger på artiklen [data transformation aktiviteter](data-factory-data-transformation-activities.md) , som viser en generel oversigt over transformation af data og de understøttede transformation aktiviteter.

## <a name="walkthrough"></a>Gennemgang

### <a name="sample-table-and-stored-procedure"></a>Eksempel på tabellen og lagret procedure
1. Oprette den følgende **tabel** i din Azure SQL-Database ved hjælp af SQL Server Management Studio eller et andet værktøj, du er tryg ved. Kolonnen datetimestamp er dato og klokkeslæt, når det tilsvarende-ID genereres. 

        CREATE TABLE dbo.sampletable
        (
            Id uniqueidentifier,
            datetimestamp nvarchar(127)
        )
        GO

        CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
        GO

    -Id'et er entydigt identificeret og kolonnen datetimestamp er dato og klokkeslæt, når det tilsvarende-ID genereres.
    ![Eksempeldata](./media/data-factory-stored-proc-activity/sample-data.png)

    > [AZURE.NOTE] Dette eksempel bruger Azure SQL-Database, men det fungerer på samme måde for Azure SQL Data Warehouse og SQL Server-Database. 
2. Oprette den følgende **lagret procedure** , som indsætter data i til **sampletable**.

        CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
        AS
        
        BEGIN
            INSERT INTO [sampletable]
            VALUES (newid(), @DateTime)
        END

    > [AZURE.IMPORTANT] **Navn** og **hus** af parameteren (DateTime i dette eksempel) skal svare til parameter, der er angivet i pipeline/aktiviteten JSON. I den lagrede procedure definition Sørg for, at **@** bruges som et præfiks for parameteren.
    
### <a name="create-a-data-factory"></a>Oprette en data fabrik  
4. Log på [Azure-portalen](https://portal.azure.com/). 
5. Klik på **Ny** i menuen til venstre, skal du klikke på **Intelligence + analyser**og klik på **Data Factory**.
    
    ![Nye data factory](media/data-factory-stored-proc-activity/new-data-factory.png)   
4.  Angiv **SProcDF** for navnet i bladet **nye data factory** . Azure Data Factory navne er **globalt entydige**. Du skal indsættes tegn foran navnet på data factory med dit navn, til at aktivere en vellykket gennemførelse af fabriksindstillingerne.

    ![Nye data factory](media/data-factory-stored-proc-activity/new-data-factory-blade.png)      
3.  Vælg dit **Azure-abonnement**. 
4.  For **Ressourcegruppe**skal du gøre et af følgende trin: 
    1.  Klik på **Opret ny** , og Angiv et navn til ressourcegruppen.
    2.  Klik på **Brug eksisterende** , og vælg en eksisterende ressourcegruppe.  
5.  Vælg en **placering** til data factory.
6.  Vælg **Fastgør til dashboard** , så du kan se data factory på dashboardet til næste gang du logger på. 
6.  Klik på **Opret** på bladet **nye data factory** .
6.  Du kan se data fabriksindstillingerne der oprettes i **dashboard** i portalen Azure. Når data factory er blevet oprettet, kan du se siden fabrik, som viser indholdet af data factory.
    ![Data Factory-startsiden](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Oprette en Azure SQL sammenkædet tjeneste  
Når du har oprettet data factory, opretter du en Azure SQL sammenkædet service, der kæder Azure SQL-Database til fabriksindstillingerne data. Denne database indeholder sampletable tabel og sp_sample lagret procedure.

7.  Klik på **forfatter og installere** på bladet **Data Factory** for **SProcDF** til start af Data Factory-Editor.
2.  Klik på **nye data gemmes** på kommandolinjen, og vælg **Azure SQL-Database**. Du bør se JSON scriptet til at oprette en Azure SQL sammenkædet tjeneste i editoren. 

    ![Nyt datalager](media/data-factory-stored-proc-activity/new-data-store.png)
4. Foretage følgende ændringer i JSON-script: 
    1. Erstatte ** &lt;servernavn&gt; ** med navnet på din server Azure SQL-Database.
    2. Erstatte ** &lt;databasenavn&gt; ** med den database, hvor du har oprettet tabellen og den lagrede procedure.
    3. Erstatte ** &lt; username@servername ** med den brugerkonto, der har adgang til databasen.
    4. Erstatte ** &lt;adgangskode&gt; ** med adgangskoden for kontoen. 

    ![Nyt datalager](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
5. Klik på **Implementer** på kommandolinjen til at udrulle sammenkædede tjenesten. Bekræft, at du ser AzureSqlLinkedService i trævisningen i venstre side. 

    ![visning som træstruktur med sammenkædede service](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Oprette et output datasæt
6. Klik på **... Flere** på værktøjslinjen, klik på **nyt datasæt**, og på **Azure SQL**. **Nye datasæt** på kommandolinjen og vælge **Azure SQL**.

    ![visning som træstruktur med sammenkædede service](media/data-factory-stored-proc-activity/new-dataset.png)
7. Kopiér og Indsæt følgende JSON-script i til JSON-editoren.

        {               
            "name": "sprocsampleout",
            "properties": {
                "type": "AzureSqlTable",
                "linkedServiceName": "AzureSqlLinkedService",
                "typeProperties": {
                    "tableName": "sampletable"
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }
7. Klik på **Implementer** på kommandolinjen til at udrulle datasættet. Bekræft, at du ser datasættet i trævisningen. 

    ![visning som træstruktur med sammenkædede tjenester](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Oprette en rørledning med SqlServerStoredProcedure aktivitet
Nu, Lad os oprette en rørledning med en SqlServerStoredProcedure aktivitet.
 
9. Klik på **... Flere** kommando liggende søjle- og klikke på **ny pipeline**. 
9. Kopiér og Indsæt den følgende JSON kodestykke. **StoredProcedureName** indstillet til **sp_sample**. Navn og hus af parameteren **dato/klokkeslæt** skal svare til navnet og hus af parameteren i den lagrede procedure definition.  

        {
            "name": "SprocActivitySamplePipeline",
            "properties": {
                "activities": [
                    {
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
                            "storedProcedureName": "sp_sample",
                            "storedProcedureParameters": {
                                "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                            }
                        },
                        "outputs": [
                            {
                                "name": "sprocsampleout"
                            }
                        ],
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "SprocActivitySample"
                    }
                ],
                "start": "2016-08-02T00:00:00Z",
                "end": "2016-08-02T05:00:00Z",
                "isPaused": false
            }
        }

    Hvis du skal overføre null for en parameter, kan du bruge syntaksen: "param1": null (kun små bogstaver). 
9. Klik på **Implementer** på værktøjslinjen for at installere rørledningen.  

### <a name="monitor-the-pipeline"></a>Overvåge pipeline

6. Klik på **X** for at lukke Data Factory Editor blade og gå tilbage til bladet Data Factory, og klik på **Diagram**.

    ![diagram felt](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
7. Du kan se en oversigt over rørledninger og datasæt, der bruges i dette selvstudium i **Diagramvisning**. 

    ![diagram felt](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
8. Dobbeltklik på datasæt **sprocsampleout**i visningen Diagram. Du kan se udsnit i klar tilstand. Der skal være fem udsnit, fordi et udsnit er oprettet i hver time mellem starttidspunkt og sluttidspunkt fra JSON.

    ![diagram felt](media/data-factory-stored-proc-activity/data-factory-slices.png) 
10. Når der er et udsnit i **klar** tilstand, skal du køre * *Vælg* sampletable** forespørgslen i SQL Azure-databasen for at bekræfte, at dataene er indsat i til tabellen ved den lagrede procedure.

    ![Outputdata](./media/data-factory-stored-proc-activity/output.png)

    Du kan finde detaljerede oplysninger om overvågning Azure Data Factory rørledninger i [skærm pipeline](data-factory-monitor-manage-pipelines.md) .  

> [AZURE.NOTE] I dette eksempel har på SprocActivitySample ikke input. Hvis du vil sammenkæde denne aktivitet med en aktivitet strømmen (det vil sige, forudgående behandling), lagrer mod strømmen aktivitetens kan bruges som input i denne aktivitet. I så fald køres denne aktivitet ikke før den foregående aktivitet er fuldført og output af de aktiviteter, der tidligere er tilgængelige (i klar status). Input kan ikke bruges direkte som en parameter til den lagrede procedure aktivitet

## <a name="json-format"></a>JSON-formatet
    {
        "name": "SQLSPROCActivity",
        "description": "description", 
        "type": "SqlServerStoredProcedure",
        "inputs":  [ { "name": "inputtable"  } ],
        "outputs":  [ { "name": "outputtable" } ],
        "typeProperties":
        {
            "storedProcedureName": "<name of the stored procedure>",
            "storedProcedureParameters":  
            {
                "param1": "param1Value"
                …
            }
        }
    }

## <a name="json-properties"></a>JSON egenskaber

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Navn | Navnet på aktiviteten | Ja
Beskrivelse | En beskrivelse af, hvad aktiviteten skal bruges til | Nej
type | SqlServerStoredProcedure | Ja
input | Valgfrit. Hvis du angiver et input datasæt, den skal være tilgængelig (i 'Klar' status) for den lagrede procedure aktivitet til at køre. Indtast datasættet kunne ikke bruges i den lagrede procedure som en parameter. Det bruges kun til at kontrollere afhængigheden før du starter den lagrede procedure aktivitet. | Nej
output | Du skal angive et output datasæt for en lagret procedure aktivitet. Output datasæt angiver **tidsplan** for den lagrede procedure aktivitet (time, ugentligt, månedligt, osv.). <br/><br/>Output datasættet skal bruge en **sammenkædet tjeneste** , som refererer til en Azure SQL-Database eller en Azure SQL Data Warehouse eller SQL Server-Database, du vil den lagrede procedure til at køre. <br/><br/>Output datasættet kan fungere som en metode til at sende resultatet af den lagrede procedure for efterfølgende behandling i en anden aktivitet ([sammenkædning aktiviteter](data-factory-scheduling-and-execution.md#chaining-activities)) i pipeline. Dog kan Data Factory ikke automatisk skrive output fra en lagret procedure til dette dataset. Det er den lagrede procedure, der skriver til en SQL-tabel, der output datasættet peger på. <br/><br/>I nogle tilfælde kan output datasættet være et **dummy datasæt**, som bruges kun til at angive, hvornår til kørsel af den lagrede procedure aktivitet. | Ja
storedProcedureName | Angiv navnet på den lagrede procedure i Azure SQL-database eller Azure SQL-datalager, der er repræsenteret af sammenkædede tjenesten, der bruger outputtabellen. | Ja
storedProcedureParameters | Angiv værdier for lagret procedureparametre. Hvis du vil overføre null for en parameter, skal du bruge syntaksen: "param1": null (alle små bogstaver). Se følgende eksempel på at lære at bruge denne egenskab.| Nej

## <a name="passing-a-static-value"></a>Overføre en statisk værdi 
Nu, Lad os se tilføje en anden kolonne med navnet 'Scenarie' i den tabel, der indeholder en statisk værdi med navnet 'Dokument eksempel'.

![Eksempeldata 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

    CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
    
    AS
    
    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime, @Scenario)
    END

Send nu parameteren scenarie og værdien fra den lagrede procedure aktivitet. Afsnittet typeProperties i det foregående eksempel ser ud som følgende kodestykke:

    "typeProperties":
    {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": 
        {
            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
            "Scenario": "Document sample"
        }
    }

