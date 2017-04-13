<properties
    pageTitle="Sådan bruger du tabellagring fra PHP | Microsoft Azure"
    description="Lær, hvordan du bruger tjenesten tabel fra PHP til at oprette og slette en tabel, og indsætte, slette og forespørge tabellen."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Sådan bruger du tabellagring fra PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Azure-tabel. Eksemplerne er skrevet i PHP og bruge [Azure SDK til PHP][download]. De scenarier, der er omfattet omfatter **oprettelse og sletning af en tabel, og indsætte, slette, og forespørgsler objekter i en tabel**. Du kan finde flere oplysninger om tjenesten Azure-tabel, afsnittet [Næste trin](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Oprette et PHP tjenesteprogram

Det eneste krav til oprettelse af et PHP-program, der har adgang til tjenesten Azure-tabel er referering klasser i Azure SDK til PHP fra i din kode. Du kan bruge en hvilken som helst udviklingsværktøjer til at oprette dit program, herunder Notesblok.

I denne vejledning, kan du bruge tabellen servicefunktioner, der kan kaldes fra, i et PHP program lokalt eller i kode, der kører en Azure web rolle, arbejder rolle eller websted.

## <a name="get-the-azure-client-libraries"></a>Få Azure klientbiblioteker

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Konfigurere dit program tilladelse til at få adgang til tjenesten tabel

Hvis du vil bruge tjenesten Azure-tabel API'er, skal du:

1. Reference til automatisk indlæser filen ved hjælp af [require_once] [ require_once] -sætning, og
2. Henvise til en hvilken som helst klasser, du kan bruge.

I følgende eksempel viser, hvordan du medtage filen automatisk indlæser og henviser til klassen **ServicesBuilder** .

> [AZURE.NOTE] I dette eksempel (og andre eksemplerne i denne artikel) forudsætter, at du har installeret PHP klient billedbiblioteker til Azure via komponist. Hvis du har installeret bibliotekerne manuelt, skal du henvise til den <code>WindowsAzure.php</code> automatisk indlæser fil.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


I eksemplerne nedenfor, den `require_once` sætning altid vises, men kun de klasser, der er nødvendige for eksempel at udføre der refereres til.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurere en forbindelse til Azure-lager

For at oprette en Azure-tabel service-klient, skal du først har en gyldig forbindelsesstreng. Formatet for tabellen service forbindelsesstrengen er:

For at få adgang til en direkte tjeneste:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

For at få adgang til emulator lagerplads:

    UseDevelopmentStorage=true


Hvis du vil oprette en hvilken som helst Azure service-klienten, skal du bruge klassen **ServicesBuilder** . Du kan:

* overføre forbindelsesstrengen direkte til den eller
* bruge **CloudConfigurationManager (CCM)** til at kontrollere flere eksterne kilder til forbindelsesstrengen:
    * som standard leveres den med understøttelse af en ekstern kilde - miljøet variabler
    * Du kan tilføje nye kilder ved at forlænge klassen **ConnectionStringSource**

Eksempler, der er beskrevet her, sendes forbindelsesstrengen direkte.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Oprette en tabel

Et **TableRestProxy** objekt kan du oprette en tabel med metoden **createTable** . Når du opretter en tabel, kan du angive tabel service timeouten. (Se [Indstillingen Timeout for tabellen servicehandlinger]kan finde flere oplysninger om tabel service timeouten[table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Finde oplysninger om begrænsninger for tabelnavne, kan du se [om tabel Service datamodellen][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Hvis du vil tilføje et objekt til en tabel, skal du oprette et nyt **objekt** -objekt og sende dem til **TableRestProxy -> insertEntity**. Bemærk, at når du opretter et objekt, skal du angive en `PartitionKey` og `RowKey`. Disse er de entydige identifikatorer for et objekt og værdier, der kan forespørges meget hurtigere end andre objektegenskaber. Systemet bruger `PartitionKey` til automatisk at distribuere tabellens enheder over mange lagerplads noder. Objekter med samme `PartitionKey` er gemt på den samme node. (Udføre handlinger på flere enheder, der er gemt på den samme node bedre end på enheder, der er gemt på tværs af forskellige noder.) Den `RowKey` er det entydige ID for et objekt i en partition.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Finde oplysninger om egenskaber for tabel og typer, kan du se [om tabel Service datamodellen][table-data-model].

Klassen **TableRestProxy** indeholder to alternative metoder til at indsætte objekter: **insertOrMergeEntity** og **insertOrReplaceEntity**. Hvis du vil bruge disse metoder, du opretter et nyt **objekt** og sende dem som en parameter til begge metoder. Hver enkelt metode indsættes enheden, hvis den ikke findes. Hvis objektet, der allerede findes, **insertOrMergeEntity** opdateres egenskabsværdier, hvis egenskaberne, der allerede findes, og tilføjer nye egenskaber, hvis de ikke findes, mens **insertOrReplaceEntity** helt erstatter et eksisterende objekt. I følgende eksempel viser, hvordan du bruger **insertOrMergeEntity**. Hvis enheden med `PartitionKey` "tasksSeattle" og `RowKey` "1" ikke allerede findes, der skal indsættes. Men hvis det er tidligere indsat (som vist i eksemplet ovenfor), den `DueDate` egenskaben opdateres, og den `Status` egenskaben bliver tilføjet. Den `Description` og `Location` egenskaber er også opdateret, men med værdier, der effektivt lade dem være uændret. Hvis disse disse to egenskaber ikke blev tilføjet som vist i eksemplet, men fandtes på objektet mål, forbliver deres eksisterende værdier uændret.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Hente et enkelt objekt

Metoden **TableRestProxy -> getEntity** gør det muligt at hente et enkelt objekt ved at forespørge efter dens `PartitionKey` og `RowKey`. I eksemplet herunder tasten partition `tasksSeattle` og række nøgle `1` der overføres til metoden **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Hente alle objekter i en partition

Enhed forespørgsler er opbygget ved hjælp af filtre (få mere at vide under [forespørgsler tabeller og objekter][filters]). For at hente alle objekter i partition skal du bruge filteret "PartitionKey eq *partition_name*". Følgende eksempel viser, hvordan du kan hente alle objekter i den `tasksSeattle` partition ved at overføre et filter til metoden **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Hente et undersæt af objekter i en partition

Det samme mønster, der bruges i det forrige eksempel kan bruges til at hente et undersæt af objekter i en partition. Undersæt af enheder, du henter, bestemmes af det filter, du bruger (få mere at vide under [forespørgsler tabeller og objekter][filters]). I følgende eksempel viser, hvordan du bruger et filter til at hente alle enheder med et bestemt `Location` og en `DueDate` mindre end en bestemt dato.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Hente et undersæt af egenskaber for enheder

En forespørgsel kan hente et undersæt af objektegenskaber. Denne metode, kaldet *projicering*reducerer båndbredde og kan forbedre forespørgselsydeevne, især for store objekter. Hvis du vil angive en egenskab, der skal hentes, du overføre navnet på egenskaben til metoden **forespørgsel -> addSelectField** . Du kan ringe denne metode flere gange for at tilføje flere egenskaber. Efter udførelse **TableRestProxy -> queryEntities**, har de returnerede objekter kun de valgte egenskaber. (Hvis du vil returnere et undersæt af tabel enheder skal bruge et filter som vist i forespørgsler ovenfor.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Opdatere et objekt

Et eksisterende objekt kan opdateres ved at bruge metoderne **enhed -> AngivEgenskab** og **objekt -> addProperty** på objektet og derefter ringe **TableRestProxy -> updateEntity**. I følgende eksempel henter et objekt, ændrer én egenskab, fjerner en ny egenskab og tilføjer en ny egenskab. Bemærk, at du kan fjerne en egenskab ved at angive dens værdi til **null**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Slette et objekt

Hvis du vil slette et objekt, du overfører navnet på tabel, og enheden `PartitionKey` og `RowKey` til metoden **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Bemærk, at på dokumentsammenfald kontrol, du kan angive Etag for et objekt til at blive slettet ved hjælp af metoden **DeleteEntityOptions -> setEtag** og sende objektet **DeleteEntityOptions** skal **deleteEntity** som en fjerde parameter.

## <a name="batch-table-operations"></a>Batchen tabel handlinger

Metoden **TableRestProxy -> batchen** giver dig mulighed at udføre flere handlinger i en enkelt anmodning. Mønster her omfatter at føje handlinger til **BatchRequest** objekt og derefter overføre objektet **BatchRequest** til metoden **TableRestProxy -> batchen** . Hvis du vil tilføje en handling til en **BatchRequest** objekt, kan du ringe til en af følgende metoder flere gange:

* **addInsertEntity** (tilføjer handlingen insertEntity)
* **addUpdateEntity** (tilføjer handlingen updateEntity)
* **addMergeEntity** (tilføjer en handling af mergeEntity)
* **addInsertOrReplaceEntity** (tilføjer handlingen insertOrReplaceEntity)
* **addInsertOrMergeEntity** (tilføjer handlingen insertOrMergeEntity)
* **addDeleteEntity** (tilføjer en handling af deleteEntity)

I følgende eksempel viser, hvordan du kan udføre **insertEntity** og **deleteEntity** handlinger i en enkelt anmodning:

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Du kan finde flere oplysninger om samling tabel handlinger, se [Udføre enhed gruppe transaktioner][entity-group-transactions].

## <a name="delete-a-table"></a>Slette en tabel

Til sidst skal for at slette en tabel, skal du overføre tabelnavnet til metoden **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af tjenesten Azure-tabel, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

- Gå til [Azure-lager-teamets blog](http://blogs.msdn.com/b/windowsazurestorage/)

Du kan finde flere oplysninger, se også [PHP Developer Center](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
