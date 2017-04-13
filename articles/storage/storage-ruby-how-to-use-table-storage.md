<properties
    pageTitle="Sådan bruger du Azure Table Storage fra fonetisk | Microsoft Azure"
    description="Gem strukturerede data i skyen ved hjælp af Azure Table storage, et NoSQL datalager."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Sådan bruger du Azure Table Storage fra fonetisk

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Azure-tabel. Eksemplerne skrives ved hjælp af fonetisk API. De scenarier, der er omfattet omfatter **oprettelse og sletning af en tabel, indsætte og forespørgsler objekter i en tabel**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Oprette et fonetisk tjenesteprogram

For at få instruktioner hvordan du opretter et fonetisk program i [fonetisk på gør webprogram på en Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Konfigurere dit program tilladelse til at få adgang til lager

Hvis du vil bruge Azure-lager, skal du hente og bruge fonetisk azure pakken, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-rubygems-to-obtain-the-package"></a>Brug RubyGems til at hente pakken

1. Brug en kommandolinjeparametre som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).

2. Skriv **gem installere azure** i kommandovinduet til at installere gem og afhængigheder.

### <a name="import-the-package"></a>Importere pakken

Brug din foretrukne teksteditor, skal du tilføje følgende til toppen af den fonetisk fil, hvor du vil bruge lagerplads:

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Konfigurere en forbindelse til Azure-lager

Modulet azure læser miljøvariabler **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS\_nøgle** oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger inden du bruger **Azure::TableService** med følgende kode:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Sådan hentes disse værdier fra en klassisk eller Ressourcestyring lagerplads konto i portalen Azure:

1. Log på [Azure-portalen](https://portal.azure.com).
2. Naviger til kontoen lagerplads, du vil bruge.
3. Klik på **Access-taster**i bladet indstillinger til højre.
4. I Access-taster bladet, der vises, får du vist hurtigtast 1 og hurtigtast 2. Du kan bruge en af disse. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder. 

Sådan hentes disse værdier fra en klassisk lagerplads konto i klassisk Azure-portalen:

1. Log på [Klassisk Azure portal](https://manage.windowsazure.com).
2. Naviger til kontoen lagerplads, du vil bruge.
3. Klik på **ADMINISTRER HURTIGTASTER** nederst i navigationsruden.
4. I pop op-dialogboksen, får du vist kontonavn lager, primære hurtigtast og sekundær hurtigtast. Du kan bruge den primære liste eller et sekundær for hurtigtast. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder.

## <a name="create-a-table"></a>Oprette en tabel

Objektet **Azure::TableService** kan du arbejde med tabeller og objekter. Hvis du vil oprette en tabel, skal du bruge den **oprette\_table()** metode. I følgende eksempel oprettes en tabel eller Udskriv fejlen, hvis der ikke er nogen.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Hvis du vil tilføje et objekt, skal du først oprette en hash-objekt, der definerer egenskaber for din enhed. Bemærk, at for hver enhed skal du angive en **PartitionKey** og **RowKey**. Dette er de entydige identifikatorer enhederne og værdier, der kan forespørges meget hurtigere end andre egenskaber. Azure-lager bruger **PartitionKey** til automatisk for at distribuere tabellens enheder over mange lagerplads noder. Objekter med det samme **PartitionKey** gemmes på den samme node. **RowKey** er det entydige ID for objektet i den partition, den hører til.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Opdatere et objekt

Der er flere metoder til at opdatere et eksisterende objekt:

* **opdatere\_entity():** Opdatere et eksisterende objekt ved at erstatte den.
* **Flet\_entity():** Opdaterer en eksisterende enhed ved at flette nye egenskabsværdier til den eksisterende enhed.
* **Indsæt\_eller\_Flet\_entity():** Opdaterer en eksisterende enhed ved at erstatte den. Hvis der findes ingen objekt, der skal indsættes en ny:
* **Indsæt\_eller\_erstatte\_entity():** Opdaterer en eksisterende enhed ved at flette nye egenskabsværdier til den eksisterende enhed. Hvis der findes ingen objekt, indsættes der en ny.

I følgende eksempel vises opdatering af en enhed ved hjælp af **opdatere\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Med **opdatere\_entity()** og **Flet\_entity()**, hvis det objekt, du vil opdatere ikke findes, og klik derefter på opdateringshandling mislykkes. Derfor Hvis du vil gemme et objekt, uanset om de allerede forefindes, skal du i stedet bruge **indsætte\_eller\_erstatte\_entity()** eller **indsætte\_eller\_Flet\_entity()**.

## <a name="work-with-groups-of-entities"></a>Arbejde med grupper af enheder

Nogle gange giver det mening at sende flere handlinger sammen i en gruppe at sikre, at atomisk behandling af serveren. For at gennemføre, skal du først oprette en **batchen** objekt og derefter bruge den **udføre\_batch()** metode på **TableService**. I følgende eksempel demonstrerer afsendelse af to objekter med RowKey 2 og 3 i en gruppe. Bemærk, at det virker kun for objekter med det samme PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Forespørgsel til et objekt

Hvis du vil forespørge et objekt i en tabel, skal du bruge den **få\_entity()** metode, ved at overføre tabelnavn, **PartitionKey** og **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Forespørgsel et sæt af objekter

Hvis du vil forespørge et sæt af objekter i en tabel, skal du oprette en forespørgsel hash-objekt og bruge den **forespørgsel\_entities()** metode. I følgende eksempel viser få alle enheder med det samme **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Hvis resultatsættet er for stor til en enkelt forespørgsel til at returnere et fortsættelse token returneres som du kan bruge til at hente efterfølgende sider.

## <a name="query-a-subset-of-entity-properties"></a>Forespørgsel et undersæt af egenskaber for enheder

En forespørgsel til en tabel kan hente blot nogle få egenskaber fra et objekt. Denne metode, kaldet "projicering" reducerer båndbredde og kan forbedre forespørgselsydeevne, især for store objekter. Bruge select-delsætningen og overføre navnene på de egenskaber, du gerne vil tage over til klienten.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Slette et objekt

Hvis du vil slette et objekt, skal du bruge den **slette\_entity()** metode. Du skal overføre navnet på den tabel, som indeholder enheden, PartitionKey og RowKey af objektet.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Slette en tabel

Hvis du vil slette en tabel, skal du bruge den **slette\_table()** metode og gang navnet på den tabel, du vil slette.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Næste trin

Hvis du vil lære mere komplekse lagerplads opgaver, skal du følge disse links:

- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure SDK til fonetisk](http://github.com/WindowsAzure/azure-sdk-for-ruby) lager på GitHub
