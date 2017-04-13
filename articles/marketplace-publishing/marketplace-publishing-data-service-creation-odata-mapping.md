<properties
   pageTitle="Vejledning i at oprette en datatjeneste på Marketplace | Microsoft Azure"
   description="Detaljerede oplysninger om, hvordan du opretter, bekræfte og installerer en datatjeneste til køb på Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Tilknytte en eksisterende webtjeneste til OData gennem CSDL

>[AZURE.IMPORTANT] **På nuværende tidspunkt vi er ikke længere er onboarding alle nye datatjeneste udgivere. Ny dataservices kan ikke få godkendt til listen.** Du kan finde flere oplysninger, hvis du har en SaaS forretningsprogram, du vil have til at publicere AppSource [her](https://appsource.microsoft.com/partners). Hvis du har en IaaS programmer eller udvikler tjeneste, du gerne vil publicere Azure Marketplace du kan finde flere oplysninger [her](https://azure.microsoft.com/marketplace/programs/certified/).

I denne artikel giver et overblik over, hvordan du bruger en CSDL til at tilknytte en eksisterende tjeneste til et OData-kompatibel tjeneste. Det forklarer, hvordan til at oprette tilknytning dokumentet (CSDL) transformerer input anmodningen fra klienten på computeren via et serviceopkald og output (data) tilbage til klienten via et kompatible OData-feed. Microsofts Azure Marketplace Fremviser tjenester til slutbrugerne ved hjælp af OData-protokollen. Tjenester, der vises af indholdsudbydere (Data ejere) vises i en række forskellige formularer, som RESTEN, SOAP osv.

## <a name="what-is-a-csdl-and-its-structure"></a>Hvad er en CSDL og dens struktur?
En CSDL (grundlæggende skema Definition Language) er en specifikation, der definerer hvordan til at beskrive webtjeneste eller databasetjenesten i almindelige XML-dialog til Azure Marketplace.

Simpel oversigt over de **anmode om flow:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**Dataflow** er i den modsatte retning:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figur 1** diagrammer, hvordan en klient vil hente data fra en indhold udbyder (service) ved at gå gennem Azure Marketplace.  CSDL bruges af komponenten tilknytning/Transformation til at håndtere anmodningen og overfører data mellem indhold udbyderens tjenester og anmoder om klienten.

*Figur 1: Detaljeret flow fra anmoder om klient til indhold udbyder via Azure Marketplace*

  ![tegning](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Få baggrundsoplysninger om Atom Atom Pub og OData-protokollen, som opretter Azure Marketplace-udvidelser, skal du gennemse: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Uddrag fra et sted over link:     *"formålet med den Open Data protocol (herefter kaldet OData) er at tilvejebringe et RESTEN-baseret protokol for CRUD-typografi handlinger (oprette, læse, Opdater og Slet) mod ressourcer, der vises som data services. En "datatjeneste" er et slutpunkt hvor der er data, der vises fra en eller flere "samlinger" hver med nul eller flere "tekst", som består af skrevet med navnet / værdi-par. OData udgives af Microsoft under OASIS (organisation til overflytning af strukturerede oplysninger standarderne) standarder, så alle, som ønsker at kan udvikle servere, klienter eller værktøjer uden royalty eller begrænsninger."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Tre vigtige enheder, der skal være defineret af CSDL er:

- **Slutpunkt** af Service Provider feltet Web adresse (URI) tjenesten
- **Data parametre** , der angives som input til udbyderen af definitioner af parametrene, der sendes til provideren indhold service ned til datatypen.
- **Skema** for dataene returneres til tjenesten anmoder om skemaet for de data, der leveres af provideren indhold tjenesten, herunder objektbeholder, samlinger/tabeller, variabler/kolonner og datatyper.

I det følgende diagram viser en oversigt over strømmen fra hvor klienten indtaster sætningen OData (kald til provideren indhold webtjeneste) til at opnå resultater/dataene tilbage.

  ![tegning](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Trin:

1. Klienten sender en anmodning om via tjenesten opkald komplet med inputparametre, der er defineret i XML til Azure Marketplace
2. CSDL bruges til at validere Service opkaldet.
    - Den formateret Service opkald sendes til tjenesten indhold udbydere af Azure Marketplace
3. Webtjeneste udfører og preforms handlingen med Http-verber (det vil sige få) returneres dataene til Azure Marketplace, hvor de ønskede data (hvis relevant) er viser i XML-Format til klienten ved hjælp af den tilknytning, der er defineret i CSDL.
4. Klienten sendes dataene (hvis relevant) i XML- eller JSON-format

## <a name="definitions"></a>Definitionerne bevares

### <a name="odata-atom-pub"></a>OData ATOM pub

Angive en udvidelse af den ATOM pub, hvor hvert element repræsenterer én række af et resultat. Delen indhold af indtastningen er forbedret, så den indeholder værdierne for rækken – som vigtige værdi-par. Yderligere oplysninger findes her: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - grundlæggende skema Definition Language

Giver mulighed for definition af funktioner (SPROCs) og enheder, der er fremvises en database. Yderligere oplysninger, der er vist her: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Klik på rullepilen i **andre versioner** , og vælg en version, hvis du ikke kan se i artiklen.

### <a name="edm---entry-data-model"></a>EDM - posten datamodel
- Oversigt: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Eksempel: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Datatyper: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

Følgende viser detaljerede venstre mod højre flyde fra hvor klienten indtaster sætningen OData (kald til provideren indhold webtjeneste) til at opnå resultater/dataene igen:

  ![tegning](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Grundlæggende om CSDL

En CSDL (grundlæggende skema Definition Language) er en specifikation, der definerer hvordan til at beskrive webtjeneste eller databasetjenesten i almindelige XML-dialog til Azure Marketplace. CSDL beskriver den kritiske stykker, **gør, at data fra datakilden til Azure Marketplace muligt.** De vigtigste dele er beskrevet her:

- Brugergrænseflade oplysninger, der beskriver alle offentligt tilgængelige funktioner (FunctionImport Node)
- Oplysninger om datatyper for alle meddelelse requests(input) og meddelelse responses(outputs) (EntityContainer/sæt med enheder/EntityType noder)
- Bindende oplysninger om transportprotokollen skal bruges (sidehoved Node)
- Adresseoplysninger til at finde den angivne tjeneste (BaseURI attribut)

I en nøddeskal repræsenterer på CSDL en platform og sprog uafhængige kontrakt mellem service-anmoder og udbyderen af. Ved hjælp af CSDL, kan en klient finde en webtjeneste til tjenesten/database og starte nogen af dens offentligt tilgængelige funktioner.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Vedrørende en CSDL til en Database eller en samling
**Specifikationen CSDL**

CSDL er XML-grammatikkontrol til at beskrive en webtjeneste. Specifikationen selve er inddelt i 4 overordnede elementer: sæt med enheder, FunctionImport; Navneområde, og EntityType.

At gøre det lettere at forstå giver mulighed for at få denne fremstilling relatere et CSDL til en tabel.

Husk;

  CSDL repræsenterer en platform og sprog uafhængige kontrakt mellem **service-anmoder** og **tjenesteudbyder**. Brug CSDL, kan en **klient** , Find en **webtjeneste service/database** og starte nogen af dens offentligt tilgængeligt **Funktioner.**

For en datatjeneste kan de fire dele af en CSDL betragtes med hensyn til en Database, tabel, kolonne og Store Procedure.

Vedrørende disse oplysninger til en datatjeneste på følgende måde:

- EntityContainer ~ = Database
- Sæt med enheder ~ = tabel
- EntityType ~ = kolonner
- FunctionImport ~ = lagret Procedure

**HTTP-verber tilladt**
- FÅ – returnerer værdier fra den database (returnerer en samling)
- INDLÆG – bruges til at overføre data til og valgfrit returværdier fra db (oprette en ny post i gruppen af websteder, returnerede id/URI)
- Slet – sletter data fra DB (sletter en samling)
- PLACERE – opdatere data i en DB (erstatte en samling, eller Opret en)

## <a name="metadatamapping-document"></a>Metadata/tilknytning dokument

Dokumentets metadata/tilknytning bruges til at tilknytte en indhold udbyder eksisterende webtjenester, så den kan vises som et OData-webtjeneste af Azure Marketplace-system. Det er baseret på CSDL og implementerer et par udvidelser til CSDL til behovet i RESTEN baseret webtjenester fremvises Azure Marketplace. Feltet filtypenavne findes i navneområdet [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Et eksempel på CSDL følger: (kopiere og indsætte den under eksempel CSDL i en XML-editor, og Skift så det svarer til din tjeneste.  Sæt ind i CSDL tilknytning under fanen DataService ved oprettelse af din tjeneste i [Azure Marketplace Udgivelsesportal](https://publish.windowsazure.com)).

**Vilkår:** Vedrørende CSDL betingelserne [Udgivelsesportal](https://publish.windowsazure.com) Brugergrænsefladen (PPUI) betingelser.
- Tilbyde "Titel" i PPUI relaterer til MyWebOffer
- Hvis du i PPUI relaterer til **Publisher visningsnavn** i [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) brugergrænseflade
- Din API relaterer til et websted eller datatjeneste (en Plan inden for PPUI)

**Hierarki:** 
 en virksomhed (indhold Provider) ejer tilbud, der har eller de planer, nemlig service(s), hvilken linje op med en API.

### <a name="webservice-csdl-example"></a>Webtjeneste CSDL eksempel

Opretter forbindelse til en tjeneste, som udsætte web application ark (som et C#-program)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Få vist flere CSDL webtjeneste eksempler i artiklen [eksempler for at tilknytte en eksisterende webtjeneste til OData gennem CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Eksempel på DataService CSDL

Opretter forbindelse til en tjeneste, som udsætte en databasetabel eller visning, som et slutpunkt nedenstående eksempel viser to API'er til grundlæggende Data baseret API CSDL (kan bruge visninger i stedet for tabeller).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Se også
- Hvis du er interesseret i at lære og forstå noderne bestemte og deres parametre Læs denne artikel [Data Service OData tilknytning noder](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) til definitioner og forklaringer, eksempler og bruge tilfælde kontekst.
- Hvis du er interesseret i at gennemgå eksempler, kan du læse denne artikel [Data Service OData tilknytning eksempler](marketplace-publishing-data-service-creation-odata-mapping-examples.md) , der kan se eksempler på kode og forstå syntaks for feltkoder og kontekst.
- Læs denne artikel [Data Service publicering Guide](marketplace-publishing-data-service-creation.md)for at vende tilbage til bestemt stien til publicering af en datatjeneste til Azure Marketplace.
