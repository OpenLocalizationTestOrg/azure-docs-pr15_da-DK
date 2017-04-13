<properties 
    pageTitle="Machine Learning anbefalinger API dokumentation | Microsoft Azure" 
    description="Azure Machine Learning anbefalinger API dokumentation for et anbefalinger program tilgængelig i Microsoft Azure Marketplace." 
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
    ms.author="LuisCa"/>

#<a name="azure-machine-learning-recommendations-api-documentation"></a>Azure Machine Learning anbefalinger API dokumentation

>[AZURE.NOTE]Du skal begynde at bruge tjenesten anbefalinger API Kognitiv i stedet for denne version. Tjenesten anbefalinger Kognitiv vil erstatte denne tjeneste, og alle de nye funktioner udvikles der. Der er nye funktioner som "samling" support, en bedre API Explorer, en renere API grundflade, mere ensartet tilmelding/fakturering oplevelse, osv.
> Lær mere om [overførsel til den nye Kognitiv tjeneste](http://aka.ms/recomigrate)

Dette dokument vises Microsoft Azure Machine Learning anbefalinger API'er.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. generel oversigt
Dette dokument er en API reference. Du skal starte med dokumentet "Azure Machine Learning anbefaling – Hurtig Start".

Azure Machine Learning anbefalinger API kan opdeles i følgende logiske grupper:

- <ins>Begrænsninger</ins> - anbefalinger API begrænsninger.
- <ins>Generelle oplysninger</ins> - oplysninger om godkendelse, URI-tjenesten og versionsstyring.
- <ins>Grundlæggende model</ins> - API'er, der gør det muligt at udføre grundlæggende handlinger på model (fx oprette, opdatere og slette en model).
- <ins>Model avancerede</ins> - API'er, der gør det muligt at få avancerede dataindsigt for modellen.
- <ins>Model forretningsregler</ins> - API'er, der gør det muligt at administrere forretningsregler på model anbefaling resultater.
- <ins>Katalog</ins> - API'er, der gør det muligt at udføre grundlæggende handlinger på et model-katalog. Et katalog indeholder metadataoplysninger om brugen dataelementer.
- <ins>Funktionen</ins> - API'er, der giver mulighed for at få indsigt på element i kataloget, og hvordan du kan bruge disse oplysninger til at oprette bedre anbefalinger.
- <ins>Data om brug af</ins> -API'er, der gør det muligt at udføre grundlæggende handlinger på brugen modeldataene. Data om brug af i formen grundlæggende består af rækker, der omfatter par af & #60; bruger-id & #62; & #60; element-id & #62;.
- <ins>Opbygge</ins> - API'er, der gør det muligt at udløse en model build og udføre grundlæggende handlinger, der er relateret til dette build. Du kan udløse en model build, når du har værdifulde Brugsdata.
- <ins>Anbefaling</ins> - API'er, der gør det muligt at bruge anbefalinger, når den nyeste version til en model slutter.
- <ins>Brugerdata</ins> - API'er, der gør det muligt at hente oplysninger på brugen brugerdata.
- <ins>Meddelelser om</ins> - API'er, der gør det muligt at modtage beskeder om problemer, der er relateret til dine API-handlinger. (For eksempel du rapporterer Brugsdata via dataindsamling og de fleste af de hændelser, der behandler mislykkes. En meddelelse om fejl, der hæves.)

##<a name="2-limitations"></a>2. begrænsninger for

- Det maksimale antal modeller per abonnement er 10.
- Det maksimale antal builds per model, er 20.
- Det maksimale antal elementer, der kan indeholde et katalog er 100.000.
- Det maksimale antal brugen punkter, gemmes er ~ 5,000,000. Den ældste slettes om nye skal overføres eller rapporteret.
- Den maksimale størrelse af data, der kan sendes i INDLÆG (fx Importér katalogdata, importere Brugsdata) er 200MB.
- Det maksimale antal elementer, der kan blive bedt om, når du modtager anbefalinger er 150.

##<a name="3-apis---general-information"></a>3. API'er - generelle oplysninger

###<a name="31-authentication"></a>3.1. Godkendelse
Følg Microsoft Azure Marketplace retningslinjer for godkendelse. På marketplace understøtter enten Basic eller OAuth godkendelsesmetode.

###<a name="32-service-uri"></a>3.2. Tjenesten URI
Tjenesten roden URI til Azure Machine Learning anbefalinger API'er er [her.](https://api.datamarket.azure.com/amla/recommendations/v3/)

Tjenesten fuld URI udtrykkes ved hjælp af elementer i OData-specifikationen.  

###<a name="33-api-version"></a>3.3. API-version
Hver API-kald får til sidst en forespørgselsparameter, kaldet apiVersion, skal du vælge 1.0.

###<a name="34-ids-are-case-sensitive"></a>3.4. Der skelnes mellem id'er
Id'er, der returneres af en af API'erne, der skelnes mellem og skal bruges som f.eks, når de overføres som parametre i efterfølgende API-opkald. For eksempel er model id'er og katalog-id'er store og små bogstaver.

##<a name="4-recommendations-quality-and-cold-items"></a>4. anbefalinger kvalitet og koldtvandssystemer elementer

###<a name="41-recommendation-quality"></a>4.1. Anbefaling kvalitet

Oprette en anbefaling model er som regel nok til at tillade system til at levere anbefalinger. Dog varierer anbefaling kvalitet afhængigt af hvordan du kan bruge behandles og beskrivelser af kataloget. Eksempelvis hvis du har en masse kolde elementer (elementer uden betydeligt brugen), har systemet problemer give en anbefaling for sådanne et element eller bruger disse et element som en anbefalede. For at løse problemet kolde element, tillader systemet brugen af metadata elementer til at forbedre anbefalingerne. Disse metadata kaldes funktioner. Typisk funktioner er en bog forfatter eller en film Agent. Funktioner, der leveres via kataloget i form af nøgleværdi/strenge. Fuld formatet af katalogfilen, du kan finde den [importere katalog sektion](#81-import-catalog-data). 

###<a name="42-rank-build"></a>4.2. Plads build

Funktioner kan forbedre anbefaling modellen, men hvis du vil gøre dette kræver brug af funktioner, der giver mening. Hertil blev introduceret et nyt build - opbygge en rang. Dette build skal placere mailfunktionen mere nyttig af funktioner. En beskrivende funktion ikke er en funktion med et plads resultat af 2 og op.
Udløse en anbefaling build med liste (eller underliste) over de funktioner, der giver mening, efter at forstå, hvilke af funktionerne, der giver mening. Det er muligt at bruge disse funktioner til forbedring af både varm elementer og koldtvandssystemer elementer. For at bruge dem for varme elementer på `UseFeatureInModel` build parameter skal være konfigureret. For at bruge funktioner for kolde elementer på `AllowColdItemPlacement` build parameter skal være aktiveret.
Bemærk: Det er ikke muligt at aktivere `AllowColdItemPlacement` uden at aktivere `UseFeatureInModel`.

###<a name="43-recommendation-reasoning"></a>4.3. Anbefaling Kognitiv

Anbefaling Kognitiv er en anden aspekter af brugen af funktionen. Faktisk kan Azure Machine Learning anbefalinger program bruge funktioner til at levere anbefaling forklaringer (kaldes også reasoning), hvilket medfører flere tilliden anbefalede elementet fra anbefaling forbrugeren.
Aktivere Kognitiv, den `AllowFeatureCorrelation` og `ReasoningFeatureList` parametre skal være konfiguration før du anmoder om et anbefaling build.


##<a name="5-model-basic"></a>5. model Basic

###<a name="51-create-model"></a>5.1. Oprette Model
Opretter en "Opret model" anmodning.

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

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###<a name="52-get-model"></a>5.2. Få Model
Opretter en anmodning om "get-model".

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   id  |   Entydigt id for modellen (store og små bogstaver) |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Modeldataene kan findes under følgende elementer:

- `feed/entry/content/properties/Id`-Model, entydigt ID.
- `feed/entry/content/properties/Name`-Navn på model.
- `feed/entry/content/properties/Date`-Modeller oprettelsesdato.
- `feed/entry/content/properties/Status`-Status for modellen. Et af følgende:
    - Oprettet - modellen er blevet oprettet og indeholder ikke-katalog for og brugen.
    - ReadyForBuild - modellen er blevet oprettet og indeholder katalog for og brugen.
- `feed/entry/content/properties/HasActiveBuild`-Angiver, hvis modellen blev oprettet.
- `feed/entry/content/properties/BuildId`-Model aktive build-ID.
- `feed/entry/content/properties/Mpr`-Model middelværdien fraktil rangeringsmodel (MPR - yderligere oplysninger finder du ModelInsight).
- `feed/entry/content/properties/UserName`-Model interne brugernavn.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get A List Of All Models</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T14:35:51Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
      </entry>
    </feed>

###<a name="53-get-all-models"></a>5.3. Få alle modeller
Henter alle modeller for den aktuelle bruger.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

- `feed/entry/content/properties/Id`-Model, entydigt ID.
- `feed/entry/content/properties/Name`-Navn på model.
- `feed/entry/content/properties/Date`-Modeller oprettelsesdato.
- `feed/entry/content/properties/Status`-Status for modellen. Et af følgende:
  - Oprettet - modellen er blevet oprettet og indeholder ikke-katalog for og brugen.
  - ReadyForBuild - modellen er blevet oprettet og indeholder katalog for og brugen.
- `feed/entry/content/properties/HasActiveBuild`-Angiver, hvis modellen blev oprettet.
- `feed/entry/content/properties/BuildId`-Model aktive build-ID.
- `feed/entry/content/properties/Mpr`-Model MPR (se ModelInsight kan finde flere oplysninger).
- `feed/entry/content/properties/UserName`-Model interne brugernavn.
- `feed/entry/content/properties/UsageFileNames`-Liste over modelfiler brugen adskilt af komma.
- `feed/entry/content/properties/CatalogId`-Model katalog-ID.
- `feed/entry/content/properties/Description`-Beskrivelse af modellen.
- `feed/entry/content/properties/CatalogFileName`-Model katalog filnavnet.

XML-OData


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get A List Of All Models</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-28T14:35:51Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
                    <d:Name m:type="Edm.String">vah-11111</d:Name>
                    <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
                    <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
                    <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
                    <d:BuildId m:type="Edm.String">-1</d:BuildId>
                    <d:Mpr m:type="Edm.String">0</d:Mpr>
                    <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
                    <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
                    <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
                    <d:Description m:type="Edm.String">short description</d:Description>
                    <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
                </m:properties>
            </content>
        </entry>
    </feed>

###<a name="54-update-model"></a>5.4. Opdatere Model

Du kan opdatere beskrivelsen af model eller det aktive build-ID.<br>
<ins>Aktive build-ID</ins> - hver build for hver model har en build-ID. Det aktive build-ID er den første vellykkede build af hver ny model. Når du har et aktivt build-ID, og du gøre yderligere opbygger for den samme model, du skal eksplicit angive den som standard build-ID, hvis du vil. Når du forbruger anbefalinger, hvis du ikke angiver det build-ID, du vil bruge, bruges standard en automatisk.<br>
Denne funktion gør det muligt for dig – når du har en anbefaling model i fremstilling – til at oprette nye modeller og teste dem, før du opgraderer til fremstilling.


| HTTP-metode | URI |
|:--------|:--------|
|LÆG     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   id      | Entydigt id for modellen (store og små bogstaver)  |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>Bemærk, at XML-koderne, beskrivelse og ActiveBuildId er valgfrit. Hvis du ikke vil angive beskrivelse eller ActiveBuildId, kan du fjerne hele mærket.|

**Svar**:

HTTP-statuskode: 200

###<a name="55-delete-model"></a>5.5. Slette Model
Sletter en eksisterende model efter-ID.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   id  |   Entydigt id for modellen (store og små bogstaver) |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Delete Model by Id</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T10:39:33Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
      </entry>
    </feed>

##<a name="6-model-advanced"></a>6. avancerede model

###<a name="61-model-data-insight"></a>6.1. Model Data indsigt
Returnerer statiske data på brugerdata, som denne model blev oprettet med.

Kun tilgængelig for anbefaling build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Der returneres dataene som en samling af egenskaber.

- `feed/entry/id/content/properties/key`-Indeholder navnet.
- `feed/entry/id/content/properties/value`-Indeholder egenskabsværdien.

Tabellen nedenfor viser den værdi, der repræsenterer hver nøgle.

|Nøgle|Beskrivelse|
|:-----|:----|
| AvgItemLength | Gennemsnitlige antal forskellige brugere per element. |
| AvgUserLength | Gennemsnitlige antal forskellige elementer på hver bruger. |
| DensificationNumberOfItems | Antallet af elementer efter fjernelse elementer, der kan være bygger. |
| DensificationNumberOfUsers | Antallet af brugen point efter fjernelse brugere og elementer, der kan være bygger. |
| DensificationNumberOfRecords | Antallet af brugen point efter fjernelse brugere og elementer, der kan være bygger. |
| MaxItemLength | Antallet af forskellige brugere til det mest populære element. |
| MaxUserLength | Maksimale antal forskellige elementer til en bruger. |
| MinItemLength | Maksimale antal entydige brugere for et element. |
| MinUserLength | Minimale antallet af forskellige elementer til en bruger. |
| RawNumberOfItems | Antallet af elementer på listen brugen filer. |
| RawNumberOfUsers | Antal brugen punkter før en hvilken som helst fjernelse. |
| RawNumberOfRecords | Antal brugen punkter før en hvilken som helst fjernelse. |
| SamplingNumberOfItems | I/T. |
| SamplingNumberOfRecords | I/T. |
| SamplingNumberOfUsers | I/T. |

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get data insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
        <m:properties>
            <d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
            <d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxItemLength</d:Key>
                <d:Value m:type="Edm.String">4,704</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxUserLength</d:Key>
                <d:Value m:type="Edm.String">190</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinItemLength</d:Key>
                <d:Value m:type="Edm.String">8</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinUserLength</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="62-model-insight"></a>6.2. Model indsigt
Returnerer model indsigt i det aktive build eller (Hvis angivet) på et bestemt build.

Kun tilgængelig for anbefaling build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |Med aktive build-ID:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Med bestemte build-ID:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   buildId |   Valgfri - nummer, der identificerer et vellykket build. |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Der returneres dataene som en samling af egenskaber.

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


Tabellen nedenfor viser den værdi, der repræsenterer hver nøgle.

| Nøgle | Beskrivelse |
|:---- |:----|
| CatalogCoverage | Hvilken del af kataloget kan bygger med brugsmønstre. Resten af elementerne skal indhold-baserede funktioner. |
| MPR | Middelværdien fraktil rangeringen af modellen. Nederste er bedre. |
| NumberOfDimensions | Antallet af dimensioner, der bruges af algoritmen matrix factorization. |


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get model insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">CatalogCoverage</d:Key>
                <d:Value m:type="Edm.String">1.000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">Mpr</d:Key>
                <d:Value m:type="Edm.String">0.367</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="63-get-model-sample"></a>6.3. Få Model eksempel
Henter en stikprøve af anbefaling modellen.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>Med bestemte build-ID:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

XML-OData

Svar returneres i rå tekstformat:

<pre>
Niveau 1 – 655fc 955-a5a3-4a26-9723-3090859cb27b, bytte: en nye 655fc 955-a5a3-4a26-9723-3090859cb27b, bytte: en ny klassificering: 0.5215 3f471802-f84f-44a0 - 99c 8-6d2e7418eec1, sort Hawk ned: en tekstenhed af moderne krig bedømmelse: 0.5151 07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon bedømmelse: 0.5148 6afc18e4-8c2a - 43d 1-9021-57543d6b11d8, Imajica bedømmelse: 0.5146 e4cc5e69-3567-43ab-b00f-f0d8d0506870, der påløber listen bedømmelse: 0.514 56b61441-0eed-46cc-a8f6-112775b81892, liv og dør i Shanghai 56b61441-0eed-46cc-a8f6-112775b81892, liv og dør i Shanghai bedømmelse: 0.5218 53156702-cc0c-443d-b718-6fb74b2491d3, søn af \ vurdering af ansøger: 0.5212 fb8cf7a6-8719-46ee - 97d 4-92f931d77a3a, røg og spejle: kort Fictions og illustrationer vurdering af ansøger : 0.5188 8f5fe006-79e4-4679-816b-950989d1db4b, A sted jeg har aldrig været (moderne American fakta) bedømmelse: 0.5156 d8db4583-cc0f-49ce-bc95-b7fa3491623f, lykke: en ny klassificering: 0.5156 50471eec-9aeb-4900-84 d 7-21567ab18546, hvis Buddha dateret: en håndbøger til at finde Kærlighed på en religiøse stien cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine hemmeligheder om den Ya-Ya Sisterhood: en ny klassificering: 0.5266 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Poisonwood Bible: en ny klassificering: 4dd0d7dc3a19-0.5252 973f8cbd-0846-4f6b - 9d 28, svin i Heaven bedømmelse: 0.5244 e2cbf7ad-0636-4117-8b30-298da6df7077, dyr må vurdering af ansøger : 0.5227 6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions af en grimme Stepsister: en ny klassificering: 0.5222 5e97148f-defb - 4d 74-af2d-80f4763bf531, Deep slutningen af Hav (Oprahs adressekartotek klubs) 5e97148f-defb - 4d 74-af2d-80f4763bf531, Deep slutningen af Hav (Oprahs adressekartotek klubs) bedømmelse: 0.537 5dcbac37-2946-4f2a-a0b3-bbe710f9409a, op ø: en ny klassificering: 0.5277 bc5b69db-733b-4346-adde-3927544258f7, Downtown bedømmelse: 0.5275 31fe5c63-3e5a - 48d 0-802b-d3b0f989a634, har en god dag: en fortælling med overvågning og Sweatsocks bedømmelse: 0.5252 0adf981a-b65b - 4c 11-b36b-78aca2f948a2, det perfekte Storm : En SAND tekstenhed af mænd mod Hav bedømmelse: 0.5238 68f97068-ae1a-4163-9e94-396b800b743d, Modoc: SAND historien om den største Legetøjselefant, der nogensinde leve 68f97068-ae1a-4163-9e94-396b800b743d, Modoc: SAND historien om den største Legetøjselefant, der nogensinde leve bedømmelse: 0.5379 6724862e-e4e7-4022-9614-1468d8b902ff, lille House på Prairie bedømmelse: 0.5345 cdedb837-1620-496d - 94c 4-6ccfed888320, lille hus i den store skov bedømmelse: 0.5325 382164ba-406b-4187-b726-d7a54b9d790d, tryk på Pooh-klassifikation: 0.5309 6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5 , På banker af spiritus fremstillet af blommer Creek bedømmelse: 0.5285 37ef8e74-e348-44e5-aabc-1d7f9efcb25b, mænd stammer fra Mars kvinder er fra & quot: en praktisk vejledning for at forbedre kommunikation og få det ønskede i din relationer 37ef8e74-e348-44e5-aabc-1d7f9efcb25b, mænd er fra Mars, kvinder fra & quot: en praktisk vejledning for at forbedre kommunikation og få det ønskede i din vurdering af ansøger relationer: 0.5397 f2be16d4-5faf - 4d 32-ab83-7ba74d29261e, politisk korrekt Bedtime historier : Moderne historier til vores liv og det klokkeslæt bedømmelse: 0.5207 ef732c5c-334b-4d6b-ab82-7255eb7286d0, ære mellem tyve vurdering: 0.5195 0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, hvor træ klassifikation: 0.5194 883b360f-8b42-407f-b977-2f44ad840877, uhyggelige tekstenheder til Fortæl i mørke: der indsamles fra American folkloristiske (uhyggelige tekstenheder) bedømmelse: 0.5184 ff51b67e-fa8e-4c5e-8f4d-02a928de735d, mænd på arbejdet: håndværk af fodbold d008dae9-c73a-40a1-9a9b-96d5cf546f36, Gulag toscano 1918 1956: forsøg i litterære undersøgelse jeg-II bedømmelse: 0.5416 ff51b67e-fa8e-4c5e-8f4d-02a928de735d, mænd på arbejde : Håndværk fodbold bedømmelse: 0.5403 49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland bedømmelse: 0.5394 cc7964fd-d30f-478e-a425-93ddbdf094ed, magisk indsamling: scene Vol. 1 bedømmelse: 0.5379 8a1e9f36-97af-4614-bed9-24e3940a05f3, flere Sniglets: et ord, der ikke vises på ordbogen, men skal bedømmelse: 0.5377 12a6d988-be21-4a09-8143-9d5f4261ba16, en drøm Eagles 07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon bedømmelse: 0.5417 e4cc5e69-3567-43ab-b00f-f0d8d0506870, der påløber listen bedømmelse: 0.5416 1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, Family bedømmelse: 0.5371 56daeffe - 7d 48-43cd-8ef8-7dffd0c103d3, systemenhed klasse bedømmelse: 0.5366 b2fe511e-5cb9-4a56-b823-2801e63e6a96, juridiske bud bedømmelse : 0.5366 df87525b-e435-4bd6-8701-4e60ad344e28, finde fiske 56d 33036-dfda-46b9-8e2a-76cb03921bb0, X-filer: bunden nul bedømmelse: 0.5417 0780cde8-6529-4e1d-b6c6-082c1b80e596, tolv rød Herrings vurdering af ansøger: 0.5416 df87525b-e435-4bd6-8701-4e60ad344e28, finde fisken vurdering af ansøger: 0.5408 400fe331 - 2c 35-490c-adbc-b28b4b73d56c, skal vi se på vegne? Klassifikation: 0.5383 f86ad7d0 - 5c 03-42b3-aebf-13d44aec8b30, toner af den udvidede bedømmelse: 0.5358 de1f62a4-89e6 - 44d 2-aaee-992a4bf093f1, kortet, ændret verden: William Sørensen og meddelelse af moderne geologi de1f62a4-89e6 - 44d 2-aaee-992a4bf093f1, kortet, ændret verden: William Sørensen og meddelelse af moderne geologi bedømmelse: 0.5422 b303538f-e2c6-4a2c-b425-8d21e684fc3e, min onkel Oswald vurdering af ansøger: 0.5385 34b84627-48af-4a4c - 96c 4-b26fb3863f56, midnat i have godt og ondskabsfulde bedømmelse: 0.5379 306cbaa7-b1a8-4142-9 d 55-e11b5018a7a8, den Gade advokat vurdering af ansøger : 0.5376 e53b4baa - 8c 09-45c 4-95c 0-b6a26b98770b, Jeg savner Smillas følelse for sne vurdering af ansøger: 0.5367

<a name="level-2"></a>Niveau 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, feltet skumle gris (Hillerman Tony) 352aaea1-6b12-454d-a3d5-46379d9e4eb2, den skumle gris (Hillerman Tony) vurdering af ansøger: 0.5425 74c 49398-bc10-4af5-a658-a996a1201254, børn af Storm (Peters Elizabeth) vurdering af ansøger: 0.5387 9ba80080-196e-43fd-8025-391d963f77e7, feltet flydende pige vurdering af ansøger: 0.5372 e68f81d5-7745-4cc7-b943-fedb8fcc2ced, halsbrækkende smiley (Scottoline Lisa) bedømmelse: 0.5353 b2fe511e-5cb9-4a56-b823-2801e63e6a96, vurdering af ansøger juridiske bud: 0.5332 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, sø Wobegon dage 0adf981a-b65b - 4c 11-b36b-78aca2f948a2, feltet perfekte Storm: A SAND historie af mænd mod feltet Hav vurdering af ansøger: 0.5433 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, sø Wobegon dage vurdering af ansøger : 0.543 a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: et ord, der ikke vises på ordbogen, men skal) vurdering af ansøger: 0.5327 6f6e192e - 0d 64-49ca-9b63-f09413ea1ee6, politisk korrekt Feriekalender historier: For en Enlightened Glædelig Season bedømmelse: 0.5307 798051a8 - 147d - 4d 46-b0dc-e836325029e6, ALDEREN på INNOCENCE (film Tilslut) bedømmelse: 0.5301 73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Pingvin af det tyvende århundrede klassikerne) cba8163f-6536-436b-8130-47b4a43c827f, sikkerhed og rettighedsadministration ingen (den officielle Guide til X-filer Vol. 2) bedømmelse: 0.5434 5708e4cb-2492-49 c 0-94a8-cc413eec5d89, lille guders (Discworld romaner (Paperback)) klassifikation: 0.5406 73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Pingvin af det tyvende århundrede klassikerne) Klassifikation: 0.5403 d885b0bd-ae4b-452d-bdf2-faa90197dbc9, farven på magiske bedømmelse: 0.539 b133a9c4-4784-4db3-b100-d0d6dffb94d2, sandheden er ud af, der (den officielle Guide til X-filer Vol. 1) bedømmelse: 0.5367 271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: eller jeg, hvorfor Winged Whale Sings 271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: eller jeg, hvorfor Winged Whale Sings bedømmelse: 0.5445 2de1c354-90ff - 47c 5-a0db-1bad7d88ef94, den Salaryman kone (børn af vold serie) bedømmelse: 0.5329 d279416e - 19c 0-43f8-9ec9-a585947879ca, Zen optræder bedømmelse : 0.5316 c8f854d7-3de3-4b23-8217-f4f851670fd4, hævn af Cootie piger: en Robin Hudson Mystery (Robin Hudson mysterierne (Paperback)) klassifikation: 0.5305 8ef4751c-7074-409e-a3ac-d49b222fc864, hvor vurdering af de vilde ting: 0.5289 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, deres Eyes ser majeure 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, deres Eyes ser majeure bedømmelse: 0.5446 da45c4d5-aba1-413b-a9bd-50df98b1e1d2, som træer vurdering af ansøger: 0.5389 65ecbdd1 - 131c - 40c 3-a3d6-d86ca281377a, majeure Small ting vurdering af ansøger: 0.5387 c78743bf-7947-4a0c-8db7-8a3bfe69ba70, de brik dagbøger vurdering af ansøger: 4dd0d7dc3a19-0.5355 973f8cbd-0846-4f6b - 9d 28, svin i Heaven bedømmelse : 0.5344 5f17d90a-2604-4fe8-8977-1a280b9098b1, en for de penge (Stephanie spiritus fremstillet af blommer romaner (Paperback)) 5f17d90a-2604-4fe8-8977-1a280b9098b1, én for penge (Stephanie spiritus fremstillet af blommer romaner (Paperback)) bedømmelse: 0.5446 57169b2b-9a8a-486b-9aac-1ed98ce57168, færdig henvende bedømmelse: 0.5332 efcb1bc4-7278-4a8f-b491-befde02070d6, øjeblik af sandheden bedømmelse: 0.5329 1efa91a2-993b - 4c 43-9f5c-3454fc12612d, brænde faktor bedømmelse: 0.5309 24c 59962-458a-4ec8-b95d-d694e861919c, derhjemme i Mitford (Mitford år) vurdering af ansøger: 0.5303 4fd48c46-1a20 - 4c 57-bc7f-a02ef123dc52, som art foretaget ham: dreng der blev opløftet som en pige 4fd48c46-1a20-4c57-bc7f-a02ef123dc52 , Som art foretaget ham: den dreng der blev opløftet som et pige vurdering af ansøger: 0.5449 cd5f2c03-20cb-43be-a1fb-3b4233e63222, svin i vurdering af Heaven: 0.5329 19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Kærlighed i tid af Cholera (pingvin fremragende bøger af det tyvende århundrede) bedømmelse: 0.5267 15689d 09-c711-4844-84 d 8-130a90237b26, vurdering af ansøger Bel Canto: 0.5245 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, feltet Poisonwood Bible: A nye vurdering: 0.5235 98df28ec-41e7-4fca-b77f-8b0d3109085d, stjerne Trek minder f874b5a3 - 5d 40-4436-94ff-0fa1c090ddf5, feltet søn også stiger (A Scribner klassisk) vurdering af ansøger : 0.5451 98df28ec-41e7-4fca-b77f-8b0d3109085d, stjerne Trek minder vurdering af ansøger: 0.5442 0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Usynlige bedømmelse: 0.5421 15316ca6-1e38-425f-893d-691944a47000, mere uhyggelige tekstenheder til Fortæl i mørke bedømmelse: 0.5409 329d 5682-3dc3-4206-8aa2-eef4b1032258, bogstaver fra Earth bedømmelse: 6f669bb1b0a9-: 0,54 5b9445d5-c072 - 419c - 8d 49, datter af Fortune: en nye (Oprahs adressekartotek klubs (Hardcover)) 5b9445d5-c072 - 419c - 8d 49-6f669bb1b0a9, datter af Fortune: en nye (Oprahs adressekartotek klubs (Hardcover)) bedømmelse: 0.5462 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Poisonwood Bible: en nye bedømmelse: 0.5372 604eb3bd-6026-4f51-bffd-9fb54f180400, Family billeder: en nye bedømmelse: 0.5341 8d06d01d-31cd-4678-b6b1-140a67987ce9, sange i almindelige tid (Oprahs adressekartotek klubs (Paperback)) bedømmelse : 0.5334 da45c4d5-aba1-413b-a9bd-50df98b1e1d2, som træer bedømmelse: 0.5319 d5358189-d70f-4e35-8add-34b83b4942b3, svin i Heaven d5358189-d70f-4e35-8add-34b83b4942b3, svin i Heaven bedømmelse: 0.5491 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, Poisonwood Bible: en ny klassificering: 0.5401 c78743bf-7947-4a0c-8db7-8a3bfe69ba70, de brik dagbøger vurdering af ansøger: 0.5393 8d06d01d-31cd-4678-b6b1-140a67987ce9, sange i almindelige tid (Oprahs adressekartotek klubs (Paperback)) bedømmelse: 4dd0d7dc3a19-0.5382 973f8cbd-0846-4f6b - 9d 28, svin i Heaven bedømmelse: 0.5367

</pre>


##<a name="7-model-business-rules"></a>7. model forretningsregler

Dette er typerne af regler, der understøttes:
- <strong>Blokeringsliste</strong> – blokeringsliste gør det muligt at angive en liste over elementer, du ikke vil returnere i anbefaling resultaterne. 

- <strong>FeatureBlockList</strong> - funktion blokeringsliste gør det muligt at blokere elementer, der er baseret på værdierne i de tilgængelige funktioner.

*Send ikke mere end 1000 elementer i en enkelt blokeringsliste regel eller dit opkald kan timeout. Hvis du vil blokere mere end 1000 elementer, kan du foretage flere blokeringsliste opkald.*

- <strong>Upsale</strong> - Upsale gør det muligt at gennemtvinge elementer, der skal returneres i anbefaling resultaterne.

- <strong>WhiteList</strong> - hvid liste kan du foreslå kun anbefalinger fra en liste over elementer.

- <strong>FeatureWhiteList</strong> - liste over funktioner hvid gør muligt at anbefaler kun elementer, der har bestemt funktion værdier.

- <strong>PerSeedBlockList</strong> - Per frø blokeringsliste gør det muligt at angive hver vare en liste over elementer, der ikke kan returneres som anbefaling resultater.




###<a name="71-get-model-rules"></a>7.1. Få Model regler

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>Eksempel:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

- `feed/entry/content/properties/Id`-Entydigt id for denne regel.
- `feed/entry/content/properties/Type`-Typen på reglen.
- `feed/entry/content/properties/Parameter`-Parameter for regel.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get a list of rules for a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T12:58:57Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000043</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000044</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="72-add-rule"></a>7.2. Tilføj regel

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|SIDEHOVED   |`"Content-Type", "text/xml"`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | 
<ins>Når give element-id'er for forretningsregler, Sørg for at bruge eksterne id'et for elementet (det samme Id, du har brugt i katalogfilen)</ins><br>
<ins>Sådan tilføjer du en blokeringsliste regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>
<ins>Sådan tilføjer du en FeatureBlockList regel:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>Sådan tilføjer du en regel for Upsale:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br>
<ins>Sådan tilføjer du en WhiteList regel:</ins><br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>Sådan tilføjer du en FeatureWhiteList regel:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>Sådan tilføjer du en PerSeedBlockList regel:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|


**Svar**:

HTTP-statuskode: 200

API returnerer nyoprettede reglen med dens detaljer. Egenskaben regler kan hentes fra følgende stier:

- `feed/entry/content/properties/Id`-Entydigt id for denne regel.
- `feed/entry/content/properties/Type`-Typen på reglen: blokeringsliste eller Upsale.
- `feed/entry/content/properties/Parameter`-Parameter for regel.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add A Rule</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T11:13:28Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AddARuleEntity</title>
        <updated>2014-11-05T11:13:28Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000041</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="73-delete-rule"></a>7.3. Slette regel

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   filterId    |   Entydigt id for filteret |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

###<a name="74-delete-all-rules"></a>7.4. Slette alle regler

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

##<a name="8-catalog"></a>8. katalog

###<a name="81-import-catalog-data"></a>8.1. Importér katalogdata

Hvis du overføre flere katalogfiler til den samme modellen med flere opkald, indsættes vi kun de nye katalogelementer. Eksisterende elementer, forbliver med de oprindelige værdier. Du kan ikke opdatere katalogdata ved hjælp af denne metode.

Datatypen katalog skal benytte følgende format:

- Uden funktioner-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Med funktioner-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

Bemærk: Den maksimale filstørrelse er 200MB.

**Formatér detaljer**

| Navn | Obligatorisk | Type |  Beskrivelse |
|:---|:---|:---|:---|
| Element-Id |Ja | [A-å], [a-z], [0-9], [_] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 50 | Entydigt id for et element. |
| Elementnavn | Ja | Alfanumeriske tegn<br> Maksimal længde: 255 | Elementnavn. | 
| Element kategori | Ja | Alfanumeriske tegn <br> Maksimal længde: 255 | Kategori, som dette element tilhører (fx madlavning bøger, Drama...); kan være tom. |
| Beskrivelse | Nej, medmindre funktioner er præsentere (men kan være tomme) | Alfanumeriske tegn <br> Maksimal længde: 4000 | Beskrivelse af elementet. |
| Funktionslisten | Nej | Alfanumeriske tegn <br> Maksimal længde: 4000; Maksimalt antal funktioner: 20 | Kommasepareret liste over funktionsnavn = funktion værdi, der kan bruges til at forbedre model anbefaling; Se [Avancerede emner](#2-advanced-topics) afsnit. |


| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|SIDEHOVED   |`"Content-Type", "text/xml"`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen  |
| filnavn | Tekstbaseret id for kataloget.<br>Kun bogstaver (A-Å, a-å), tal (0-9), bindestreger (--) og understregningstegnet (_) er tilladt.<br>Maksimal længde: 50 |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | Eksempel (med funktioner):<br/>2406e770-769c-4189-89de-1c9283f93a96, Clara Callan, adressekartotek, beskrivelsen adressekartotek forfatternavn = Richard Wright publisher = Harper Flamingo Canada, år = 2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, feltet glemme rum: A fakta (Byzantium mappe), adressekartotek, forfatternavn = Nick Bantock publisher = Harpercollins, år = 1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework, adressekartotek, forfatternavn = Timothy Findley publisher = HarperFlamingo Canada, år = 2001<br>552a1940-21e4-4399-82bb-594b46d7ed54, begrænsning af dyr, adressekartotek, beskrivelsen adressekartotek forfatternavn = Magnus Mills publisher = Arkade publicering år = 1998</pre> |


**Svar**:

HTTP-statuskode: 200

API returnerer en rapport over importen.
- `feed\entry\content\properties\LineCount`-Antal linjer, der er accepteret.
- `feed\entry\content\properties\ErrorCount`-Antal linjer, der ikke blev indsat på grund af en fejl.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Import catalog file</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ImportCatalogFileEntity2</title>
        <updated>2014-10-05T06:58:04Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:LineCount m:type="Edm.String">4</d:LineCount>
                <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="82-get-catalog"></a>8.2. Få katalog
Henter alle katalogelementer.
Kataloget bliver hentet én side ad gangen. Hvis du vil have vist elementer på et bestemt indeks, kan du bruge parameteren $skip odata. For eksempel hvis du vil hente elementer fra og med startposition 100, skal du tilføje parameteren $skip = 100 til anmodningen.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post hver vare i kataloget. Hvert element har følgende data:

- `feed/entry/content/properties/ExternalId`-Katalog vare eksterne-ID, den, der blev leveret af kunden.
- `feed/entry/content/properties/InternalId`-Kataloget interne element-ID, det, der har oprettet Azure Machine Learning anbefalinger.
- `feed/entry/content/properties/Name`-Elementnavn katalog.
- `feed/entry/content/properties/Category`-Katalog element kategori.
- `feed/entry/content/properties/Description`-Kataloget element beskrivelse.
- `feed/entry/content/properties/Metadata`-Kataloget elementmetadata.


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get All Catalog Items</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
                <d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
                <d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
                <d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
                <d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
                <d:Name m:type="Edm.String">Spadework</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
                <d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
                <d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="83-get-catalog-items-by-token"></a>8.3. Få Katalogelementer ved Token

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen |
|   token   |   Token af kataloget elementets navn. Skal indeholde mindst 3 tegn. |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post hver vare i kataloget. Hvert element har følgende data:

- `feed/entry/content/properties/InternalId`-Kataloget interne element-ID, det, der har oprettet Azure Machine Learning anbefalinger.
- `feed/entry/content/properties/Name`-Elementnavn katalog.
- `feed/entry/content/properties/Rating`-(til fremtidig brug)
- `feed/entry/content/properties/Reasoning`-(til fremtidig brug)
- `feed/entry/content/properties/Metadata`-(til fremtidig brug)
- `feed/entry/content/properties/FormattedRating`-(til fremtidig brug)

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get Catalog items that contain a token</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:48:19Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">CatalogItemsThatContainATokenEntity</title>
            <updated>2014-10-29T11:48:19Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                    <d:Name m:type="Edm.String">Clara Callan</d:Name>
                    <d:Rating m:type="Edm.Double">0</d:Rating>
                    <d:Reasoning m:type="Edm.String"></d:Reasoning>
                    <d:Metadata m:type="Edm.String"></d:Metadata>
                    <d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
                </m:properties>
            </content>
        </entry>
    </feed>

##<a name="9-usage-data"></a>9. data om brug af
###<a name="91-import-usage-data"></a>9.1. Importere Data om brug af
####<a name="911-uploading-file"></a>9.1.1. Overførsel af filer
Dette afsnit viser, hvordan du overføre Brugsdata ved hjælp af en fil. Du kan ringe til denne API flere gange med data om brug af. Alle brugerdata, gemmes for alle opkald.

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId |   Entydigt id for modellen  |
| filnavn | Tekstbaseret id for kataloget.<br>Kun bogstaver (A-Å, a-å), tal (0-9), bindestreger (--) og understregningstegnet (_) er tilladt.<br>Maksimal længde: 50 |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | Data om brug af. Format:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Navn</th><th>Obligatorisk</th><th>Type</th><th>Beskrivelse</th></tr><tr><td>Bruger-Id</td><td>Ja</td><td>[A-å], [a-z], [0-9], [_] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 255 </td><td>Entydigt id for en bruger.</td></tr><tr><td>Element-Id</td><td>Ja</td><td>[A-å], [a-z], [0-9], [& #95;] & #40; Understregningstegnet & #41; [-] & #40; tankestreg & #41;<br> Maksimal længde: 50</td><td>Entydigt id for et element.</td></tr><tr><td>Tid</td><td>Nej</td><td>Dato i formatet: åååå/MM/ååååTtt (fx 2013/06/20T10:00:00)</td><td>Tidspunkt for data.</td></tr><tr><td>Begivenhed</td><td>Nej; Hvis leveret før du skal også placerer dato</td><td>Et af følgende:<br>• Klik på<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Køb</td><td></td></tr></table><br>Maksimumfilstørrelse: 200MB<br><br>Eksempel:<br><pre>149452, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Svar**:

HTTP-statuskode: 200

- `Feed\entry\content\properties\LineCount`-Antal linjer, der er accepteret.
- `Feed\entry\content\properties\ErrorCount`-Antal linjer, der ikke blev indsat på grund af en fejl.
- `Feed\entry\content\properties\FileId`-Fil id.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="912-using-data-acquisition"></a>9.1.2. Brug af dataindsamling
Dette afsnit viser, hvordan du sende hændelser i realtid til Azure Machine Learning anbefalinger, som regel fra dit websted.

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|SIDEHOVED   |`"Content-Type", "text/xml"`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
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
                    <PurchaseItem>
                        <ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
                        <Count>1</Count>
                    </PurchaseItem>
                    <PurchaseItem>
                        <ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
                        <Count>3</Count>
                    </PurchaseItem>
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

###<a name="92-list-model-usage-files"></a>9.2. Listen modelfiler brugen
Henter metadataene i alle modelfiler brugen.
Brug filer bliver hentet én side ad gangen. Hver containes 100 sideelementer. Hvis du vil have vist elementer på et bestemt indeks, kan du bruge parameteren $skip odata. For eksempel hvis du vil hente elementer fra og med startposition 100, skal du tilføje parameteren $skip = 100 til anmodningen.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   forModelId  |   Entydigt id for modellen |
|   apiVersion      | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post per brugen fil. Hvert element har følgende data:

- `feed\entry\content\properties\Id`-Brugen fil-ID.
- `feed\entry\content\properties\Length`-Brugen fillængde i MB.
- `feed\entry\content\properties\DateModified`-Dato, hvor filen brugen blev oprettet.
- `feed\entry\content\properties\UseInModel`-Om filen brugen bruges i modellen.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of model's usage files info</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
            <updated>2014-10-30T09:40:25Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
                    <d:Length m:type="Edm.Double">0</d:Length>
                    <d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
                    <d:UseInModel m:type="Edm.String">true</d:UseInModel>
                </m:properties>
            </content>
        </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
                <d:Length m:type="Edm.Double">0.001</d:Length>
                <d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
                <d:UseInModel m:type="Edm.String">true</d:UseInModel>
            </m:properties>
        </content>
    </entry>
</feed>

###<a name="93-get-usage-statistics"></a>9.3. Få Brugsstatistik
Henter brugsstatistik.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| Startdato |   Startdato. Format: åååå/MM/ååååTtt |
| Slutdato | Slutdato. Format: åååå/MM/ååååTtt |
| eventTypes |  Kommasepareret streng med begivenhed typer eller null-værdier for at få alle begivenheder  |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

En samling af nøgleværdi/elementer. Hver af dem indeholder summen af hændelser for en bestemt hændelsestype, der er grupperet efter time.

- `feed\entry[i]\content\properties\Key`-Indeholder den tid (grupperet efter time) og hændelsestypen.
- `feed\entry[i]\content\properties\Value`– Det totale begivenhed antal.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-18T11:39:16Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
                <d:Value m:type="Edm.String">5</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="94-get-usage-file-sample"></a>9,4. Få brugen fil eksempel
Henter de første 2KB af brugen filens indhold.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| fileId |  Entydigt id for brugen modelfil  |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Svar returneres i rå tekstformat:
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15 , SAND, 1 235588, 21BF8088-B6C0-4509-870C-E1C7AC78304A, 2014 11/02T13:40:15, SAND, 1 158254, 21BF8088-B6C0-4509-870 C-E1C7AC78304A, 2014-11/02T13:40:15, SAND, 1 271195, 21BF8088-B6C0-4509-870 C-E1C7AC78304A, 2014 11/02T13:40:15, SAND, 1 141157, 21BF8088-B6C0-4509-870 C-E1C7AC78304A, 2014 11/02T13:40:15, SAND, 1 171118, 3BB5CB44-D143-4BDD-A55C-443964BF4B23, 2014-11/02T13:40:15, SAND, 1 225087, 3BB5CB44-D143-4BDD-A55C-443964BF4B23, 2014 11/02T13:40:15, SAND, 1
</pre>


###<a name="95-get-model-usage-file"></a>9.5. Få brugen modelfil
Henter hele indholdet af filen brugen.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| Mid | Entydigt id for modellen  |
| FID | Entydigt id for brugen modelfil |
| Download | 1 |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

Svar returneres i rå tekstformat:
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15 ,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15 ,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15 , SAND, 1 260965, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014-11/02T13:40:15, SAND, 1 102758, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014 11/02T13:40:15, SAND, 1 112602, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014 11/02T13:40:15, SAND, 1 163925, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014 11/02T13:40:15, SAND, 1 262998, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014 11/02T13:40:15, SAND, 1 144717, 552A1940-21E4-4399-82BB-594B46D7ED54, 2014-11/02T13:40:15, SAND, 1
</pre>

###<a name="96-delete-usage-file"></a>score på 9,6. Slette brugen fil
Sletter den angivne Brug modelfil.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| Parameternavn    |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| fileId | Entydigt id for den fil, der skal slettes |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200


###<a name="97-delete-all-usage-files"></a>9.7. Slet alle brugen filer
Sletter alle modelfiler brugen.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| Parameternavn    |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

##<a name="10-features"></a>10. funktioner
Dette afsnit viser, hvordan til at hente oplysninger om funktionen, som de importerede funktioner og deres værdier deres plads, og når denne rang blev tildelt. Funktioner, der er importeret som en del af datatypen katalog, og derefter deres plads er tilknyttede, når et plads build er færdig.
Funktionen plads kan ændre efter anvendelse af Brugsdata og de typer elementer. Men for ensartet brugen/elementer, rangen bør have kun små udsving.
Rangen for funktioner, der er en ikke-negative tal. Tallet 0 betyder, at funktionen ikke blev vurderet (sker der, hvis du kalder denne API før afslutningen af det første plads build). Den dato, hvor pladsen blev tilskrevet kaldes karakteren friskhed.

###<a name="101-get-features-info-for-last-rank-build"></a>10.1. Få oplysninger om funktioner (For sidste plads Build)
Henter disse oplysninger om funktionen, herunder rangeringsmodel, til det sidste vellykkede plads build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT      |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| Parameternavn    |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
|samplingSize| Antallet af værdier, der skal medtages for hver funktion efter dataene i kataloget. <br/>Mulige værdier er:<br> -1 - eksempler på alle. <br>0 - ingen udvalg. <br>N - Returner N eksempler for hvert funktionsnavn.|
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |


**Svar**:

HTTP-statuskode: 200

Svaret indeholder en oversigt over funktion oplysninger poster. Hvert element indeholder:

- `feed/entry/content/m:properties/d:Name`-Funktionsnavn.
- `feed/entry/content/m:properties/d:RankUpdateDate`-Dato, hvor pladsen der er allokeret til denne funktion kaldes også resultat friskhed funktion. En historisk dato ('0001-01-01T00:00:00') betyder, at ingen plads build blev udført.
- `feed/entry/content/m:properties/d:Rank`-Funktion rang (flydende). Placeringen af 2.0 og op betragtes som en god funktion.
- `feed/entry/content/m:properties/d:SampleValues`-Kommasepareret liste over værdier op til størrelsen stikprøver, hvor der anmodes om.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get the features of a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-01-08T13:15:02Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Author</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Publisher</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
        <contenttype="application/xml">
        <m:properties>
            <d:Name m:type="Edm.String">Year</d:Name>
            <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
            <d:Rank m:type="Edm.String">0</d:Rank>
            <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
        </m:properties>
        </content>
    </entry>
</feed>


###<a name="102-get-features-info-for-specific-rank-build"></a>10.2. Få oplysninger om funktioner (For bestemte plads Build)

Henter disse oplysninger om funktionen, herunder rangorden for et bestemt plads build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT      |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| Parameternavn    |   Gyldige værdier    |
|:--------          |:--------          |
| modelId | Entydigt id for modellen  |
|samplingSize| Antallet af værdier, der skal medtages for hver funktion efter dataene i kataloget.<br/> Mulige værdier er:<br> -1 - eksempler på alle. <br>0 - ingen udvalg. <br>N - Returner N eksempler for hvert funktionsnavn.|
|rankBuildId| Entydigt id for den nyeste version af plads eller -1 for det sidste plads build|
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |


**Svar**:

HTTP-statuskode: 200

Svaret indeholder en oversigt over funktion oplysninger poster. Hvert element indeholder:

- `feed/entry/content/m:properties/d:Name`-Funktionsnavn.
- `feed/entry/content/m:properties/d:RankUpdateDate`-Dato, hvor pladsen der er allokeret til denne funktion kaldes også resultat friskhed funktion. En historisk dato ('0001-01-01T00:00:00') betyder, at ingen plads build blev udført.
- `feed/entry/content/m:properties/d:Rank`-Funktion rang (flydende). Placeringen af 2.0 og op betragtes som en god funktion.
- `feed/entry/content/m:properties/d:SampleValues`-Kommasepareret liste over værdier op til størrelsen stikprøver, hvor der anmodes om.

OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get the features of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:54:22Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Author</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">3.38867426</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Publisher</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.67839336</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Year</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.12352145</d:Rank>
                    <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
                </m:properties>
            </content>
        </entry>
    </feed>


##<a name="11-build"></a>11. Opbyg

  Dette afsnit beskrives de forskellige API'er relateret til builds. Der er 3 typer builds: et anbefaling build, et plads build og et FBT (ofte har købt sammen) build.

Opret anbefaling formålet er at oprette en anbefaling model, der bruges til prognoser. Prognoser (for denne type build) findes to typer:
* I2I - kaldes også Emne til emne anbefalinger - givet et element eller en liste over elementer denne indstilling kan forudsige en liste over elementer, der sandsynligvis er af høj interesse.
* U2I - kaldes også Bruger element anbefalinger - den angivne et bruger-id (og eventuelt en liste over elementer) forudsige denne indstilling for en liste over elementer, der sandsynligvis er af høj interesse for den angivne bruger (og dens flere valg af varer). U2I anbefalinger er baseret på oversigten over elementer, der er af interesse for brugeren, indtil modellen blev oprettet.

Et plads build er et teknisk build, hvor du kan få mere at vide om mailfunktionen mere nyttig dine funktioner. Normalt, hvis du vil have det bedste resultat for en anbefaling model, der omfatter funktioner, skal du benytte følgende fremgangsmåde:
- Udløse en plads build (medmindre karakteren dine funktioner er stabil), og Vent, indtil du får funktion resultatet.
- Hent rangen for dine funktioner ved at ringe [Funktioner oplysninger](#101-get-features-info-for-last-rank-build) API.
- Konfigurere et anbefaling build med følgende parametre:
    - `useFeatureInModel`– Indstillet til sand.
    - `ModelingFeatureList`– Indstillet til en kommasepareret liste over funktioner med et resultat af 2.0 eller mere (ifølge de rangordner, du har hentet i ovenstående trin).
    - `AllowColdItemPlacement`– Indstillet til sand.
    - Du kan eventuelt angive `EnableFeatureCorrelation` til SAND og `ReasoningFeatureList` til listen over de funktioner, du vil bruge til forklaringer (som regel den samme liste over funktioner, der bruges i modellering eller en underliste).
- Udløse anbefaling Opret sammen med parametrene konfigureret.

Bemærk: Hvis du ikke konfigurerer en hvilken som helst parametre (fx kalde anbefaling Opret uden parametre) eller du ikke eksplicit deaktiverer brugen af funktioner (fx `UseFeatureInModel` indstillet til FALSK), systemet indstiller funktion beslægtede parametre til forklaret værdierne ovenfor, hvis der findes et plads build.

Der er ingen begrænsninger for kører et plads build og et anbefaling build samtidig til den samme model. Du kan dog køres to versioner af samme type på den samme model parallelt.

Et FBT (ofte har købt sammen) build er endnu en anden anbefalinger algoritme kaldes nogle gange "konservativ" anbefalet af, hvilket er velegnet til kataloger, der ikke er ensartet karakter (homogent: bøger, film, nogle mad måde; ikke-homogent: computer og enheder, mellem-domæner, meget forskelligartet).

Bemærk: Hvis brugen filerne, du har overført indeholder feltet valgfrit "hændelsestype" derefter FBT modellering kun "Køb" hændelser der skal bruges. Hvis ingen hændelsestype ikke er angivet alle hændelser, der betragtes som køb.


####<a name="111-build-parameters"></a>11.1 opbygge parametre

Hver build type kan konfigureres via et sæt af parametre (vist nedenfor). Hvis du ikke konfigurerer parametrene, attributten systemet automatisk værdier til parametrene ifølge de oplysninger, der findes på tidspunktet, du udløse en build.

#####<a name="1111-usage-condenser"></a>11.1.1. Brugen svaleren
Brugere eller elementer med par brugen punkter kan indeholde flere støj end oplysninger. Systemet forsøger at forudsige det minimale antal brugen punkter per bruger/element, der skal bruges i en model. Dette nummer bliver i det område, der er defineret af parametrene ItemCutoffLowerBound og ItemCutoffUpperBound for elementer, og det område, der er defineret af parametrene UserCutOffLowerBound og UserCutoffUpperBound for brugere. Effekten svaleren på elementer eller brugere kan minimeres ved at indstille mindst én af de tilsvarende grænser til nul.

#####<a name="1112-rank-build-parameters"></a>11.1.2. Rang opbygge parametre

Tabellen nedenfor beskrives build parametrene for en plads build.

|Nøgle|Beskrivelse|Type|Ugyldig værdi|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Antallet af gentagelser modellen udfører afspejles ved den overordnede Beregn tid og model nøjagtigheden. Jo højere tal, bedre nøjagtigheden får du, men Beregn tid tager længere tid.| Heltal | 10-50 |
| NumberOfModelDimensions | Antallet af dimensioner, der relaterer til antallet af 'Funktioner' modellen forsøger at finde inden for dine data. Øge antallet af dimensioner, så bedre finjustere af resultater i mindre klynger. Dog forhindrer for mange dimensioner modellen i finde korrelationer mellem elementerne. | Heltal | 10-40 |
|ItemCutOffLowerBound| Definerer den element nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|ItemCutOffUpperBound| Definerer den element øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffLowerBound| Definerer den bruger nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffUpperBound| Definerer den bruger øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |

#####<a name="1113-recommendation-build-parameters"></a>11.1.3. Anbefaling build parametre
Tabellen nedenfor beskrives build parametrene for anbefaling build.

|Nøgle|Beskrivelse|Type|Ugyldig værdi|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Antallet af gentagelser modellen udfører afspejles ved den overordnede Beregn tid og model nøjagtigheden. Jo højere tal, bedre nøjagtigheden får du, men Beregn tid tager længere tid.| Heltal | 10-50 |
| NumberOfModelDimensions | Antallet af dimensioner, der relaterer til antallet af 'Funktioner' modellen forsøger at finde inden for dine data. Øge antallet af dimensioner, så bedre finjustere af resultater i mindre klynger. Dog forhindrer for mange dimensioner modellen i finde korrelationer mellem elementerne. | Heltal | 10-40 |
|ItemCutOffLowerBound| Definerer den element nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|ItemCutOffUpperBound| Definerer den element øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffLowerBound| Definerer den bruger nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffUpperBound| Definerer den bruger øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
| Beskrivelse | Opbygge beskrivelse. | Streng | Tekst, maksimale 512 tegn |
| EnableModelingInsights | Gør det muligt at beregne målepunkter på anbefaling modellen. | Boolesk værdi | Sand/falsk |
| UseFeaturesInModel | Angiver, hvis funktioner kan bruges for at forbedre anbefaling modellen. | Boolesk værdi | Sand/falsk |
| ModelingFeatureList | Kommasepareret liste over funktionsnavne skal bruges i Opret anbefaling for at forbedre anbefalingen. | Streng | Funktionen navne, op til 512 tegn |
| AllowColdItemPlacement | Angiver, hvis anbefalingen også skal push kolde elementer via funktionen lighed. | Boolesk værdi | Sand/falsk |
| EnableFeatureCorrelation | Angiver, hvis funktioner kan bruges i Kognitiv. | Boolesk værdi | Sand/falsk |
| ReasoningFeatureList | Kommasepareret liste over funktionsnavne skal bruges til reasoning sætninger (fx anbefaling forklaringer).  | Streng | Funktionen navne, op til 512 tegn |
| EnableU2I | Tillad tilpassede anbefaling kaldes også U2I (brugeren element anbefalinger). | Boolesk værdi | Sand/falsk (standard SAND) |

#####<a name="1114-fbt-build-parameters"></a>11.1.4. FBT build parametre
Tabellen nedenfor beskrives build parametrene for anbefaling build.

|Nøgle|Beskrivelse|Type|Ugyldig værdi (standard)|
|:-----|:----|:----|:---|
|FbtSupportThreshold | Hvordan konservativ modellen er. Antallet af samtidig forekomster af elementer, der skal betragtes som for modellering.| Heltal | 3-50 (6) |
|FbtMaxItemSetSize | Bounds antallet af elementer i et hyppige sæt.| Heltal | 2-3 (2) |
|FbtMinimalScore | Minimale resultat, som skal have et hyppige sæt for at indgå i de returnerede resultater. Jo højere bedre.| Dobbelt | 0 og ovenfor (0) |
|FbtSimilarityFunction | Definerer funktionen lighed der skal bruges i den nyeste version. Elevatoren prioriteres højere serendipity, samtidig forekomst prioriteres højere forudsigelighed og Jaccard er et godt sammensat mellem to. | Streng | cooccurrence, elevatoren jaccard (elevator) |


###<a name="112-trigger-a-recommendation-build"></a>11.2. Udløse en anbefaling Build

  Som standard udløser denne API et anbefaling model build. Udløse en plads build (for at kunne resultat funktioner) build API variant med build typeparameter skal bruges.


| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|SIDEHOVED   |`"Content-Type", "text/xml"`(Hvis sender anmode om brødtekst)|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| userDescription | Tekstbaseret id for kataloget. Bemærk, at hvis du bruger mellemrum skal du kode det med % 20 i stedet. Se eksemplet ovenfor.<br>Maksimal længde: 50 |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | Hvis du stå tomt udføres Opret sammen med parametrene standard build.<br><br>Hvis du vil angive build parametre, skal du sende parametrene som XML ind i meddelelsesteksten som i følgende eksempel. (Se afsnittet "Oprette parametre" for en forklaring af parametre).`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**Svar**:

HTTP-statuskode: 200

Dette er en asynkron API. Du får et build-ID som et svar. Hvis du vil vide, når Opret er afsluttet, skal du ringe "Få opbygger Status for en Model" API og finde dette build-ID i svaret. Bemærk, at et build kan tage fra minutter til timer afhængigt af størrelsen af dataene.

Du kan ikke forbruge anbefalinger indtil Opret slutter.

Gyldige build status:

- Oprette - Build anmodningen blev oprettet.
- I kø - Build-anmodningen blev sendt, og det er i kø.
- Bygning - Build er i gang.
- Lykkedes - Build afsluttet.
- Fejl - Build afsluttet med en fejl.
- Annulleret - blev Build annulleret.
- Annullering af - en Annuller anmodning om Opret blev sendt.


Bemærk, at build-ID kan findes under følgende sti:`Feed\entry\content\properties\Id`

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###<a name="113-trigger-build-recommendation-rank-or-fbt"></a>11,3. Udløser Build (anbefaling, rang eller FBT)

| HTTP-metode | URI |
|:--------|:--------|
|INDLÆG     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|SIDEHOVED   |`"Content-Type", "text/xml"`(Hvis sender anmode om brødtekst)|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen  |
| userDescription | Tekstbaseret id for kataloget. Bemærk, at hvis du bruger mellemrum skal du kode det med % 20 i stedet. Se eksemplet ovenfor.<br>Maksimal længde: 50 |
| buildType | Opret til at aktivere type: <br/> -'Anbefaling' for anbefaling build <br> -Rangering for plads build <br/> -'Fbt' for FBT build
| apiVersion | 1.0 |
|||
| Anmodningsteksten | Hvis du stå tomt udføres Opret sammen med parametrene standard build.<br><br>Hvis du vil angive build parametre, skal du sende dem som XML ind i meddelelsesteksten som i følgende eksempel. (Se afsnittet "Oprette parametre" for en forklaring og en komplet liste over parametrene).`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**Svar**:

HTTP-statuskode: 200

Dette er en asynkron API. Du får et build-ID som et svar. Hvis du vil vide, når Opret er afsluttet, skal du ringe "Få opbygger Status for en Model" API og finde dette build-ID i svaret. Bemærk, at et build kan tage fra minutter til timer afhængigt af størrelsen af dataene.

Du kan ikke forbruge anbefalinger indtil Opret slutter.

Gyldige build status:

- Oprette - modellen blev oprettet.
- I kø - Model build blev udløst og det er i kø.
- Bygning - Model bygger.
- Lykkedes - Build afsluttet.
- Fejl - Build afsluttet med en fejl.
- Annulleret - blev Build annulleret.
- Annullering af - Build annulleres.

Bemærk, at build-ID kan findes under følgende sti:`Feed\entry\content\properties\Id`

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###<a name="114-get-builds-status-of-a-model"></a>11.4. Hent opbygger Status for en Model
Henter builds og deres status for en bestemt model.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   modelId         |   Entydigt id for modellen  |
|   onlyLastBuild   |   Angiver, om der skal returneres alle build oversigten over modellen eller kun status for det seneste build  |
|   apiVersion      |   1.0                                 |


**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post per build. Hvert element har følgende data:

- `feed/entry/content/properties/UserName`-Navnet på brugeren.
- `feed/entry/content/properties/ModelName`-Navnet på modellen.
- `feed/entry/content/properties/ModelId`-Model entydigt id.
- `feed/entry/content/properties/IsDeployed`-Om Opret er implementeret (kaldes også aktive build).
- `feed/entry/content/properties/BuildId`-Opbygge entydigt id.
- `feed/entry/content/properties/BuildType`-Opret type.
- `feed/entry/content/properties/Status`-Opbygge status. Kan være en af følgende: fejl dokumentkomponent i kø, Cancelling, annulleret, og succes.
- `feed/entry/content/properties/StatusMessage`-Den detaljerede statusmeddelelse (gælder kun for bestemte stater).
- `feed/entry/content/properties/Progress`-Opbygge status (%).
- `feed/entry/content/properties/StartTime`-Build starttidspunkt.
- `feed/entry/content/properties/EndTime`-Opbygge sluttidspunkt.
- `feed/entry/content/properties/ExecutionTime`-Opbygge varighed.
- `feed/entry/content/properties/ProgressStep`-Oplysninger om den aktuelle fase for et build i gang.

Gyldige build status:
- Oprettet - blev Build anmodning om posten oprettet.
- I kø - Build anmodningen blev udløst og det er i kø.
- Bygning - Build er i gang.
- Lykkedes - Build afsluttet.
- Fejl - Build afsluttet med en fejl.
- Annulleret - blev Build annulleret.
- Annullering - Build annulleres.

Gyldige værdier for build type:
- Rang - rang opbygge.
- Anbefaling - anbefaling build.


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T17:51:10Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T17:51:10Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###<a name="115-get-builds-status"></a>11.5. Hent opbygger Status
Henter opbygge status for alle modeller for en bruger.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
|   onlyLastBuild   |   Angiver, om der skal returneres alle build oversigten over modellen eller kun status for det seneste build. |
|   apiVersion      |   1.0                                 |


**Svar**:

HTTP-statuskode: 200

Svaret indeholder én post per build. Hvert element har følgende data:

- `feed/entry/content/properties/UserName`-Navnet på brugeren.
- `feed/entry/content/properties/ModelName`-Navnet på modellen.
- `feed/entry/content/properties/ModelId`-Model entydigt id.
- `feed/entry/content/properties/IsDeployed`-Om Opret er installeret.
- `feed/entry/content/properties/BuildId`-Opbygge entydigt id.
- `feed/entry/content/properties/BuildType`-Opret type.
- `feed/entry/content/properties/Status`-Opbygge status. Kan være en af følgende: fejl dokumentkomponent i kø, annulleret, Cancelling, og succes.
- `feed/entry/content/properties/StatusMessage`-Den detaljerede statusmeddelelse (gælder kun for bestemte stater).
- `feed/entry/content/properties/Progress`-Oprette status (%).
- `feed/entry/content/properties/StartTime`-Build starttidspunkt.
- `feed/entry/content/properties/EndTime`-Opbygge sluttidspunkt.
- `feed/entry/content/properties/ExecutionTime`-Opbygge varighed.
- `feed/entry/content/properties/ProgressStep`-Oplysninger om den aktuelle fase for et build i gang.

Gyldige build status:
- Oprettet - blev Build anmodning om posten oprettet.
- I kø - Build anmodningen blev udløst og det er i kø.
- Bygning - Build er i gang.
- Lykkedes - Build afsluttet.
- Fejl - Build afsluttet med en fejl.
- Annulleret - blev Build annulleret.
- Annullering - Build annulleres.


Gyldige værdier for build type:
- Rang - rang opbygge.
- Anbefaling - anbefaling build.


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T18:41:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T18:41:21Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###<a name="116-delete-build"></a>11.6. Slette Build
Sletter et build.

BEMÆRK! <br>Du kan ikke slette et aktivt build. Modellen skal opdateres til et andet aktivt build, inden du sletter den.<br>Du kan ikke slette et igangværende build. Du skal først annullere Opret ved at ringe til <strong>Annullere opbygge</strong>.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| buildId | Entydigt id for Opret. |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200

###<a name="117-cancel-build"></a>11,7. Annullere Build
Annullerer et build, der er i opbygning af status.

| HTTP-metode | URI |
|:--------|:--------|
|LÆG     |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| buildId | Entydigt id for Opret. |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200

###<a name="118-get-build-parameters"></a>11.8. Få Build parametre
Henter opbygge parametre.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| buildId | Entydigt id for Opret. |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200

Denne API returnerer en samling af nøgleværdi/elementer. Hvert element repræsenterer en parameter og dens værdi:
- `feed/entry/content/properties/Key`-Opbygge parameternavn.
- `feed/entry/content/properties/Value`-Opbygge parameterværdi.

Tabellen nedenfor viser den værdi, der repræsenterer hver nøgle.

|Nøgle|Beskrivelse|Type|Ugyldig værdi|
|:-----|:----|:----|:---|
|NumberOfModelIterations | Antallet af gentagelser modellen udfører afspejles ved den overordnede Beregn tid og model nøjagtigheden. Jo højere tal, bedre nøjagtigheden får du, men Beregn tid tager længere tid.| Heltal | 10-50 |
| NumberOfModelDimensions | Antallet af dimensioner, der relaterer til antallet af 'Funktioner' modellen forsøger at finde inden for dine data. Øge antallet af dimensioner, så bedre finjustere af resultater i mindre klynger. Dog forhindrer for mange dimensioner modellen i finde korrelationer mellem elementerne. | Heltal | 10-40 |
|ItemCutOffLowerBound| Definerer den element nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|ItemCutOffUpperBound| Definerer den element øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffLowerBound| Definerer den bruger nedre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
|UserCutOffUpperBound| Definerer den bruger øvre grænse for dennes. Se brugen svaleren ovenfor. | Heltal | 2 eller flere (0 Deaktiver svaleren) |
| Beskrivelse | Opbygge beskrivelse. | Streng | Tekst, maksimale 512 tegn |
| EnableModelingInsights | Gør det muligt at beregne målepunkter på anbefaling modellen. | Boolesk værdi | Sand/falsk |
| UseFeaturesInModel | Angiver, hvis funktioner kan bruges for at forbedre anbefaling modellen. | Boolesk værdi | Sand/falsk |
| ModelingFeatureList | Kommasepareret liste over funktionsnavne skal bruges i Opret anbefaling for at forbedre anbefalingen. | Streng | Funktionen navne, op til 512 tegn |
| AllowColdItemPlacement | Angiver, hvis anbefalingen også skal push kolde elementer via funktionen lighed. | Boolesk værdi | Sand/falsk |
| EnableFeatureCorrelation | Angiver, hvis funktioner kan bruges i Kognitiv. | Boolesk værdi | Sand/falsk |
| ReasoningFeatureList | Kommasepareret liste over funktionsnavne skal bruges til reasoning sætninger (fx anbefaling forklaringer).  | Streng | Funktionen navne, op til 512 tegn |


XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get build parameters</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:50:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
            <titletype="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">Description</d:Key>
                    <d:Value m:type="Edm.String">rankBuild</d:Value>
                </m:properties>
            </content>
        </entry>
    </feed>

##<a name="12-recommendation"></a>12. anbefaling
###<a name="121-get-item-recommendations-for-active-build"></a>12.1. Få element anbefalinger (for aktive build)

Få anbefalinger af det aktive build af typen "Anbefaling" eller "Fbt" baseret på en liste over frø (Skriv) elementer.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| at ItemID'er | Kommasepareret liste over de elementer, der anbefales til. <br>Hvis det aktive build er af typen FBT kan du sende kun ét element. <br>Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater <br> Maks: 150 |
| includeMetatadata | Fremtidig brug altid false |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element. Hvert element har følgende data:
- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Eksempel svaret nedenfor indeholder 10 anbefalede elementer.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###<a name="122-get-item-recommendations-of-a-specific-build"></a>12.2. Få element anbefalinger (for en bestemt build)

Få anbefalinger for et bestemt build af typen "Anbefaling" eller "Fbt".

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| at ItemID'er | Kommasepareret liste over de elementer, der anbefales til. <br>Hvis det aktive build er af typen FBT kan du sende kun ét element. <br>Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater <br> Maks: 150  |
| includeMetatadata | Fremtidig brug altid false
| buildId | build-id, der skal bruges til denne anbefaling anmodning |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element. Hvert element har følgende data:
- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Se et eksempel på svar i 12.1

###<a name="123-get-fbt-recommendations-for-active-build"></a>12.3. Få FBT anbefalinger (for aktive build)

Få anbefalinger af det aktive build af typen "Fbt" baseret på et element, der frø (input).

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| element-id | Element til anbefaler til. <br>Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater <br>Maks: 150 |
| minimalScore | Minimale resultat, som skal have et hyppige sæt for at indgå i de returnerede resultater |
| includeMetatadata | Fremtidig brug altid false |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element sæt (et sæt af elementer som normalt har købt sammen med elementet frø/input). Hvert element har følgende data:
- `Feed\entry\content\properties\Id1`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name1`-Navnet på elementet.
- `Feed\entry\content\properties\Id2`-2. anbefalede vare-ID (valgfrit).
- `Feed\entry\content\properties\Name2`-Navnet på elementet 2. (valgfrit).
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Eksempel svaret nedenfor indeholder 3 sæt, anbefalede element.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
        <d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="124-get-fbt-recommendations-of-a-specific-build"></a>12.4. Få FBT anbefalinger (for en bestemt build)

Få anbefalinger for et bestemt build af typen "Fbt".

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| element-id | Element til anbefaler til. <br>Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater <br>Maks: 150 |
| minimalScore | Minimale resultat, som skal have et hyppige sæt for at indgå i de returnerede resultater |
| includeMetatadata | Fremtidig brug altid false |
| buildId | build-id, der skal bruges til denne anbefaling anmodning |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element sæt (et sæt af elementer som normalt har købt sammen med elementet frø/input). Hvert element har følgende data:
- `Feed\entry\content\properties\Id1`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name1`-Navnet på elementet.
- `Feed\entry\content\properties\Id2`-2. anbefalede vare-ID (valgfrit).
- `Feed\entry\content\properties\Name2`-Navnet på elementet 2. (valgfrit).
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Se et eksempel på svar i 12.3

###<a name="125-get-user-recommendations-for-active-build"></a>12.5. Få bruger anbefalinger (for aktive build)

Få bruger anbefalinger af et build af typen "Anbefaling" er markeret som aktive build.

API returnerer en liste over estimerede element efter oversigt over brug af brugeren.

Noter: 
 1. Der er ingen bruger anbefalinger til FBT build.
 2. Hvis det aktive build er FBT denne metode, der returnerer en fejl.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| bruger-id  | Entydigt id for brugeren |
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

Se et eksempel på svar i 12.1

###<a name="126-get-user-recommendations-with-item-list-for-active-build"></a>12.6. Få bruger anbefalinger med listen over elementer (for aktive build)

Få tip hvordan du bruger en opbygning af typen "Anbefaling" er markeret som aktive build med en ekstra liste over poster

API returnerer en liste over estimerede element efter brugen oversigten for brugeren, og de ekstra medfølgende elementer.

Noter: 
 1. Der er ingen bruger anbefalinger til FBT build.
 2. Hvis det aktive build er FBT denne metode, der returnerer en fejl.


| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| bruger-id  | Entydigt id for brugeren |
| itemsIds | Kommasepareret liste over de elementer, der anbefales til. Maksimal længde: 1024 |
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

Se et eksempel på svar i 12.1

###<a name="127-get-user-recommendations--of-a-specific-build"></a>12,7. Få bruger anbefalinger (for en bestemt build)

Få tip hvordan du bruger en bestemt opbygning af typen "Anbefaling".

API returnerer en liste over estimerede element efter oversigt over brug af brugeren (bruges i specifikke Opret).

Bemærk: Der er ingen bruger anbefalinger til FBT build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| bruger-id  | Entydigt id for brugeren |
| numberOfResults | Antallet af nødvendige resultater |
| includeMetatadata | Fremtidig brug altid false |
| buildId | build-id, der skal bruges til denne anbefaling anmodning |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element. Hvert element har følgende data:
- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Se et eksempel på svar i 12.1


###<a name="128-get-user-recommendations-with-item-list-of-a-specific-build"></a>12,8. Få bruger anbefalinger med listen over elementer (for en bestemt build)

Få bruger anbefalinger for et bestemt build af typen "Anbefaling" og listen over flere elementer.

API returnerer en liste over estimerede element efter brugen oversigten for brugeren, og den supplerende liste over elementer.

Bemærk: Tthere er ingen bruger anbefalinger til FBT build.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| bruger-id  | Entydigt id for brugeren |
| at ItemID'er | Kommasepareret liste over de elementer, der anbefales til. Maksimal længde: 1024 |
| numberOfResults | Antallet af nødvendige resultater |
| includeMetatadata | Fremtidig brug altid false |
| buildId | build-id, der skal bruges til denne anbefaling anmodning |
| apiVersion | 1.0 |

**Svar:**

HTTP-statuskode: 200


Svaret indeholder én post per anbefalede element. Hvert element har følgende data:
- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-Bedømmelse af anbefaling; højere tal betyder højere konfidensinterval.
- `Feed\entry\content\properties\Reasoning`-Anbefaling reasoning (fx anbefaling forklaringer).

Se et eksempel på svar i 12.1

##<a name="13-user-usage-history"></a>13. oversigt over brug af brugeren
Når en anbefaling model blev oprettet systemet vil tillade, for at hente bruger oversigten (elementer, der er knyttet til en bestemt bruger) bruges til den nyeste version.
Tillad denne API til at hente bruger oversigten

Bemærk: bruger oversigten er i øjeblikket kun tilgængelig for anbefaling builds.

###<a name="131-retrieve-user-history"></a>13.1 hente bruger historik
Hente listen over element, der bruges i det aktive build eller i det angivne build for den angivne bruger-id.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     | Få bruger oversigten for det aktive build.<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>Få bruger oversigten for det angivne build`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>Eksempel:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen.|
| bruger-id | Entydigt id for brugeren.
| buildId | valgfri parameter, give tilladelse til at angive fra hvilke build bruger oversigten skal være Hent
| apiVersion | 1.0 |


**Svar:**

HTTP-statuskode: 200

Svaret indeholder én post per anbefalede element. Hvert element har følgende data:
- `Feed\entry\content\properties\Id`-Anbefalede element-ID.
- `Feed\entry\content\properties\Name`-Navnet på elementet.
- `Feed\entry\content\properties\Rating`-I/T.
- `Feed\entry\content\properties\Reasoning`-I/T.

XML-OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get User History</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-05-26T15:32:47Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">CatalogItemsThatContainATokenEntity</title>
        <updated>2015-05-26T15:32:47Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Rating m:type="Edm.Double">0</d:Rating>
                <d:Reasoning m:type="Edm.String"/>
                <d:Metadata m:type="Edm.String"/>
                <d:FormattedRating m:type="Edm.Double" m:null="true"/>
            </m:properties>
        </content>
    </entry>
</feed>

##<a name="14-notifications"></a>14. meddelelser om
Azure Machine Learning anbefalinger opretter beskeder, når fast fejl sker i systemet. Der er 3 typer meddelelser:
1.  Opbyg fejl – denne meddelelse udløses for hver build-fejl.
2.  Dataindsamling behandling fejl – denne meddelelse udløses, når vi har mere end 100 fejl i de sidste 5 minutter i behandling af forbrugsbegivenheder i modellen.
3.  Anbefaling forbrug fejl – denne meddelelse udløses, når vi har mere end 100 fejl i de sidste 5 minutter i behandling af anmodninger om anbefaling i modellen.


###<a name="141-get-notifications"></a>14,1. Få besked via
Henter alle beskeder for alle modeller eller for en enkelt model.

| HTTP-metode | URI |
|:--------|:--------|
|HENT     |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>Få alle meddelelser i alle modeller:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>Eksempel for at komme beskeder for en bestemt model:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Valgfri parameter. Når udelades, får du alle beskeder for alle modeller. <br>Ugyldig værdi: entydigt id for modellen.|
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar:**

HTTP-statuskode: 200

XML-OData

    The response includes one entry per notification. Each entry has the following data:
        * feed\entry\content\properties\UserName - Internal user name identification.
        * feed\entry\content\properties\ModelId - Model ID.
        * feed\entry\content\properties\Message - Notification message.
        * feed\entry\content\properties\DateCreated - Date that this notification was created in UTC format.
        * feed\entry\content\properties\NotificationType - Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of Notifications for a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-04T13:24:23Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
        <entry>
                <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfNotificationsForAUserEntity</title>
            <updated>2014-11-04T13:24:23Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
                    <d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
                    <d:Message m:type="Edm.String">Build failed for user</d:Message>
                    <d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
                    <d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
                </m:properties>
            </content>
        </entry>
    </feed>

###<a name="142-delete-model-notifications"></a>14.2. Slette Model beskeder
Sletter alle læste meddelelser i en model.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>Eksempel:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| modelId | Entydigt id for modellen |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200

###<a name="143-delete-user-notifications"></a>14.3. Slette brugerbeskeder
Sletter alle beskeder for alle modeller.

| HTTP-metode | URI |
|:--------|:--------|
|SLET     |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|   Parameternavn  |   Gyldige værdier                        |
|:--------          |:--------                              |
| apiVersion | 1.0 |
|||
| Anmodningsteksten | INGEN |

**Svar**:

HTTP-statuskode: 200




##<a name="15-legal"></a>15. juridiske
Dette dokument leveres "som-er". Du kan finde oplysninger og visninger, der er angivet i dette dokument, herunder URL-Adresser og andre referencer til websteder, kan ændres uden varsel.<br><br>
Nogle eksempler, der er anvendt i eksempler findes kun til illustration og er opdigtet. Ingen reelle tilknytning eller forbindelse er beregnet eller begivenheder.<br><br>
Dette dokument giver dig en hvilken som helst juridiske rettigheder til intellektuel ejendom i Microsoft-produkter. Du kan kopiere og bruge dette dokument til interne referenceformål.<br><br>
© 2015 Microsoft. Microsoft Corporation.
 
