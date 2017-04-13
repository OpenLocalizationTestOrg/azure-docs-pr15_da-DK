<properties
    pageTitle="Sådan bruger du Table storage fra Python | Microsoft Azure"
    description="Gem strukturerede data i skyen ved hjælp af Azure Table storage, et NoSQL datalager."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Sådan bruger du Table storage fra Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier via lagringstjeneste Azure-tabel. Eksemplerne er skrevet i Python og bruge [Microsoft Azure lagerplads SDK for Python]. De omfattede scenarier omfatter oprettelse og sletning af en tabel, ud over at indsætte og forespørgsler objekter i en tabel.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Oprette en tabel

Objektet **TableService** kan du arbejde med tabel-tjenester. Følgende kode opretter et **TableService** objekt. Føj kode i den øverste del af en Python fil, du ønsker fra et program adgang til Azure-lager:

    from azure.storage.table import TableService, Entity

Følgende kode opretter et **TableService** objekt ved at bruge lagerplads konto navn og konto.  Erstat 'min konto' og 'mykey' med dit kontonavn og din nøgle.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Hvis du vil tilføje et objekt, skal du først oprette en ordbog eller enhed, der definerer din enhedsnavne og værdier. Bemærk, at for hver enhed, skal du angive **PartitionKey** og **RowKey**. Dette er de entydige identifikatorer enhederne. Du kan forespørge ved hjælp af disse værdier meget hurtigere end kan du forespørge andre egenskaber. **PartitionKey** bruges til automatisk for at distribuere tabel enheder over mange lagerplads noder.
Objekter, der har den samme **PartitionKey** gemmes på den samme node. **RowKey** er det entydige ID for objektet i den partition, den hører til.

Hvis du vil tilføje et objekt i tabellen, du overfører en ordbog objekt til den **indsætte\_enhed** metode.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Du kan også sende en forekomst af klassen **enhed** til den **indsætte\_enhed** metode.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Opdatere et objekt

Denne kode viser, hvordan du erstatter den gamle version af et eksisterende objekt med en opdateret version.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Hvis den enhed, der opdateres ikke findes, mislykkes handlingen update. Hvis du vil gemme et objekt, uanset om det fandtes før, skal du bruge **indsætte\_eller\_replace_entity**.
I eksemplet nedenfor erstatte første opkaldet den eksisterende enhed. Det andet opkald vil indsætte et nyt objekt siden ingen enhed med den angivne **PartitionKey** og **RowKey** findes i tabellen.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Ændre en gruppe af objekter

Nogle gange giver det mening at sende flere handlinger sammen i en gruppe at sikre, at atomisk behandling af serveren. For at opnå, kan bruge du klassen **TableBatch** . Når du vil sende batchen, skal du ringe til **Anvend\_batchen**. Bemærk, at alle enheder skal være i samme partition for at kunne ændres som en batch. Eksemplet herunder tilføjer to objekter sammen i en gruppe.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Batches kan også bruges med contex manager syntaks:

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Forespørgsel til et objekt

Hvis du vil forespørge et objekt i en tabel, skal du bruge den **få\_enhed** metode ved at overføre **PartitionKey** og **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Forespørgsel et sæt af objekter

I dette eksempel finder alle opgaver i Seattle baseret på **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Forespørgsel et undersæt af egenskaber for enheder

En forespørgsel til en tabel kan hente blot nogle få egenskaber fra et objekt.
Denne metode, kaldet *projicering*reducerer båndbredde og kan forbedre forespørgselsydeevne, især for store objekter. Bruge parameteren **Vælg** og overføre navnene på de egenskaber, du vil overføre til klienten.

Forespørgslen i følgende kode returnerer kun beskrivelser af enheder i tabellen.

[AZURE.NOTE] Følgende kodestykke fungerer kun med skylagringstjeneste. Dette understøttes ikke af emulatoren lagerplads.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Slette et objekt

Du kan slette et objekt ved at bruge dets partition og række.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Slette en tabel

Følgende kode sletter en tabel fra en lagerplads-konto.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende tabel lagerplads, skal du følge disse links for at få mere at vide.

- [Python Developer Center](/develop/python/)
- [Azure-lager Services REST-API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure-lager-teamets Blog]
- [Microsoft Azure-lager SDK for Python]

[Azure-lager teamets blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure-lager SDK for Python]: https://github.com/Azure/azure-storage-python
