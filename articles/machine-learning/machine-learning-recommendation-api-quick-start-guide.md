<properties 
    pageTitle="Guiden Hurtig start: Machine Learning anbefalinger API | Microsoft Azure" 
    description="Azure Machine Learning anbefalinger - guiden Hurtig Start" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Startvejledning til Machine Learning anbefalinger API

>[AZURE.NOTE]Du skal begynde at bruge tjenesten anbefalinger API Kognitiv i stedet for denne version. Tjenesten anbefalinger Kognitiv vil erstatte denne tjeneste, og alle de nye funktioner udvikles der. Der er nye funktioner som "samling" support, en bedre API Explorer, en renere API grundflade, mere ensartet tilmelding/fakturering oplevelse, osv.
> Lær mere om [overførsel til den nye Kognitiv tjeneste](http://aka.ms/recomigrate)


Dette dokument beskrives, hvordan til indbyggede din tjeneste eller program tilladelse til at bruge Microsoft Azure Machine Learning anbefalinger. Du kan finde flere oplysninger under anbefalinger API i [galleriet](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Generel oversigt

Hvis du vil bruge Azure Machine Learning anbefalinger, skal du benytte følgende fremgangsmåde:

* Oprette en model – en model er en objektbeholder af dine brugerdata, katalogdata og anbefaling modellen.
* Importér katalogdata - et katalog indeholder metadataoplysninger om elementerne. 
* Importér Brugsdata - Brugsdata kan overføres i en af 2 måder (eller begge):
    * Ved at overføre en fil, der indeholder brugerdataene.
    * Ved at sende data acquisition begivenheder.
    Normalt kan du overføre en brugen fil for at kunne oprette en indledende anbefaling model (bootstrap) og bruge det, indtil systemet samler nok data ved hjælp af acquisition dataformat.
* Oprette en anbefaling model – dette er en asynkron handling, hvori anbefaling systemet tager alle Brugsdata og opretter en anbefaling model. Denne handling kan tage flere minutter eller flere timer afhængigt af størrelsen af dataene og parametrene build konfiguration. Når udløser Opret, får du et build-ID. Bruge det til at kontrollere, hvornår build processen er afsluttet før du begynder at bruge anbefalinger.
* Bruge anbefalinger - få anbefalinger til et bestemt element eller en liste over elementer.

Alle ovenstående trin sker via Azure Machine Learning anbefalinger API.  Du kan hente en Northwind, der implementerer hvert af disse trin fra den [galleriet samt.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Begrænsninger

* Det maksimale antal modeller per abonnement er 10.
* Det maksimale antal elementer, der kan indeholde et katalog er 100.000.
* Det maksimale antal brugen punkter, gemmes er ~ 5,000,000. Den ældste slettes om nye skal overføres eller rapporteret.
* Den maksimale størrelse af data, der kan sendes i INDLÆG (fx Importér katalogdata, importere Brugsdata) er 200MB.
* Antallet af transaktioner i sekundet for et anbefaling model build, der ikke er aktivt er ~ 2TPS. Et anbefaling model build, der er aktive kan indeholde til 20TPS.

##<a name="integration"></a>Integration

###<a name="authentication"></a>Godkendelse
Micosoft Azure Marketplace understøtter enten Basic eller OAuth godkendelsesmetode. Du kan nemt finde tasterne konto ved at navigere til de pågældende taster på markedet under [indstillingerne for din konto](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Grundlæggende godkendelse
Tilføje godkendelse sidehoved:

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Konvertere til Base64 (C#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Konvertere til Base64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>Tjenesten URI
Tjenesten roden URI til Azure Machine Learning anbefalinger API'er er [her.](https://api.datamarket.azure.com/amla/recommendations/v2/)

Tjenesten fuld URI udtrykkes ved hjælp af elementer i OData-specifikationen.

###<a name="api-version"></a>API-version
Hver API-kald får til sidst en forespørgselsparameter, kaldet apiVersion, skal du vælge "1.0".

###<a name="ids-are-case-sensitive"></a>Der skelnes mellem id'er
Id'er, der returneres af en af API'erne, der skelnes mellem og skal bruges som f.eks, når de overføres som parametre i efterfølgende API-opkald. For eksempel er model id'er og katalog-id'er store og små bogstaver.

###<a name="create-a-model"></a>Oprette en model
Oprette en "Opret model" anmodning:

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelName   |   Kun bogstaver (A-Å, a-å), tal (0-9), bindestreger (--) og understregningstegnet (_) er tilladt.<br>Maksimal længde: 20 |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |


**Svar**:

HTTP-statuskode: 200

- `feed/entry/content/properties/id`-Indeholder model-ID.
**Bemærk**: der skelnes mellem model-ID.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importere katalogdata

Hvis du overføre flere katalogfiler til den samme modellen med flere opkald, indsættes vi kun de nye katalogelementer. Eksisterende elementer, forbliver med de oprindelige værdier.

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen (store og små bogstaver)  |
| filnavn | Tekstbaseret id for kataloget.<br>Kun bogstaver (A-Å, a-å), tal (0-9), bindestreger (--) og understregningstegnet (_) er tilladt.<br>Maksimal længde: 50 |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | Katalogfletning data. Format:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Navn</th><th>Obligatorisk</th><th>Type</th><th>Beskrivelse</th></tr><tr><td>Element-Id</td><td>Ja</td><td>Alfanumeriske, maksimal længde 50</td><td>Entydigt id for et element</td></tr><tr><td>Elementnavn</td><td>Ja</td><td>Alfanumeriske, maksimal længde på 255</td><td>Elementnavn</td></tr><tr><td>Element kategori</td><td>Ja</td><td>Alfanumeriske, maksimal længde på 255</td><td>Kategori, som dette element tilhører (fx madlavning bøger, Drama...)</td></tr><tr><td>Beskrivelse</td><td>Nej</td><td>Alfanumeriske, maksimal længde 4000</td><td>Beskrivelse af elementet</td></tr></table><br>Maksimale filstørrelse er 200MB.<br><br>Eksempel:<br><pre>2406e770-769c-4189-89de-1c9283f93a96, Clara Callan adressekartotek<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, rummet glemme: fakta (Byzantium mappe), reserver<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework, adressekartotek<br>552a1940-21e4-4399-82bb-594b46d7ed54, begrænsning af dyr, adressekartotek</pre> |


**Svar**:

HTTP-statuskode: 200

- `Feed\entry\content\properties\LineCount`-Antal linjer, der er accepteret.
- `Feed\entry\content\properties\ErrorCount`-Antal linjer, der ikke blev indsat på grund af en fejl.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importere data om brug af

####<a name="uploading-a-file"></a>Overføre en fil
Dette afsnit viser, hvordan du overføre Brugsdata ved hjælp af en fil. Du kan ringe til denne API flere gange med data om brug af. Alle brugerdata, gemmes for alle opkald.

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen (store og små bogstaver) |
| filnavn | Tekstbaseret id for kataloget.<br>Kun bogstaver (A-Å, a-å), tal (0-9), bindestreger (--) og understregningstegnet (_) er tilladt.<br>Maksimal længde: 50 |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | Data om brug af. Format:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Navn</th><th>Obligatorisk</th><th>Type</th><th>Beskrivelse</th></tr><tr><td>Bruger-Id</td><td>Ja</td><td>Alfanumeriske</td><td>Entydigt id for en bruger</td></tr><tr><td>Element-Id</td><td>Ja</td><td>Alfanumeriske, maksimal længde 50</td><td>Entydigt id for et element</td></tr><tr><td>Tid</td><td>Nej</td><td>Dato i formatet: åååå/MM/ååååTtt (fx 2013/06/20T10:00:00)</td><td>Klokkeslættet for data</td></tr><tr><td>Begivenhed</td><td>Nej, hvis leveret før du skal også placerer dato</td><td>Et af følgende:<br>• Klik på<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Køb</td><td></td></tr></table><br>Maksimale filstørrelse er 200MB.<br><br>Eksempel:<br><pre>149452, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Svar**:

HTTP-statuskode: 200

- `Feed\entry\content\properties\LineCount`-Antal linjer, der er accepteret.
- `Feed\entry\content\properties\ErrorCount`-Antal linjer, der ikke blev indsat på grund af en fejl.
- `Feed\entry\content\properties\FileId`-Fil id.


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>Brug af dataindsamling
Dette afsnit viser, hvordan du sende hændelser i realtid til Azure Machine Learning anbefalinger, som regel fra dit websted.

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Anmodningsteksten| Begivenhed dataindtastning for hver enkelt hændelse, du vil sende. Du skal sende til den samme bruger eller browser session samme-ID i feltet session-id. (Se eksempel på brødteksten hændelsen nedenfor).|


- Eksempel på hændelse 'Klik':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Eksempel på hændelse 'RecommendationClick':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Eksempel på hændelse 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Eksempel på hændelse 'RemoveShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Eksempel på hændelse 'Købe':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Eksempel på at sende '2 begivenheder Klik på' og 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Svar**: HTTP statuskode: 200

###<a name="build-a-recommendation-model"></a>Oprette en anbefaling model

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen (store og små bogstaver)  |
| userDescription | Tekstbaseret id for kataloget. Bemærk, at hvis du bruger mellemrum skal du kode det med % 20 i stedet. Se eksemplet ovenfor.<br>Maksimal længde: 50 |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Dette er en asynkron API. Du får et build-ID som et svar. Hvis du vil vide, når Opret er afsluttet, skal du ringe "Få opbygger Status for en Model" API og finde dette build-ID i svaret. Bemærk, at et build kan tage fra minutter til timer afhængigt af størrelsen af dataene.

Du kan ikke forbruge anbefalinger indtil Opret slutter.

Gyldige build status:

- Opret-modellen blev oprettet.
- I kø – Model build blev udløst og det er i kø.
- Bygning – Model bygger.
- Succes – Build afsluttet.
- Fejl – Build afsluttet med en fejl.
- Annulleret – blev Build annulleret.
- Annullering af – Build annulleres.


Bemærk, at build-ID kan findes under følgende sti:`Feed\entry\content\properties\Id`

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Få status for oprettelse af en model

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId         |   Entydigt id for modellen (store og små bogstaver)    |
|   onlyLastBuild   |   Angiver, om der skal returneres alle build oversigten over modellen eller kun status for det seneste build. |
|   apiVersion      |   1.0                                 |


**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post per build. Hvert element har følgende data:

- `feed/entry/content/properties/UserName`– Navnet på brugeren.
- `feed/entry/content/properties/ModelName`– Navnet på modellen.
- `feed/entry/content/properties/ModelId`– Entydigt id-model.
- `feed/entry/content/properties/IsDeployed`– Om Opret er implementeret (kaldes også aktive build).
- `feed/entry/content/properties/BuildId`– Oprette entydigt id.
- `feed/entry/content/properties/BuildType`-Opret type.
- `feed/entry/content/properties/Status`– Oprette status. Kan være en af følgende: fejl dokumentkomponent i kø, Cancelling, annulleret, og succes
- `feed/entry/content/properties/StatusMessage`– Detaljerede statusmeddelelse (gælder kun for bestemte stater).
- `feed/entry/content/properties/Progress`– Oprette status (%).
- `feed/entry/content/properties/StartTime`– Build starttidspunkt.
- `feed/entry/content/properties/EndTime`– Oprette sluttidspunkt.
- `feed/entry/content/properties/ExecutionTime`– Oprette varighed.
- `feed/entry/content/properties/ProgressStep`– Oplysninger om den aktuelle fase, som er et build i gang i.

Gyldige build status:
- Oprettet – blev Build anmodning om posten oprettet.
- I kø – Build anmodningen blev udløst og det er i kø.
- Bygning – Build er i gang.
- Succes – Build afsluttet.
- Fejl – Build afsluttet med en fejl.
- Annulleret – blev Build annulleret.
- Annullering af – Build annulleres.

Gyldige værdier for build type:
- Rangen - rangen opbygge. (For rang opbygge detaljer, skal du henvise til "Machine Learning anbefaling API dokumentation" dokumentet).
- Anbefaling - anbefaling build.
- FBT - købte ofte sammen build.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Få anbefalinger

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen (store og små bogstaver) |
| at ItemID'er | Kommasepareret liste over de elementer, der anbefales til.<br>Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater |
| includeMetatadata | Fremtidig brug altid false |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200

Svaret indeholder én post per anbefalede element. Hvert element har følgende data:

- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

XML-OData

Eksempel svaret nedenfor indeholder 10 anbefalede elementer:

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Opdatere model
Du kan opdatere beskrivelsen af model eller det aktive build-ID.
*Aktive build-ID* - hver build for hver model har en build-ID. Det aktive build-ID er den første vellykkede build af hver ny model. Når du har et aktivt build-ID, og du gøre yderligere opbygger for den samme model, du skal eksplicit angive den som standard build-ID, hvis du vil. Når du forbruger anbefalinger, hvis du ikke angiver det build-ID, du vil bruge, bruges standard en automatisk.

Denne funktion gør det muligt for dig – når du har en anbefaling model i fremstilling – til at oprette nye modeller og teste dem, før du opgraderer til fremstilling.

| HTTP-metode | URI |
|:--------|:--------|
|LÆG     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| id | Entydigt id for modellen (store og små bogstaver) |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Bemærk, at XML-koderne, beskrivelse og ActiveBuildId er valgfrit. Hvis du ikke vil angive beskrivelse eller ActiveBuildId, kan du fjerne hele mærket. |

**Svar**:

HTTP-statuskode: 200

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Juridiske
Dette dokument leveres "som-er". Du kan finde oplysninger og visninger, der er angivet i dette dokument, herunder URL-Adresser og andre referencer til websted, kan ændres uden varsel. Nogle eksempler, der er anvendt i eksempler findes kun til illustration og er opdigtet. Ingen reelle tilknytning eller forbindelse er beregnet eller begivenheder. Dette dokument giver dig en hvilken som helst juridiske rettigheder til intellektuel ejendom i Microsoft-produkter. Du kan kopiere og bruge dette dokument til interne referenceformål. © 2014 Microsoft. Microsoft Corporation. 
 
