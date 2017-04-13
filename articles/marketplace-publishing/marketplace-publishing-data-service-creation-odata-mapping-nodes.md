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

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Forstå noder skemaet for at tilknytte en eksisterende webtjeneste til OData gennem CSDL

>[AZURE.IMPORTANT] **På nuværende tidspunkt vi er ikke længere er onboarding alle nye datatjeneste udgivere. Ny dataservices kan ikke få godkendt til listen.** Du kan finde flere oplysninger, hvis du har en SaaS forretningsprogram, du vil have til at publicere AppSource [her](https://appsource.microsoft.com/partners). Hvis du har en IaaS programmer eller udvikler tjeneste, du gerne vil publicere Azure Marketplace du kan finde flere oplysninger [her](https://azure.microsoft.com/marketplace/programs/certified/).

Dette dokument hjælper afklare strukturen for at tilknytte en OData-protokol til CSDL node. Det er vigtigt at være opmærksom på, at node strukturen er godt udformet XML. Rod, overordnede og underordnede skemaet er så det er relevant, når du designer din OData-tilknytning.

## <a name="ignored-elements"></a>Ignorerede elementer
Følgende er på højt niveau CSDL elementer (XML-noder), der ikke skal bruges af back-Azure Marketplace under import af webtjenesten metadata. De kan være til stede, men vil blive ignoreret.

| Element | Omfang |
|----|----|
| Ved hjælp af Element | Noden, sub noder og alle attributter |
| Dokumentation Element | Noden, sub noder og alle attributter |
| ComplexType | Noden, sub noder og alle attributter |
| Tilknytning Element | Noden, sub noder og alle attributter |
| Udvidet egenskab | Noden, sub noder og alle attributter |
| EntityContainer | Kun følgende attributter ignoreres: *udvider* og *AssociationSet* |
| Skema | Kun følgende attributter ignoreres: *Namespace* |
| FunctionImport | Kun følgende attributter ignoreres: *tilstand* (standardværdien for ln antages) |
| EntityType | Kun følgende sub noder ignoreres: *nøgle* og *PropertyRef* |

Nedenfor beskrives de ændringer (tilføjet og ignoreret elementer) til de forskellige CSDL XML noder detaljeret.

## <a name="functionimport-node"></a>FunctionImport node
En FunctionImport node repræsenterer én URL-adresse (indgangspunkt), der viser en tjeneste til slutbrugeren. Noden tillader, der beskriver, hvordan URL-adressen er adresseret, hvilke parametre, der er tilgængelige til slutbrugeren og hvordan disse parametre leveres.

Oplysninger om denne node findes på [her] [MSDNFunctionImportLink]

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Følgende er de yderligere attributter (eller tilføjelser til attributter), der vises af noden FunctionImport:

**d:BaseUri** -
feltet URI-skabelonen for RESTEN ressourcen, der vises til Marketplace. Marketplace bruger skabelonen til at oprette forespørgsler REST-webtjeneste. URI-skabelonen indeholder pladsholdere for parametre i form af {parameterName}, hvor parameterName er navnet på parameteren. F.eks. apiVersion = {apiVersion}.
Parametre, der kan vises som URI parametre eller som en del af URI-sti. I forbindelse med udseende i stien de er altid obligatorisk (kan ikke markeres som null-værdi). *Eksempel:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Navn** - navnet på den importerede funktion.  Må ikke være den samme som andre definerede navne i CSDL.  F.eks. Navn = "GetModelUsageFile"

**Sæt med enheder** *(valgfrit)* – Hvis funktionen returnerer en samling af objekttyper, værdien af **sæt med enheder** skal være den enhed, der er angivet som samlingen tilhører. Ellers skal attributten **sæt med enheder** ikke bruges. *Eksempel:*`EntitySet="GetUsageStatisticsEntitySet"`

**Returtype** *(Valgfrit)* - angiver typen elementer, der returneres af URI.  Brug ikke denne attribut, hvis den ikke returnerer en værdi. Følgende er de understøttede datatyper:

 - **Af websteder (<Entity type name>)**: Angiver en samling af definerede objekttyper. Navnet findes i Name-attributten for noden EntityType. Et eksempel er samling (WXC. HourlyResult).
 - **Raw (<mime type>)**: Angiver en rå dokument/blob, der returneres til brugeren. Et eksempel er Raw(image/jpeg) andre eksempler:

  - ReturnType="Raw(text/plain)"
  - Returtype = "af websteder (sage. DeleteAllUsageFilesEntity) "*

**d:Paging** - angiver, hvordan sideopdeling håndteres af RESTEN ressourcen. Parameterværdierne anvendes i krøllet braches fx siden = {$page} & itemsperpage = {$size} de tilgængelige indstillinger er:

- **Ingen:** ingen sideinddeling er tilgængelig
- **Spring:** sideopdeling udtrykkes via en logisk "Spring over" og "Hent" (øverst). Spring over springer over M elementer, og tag derefter returnerer de næste N elementer. Parameterværdi: $skip
- **Tage:** Tag returnerer de næste N elementer. Parameterværdi: $take
- **PageSize:** sideopdeling udtrykkes gennem en logisk side og størrelse (elementer på hver side). Siden repræsenterer den aktuelle side, der returneres. Parameterværdi: $page
- **Størrelse:** størrelse repræsenterer antallet af elementer, der returneres for hver side. Parameterværdi: $size

**d:AllowedHttpMethods** *(Valgfrit)* - angiver, hvilke verber håndteres af RESTEN ressourcen. Desuden begrænser accepteret verber til den angivne værdi.  Standard = INDLÆG.  *Eksempel:* `d:AllowedHttpMethods="GET"` De tilgængelige indstillinger er:

- **Få:** normalt bruges til at returnere data
- **INDLÆG:** normalt bruges til at indsætte nye data
- **PLACERE:** normalt bruges til at opdatere data
- **Slette:** bruges til at slette data

Flere underordnede hierarkinoder (ikke omfattet af CSDL dokumentation) i noden FunctionImport er:

**d:RequestBody** *(Valgfrit)* - anmodningsteksten bruges til at angive, at din anmodning forventer helhed skal sendes. Parametre kan angives i anmodningsteksten. De udtrykkes i klammeparenteser, f.eks. {parameterName}. Disse parametre er tilknyttet fra brugerinput ind i meddelelsesteksten, der overføres til det indhold provider-tjeneste. Elementet requestBody har en attribut med navnet httpMethod. Attributten tillader to værdier:

- **INDLÆG:** Bruges, hvis anmodningen er en HTTP-POST
- **Få:** Bruges, hvis anmodningen er en HTTP GET

    Eksempel:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** og **d:Namespace** - beskrives denne node navneområder, der er defineret i XML, der returneres af funktionen importen (URI slutpunkt). XML-filen, der returneres af back end-tjenesten kan indeholde et vilkårligt antal navneområder at skelne det indhold, der returneres. **Alle disse navneområder, hvis bruges i d:Map eller d:Match XPath-forespørgsler skal være angivet.** Noden d:Namespaces indeholder et sæt/liste over d:Namespace noder. Hver af dem indeholder et navneområde, der bruges i back end-tjenesten svaret. Følgende er attributten for noden d:Namespace:

-   **d:Prefix:** Præfikset for navneområdet, som det fremgår af XML-resultater, der returneres af tjenesten, fx f:FirstName, f:LastName, hvor f er præfikset.
- **d:Uri:** Den fulde URI af navneområdet bruges i dokumentets resultat. Den værdi, der præfikset oversættes til på kørselstidspunktet repræsenterer.

**d:ErrorHandling** *(Valgfrit)* - denne node indeholder betingelser for håndtering af fejl. Hver af betingelserne valideres mod det resultat, der returneres af indhold provider-tjeneste. Hvis en betingelse stemmer overens med den foreslåede HTTP-fejlkode returneres en fejlmeddelelse til slutbrugeren.

**d:ErrorHandling** *(Valgfrit)* og **d:Condition** *(valgfrit)* - en betingelse node indeholder en enkelt betingelse, der er markeret i resultatet, returneres af indhold provider-tjeneste. Følgende er de **nødvendige** attributter:

- **d:Match:** Et XPath-udtryk, der kontrollerer, om en given node/værdi er til stede i XML-indhold udbyderens outputtet. XPath-udtrykket kører mod output og skal returnere SAND, hvis betingelsen er en match eller FALSK ellers.
- **d:HttpStatusCode:** HTTP-statuskode, der skal returneres ved Marketplace i tilfælde betingelsen svarer til. Marketplace signalizes fejl til brugeren via HTTP statuskoder. En oversigt over HTTP statuskoder findes på http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** Den fejlmeddelelse, der returneres – med HTTP-statuskode – til slutbrugeren. Dette skal være en fejlmeddelelse, som ikke indeholder nogen hemmeligheder.

**d:Title** *(Valgfrit)* - giver mulighed for titlen på funktionen, der indeholder en beskrivelse. Værdien for titlen kommer fra

- Valgfrit kort attributten (en xpath) der angiver, hvor du finder titlen i svar, som returneres fra serviceanmodningen.
- - Eller - titlen angivet som værdien af noden.

**d:Rights** *(Valgfrit)* - rettigheder (fx copyright) der er knyttet til funktionen. Værdien for rettighederne, der kommer fra:

- Valgfrit kort attributten (en xpath) der angiver, hvor du finder rettigheder i svar, som returneres fra serviceanmodningen.
-   - Eller - de rettigheder, der er angivet som værdien af noden.

**d:Description** *(Valgfrit)* – en kort beskrivelse for funktionen. Værdien for beskrivelsen af kommer fra

- Valgfrit kort attributten (en xpath) der angiver, hvor du kan finde en beskrivelse i svaret returneres fra serviceanmodningen.
- - Eller – den beskrivelse, der er angivet som værdien af noden.

**d:EmitSelfLink** - *se over eksempel "FunctionImport for 'Sideopdeling' gennem returnerede data"*

**d:EncodeParameterValue** - valgfri udvidelse til OData

**d:QueryResourceCost** - valgfri udvidelse til OData

**d:Map** - valgfri udvidelse til OData

**d:Headers** - valgfri udvidelse til OData

**d:Headers** - valgfri udvidelse til OData

**d:Value** - valgfri udvidelse til OData

**d:HttpStatusCode** - valgfri udvidelse til OData

**d:ErrorMessage** - valgfri udvidelse til OData

## <a name="parameter-node"></a>Parameternode

Denne node repræsenterer en parameter, der vises som en del af skabelonen URI / anmodningsteksten, der er angivet i noden FunctionImport.

Side i et dokument meget nyttige oplysninger om noden "Parameter Element" er fundet på [her](http://msdn.microsoft.com/library/ee473431.aspx) (brug på **Andre Version** rullelisten til at vælge en anden version, hvis det er nødvendigt at få vist dokumentationen). *Eksempel:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Parameter attribut | Er påkrævet | Værdi |
|----|----|----|
| Navn | Ja | Navnet på parameteren. Bogstaver!  Svare til BaseUri store og små bogstaver. **Eksempel:**`<Property Name="IsDormant" Type="Byte" />` |
| Type | Ja | Parametertypen. Værdien skal være en **EDMSimpleType** eller en kompleks type, der er omfattet af modellen. Yderligere oplysninger finder du se "6 understøttede Parameter/egenskaben typer".  (Bogstaver! Første tegn er stort, resten er små bogstaver.)  Se også, [grundlæggende Model typer (CSDL)] [MSDNParameterLink]. **Eksempel:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Tilstand | Nej | **I**, ud eller ind-/ udgående afhængigt af om parameteren er en input, output eller input/output parameter. (Kun "Ind" er tilgængelig i Azure Marketplace.) **Eksempel:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | Nej | Den maksimale længde af parameteren. **Eksempel:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Præcision | Nej | Nøjagtigheden af parameteren. **Eksempel:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Skala | Nej | Skalaen på parameteren. **Eksempel:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Følgende er de attributter, der er blevet føjet til specifikationen CSDL:

| Parameter attribut | Beskrivelse |
|----|----|
| **d:regex** *(Valgfrit)* | En regex-sætning, der bruges til at validere inputværdi for parameteren. Hvis den værdi, der ikke stemmer overens med sætningen afvises værdien. Dette giver mulighed for at angive også et sæt af mulige værdier, f.eks. ^ [0-9] +? $ til kun at tillade tal. **Eksempel:** ' < parameternavn = "navn" tilstand = "I" Type = "Streng" d: null-værdi = "false" d:Regex = "^ [a-hamza-Z] * $" d:Description = "et navn, der ikke må indeholde mellemrum eller ikke er bogstaver ikke-engelsk tegn" d:SampleValues = "Jens|John|Thomas|James "/ >' |
| **d:Enum** *(Valgfrit)* | En pipe adskilt liste over værdier, der er gyldige for parameteren. Typen af værdierne skal svare til de angivne typer parameteren. Eksempel: ' engelsk|metrisk|rå`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< parameternavn = "Varighed" Type = "Streng" tilstand = "I" være nul = "true" d:Enum = "1 år|5years|10years "/ >' |
| **d: null-værdi** *(Valgfrit)* | Giver mulighed for definition af om en parameter kan være null. Standard er: SAND. Parametre, der vises som en del af stien i URI-skabelonen kan dog ikke null. Når attributten er indstillet til falsk for disse parametre – tilsidesættes brugerinput. **Eksempel:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Valgfrit)* | En eksempelværdi, der skal vises som en note til klienten i Brugergrænsefladen.  Det er muligt at tilføje flere værdier ved hjælp af en pipe adskilt liste, det vil sige ' en|b|c` **Example:** `< parameternavn = "BikeOwner" Type = "Streng" tilstand = "I" d:SampleValues = "Jens|John|Thomas|James "/ >' |

## <a name="entitytype-node"></a>EntityType node

Denne node repræsenterer en af de typer, der returneres fra Marketplace til slutbrugeren. Den indeholder også tilknytning fra output, der returneres af indhold provider-tjeneste til de værdier, der returneres til slutbrugeren.

Oplysninger om denne node findes på [her](http://msdn.microsoft.com/library/bb399206.aspx) (brug på **Andre Version** rullelisten til at vælge en anden version, hvis det er nødvendigt at få vist dokumentationen.)

| Attributnavn | Er påkrævet | Værdi |
|----|----|----|
| Navn | Ja | Navnet på enhedstypen. **Eksempel:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | Nej | Navnet på en anden enhedstype, som er den grundlæggende type af enhedstypen, der skal defineres. **Eksempel:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Følgende er de attributter, der er blevet føjet til specifikationen CSDL:

**d:Map** - et XPath-udtryk, der køres i forhold til tjenesten output. Her antages det, at service output indeholder et sæt af elementer, der gentages, såsom en ATOM-hvor der er et sæt posten noder, der gentages. Hver af disse gentaget noder indeholder én post. XPath-udtrykket angives derefter til at pege på den enkelte gentagne node i provideren indhold service resultat, der indeholder værdierne for en enkelt post. Eksempel på output fra tjenesten:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath-udtrykket, vil være /foo/søjle, fordi hver på linjen node er noden gentaget i outputtet, og den indeholder det faktiske indhold, der returneres til slutbrugeren.

**Nøgle** – denne attribut ignoreres ved Marketplace. RESTEN baseret webtjenester, skal du generelt få vist ikke en primær nøgle.


## <a name="property-node"></a>Egenskaben node

Denne node indeholder en egenskab for posten.

Oplysninger om denne node findes på [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (brug på **Andre Version** rullelisten til at vælge en anden version, hvis det er nødvendigt at få vist dokumentationen.) *Eksempel:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Påkrævet | Værdi |
|----|----|----|
| Navn | Ja | Navnet på egenskaben. |
| Type | Ja | Typen af egenskabsværdien for. Typen af egenskabsværdi skal være en **EDMSimpleType** eller en kompleks type (angivet med et fuldt kvalificeret navn), der er omfattet af modellen. Du kan finde yderligere oplysninger finder grundlæggende Model typer (CSDL). |
| Null-værdi | Nej | **Sand** (standardværdien) eller **Falsk** afhængigt af om egenskaben kan have en null-værdi. Bemærk: Versionen af CSDL, der er angivet af navneområdet [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) en kompleks type egenskab skal have være nul = "False". |
| Standardværdi | Nej | Standardværdien for egenskaben. |
|MaxLength | Nej | Den maksimale længde af egenskabsværdien for. |
| FixedLength | Nej | **Sand** eller **Falsk** afhængigt af om egenskabsværdien gemmes som en streng af længden nul fiexed. |
| Præcision | Nej | Refererer til det maksimale antal cifre til at bevare i den numeriske værdi. |
| Skala | Nej | Maksimale antal decimaler bevare den numeriske værdi. |
| Unicode | Nej | **Sand** eller **Falsk** afhængigt af om Egenskabsværdien er gemt som en Unicode-streng. |
| Sortering | Nej | En streng, der angiver den sætvise sekvens, som bruges i datakilden. |
| ConcurrencyMode | Nej | **Ingen** (standardværdien) eller **fast**. Hvis værdien er indstillet til **fast**, bruges egenskabsværdien i optimistisk trykkeri. |

Følgende er de yderligere attributter, der er blevet føjet til specifikationen CSDL:

**d:Map** - XPath-udtryk, der køres i forhold til tjenesten output og henter en egenskab for output. Den angivne XPath er i forhold til noden gentaget, der er valgt i noden EntityType XPath. Det er også muligt at angive en absolut XPath for at tillade, herunder en statisk ressource i hver af output noderne, som for eksempel en copyright-sætning, der findes kun én gang i den oprindelige tjeneste output, men skal være til stede i hver af rækkerne i OData output. Eksempel fra tjenesten:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

XPath-udtrykket her ville være ./bar/baz0 til at hente noden baz0 fra den indhold provider-tjeneste.

**d:CharMaxLength** - For strengtypen, kan du angive den maksimale længde. Se DataService CSDL eksempel

**d:IsPrimaryKey** - angiver, om kolonnen er den primære nøgle i tabelvisningen. Se DataService CSDL eksempel.

**d:isExposed** - bestemmer, hvis der vises i tabellen skemaet (generelt SAND). Se DataService CSDL eksempel

**d:IsView** *(Valgfrit)* - SAND, hvis dette er baseret på en visning i stedet for en tabel.  Se DataService CSDL eksempel

**d:Tableschema** - se DataService CSDL eksempel

**d:ColumnName** - er navnet på kolonnen i tabelvisningen.  Se DataService CSDL eksempel

**d:IsReturned** - er den boolesk værdi, der bestemmer, hvis tjenesten Fremviser denne værdi til klienten.  Se DataService CSDL eksempel

**d:IsQueryable** - er den boolesk værdi, der bestemmer, hvis kolonnen kan bruges i en databaseforespørgsel.   Se DataService CSDL eksempel

**d:OrdinalPosition** - er kolonnens numeriske position af udseende, x, i tabellen eller visningen, hvor x er fra 1 til antallet af kolonner i tabellen.  Se DataService CSDL eksempel

**d:DatabaseDataType** - er datatypen for kolonnen i databasen, det vil sige SQL-datatype. Se DataService CSDL eksempel

## <a name="supported-parametersproperty-types"></a>Understøttede parametre/egenskaben typer
Følgende er de understøttede datatyper for parametre og egenskaber. (Store og små bogstaver)

| Enkle typer | Beskrivelse |
|----|----|
| Null | Repræsenterer et manglende en værdi |
| Boolesk værdi | Repræsenterer matematiske begrebet binær værdier logik|
| Byte | Usigneret 8-bit heltalsværdi|
|Dato og klokkeslæt| Repræsenterer dato og klokkeslæt med værdier fra 12:00:00 midnat, januar 1, 1753 e.kr. gennem 11:59:59 P.M, December 9999 e.kr.|
|Decimal | Repræsenterer numeriske værdier med fast præcision og skala. Denne type kan beskrive en numerisk værdi fra negative 10 ^ 255 + 1 til positive 10 ^ 255 -1|
| Dobbelt | Repræsenterer et flydende punkt tal med 15 cifres nøjagtighed, som kan repræsentere værdier med tilnærmet celleområde ± 2, 23E-308 til ± 1, 79E + 308. **Bruge Decimal på grund af Exel Eksportér problem**|
| Enkelt | Repræsenterer et flydende punkt tal 7 cifre præcist, som kan repræsentere værdier med tilnærmet celleområde ± 1.18E-38 til ± 3.40E + 38|
|GUID |Repræsenterer en 16-byte (128-bit) entydigt id-værdi |
|Int16|Repræsenterer en signeret 16-bit heltalsværdi |
|Int32|Repræsenterer en signeret 32-bit heltalsværdi |
|Int64|Repræsenterer en signeret 64-bit heltalsværdi |
|Streng | Repræsenterer fast eller variabel-længde tegndata |


## <a name="see-also"></a>Se også
- Hvis du er interesseret i at forstå de overordnede OData tilknytningsproces og formål, kan du læse denne artikel [Data Service OData tilknytning](marketplace-publishing-data-service-creation-odata-mapping.md) til Gennemse definitioner, strukturer og instruktioner.
- Hvis du er interesseret i at gennemgå eksempler, kan du læse denne artikel [Data Service OData tilknytning eksempler](marketplace-publishing-data-service-creation-odata-mapping-examples.md) , der kan se eksempler på kode og forstå syntaks for feltkoder og kontekst.
- Læs denne artikel [Data Service publicering Guide](marketplace-publishing-data-service-creation.md)for at vende tilbage til bestemt stien til publicering af en datatjeneste til Azure Marketplace.
