<properties
    pageTitle="Føje handlingen forespørgsel i logik apps | Microsoft Azure"
    description="Oversigt over handlingen forespørgsel for at udføre handlinger som filter matrix."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-query-action"></a>Komme i gang med handlingen forespørgsel

Ved hjælp af handlingen forespørgsel, kan du arbejde med navne og matrixer til at udføre arbejdsprocesser, der skal:

- Oprette en opgave for alle høj prioritet poster fra en database.
- Gem alle vedhæftede filer i PDF-fil til mails til en Azure blob.

For at komme i gang ved hjælp af handlingen forespørgsel i en logik app skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Bruge handlingen forespørgsel

En handling er en handling, der udføres af arbejdsprocessen, der er defineret i en logik app. [Lær mere om handlinger](connectors-overview.md).  

Handlingen forespørgsel har i øjeblikket en handling, kaldet matrixen filter, der vises i designer. Dette kan du forespørge en matrix og returnere et sæt af filtrerede resultater.

Her er, hvordan du kan tilføje det i en logik app:

1. Vælg knappen **Nyt trin** .
2. Vælg **Tilføj en handling**.
3. Skriv **filter** for at få vist handlingen **Filter matrix** i søgefeltet handling.

    ![Vælg handlingen, forespørgsel](./media/connectors-native-query/using-action-1.png)

4. Vælg en matrix til at filtrere. (Følgende skærmbillede viser af en række resultater fra en Twitter-søgning).
5. Oprette en betingelse for at evaluere på hvert enkelt element. (Følgende skærmbillede filtrerer tweets fra brugere, der har mere end 100 tilhængere.)

    ![Udføre handlingen forespørgsel](./media/connectors-native-query/using-action-2.png)

    Handlingen output vil blive sendt en ny matrix, der indeholder kun de resultater, der opfyldt filterkriterierne.
6. Klik på det øverste venstre hjørne af værktøjslinjen for at gemme, og din logik app kan både gemme og publicere (aktivere).

## <a name="query-action"></a>Forespørgsel handling

Her er nogle oplysninger til den handling, der understøtter denne forbindelse. Forbindelsen er en mulig handling.

|Handling|Beskrivelse|
|---|---|
|Filtrere matrix|Evaluerer en betingelse for hvert element i en matrix og returnerer resultatet|

## <a name="action-details"></a>Handlingsdetaljer

Handlingen forespørgsel leveres med en mulig handling. I følgende tabel beskrives de krævede og valgfrie input felter til handlingen og de tilsvarende output detaljer, som er knyttet til ved hjælp af handlingen.

### <a name="filter-array"></a>Filtrere matrix
Følgende er input felter for den handling, som gør en udgående HTTP-anmodning.
A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Fra *|fra|Matrixen til at filtrere|
|Betingelse *|hvor|Betingelsen skal evalueres for hvert element|
<br>

### <a name="output-details"></a>Output detaljer

Følgende er nogle output oplysninger til HTTP-svaret.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Filtrerede matrix|matrix|En matrix, der indeholder et objekt for hvert filtrerede resultat|

## <a name="next-steps"></a>Næste trin

Prøv nu den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Du kan udforske andre tilgængelige forbindelserne i logik apps ved at kigge på vores [API'er liste](apis-list.md).
