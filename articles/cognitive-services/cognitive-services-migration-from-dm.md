
<properties
    pageTitle="Overføre til Azure Kognitiv Services anbefalinger API fra DataMarket anbefalinger API | Microsoft Azure"
    description="Azure machine learning-anbefalinger--skifte til anbefalinger Kognitiv service"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Overføre til Azure Kognitiv Services anbefalinger API fra DataMarket anbefalinger API
I denne artikel beskrives, hvordan til at overføre fra [Microsoft DataMarket anbefalinger API](https://datamarket.azure.com/dataset/amla/recommendations) til [Microsoft Azure Kognitiv Services anbefalinger API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

DataMarket anbefalinger API stopper acceptere nye kunder 31 December 2016 og vil være forældet februar 28 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Hvordan kommer jeg i gang ved hjælp af Azure Kognitiv Services anbefalinger API?

Hvis du vil overføre til Kognitiv Services anbefalinger API, skal du gøre følgende:

1.  Hvis du ikke allerede har et Azure-abonnement, [Tilmeld dig](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) til en. 

1.  Finde en trinvis vejledning fra [Startvejledning](cognitive-services-recommendations-quick-start.md) til at tilmelde sig Kognitiv Services anbefalinger API og bruge det fra et program. 

1.  Gå til den [Kognitiv Services anbefalinger API landingssiden](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) til at få mere at vide om tjenesten og finde dokumentation.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>Jeg har brugt anbefalinger Brugergrænsefladen til at oprette min modeller. Er der et tilsvarende værktøj til Kognitiv Services anbefalinger API?

Absolut! URL-adressen til den nye [Anbefalinger Brugergrænsefladen](http://recommendations-portal.azurewebsites.net/) er http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Legitimationsoplysningerne DataMarket virker ikke her. Tilmelde dig et abonnement på portalen Azure, og få tasten konto skal bruges til at bruge den nye [Brugergrænseflade anbefalinger](http://recommendations-portal.azurewebsites.net/).
Hvis du ikke har en konto tast, skal du se opgave 1 i [Guiden Hurtig Start](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>Er den samme som DataMarket anbefalinger API API og det nye format?

API understøtter samme scenarier og proces flyder som disse scenarier, der understøttes i DataMarket-version, men den faktiske API-grænseflade er blevet opdateret, så de svarer til [Microsoft RESTEN API retningslinjer](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). API'erne er mere ensartet og arbejde bedre med værktøjer, der understøtter Swagger.

For at forstå hver af API'erne, skal du se [API explorer](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Du kan bruge den *prøve* knappen for at teste et API-kald. Formatet af filer, der bruges af anbefalinger API (katalog for og brugen filer) er ikke ændret, så du kan fortsætte med at bruge de samme filer og/eller infrastruktur, du har udviklet til at generere disse filer.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Hvad er nogle nye funktioner i Kognitiv Services anbefalinger API?

Vi har udgivet nye muligheder for Kognitiv Services anbefalinger API over de sidste to måneder:
-   Anbefalinger brugergrænseflade til uddannelse og test af modeller uden at skrive en enkelt linje af kode
-   Batchen vundne for at give dig tusindvis af anbefalinger på én gang
-   Opbygge målepunkter support til forespørgsel kvaliteten af anbefalinger modeller
-   Understøttelse af forretningsregler
-   Muligheden for at Optæl og downloade brugen og katalog filer
-   Fastlæggelse af rækkefølgen build support til at forespørge kvaliteten af element-funktioner i en model, anbefalinger
-   Tilføjede mulighed for at søge efter et produkt i kataloget

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Når forhindrer Microsoft understøttende DataMarket anbefalinger API?

[Anbefalinger API på DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) længere ikke accepterer nye kunder efter 31 December 2016 og vil være helt forældet ved februar 28 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Hvad nu, hvis jeg ikke har de data, som jeg har brug for at genskabe min-modeller i Kognitiv Services anbefalinger API?

Vi vil gøre denne overgang lige så nemt som muligt for dig. Vi kan hjælpe dig med at flytte din gamle modeller fra din DataMarket-konto til din nye Azure Kognitiv Services anbefalinger API-abonnement. Kontakt os på [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Vi beder dig om at angive dit DataMarket-abonnement-ID og din Kognitiv Services-abonnement-ID, før vi knytte modellerne til din nye konto.
