<properties
    pageTitle="Sådan model komplekse datatyper i Azure Søg | Microsoft Azure-søgning"
    description="Indlejrede eller hierarkiske datastrukturer kan dannes i en Azure søgeindekset ved hjælp af flad rækkesættet og samlinger datatype."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Sådan model komplekse datatyper i Azure Søg

Eksterne datasæt, der bruges til at udfylde en Azure søgeindekset nogle gange omfatter hierarkisk eller indlejrede substructures, der ikke opdele pænt i et tabelformat rækkesæt. Eksempler på sådanne strukturer muligvis indeholder flere steder og telefonnumre til en enkelt kunde, flere farver og skriftstørrelser for en enkelt SKU, flere forfattere i en enkelt bog, og så videre. I modeling betingelserne, kan du se disse strukturer, der refereres til som *komplekse datatyper*, *sammensatte datatyper*, *sammensatte datatyper*eller *sammenlægge datatyper*, for at navngive nogle.

Komplekse datatyper understøttes ikke indbygget i Azure søgning, men en dokumenteret løsning omfatter en to-trins processen med at udjævne strukturen og derefter bruge datatypen **samling** til således rekonstruere den indvendige struktur. Følge den metode, der er beskrevet i denne artikel giver mulighed for indholdet, der skal søges facetteret, filtreret og sorteret.

## <a name="example-of-a-complex-data-structure"></a>Eksempel på en kompleks datastruktur

Typisk gemt de pågældende data, som et sæt JSON eller XML-dokumenter eller elementer i en NoSQL store som DocumentDB. Strukturelt, stammer udfordringen fra har flere underordnede elementer, der skal søges og filtreret.  Som et udgangspunkt til at illustrere problemet skal du tage følgende JSON dokument, der viser et sæt af kontakter som et eksempel:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Mens felterne navnet 'id', 'name' og 'firma' kan nemt tilknyttes en som felter i en Azure søgeindekset, feltet 'placeringer' indeholder en matrix med placeringer, har du begge et sæt af placering id'er samt beskrivelser af placering. Da der Azure Søg ikke har en datatype, der understøtter dette, har vi brug for en anden måde at skabe dette i Azure Søg. 

> [AZURE.NOTE] Denne metode er også beskrevet ved Kirk Karlsen i et blogindlæg [Indeksering DocumentDB med Azure søgning](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), der viser en teknik kaldet "udjævne data", hvorved du ville have et felt med navnet `locationsID` og `locationsDescription` , der er både [af websteder](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matrix af strenge).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Del 1: Udjævne matrixen til individuelle felter

Hvis du vil oprette en Azure søgeindekset, som kan rumme dette dataset, oprette individuelle felter for den indlejrede substructure: `locationsID` og `locationsDescription` med datatypen [af websteder](https://msdn.microsoft.com/library/azure/dn798938.aspx) (eller en matrix af strenge). Du vil indeksere værdierne '1' og '2' i disse felter til den `locationsID` felt for John Smith og værdierne '3' og '4' i den `locationsID` felt til Jen Campbell.  

Dine data i Azure Søg ville se således ud: 

![eksempeldata, 2 rækker](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Del 2: Tilføje et felt af websteder i indeksdefinitionen af

I skemaet indeks, vil feltet definitioner se nogenlunde sådan i dette eksempel.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Valider Søg funktionsmåder, og du kan også udvide indekset

Hvis du har oprettet indekset og indlæst dataene, kan du nu teste løsning for at bekræfte Søg udførelse af forespørgsel mod datasættet. Hvert felt, **af websteder** skal være **søgbar** **filtrerbare** og **facetable**. Du bør kunne køre forespørgsler som:

* Finde alle de personer, du arbejder i 'Adventureworks hovedsædet'.
* Få vist antallet af personer, du arbejder i en 'hjem Office'.  
* Vis på de personer, der arbejder i en 'hjem Office', hvilke andre kontorer, de arbejder sammen med en optælling af personer i hver placering.  

Hvor denne metode ligger fra hinanden er, når du skal gøre en søgning, der kombinerer både placering-id samt beskrivelsen af placeringen. Eksempel:

* Finde alle personer, hvor de har en Office til hjemmet og har et placering-ID på 4.  

Hvis du tilbagekalde det oprindelige indhold set således:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Men nu, hvor vi har adskilt data i separate felter, vi har ingen mulighed for ved hvis hjem Office for Jen Campbell relaterer til `locationsID 3` eller `locationsID 4`.  

Hvis du vil håndtere dette tilfælde, at definere et andet felt i indekset, der kombinerer data i en enkelt samling.  I dette eksempel skal vi kalder dette felt `locationsCombined` og vi vil adskille indholdet med et `||` selvom du kan vælge en hvilken som helst separator, som du mener kan være et entydigt sæt tegn til indholdet. Eksempel: 

![eksempeldata, 2 rækker med separator](./media/search-howto-complex-data-types/sample-data-2.png)

Ved hjælp af dette `locationsCombined` felt, kan vi nu rumme endnu flere forespørgsler, f.eks.:

* Vis en optælling af personer, du arbejder på en 'hjem Office' med placering Id '4'.  
* Søge efter personer, du arbejder på en 'hjem Office' med placering Id '4'. 

## <a name="limitations"></a>Begrænsninger

Denne metode er nyttige til en række scenarier, men det er ikke tilgængelig i alle tilfælde.  Eksempel:

1. Hvis du ikke har et statisk sæt felter i din kompleks datatype, og der var ikke muligt at tilknytte alle de mulige typer til et enkelt felt. 
2. Opdatere de indlejrede objekter kræver noget ekstra arbejde at finde ud af præcis hvad der skal opdateres i Azure søgeindekset

## <a name="sample-code"></a>Eksempel på kode

Du kan se et eksempel i at indeksere en kompleks JSON stokastisk variabel til Azure Søg og udføre et antal forespørgsler over dette dataset på denne [GitHub repo](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Næste trin

[Stemme for indbygget understøttelse af komplekse datatyper](https://feedback.azure.com/forums/263029-azure-search) på Azure Søg UserVoice side og yderligere yderligere input, du vil have os til at overveje om implementeringen af funktioner. Du kan også Henvend dig til mig direkte på Twitter på @liamca.


 