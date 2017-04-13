<properties
   pageTitle="Oprette et indeks for dokumenter på flere sprog i Azure Søg | Microsoft Azure | Hostet skyen search-tjenesten"
   description=" Azure søgning understøtter 56 sprog udnytte sprog programmer til analyse fra Lucene og behandling af naturligt sprog teknologi fra Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Oprette et indeks for dokumenter på flere sprog i Azure Søg
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [RESTEN](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Unleashing en potens af programmer til analyse sprog er lige så nemt som indstilling én egenskab på et søgbare felt i indeksdefinitionen af. Nu kan du udføre dette trin i portalen.

Nedenfor finder du skærmbilleder af Azure Portal blade til Azure-søgning, Tillad brugere at definere et indeks skema. Brugere kan oprette alle felter og angive egenskaben analyzer for hver af dem fra denne blade.

> [AZURE.IMPORTANT] Du kan kun angive en sprog analyzer under feltdefinition, som i, når du opretter et nyt indeks fra bunden op, eller når du føjer et nyt felt til et eksisterende indeks. Kontrollér, at du angiver fuldt alle attributter, herunder analyzer, mens du opretter feltet. Du kan ikke redigere attributterne eller ændre typen analyzer, når du gemmer dine ændringer.

## <a name="define-a-new-field-definition"></a>Definere et nyt feltdefinition

1. Log på [Azure-portalen](https://portal.azure.com) , og Åbn bladet tjenesten for din søgetjeneste.
2. Klik på **Tilføj indeks** i kommandolinjen øverst i dashboardet service for at starte et nyt indeks, eller Åbn en eksisterende indeks for at angive en analyzer på nye felter, du føjer til et eksisterende indeks.
3. Bladet felter vises, hvorved du indstillinger for definition af indekset, herunder fanen Analyzer bruges til at vælge et sprog analyzer.
4. Starte en feltdefinition i felter, ved at indtaste et navn, vælge datatypen og angive attributter for at markere feltet som fulde tekst søgbar, størrelse i søgeresultaterne skal bruges i grene navigation strukturer, sorterbar, og så videre. 
5. Åbn fanen **Analyzer** , inden du går videre til det næste felt. 

   
![][1]
*Du kan vælge en analyzer ved at klikke på fanen Analyzer på bladet felter*

## <a name="choose-an-analyzer"></a>Vælg en analyzer

6. Rul ned for at finde det felt, du definerer. 
7. Hvis du ikke har markeret feltet, som kan søges efter, skal du klikke på afkrydsningsfeltet nu for at markere det som **søgbar**.
8. Klik på området Analyzer for at få vist listen over tilgængelige programmer til analyse.
9. Vælg den analyzer, du vil bruge.

![][2]
*Vælg en af de understøttede programmer til analyse for hvert felt*

Som standard bruge alle søgefelterne [Standard Lucene analyzer](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) som er sprog agnostic. Se [Understøttelse af sprog i Azure Søg](https://msdn.microsoft.com/library/azure/dn879793.aspx)for at få vist den komplette liste over understøttede programmer til analyse.

Når sprog analyzer er valgt for et felt, bruges den sammen med hver indeksering og søgning ansøgning for det pågældende felt. Når der udsendes en forespørgsel med flere felter ved hjælp af forskellige programmer til analyse, behandles forespørgslen uafhængigt af de højre programmer til analyse for hvert felt.

Mange web og mobilprogrammer fungere brugere hele verden ved hjælp af forskellige sprog. Det er muligt at definere et indeks for et scenario således ved at oprette et felt for hvert sprog, der understøttes.

![][3]
*Definition af indeks med et beskrivelsesfelt for de enkelte sprog understøttes*

Hvis du kender sproget af agent udstedelse af en forespørgsel, kan en søgeanmodning fastsat til et bestemt felt ved hjælp af parameteren **searchFields** forespørgsel. Følgende forespørgsel, der udstedes kun mod beskrivelsen i polsk:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Du kan forespørge indekset fra portalen kan bruge **Search explorer** til at sætte ind i en forespørgsel, der ligner den, der vises over. Søg explorer er tilgængelig fra kommandolinjen i bladet tjeneste. Du kan finde oplysninger i [forespørgsel dit Azure søgeindeks på portalen](search-explorer.md) .

Nogle gange kendes sproget for agent udstedelse af en forespørgsel ikke, i hvilket tilfælde forespørgslen kan udstedes mod alle felter samtidigt. Hvis det er nødvendigt, kan præference for resultater i et bestemt sprog defineres ved hjælp af [vundne profiler](https://msdn.microsoft.com/library/azure/dn798928.aspx). I eksemplet herunder kan forekomster i beskrivelsen i engelsk bedømmes højere i forhold til tilsvarende værdier i polsk og fransk:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Hvis du er en .NET developer, Bemærk, at du kan konfigurere sprog programmer til analyse ved hjælp af [Azure Søg .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). Den seneste udgave indeholder understøttelse af Microsoft sprog programmer til analyse samt.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
